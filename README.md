


# 🎓 Sistema de Cadastro de Faculdade

## 🧾 Descrição do Projeto
O **Sistema de Cadastro de Faculdade** tem como objetivo gerenciar informações acadêmicas de uma instituição de ensino superior.  
Ele permite o controle de **departamentos, professores, cursos, disciplinas, alunos e matrículas**, garantindo integridade e organização dos dados.

O projeto foi desenvolvido em **MySQL** e segue boas práticas de modelagem e normalização de dados, com relacionamentos bem definidos entre as entidades.

---

## 🧩 Funcionalidades Principais
- Cadastro e gerenciamento de **departamentos**;
- Registro de **professores**, com vínculo ao departamento;
- Criação de **cursos**, associando coordenadores e departamentos;
- Controle de **disciplinas** vinculadas a cursos e professores;
- Cadastro de **alunos** e acompanhamento de **matrículas**;
- Registro de **notas** e **status** (Cursando, Aprovado, Reprovado);
- **Relacionamento** entre alunos ↔ disciplinas.

---

## 🗃️ Estrutura do Banco de Dados (Script SQL)

```sql
-- Criação do banco
CREATE DATABASE IF NOT EXISTS cadastro_faculdade
  DEFAULT CHARACTER SET utf8mb4
  DEFAULT COLLATE utf8mb4_unicode_ci;

USE cadastro_faculdade;

-- ======================================================
-- 1️⃣ TABELA DEPARTAMENTO
-- ======================================================
CREATE TABLE departamento (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(150) NOT NULL UNIQUE,
  descricao TEXT
);

-- ======================================================
-- 2️⃣ TABELA PROFESSOR
-- ======================================================
CREATE TABLE professor (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(150) UNIQUE NOT NULL,
  telefone VARCHAR(20),
  data_contratacao DATE,
  salario DECIMAL(10,2) DEFAULT 0.00,
  departamento_id BIGINT,
  FOREIGN KEY (departamento_id) REFERENCES departamento(id)
    ON DELETE SET NULL ON UPDATE CASCADE
);

-- ======================================================
-- 3️⃣ TABELA CURSO
-- ======================================================
CREATE TABLE curso (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  descricao TEXT,
  carga_horaria INT NOT NULL,
  departamento_id BIGINT,
  coordenador_id BIGINT,
  FOREIGN KEY (departamento_id) REFERENCES departamento(id)
    ON DELETE SET NULL ON UPDATE CASCADE,
  FOREIGN KEY (coordenador_id) REFERENCES professor(id)
    ON DELETE SET NULL ON UPDATE CASCADE
);

-- ======================================================
-- 4️⃣ TABELA DISCIPLINA
-- ======================================================
CREATE TABLE disciplina (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  ementa TEXT,
  carga_horaria INT NOT NULL,
  curso_id BIGINT,
  professor_id BIGINT,
  FOREIGN KEY (curso_id) REFERENCES curso(id)
    ON DELETE CASCADE ON UPDATE CASCADE,
  FOREIGN KEY (professor_id) REFERENCES professor(id)
    ON DELETE SET NULL ON UPDATE CASCADE
);

-- ======================================================
-- 5️⃣ TABELA ALUNO
-- ======================================================
CREATE TABLE aluno (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nome VARCHAR(150) NOT NULL,
  email VARCHAR(150) UNIQUE NOT NULL,
  telefone VARCHAR(20),
  data_nascimento DATE,
  data_matricula DATE DEFAULT (CURRENT_DATE),
  ativo BOOLEAN DEFAULT TRUE
);

-- ======================================================
-- 6️⃣ TABELA MATRICULA (relação aluno ↔ disciplina)
-- ======================================================
CREATE TABLE matricula (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  aluno_id BIGINT NOT NULL,
  disciplina_id BIGINT NOT NULL,
  data_matricula DATE DEFAULT (CURRENT_DATE),
  nota_final DECIMAL(4,2),
  status ENUM('Cursando', 'Aprovado', 'Reprovado') DEFAULT 'Cursando',
  UNIQUE (aluno_id, disciplina_id),
  FOREIGN KEY (aluno_id) REFERENCES aluno(id)
    ON DELETE CASCADE ON UPDATE CASCADE,
  FOREIGN KEY (disciplina_id) REFERENCES disciplina(id)
    ON DELETE CASCADE ON UPDATE CASCADE
);

-- ======================================================
-- 7️⃣ ÍNDICES
-- ======================================================
CREATE INDEX idx_prof_departamento ON professor(departamento_id);
CREATE INDEX idx_curso_departamento ON curso(departamento_id);
CREATE INDEX idx_disciplina_curso ON disciplina(curso_id);
CREATE INDEX idx_matricula_aluno ON matricula(aluno_id);

-- ======================================================
-- 8️⃣ INSERÇÕES INICIAIS (opcional)
-- ======================================================
INSERT INTO departamento (nome, descricao)
VALUES ('Ciências Exatas', 'Departamento de Matemática, Física e Computação'),
       ('Ciências Humanas', 'Departamento de História, Filosofia e Psicologia');

INSERT INTO professor (nome, email, data_contratacao, salario, departamento_id)
VALUES ('Carlos Almeida', 'carlos.almeida@faculdade.edu', '2015-02-15', 6500.00, 1),
       ('Mariana Souza', 'mariana.souza@faculdade.edu', '2018-03-10', 5800.00, 2);

INSERT INTO curso (nome, descricao, carga_horaria, departamento_id, coordenador_id)
VALUES ('Sistemas de Informação', 'Formação em desenvolvimento de software e gestão de TI', 3200, 1, 1),
       ('Psicologia', 'Curso voltado ao estudo do comportamento humano', 3600, 2, 2);

INSERT INTO disciplina (nome, ementa, carga_horaria, curso_id, professor_id)
VALUES ('Programação I', 'Lógica e fundamentos de programação em Java', 80, 1, 1),
       ('Banco de Dados', 'Modelagem e implementação de bancos relacionais', 80, 1, 1),
       ('Psicologia Geral', 'Introdução à psicologia e seus fundamentos teóricos', 60, 2, 2);

INSERT INTO aluno (nome, email, telefone, data_nascimento)
VALUES ('Ana Ribeiro', 'ana.ribeiro@email.com', '11987654321', '2001-08-25'),
       ('João Pereira', 'joao.pereira@email.com', '21999887766', '2000-12-10');

INSERT INTO matricula (aluno_id, disciplina_id, nota_final, status)
VALUES (1, 1, 8.5, 'Aprovado'),
       (1, 2, 7.0, 'Cursando'),
       (2, 3, NULL, 'Cursando');
````

---

## 🧮 Caso de uso

![DER do Sistema](docs/Caso_de_uso.png)

## 🧮 Classe UML



![DER do Sistema](docs/Classe_UML.png)




---

## 🧠 Protótipos do Sistema

Abaixo estão os protótipos desenvolvidos na Fase 1 do projeto:

* Tela de Login
* Tela de Cadastro de Aluno
* Tela de Cadastro de Professor
* Tela de Matrículas

* Protótipo Figma: https://www.figma.com/proto/R3wfY7HNlmr0yZh9Ey5R2r/Untitled?node-id=41-2702&t=DPXO4NJRPc3XcS2c-0&scaling=min-zoom&content-scaling=fixed&page-id=0%3A1&starting-point-node-id=41%3A329

---

## ⚙️ Como Executar o Projeto

1. **Clone o repositório:**

   ```bash
   git clone https://github.com/SeuUsuario/cadastro_faculdade.git
   ```

2. **Acesse a pasta do projeto:**

   ```bash
   cd cadastro_faculdade
   ```

3. **Execute o script SQL no MySQL:**

   ```sql
   SOURCE script_banco.sql;
   ```

4. **Verifique as tabelas criadas:**

   ```sql
   SHOW TABLES;
   ```

------

## 🧱 Tecnologias Utilizadas

* **MySQL** — Modelagem e criação do banco de dados
* **Draw.io / Lucidchart** — Criação dos diagramas
* **Figma / Canva** — Protótipos de interface
* **Markdown (GitHub)** — Documentação do projeto

---

## 📄 Licença

Este projeto foi desenvolvido para fins **acadêmicos**.
© 2025 — Faculdade Senac. Todos os direitos reservados.

---


.
.
