# Catálogo de Filmes

Este é um projeto de catálogo de filmes criado com Vue.js e Vite. Ele busca e exibe filmes da API Simkl.

## Como Executar

1. Instale as dependências:
   ```sh
   npm install
   ```
2. Execute o servidor de desenvolvimento:
   ```sh
   npm run dev
   ```

## Requisitos do Projeto

O projeto foi desenvolvido para atender aos seguintes requisitos:

- **Vue.js**: Utilizado como o framework principal para a construção da interface de usuário reativa.
- **JSON**: Os dados dos filmes são recebidos da API Simkl em formato JSON.
- **Requisições Ajax com `fetch`**: O conceito de Ajax (buscar dados de um servidor sem recarregar a página) é implementado usando a API `fetch` nativa do navegador. Esta é a abordagem moderna e padrão para fazer chamadas assíncronas. Um exemplo é a função `fetchFromApi` no `HomeView.vue`:

  *Arquivo: `src/views/HomeView.vue`*
  ```javascript
  const fetchFromApi = async (endpoint) => {
    const url = `https://api.simkl.com/${endpoint}`;
    const response = await fetch(url, {
      headers: { 'simkl-api-key': clientId }
    });
    if (!response.ok) throw new Error(`Erro na rede: ${response.statusText}`);
    return await response.json();
  };
  ```

- **jQuery**: Utilizado para manipulações específicas do DOM que estão fora do escopo principal do Vue.

### Uso do jQuery

Neste projeto, o jQuery é carregado via CDN no arquivo `index.html` e é utilizado para uma tarefa específica de manipulação do DOM global:

- **Controle de Scroll da Página**: Quando um modal de detalhes de um filme é aberto, um evento de clique no componente Vue aciona uma função que utiliza o jQuery para adicionar a classe `modal-open` ao elemento `<body>` da página.

  *Arquivo: `src/views/HomeView.vue`*
  ```javascript
  const openModal = async (movie) => {
    // ... código do Vue
    // jQuery: Adiciona classe para travar o scroll do fundo
    $('body').addClass('modal-open');
    // ... código do Vue
  };

  const closeModal = () => {
    // ... código do Vue
    // jQuery: Remove classe para restaurar o scroll do fundo
    $('body').removeClass('modal-open');
  };
  ```

- Esta classe, definida em `src/assets/main.css`, aplica o estilo `overflow: hidden;` ao corpo da página, o que impede que o conteúdo de fundo role enquanto o modal estiver ativo. Esta é uma forma prática de gerenciar um estado global (como a abertura de um modal) que afeta elementos fora do controle direto do Vue, sendo um caso de uso comum e seguro para o jQuery em aplicações modernas.