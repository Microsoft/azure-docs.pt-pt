---
title: Crie um assistente de voz com Azure Percept DK e Azure Percept Audio
description: Saiba como criar e implementar uma solução de fala sem código para o seu Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: tutorial
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 76333e11916641be71c72ce6142cd59b496a17e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105023168"
---
# <a name="create-a-voice-assistant-with-azure-percept-dk-and-azure-percept-audio"></a>Crie um assistente de voz com Azure Percept DK e Azure Percept Audio

Neste tutorial, irá criar um assistente de voz a partir de um modelo para utilizar com o seu Azure Percept DK e Azure Percept Audio. A demonstração do assistente de voz é executada dentro do [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819) e contém uma seleção de objetos virtuais controlados por voz. Para controlar um objeto, diga a sua palavra-chave, que é uma palavra ou frase curta que acorda o seu dispositivo, seguida de um comando. Cada modelo responde a um conjunto de comandos específicos.

Este guia irá acompanhá-lo através do processo de configuração dos seus dispositivos, criando um assistente de voz e os recursos [necessários dos Serviços de Fala,](../cognitive-services/speech-service/overview.md) testando o seu assistente de voz, configurando a sua palavra-chave e criando palavras-chave personalizadas.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- Áudio Azure Percept
- Altifalantes ou auscultadores que podem ligar-se a tomada de áudio de 3,5 mm (opcional)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub
- [Configuração de áudio Azure Percept](./quickstart-percept-audio-setup.md)


## <a name="create-a-voice-assistant-using-an-available-template"></a>Crie um assistente de voz usando um modelo disponível

1. Navegue para [o Azure Percept Studio.](https://go.microsoft.com/fwlink/?linkid=2135819)

1. Abra o **separador Demos & tutoriais.**

    :::image type="content" source="./media/tutorial-no-code-speech/portal-overview.png" alt-text="Screenshot da página inicial do portal Azure.":::

1. Clique **em Experimentar modelos de assistente de voz** em **tutoriais e demonstrações de discurso**. Isto abrirá uma janela no lado direito do seu ecrã.

1. Faça o seguinte na janela:

    1. No menu dropdown **IoT Hub,** selecione o hub IoT ao qual o seu devkit está ligado.

    1. No menu de entrega do **dispositivo,** selecione o seu devkit.

    1. Selecione um dos modelos de assistente de voz disponíveis.

    1. Clique no **eu concordo com os termos & condições para esta** caixa de verificação do projeto.

    1. Clique em **Criar**.

    :::image type="content" source="./media/tutorial-no-code-speech/template-creation.png" alt-text="Screenshot da criação do modelo de assistente de voz.":::

1. Depois de clicar em **Criar,** o portal abre outra janela para criar o seu recurso temático de fala. Faça o seguinte na janela:

    1. Selecione a sua subscrição Azure na caixa **de subscrição.**

    1. Selecione o seu grupo de recursos preferido no menu de dropdown **do grupo De recurso.** Se quiser criar um novo grupo de recursos para utilizar com o seu assistente de voz, clique em **Criar** no menu suspenso e siga as indicações.

    1. Para **prefixo de aplicação,** insira um nome. Este será o prefixo do seu projeto e o seu nome de comando personalizado.

    1. Sob **Região,** selecione a região para a implantação de recursos para.

    1. De acordo com **o nível de preços de previsão LUIS**, selecione **Standard** (o nível livre não suporta pedidos de fala).

    1. Clique no botão **Criar**. Os recursos para a aplicação de assistente de voz serão implantados na sua subscrição.

        > [!WARNING]
        > **NÃO** feche a janela até que o portal termine de implantar o recurso. Fechar a janela prematuramente pode resultar num comportamento inesperado do assistente de voz. Uma vez implantado o seu recurso, a demonstração será exibida.

    :::image type="content" source="./media/tutorial-no-code-speech/resource-group.png" alt-text="Screenshot da janela de seleção de grupo de subscrição e recursos.":::

## <a name="test-out-your-voice-assistant"></a>Teste o seu assistente de voz

Para interagir com o seu assistente de voz, diga a palavra-chave seguida de um comando. Quando o SoM auricular reconhece a sua palavra-chave, o dispositivo emite um carrilhão (que pode ouvir se um altifalante ou auscultadores estiverem ligados) e os LEDs piscarão azul. Os LEDs mudarão para o azul de corrida enquanto o seu comando é processado. A resposta do assistente de voz ao seu comando será impressa em texto na janela de demonstração e emitida de forma audível através dos seus altifalantes/auscultadores. A palavra-chave predefinida (listada ao lado **da palavra-chave personalizada)** é definida como "Computador", e cada modelo tem um conjunto de comandos compatíveis que permitem interagir com objetos virtuais na janela de demonstração. Por exemplo, se estiver a usar a demo de hospitalidade ou de cuidados de saúde, diga "Computador, ligue a TELEVISÃO" para ligar a televisão virtual.

:::image type="content" source="./media/tutorial-no-code-speech/hospitality-demo.png" alt-text="Imagem de janela de demonstração de hospitalidade.":::

### <a name="hospitality-and-healthcare-demo-commands"></a>Comandos de demonstração de hospitalidade e cuidados de saúde

Tanto as demos de cuidados de saúde como de hospitalidade têm televisões virtuais, luzes, persianas e termostatos com os que pode interagir. São suportados os seguintes comandos (e variações adicionais):

* "Ligue/desligue as luzes."
* "Ligar/desligar a televisão."
* "Ligue/desligue o ar condicionado."
* "Abra/feche as persianas."
* "Definir a temperatura em X graus." (X é a temperatura desejada, por exemplo 75.)

:::image type="content" source="./media/tutorial-no-code-speech/healthcare-demo.png" alt-text="Imagem da janela de demonstração de cuidados de saúde.":::

### <a name="automotive-demo-commands"></a>Comandos de demonstração automóvel

A demonstração automóvel tem um aquecedor de assento virtual, descongelador e termóstato com o que pode interagir. São suportados os seguintes comandos (e variações adicionais):

* "Ligue/desligue o descongelador."
* "Ligue/desligue o aquecedor do assento."
* "Definir a temperatura em X graus." (X é a temperatura desejada, por exemplo 75.)
* "Aumentar/diminuir a temperatura em graus Y."


:::image type="content" source="./media/tutorial-no-code-speech/auto-demo.png" alt-text="Imagem de janela de demonstração de automóveis.":::

### <a name="inventory-demo-commands"></a>Comandos de demonstração de inventário

A demonstração de inventário tem uma seleção de caixas virtuais azuis, amarelas e verdes para interagir juntamente com uma app de inventário virtual. São suportados os seguintes comandos (e variações adicionais):

* "Adicionar/remover caixas X." (X é o número de caixas, por exemplo 4.)
* "Encomendar/enviar caixas X."
* "Quantas caixas estão em stock?"
* "Caixas do Conde Y" (Y é a cor das caixas, por exemplo, amarelo.)
* "Envie tudo em stock."


:::image type="content" source="./media/tutorial-no-code-speech/inventory-demo.png" alt-text="Screenshot da janela de demonstração de inventário.":::

## <a name="configure-your-keyword"></a>Configure a sua palavra-chave

Pode personalizar a palavra-chave para a sua aplicação de assistente de voz.

1. Clique em **alterar** ao lado **da palavra-chave personalizada** na janela de demonstração.

1. Selecione uma das palavras-chave disponíveis. Poderá escolher entre uma seleção de palavras-chave de amostra e quaisquer palavras-chave personalizadas que tenha criado.

1. Clique em **Guardar**.

### <a name="create-a-custom-keyword"></a>Crie uma palavra-chave personalizada

Pode criar a sua própria palavra-chave para a sua aplicação de voz. O treino para a sua palavra-chave personalizada pode ser concluído em apenas alguns minutos.

1. Clique **+ Crie a palavra-chave personalizada** perto da parte superior da janela de demonstração. 

1. Insira a sua palavra-chave desejada, que pode ser uma única palavra ou uma frase curta.

1. Selecione o seu **recurso Speech** (este encontra-se listado ao lado **do Comando Personalizado** na janela de demonstração e contém o seu prefixo de aplicação).

1. Clique em **Guardar**. 

## <a name="create-a-custom-command"></a>Criar um comando personalizado

O portal também fornece funcionalidade para criar comandos personalizados com recursos de fala existentes. "Comando personalizado" refere-se à própria aplicação do assistente de voz, e não a um comando específico dentro da aplicação existente. Ao criar um comando personalizado, está a criar um novo projeto de fala, que deve desenvolver ainda mais no [Speech Studio.](https://speech.microsoft.com/)

Para criar um novo comando personalizado a partir da janela de demonstração, clique **em + Criar Comando Personalizado** no topo da página e fazer o seguinte:

1. Insira um nome para o seu comando personalizado.

1. Introduza uma descrição do seu projeto (opcional).

1. Selecione o seu idioma preferido.

1. Selecione o seu recurso de fala.

1. Selecione o seu recurso LUIS.

1. Selecione o seu recurso de autoria LUIS ou crie um novo.

1. Clique em **Criar**.

:::image type="content" source="./media/tutorial-no-code-speech/custom-commands.png" alt-text="Screenshot da janela de criação de comandos personalizados.":::

Uma vez criado um comando personalizado, deve ir ao [Speech Studio](https://speech.microsoft.com/) para mais desenvolvimento. Se abrir o Speech Studio e não vir o seu comando personalizado listado, siga estes passos:

1. No painel de menus à esquerda no Azure Percept Studio, clique em **Speech** in **AI Projects**.

1. Selecione o separador **Comandos.**

    :::image type="content" source="./media/tutorial-no-code-speech/ai-projects.png" alt-text="Screenshot da lista de comandos personalizados disponíveis para editar.":::

1. Selecione o comando personalizado que deseja desenvolver. Isto abre o projeto no Speech Studio.

    :::image type="content" source="./media/tutorial-no-code-speech/speech-studio.png" alt-text="Screenshot do ecrã principal do estúdio da fala.":::

Para obter mais informações sobre o desenvolvimento de comandos personalizados, consulte a [documentação](../cognitive-services/speech-service/custom-commands.md)do Serviço de Fala .

## <a name="troubleshooting"></a>Resolução de problemas

### <a name="voice-assistant-was-created-but-does-not-respond-to-commands"></a>Assistente de voz foi criado mas não responde aos comandos

Verifique as luzes LED na Placa Interposer:

* Três luzes azuis sólidas indicam que o assistente de voz está pronto e à espera da palavra-chave.
* Se o LED central (L02) for branco, o devkit completou a inicialização e precisa de ser configurado com uma palavra-chave.
* Se o LED central (L02) estiver a piscar de branco, o Audio SoM ainda não completou a inicialização. A inicialização pode demorar alguns minutos a ser concluída.

Para obter mais informações sobre os indicadores LED, consulte o [artigo LED](./audio-button-led-behavior.md).

### <a name="voice-assistant-does-not-respond-to-a-custom-keyword-created-in-speech-studio"></a>Assistente de voz não responde a uma palavra-chave personalizada criada no Speech Studio

Isto pode ocorrer se o módulo de fala estiver desatualizado. Siga estes passos para atualizar o módulo de fala para a versão mais recente:

1. Clique em Dispositivos no painel de **menus** à esquerda da página inicial do Azure Percept Studio.

1. Encontre e selecione o seu dispositivo.

    :::image type="content" source="./media/tutorial-no-code-speech/devices.png" alt-text="Screenshot da lista de dispositivos no Azure Percept Studio.":::

1. Na janela do dispositivo, selecione o **separador Discurso.**

1. Verifique a versão do módulo de fala. Se houver uma atualização disponível, verá um botão **De atualização** ao lado do número da versão.

1. Clique em **Update** para implementar a atualização do módulo de fala. O processo de atualização geralmente demora 2-3 minutos a ser concluído.

## <a name="clean-up-resources"></a>Limpar os recursos

Assim que terminar de trabalhar com a sua aplicação de assistente de voz, siga estes passos para limpar os recursos de fala que implementou durante este tutorial:

1. A partir do [portal Azure,](https://portal.azure.com)selecione **grupos** de recursos do painel de menus esquerdo ou digite-o na barra de pesquisa.

    :::image type="content" source="./media/tutorial-no-code-speech/azure-portal.png" alt-text="Screenshot da página inicial do portal Azure mostrando painel de menu esquerdo e grupos de recursos.":::

1. Selecione o seu grupo de recursos.

1. Selecione todos os seis recursos que contêm o prefixo da sua aplicação e clique no ícone **Eliminar** no painel de menu superior.
\
    :::image type="content" source="./media/tutorial-no-code-speech/select-resources.png" alt-text="Screenshot dos recursos da fala selecionados para eliminação.":::

1. Para confirmar a eliminação, escreva **sim** na caixa de confirmação, verifique se selecionou os recursos corretos e clique em **Eliminar**.

    :::image type="content" source="./media/tutorial-no-code-speech/delete-confirmation.png" alt-text="Screenshot da janela de confirmação de apagar.":::

> [!WARNING]
> Isto removerá quaisquer palavras-chave personalizadas criadas com os recursos de fala que está a eliminar, e a demonstração do assistente de voz deixará de funcionar.

## <a name="next-steps"></a>Passos Seguintes

Agora que criou uma solução de fala sem código, tente criar uma [solução de visão sem código](./tutorial-nocode-vision.md) para o seu Azure Percept DK.