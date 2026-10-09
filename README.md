# Quadrante Growth Lab | Mesa 4X

## Como abrir e testar localmente

O projeto é composto por duas peças estáticas, sem necessidade de instalação de dependências, servidor local ou processo de build.

- **Email:** abra `email/index.html` diretamente no navegador.
- **Landing Page:** abra `lp/index.html` diretamente no navegador.

Os logotipos do email estão armazenados localmente em `email/assets/`. Os links de inscrição, descadastro e preferências de comunicação utilizam `example.com` como destino demonstrativo.

## Decisões técnicas

### Email HTML

A estrutura utiliza tabelas de apresentação, com `role="presentation"` onde apropriado, CSS inline para os estilos essenciais e um contêiner fluido com largura máxima de 600 px.

Para compatibilidade com o Outlook desktop, implementei uma ghost table condicional para preservar a largura do contêiner, configuração MSO de 96 PPI e botões bulletproof com VML, acompanhados de links HTML como alternativa para os demais clientes.

O preheader permanece visualmente oculto no corpo do email, com caracteres de preenchimento para reduzir a exibição de conteúdo indesejado na prévia da mensagem.

Mantive um bloco CSS compacto para resets, responsividade e dark mode. As regras utilizam `prefers-color-scheme` nos clientes compatíveis e seletores `data-ogsc` e `data-ogsb` para ajustes no Outlook.com.

Também preparei duas versões PNG locais do wordmark, destinadas a fundos claros e escuros, para preservar a legibilidade da identidade visual em diferentes condições de exibição.

### Landing Page

A página utiliza HTML semântico, com landmarks, hierarquia de títulos, link de salto para o conteúdo principal e SVGs inline para elementos visuais.

Organizei os estilos com a convenção BEM e centralizei cores, espaçamentos e outras medidas em tokens CSS, facilitando a manutenção e a consistência visual.

A interface é responsiva e utiliza a FAQ nativa com `<details>` e `<summary>`, sem JavaScript. A animação de abertura e fechamento utiliza aprimoramentos progressivos com `::details-content` e recursos modernos de dimensionamento, mantendo o comportamento funcional sem depender da animação.

Os planos Classic, VIP e Camarote são apresentados em cartões, com destaque visual para a opção recomendada por meio de selo e borda.

Na FAQ, cada item pode ser expandido independentemente dos demais. Para os textos em laranja sobre fundo branco, utilizei `#C2410C`, com contraste calculado de 5,18:1. O laranja da marca também aparece em bordas e detalhes visuais.

## Compatibilidade e limitações

### Email

A implementação considera as particularidades dos principais ambientes de leitura de email:

| Cliente | Recursos considerados |
|---|---|
| Gmail web | Tabelas, estilos inline, layout fluido e links HTML nos CTAs |
| Gmail app | Layout fluido, media queries para dispositivos móveis e links HTML |
| Apple Mail | Media queries para dispositivos móveis e `prefers-color-scheme` |
| Outlook desktop | Ghost table, VML nos CTAs e configuração MSO de 96 PPI |
| Outlook.com | Ajustes de dark mode com `data-ogsc` e `data-ogsb` |

O suporte a propriedades CSS e o tratamento de cores podem variar entre clientes. No Outlook desktop, por exemplo, propriedades como `border-radius` podem não ser reproduzidas de forma consistente. Por isso, os CTAs contam com uma implementação VML complementar.

O dark mode também pode envolver inversões automáticas de cores, dependendo do cliente e de suas configurações. As regras específicas e as versões alternativas do logotipo ajudam a preservar a legibilidade nesses cenários.

### Landing Page

A página utiliza recursos nativos do HTML e aprimoramentos progressivos de CSS. A animação da FAQ depende do suporte a `::details-content` e `interpolate-size`; em ambientes sem suporte, o conteúdo continua disponível por meio do comportamento nativo de `<details>` e `<summary>`.

Os elementos interativos contam com foco visível e suporte à navegação por teclado. A estrutura semântica, a hierarquia dos títulos e o contraste das cores contribuem para a acessibilidade e a manutenção da interface.

### Conteúdo demonstrativo

O projeto utiliza dados fictícios para fins de apresentação, incluindo endereço, CNPJ, depoimentos, métricas, evento e preços. Os destinos `example.com` também são demonstrativos e devem ser substituídos pelos endereços definitivos antes de uma publicação ou campanha real.

## O que eu faria diferente com mais tempo

- Expandiria a validação de compatibilidade do email em diferentes clientes, dispositivos e configurações de light e dark mode.
- Avaliaria o comportamento do email com imagens bloqueadas, ampliação de texto e diferentes condições de exibição dos CTAs.
- Ampliaria a validação da landing page em diferentes navegadores, níveis de zoom e tecnologias assistivas, incluindo NVDA e VoiceOver.
- Refinaria os detalhes de acessibilidade e responsividade a partir dos resultados dessas avaliações.