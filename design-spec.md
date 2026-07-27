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

**Tabela `leads`:** nome, instagram, whatsapp, gargalo, origem, status, notas, created_at, updated_at.
`status` = `qualificado` | `contato` | `fechado` | `desqualificado` (colunas do kanban).

**Segurança (validada):**
- Visitante (anon) só faz INSERT, e só nas colunas nome/instagram/whatsapp/gargalo/origem — não consegue ler nada nem definir status/notas (GRANT por coluna + policy com CHECK).
- Só e-mails na tabela `crm_admins` leem/movem/apagam leads. Testado: outro usuário logado enxerga 0 linhas.
- Funções SECURITY DEFINER sem EXECUTE pra anon.

**Landing:** seção `#form` ficou no lugar do antigo CTA do meio ("Tenha SeuAgente ainda Hoje"). Todos os CTAs apontam pra `#form`; o único link de WhatsApp que sobrou é o do painel de sucesso, depois do envio. `#cta-final` fecha a página empurrando de volta pro form.

**CRM:** `crm.html` — login por magic link (e-mail), kanban de 4 colunas com drag-and-drop + select (mobile), métricas no topo, modal com anotações, WhatsApp e Instagram clicáveis. `noindex`.
