---
layout: post
title:  "Proxy reverso usando Nginx"
date:   2024-10-04 08:06:00 -0300
categories: ambiente
---
Uma das coisas que eu mais acabo apanhando na hora de começar um projeto é configurar minha estação de trabalho. A gente sai desenvolvendo coisas e um detalhe que você mexe de um lado quebra todo o resto (pelo menos comigo é assim).

Recentemente eu fiz uma imagem de uma VM que me deixou bem feliz. E, para conseguir fazer meus códigos, configurei um proxy reverso. Depois de muitas idas, vindas, backups feitos e restaurados, montei essa stack aqui e estou bem satisfeito com ela, dadas as limitações que temos:

* VM - VirtualBox 7;
* Sistema Operacional - Ubuntu 24;
* Framework do frontend - Angular 18;
* Linguagem do backend - PHP;
* Documentação - Git e Jekyll.

Com essa pilha eu gostaria de, na minha maquina de desenvolvimento, ter a capacidade de acessar cada um desses itens separados. O PHP nós desenvolvemos e hospedamos diretamente no FTP, até para testarmos algumas funcionalidades de Cross-Origin que usamos. Do nosso ambiente de Dev consumimos o Angular e o Jekyll, nas suas respectivas portas.

No começo, tentei fazer pelo Apache mas sempre esbarrava em algum problema. As vezes desligava a máquina no final do dia e, no dia seguinte não funcionava. Quando consegui montar uma configuração estável, tive problemas com diretório base e tipo MIME.

Nesse momento, decidi ir para uma abordagem mais simples: desinstalar o Apache e instalar o Nginx, que tem DNA de proxy. Eu segui os passos abaixo. Parti de uma instalação zerada e vanilla do Ubuntu, só com o SSH configurado.

#### Desinstalei o Apache
{% highlight bash %}
   sudo systemctl stop apache2
   sudo apt-get purge apache2 apache2-utils apache2-bin apache2.2-common
   sudo apt-get autoremove
{% endhighlight %}

#### Instalei o Nginx
{% highlight bash %}
   sudo apt update
   sudo apt-get install nginx
   sudo systemctl enable nginx
{% endhighlight %}

Depois de instalar, testei pra ver se estava funcionando:
{% highlight bash %}
   curl localhost
{% endhighlight %}

#### Configurando o Port Fowarding
Configurei três port fowardings:
* 8080 - para a porta 80 da VM;
* 8181 - para a porta 81 da VM;
* 8282 - para a porta 82 da VM.

[Aqui][Tutorial-Port-Fowarding-VirtualBox] tem um tutorial que explica como fazer, mas é bem facinho e intuitivo.

#### Configurando o Proxy no Nginx
Interamente, as portes que eu fiz fowarding no VirtualBox deveriam se comportar assim:
* 80 - Estático;
* 81 - Porta 4200 (Angular);
* 82 - Porta 4000 (Jekyll).

E para fazer esse proxy, criei um arquivo chamado reverse-proxy.conf:
{% highlight bash %}
   sudo nano /etc/nginx/sites-available/reverse-proxy.conf
{% endhighlight %}

O arquivo ficou mais ou menos assim:
{% highlight bash %}
# Configuração para o estático
server {
    listen 80;
    listen 80 default_server;
    listen [::]:80 default_server;

    root /var/www/site;

    location / {
        try_files $uri $uri/ =404;
    }
}

# Configuração para a aplicação Angular
server {
    listen 81;
    server_name angular.exemplo.com;

    location / {
        proxy_pass http://localhost:4200;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }
}

# Configuração para a aplicação Jekyll
server {
    listen 82;
    server_name jekyll.exemplo.com;

    location / {
        proxy_pass http://localhost:4000;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
{% endhighlight %}

Com isso eu tinha, através da minha estação de desenvolvimento acesso a todos os conteúdos que eu precisava.

Conhece uma maneira melhor ou mais simples de fazer? Manda pra gente!

[Tutorial-Port-Fowarding-VirtualBox]: https://www.simplified.guide/virtualbox/port-forwarding