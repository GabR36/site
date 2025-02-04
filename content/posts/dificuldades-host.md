+++
title = 'Mais Dificuldades com o Host e a confusão de conceitos em Redes'
date = 2024-02-17T21:59:01-03:00
description = 'Conceitos sobre ip dinâmico/estatico ao nível do provedor e CGNAT x NAT, comparação tipos de host'
tags = ['redes']
draft = false
authors = ['gabriel_chuede']
+++

# IP dinâmico/estático, ao nível do provedor

Acontece que o meu ipv6 também era dinâmico, ou seja o ipv6 estático
configurado pro meu servidor parou de funcionar apos alguns dias,
bastava reiniciar o roteador.

Isso me leva a pensar em como o assunto de redes é confuso: há sempre
essa confusão entre dinâmico e estático, se já é difícil encontrar
explicações sobre configuração de ip estatico dentro da rede, do
sistema operacional dos computadores, imagine ainda que há o nível do
provedor e que este pode oferecer ipv6 e ipv4 estáticos ou dinâmicos
aos seus clientes. Na minha experiencia, muito pouco é falado sobre
esse nível, como saber se meu ipv6 é dinâmico ou fixo/estático só gera
respostas sobre dhcp/slaac quando muito.

O que aprendi: há o nível do provedor que pode oferecer ipv4 e ipv6
dinâmicos ou fixos, você pode notar isso percebendo a mudança do
prefixo ipv6 e ip da wan no site do roteador antes e depois de
reiniciar o roteador. Isso está sob o controle total do provedor e
você tem que contatá-lo para pedir por ips fixos, podendo eles cobrar
taxas sobre essa medida. Ainda não pedi nenhum plano de ip fixo mas
acredito que nesse caso será informado o prefixo e você poderá setar
um ip estático com esse prefixo assim como o dhcp/slaac fornecerá ips
com esse prefixo e ao reiniciar o roteador o prefixo permanecerá.

Agora, o problema é que a ultima vez que contatei o meu provedor
pedindo um ipv6 fixo, o mesmo me disse que cobraria 50 reais por esse
serviço, o mesmo por ipv4 fixo. Eu tentarei novamente assim apos o fim
de semana pois acredito que possa ter havido alguma confusão. Cobrar
por ipv6 fixo é o cumulo, o ipv6 foi desenvolvido para não ter o mesmo
problema que o ipv4: menos ips do que usuários, então tem ipv6 de
sobra para todo mundo, nao tem por que cobrar em cima disso.

# Comparações entre tipos de hosts

Se as novas tentativas não funcionarem minhas opções serão DDNS e
continuar com o raspberry pi, hostear o site no github pages ou
comprar um vps. Não pagarei 50 reais por um ipv6 até por que o vps é
mais barato (cerca de 30 reais) e me da acesso ao ipv6 e ipv4 publico
fixo e ainda me da as vantagens de não pagar luz, não interferir na
minha internet, diminuir riscos de segurança por não estar na minha
rede de casa e facilitar o futuro host de serviço de e-mail (pelo que
dizem é difícil hostear e-mail no homeserver).

### DDNS
Não sei ainda como funciona o DDNS para serviços como websites mas as
vantagens parecem ser:
- upgrade hardware (já que vai continuar no homeserver)
- grátis
- controle total
- possibilita mais serviços que so o site

### Github Pages
- gratis
- facil de usar
- dominio facil de ler
- facilita contribuições ao site
- mais profissional que o ddns

### VPS
- serviço de e-mail futuro é uma opção
- segurança (rede fora da minha localização)
- mais controle que o github pages
- não tem custos de energia elétrica
- não interfere na minha internet
- mais fácil de usar que o DDNS
- domínio é fácil de ler
- possibilita mais serviços que só o site
- mais profissional que o ddns e github pages

Decidi tentar usar o DDNS pois parece ser a opção mais adequada para mim:
ele é grátis, vai me fazer dar um uso para o raspberry pi, o gasto não
vai ser inútil, vai me permitir aprender mais pois é tudo feito do
zero e eu posso fazer tudo com meu servidor, ao contrario do Pages que
só me possibilitaria fazer o site, ainda poderei testar os limites da
minha internet e ver se isso realmente é um problema, se for, basta
mudar para o VPS que é a segunda melhor opção, vamos ver se esse
caminho não vai me trazer alguma surpresa.

# Mais problemas, CGNAT e NAT

Após muita tentativas de fazer o servidor funcionar com o duckdns
descubro mais uma coisa muito importante sobre redes: CGNAT é
diferente de NAT e é o motivo dos meus problemas. Acontece que minha
rede está sobre o tal CGNAT, eu não liguei muito para isso achando que
era a mesma coisa que o NAT e que não deveria ter importância mas
estava enganado. O CGNAT é um nível a mais de NAT, se no NAT um ip era
compartilhado com a rede local inteira, no CGNAT um ip é compartilhado
com várias redes locais, e o problema disso: o seu roteador é inútil,
você precisa redirecionar as portas externas para as internas do seu
servidor mas as requisições passam sempre pelo roteador com ip
publico, que com cgnat nao é o roteador na sua casa e sim o roteador
do provedor.

Após tentar outro provedor que tenho em casa e nem conseguir acessar
as configurações do roteador devendo este estar bloqueado, desisti do
homelab e comprei o VPS mais barato que achei, sem problemas até
agora.
