#  Agendador de Horários

API REST desenvolvida em **Java + Spring Boot** para gerenciamento de agendamentos de serviços (ex: salões, clínicas, barbearias, consultórios). Permite criar, consultar, atualizar e cancelar horários, validando conflitos de agenda automaticamente.

![Java](https://img.shields.io/badge/Java-25-orange?logo=openjdk)
![Spring Boot](https://img.shields.io/badge/Spring%20Boot-4.1.0-brightgreen?logo=springboot)
![Maven](https://img.shields.io/badge/Build-Maven-C71A36?logo=apachemaven)
![H2 Database](https://img.shields.io/badge/Database-H2-blue)
![License](https://img.shields.io/badge/license-MIT-informational)

---

##  Sobre o projeto

O **Agendador de Horários** é uma API backend que centraliza o controle de agendamentos de um serviço, permitindo:

- Cadastrar um novo agendamento (com validação para evitar horários duplicados no mesmo serviço);
- Consultar todos os agendamentos de um dia específico;
- Alterar um agendamento existente;
- Cancelar (excluir) um agendamento.

O projeto segue uma arquitetura em camadas (**Controller → Service → Repository**), utilizando Spring Data JPA para persistência e um banco H2 em memória para facilitar testes e execução local sem dependências externas.

---

##  Funcionalidades

- ✅ Criar agendamento com verificação de conflito de horário
- ✅ Listar agendamentos de um dia específico
- ✅ Atualizar um agendamento existente
- ✅ Remover um agendamento
- ✅ Persistência automática da data de inserção do registro
- ✅ Console web do banco H2 para inspeção dos dados em desenvolvimento

---

##  Tecnologias utilizadas

| Tecnologia | Descrição |
|---|---|
| [Java 25](https://openjdk.org/) | Linguagem principal |
| [Spring Boot 4.1.0](https://spring.io/projects/spring-boot) | Framework base da aplicação |
| [Spring Web MVC](https://docs.spring.io/spring-framework/reference/web/webmvc.html) | Criação da API REST |
| [Spring Data JPA](https://spring.io/projects/spring-data-jpa) | Persistência e acesso a dados |
| [H2 Database](https://www.h2database.com/) | Banco de dados em memória |
| [Lombok](https://projectlombok.org/) | Redução de código boilerplate |
| [Maven](https://maven.apache.org/) | Gerenciamento de dependências e build |

---

##  Estrutura do projeto

```
Agendador-horarios/
├── src/
│   ├── main/
│   │   ├── java/com/api/agendador_horarios/
│   │   │   ├── AgendadorHorariosApplication.java      # Classe principal (main)
│   │   │   ├── controller/
│   │   │   │   └── AgendamentoController.java         # Endpoints REST
│   │   │   ├── service/
│   │   │   │   └── AgendamentoService.java             # Regras de negócio
│   │   │   └── infrastructure/repository/
│   │   │       ├── AgendamentoRepository.java           # Interface JPA
│   │   │       └── entity/
│   │   │           └── Agendamento.java                 # Entidade / tabela
│   │   └── resources/
│   │       └── application.properties                   # Configurações da aplicação
│   └── test/
│       └── java/com/api/agendador_horarios/
│           └── AgendadorHorariosApplicationTests.java
├── pom.xml
├── mvnw / mvnw.cmd
└── README.md
```

---

##  Como executar o projeto

### Pré-requisitos

- [Java 25 (JDK)](https://adoptium.net/) instalado
- Maven (opcional — o projeto já inclui o Maven Wrapper `mvnw`)

### Passo a passo

```bash
# 1. Clone o repositório
git clone https://github.com/soareslevi566-lgtm/Agendador-horarios.git

# 2. Acesse a pasta do projeto
cd Agendador-horarios

# 3. Execute a aplicação com o Maven Wrapper
./mvnw spring-boot:run       # Linux/macOS
mvnw.cmd spring-boot:run     # Windows
```

A aplicação sobe por padrão em:

```
http://localhost:8080
```

###  Console do banco H2

Como o projeto utiliza um banco H2 em memória, você pode inspecionar os dados através do console web:

```
http://localhost:8080/h2-console
```

**Configurações de conexão:**

| Campo | Valor |
|---|---|
| JDBC URL | `jdbc:h2:mem:agendamentos-db` |
| User Name | `sa` |
| Password | *(em branco)* |

>  Por se tratar de um banco em memória, todos os dados são perdidos ao reiniciar a aplicação.

---

##  Endpoints da API

Rota base: `/agendamentos`

| Método | Endpoint | Descrição |
|---|---|---|
| `POST` | `/agendamentos` | Cria um novo agendamento |
| `GET` | `/agendamentos?data={yyyy-MM-dd}` | Lista os agendamentos de um dia específico |
| `PUT` | `/agendamentos?cliente={cliente}&dataHoraAgendamento={yyyy-MM-ddTHH:mm:ss}` | Atualiza um agendamento existente |
| `DELETE` | `/agendamentos?cliente={cliente}&dataHoraAgendamento={yyyy-MM-ddTHH:mm:ss}` | Remove um agendamento |

### Modelo de dados (`Agendamento`)

```json
{
  "id": 1,
  "servico": "Corte de cabelo",
  "profissional": "João Silva",
  "dataHoraAgendamento": "2026-10-01T14:30:00",
  "cliente": "Maria Souza",
  "telefoneCliente": "(11) 99999-0000",
  "dataInsercao": "2026-09-25T10:00:00"
}
```

### Exemplos de uso

**Criar um agendamento**
```bash
curl -X POST http://localhost:8080/agendamentos \
  -H "Content-Type: application/json" \
  -d '{
        "servico": "Corte de cabelo",
        "profissional": "João Silva",
        "dataHoraAgendamento": "2026-10-01T14:30:00",
        "cliente": "Maria Souza",
        "telefoneCliente": "(11) 99999-0000"
      }'
```

**Consultar agendamentos de um dia**
```bash
curl "http://localhost:8080/agendamentos?data=2026-10-01"
```

**Atualizar um agendamento**
```bash
curl -X PUT "http://localhost:8080/agendamentos?cliente=Maria%20Souza&dataHoraAgendamento=2026-10-01T14:30:00" \
  -H "Content-Type: application/json" \
  -d '{
        "servico": "Corte e barba",
        "profissional": "João Silva",
        "dataHoraAgendamento": "2026-10-01T15:00:00",
        "cliente": "Maria Souza",
        "telefoneCliente": "(11) 99999-0000"
      }'
```

**Cancelar um agendamento**
```bash
curl -X DELETE "http://localhost:8080/agendamentos?cliente=Maria%20Souza&dataHoraAgendamento=2026-10-01T14:30:00"
```

> ℹ Ao tentar criar um agendamento em um horário já ocupado para o mesmo serviço, a API retorna erro informando que o **"Horário já está preenchido"**.

---

##  Testes

O projeto conta com uma estrutura inicial de testes com Spring Boot Test. Para executá-los:

```bash
./mvnw test
```

---

##  Roadmap / Próximos passos

- [ ] Adicionar validações de entrada (Bean Validation) nos campos do agendamento
- [ ] Implementar tratamento global de exceções (`@ControllerAdvice`)
- [x] Migrar para um banco relacional persistente (PostgreSQL/MySQL) em produção
- [ ] Adicionar documentação interativa da API (Swagger/OpenAPI)
- [ ] Criar autenticação e autorização (Spring Security)
- [ ] Cobertura de testes unitários e de integração

---

##  Contribuindo

Contribuições são bem-vindas! Para contribuir:

1. Faça um fork do projeto
2. Crie uma branch para sua feature (`git checkout -b feature/nova-funcionalidade`)
3. Commit suas alterações (`git commit -m 'Adiciona nova funcionalidade'`)
4. Envie para o seu fork (`git push origin feature/nova-funcionalidade`)
5. Abra um Pull Request

---


##  Autor

Desenvolvido por [**soareslevi566-lgtm**](https://github.com/soareslevi566-lgtm)
