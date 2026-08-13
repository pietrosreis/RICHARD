# RICHARD ⚓

Lista de presentes do chá de bebê do Richard — página única, sem back-end.

## Como usar

Abra `index.html` no navegador. Não há build nem dependências.

## Publicação

O site está no ar em **https://kingrichard.vercel.app**.

O projeto da Vercel é conectado a este repositório: **todo merge na `main` publica em
produção automaticamente**, e cada pull request ganha uma URL de pré-visualização. Não
há build — a Vercel serve o `index.html` direto.

> O GitHub Pages foi tentado antes e não vingou: o `GITHUB_TOKEN` do Actions não
> consegue criar o site (`Resource not accessible by integration`), só sairia ligando
> o Pages à mão em **Settings → Pages**. Como a Vercel já cobre a publicação, o
> workflow foi removido para não deixar falha vermelha em todo commit.

## Visual

Tema náutico em azul-marinho, areia e latão. O topo é um hero com medalhão da âncora,
filete com losango e uma onda em SVG que costura o cabeçalho com o corpo da página; o
resumo flutua sobre ela em quatro tiles com ícones e um anel de progresso. Os cards têm
faixa superior que muda de cor conforme o estado (disponível, reservado, meta atingida),
ícone por categoria, barra de progresso em duas camadas — verde para comprado, listrado
para reservado — e entrada escalonada.

Fontes do sistema (`Iowan Old Style`/`Palatino`/`Georgia` nos títulos), ícones em SVG
inline, nenhum arquivo externo: a página continua sendo um HTML só, sem requisição de
rede além do Supabase. Todo texto passa de 4.5:1 de contraste.

### Ilustração de fundo no topo

O cabeçalho usa `assets/farol.jpg` — o farol sobre o forte, com estrelinhas e mar.

O site procura, nesta ordem, `assets/farol.jpg`, `farol.png`, `fundo.jpg` e `fundo.png`
(`ARQUIVOS_FUNDO`, no fim do `<script>`) e usa o primeiro que **carregar de verdade**
(`Image.onload`). Achando um, preenche `--hero-img` e liga a classe `com-imagem`;
sem nenhum arquivo, o topo volta ao degradê e nada quebra. Para trocar a arte, basta
substituir o arquivo — nenhuma edição de código.

Com a ilustração ativa o cabeçalho fica mais alto, o medalhão da âncora sai de cena (o
farol já é o emblema) e entra um véu em duas camadas: mais leve no centro, onde ficam o
farol e as estrelas, e fechado embaixo, onde entra a onda. O enquadramento é
`center 30%` com `cover` — no desktop mostra o farol atrás do título; no celular a
ilustração cabe quase inteira.

O véu foi calibrado medindo o fundo **sem o texto na amostra** (medir com o texto
inflava o resultado). Pior ponto sob cada elemento: título 5,4:1 (mínimo 3:1 para texto
grande), parágrafo 5,5:1 e pílula de sincronização 8,6:1 (mínimo 4,5:1).

A imagem foi reencodada para 216 KB (de 450 KB) — JPEG progressivo, qualidade 78, sem
EXIF, mantendo os 1051×1496 originais.

## Idiomas

A página inteira funciona em **português do Brasil** e **inglês do Reino Unido** — botões
`PT` / `EN` no canto superior direito. O idioma inicial vem do navegador (`navigator.language`),
a escolha fica salva no `localStorage` e a troca é instantânea, sem recarregar.

Os textos ficam no objeto `TEXTOS` (`pt` e `en`) no início do `<script>`; strings com
número ou nome dentro são funções (`avisoPendente(n, quem, atraso)`). O HTML estático usa
atributos `data-i18n` (texto), `data-i18n-html` (com marcação) e `data-i18n-attr`
(`placeholder`, `aria-label`), preenchidos por `aplicarIdioma()`.

Cada presente tem nome e descrição nos dois idiomas (`nome_pt`/`nome_en`,
`desc_pt`/`desc_en`); o card mostra o idioma escolhido no título e o outro logo abaixo,
já que as lojas são britânicas. A busca procura nos quatro campos, então "towel" e
"toalha" acham o mesmo item em qualquer idioma. Horários seguem o locale
(`pt-BR` / `en-GB`), e `<html lang>`, `<title>` e a meta description acompanham a troca.

## Lista da Amazon (entrega em casa)

Uma faixa em destaque abre a área de conteúdo, antes da busca, convidando quem preferir
comprar na **Amazon UK** — nesse caso o presente vai direto para o endereço da família.
Aponta para a lista de bebê:

```
https://www.amazon.co.uk/baby-reg/heberbarros-reis-december-2026-southall/2YCUE413GSD04
```

O endereço foi enviado com `?ref_=cm_sw_r_apin_dp_…&language=en-US`; os dois parâmetros
saíram — o primeiro é rastreio de compartilhamento e o segundo força inglês americano
numa página britânica. Para restaurar, é só devolvê-los ao `href` da faixa.

## Fraldas

A categoria **Fraldas** tem meta em **pacotes**, não em unidades — cada pacote precisa
ter 50 fraldas ou mais, e a descrição do card diz quantos pacotes fecham o total:

| Tamanho | Total de fraldas | Pacotes |
| --- | --- | --- |
| P (Size 1) | 300 | 6 |
| M (Size 2) | 500 | 10 |
| G (Size 3) | 700 | 14 |
| XG (Size 4) | 600 | 12 |
| XXG (Size 5) | 350 | 7 |

Os links vão para a **Amazon UK** (`nappies size N jumbo pack`), como o resto do site.
A lista original trazia links da Amazon Brasil; foram trocados porque os convidados
compram no Reino Unido.

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

### Desistir da reserva

Quem reservou pode liberar o item **sem sair do card**: aparece um botão *Liberar minha
reserva* logo abaixo do botão principal, visível só para o aparelho dono da reserva.
Havendo mais de uma reserva sua no mesmo item, o botão vira *Ver minhas reservas* e leva
ao painel. Liberar uma compra **já confirmada** também é possível, com uma pergunta
diferente para não acontecer sem querer. Nada é apagado no banco: a linha vira
`cancelado` e o item volta à lista para os outros convidados.

Os prazos ficam nas constantes `TTL_HORAS` e `AVISO_HORAS` no início do `<script>`.

## Outras melhorias

- Persistência em `localStorage` (a lista não zera ao recarregar).
- Resumo geral no topo: confirmados, aguardando confirmação, faltantes e % do enxoval.
- Busca por nome (EN/PT) e filtros de categoria gerados a partir do catálogo.
- Nome do convidado obrigatório, para os pais saberem quem presenteou.
- Exportação das reservas em JSON e limpeza dos dados do aparelho existem, mas
  **fora da interface**: `exportarDados()` e `limparMeusDados()` são chamadas pelo
  console do navegador, para nenhum convidado tropeçar nelas.
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
