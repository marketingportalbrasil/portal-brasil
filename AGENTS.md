# AGENTS.md — Newsletters da Portal Brasil

Este repositório hospeda os **assets de e-mail marketing da Portal Brasil** via **GitHub Pages**
(branch `main`, servido a partir da raiz). Cada newsletter é uma página HTML montada com **fatias
de imagem** — um layout de tabela à prova de Outlook. O e-mail é montado aqui, publicado no Pages,
e depois **copiado e colado no Outlook** para o disparo.

> Você (o agente) tem permissão para criar/editar os arquivos de uma newsletter e enviar para o
> `main` (ou abrir um PR para o `main`). Assim que cair no `main`, o GitHub Pages atualiza em ~1 min.

---

## 1. O que já existe (referência de ouro)

- Página no ar: <https://marketingportalbrasil.github.io/portal-brasil/email.html>
- HTML de referência: **`/email.html`** (a primeira newsletter, "Hamidi está no Brasil")
- Fatias dela: **`/images/`** (`01-header.jpg`, `02-hero.jpg`, … `08c-footer-url.jpg`)

**Sempre use `/email.html` como modelo.** Copie a estrutura de tabela dele. Não invente um layout novo.

---

## 2. Onde colocar uma newsletter nova

Cada newsletter nova vai em **sua própria subpasta** (não mexa na newsletter antiga):

```
/NOME-DA-CAMPANHA/
├── email.html
├── images/
│   ├── 01-....jpg
│   ├── 02-....jpg
│   └── ...
└── _fonte.png          (imagem grande original — opcional manter)
```

- `NOME-DA-CAMPANHA` = slug curto em minúsculas, sem espaço/acento (ex.: `news-agosto`, `black-friday`).
- URL final da página: `https://marketingportalbrasil.github.io/portal-brasil/NOME-DA-CAMPANHA/email.html`
- URL de cada imagem: `https://marketingportalbrasil.github.io/portal-brasil/NOME-DA-CAMPANHA/images/ARQUIVO.jpg`

---

## 3. Como fatiar a imagem grande

A pessoa entrega **uma imagem grande pronta** (o design da newsletter inteira, vertical) e diz
**onde cortar** e **para onde cada pedaço deve linkar**.

Regras de fatiamento:

1. **Largura de exibição = 600px.** As imagens são exportadas em **2x** (então a fonte ideal tem
   ~1200px de largura). Se a imagem original não tiver 1200px de largura, **redimensione a largura
   para 1200px** mantendo a proporção antes de cortar.
2. **Corte em faixas horizontais** nos pontos indicados. Cada faixa vira uma `<img>` numa `<tr>`.
3. Para cada `<img>`, o atributo `width="600"` é fixo e `height` = **altura da fatia na fonte ÷ 2**
   (arredondado). Ex.: fatia de 1000px de altura na fonte → `height="500"`.
4. **Linhas de 2 colunas** (ex.: "site | instagram"): corte a faixa verticalmente no meio (ou no X
   indicado) em duas imagens de `width="300"` cada, lado a lado numa tabela interna.
5. Nomeie os arquivos `NN-descricao.jpg` na ordem de cima pra baixo (`01-topo`, `02-hero`, …).
   Use letras para colunas de uma mesma faixa (`06a-site`, `06b-insta`).
6. Salve como **JPG qualidade ~85** (fotos) ou **PNG** (logos/transparência).

Recomendado usar **Pillow** (Python). Esqueleto de referência:

```python
from PIL import Image

img = Image.open("_fonte.png").convert("RGB")
# garante 1200px de largura (2x de 600)
if img.width != 1200:
    img = img.resize((1200, round(img.height * 1200 / img.width)))

# cortes = lista de Y (em px, na imagem de 1200 de largura) onde cada faixa termina
cortes = [0, 112, 1112, ...]   # 0, fim da faixa 1, fim da faixa 2, ...
nomes  = ["01-header", "02-hero", ...]

for i, nome in enumerate(nomes):
    faixa = img.crop((0, cortes[i], 1200, cortes[i+1]))
    faixa.save(f"images/{nome}.jpg", quality=85)
    print(nome, "-> width=600 height=", round(faixa.height / 2))
```

**Se você não souber os pontos de corte:** abra a imagem, proponha as linhas de corte nas quebras
visuais naturais (fim de cada bloco/seção), gere um preview com as linhas desenhadas e **confirme
com a pessoa antes de fatiar de vez.**

---

## 4. Como montar o `email.html`

Copie a estrutura de `/email.html`. Regras fixas (não altere — são o que faz funcionar no Outlook):

- Tabela `role="presentation" width="600"`, `cellpadding=0 cellspacing=0 border=0`,
  `style="border-collapse:collapse;width:600px;max-width:600px;"`.
- Cada faixa numa `<tr><td style="padding:0;font-size:0;line-height:0;mso-line-height-rule:exactly;">`.
- Cada `<img>` com `width` e `height` explícitos + `style="display:block;width:100%;max-width:600px;height:auto;border:0;"`.
- **URLs sempre absolutas** apontando para o GitHub Pages (nunca caminho relativo — o Outlook não
  resolve). Base: `https://marketingportalbrasil.github.io/portal-brasil/NOME-DA-CAMPANHA/images/`.
- Cada fatia clicável embrulhada em `<a href="..." target="_blank">`.
- **Sempre preencha o `alt`** de cada imagem (acessibilidade + fallback quando as imagens não
  carregam). Use `&amp;` no lugar de `&` dentro do alt.

Links padrão da Portal (reaproveite quando fizer sentido):
- Site Portal: `https://somosportal.com`
- Instagram Portal: `https://www.instagram.com/somosportal_/`
- LinkedIn Portal: `https://www.linkedin.com/company/portalbrasil/`

---

## 5. Publicar

1. Faça commit dos arquivos (`NOME-DA-CAMPANHA/email.html` + `NOME-DA-CAMPANHA/images/*`) no `main`
   (ou abra um PR para o `main` e faça merge).
2. Aguarde ~1 min e abra a URL da página pra conferir que carregou tudo (nenhuma imagem quebrada).
3. Informe à pessoa a **URL final** da página.

---

## 6. Envio (passo manual da pessoa — não é com você)

O disparo é feito à mão, não pelo agente:

1. Abrir a URL da página no **Chrome**.
2. `Cmd + A` (selecionar tudo) → `Cmd + C` (copiar).
3. Colar (`Cmd + V`) no corpo de um e-mail novo do **Outlook** e enviar.

---

## 7. O que NÃO fazer

- Não apague nem edite `/email.html` nem `/images/` (é a newsletter Hamidi que já foi ao ar).
- Não use caminhos relativos de imagem no HTML — só URLs absolutas do Pages.
- Não mude a largura de 600px nem a estrutura de tabela do modelo.
- Não mexa nas pastas `hamidi-quiz/` e `hamidi-quiz-v2/` (assets de outra campanha).
