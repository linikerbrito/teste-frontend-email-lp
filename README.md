# Mesa 4X: email HTML e landing page

Teste técnico de front-end. O repositório tem duas entregas para um produto fictício, a imersão **Mesa 4X**, da marca fictícia **Quadrante Growth Lab**: um email de campanha em HTML (`/email`) e três seções de landing page, com o desafio extra B, um FAQ em acordeão sem JavaScript (`/lp`).

> Tudo aqui é fictício: marca, preços, depoimentos, endereço, CNPJ, política de reembolso e os números do painel da LP. Os links dos botões, do descadastro e das preferências são placeholders (`example.com`).

## Estrutura

```
.
├── README.md
├── .gitignore
├── email/
│   ├── index.html
│   └── assets/
│       ├── logo-quadrante-claro.png
│       └── logo-quadrante-escuro.png
└── lp/
    ├── index.html
    └── styles.css
```

## Como abrir e testar localmente

Não há build, dependência nem JavaScript. Basta clonar e abrir os arquivos no navegador.

```bash
git clone https://github.com/linikerbrito/teste-frontend-email-lp.git
```

- **Email:** abra `email/index.html` (duplo clique serve).
- **LP:** abra `lp/index.html`, ou use qualquer servidor local, como a extensão Live Server do VS Code.

**Email, no navegador**

1. **Mobile:** no DevTools, ative o modo de dispositivo e teste em 360 px e em 320 px.
2. **Dark mode:** no DevTools, abra *More tools > Rendering* e use *Emulate CSS media feature prefers-color-scheme* com o valor `dark`. Isso exercita o bloco `prefers-color-scheme`. Não exercita os seletores do Outlook.com nem o motor do Word, que só um cliente real mostra.
3. **Logos:** o HTML aponta para `assets/...` com caminho relativo, que é o que faz o arquivo abrir local. Num envio real o cliente de email não enxerga a minha pasta, então os `src` precisam virar URLs absolutas hospedadas. Para um teste de envio, faça uma cópia do HTML com essas URLs, sem commitar.

**LP, o que eu olho**

1. Larguras de 320, 375, 768, 900 e 1280 px, sem rolagem horizontal.
2. Teclado: `Tab` do início ao fim, com foco visível em cada link e em cada pergunta do FAQ. O link "Pular para o conteúdo" aparece ao receber foco.
3. Zoom de 200%, sem sobreposição nem conteúdo cortado.
4. Movimento reduzido ativo no sistema: nenhuma transição acontece.
5. Cores soltas: nenhum valor literal deve aparecer fora do `:root`.

```bash
grep -nE "#[0-9A-Fa-f]{3,8}\b" lp/styles.css
```

## Decisões técnicas

### Email

**Estrutura**

- **Tabelas com layout híbrido/fluido.** A tabela externa tem 100% de largura e o contêiner usa `width:100%; max-width:600px`. Assim o email funciona mesmo em cliente que ignora media query. Como o Outlook desktop ignora `max-width`, o contêiner também está dentro de uma *ghost table* de 600 px em comentário condicional (`<!--[if mso]>`).
- **CSS essencial inline, `<style>` mínimo.** O que define a aparência está inline, porque é o que sobrevive à maioria dos clientes. O `<style>` guarda só o que não dá para fazer inline: reset, media query mobile, dark mode e os ajustes para Apple Mail e Outlook.
- **Espaçamento por `padding` em `td`.** Margem em tabela é pouco confiável. As únicas margens do arquivo estão em parágrafos e títulos, para o ritmo vertical do texto. Os espaços entre cartões são células de 10 px com `&nbsp;`, porque células vazias podem colapsar em alguns clientes.
- **`role="presentation"` e atributos de tabela** (`cellpadding`, `cellspacing`, `border`). O editor reclama que são obsoletos, mas em email ainda são o padrão que garante o reset em clientes antigos, então mantive.
- **Fonte de sistema** (Arial, Helvetica, sans-serif), sem web fonts, porque várias não carregam em email.

**Botões**

- **Bulletproof com fallback VML.** O Outlook desktop usa o motor do Word, que ignora `padding` e `border-radius` em links. Ele recebe um retângulo VML (`v:roundrect`) de 48 px de altura e 250 px de largura. Os outros clientes recebem um `<a>` estilizado. O par `<!--[if mso]>` e `<!--[if !mso]><!-->` impede que o Outlook mostre os dois botões. Os dois CTAs seguem exatamente o mesmo padrão.
- **Texto escuro sobre laranja.** O branco sobre o laranja da marca dá cerca de 3,1:1 de contraste. O texto `#14161A` sobre `#FF5A1F` dá 5,8:1.

**Preheader e mobile**

- **Preheader** em um `div` oculto, seguido de uma sequência de `&zwnj;&nbsp;`, para o cliente não completar o preview com o texto seguinte.
- **Mobile.** A media query (`max-width: 600px`) tira o respiro externo, reduz o padding lateral para 20 px, baixa o título de 36 px para 28 px, deixa o botão em largura total e estreita a coluna de horário da agenda para 100 px (o horário mais longo, `14h30–14h45`, ocupa cerca de 87 px em negrito 14 px). Sem a media query o email continua legível, só com margens maiores.

**Dark mode**

- **Classes por papel de cor**, e não por elemento: `bg-page`, `bg-surface`, `bg-card`, `bg-deep`, `text-ink`, `text-muted` e `text-subtle`. O mesmo conjunto serve ao Apple Mail (`prefers-color-scheme: dark`) e ao Outlook.com (`[data-ogsb]` para fundo e `[data-ogsc]` para texto). As cores inline são o estado claro, e as classes com `!important` sobrescrevem no escuro.
- **Troca de logo com duas imagens.** A versão para fundo escuro nasce oculta (`display:none`, `max-height:0`, `mso-hide:all`) e o bloco de dark mode a revela. O botão laranja não muda.
- `meta color-scheme` e `supported-color-schemes` declaram que o email aceita os dois esquemas.

**Rastreamento e acessibilidade**

- **UTMs por botão.** `utm_source=crm`, `utm_medium=email`, `utm_campaign=mesa-4x-nov-2026` e `utm_content` com `cta-hero` ou `cta-final`, para saber qual botão foi clicado. O destino é um placeholder, que numa operação real o ESP trocaria pelo link com rastreamento próprio.
- **Acessibilidade:** `lang="pt-BR"`, `alt` no logo, ordem de leitura linear, hierarquia h1, h2 e h3, e links com texto descritivo. O texto de corpo tem 15 a 16 px e só legendas e rodapé descem para 13 e 14 px.
- **Logo em PNG**, porque o Gmail não renderiza SVG em `<img>`. Há duas versões com fundo transparente.
- **Peso:** o HTML tem pouco menos de 30 KB, longe dos 102 KB a partir dos quais o Gmail corta a mensagem.

Pares de contraste que calculei com a fórmula do WCAG:

| Par | Razão |
|---|---|
| Tinta sobre branco | 18,1:1 |
| Texto de apoio (`#5B606B`) sobre off-white | 5,7:1 |
| Intervalos da agenda (`#6B6F77`) sobre branco | 5,0:1 |
| Tinta sobre laranja (botão) | 5,8:1 |
| Laranja sobre tinta (data do hero) | 5,8:1 |
| Dark mode: apoio (`#A9AEB8`) sobre cartão (`#20242B`) | 7,0:1 |
| Dark mode: intervalos (`#8F95A0`) sobre superfície (`#171A1F`) | 5,8:1 |

### Landing page

**Semântica e estrutura**

- `header`, `nav` (com `aria-label`), `main`, `section` (com `aria-labelledby`) e `footer`. Há um único `h1`, um `h2` por seção e `h3` nos passos e nos planos, sem saltos de nível.
- Os passos são uma lista ordenada (`ol`), os planos e os itens incluídos são listas (`ul`). Coloquei `role="list"` nas listas que perdem os marcadores com `list-style: none`, porque o Safari com VoiceOver deixa de anunciá-las como listas. O editor aponta o atributo como redundante, e a redundância é proposital.
- O primeiro elemento focável é um link "Pular para o conteúdo".
- O logo do cabeçalho é um SVG inline decorativo dentro de um link que tem `aria-label`, para o nome não ser lido duas vezes. Os ícones dos passos também são decorativos (`aria-hidden`).

**CSS**

- Um arquivo (`styles.css`), mobile-first, organizado em seções comentadas: tokens, reset, base, utilitários, componentes e media queries.
- **BEM** (`bloco__elemento--modificador`), por exemplo `ticket-card--recommended`, `process-step__title` e `faq__item`. Os utilitários próprios são só `.container` e `.visually-hidden`.
- **Tokens no `:root`:** cores, escala de espaçamento, raios, sombras, tipografia (com `clamp()` para os títulos), larguras e a duração das transições. A regra que segui é não deixar cor, espaçamento nem raio literais fora do `:root`, e o `grep` da seção anterior serve para conferir. As exceções são as cores dos SVGs inline, que ficam nos atributos do HTML, e o favicon em data URI, que não aceita variáveis CSS.
- **Fonte de sistema** (`system-ui` e alternativas), sem web fonts, sem requisição externa e sem imagens pesadas. O favicon é um SVG em data URI para não gerar 404.
- `text-wrap: balance` nos títulos, para evitar palavras soltas. Onde não houver suporte, a quebra é a normal.

**Acessibilidade e conteúdo**

- **Foco visível** com `:focus-visible` e um contorno duplo (tinta por dentro e branco por fora), pensado para ser legível sobre fundo claro, escuro e sobre o botão laranja. Alvos de toque com pelo menos 44 px de altura.
- **Regra do laranja.** O laranja da marca dá cerca de 3,1:1 sobre branco, abaixo dos 4,5:1 exigidos para texto. Ele aparece só como fundo, borda e detalhe, e sempre com texto escuro por cima. A exceção é o texto da pergunta aberta do FAQ, que usa um tom mais escuro (`--color-orange-text`) para passar no contraste.
- **O plano VIP não depende só de cor:** tem o selo de texto "Recomendado", um contorno mais forte (feito com sombra, para não alterar as dimensões do card) e uma elevação em telas largas.
- **Preços** divididos em valor (grande) e condição (pequena e em cinza), mas lidos em sequência como uma frase só. Cada CTA tem o nome do plano no texto ("Quero o ingresso VIP"), para fazer sentido isolado numa lista de links de leitor de tela.
- O **painel do hero** é decorativo (`aria-hidden`), com a descrição equivalente em texto para leitores de tela e os rótulos visíveis "EXEMPLO" e "Valores ilustrativos, sem promessa de resultado".
- O CTA do hero e o link "Ingressos" do cabeçalho levam à âncora `#planos`. Os CTAs dos cards usam placeholders com UTM por plano (`plano-classic`, `plano-vip` e `plano-camarote`).
- No mobile mantive a ordem Classic, VIP, Camarote, porque a progressão "cada plano inclui o anterior" fica mais fácil de entender de cima para baixo.
- `prefers-reduced-motion` desativa as transições.

### Desafio extra escolhido: B (FAQ com acordeão sem JS)

Seis perguntas em cartões, usando `<details>` e `<summary>` nativos.

- Sem JavaScript, sem `aria-expanded` e sem `role` extra: a semântica nativa do `details` já comunica o estado.
- Sem heading dentro do `summary`, para a hierarquia da página continuar h1, h2 e h3 sem desvios.
- Todos os itens começam fechados e **não usei o atributo `name`**, então vários itens podem ficar abertos ao mesmo tempo.
- O chevron é feito só com CSS e o `summary` tem o marcador nativo escondido nos dois motores (`list-style: none` e `::-webkit-details-marker`).
- O item aberto ganha a borda laranja da marca, e o foco do teclado mantém o contorno duplo.
- As respostas só repetem dados que já estão no email e na LP. **A política de reembolso é a única informação nova e é fictícia.**

## Compatibilidade e limitações

### Email

| Cliente | O que conferir | Status | Observações |
|---|---|---|---|
| Gmail (web) | Layout fluido, botão, inversão automática no modo escuro, corte por tamanho | OK | Sem observações |
| Gmail (app) | Media query mobile, botão em largura total | OK | Sem observações |
| Apple Mail (macOS) | Dark mode por `prefers-color-scheme`, troca de logo | OK | Sem observações |
| Outlook desktop (Windows) | Ghost table de 600 px, botão VML único e centralizado, espaçamentos | OK | Sem observações |
| Outlook.com | Dark mode por `data-ogsb` e `data-ogsc`, troca de logo | OK | Sem observações |

Limitações que já conheço:

- O Outlook desktop ignora `max-width` e `border-radius` em links. Por isso existem a ghost table e o botão VML. O mesmo vale para o `max-width` do parágrafo de público do hero, que serve para evitar uma palavra solta na última linha e pode quebrar de outro jeito lá.
- O botão VML tem largura fixa de 250 px. Se o texto do botão mudar, é preciso ajustar o valor.
- O dark mode não se comporta igual em todos os clientes. O Gmail aplica uma inversão própria que eu não controlo, e outros clientes podem inverter cores por conta própria. As cores escuras que defini valem onde o cliente respeita `prefers-color-scheme` ou os atributos do Outlook.com.
- Media queries podem ser ignoradas em alguns apps. É por isso que o layout base é fluido.
- Os benefícios e os depoimentos empilham em todas as larguras. Foi uma escolha de segurança, e não a melhor composição para desktop.
- Os links dos botões são placeholders, os logos usam caminho relativo e o email não tem versão em texto simples.

### Landing page

- **O que verifiquei:** o comportamento no Chrome. Safari e Firefox **não foram testados**.
- Os **breakpoints** (`48rem`, `56.25rem` e `64rem`) estão escritos como valores literais nas media queries, porque variáveis CSS não funcionam ali. `48rem` (768 px) coloca os três passos lado a lado, `56.25rem` (900 px) é a largura a partir da qual os três cards de plano cabem em linha, e `64rem` (1024 px) coloca o hero em duas colunas.
- As cores dentro dos SVGs inline e do favicon ficam fixas no HTML e não acompanham os tokens.
- A página é só em tema claro (`color-scheme: light`). O dark mode ficou no email.
- Os CTAs levam a `example.com`: não existe página de inscrição.
- O texto é em português e não há estrutura para tradução.

Verificação manual, a preencher depois dos testes:

| Verificação | Status | Observações |
|---|---|---|
| 320 px | OK | |
| 375 px | OK | |
| 768 px | OK | |
| 900 px | OK | |
| 1280 px | OK | |
| Navegação só com teclado | OK | |
| Zoom de 200% | OK | |

## O que eu faria diferente com mais tempo

**Email**

- Fazer também a variação do email com oferta relâmpago, uma das opções do desafio extra. Escolhi o FAQ na LP.

**Landing page**

- Levar a prova social do email para a LP e usar conteúdo real em vez de números ilustrativos.
- Ter uma página de inscrição de verdade, com validação, que seria o único motivo razoável para um pouco de JavaScript.

## Autor

Liniker Brito · [GitHub](https://github.com/linikerbrito) · [LinkedIn](https://www.linkedin.com/in/liniker-brito/)
