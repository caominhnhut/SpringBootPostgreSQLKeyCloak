# Spring Boot PostgreSQL with Keycloak Example

A simple Spring Boot application demonstrating integration with PostgreSQL database and Keycloak for authentication.

## Prerequisites

- Java 17
- Maven
- Docker and Docker Compose

## Setup

1. Start PostgreSQL and Keycloak using Docker Compose:
```bash
docker compose up -d
```

2. Build the project:
```bash
mvn clean install
```

3. Run the application with desired profile:
```bash
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

## Configuration

The application uses Spring profiles for different environments:
- `dev` - Development environment
- `test` - Testing environment

Each profile has its own configuration in:
- `application-dev.properties`
- `application-test.properties`

Choose the appropriate profile when running the application.

Update `src/main/resources/application.properties`:

```properties
# Database Configuration
spring.datasource.url=jdbc:postgresql://localhost:5433/your_database
spring.datasource.username=your_username
spring.datasource.password=your_password

# Keycloak Configuration
spring.security.oauth2.resourceserver.jwt.jwk-set-uri=http://localhost:8080/realms/your-realm/protocol/openid-connect/certs
```

To configure Keycloak:
1. Create a new realm in Keycloak
2. Update the application.properties with your realm name

## Keycloak Setup

1. Access Keycloak Admin Console:
   - Open http://localhost:8080
   - Login with default admin credentials (admin/admin)

2. Create a new realm:
   - Click "Create Realm"
   - Set Name: `test`
   - Click "Create"

3. Create a new client:
   - Go to Clients → Create client
   - Client ID: `spring-boot-postgre-sql-keycloak`
   - Client Protocol: `openid-connect`
   - Click "Next"
   - Client authentication: `OFF`
   - Authentication flow: check `Direct access grants` only
   - Click "Save"

4. Create ADMIN role:
   - Go to Realm roles → Create role
   - Role name: `ADMIN`
   - Click "Save"

5. Create a new user:
   - Go to Users → Add user
   - Username: `user`
   - Click "Create"
   - Go to Credentials tab
   - Set password: `user`
   - Turn off "Temporary" password
   - Click "Save credentials"
   - Go to Role mapping tab
   - Click "Assign role"
   - Select "ADMIN"
   - Click "Assign"

## Authentication

To get an access token, use the following curl command:

```bash
curl --location 'localhost:8080/realms/test/protocol/openid-connect/token' \
--header 'Content-Type: application/x-www-form-urlencoded' \
--data-urlencode 'client_id=spring-boot-postgre-sql-keycloak' \
--data-urlencode 'grant_type=password' \
--data-urlencode 'username=user' \
--data-urlencode 'password=user'
```

Use the obtained token in the Authorization header for subsequent API requests:
```bash
Authorization: Bearer <your_token>
```

## API Endpoints

### User Endpoints
- GET `/api/users` - Get all users
- GET `/api/users/{id}` - Get user by ID
- POST `/api/users` - Create new user
- PUT `/api/users/{id}` - Update user
- DELETE `/api/users/{id}` - Delete user

### Role Endpoints
- GET `/api/roles` - Get all roles (requires ADMIN or MODERATOR role)
- GET `/api/roles/{id}` - Get role by ID
- POST `/api/roles` - Create new role
- PUT `/api/roles/{id}` - Update role
- DELETE `/api/roles/{id}` - Delete role

### User-Role Endpoints
- GET `/api/user-roles` - Get all user roles
- GET `/api/user-roles/{id}` - Get user role by ID
- POST `/api/user-roles` - Create new user role
- PUT `/api/user-roles/{id}` - Update user role
- DELETE `/api/user-roles/{id}` - Delete user role

## Technologies

- Spring Boot
- Spring Data JPA
- Spring Security
- Keycloak
- Spring Audit
- PostgreSQL
- Flyway (Database Migration)
- Maven
- Lombok
