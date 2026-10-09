# Landing page — Nacional · Revenda XCMG Manaus

**Já configurado com o catálogo:** 29 modelos em 7 linhas, logos Nacional e XCMG, WhatsApp/telefone (92) 99474-7926, e-mail adm.nacional@zucatellibrasil.com.br, Instagram @nacional.maquinas, endereço Av. Torquato Tapajós, nº 03 (Manaus/AM).

**Ainda a confirmar no `CONFIG`:** razão social, CNPJ, horário de atendimento, webhook do n8n e ID do GTM.

Site estático (HTML + CSS + JS em um único arquivo). Publica em qualquer hospedagem: cPanel, Locaweb, HostGator, Netlify, Vercel.

## 1. Antes de publicar (obrigatório)

Abra `index.html`, procure o bloco `const CONFIG` e preencha **somente dados confirmados**:

| Campo | Exemplo | Efeito |
|---|---|---|
| `empresa.nome` | `Martins Máquinas` | Logotipo, títulos, mensagens |
| `empresa.razaoSocial`, `cnpj` | — | Rodapé e Política de Privacidade |
| `contato.whatsapp` | `5579999999999` | Todos os botões de WhatsApp |
| `contato.telefone`, `email`, `endereco`, `horario`, `mapaUrl` | — | Seção Contato (campo vazio não aparece) |
| `contato.dpoEmail` | `privacidade@...` | Pedidos LGPD |
| `integracao.webhookUrl` | URL do n8n | Destino do formulário |
| `integracao.gtmId` | `GTM-XXXXXXX` | Google Tag Manager |
| `diferenciais[].ativo` | `true/false` | Só exibe o que a empresa oferece de fato |

Também troque `SEU-DOMINIO.com.br` em `index.html` (canonical), `robots.txt` e `sitemap.xml`.

A faixa amarela "Modo de configuração" no topo some quando nome, WhatsApp e webhook estiverem preenchidos.

## 2. Catálogo de equipamentos

Cadastre no array `EQUIPAMENTOS` (nada é inventado; vazio = mensagem "consulte disponibilidade"):

```js
{ id:"esc-01", categoria:"escavadeiras", marca:"XCMG", modelo:"MODELO REAL",
  ano:2025, condicao:"novo", disponibilidade:"Pronta entrega",
  preco:null, foto:"img/esc-01.webp", destaque:true,
  specs:[["Peso operacional","valor do fabricante"],["Potência","valor"]] }
```

Categorias válidas: `escavadeiras`, `pas`, `motoniveladoras`, `rolos`, `retros`, `pavimentacao`, `empilhadeiras`.
Campos extras usados: `br:true` (selo Fabricado no Brasil), `tipo` (subtítulo), `extra` (lista em "Mais especificações").
`preco:null` exibe "Consulte condições comerciais".

## 3. Formulário (como o lead chega)

Ordem de prioridade:
1. **Webhook configurado** → `POST` JSON para o n8n/Make/CRM e mensagem de sucesso só se a resposta for 2xx.
2. **Só WhatsApp configurado** → monta a mensagem com todos os dados e o cliente confirma o envio no WhatsApp.
3. **Nada configurado** → avisa que não há destino. Nenhum envio é simulado.

Payload enviado ao webhook:

```json
{ "nome":"", "empresa":"", "telefone":"", "email":"", "cidade":"", "uf":"",
  "segmento":"", "categoria":"escavadeiras", "categoria_nome":"Escavadeiras hidráulicas",
  "modelo":"", "quantidade":"1", "prazo":"", "observacoes":"",
  "consentimento":true, "consentimento_em":"ISO", "enviado_em":"ISO",
  "origem":{ "pagina":"/", "referrer":"", "cta":"categoria_escavadeiras",
             "utm_source":"google", "utm_medium":"cpc", "utm_campaign":"", "gclid":"" } }
```

Fluxo sugerido no n8n: **Webhook → validação → planilha/CRM → e-mail ao comercial → WhatsApp ao vendedor → resposta 200**.
Libere CORS no webhook para o domínio do site.

Antispam: campo isca oculto + bloqueio de envio em menos de 3 segundos. Se houver volume de spam, adicione Cloudflare Turnstile no webhook.

## 4. Eventos de rastreamento (dataLayer → GTM/GA4)

`whatsapp_click`, `cta_orcamento_click`, `cta_click`, `catalogo_categoria`, `catalogo_filtro`,
`lead_form_start`, `lead_form_invalid`, `lead_form_submit`, `lead_form_success`, `lead_form_error`,
`lead_whatsapp_handoff`, `privacy_open`.

Conversão recomendada no Google Ads/Meta: `lead_form_success` e `lead_whatsapp_handoff`.

## Ficha técnica e comparador

- Clique na foto ou em **Ficha técnica**: abre a ficha completa do modelo (todas as especificações do catálogo, componentes, aplicações), com navegação Anterior/Próximo.
- **Enviar esta ficha pelo WhatsApp**: manda a ficha em texto com o link direto do modelo (ex.: `seusite/#xe225br` abre a ficha automaticamente).
- **Comparar**: marque até 3 modelos e toque em **Comparar** na barra inferior para ver lado a lado.
Eventos: `ficha_abrir`, `comparar_toggle`, `comparar_abrir`, `cta_click` (ficha_whatsapp_*, ficha_compartilhar_*, comparar_whatsapp).

## 5. Catálogo em PDF

Arquivo: `catalogo-xcmg-nacional.pdf` (na raiz do site). Para atualizar, substitua o arquivo mantendo o mesmo nome.
Botões: cabeçalho, banner principal, seção Catálogo e menu do celular.
- **Abrir**: abre o PDF em nova aba.
- **Baixar**: baixa como `Catalogo-XCMG-Nacional.pdf`.
- **Enviar pelo WhatsApp**: no celular (HTTPS), abre o compartilhamento do sistema com o próprio PDF anexado; no computador, abre o WhatsApp com o link do catálogo.
Eventos: `catalogo_modal`, `catalogo_abrir`, `catalogo_baixar`, `catalogo_whatsapp`, `catalogo_compartilhado_arquivo`.

## 6. Fotos

Padrão das fotos de modelos: PNG/WebP com fundo transparente, tela 960×720 px, máquina centralizada e apoiada na linha de chão (y = 680 px), ocupando até 860×610 px. Fotos novas no mesmo padrão ficam alinhadas com as demais nos cards.

Coloque em `img/` e informe o caminho em `foto`, `logoUrl` e `fotoInstitucional`.
Use fotos próprias ou cedidas pelo fabricante com autorização de uso. Enquanto não houver foto, o site mostra ilustrações técnicas originais.
