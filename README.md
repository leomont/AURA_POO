# Requerimientos del Módulo de Registro de Usuarios

## 1. Requerimientos Funcionales  
1. **RF1 – Registro de Usuario**  
   - El sistema debe exponer un endpoint `POST /api/auth/register` que reciba un JSON con los campos:
     - `fullName` (String, obligatorio)  
     - `email` (String, obligatorio, formato válido)  
     - `password` (String, obligatorio, ≥ 8 caracteres)  
   - El endpoint debe devolver **201 Created** y los datos del usuario (sin la contraseña).

2. **RF2 – Validación de Datos**  
   - Validar con `@NotBlank` que `fullName`, `email` y `password` no estén vacíos.  
   - Validar con `@Email` que `email` tenga formato correcto.  
   - Validar con `@Size(min=8)` que `password` cumpla longitud mínima.

3. **RF3 – Prevención de Duplicados**  
   - Antes de crear el usuario, comprobar `existsByEmail(email)`.  
   - Si el correo ya existe, responder **409 Conflict** con mensaje de error.

4. **RF4 – Encriptación de Contraseña**  
   - Usar `PasswordEncoder` (p. ej. `BCryptPasswordEncoder`) para guardar `passwordHash` en lugar de la contraseña en texto claro.

5. **RF5 – Persistencia de Usuario**  
   - Mapear la entidad `User` con JPA a la tabla `users`:
     - `id` (PK, auto­generado)  
     - `fullName`  
     - `email` (único)  
     - `passwordHash`  
     - `createdAt` (timestamp de creación)

6. **RF6 – Manejo de Errores Centralizado**  
   - Capturar `EntityExistsException` → **409 Conflict**.  
   - Capturar `MethodArgumentNotValidException` → **400 Bad Request** con detalles de validación.

## 2. Requerimientos No Funcionales  
1. **RNF1 – Seguridad**  
   - Deshabilitar CSRF.  
   - Permitir acceso anónimo solo a `/api/auth/register`.  
   - Proteger el resto de endpoints con autenticación (p. ej. HTTP Basic o JWT).

2. **RNF2 – Rendimiento**  
   - El proceso de registro debe responder en menos de 200 ms en condiciones normales de carga.

3. **RNF3 – Escalabilidad**  
   - La aplicación debe poder desplegarse en contenedores (Docker/Kubernetes) y escalar horizontalmente.

4. **RNF4 – Mantenibilidad**  
   - Seguir principios SOLID en el diseño de clases y componentes.  
   - Separación clara entre capas: controlador, servicio, repositorio, dominio.

5. **RNF5 – Trazabilidad**  
   - Registrar en logs cada intento de registro (éxitos y errores) con suficiente detalle para auditoría.

## 3. Restricciones y Tecnologías  
- **Framework**: Spring Boot (Web, Data JPA, Security, Validation)  
- **Base de datos**: PostgreSQL (o equivalente relacional)  
- **Encriptación**: BCrypt a través de `PasswordEncoder`  
- **Validación**: Hibernate Validator  
- **Gestión de dependencias**: Maven (pom.xml con versiones alineadas a Spring Boot)  

