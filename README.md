# Comanda online · Protótipo

Protótipo de um sistema de comanda online para restaurantes. O cliente escolhe os itens pelo celular, informa a mesa e envia o pedido. A cozinha vê o pedido em um painel e atualiza o status até a entrega.

Tudo está em um único arquivo, `comanda-online.html`, com HTML, CSS e JavaScript puros (sem frameworks e sem build).

## Como usar

1. Baixe `comanda-online.html` e abra no navegador (duplo clique).
2. Em uma aba, use **Cardápio** para montar e enviar um pedido.
3. Em outra aba do mesmo navegador, abra **Cozinha** e avance o status do pedido.
4. Volte ao cardápio e veja o status mudar em "Meus pedidos".

As duas telas também abrem direto por endereço: `#cardapio` e `#cozinha`.

## Estrutura do arquivo

O arquivo tem três blocos, nesta ordem:

| Bloco | O que contém |
|---|---|
| `<style>` | Variáveis de cor e fonte no `:root`, tema claro e escuro automático, layout em grid, estilo do ticket, painel da cozinha e ajustes para celular. |
| `<body>` | Cabeçalho com navegação, a seção `#v-cardapio` (cliente) e a seção `#v-cozinha` (cozinha). Só uma fica visível por vez. |
| `<script>` | Toda a lógica, dividida em partes comentadas. |

## O script, parte por parte

### Dados do cardápio

`MENU` é uma lista de itens, cada um com `id`, `cat` (categoria), `nome`, `desc` e `preco`. Para mudar o cardápio, edite essa lista. As categorias das abas vêm automaticamente do campo `cat`.

### Estado da tela

- `cat`: categoria selecionada.
- `cart`: carrinho no formato `{ idDoItem: quantidade }`.
- `form`: mesa, nome e observações digitados.

### Armazenamento

O objeto `store` lê e grava no `localStorage` e, se o navegador bloquear, usa a memória como alternativa. Duas chaves são usadas:

- `comanda:pedidos`: lista de todos os pedidos.
- `comanda:meus`: números dos pedidos feitos naquele navegador (usado em "Meus pedidos").

### Cardápio e comanda

| Função | O que faz |
|---|---|
| `renderTabs()` | Desenha as abas de categoria. |
| `renderMenu()` | Lista os itens da categoria atual com os botões + e −. |
| `renderTicket()` | Desenha a comanda (itens, total, campos e botão de envio). O botão fica desabilitado sem itens ou sem mesa. |
| `renderMine()` | Mostra os pedidos do cliente com o status. |
| `enviar()` | Cria o pedido, gera o número (começa em 101), salva e limpa o carrinho. |

### Cozinha

| Função | O que faz |
|---|---|
| `renderBoard()` | Desenha as colunas Novo, Preparando e Pronto. |
| `avancar(id, status)` | Muda o status de um pedido e redesenha o painel. |

O fluxo de status é `novo → preparo → pronto → entregue`. Pedidos entregues saem do painel, mas continuam salvos.

### Rotas e sincronização

- `route()` mostra a tela certa conforme o `#` da URL.
- O evento `storage` e um `setInterval` de 3 segundos atualizam as telas quando outra aba altera os dados.
- Os eventos de clique e digitação usam delegação: um único `addEventListener` no `document` trata todos os botões pelos atributos `data-*` (`data-inc`, `data-dec`, `data-cat`, `data-go`, `data-next`).

## Formato de um pedido

```json
{
  "id": 101,
  "mesa": "7",
  "nome": "Ana",
  "obs": "sem cebola",
  "itens": [{ "n": "Picanha na chapa", "q": 1, "p": 62 }],
  "total": 62,
  "status": "novo",
  "hora": "2026-11-10T19:30:00.000Z"
}
```

## Limitações do protótipo

- **Sem servidor:** os dados ficam só no navegador. Cliente e cozinha só se enxergam se estiverem no mesmo navegador e aparelho.
- **Sem login:** qualquer pessoa abre o painel da cozinha.
- **Sem pagamento, estoque ou impressão.**
- Números de pedido só são únicos dentro do mesmo navegador.

## Próximos passos sugeridos

1. **Backend:** trocar `store.get` e `store.set` por chamadas a uma API (Node + banco de dados, Firebase ou Supabase). As funções `enviar` e `avancar` são os pontos principais de troca.
2. **Tempo real:** usar WebSocket ou o recurso de tempo real do Firebase/Supabase no lugar do `setInterval`.
3. **QR code por mesa:** gerar um link como `?mesa=7` e preencher o campo de mesa automaticamente.
4. **Acesso da cozinha:** proteger a rota `#cozinha` com login.
5. **Extras:** impressão da comanda, aviso sonoro de novo pedido, pagamento (Pix) e relatório do dia.

## Personalização rápida

- **Nome do restaurante:** troque o texto em `.brand`.
- **Cores e fontes:** edite as variáveis no começo do `<style>` (`--accent`, `--bg`, `--ink`, `--sans`, `--mono`). Há um conjunto para o tema escuro logo abaixo.
- **Itens e preços:** edite o array `MENU` no script.
