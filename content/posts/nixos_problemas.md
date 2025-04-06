+++
title = 'NixOS é muito bom para ser verdade'
date = 2025-02-19T21:56:00-03:00
description = 'teste'
tags = ['linux']
draft = true
authors = ['gabriel_chuede']
+++

[NixOS](www.nixos.org) é um sistema (e gerenciador de pacotes) que
promete grandes coisas. As características que mais me interessaram
foram a possibilidade de instalar pacotes e configurar pacotes em um
arquivo de texto e poder usar esse arquivo em outras máquinas,
solucionando o problema de esquecer os pacotes instalados e as
configurações e ficar dependente da sua máquina para tudo. Outra
caracterítica interessantíssima é poder instalar várias versões do
mesmo programa, acabando com o dependency hell, e portanto sendo o
ambiente ideal para desenvolver programas e para instalar programas em
geral.

O problema é que essas afirmações não ocorrem na experiência real,
apesar de serem verdadeiras.

Bom, primeiro tenho que esclarecer que essas qualidades não são únicas
do NixOS. Outros programas tem essas mesmas funcionalidades, como por
exemplo o Docker lhe permite isolar dependencias e instalar qualquer
programa. O Ansible permite configurar tudo em um arquivo de
texto.

O que torna o NixOS especial diante dessas ferramentas é que o NixOS
faz tudo isso de uma forma muito eficiente em recursos e em
complexidade. Tudo parece fazer sentido e está integrado no
sistema. Não há duplicação nem isolamento, apenas uma fina parede de
abstração separa os programas e suas dependencias.

Infelizmente esse gerenciador de pacotes genial tem problemas. O que
não significa que seja ruim, pelo contrário. Gosto de pensar que os
problemas do Nixos são mais /a posteriori/ do que /a priori/.

Para começar, um de seus problemas é que ele, apesar de poder instalar
várias versões de programas e dependencias em paralelo, não possui
todas as versões (ou boa parte delas) em seu repositório. E isso se
deve ao fato que é dificil manter versões antigas de programas, uma
vez que nem o desenvolvedor original as mantém, e ninguem quer manter
pacotes inseguros no repositorio[1]. Os usuarios então usam os chamados
Flakes para obter versões do repositorio original do nixos em diversos
pontos no tempo e assim conseguir as versões requeridas. Mas isso, em
minha opinião, é remediar um mal mais profundo, que não tardará a
retornar. Mas, se você percerber, isso é um problema com todas as
alternativas. O Docker, Flatpak, e outras soluções de deploy também
ficam dependentes de versões desatualizadas que os autores das imagens
não podem realmente manter.

Outro problema encontrado desse gerenciador de pacotes é que, embora
ele se auto entitule como um "gerenciador de pacotes que pode ser
instalado em qualquer distro", muitos pacotes não funcionarão
corretamente fora do próprio NixOS. Isso se deve ao fato de que
modulos não são acessíveis no nix em distros diferentes. {Melhorar
essa parte}

Portanto, o Nix é ainda muito interessante e acredito sim que pode ser
"o futuro" da instalação de programas e gerenciamento de dependencias,
mas talvez devessemos usar outras abordagens que não envolvam
especificar versões, como o próprio Arch Linux, ou então poucas
versões suportadas e mantidas pelo próprio desenvolvedor. E então só
poderia usar dependencias no repositorio. E isso é útil no NixOS pois
você não precisa ter uma versão apenas, como no Debian ou Arch.

## Referências

[1] Isn’t the point of Nix not to need stable channels? Disponível em: <https://discourse.nixos.org/t/isnt-the-point-of-nix-not-to-need-stable-channels/57385/9>. Acesso em: 15 dez. 2024. 
