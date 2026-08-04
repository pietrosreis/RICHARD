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

## Limitação importante

Os dados ficam no navegador de cada convidado (`localStorage`), então **as reservas
não são compartilhadas entre pessoas**. Para uma lista realmente sincronizada é
preciso um back-end (por exemplo Supabase) guardando a tabela de reservas — a
estrutura de dados atual (`{ produtoId, nome, qtd, status, criadoEm, confirmadoEm }`)
já está pronta para isso.
