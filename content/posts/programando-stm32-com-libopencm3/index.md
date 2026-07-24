+++
title = 'Programando STM32 Com Libopencm3'
date = 2026-07-23T16:19:12-03:00
description = 'Aprenda a usar qualquer IDE da sua escolha para programar STM32 usando uma HAL código aberto, muito melhor que a do fabricante.'
tags = ['programação']
draft = false
authors = ['rafael_chuede']
+++

# Introdução / Problemática e Justificativa

Se você já programou alguma coisa para os microcontroladores da família STM32, já com certeza teve o desprazer de utilizar a IDE do fabricante, que além de lenta, poluída e cheia de bloatware, também necessita de login para funcionar. Nos primeiros instantes de uso o usuário ja é alertado que deve baixar as atualizações e pacotes sendo necessário a conexão com internet e login, o qual simplesmente não entra, você precisa ser um monge budista para não querer quebrar a tela do PC ao tentar configurar essa IDE... Enfim essas são apenas algumas reclamações sobre a CubeIDE, eu poderia ficar aqui escrevendo o dia todo sobre as inconveniências do programa, como os *bilhões de comentários* no *código gerado* pela IDE em todos os projetos e a praticamente obrigação do uso do CubeMX, um programa com Interface Gráfica onde o usuario usa o mouse para setar os periféricos, clocks e qualquer coisa no desenho do chip STM32...

Agora que o meu ódio com a IDE do fabricante foi destilado, posso introduzir sobre o que é a libopencm3. Libopencm3 trata-se de uma HAL(Hardware Abstraction Layer) que permite programar um monte  de microcontroladores ARM sem utilizar números mágicos(registradores), apenas com funções abstraídas você pode controlar todas as utilidades do MCU, como utilizar I2C com display, SPI, I2S, DMA, Interrupções, ou piscar um led. A libopencm3 é código aberto o que significa que nunca vai deixar de existir ou mudar drasticamente invibializando seu uso e o melhor de tudo *você pode usar qual IDE quiser*.


# Como começar a usar

Para programar usando libopencm3 existem vários jeitos, mas vou ensinar o jeito que eu faço, é muito simples, basta clonar esse template de código com submodulos ativos para que ja baixe tambem a libopencm3 dentro do repositório.
```
git clone --recurse-submodules https://github.com/libopencm3/libopencm3-template.git
```

depois dentro do repositorio libopencm3-template, entre no diretorio do libopencm3 e compile tudo com ``make`` ou apenas a família ARM que for utilizar, nesse caso F1 e F4:

```
cd libopencm3/ && make TARGETS='stm32/f1 stm32/f4'
```

Depois apenas volte para o root do repositório e entre no diretório 'my-project' e edite o Makefile para gerar os linkers certos do seu STM32. Apenas edite a linha DEVICE=stm32f407vgt6 para DEVICE=stm32f411re ou qualquer modelo e se quiser exclua as linhas `` CFILES += api.c AFILES += api-asm.S ``, agora você ja pode escrever seu código em my-project.c e compilar com `make` mas eu editaria tambem o nome do arquivo que será compilado: ``PROJECT = awesomesauce `` para um nome mais especifico do seu projeto, e eu sempre coloco uma regra para facilitar o flash do programa no STM32, adicionando: 
``
flashbin:
st-flash write main.bin 0x8000000
``. Pronto meu Makefile ficou assim:
```
PROJECT = testeTemplate
BUILD_DIR = bin

SHARED_DIR = ../my-common-code
CFILES = my-project.c

# TODO - you will need to edit these two lines!
DEVICE=stm32f411ce
OOCD_FILE = board/stm32f4discovery.cfg

# You shouldn't have to edit anything below here.
VPATH += $(SHARED_DIR)
INCLUDES += $(patsubst %,-I%, . $(SHARED_DIR))
OPENCM3_DIR=../libopencm3

include $(OPENCM3_DIR)/mk/genlink-config.mk
include ../rules.mk
include $(OPENCM3_DIR)/mk/genlink-rules.mk

flashbin:
	st-flash write main.bin 0x8000000

```

Para começar você pode editar my-project.c para esse exemplo de blink led seestiver usando uma placa stm32f411 como eu:
```
#include <libopencm3/stm32/rcc.h>
#include <libopencm3/stm32/gpio.h>

static void gpio_setup(void)
{

	rcc_periph_clock_enable(RCC_GPIOC);
	gpio_mode_setup(GPIOC, GPIO_MODE_OUTPUT, GPIO_PUPD_NONE, GPIO13);
}

int main(void)
{
	int i;

	gpio_setup();

	/* Blink the LED (PC8) on the board. */
	while (1) {

		gpio_toggle(GPIOC, GPIO13);	/* LED on/off */
		for (i = 0; i < 1000000; i++) {	/* Wait a bit. */
			__asm__("nop");
		}
	}

	return 0;
}
```

vai piscar o led em pc13, na blackpill é o led embutido na placa.

Se tudo ocorreu legal você já pode fazer outras pastas com diferentes projetos apenas crie uma pasta ao lado de my-project/ no root do repositorio do template e copie esse Makefile, editando somente o que for diferente como o nome do projeto e o modelo do stm32 se for diferente.

## Adicionando bibliotecas e outros arquivos .c/.h no projeto

Para adicionar outros arquivos .c apenas adicione o caminho do arquivo c no Makefile do projeto em C-files, separando cada arquivo com espaço ou '\':
```
CFILES = myproject.c  drivers/led.c
```

e inclua a pasta que contem os .h no Makefile como se fosse qualquer include em C:
```
INCLUDES += -I drivers/
```

bibliotecas pre-compiladas como .a você pode adicionar apenas passando o caminho no makefile:
``
LDLIBS += lib.a
``

# Como aprender a programar com libopencm3

O melhor jeito de começar a entender o básico no meu caso foi lendo um livro ótimo: * Beginning STM32: Developing with FreeRTOS, libopencm3 and GCC * do Autor Warren Gay, que eu saiba este livro é somente em inglês mas isso não deve ser um problema pra você ainda mais com IAs hoje em dia. O livro é muito bom para aprender a maior parte dos periféricos e funções básicas mas complica desnecessáriamente em alguns pontos na minha opinião como adição de FreeRTOS de um jeito complicado(A adição é bem simples na real) e com a instalação das dependencias logo no início.

Outro jeito ótimo de aprender é olhando exemplos de códigos que usam stm32 e libopencm3 na internet, existem vários e você pode pedir para IA te ajudar também. Eu mesmo tenho um repositório de exemplos simples de códigos e sempre que faço algo novo atualizo:
https://github.com/rbmannchued/libopencm3-examples/
Vale a pena dar uma olhada.

# Conclusão
Na minha humilde opinião a adoção da libopencm3 ao inves da ST hal e IDEs do fabricante são muito vantajosas, você se livra de qualquer software proprietário e das amarras do sistemas e ainda de quebra aprende melhor o que está fazendo, visto que a libopencm3 é menos abstraída. 

Você conseguirá desenvolver desde projetos mais simples a extraordinariamente complexos, eu mesmo já tenho vários projetos parrudos e mais complexos escritos com libopencm3, além de drivers e bibliotecas para facilitar a vida utilizando, por exemplo displays(https://github.com/rbmannchued).
