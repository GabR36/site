+++
title = 'Nixos, a Essência'
date = 2025-05-02T01:33:21-03:00
description = 'Quais as vantagens do NixOS sem usar chavões técnicos'
tags = ['linux']
draft = false
authors = ['gabriel_chuede']
+++

[NixOS](https://www.nixos.org) é um sistema (e gerenciador de pacotes) que
promete grandes coisas. Nesse artigo vou tentar expor a essência do
NixOS, pela minha pequena pesquisa e tentativa de fazer um pacote nix,
e porque acho que quase todas tentativas de explicar o que é o sistema
e o que ele tem de diferente, na internet, são muito ruins. Dentre os
motivos do NixOS ser tão mal explicado está o uso de palavras técnicas
que não dizem nada como 'imutabilidade', 'declarativo',
'reproduzível', e também por não explicar o jeito real que ele
funciona. Também irei explicar os seus problemas e porque talvez você
queira usar o Arch Linux ao invés dele.

## Vantagens

Primeiramente gostaria de explicar o que ele tem de interessante.

O NixOS te permite não perder tempo mais tentando lembrar quais
programas você tinha no seu computador e como estavam configurados, ao
trocar de computador. Isso é possível devido ao fato de que no NixOS
toda a instalação e configuração é feita num arquivo de texto que pode
ser facilmente transferível para outros computadores. E ainda diminui
muito o tamanho do backup, ao invés de copiar todos os programas e
configurações, você só copia um arquivo de texto de alguns kilobytes
(você ainda precisa fazer backup dos seus arquivos /home, imagens,
documentos, etc.).

Mas você pode estar se perguntando qual a diferença entre ele e o tal
do ansible, que permite deixar toda a configuração declarada
também. Pois bem, a diferença é que o NixOS é mais simples de usar,
vai garantir que tudo esteja como declarado (devido a tecnologia
especial de hashing, que não vem ao caso), e vai ter outras vantagens
como explicado a seguir.

Outra vantagem é poder instalar diferentes versões de programas em
paralelo, por exemplo duas versões do emacs, 29 e 28, o que é
especialmente útil para desenvolvedores e para conseguir, no final das
contas, instalar qualquer tipo de programa. Nunca mais fique
impossibilitado de instalar um programa porque as dependencias dele
são mais antigas que as disponíveis no repositório da sua distro. Isso
é útil para desenvolvedores que precisam desenvolver programas que
usam diferentes versões de dependencias.

Mas se você pensar bem vai notar que, novamente, já existem outras
alternativas no 
mercado para resolver esse problema, como o docker, flatpak, snap,
appimage. A diferença está no fato de que o NixOS usa muito menos
recursos do que uma abordagem de conteineres, pois não terá duplicação
de dependências e nem camada de sistema, rodando tudo como se fosse
nativo. Ele também é mais fácil de usar, pois você usa a mesma
interface para instalar programas, configurar, etc. tudo no mesmo
lugar.

E muitas outras *features* como voltar a versão anterior do sistema,
permitir usuarios comuns instalar programas, ter profiles de programas
e configs, etc. mas as principais são essas.

## Funcionamento

Agora que você já sabe porque ele é tão bom, quero explicar como ele
faz para ter essas funcionalidades, que também é muito mal explicado
pelos entusiastas e pela própria documentação oficial.

Acho que a grande tecnologia que possibilitou essas qualidades
incríveis é o hash de diretórios de arquivos, parecido com o que faz o
git. O NixOS usa isso para ter versões diferentes para cada
modificação, seja por versão, flags de compilação, etc. de um mesmo
programa[6]. Então os pacotes nix são descrições de como obter o código
fonte, como compilar, quais programas são dependências de compilação,
quais são dependências de run-time. E fazer um hash do resultado disso
(que será um diretório com binários, includes, libs, etc.). Desse modo
é possível referênciar uma versão específica mesmo e garantir que não
vai mudar (em distros normais, um programa como emacs pode significar
várias coisas, como emacs 29, emacs 28, emacs compilado para o debian,
para o arch, etc).

Porém programas também tem partes que não são iguais, como arquivos de
banco de dados, configurações, etc. o NixOS considera cada diferença
na configuração uma versão nova? Evidente que não. É por isso que os
pacotes são divididos em duas partes: os pacotes nix, que são os
binários, includes e outras partes que nunca mudam, a não ser em
diferentes versões e compilações (por isso são 'imutáveis'), e os nix
modules, que são as partes que mudam, como configs, bancos de dados,
etc. Os nix modules, não fazem parte dos pacotes propriamente ditos,
não estão disponíveis no gerenciador de pacotes nix em outras distros
(por isso você não consegue declarar configurações nesse tipo de
instalação, e portanto quem diz que o gerenciador de pacotes e a
distribuição são iguais está redondamente enganado), e são gerenciados
pelo systemd[3]. Você vai ter que escrever ou usar
um nix module se quiser que seu programa seja declarativo nas
configurações e rode serviços no sistema[4].

Outro ponto importante no funcionamento do Nix é que ele usa links
simbólicos para que você consiga escolher quais programas usar,
importante quando diferentes versões do mesmo programa estão em
conflito (ambas usam o mesmo nome de binário)[7][8][9].

E por último gostaria de esclarecer o por que de o NixOS ter canais
estáveis e instáveis, sendo que ele pode instalar diferentes versões
ao mesmo tempo e se der ruim é só voltar. O sentido reside no fato de
que Instavel e Estável é menos por questão de dependency hell e mais
para não precisar lidar com mudanças de comportamento, configuração
que ocorrem em atualizações, daí o estável 'não tem' atualizações e o
instável tem [1]. Isso é mais útil para servidores (e nem tanto também
como discuto adiante).

## Problemas

Apesar de poder ter várias versões de programas em seu repositório, o
que acontece é que ele normalmente tem apenas uma, ou algumas a mais
em programas importantes, e isso acontece porque manter diversas
versões funcionando é difícil[1][2], principalmente porque o próprio
desenvolvedor não dá suporte mais as versões antigas, então não dá
para manter uma versão que pode estar com problemas de segurança nos
repositórios (embora muitas alternativas como o docker façam isso, mas
mais sobre isso adiante).

Mas espera... e os tais flakes? pois é, os flakes são muito mencionados
e se tem planos do NixOS em torná-los oficiais, mas em minha opinião,
são apenas uma gambiarra que deve ser usada somente em último
caso. Eles funcionam pinando uma versão do repositório de pacotes (por
commit, tag, etc.), de modo que a versão do pacote desejado esteja
disponível[5]. Porém, isso também incorre nos erros que fizeram os
mantenedores não manterem as versões em primeiro lugar: a ausência de
suporte dos desenvolvedores.

Outro problema é que não tem todos os pacotes do mundo, ao contrário
do que se possa esperar ao ver o tamanho do repositório nix. Não tenho
uma razão concreta sobre porque isso ocorre mas deve ser devido ao
fato de que converter um pacote deb, rpm, arch para nix é
difícil. Muitas vezes tem que mudar coisas no próprio código, devido
aos desenvolvedores não fazerem os dados variáveis (que irão ser
gerenciados pelos nix modules) serem criados ao rodar e não ao
compilar, por exemplo (esse é um caso que eu presenciei ao tentar
empacotar o Urbackup). Embora, se o programa em questão seguir as
diretrizes padrão de pacotes linux, isso não devesse ser
necessário. Por exemplo, no meu caso de uso, não consegui achar os
pacotes do qt6 - nem qt mantainence (nem deveria) -, urbackup-server,
pi-hole.

Um terceiro problema, menos grave, é que ele não tem gerenciador de
pacotes gráfico (famosa lojinha), documentação é bem útil e até
extensa mas meio confusa.

## Conclusões

O NixOS tem seus problemas atuais mas nada nega o potencial que ele
tem de ser a melhor distro no futuro, dispensando docker, flatpak,
ansible, etc. E eu recomendaria que você começasse a usar e aprender
ele imediatamente que no futuro só se usaria Nix e distros baseadas
nele.

Porém, ando pensando que talvez não devessemos usar mais de
uma versão do mesmo software, talvez devessemos usar só o software
mais atualizado, e as dependencias que não forem achadas deve-se
tornar responsabilidade do desenvolvedor em atualizá-las. Isso porque
programas e dependências que não estão na última versão são inseguros
por natureza, o desenvolvedor não as mantêm mais e tentar manter no
nível do enpacotador é só recursos jogados fora. É um problema de
segurança usar softwares antigos. A versão atual tem que ser melhor
que a antiga, e se não for, faça um fork e portanto um novo programa,
que será mantido por você. Nesse ponto, o Nix não seria ruim, mas
não teria tantas vantagens (ainda assim mil vezes melhor que docker,
ansible, etc.).

E Luke Smith talvez esteja certo sobre o Arch Linux ser a melhor
distro. No Arch todos os programas são os mais atualizados possíveis,
não tem release, é só um repositório atualizando infinitamente. Porém
você vai ter que lidar com a instalação difícil e com mudanças de
configuração constantes, mas isso também é responsabilidade do
desenvolvedor garantir que atualizar não seja um sacrifício.

Enfim, use NixOS ou use Arch Linux (ou outro rolling release).

## Referências

[1] Isn’t the point of Nix not to need stable channels? Disponível em: <https://discourse.nixos.org/t/isnt-the-point-of-nix-not-to-need-stable-channels/57385/9>. Acesso em: 15 dez. 2024. 

[2] Why is there only one version in nixpkgs? - Development / Nixpkgs Architecture. Disponível em: <https://discourse.nixos.org/t/why-is-there-only-one-version-in-nixpkgs/47773>. Acesso em: 21 dez. 2024. 

[3] How to install /var - Help. Disponível em: <https://discourse.nixos.org/t/how-to-install-var/58040/19>. Acesso em: 31 dez. 2024. 

[4] NixOS modules - NixOS Wiki. Disponível em: <https://nixos.wiki/wiki/NixOS_modules>. Acesso em: 22 fev. 2025. 

[5] flake.nix Configuration Explained. Disponível em: <https://nixos-and-flakes.thiscute.world/nixos-with-flakes/nixos-flake-configuration-explained>. Acesso em: 16 dez. 2024. 

[6] DOLSTRA, E.; DE JONGE, M.; VISSER, E. Nix: A Safe and Policy-Free
System for Software Deployment. 2004.

[7] How Nix Works | Nix & NixOS. Disponível em: <https://nixos.org/guides/how-nix-works/>. Acesso em: 30 jun. 2024. 

[8] EMERGENCY_APRICOT_77. How do multiple versions of the package internally work? r/NixOS, 2 jul. 2023. Disponível em: <www.reddit.com/r/NixOS/comments/14ogcqh/how_do_multiple_versions_of_the_package/>. Acesso em: 13 dez. 2024

[9] Nix-env : how i can install two differents versions of the same software inside a user - Help. Disponível em: <https://discourse.nixos.org/t/nix-env-how-i-can-install-two-differents-versions-of-the-same-software-inside-a-user/3490>. Acesso em: 13 dez. 2024. 
