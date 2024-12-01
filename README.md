# API REST - E-Commerce

## Documentos de Requisitos
A API REST criada é uma plataforma de E-Commerce, com funcionalidades essenciais para gerenciar pedidos, produtos, categorias, usuários e pagamentos. A seguir, apresento um diagrama UML que ilustra a estrutura do projeto, com as entidades principais: Order, OrderItem, Product, Category, Payment, User e o enum OrderStatus. 

![1](https://github.com/user-attachments/assets/3ec8f5a8-ef3c-4f4b-83fd-9bcffd15023d)

Este diagrama foi crucial para entender os requisitos e organizar o modelo de dados do sistema de maneira eficiente.

##
## Conexão com Banco de Dados H2 para Testes
Para persistência de dados, foi utilizado o banco de dados H2 em modo de teste. Aprendi a criar o modelo de domínio a partir da especificação utilizando Java e Spring Boot, e a mapear as entidades para o banco de dados relacional através do mecanismo de ORM (Object-Relational Mapping). O sistema utiliza relacionamentos muitos-para-um, um-para-um e muitos-para-muitos para refletir a lógica do negócio de forma eficiente. Além disso, realizei o seeding da base de dados para popular automaticamente com dados de teste, facilitando a validação e os testes da API.

##
## Camadas, CRUD, Exceções e Validações
A API REST foi desenvolvida e organizada com o padrão de camadas (DTOs, Repositories, Controllers e Services), seguindo boas práticas e aplicando o padrão arquitetural MVC (Model-View-Controller). Cada camada tem uma responsabilidade bem definida, facilitando a manutenção e escalabilidade da aplicação. Foram implementadas as operações básicas do CRUD (Criar, Ler, Atualizar, Excluir) utilizando os verbos HTTP.

![2](https://github.com/user-attachments/assets/33763d58-47c1-4646-97e5-d966971a1670)

### Exceções e Handlers
Para tratar exceções de forma centralizada, criamos um ControllerExceptionHandler que captura erros como ResourceNotFoundException, retornando respostas estruturadas e amigáveis para o usuário.

    @ControllerAdvice
    public class ControllerExceptionHandler {
    
        @ExceptionHandler(ResourceNotFoundException.class)
        public ResponseEntity<CustomError> resourceNotFound(ResourceNotFoundException e, HttpServletRequest request) {
            HttpStatus status = HttpStatus.NOT_FOUND;
            CustomError err = new CustomError(Instant.now(), status.value(), e.getMessage(), request.getRequestURI());
            return ResponseEntity.status(status).body(err);
        }

### Validações
Utilizamos o Bean Validation com anotações como @NotBlank, @Size, @NotNull e @Positive para garantir que os dados enviados para a API atendam aos requisitos de integridade. Por exemplo, a classe ProductDTO usa essas anotações para validar atributos como nome, descrição, preço e categorias.

    public class ProductDTO {
    
        private Long id;
    
        @Size(min = 3, max = 80, message = "Nome precisa ter de 3 a 80 caracteres")
        @NotBlank(message = "Campo requerido")
        private String name;
    
        @Size(min = 10, message = "Descrição precisa ter no mínimo 10 caracteres")
        @NotBlank(message = "Campo requerido")
        private String description;
    
        @NotNull(message = "Campo requerido")
        @Positive(message = "O preço deve ser positivo")
        private Double price;
        private String imgUrl;
    
        @NotEmpty(message = "Deve ter pelo menos uma categoria")
        private List<CategoryDTO> categories = new ArrayList<>();

##
## Query Methods, JPA, Consultas SQL e JPQL
Aqui, aprendi a utilizar JPA para acessar e manipular dados no banco de dados. Para facilitar consultas específicas, criamos Query Methods e usamos JPQL (Java Persistence Query Language) para buscar dados no banco.

    public interface ProductRepository extends JpaRepository<Product, Long> {
    
        @Query("SELECT obj " +
                "FROM Product obj " +
                "WHERE UPPER(obj.name) LIKE UPPER(CONCAT('%', :name, '%'))")
        Page<Product> searchByName(String name, Pageable pageable);
    }

Essa abordagem de consultas com JPA e JPQL permite otimizar o desempenho e simplificar a interação com o banco de dados.

##
## Login e Controle de Acesso
Para garantir a segurança da nossa plataforma, implementamos um sistema de login e controle de acesso com OAuth2 e JWT (JSON Web Tokens). O processo de autenticação é feito de maneira segura com BCrypt para o armazenamento das senhas de forma codificada. 

JWT: Ao fazer login, o sistema gera um token JWT que é utilizado para autenticar o usuário nas requisições subsequentes. 

CORS: Configuramos as regras de CORS para controlar quais origens podem acessar a API.

![3](https://github.com/user-attachments/assets/2e756d96-39ce-4847-ba60-42d4f6f4d088)
