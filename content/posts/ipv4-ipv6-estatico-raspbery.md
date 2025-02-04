+++
title = 'Ipv4 e Ipv6 Estático no Raspberry Pi e Linux'
date = 2024-02-11T22:55:38-03:00
description = 'guia para configuração de ipv6 e ipv4 estatico no raspberry pi'
tags = [ 'linux', 'redes', 'guia' ]
authors = ['gabriel_chuede']
+++

Como primeiro post do site, nada mais justo que falar sobre as
dificuldades que tive para hosteá-lo, e como consegui fazer tudo
funcionar, de modo que ajude quem seguir por esse caminho também.

Minhas maiores dificuldades foram a respeito de configurar um ipv6
estático no meu servidor, no caso, um raspberry pi 3b. Na internet
parece ter pouca coisa relacionada a ipv6 e, pra ser honesto, não acho
que entendi profundamente o que fiz, mas como funcionou e você pode
atestar isso, isso não importa muito e os passos devem ser os mesmos
para você. 

Vou deixar os passos mais concretos separados da explicação (embaixo
dos passos), desse modo quem entende mais do assunto pode ir direto ao
ponto. Se você não entende muito, ou não consegue passar de alguma
parte, recomendo ler as explicações.

## 1- Coletar ipv4 e ipv6 alocados dinamicamente a seu raspberry pi
você pode fazer isso com os comandos:

`ip -4 addr show eth0`

mudando eth0 para o nome da sua interface de rede (eth0, wlpls0, eno0,
etc.)
seu ipv4 vai estar ao lado de 'inet'

para o ipv6:

`ip -6 addr show eth0`

o ipv6 vai estar ao lado de 'inet6', pode escolher um, desde que nao
comece com fe80.

## 2- Coletar ipv4 e ipv6 do gateway padrão

`ip -4 route show eth0`

`ip -6 route show eth0`

pegue os ips após 'default via'.

## 3- ipv4 e ipv6 dos servidores DNS

para isso você tem de acessar a página do seu roteador e colocar a
senha (geralmente 'admin'), no meu roteador essas informações estão na
página de informações gerais. São dois ipv4 e dois ipv6.

você pode acessar a página do roteador digitando o ipv4 do gateway
padrão que obteve no passo anterior.

## 4- Mudar o arquivo de configuração

`sudo nano /etc/network/interfaces.d/eth0`

coloque o seguinte:
```
allow-hotplug eth0
iface eth0 inet static
    address 192.168.3.200
    netmask 255.255.255.0
    gateway 192.168.3.1
    dns-nameservers 187.85.152.10 187.85.152.11

iface eth0 inet6 static
    address 2804:3b0:9206:6913:6f16:cc5f:9c20:bec0
    netmask 64
    gateway fe80::e2d4:62ff:feb3:6a82
    dns-nameservers 2804:3b0:8100:0:187:85:152:10 2804:3b0:8100:0:187:85:152:11
```
Mudando os ipv4 e ipv6 com os ips que conseguiu nos passos anteriores,
os nomes devem ser auto-explicativos. Salve e feche o arquivo.

você pode alterar os ipv4 e ipv6 mas para isso tem de observar sua
mascara de rede/subnet mask, o que geralmente funciona é mudar o
último ponto do ipv4 para um número alto, como 200, e o último campo
separado por : com qualquer combinação de 4 digitos com os algarismos
de 0 até 9 e letras de a até f. Eu usei o :bec0.

## 5- desativar o dhcpcd

desative o dhcpcd:

`sudo systemctl stop dhcpcd.service`

`sudo systemctl disable dhcpcd.service`

Reinicie a máquina:

`sudo reboot`

# 6- final

Seu ipv6 e ipv4 estatico devem estar funcionando, faça os testes:

`ip -4 addr show eth0`

`ip -6 addr show eth0`

não devem conter a palavra 'dynamic' e devem estar mostrando os seus
ips estáticos que foram configurados. com valid_lft forever e
preferred_lft forever.

`ping4 google.com`

`ping6 google.com`

para verificar se os servidores dns estão funcionando corretamente.

## Explicações

Primeiramente deve-se entender que as redes com planos normais
funcionam com CG-NAT, ou seja, um ipv4 público é usado por todos os
computadores da rede ou algo do tipo. Por isso quando você acessar o
seu ipv4 ele vai ser algo como 192.168.3.2 e não 103.240.230.40, este
é um ip privado e não pode ser usado no servidor. Para conseguir um
ipv4 público você deve contatar seu provedor de internet e contratar
um plano, que vai lhe custar algo em torno de 50 reais.

Mas você tem acesso ao ipv6 que não usa cg-nat (apesar de ter uma
funcionalidade parecida, podendo ser notado em ipv6 com inicio fe80),
e o ip que você consegue ter acesso é público, mas o problema é que os
roteadores podem não ter a funcionalidade de configurar ipv6 estático,
como foi o meu caso, apesar de suportarem ipv6.

Para isso, você terá de configurá-lo no seu servidor, o problema ai
reside no fato de que, pelo menos no raspberry pi, tem varias formas
de se fazer isso, como dhcpcd e networking, e de onde encontrar as informações que você precisa,
como servidores dns, gateway e ips. O seu roteador diz uma coisa, os
comandos no servidor dizem outra e por ai vai.

a minha solução, após várias tentativas, foi usar os ips dos
servidores dns indicados no roteador e os ipv4 e ipv6 do gateway do
comando ip route, não os indicados no roteador.

quanto a configuraçao, esta será feita pelo /etc/network e não pelo
dhcpcd.conf que não parece ter todos os dados para o ipv6, e então
desativar o dhcpcd. cuidado para não desativar o networking.service
também se não não poderá usar o ssh.

Acho que seria isso. Aproveite seu servidor sem gastos inúteis como
ipv4 público.

## Veja Também

- [Mais problemas no Host](/posts/dificuldades-host)