# SeuAgente — Design Spec (base: aesthetory.com, versão light)

Fonte autoritativa de design do `index.html`. Base: aesthetory.com (Framer, dark editorial), paleta invertida pra light.

## Tokens

```css
:root {
  --primary: #0A0A0A;              /* fundo do CTA pill */
  --primary-foreground: #FFFFFF;   /* texto do CTA */
  --bg-base: #FFFFFF;
  --bg-deep: #FAFAFA;
  --bg-elevated: rgba(64, 64, 64, 0.06);       /* cards */
  --bg-elevated-hover: rgba(64, 64, 64, 0.10);
  --text-primary: #0A0A0A;
  --text-muted: rgba(10, 10, 10, 0.6);
  --grad-section: linear-gradient(317deg, #F5F5F5 16%, #FFFFFF 91%);
  --grad-section-alt: linear-gradient(129deg, #F5F5F5 16%, #FAFAFA 91%);
  --font-display: 'Merriweather', serif;  /* weight 300 SEMPRE — nada de bold */
  --font-body: 'Inter', sans-serif;       /* 300/400 */
  --radius-md: 12px; --radius-lg: 20px; --radius-full: 9999px;
  /* sem box-shadow em lugar nenhum; sem bordas — profundidade vem de contraste de fundo */
}
```

## Regras de ouro

- Serif light (Merriweather 300) pra tudo que é grande; Inter light pequeno pro resto.
- Contraste vem de tamanho + cor (60% de preto no texto secundário), nunca de peso bold.
- Cards: fundo translúcido 6% (máx 12%), radius 12–22px, sem borda, sem sombra.
- Botão único: pill preto, texto branco Inter 16px 400, seta →, hover scale 1.03.
- H1: clamp(36px, 5.5vw, 56px), line-height 1.05; segunda linha anima letra-a-letra na entrada.
- Container 1200px; seções 64px mobile / 112px desktop.

## Estrutura (ordem fixa)

1. Hero — eyebrow "SeuAgente" + H1 "Atenda mais clientes pela metade do tempo." + sub + CTA
2. Como funciona (#como-funciona) — 3 cards numerados
3. Funcionalidades (#funcionalidades) — pares funcionalidade → resultado + fecho serif grande
4. CTA meio (#cta-meio)
5. Quem somos (#quem-somos) — foto à esquerda (placeholder, trocar por retrato do Igor) + bio
6. FAQ (#faq) — accordion 4 itens, um aberto por vez
7. CTA final (#cta-final) — seção invertida (fundo preto) + footer

## Componentes extras (add-on pós-v1)

- **Hero visual**: mockup de iPhone realista (moldura #111114 radius 56px, botões laterais via pseudo-elements, dynamic island, status bar 9:41 + sinal/wifi/bateria em SVG, sombra suave — única exceção à regra "sem sombra") com chat de IA: header voltar/título/reticências, bolha do usuário preta à direita, do agente cinza #F2F2F4 à esquerda com avatar "S", horários sob as bolhas, mensagem de áudio (pill preto, play branco, waveform, duração), "Aqui está o que criei:" com 3 mini cards inclinados (🎬 roteiro, ✍️ legenda, 📸 stories), ações Refazer + 👍👎, input "Digite sua mensagem..." com botão enviar e home bar. + 4 chips de notificação flutuantes absolutos (💡 ideia, 📊 métricas com mini bar chart, 🔥 tendência, ✅ conteúdo pronto) com animação floaty. Abaixo de 1024px os chips laterais somem e 2 empilham sob o celular.
- Atenção: `.hero` é flex — `.container` precisa de `width: 100%` senão `margin: 0 auto` faz shrink-to-fit.

## Pendências

- [x] Foto do Igor em #quem-somos (igor.jpg)
- [x] CTAs → https://w.app/seuagente (target _blank)
- [ ] Confirmar e-mail do footer (contato@seuagente.com.br é placeholder) e @ do Instagram

## Formulário + CRM (Supabase)

**Projeto Supabase:** `seuagente-crm` (ref `npkoidvkuomiuqkyeadf`, região sa-east-1)
URL: `https://npkoidvkuomiuqkyeadf.supabase.co` · chave publishable no HTML (pode ser pública, é protegida por RLS)

**Tabela `leads`:** nome, instagram, whatsapp, clientes, gargalo, origem, status, notas, created_at, updated_at.

**Obrigatórios** (validados no formulário E na policy de INSERT): nome, instagram, whatsapp, clientes, gargalo. Só `origem` ("Como conheceu a gente?") é opcional.
`status` = `qualificado` | `contato` | `fechado` | `desqualificado` (colunas do kanban).

**Segurança (validada):**
- Visitante (anon) só faz INSERT, e só nas colunas nome/instagram/whatsapp/gargalo/origem — não consegue ler nada nem definir status/notas (GRANT por coluna + policy com CHECK).
- Só e-mails na tabela `crm_admins` leem/movem/apagam leads. Testado: outro usuário logado enxerga 0 linhas.
- Funções SECURITY DEFINER sem EXECUTE pra anon.

**Landing:** seção `#form` ficou no lugar do antigo CTA do meio ("Tenha SeuAgente ainda Hoje"). Todos os CTAs apontam pra `#form`; o único link de WhatsApp que sobrou é o do painel de sucesso, depois do envio. `#cta-final` fecha a página empurrando de volta pro form.

**CRM:** `crm.html` — login por magic link (e-mail), kanban de 4 colunas com drag-and-drop + select (mobile), métricas no topo, modal com anotações, WhatsApp e Instagram clicáveis. `noindex`.

## Rastreio de origem (links de indicação)

- `vercel.json` faz redirect temporário (307): `/ias` → `/?ref=ias` e `/r/:slug` → `/?ref=:slug` (link genérico pra qualquer parceiro novo, sem precisar mexer no código).
- O JS lê `?ref=` (ou `utm_source`), sanitiza pra `[a-z0-9_-]` com até 40 chars, guarda em `sessionStorage` no carregamento e envia na coluna `ref` do lead. Sem link de indicação → `ref` fica null (aparece como "Direto").
- No CRM: etiqueta preta "via ias" no card, linha "Link de origem" no detalhe, e uma barra de filtros por origem acima do board — clicando, as 4 métricas do topo recalculam só pra aquela origem (dá pra ver a taxa de conversão por parceiro).

## Landing de captação: /estrategia

Segunda landing, single-purpose (só formulário), inspirada na estrutura da LP da Smart Sales:
logo centralizado → headline grande → subtítulo → um painel único com o formulário → botão.

- Arquivo `estrategia.html`, servido por rewrite no `vercel.json` (URL limpa, sem .html) em três caminhos: **/formsv1** (versão neutra, uso geral), **/formsias** (link da Iasmyn) e /estrategia (alias antigo).
- O caminho define a indicação: a regex `/forms<slug>` vira `ref=<slug>`, com `v1` tratado como neutro. Ou seja, /formscarol já funciona pra um parceiro novo sem tocar no código, e o link compartilhado fica limpo (sem ?ref= na URL). Um `?ref=` explícito na query ainda tem precedência sobre o caminho.
- Headline: "É preciso de EstratégIA para crescer." — o "IA" leva um sublinhado fino, que faz o trocadilho aparecer sem quebrar a paleta monocromática.
- Mesma identidade da principal: Merriweather 300 nos títulos, Inter no corpo, painel rgba(64,64,64,0.06), CTA pill preto com seta.
- Mesmos 6 campos do formulário principal, com os 5 obrigatórios.
- **Inputs com font-size 16px de propósito**: abaixo disso o Safari do iPhone dá zoom automático ao focar o campo e desloca o layout.
- Alvos de toque: inputs 52px, pílulas 48px, botão 54px.
- Grava na MESMA tabela `leads` (aparece no mesmo CRM), com `pagina = 'estrategia'` pra distinguir de quem veio pela landing principal (`pagina = 'principal'`, valor padrão da coluna).
- O rastreio de indicação (`?ref=`) continua funcionando aqui também, então as duas dimensões coexistem: quem indicou × qual landing converteu.
- No CRM: etiqueta tracejada "LP EstratégIA" no card, linha "Landing" no detalhe e um segundo grupo de filtros ("Landing:") que recalcula as métricas — dá pra comparar a conversão das duas páginas.

## Reestruturação da landing principal (feedback do público)

Nova ordem: Hero → Como funciona na prática → Bônus → Formulário → Quem somos → FAQ → CTA final.

- **Hero**: sem celular e sem chips. O fundo é um **tabuleiro de xadrez isométrico desenhado em SVG inline** (gerado por script: 32 casas escuras + 15 peças com silhuetas próprias — peão, torre, bispo, cavalo, dama, rei). Sem retângulo de fundo: só as casas escuras sobre o gradiente da seção, então não existe borda de imagem aparecendo. Animação de montagem: a borda entra, as casas surgem em onda diagonal (0.055s entre elas) e as peças **pousam** de cima com leve overshoot, uma a uma. Opacidades finais: borda 0.16, casas 0.10, peças 0.32. Um setTimeout marca  aos 4.5s garantindo o estado final caso a animação não rode. Largura 120% no mobile pra o tabuleiro caber inteiro (nada cortado). A foto original em JPG foi REMOVIDA do projeto — era comp com marca d_água e mostrava a borda do retângulo.
- **Hero (copy)**: "Faça roteiros com a voz dos seus clientes." (a segunda linha entra letra a letra) + "Com o Social Media IA, você pode atender mais clientes sem ter que se preocupar com os roteiros."
- **Bloco 2 — Como funciona na prática**: dois passos (1. Diagnóstico profundo do cliente → 2. Desenvolvimento sob medida) + `.demo`, uma recriação em HTML/CSS da conversa do agente no Discord (avatar, badge APP, horário, contexto da pergunta acima). Animação: pontinhos de "digitando" aparecem e somem, depois os parágrafos entram em cascata (1.5s / 2.1s / 2.7s). Recriado em HTML em vez de usar o print: fica nítido em qualquer tela, é responsivo e acessível.
- **Bloco 3 — Bônus**: o segundo agente que traz ideias e sugere formato. Três cards de notícia entrando em sequência, cada um com chips de formato sugerido (o ativo em preto).
- **Bloco 4**: formulário com o título "Pra ter o seu, é só preencher aqui."
- Navbar: o link "Funcionalidades" virou "Bônus" (#bonus).

**Regra importante de robustez:** o estado inicial invisível das animações fica sob a classe `.js-anim`, adicionada ao `<html>` por um script logo no início do body. Sem JS (ou se o IntersectionObserver falhar), todo o conteúdo nasce visível — texto nunca depende de JS pra ser lido. O bloco de `prefers-reduced-motion` também zera as animações novas.
