
# DSList Games API

## Visão Geral

API REST desenvolvida com Spring Boot para gerenciamento de jogos e listas de jogos. O sistema permite consultar informações detalhadas sobre jogos, organizá-los em listas personalizadas e reordenar a posição dos jogos dentro das listas.

## Funcionalidades

- ✅ Consulta de jogos individuais e listagem completa
- ✅ Gerenciamento de listas de jogos
- ✅ Busca de jogos por lista específica
- ✅ Reordenação de jogos dentro das listas
- ✅ DTOs otimizados para diferentes necessidades
- ✅ Arquitetura em camadas (Controller, Service, Repository)

## Tecnologias Utilizadas

- **Java**: 21+
- **Spring Boot**: 3.x
- **Spring Web**: Para criação da API REST
- **Spring Data JPA**: Para persistência de dados
- **Jakarta Persistence API**: Para mapeamento objeto-relacional
- **Banco de Dados**: MySQL/H2 (TESTE)

## Instalação e Configuração

### Pré-requisitos

- Java 17 ou superior
- Maven 3.6+
- MySQL (ou H2 para desenvolvimento)


## Documentação da API

### Base URL
```
http://localhost:8080
```

### Endpoints

#### Jogos

**GET** `/games` - Lista todos os jogos (versão resumida)
- **Descrição**: Retorna uma lista com informações básicas de todos os jogos
- **Resposta**: Array de objetos GameMinDTO
- **Exemplo de resposta**:
```json
[
  {
    "id": 1,
    "title": "Mass Effect Trilogy",
    "year": 2012,
    "imgUrl": "https://exemplo.com/image1.jpg",
    "shortDescription": "Lorem ipsum dolor sit amet..."
  },
  {
    "id": 2,
    "title": "Red Dead Redemption 2",
    "year": 2018,
    "imgUrl": "https://exemplo.com/image2.jpg",
    "shortDescription": "Lorem ipsum dolor sit amet..."
  }
]
```

**GET** `/games/{id}` - Busca jogo por ID (versão completa)
- **Parâmetros**: 
  - `id` (Long) - ID do jogo
- **Descrição**: Retorna informações completas de um jogo específico
- **Resposta**: Objeto GameDTO
- **Exemplo de resposta**:
```json
{
  "id": 1,
  "title": "Mass Effect Trilogy",
  "year": 2012,
  "genre": "Role-playing (RPG), Shooter",
  "platforms": "XBox, Playstation, PC",
  "score": 4.8,
  "imgUrl": "https://exemplo.com/image1.jpg",
  "shortDescription": "Lorem ipsum dolor sit amet...",
  "longDescription": "Lorem ipsum dolor sit amet consectetur adipisicing elit..."
}
```

#### Listas de Jogos

**GET** `/lists` - Lista todas as listas de jogos
- **Descrição**: Retorna todas as listas de jogos disponíveis
- **Resposta**: Array de objetos GameListDTO
- **Exemplo de resposta**:
```json
[
  {
    "id": 1,
    "name": "Aventura e RPG"
  },
  {
    "id": 2,
    "name": "Jogos de plataforma"
  }
]
```

**GET** `/lists/{listId}/games` - Busca jogos de uma lista específica
- **Parâmetros**: 
  - `listId` (Long) - ID da lista
- **Descrição**: Retorna todos os jogos pertencentes a uma lista específica
- **Resposta**: Array de objetos GameMinDTO ordenados pela posição na lista
- **Exemplo de resposta**:
```json
[
  {
    "id": 1,
    "title": "Mass Effect Trilogy",
    "year": 2012,
    "imgUrl": "https://exemplo.com/image1.jpg",
    "shortDescription": "Lorem ipsum dolor sit amet..."
  },
  {
    "id": 5,
    "title": "The Witcher 3",
    "year": 2015,
    "imgUrl": "https://exemplo.com/image5.jpg",
    "shortDescription": "Lorem ipsum dolor sit amet..."
  }
]
```

**POST** `/lists/{listId}/replacement` - Reordena jogos na lista
- **Parâmetros**: 
  - `listId` (Long) - ID da lista
- **Body**: Objeto ReplacementDTO
- **Descrição**: Move um jogo de uma posição para outra dentro da lista
- **Exemplo de requisição**:
```json
{
  "sourceIndex": 2,
  "destinationIndex": 0
}
```
- **Resposta**: Status 200 (sem conteúdo)

### Códigos de Status HTTP

| Código | Descrição |
|--------|-----------|
| 200 | Sucesso |
| 404 | Recurso não encontrado |
| 400 | Requisição inválida |
| 500 | Erro interno do servidor |

## Modelo de Dados

### Game (Entidade Principal)

```java
{
  "id": Long,              // ID único do jogo
  "title": String,         // Título do jogo
  "year": Integer,         // Ano de lançamento
  "genre": String,         // Gênero(s) do jogo
  "platforms": String,     // Plataformas disponíveis
  "score": Double,         // Pontuação (0.0 - 5.0)
  "imgUrl": String,        // URL da imagem de capa
  "shortDescription": String,  // Descrição curta
  "longDescription": String    // Descrição detalhada
}
```

### DTOs Utilizados

**GameMinDTO** - Para listagens (dados essenciais):
- id, title, year, imgUrl, shortDescription

**GameDTO** - Para consultas detalhadas (todos os campos):
- Todos os campos da entidade Game

**GameListDTO** - Para informações das listas:
- id, name

**ReplacementDTO** - Para reordenação:
- sourceIndex, destinationIndex

## Exemplos de Uso

### Java (RestTemplate)
```java
RestTemplate restTemplate = new RestTemplate();

// Buscar todos os jogos
GameMinDTO[] games = restTemplate.getForObject(
    "http://localhost:8080/games", 
    GameMinDTO[].class
);

// Buscar jogo específico
GameDTO game = restTemplate.getForObject(
    "http://localhost:8080/games/1", 
    GameDTO.class
);

// Buscar jogos de uma lista
GameMinDTO[] gamesInList = restTemplate.getForObject(
    "http://localhost:8080/lists/1/games", 
    GameMinDTO[].class
);
```

### JavaScript (Fetch API)
```javascript
// Buscar todos os jogos
const games = await fetch('http://localhost:8080/games')
  .then(response => response.json());

// Buscar jogo específico
const game = await fetch('http://localhost:8080/games/1')
  .then(response => response.json());

// Reordenar jogos na lista
await fetch('http://localhost:8080/lists/1/replacement', {
  method: 'POST',
  headers: {
    'Content-Type': 'application/json',
  },
  body: JSON.stringify({
    sourceIndex: 2,
    destinationIndex: 0
  })
});
```

### cURL
```bash
# Listar todos os jogos
curl -X GET http://localhost:8080/games

# Buscar jogo específico
curl -X GET http://localhost:8080/games/1

# Buscar listas disponíveis
curl -X GET http://localhost:8080/lists

# Buscar jogos de uma lista
curl -X GET http://localhost:8080/lists/1/games

# Reordenar jogos
curl -X POST http://localhost:8080/lists/1/replacement \
  -H "Content-Type: application/json" \
  -d '{"sourceIndex": 2, "destinationIndex": 0}'
```

## Estrutura do Projeto

```
src/main/java/com/devsuperior/dslist/
├── controllers/        # Controladores REST
│   ├── GameController.java
│   └── GameListController.java
├── dto/               # Data Transfer Objects
│   ├── GameDTO.java
│   ├── GameMinDTO.java
│   ├── GameListDTO.java
│   └── ReplacementDTO.java
├── entities/          # Entidades JPA
│   ├── Game.java
│   ├── GameList.java
│   └── Belonging.java
├── repositories/      # Repositórios JPA
│   ├── GameRepository.java
│   └── GameListRepository.java
├── services/          # Camada de serviços
│   ├── GameService.java
│   └── GameListService.java
└── DslistApplication.java  # Classe principal
```

## Arquitetura

A aplicação segue o padrão de arquitetura em camadas:

- **Controller**: Camada de apresentação, responsável por receber as requisições HTTP
- **Service**: Camada de negócio, contém a lógica da aplicação
- **Repository**: Camada de acesso a dados, utiliza Spring Data JPA
- **Entity**: Representa as tabelas do banco de dados
- **DTO**: Objetos de transferência de dados entre as camadas

## Desenvolvimento

### Executar em modo desenvolvimento
```bash
mvn spring-boot:run -Dspring-boot.run.profiles=dev
```

### Executar testes
```bash
mvn test
```

### Gerar build para produção
```bash
mvn clean package
java -jar target/dslist-0.0.1-SNAPSHOT.jar
```

## Deploy

### Docker
```dockerfile
FROM openjdk:17-jdk-slim
COPY target/dslist-0.0.1-SNAPSHOT.jar app.jar
EXPOSE 8080
ENTRYPOINT ["java", "-jar", "/app.jar"]
```

### Variáveis de Ambiente para Produção
```bash
SPRING_PROFILES_ACTIVE=prod
DATABASE_URL=jdbc:postgresql://localhost:5432/dslist_prod
DATABASE_USERNAME=usuario_prod
DATABASE_PASSWORD=senha_prod
```


**Desenvolvido com ☕ usando Spring Boot**
