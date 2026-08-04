# RICHARD ⚓

Lista de presentes do chá de bebê do Richard — página única, sem back-end.

## Como usar

Abra `index.html` no navegador, ou publique o arquivo em qualquer hospedagem estática
(GitHub Pages, Netlify, Vercel). Não há build nem dependências.

## Como funciona a confirmação de compra

O ponto central é saber **se a pessoa realmente comprou ou não**. Cada presente tem
três números: meta, reservado e confirmado.

1. O convidado clica em **Quero presentear**, informa nome e quantidade.
2. O item fica **reservado** (barra listrada amarela) e a loja abre em outra aba.
3. Ao voltar, aparece o aviso **“Você conseguiu comprar?”** com três respostas:
   - ✅ **Sim, já comprei** → vira compra confirmada (barra verde).
   - ⏳ **Ainda não** → continua reservado, com aviso amarelo no card.
   - ❌ **Não vou comprar** → o item volta imediatamente para a lista.
4. Reservas sem confirmação passam a exibir um alerta depois de **24h** e
   **expiram em 72h**, liberando o item automaticamente para outro convidado.
5. O bloco **Meus presentes** lista as reservas feitas naquele dispositivo, com os
   botões *Já comprei*, *Abrir loja* e *Liberar*. Um lembrete aparece ao reabrir a
   página enquanto houver reserva sem confirmação.

Os prazos ficam nas constantes `TTL_HORAS` e `AVISO_HORAS` no início do `<script>`.

## Outras melhorias

- Persistência em `localStorage` (a lista não zera ao recarregar).
- Resumo geral no topo: confirmados, aguardando confirmação, faltantes e % do enxoval.
- Busca por nome (EN/PT) e filtros de categoria gerados a partir do catálogo.
- Nome do convidado obrigatório, para os pais saberem quem presenteou.
- Exportação das reservas em JSON e opção de apagar os dados do dispositivo.
- Layout responsivo, toasts no lugar de `alert()`, fechar com `Esc`, foco visível,
  rótulos ARIA e respeito a `prefers-reduced-motion`.

## Lista compartilhada (Supabase)

As reservas são gravadas no Supabase, então **todos os convidados enxergam a mesma
lista**: se alguém reserva ou confirma um presente, os outros veem em até 20 segundos
(e o card mostra quem reservou/comprou). A configuração fica no início do `<script>`:

```js
const SUPABASE = {
  url: 'https://zsivjijnljfexqyqmbnb.supabase.co',
  chave: 'sb_publishable_…',   // chave pública, protegida por RLS
  tabela: 'richard_reservas'
};
```

Deixe `url` vazio para voltar ao modo local (só o navegador de cada um).

### Tabela e regras de acesso

`public.richard_reservas` — `id`, `dispositivo_id`, `produto_id`, `nome`, `qtd`,
`status` (`pendente` / `confirmado` / `cancelado`), `criado_em`, `confirmado_em`.

O site manda o cabeçalho `x-dispositivo-id` em toda requisição e as políticas de RLS
usam esse valor:

| Operação | Regra |
| --- | --- |
| `select` | liberado — todos precisam ver o que já foi comprado |
| `insert` | só em nome do próprio aparelho e sempre como `pendente` |
| `update` | só a própria reserva; um gatilho impede mudar nome, produto ou quantidade |
| `delete` | bloqueado — nada é apagado, apenas marcado como `cancelado` |

### Quando a internet cai

O site guarda o último retrato da lista, continua mostrando o que já foi comprado,
avisa no cabeçalho (`🟠 Sem conexão`) e coloca as reservas feitas offline numa fila,
que sobe sozinha quando a conexão volta.

### Limitações

- O `x-dispositivo-id` identifica o aparelho, não a pessoa: quem trocar de celular
  perde o controle das próprias reservas (a lista continua correta para todos).
- Como não há login, alguém determinado poderia forjar o cabeçalho de outro aparelho.
  É proposital — pedir cadastro para um chá de bebê afastaria os convidados.
- A verificação de "ainda tem vaga" acontece com os dados da última sincronização,
  então duas pessoas reservando o mesmo item no mesmo segundo podem passar as duas.
