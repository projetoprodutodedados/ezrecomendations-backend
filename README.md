# ezrecomendations-backend

Para modelar o banco de dados de forma eficiente, é importante definir claramente as relações entre as tabelas. Vamos analisar cada tabela e suas relações:

Users: Cada usuário pode ter várias recomendações, mas cada recomendação pertence a um único usuário.

Relação: 1 para N (Um usuário pode ter várias recomendações)
Recomendations: Cada recomendação está associada a um único usuário e pode conter vários filmes recomendados. No entanto, a estrutura atual da tabela Recomendations precisa ser ajustada para refletir corretamente a relação entre recomendações e filmes.

Relação: N para N (Uma recomendação pode conter vários filmes e um filme pode estar em várias recomendações)
Movies: Cada filme pode estar em várias recomendações e pode ser associado a vários datasets de streaming (Netflix, Prime, etc.).

Relação: N para N (Um filme pode estar em várias recomendações e um filme pode estar em vários datasets de streaming)
NETFLIX_DATASET e PRIME_DATASET: Cada dataset contém informações sobre filmes específicos. Esses filmes podem estar na tabela Movies.

Relação: 1 para N (Um dataset pode conter vários filmes, mas cada filme pertence a um único dataset)
Ajustes na Modelagem
Para refletir corretamente as relações N para N, é necessário criar tabelas intermediárias (join tables). Vamos ajustar a modelagem:

Tabela Users



CREATE TABLE Users (
    id INT PRIMARY KEY,
    name VARCHAR(255),
    email VARCHAR(255),
    password VARCHAR(255)
);
Tabela Movies



CREATE TABLE Movies (
    id INT PRIMARY KEY,
    title VARCHAR(255),
    year INT,
    imdb FLOAT,
    gender VARCHAR(255),
    streaming VARCHAR(255)
);
Tabela Recomendations



CREATE TABLE Recomendations (
    id INT PRIMARY KEY,
    userId INT,
    FOREIGN KEY (userId) REFERENCES Users(id)
);
Tabela RecomendationMovies (Join Table para Recomendations e Movies)



CREATE TABLE RecomendationMovies (
    recomendationId INT,
    movieId INT,
    FOREIGN KEY (recomendationId) REFERENCES Recomendations(id),
    FOREIGN KEY (movieId) REFERENCES Movies(id),
    PRIMARY KEY (recomendationId, movieId)
);
Tabela MoviesLiked (Join Table para Movies Liked)



CREATE TABLE MoviesLiked (
    recomendationId INT,
    movieId INT,
    FOREIGN KEY (recomendationId) REFERENCES Recomendations(id),
    FOREIGN KEY (movieId) REFERENCES Movies(id),
    PRIMARY KEY (recomendationId, movieId)
);
Tabela MoviesUnliked (Join Table para Movies Unliked)



CREATE TABLE MoviesUnliked (
    recomendationId INT,
    movieId INT,
    FOREIGN KEY (recomendationId) REFERENCES Recomendations(id),
    FOREIGN KEY (movieId) REFERENCES Movies(id),
    PRIMARY KEY (recomendationId, movieId)
);
Tabela NETFLIX_DATASET



CREATE TABLE NETFLIX_DATASET (
    id INT PRIMARY KEY,
    title VARCHAR(255),
    gender VARCHAR(255),
    director VARCHAR(255)
);
Tabela PRIME_DATASET



CREATE TABLE PRIME_DATASET (
    id INT PRIMARY KEY,
    title VARCHAR(255),
    gender VARCHAR(255),
    director VARCHAR(255)
);
Relações Entre Tabelas
Users e Recomendations: 1 para N
Recomendations e Movies: N para N (via RecomendationMovies)
Movies e MoviesLiked: N para N (via MoviesLiked)
Movies e MoviesUnliked: N para N (via MoviesUnliked)
Movies e NETFLIX_DATASET: 1 para N
Movies e PRIME_DATASET: 1 para N
Considerações Adicionais
Normalização: Certifique-se de que os dados estão normalizados para evitar redundâncias e inconsistências.
Índices: Adicione índices nas colunas frequentemente usadas em consultas para melhorar o desempenho.
Integridade Referencial: Use chaves estrangeiras para manter a integridade referencial entre as tabelas.
Escalabilidade: Considere a escalabilidade do banco de dados, especialmente se o volume de dados for grande.

Vamos simular o fluxo de dados de um usuário real utilizando a plataforma de sugestão de filmes. Abaixo está uma descrição passo a passo do processo, desde o cadastro do usuário até a recomendação de filmes e o feedback.

1. Cadastro do Usuário
O usuário se cadastra na plataforma fornecendo suas informações básicas.




INSERT INTO Users (id, name, email, password) VALUES (1, 'John Doe', 'john.doe@example.com', 'password123');
2. Preenchimento do Formulário de Preferências
O usuário preenche um formulário indicando suas preferências de filmes. Com base nessas preferências, a plataforma gera recomendações.

3. Geração de Recomendações
A plataforma utiliza os dados do formulário e consulta os datasets de filmes (Movies, NETFLIX_DATASET, PRIME_DATASET) para gerar recomendações.

Exemplo de Consulta para Recomendações



SELECT m.id, m.title, m.year, m.imdb, m.gender, m.streaming
FROM Movies m
JOIN NETFLIX_DATASET n ON m.title = n.title
JOIN PRIME_DATASET p ON m.title = p.title
WHERE m.gender = 'Action' AND m.imdb > 7.0
LIMIT 5;
4. Armazenamento das Recomendações
As recomendações geradas são armazenadas na tabela Recomendations.




INSERT INTO Recomendations (id, userId) VALUES (1, 1);
Armazenamento dos Filmes Recomendados



INSERT INTO RecomendationMovies (recomendationId, movieId) VALUES (1, 101), (1, 102), (1, 103);
5. Exibição das Recomendações ao Usuário
A plataforma exibe os filmes recomendados ao usuário.

6. Feedback do Usuário
O usuário pode fornecer feedback sobre as recomendações, indicando se gostou ou não dos filmes.

Exemplo de Feedback



INSERT INTO MoviesLiked (recomendationId, movieId) VALUES (1, 101);
INSERT INTO MoviesUnliked (recomendationId, movieId) VALUES (1, 102);
7. Atualização das Recomendações
Com base no feedback do usuário, a plataforma pode atualizar as recomendações, substituindo filmes que o usuário não gostou por novos filmes.

Exemplo de Atualização



DELETE FROM RecomendationMovies WHERE recomendationId = 1 AND movieId = 102;
INSERT INTO RecomendationMovies (recomendationId, movieId) VALUES (1, 104);
8. Retroalimentação do Banco de Dados
Os dados de feedback são utilizados para melhorar os algoritmos de recomendação, ajustando os modelos de Machine Learning para fornecer recomendações mais precisas no futuro.

Resumo do Fluxo de Dados
Cadastro do Usuário: Dados do usuário são inseridos na tabela Users.
Formulário de Preferências: Preferências do usuário são coletadas.
Geração de Recomendações: Filmes são recomendados com base nas preferências e armazenados na tabela Recomendations.
Exibição das Recomendações: Filmes recomendados são exibidos ao usuário.
Feedback do Usuário: Feedback é coletado e armazenado nas tabelas MoviesLiked e MoviesUnliked.
Atualização das Recomendações: Recomendações são atualizadas com base no feedback.
Retroalimentação: Feedback é utilizado para melhorar os algoritmos de recomendação.
Esse fluxo garante que a plataforma forneça recomendações personalizadas e melhore continuamente a qualidade das sugestões com base no feedback dos usuários.