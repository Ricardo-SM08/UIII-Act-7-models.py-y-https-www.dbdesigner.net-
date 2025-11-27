# UIII-Act-7-models.py-y-https-www.dbdesigner.net-

<img width="1133" height="689" alt="image" src="https://github.com/user-attachments/assets/bad49720-6ee0-4e9b-8cd4-c6b3bafce049" />

# models.py

from django.db import models

## 🧑‍⚕️ Modelos para Gestión Psicológica
# ---

### Paciente
class Paciente_Psicologia(models.Model):
    # El id_paciente (clave primaria INT) se crea automáticamente por Django como 'id'
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    fecha_nacimiento = models.DateField()
    genero = models.CharField(max_length=1, choices=[('M', 'Masculino'), ('F', 'Femenino'), ('O', 'Otro')])
    direccion = models.CharField(max_length=255, blank=True, null=True)
    telefono = models.CharField(max_length=20, blank=True, null=True)
    email = models.EmailField(max_length=100, unique=True)
    profesion = models.CharField(max_length=100, blank=True, null=True)
    fecha_registro = models.DateField(auto_now_add=True) # Se establece en la creación
    motivo_consulta_inicial = models.TextField()

    def __str__(self):
        return f'{self.nombre} {self.apellido}'
    
    class Meta:
        verbose_name = "Paciente"
        verbose_name_plural = "Pacientes"

# ---

### Psicólogo
class Psicologo(models.Model):
    # El id_psicologo (clave primaria INT) se crea automáticamente como 'id'
    nombre = models.CharField(max_length=100)
    apellido = models.CharField(max_length=100)
    especialidad = models.CharField(max_length=100)
    email = models.EmailField(max_length=100, unique=True)
    telefono = models.CharField(max_length=20, blank=True, null=True)
    fecha_contratacion = models.DateField()
    salario = models.DecimalField(max_digits=10, decimal_places=2)
    licencia_colegiado = models.CharField(max_length=50, unique=True)
    enfoque_terapeutico = models.CharField(max_length=100)

    def __str__(self):
        return f'Lic. {self.nombre} {self.apellido}'

    class Meta:
        verbose_name = "Psicólogo"
        verbose_name_plural = "Psicólogos"

# ---

### Sesión
class Sesion(models.Model):
    # El id_sesion (clave primaria INT) se crea automáticamente como 'id'
    
    # Relaciones Muchos a Uno (Foreign Keys)
    id_paciente = models.ForeignKey(
        Paciente_Psicologia, 
        on_delete=models.CASCADE, 
        related_name='sesiones'
    )
    id_psicologo = models.ForeignKey(
        Psicologo, 
        on_delete=models.SET_NULL, # Si el psicólogo se elimina, mantiene el registro de la sesión
        null=True, 
        blank=True, 
        related_name='sesiones_impartidas'
    )
    
    fecha_sesion = models.DateTimeField() # DATETIME
    hora_sesion = models.TimeField()      # TIME
    tipo_sesion = models.CharField(max_length=50, help_text="Ej: Individual, Grupal, Pareja, Familiar")
    duracion_minutos = models.IntegerField()
    notas_sesion = models.TextField(blank=True, null=True)
    diagnostico_actual = models.TextField(blank=True, null=True)
    costo_sesion = models.DecimalField(max_digits=10, decimal_places=2)

    def __str__(self):
        return f'Sesión {self.id} - {self.id_paciente} ({self.fecha_sesion.date()})'

    class Meta:
        verbose_name = "Sesión"
        verbose_name_plural = "Sesiones"
        ordering = ['fecha_sesion']

# ---

### Diagnóstico
class Diagnostico(models.Model):
    # El id_diagnostico (clave primaria INT) se crea automáticamente como 'id'
    nombre_diagnostico = models.CharField(max_length=255)
    descripcion_corta = models.TextField(blank=True, null=True)
    cie_10_codigo = models.CharField(max_length=20, blank=True, null=True, unique=True)
    dsm_5_codigo = models.CharField(max_length=20, blank=True, null=True, unique=True)
    tratamientos_sugeridos = models.TextField(blank=True, null=True)
    es_cronico = models.BooleanField(default=False)

    def __str__(self):
        return self.nombre_diagnostico
    
    class Meta:
        verbose_name = "Diagnóstico"
        verbose_name_plural = "Diagnósticos"

# ---

### Factura de Psicología
class Factura_Psicologia(models.Model):
    # El id_factura (clave primaria INT) se crea automáticamente como 'id'

    # Relaciones Muchos a Uno (Foreign Keys)
    id_paciente = models.ForeignKey(
        Paciente_Psicologia, 
        on_delete=models.PROTECT, # No permitir eliminar el paciente si tiene facturas
        related_name='facturas'
    )
    id_sesion_asociada = models.ForeignKey(
        Sesion, 
        on_delete=models.SET_NULL, 
        null=True, 
        blank=True, 
        related_name='facturas_asociadas'
    )
    
    fecha_emision = models.DateField(auto_now_add=True)
    total_factura = models.DecimalField(max_digits=10, decimal_places=2)
    estado_pago = models.CharField(max_length=50, choices=[
        ('PENDIENTE', 'Pendiente'), 
        ('PAGADO', 'Pagado'), 
        ('ANULADO', 'Anulado')
    ], default='PENDIENTE')
    metodo_pago = models.CharField(max_length=50, blank=True, null=True)
    fecha_vencimiento = models.DateField(blank=True, null=True)
    descuento_aplicado = models.DecimalField(max_digits=5, decimal_places=2, default=0.00)

    def __str__(self):
        return f'Factura {self.id} - {self.id_paciente}'

    class Meta:
        verbose_name = "Factura de Psicología"
        verbose_name_plural = "Facturas de Psicología"

# ---

### Historial Clínico de Psicología
class Historial_Clinico_Psicologia(models.Model):
    # El id_historial (clave primaria INT) se crea automáticamente como 'id'
    
    # Relaciones Muchos a Uno (Foreign Keys)
    id_paciente = models.OneToOneField( # Asumo que es 1 a 1 ya que es "Historial"
        Paciente_Psicologia, 
        on_delete=models.CASCADE, 
        primary_key=True,
        related_name='historial_clinico'
    )
    id_psicologo_tratante = models.ForeignKey(
        Psicologo, 
        on_delete=models.SET_NULL,
        null=True, 
        blank=True, 
        related_name='pacientes_tratados'
    )
    
    fecha_actualizacion = models.DateTimeField(auto_now=True) # Se actualiza en cada save
    antecedentes_familiares = models.TextField(blank=True, null=True)
    antecedentes_personales = models.TextField(blank=True, null=True)
    medicamentos_actuales = models.TextField(blank=True, null=True)
    resumen_evolucion = models.TextField(blank=True, null=True)
    
    def __str__(self):
        return f'Historial Clínico de {self.id_paciente.nombre} {self.id_paciente.apellido}'

    class Meta:
        verbose_name = "Historial Clínico"
        verbose_name_plural = "Historiales Clínicos"

# ---

### Recurso Psicológico
class Recurso_Psicologico(models.Model):
    # El id_recurso (clave primaria INT) se crea automáticamente como 'id'
    nombre_recurso = models.CharField(max_length=255)
    tipo_recurso = models.CharField(max_length=50, help_text="Ej: Libro, Artículo, Video, Test")
    descripcion = models.TextField(blank=True, null=True)
    autor = models.CharField(max_length=100, blank=True, null=True)
    fecha_publicacion = models.DateField(blank=True, null=True)
    url_recurso = models.URLField(max_length=255, blank=True, null=True) # VARCHAR(255) para URL
    tema_principal = models.CharField(max_length=100, blank=True, null=True)
    publico_objetivo = models.CharField(max_length=100, blank=True, null=True)

    def __str__(self):
        return self.nombre_recurso

    class Meta:
        verbose_name = "Recurso Psicológico"
        verbose_name_plural = "Recursos Psicológicos"















-- -----------------------------------------------------
-- Creación de la base de datos (opcional si ya existe)
-- -----------------------------------------------------
-- CREATE DATABASE IF NOT EXISTS psicologia_db;
-- USE psicologia_db;

-- -----------------------------------------------------
-- Tabla: Paciente_Psicologia
-- -----------------------------------------------------
CREATE TABLE Paciente_Psicologia (
    id_paciente INT NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    fecha_nacimiento DATE NOT NULL,
    genero CHAR(1) NOT NULL,
    direccion VARCHAR(255) NULL,
    telefono VARCHAR(20) NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    profesion VARCHAR(100) NULL,
    fecha_registro DATE NOT NULL,
    motivo_consulta_inicial TEXT NOT NULL,
    PRIMARY KEY (id_paciente)
);

---

-- -----------------------------------------------------
-- Tabla: Psicologo
-- -----------------------------------------------------
CREATE TABLE Psicologo (
    id_psicologo INT NOT NULL AUTO_INCREMENT,
    nombre VARCHAR(100) NOT NULL,
    apellido VARCHAR(100) NOT NULL,
    especialidad VARCHAR(100) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    telefono VARCHAR(20) NULL,
    fecha_contratacion DATE NOT NULL,
    salario DECIMAL(10,2) NOT NULL,
    licencia_colegiado VARCHAR(50) NOT NULL UNIQUE,
    enfoque_terapeutico VARCHAR(100) NOT NULL,
    PRIMARY KEY (id_psicologo)
);

---

-- -----------------------------------------------------
-- Tabla: Sesion
-- -----------------------------------------------------
CREATE TABLE Sesion (
    id_sesion INT NOT NULL AUTO_INCREMENT,
    id_paciente INT NOT NULL,
    id_psicologo INT NOT NULL,
    fecha_sesion DATETIME NOT NULL,
    hora_sesion TIME NOT NULL,
    tipo_sesion VARCHAR(50) NOT NULL,
    duracion_minutos INT NOT NULL,
    notas_sesion TEXT NULL,
    diagnostico_actual TEXT NULL,
    costo_sesion DECIMAL(10,2) NOT NULL,
    PRIMARY KEY (id_sesion),
    FOREIGN KEY (id_paciente) REFERENCES Paciente_Psicologia (id_paciente) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (id_psicologo) REFERENCES Psicologo (id_psicologo) ON DELETE RESTRICT ON UPDATE CASCADE
);

---

-- -----------------------------------------------------
-- Tabla: Diagnostico
-- -----------------------------------------------------
CREATE TABLE Diagnostico (
    id_diagnostico INT NOT NULL AUTO_INCREMENT,
    nombre_diagnostico VARCHAR(255) NOT NULL,
    descripcion_corta TEXT NULL,
    cie_10_codigo VARCHAR(20) UNIQUE NULL,
    dsm_5_codigo VARCHAR(20) UNIQUE NULL,
    tratamientos_sugeridos TEXT NULL,
    es_cronico BOOLEAN NOT NULL,
    PRIMARY KEY (id_diagnostico)
);

---

-- -----------------------------------------------------
-- Tabla: Factura_Psicologia
-- -----------------------------------------------------
CREATE TABLE Factura_Psicologia (
    id_factura INT NOT NULL AUTO_INCREMENT,
    id_paciente INT NOT NULL,
    fecha_emision DATE NOT NULL,
    total_factura DECIMAL(10,2) NOT NULL,
    estado_pago VARCHAR(50) NOT NULL,
    metodo_pago VARCHAR(50) NULL,
    fecha_vencimiento DATE NULL,
    id_sesion_asociada INT NULL,
    descuento_aplicado DECIMAL(5,2) DEFAULT 0.00,
    PRIMARY KEY (id_factura),
    FOREIGN KEY (id_paciente) REFERENCES Paciente_Psicologia (id_paciente) ON DELETE RESTRICT ON UPDATE CASCADE,
    FOREIGN KEY (id_sesion_asociada) REFERENCES Sesion (id_sesion) ON DELETE SET NULL ON UPDATE CASCADE
);

---

-- -----------------------------------------------------
-- Tabla: Historial_Clinico_Psicologia
-- -----------------------------------------------------
CREATE TABLE Historial_Clinico_Psicologia (
    id_historial INT NOT NULL AUTO_INCREMENT,
    id_paciente INT NOT NULL UNIQUE,
    fecha_actualizacion DATETIME NOT NULL,
    antecedentes_familiares TEXT NULL,
    antecedentes_personales TEXT NULL,
    medicamentos_actuales TEXT NULL,
    resumen_evolucion TEXT NULL,
    id_psicologo_tratante INT NULL,
    PRIMARY KEY (id_historial),
    FOREIGN KEY (id_paciente) REFERENCES Paciente_Psicologia (id_paciente) ON DELETE CASCADE ON UPDATE CASCADE,
    FOREIGN KEY (id_psicologo_tratante) REFERENCES Psicologo (id_psicologo) ON DELETE SET NULL ON UPDATE CASCADE
);

---

-- -----------------------------------------------------
-- Tabla: Recurso_Psicologico
-- -----------------------------------------------------
CREATE TABLE Recurso_Psicologico (
    id_recurso INT NOT NULL AUTO_INCREMENT,
    nombre_recurso VARCHAR(255) NOT NULL,
    tipo_recurso VARCHAR(50) NOT NULL,
    descripcion TEXT NULL,
    autor VARCHAR(100) NULL,
    fecha_publicacion DATE NULL,
    url_recurso VARCHAR(255) NULL,
    tema_principal VARCHAR(100) NULL,
    publico_objetivo VARCHAR(100) NULL,
    PRIMARY KEY (id_recurso)
);
