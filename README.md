# UIII-Act-7-models.py-y-https-www.dbdesigner.net-

<img width="1133" height="689" alt="image" src="https://github.com/user-attachments/assets/bad49720-6ee0-4e9b-8cd4-c6b3bafce049" />


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
