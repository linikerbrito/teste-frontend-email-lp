# Quadrante Growth Lab | Mesa 4X

## Como abrir e testar localmente
Eu abro `email/index.html` ou `lp/index.html` diretamente no navegador. As duas peças são arquivos estáticos e não exigem instalação, servidor, build ou dependências externas.

Mantive os logos do email em `email/assets/`. Usei `example.com` como destino placeholder para inscrição, descadastro e preferências.

Na LP, abro `lp/index.html` diretamente. Conferi no Chromium integrado ao VS Code em 320, 375, 768 e 1280 px. Testei navegação por Tab, foco visível, Enter e Espaço na FAQ, além da ausência de overflow horizontal nesses tamanhos.

## Decisões técnicas
No email, usei tabelas de apresentação com estilos essenciais inline e um contêiner fluido com limite de 600 px. Acrescentei uma ghost table condicional para fixar essa largura no Outlook desktop, configuração MSO de 96 PPI e botões bulletproof com VML, acompanhados de links HTML para os demais clientes. O preheader fica oculto no corpo e usa caracteres de preenchimento para evitar que texto seguinte apareça no preview.

Mantive CSS em um bloco pequeno para reset, responsividade e dark mode. As regras usam `prefers-color-scheme` para clientes compatíveis e `data-ogsc` e `data-ogsb` para Outlook.com. Criei duas versões PNG locais do wordmark para fundos claros e escuros.

Na LP, usei landmarks, títulos hierárquicos, link de salto, SVGs inline e classes BEM. Centralizei cores e medidas em tokens CSS. A FAQ usa `<details>` e `<summary>` sem JavaScript, com animação progressiva de `::details-content` onde há suporte.

Organizei os ingressos em cartões Classic, VIP e Camarote, mantendo o VIP identificado por selo e borda. Na FAQ, usei cartões com várias respostas abertas de forma independente. O texto laranja aberto usa `#C2410C`, com contraste calculado de 5,18:1 sobre branco; reservei o laranja da marca para bordas e detalhes.

## Compatibilidade e limitações
Não testei o email nos clientes reais abaixo. A matriz registra as técnicas implementadas, não uma confirmação de renderização.

| Cliente | Abordagem implementada | Teste real |
| --- | --- | --- |
| Gmail web | Tabelas, estilos inline, layout fluido e fallback HTML dos botões | Não testado |
| Gmail app | Layout fluido, media query mobile e fallback HTML | Não testado |
| Apple Mail | Media queries de mobile e `prefers-color-scheme` | Não testado |
| Outlook desktop | Ghost table, VML nos CTAs, configuração de 96 PPI | Não testado |
| Outlook.com | Regras de dark mode com `data-ogsc` e `data-ogsb` | Não testado |

Ainda não testei o email em cliente real. Considero como limitações conhecidas que o Outlook desktop pode ignorar `border-radius` e outras propriedades CSS modernas; por isso implementei VML nos CTAs. A inversão automática de cores no Gmail e em clientes Outlook pode variar. Usei endereço, CNPJ, depoimentos, métricas e preços fictícios, que devem ser revisados antes de qualquer envio ou publicação.

Verifiquei a LP apenas no Chromium integrado ao VS Code, que reportou suporte a `::details-content` e `interpolate-size`. Ainda não testei Firefox, Safari, Edge nem leitores de tela. A altura da FAQ anima quando esses recursos estão disponíveis; sem eles, o conteúdo abre sem animação de altura e continua funcional. Os preços, métricas, evento e destinos `example.com` são fictícios.

## O que eu faria diferente com mais tempo
Eu enviaria o email de teste para contas reais em Gmail web, Gmail app, Apple Mail, Outlook desktop e Outlook.com. Compararia light e dark mode, imagens bloqueadas, texto ampliado e os botões VML e HTML. Também testaria a LP em Firefox, Safari e Edge, validaria zoom de 200% e faria uma rodada completa com NVDA e VoiceOver.
