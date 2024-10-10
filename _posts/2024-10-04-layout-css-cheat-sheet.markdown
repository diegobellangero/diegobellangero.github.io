---
layout: post
title:  "Cheat Sheet - Layouts no CSS"
date:   2024-10-10 20:10:00 -0300
categories: cheat sheets
---
Layouts em CSS podem ser confusos, especialmente quando se trata de alinhar objetos em linhas ou colunas, ou corrigir um layout quebrado. Esta colinha serve como um guia rápido para ajudar você a entender e implementar layouts eficientes usando Flexbox e Grid.

## Organizando elementos em uma única direção (linha ou coluna)
Em ambos os casos eu uso o flex. Ele é ótimo para esse tipo de layout

# Elementos em linha (um do lado do outro)
![Image](/assets/images/posts/2024-10-04-layout-css-cheat-sheet-01.png)
{% highlight css %}
.meu-container {
    display: flex;
    flex-direction: row; /* Este é o valor padrão, então pode ser omitido */
}
{% endhighlight %}

# Elementos em coluna (Um embaixo do outro)
![Image](/assets/images/posts/2024-10-04-layout-css-cheat-sheet-02.png)
{% highlight css %}
.meu-container {
    display: flex;
    flex-direction: column;
}
{% endhighlight %}

# Propriedades bacanas:
* `justify-content`:  Alinha os itens horizontalmente;
* `align-items`: Alinha os itens verticalmente;
* `gap`: Define o espaçamento entre os itens flex.
![Image](/assets/images/posts/2024-10-04-layout-css-cheat-sheet-03.png)

# Tamanhos fixos e variáveis
É possível também definir tamanhos para elementos ou deixar que o elemento preencha o espaço restante

![Image](/assets/images/posts/2024-10-04-layout-css-cheat-sheet-04.png)
{% highlight css %}
.objeto-a {
    flex: 0 0 100px; /* Tamanho fixo de 100px */
}

.objeto-b {
    flex: 1; /* Preenche o espaço restante */
}

.objeto-c {
    flex: 0 0 200px; /* Tamanho fixo de 200px */
}
{% endhighlight %}

## Organizando elementos bidimensionalmente (na linha e na coluna)
O grid é perfeito para isso. Através dele é possível organizar layouts mais complexos.

Um grid básico pode ser criado assim:
{% highlight css %}
.container {
	display: grid;
    grid-template-columns: repeat(3, 1fr); /* Cria 3 colunas de tamanho igual */
    grid-template-rows: auto auto; /* Cria 2 linhas com altura automática */
    gap: 5px;
}
{% endhighlight %}
![Image](/assets/images/posts/2024-10-04-layout-css-cheat-sheet-05.png)

É possível controlar a exibição dos objetos ao longo do grid:
{% highlight css %}
.item1 {
    grid-column: 1 / 3; /* Ocupa da coluna 1 até a 3 */
    grid-row: 1 / 2; /* Ocupa a primeira linha */
}

.item2 {
    grid-column: 3; /* Ocupa apenas a terceira coluna */
    grid-row: 1 / 3; /* Ocupa da primeira à terceira linha */
}
{% endhighlight %}
![Image](/assets/images/posts/2024-10-04-layout-css-cheat-sheet-06.png)

Com o grid é possível criar todo o layout em um único lugar e depois só definir o que é o quê:
{% highlight html %}
<style>
.container {
  display: grid;
  grid-template-areas:
    'header header header'
    'menu main aside'
    'footer footer footer';
  grid-template-rows: auto;
  grid-template-columns: 1fr 2fr 1fr;
    gap: 5px;
}

.header {
  grid-area: header;
}
.menu {
  grid-area: menu;
}
.main {
  grid-area: main;
}
.aside {
  grid-area: aside;
}
.footer {
  grid-area: footer;
}

</style>
</head>
<body>

<div class="container">
<div class="header">header</div>
<div class="footer">footer</div>
<div class="menu">menu</div>
<div class="aside">aside</div>
<div class="main">main</div>
</div>
{% endhighlight %}
![Image](/assets/images/posts/2024-10-04-layout-css-cheat-sheet-07.png)