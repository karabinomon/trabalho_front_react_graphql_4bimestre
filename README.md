# Trabalho de Desenvolvimento de Sistemas - Consumo de API GraphQL no Frontend

**Escola Manoel Ignácio** - Desenvolvimento de Sistemas - 3ª série B - 2026 <br>
**Programação Frontend**: Atividade Avaliativa (Peso 1) <br>
`nome@dev:~$_`

---

## 1. Descrição da Atividade

No desenvolvimento de aplicações modernas, a comunicação entre o cliente (Frontend) e o servidor (Backend) é o alicerce para exibir e manipular informações. Tradicionalmente, utilizamos arquiteturas **REST**, onde cada recurso possui uma URL específica (por exemplo, `/api/students`, `/api/courses`).

Nesta atividade, exploraremos o **GraphQL**, uma linguagem de consulta criada pelo Facebook que revoluciona essa comunicação:
1. **Ponto Único de Acesso (*Single Endpoint*):** Todas as requisições são enviadas para a mesma URL via método HTTP `POST`.
2. **Sem *Over-fetching* ou *Under-fetching*:** O cliente especifica no corpo da requisição exatamente quais campos deseja receber, evitando transferir dados desnecessários pela rede.
3. **Payload Transparente:** Por baixo dos panos, uma requisição GraphQL não exige bibliotecas pesadas; ela é simplesmente uma chamada HTTP enviando um JSON com a propriedade `query`.

O objetivo desta prática é integrar uma aplicação frontend a uma API GraphQL real em produção, focando **exclusivamente na lógica de consulta (Queries) e exibição dos dados**, sem necessidade de criar estilos visuais complexos ou mexer na estrutura visual base.

---

## 2. Objetivos de Aprendizagem

* Compreender o funcionamento do protocolo GraphQL sobre requisições HTTP (`POST` com payload JSON).
* Diferenciar o modelo de múltiplos endpoints REST do endpoint único GraphQL.
* Escrever documentos de consulta (Queries) solicitando campos aninhados e relacionamentos.
* Utilizar a `Fetch API` nativa do JavaScript para despachar requisições assíncronas (`async/await`).
* Manipular a resposta estruturada `{ data: { ... } }` e renderizar os dados dinamicamente no estado da aplicação.

---

## 3. Dados da API GraphQL (Produção)

* **Endpoint Oficial:** `https://testing-graphql.onrender.com/graphql`
* **Método HTTP:** `POST`
* **Cabeçalho Obrigatório:** `Content-Type: application/json`
* **Permissão:** Apenas leitura (**Queries**)

### Esquema de Consultas Suportado:

1. **Alunos (`students`):**
   ```graphql
   query {
     students {
       id
       name
       email
       course {
         id
         name
         credits
       }
     }
   }
   ```

2. **Cursos (`courses`):**
   ```graphql
   query {
     courses {
       id
       name
       credits
       students {
         id
         name
       }
     }
   }
   ```

3. **Professores (`teachers`):**
   ```graphql
   query {
     teachers {
       id
       name
       courses {
         id
         name
       }
     }
   }
   ```

---

## 4. Como Fazer uma Consulta GraphQL (Exemplo Prático)

Para consumir a API, não é necessário instalar clientes pesados (como Apollo Client ou Relay). O navegador já possui a ferramenta nativa ideal: o **`fetch`**.

Observe o exemplo abaixo que realiza a consulta de alunos:

```javascript
// 1. Defina o endereço da API
const GRAPHQL_ENDPOINT = "https://testing-graphql.onrender.com/graphql";

// 2. Escreva a sua Query como uma string
const BUSCAR_ALUNOS_QUERY = `
  query {
    students {
      id
      name
      email
      course {
        name
      }
    }
  }
`;

// 3. Função assíncrona para disparar a requisição HTTP POST
async function carregarAlunos() {
  try {
    const resposta = await fetch(GRAPHQL_ENDPOINT, {
      method: "POST",
      headers: {
        "Content-Type": "application/json",
      },
      body: JSON.stringify({
        query: BUSCAR_ALUNOS_QUERY, // O documento GraphQL vai aqui dentro
      }),
    });

    // Converte o corpo da resposta para objeto JSON
    const resultado = await resposta.json();

    // Em GraphQL, os dados bem-sucedidos sempre vêm dentro da chave 'data'
    if (resultado.data && resultado.data.students) {
      console.log("Alunos recebidos:", resultado.data.students);
      return resultado.data.students;
    }

    // Caso ocorra algum erro de validação ou resolver no GraphQL
    if (resultado.errors) {
      console.error("Erros do GraphQL:", resultado.errors);
    }
  } catch (erro) {
    console.error("Erro na comunicação de rede:", erro);
  }
}
```

---

## 5. Tarefas do Estudante

Você deve conectar o seu código à API e implementar as três consultas na interface:

1. **Consulta 1 - Listar Alunos:**
   - Disparar a query `students` e listar na tela o **Nome**, **E-mail** e o **Nome do Curso** de cada aluno.
2. **Consulta 2 - Listar Cursos:**
   - Disparar a query `courses` e exibir o **Nome do Curso**, quantidade de **Créditos** e os nomes dos alunos que pertencem a ele.
3. **Consulta 3 - Listar Professores:**
   - Disparar a query `teachers` e exibir o **Nome do Professor** e as matérias/cursos que ele leciona.
4. **Tratamento de Estado:**
   - Exibir uma mensagem simples de "Carregando..." enquanto a resposta do servidor não chega.
   - Exibir mensagem de erro caso ocorra falha na conexão.

> 💡 **Nota de escopo:** Não perca tempo criando estilos visuais elaborados nem configurações complexas de build. O foco da avaliação é a lógica de requisição, o formato do payload e a correta exibição dos dados retornados.

---

## 6. Critérios de Avaliação

| Critério de Aceitação |
**O NÃO FUNCIONAMENTO DOS REQUISITOS A SEGUIR PODEM GERAR DESCONTOS NA NOTA**
|---|---|---|
| **Estrutura da Requisição** | Montagem correta da chamada `fetch` (método `POST`, headers `application/json` e payload com `{ query }`). | -3,0 pts |
| **Sintaxe das Queries** | Elaboração correta das consultas GraphQL solicitando os campos solicitados (incluindo objetos aninhados como `course`). | -3,0 pts |
| **Renderização dos Dados** | Extração adequada de `resultado.data` e exibição dinâmica das três listas no Frontend. | -3,0 pts |
| **Tratamento de Estados** | Feedback visual de carregamento (*loading*) e captura de erros de requisição. | -1,0 pts |
---

## 7. Prazos e Pontuação

* **Entrega até o prazo regular (16 de outubro):** Nota máxima (10,0).
* **Entrega após o prazo regular (16 de outubro):** A nota máxima será limitada a 5,0.
* **Após 23 de outubro:** A atividade não será mais aceita.

---

## 8. Envio e Comprovação

* **Identificação Obrigatória:** Salve o seu código em um repositório no GitHub com a sua identificação.
* **Envio por E-mail:** Envie o link do repositório para o e-mail:  
  **`sergiogabriel@prof.educacao.sp.gov.br`**  
  **Assunto:** `Trabalho de Desenvolvimento de Sistemas - Consumo GraphQL - [seu_nome_aqui] - 3ª série B`  
  *(Atividades entregues sem identificação poderão sofrer descontos na nota ou não ser avaliadas).*

