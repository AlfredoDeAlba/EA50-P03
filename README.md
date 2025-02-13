--tables creation 

CREATE TABLE libros (
    id_libro SERIAL PRIMARY KEY,
    titulo VARCHAR(50),
    autor VARCHAR(50),
    anio_publicacion DATE NOT NULL
);

CREATE TABLE usuarios(
    id_usuario SERIAL PRIMARY KEY,
    nombre VARCHAR(50) NOT NULL,
    email VARCHAR(50)
);


CREATE TABLE prestamos (
    id_prestamo SERIAL PRIMARY KEY,
    id_libro INT NOT NULL,
    id_usuario INT NOT NULL,
    fecha_prestamo DATE NOT NULL,
    fecha_devolucion DATE,
    FOREIGN KEY (id_libro) REFERENCES libros(id_libro),
    FOREIGN KEY (id_usuario) REFERENCES usuarios(id_usuario)
);

INSERT INTO libros (titulo, autor, anio_publicacion) VALUES
('El Señor de los Anillos', 'J.R.R. Tolkien', '1954-07-29'),
('1984', 'George Orwell', '1949-06-08'),
('Cien años de soledad', 'Gabriel García Márquez', '1967-06-05'),
('La sombra del viento', 'Carlos Ruiz Zafón', '2001-03-21'),
('Don Quijote de la Mancha', 'Miguel de Cervantes', '1605-01-16');

INSERT INTO usuarios (nombre, email) VALUES
('Ana Pérez', 'ana.perez@example.com'),
('Luis Gómez', 'luis.gomez@example.com'),
('María López', 'maria.lopez@example.com'),
('Carlos Rodríguez', 'carlos.rodriguez@example.com'),
('Elena Fernández', 'elena.fernandez@example.com');

INSERT INTO prestamos (id_libro, id_usuario, fecha_prestamo, fecha_devolucion) VALUES
(1, 1, '2025-01-10', '2025-01-24'),
(2, 1, '2025-01-15', '2025-02-01'),
(3, 2, '2025-01-20', '2025-02-05'),
(4, 5, '2024-01-25', '2025-02-09');

-- Obtener todos los libros prestados actualmente:

SELECT libros.id_libro AS id_libro, libros.titulo AS titulo
FROM libros
JOIN prestamos ON libros.id_libro = prestamos.id_libro;


-- Listar los usuarios que han prestado libros en el último mes:

SELECT usuarios.id_usuario, usuarios.nombre, email FROM usuarios
JOIN prestamos ON usuarios.id_usuario=prestamos.id_usuario
WHERE fecha_prestamo >=CURRENT_DATE - INTERVAL '1 month';

-- Encontrar los libros que no han sido prestados nunca:

SELECT id_libro, titulo FROM libros
WHERE id_libro IN (
    SELECT id_libro FROM libros
    EXCEPT
    SELECT id_libro FROM prestamos
);
-- Contar el número de préstamos por usuario:

SELECT usuarios.id_usuario, usuarios.nombre, COUNT(prestamos.id_prestamo) AS veces_prestado
FROM usuarios
JOIN prestamos ON usuarios.id_usuario = prestamos.id_usuario
GROUP BY usuarios.id_usuario;
