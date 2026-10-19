DROP TABLE IF EXISTS envio_ruta;
DROP TABLE IF EXISTS ruta;
DROP TABLE IF EXISTS recogidas;
DROP TABLE IF EXISTS envios_recogida;
DROP TABLE IF EXISTS mensajero_estado;
DROP TABLE IF EXISTS pagoparcial_envio;
DROP TABLE IF EXISTS encargado_envio;
DROP TABLE IF EXISTS password_reset_tokens;
DROP TABLE IF EXISTS datos_login;
DROP TABLE IF EXISTS R_UsuarioRol;
DROP TABLE IF EXISTS registro_estados_pago;
DROP TABLE IF EXISTS registro_estados_envio;
DROP TABLE IF EXISTS envio;
DROP TABLE IF EXISTS direccion;
DROP TABLE IF EXISTS barrio;
DROP TABLE IF EXISTS usuario;
DROP TABLE IF EXISTS rol;
DROP TABLE IF EXISTS estado_pagos;
DROP TABLE IF EXISTS estados_envios;
DROP TABLE IF EXISTS localidad;

CREATE TABLE localidad (
  id_localidad INT PRIMARY KEY,
  nombre VARCHAR(20) NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL
);

CREATE TABLE estados_envios (
  id_estado SMALLINT PRIMARY KEY,
  nombre VARCHAR(20) NOT NULL,
  descripcion VARCHAR(50),
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL
);

CREATE TABLE estado_pagos (
  id_estado TINYINT PRIMARY KEY,
  nombre VARCHAR(30) NOT NULL,
  descripcion VARCHAR(100) NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_modificacion DATETIME NOT NULL,
  modificado_por INT NOT NULL
);

CREATE TABLE rol (
  id_rol INT PRIMARY KEY,
  nombre VARCHAR(15) NOT NULL,
  descripcion VARCHAR(80),
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL
);

CREATE TABLE usuario (
  id_usuario INT PRIMARY KEY,
  nombre_apellido VARCHAR(80) NOT NULL,
  numero_primario VARCHAR(10) NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  numero_secundario INT,
  modificado_por INT NOT NULL
);

CREATE TABLE barrio (
  id_barrio INT PRIMARY KEY,
  nombre VARCHAR(30) NOT NULL,
  localidad INT NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(localidad) REFERENCES localidad(id_localidad)
);

CREATE TABLE direccion (
  id_direccion INT PRIMARY KEY,
  usuario INT NOT NULL,
  linea_direccion VARCHAR(50) NOT NULL,
  barrio INT NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(usuario) REFERENCES usuario(id_usuario),
  FOREIGN KEY(barrio) REFERENCES barrio(id_barrio)
);

CREATE TABLE envio (
  id_envio INT PRIMARY KEY,
  remitente INT NOT NULL,
  destinatario INT NOT NULL,
  valor_declarado INT NOT NULL,
  valor_cobro INT NOT NULL,
  valor_flete INT NOT NULL,
  medidas_x TINYINT NOT NULL,
  medidas_y TINYINT NOT NULL,
  medidas_z TINYINT NOT NULL,
  fecha_entrega DATE NOT NULL,
  horario_recomendado DATE,
  fecha_carga DATETIME NOT NULL,
  dir_recogida INT NOT NULL,
  dir_entrega INT NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(remitente) REFERENCES usuario(id_usuario),
  FOREIGN KEY(destinatario) REFERENCES usuario(id_usuario),
  FOREIGN KEY(dir_recogida) REFERENCES direccion(id_direccion),
  FOREIGN KEY(dir_entrega) REFERENCES direccion(id_direccion)
);

CREATE TABLE registro_estados_envio (
  id_registro BIGINT PRIMARY KEY,
  estado SMALLINT NOT NULL,
  envio INT NOT NULL,
  razon VARCHAR(100) NOT NULL,
  fecha_carga DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(estado) REFERENCES estados_envios(id_estado),
  FOREIGN KEY(envio) REFERENCES envio(id_envio)
);

CREATE TABLE registro_estados_pago (
  id_registro BIGINT PRIMARY KEY,
  envio INT NOT NULL,
  monto INT NOT NULL,
  estado TINYINT NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(envio) REFERENCES envio(id_envio),
  FOREIGN KEY(estado) REFERENCES estado_pagos(id_estado)
);

CREATE TABLE R_UsuarioRol (
  usuario INT NOT NULL,
  rol INT NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(usuario) REFERENCES usuario(id_usuario),
  FOREIGN KEY(rol) REFERENCES rol(id_rol)
);

CREATE TABLE datos_login (
  id_datos INT PRIMARY KEY,
  email VARCHAR(150) NOT NULL,
  password_hash VARCHAR(255) NOT NULL,
  usuario INT NOT NULL,
  fecha_carga DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(usuario) REFERENCES usuario(id_usuario)
);

CREATE TABLE password_reset_tokens (
  id_token BIGINT PRIMARY KEY,
  id_usuario INT NOT NULL,
  token VARCHAR(255) NOT NULL,
  fecha_creacion DATETIME NOT NULL,
  fecha_expiracion DATETIME NOT NULL,
  usado BOOLEAN NOT NULL,
  FOREIGN KEY(id_usuario) REFERENCES usuario(id_usuario)
);

CREATE TABLE encargado_envio (
  encargado_id INT NOT NULL,
  envio_id INT NOT NULL,
  fecha_creacion DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(encargado_id) REFERENCES usuario(id_usuario),
  FOREIGN KEY(envio_id) REFERENCES envio(id_envio),
  FOREIGN KEY(modificado_por) REFERENCES usuario(id_usuario)
);

CREATE TABLE pagoparcial_envio (
  id INT PRIMARY KEY NOT NULL,
  envio INT NOT NULL,
  valor INT NOT NULL,
  fecha_creacion DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(envio) REFERENCES envio(id_envio),
  FOREIGN KEY(modificado_por) REFERENCES usuario(id_usuario)
);

CREATE TABLE recogidas (
  id INT PRIMARY KEY NOT NULL,
  remitente INT NOT NULL,
  recogida_como_envio INT NOT NULL,
  FOREIGN KEY(remitente) REFERENCES usuario(id_usuario),
  FOREIGN KEY(recogida_como_envio) REFERENCES envio(id_envio)
);

CREATE TABLE envios_recogida (
  id_recogida INT NOT NULL,
  id_envio INT NOT NULL,
  FOREIGN KEY(id_recogida) REFERENCES recogidas(id),
  FOREIGN KEY(id_envio) REFERENCES envio(id_envio)
);

CREATE TABLE mensajero_estado (
  id_mensajero INT NOT NULL PRIMARY KEY,
  estado BOOLEAN NOT NULL,
  zona VARCHAR(30) NOT NULL,
  fecha_creacion DATETIME NOT NULL,
  fecha_actualizacion DATETIME NOT NULL,
  modificado_por INT NOT NULL,
  FOREIGN KEY(id_mensajero) REFERENCES usuario(id_usuario),
  FOREIGN KEY(modificado_por) REFERENCES usuario(id_usuario)
);

CREATE TABLE ruta (
  id_ruta INT PRIMARY KEY AUTO_INCREMENT NOT NULL,
  usuario INT NOT NULL,
  estado BOOLEAN NOT NULL,
  FOREIGN KEY(usuario) REFERENCES mensajero_estado(id_mensajero)
);

CREATE TABLE envio_ruta (
  id_relacion_envio_ruta INT PRIMARY KEY AUTO_INCREMENT NOT NULL,
  ruta INT NOT NULL,
  envio INT NOT NULL,
  FOREIGN KEY(ruta) REFERENCES ruta(id_ruta),
  FOREIGN KEY(envio) REFERENCES envio(id_envio)
);