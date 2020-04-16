---
title: 'Tutorial: Integrar-se com agentes virtuais de potência - Fabricante QnA'
titleSuffix: Azure Cognitive Services
description: Neste tutorial, melhore a qualidade da sua base de conhecimento com aprendizagem ativa. Reveja, aceite ou rejeite, ou adicione sem remover ou alterar as questões existentes.
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 03/11/2020
ms.author: diberry
ms.openlocfilehash: 4557dee995c8a01067f7e6ad0e79bb7115b6ecdb
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402820"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Tutorial: Adicione a sua base de conhecimento a Agentes Virtuais De Potência
Crie e estenda um bot [Power Virtual Agents](https://powervirtualagents.microsoft.com/) para fornecer respostas da sua base de conhecimento.

Neste tutorial, ficará a saber como:

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um bot power virtual agents
> * Criar um tópico de recuo do sistema
> * Adicione o Fabricante qnA como uma ação a um tópico como um fluxo power automate
> * Criar uma solução Power Automate
> * Adicione um fluxo power automate à sua solução
> * Publicar Agentes Virtuais de Poder
> * TestPower Agentes Virtuais, e receba uma resposta da sua base de conhecimento qnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrar um agente com base de conhecimento

[Os Agentes Virtuais de Potência](https://powervirtualagents.microsoft.com/) permitem que as equipas criem bots poderosos utilizando uma interface gráfica guiada e sem código. Não precisa de cientistas de dados ou desenvolvedores.

Em Power Virtual Agents, cria-se um agente com uma série de tópicos (áreas temáticas), de forma a responder às perguntas dos utilizadores através da realização de ações. Se não for possível encontrar uma resposta, um recuo do sistema pode devolver uma resposta.

Configure o agente para enviar a questão para a sua base de conhecimento como parte da ação de um tópico, ou como parte do caminho tópico do *Sistema de Recuo.* Ambos usam uma ação para se ligarem à sua base de conhecimento e devolverem uma resposta.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate liga-se à `GenerateAnswer` ação

Para ligar o seu agente à sua base de conhecimentos, utilize power automate para criar a ação. Power Automate fornece um fluxo de processo, que se `GenerateAnswer` conecta à API da QnA Maker.

Depois de projetar e salvar o fluxo, está disponível a partir de uma solução Power Automate. Use esta solução como uma ação no seu agente.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Ligue um agente à sua base de conhecimentos

Aqui está uma visão geral dos passos para ligar um agente em Agentes Virtuais de Energia a uma base de conhecimento em QnA Maker.

* No portal [QnA Maker:](https://www.qnamaker.ai/)
    * Construa e publique a sua base de conhecimento.
    * Copie os detalhes da sua base de conhecimento, incluindo a chave de ponto final do ponto de execução, e o anfitrião do ponto final do tempo de execução.
* No portal [Power Virtual Agents:](https://powerva.microsoft.com/)
    * Construa um tópico de agente.
    * Chame uma ação (para power Automate flow).
* No portal [Power Automate:](https://us.flow.microsoft.com/)
    * Construa um fluxo com um conector para a [Resposta Gerada do Fabricante qnA](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * A QnA Maker publicou informações sobre base de conhecimento:
            * ID base de conhecimento
            * Hospedeiro de fim de recurso QnA Maker
            * Chave final do ponto final do qnA Maker
        * Entrada - consulta do utilizador
        * Saída - resposta base de conhecimento
    * Crie uma solução e adicione o fluxo.
* Regresso a Agentes Virtuais De Energia:
    * Selecione a saída da solução como mensagem para um tópico.

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

1. Siga o [início rápido](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de conhecimento. Não complete a última secção sobre criar um bot. Em vez disso, use este tutorial para criar um bot com Agentes Virtuais De Potência.

    > [!div class="mx-imgBorder"]
    > ![Screenshot das definições de base de conhecimento publicadas](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Introduza as definições de base de conhecimento publicadas encontradas na página **Definições** do portal [QnA Maker.](https://www.qnamaker.ai/) Você precisará desta informação para o [passo Power Automate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) para configurar a sua ligação QnA Maker. `GenerateAnswer`

1. No portal QnA Maker, na página **Definições,** encontre a chave de ponto final, o anfitrião do ponto final e o ID da base de conhecimento.

## <a name="create-an-agent-in-power-virtual-agents"></a>Criar um agente em Agentes Virtuais de Energia

1. [Inscreva-se na Power Virtual Agents](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Use a sua conta de e-mail escolar ou de trabalho.
1. Se este é o seu primeiro bot, estará na página **inicial** do agente. Se este não for o seu primeiro bot, selecione o bot da área superior direita da página e selecione **+ Novo Bot**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da página inicial dos Agentes Virtuais de Poder](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

1. Introduza as definições de base de conhecimento publicadas, encontradas na página **Definições** no portal [QnA Maker.](https://www.qnamaker.ai/)

## <a name="topics-provided-in-the-bot"></a>Tópicos fornecidos no bot

O agente usa a coleção de tópicos para responder a perguntas na sua área de assunto. Neste tutorial, o agente tem muitos tópicos previstos para si, divididos em tópicos de utilizador e tópicos do sistema.

> [!div class="mx-imgBorder"]
> ![Screenshot dos tópicos fornecidos no agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Criar o tópico de recuo do sistema

Embora o agente possa ligar-se à sua base de conhecimento a partir de qualquer tópico, este tutorial usa o tópico de recuo do *sistema.* O tópico de recuo é usado quando o agente não consegue encontrar uma resposta. O agente transmite o texto do utilizador à `GenerateAnswer` API do Fabricante de QnA, recebe a resposta da sua base de conhecimentos e exibe-a ao utilizador como mensagem.

1. No portal [Power Virtual Agents,](https://powerva.microsoft.com/#/) no canto superior direito, selecione **Definições** (o ícone da engrenagem). Em seguida, selecione **System Fallback**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do item do menu Power Agentes Virtuais para O Recuo do Sistema](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Selecione **+ Adicione** para adicionar um tópico de recuo do sistema.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar um tópico de recuo.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Depois de adicionado o tópico, selecione **Go to Fallback tópico** para autor do tema de recuo na tela de autor.

    > [!TIP]
    > Se precisar de voltar ao tema do recuo, está disponível na secção **Tópicos,** como parte dos tópicos do **Sistema.**

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Use a tela de autoria para adicionar uma ação

Utilize a tela de autor de Agentes Virtuais Power para ligar o tópico de recuo à sua base de conhecimentos. O tópico começa com o texto do utilizador não reconhecido. Adicione uma ação que passa esse texto ao QnA Maker e, em seguida, mostre a resposta como uma mensagem. O último passo de mostrar uma resposta é tratado como um [passo separado,](#add-your-solutions-flow-to-power-virtual-agents)mais tarde neste tutorial.

Esta secção cria o fluxo de conversação tópico de recuo.

1. A nova ação de recuo pode já ter elementos de fluxo de conversação. Elimine o item **Escalate** selecionando o menu **Opções.**

    > [!div class="mx-imgBorder"]
    > ![Screenshot do tópico de recuo dos agentes virtuais de Power](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Selecione o **+** conector que flui **da** caixa mensagem e, em seguida, selecione Ligue para **uma ação**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de Call a action](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Selecione **Criar um fluxo**. O processo leva-o ao portal Power Automate.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de Criar um fluxo](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Crie um fluxo power automate para ligar à sua base de conhecimento

O seguinte procedimento cria um fluxo power automate que:
* Pega no texto do utilizador que chega e envia-o para o QnA Maker.
* Atribui a resposta superior do Fabricante QnA a uma variável, e envia a variável (resposta superior) como resposta de volta ao seu agente.

1. Em **Power Automate,** o modelo de **fluxo** é iniciado para si. No item de fluxo de **agentes virtuais power,** selecione **Editar** para configurar a variável de entrada proveniente do agente para a sua base de conhecimentos. A variável de entrada baseada em texto é a pergunta de texto submetida pelo utilizador do seu agente.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da opção Power Automate para configurar a sua variável de entrada como uma cadeia de texto](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Adicione uma entrada de texto `InputText`e nomeie `IncomingUserQuestion`a variável, com uma descrição de . Este naming ajuda a distinguir o texto de entrada do texto de saída que cria mais tarde.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da opção Power Automate para configurar o nome e descrição variáveis de entrada](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Selecione o **+** conector que flui da caixa **Power Virtual Agents,** para inserir um novo passo no fluxo (antes do valor de retorno para o Agente Virtual **power**). Em seguida, selecione **Adicionar uma ação**.

1. Procure `Qna` encontrar as ações do **Fabricante qnA** e, em seguida, selecione **Generate answer**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da resposta Gerar](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    As definições de ligação necessárias para o Fabricante QnA aparecem na ação e as definições de perguntas do agente.

    > [!div class="mx-imgBorder"]
    > ![Screenshot das definições de ligação necessárias](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configure a ação com o id da base de conhecimento, anfitrião de ponto final e chave de ponto final. Estes encontram-se na página **Definições** da sua base de conhecimentos, no portal QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Screenshot das definições de base de conhecimento publicadas](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Para configurar a **pergunta,** selecione a `InputText` caixa de texto e, em seguida, selecione a lista.

1. Para inserir um novo passo no **+** fluxo, selecione o conector que flui da caixa de ação **de resposta Generate.** Em seguida, selecione **Adicionar uma ação**.

1. Para adicionar uma variável para `GenerateAnswer`capturar o texto `Initialize variable` de resposta devolvido, procure e selecione a ação.

    Desloque o `OutgoingQnAAnswer`nome da variável para, e selecione o tipo de **String**. Não desloque o **Valor.**

    > [!div class="mx-imgBorder"]
    > ![Screenshot de inicialização da variável de saída](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Para inserir um novo passo no **+** fluxo, selecione o conector que flui da caixa de ação **variável Inicializar.** Em seguida, selecione **Adicionar uma ação**.

1. Para definir toda a base de conhecimento Resposta JSON à variável, procure e selecione a`Apply to each` ação. Selecione o `GenerateAnswer` `answers`.

1. Para devolver apenas a resposta superior, na mesma **Aplicar a cada** caixa, selecione Adicionar uma **ação**. Procure e selecione **variável set**.

    Na caixa **variável set,** selecione a caixa de texto para **Nome**, e, em seguida, selecione **OutgoingQnAAnswer** da lista.

    Selecione a caixa de texto para **Valor**e, em seguida, selecione **Resposta resposta** resposta da lista.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de definir o nome e o valor para a variável](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Para devolver a variável (e o seu valor), selecione o **valor de Retorno(s) ao** item de fluxo do Agente Virtual Power. Em seguida, selecione **Editar** > **Adicionar uma saída**. Selecione um tipo de saída `FinalAnswer`de **texto** e, em seguida, introduza o **título** de . Selecione a **Value**caixa de texto `OutgoingQnAAnswer` para o Valor e, em seguida, selecione a variável.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de definir o valor de retorno](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Selecione **Guardar** para guardar o fluxo.

## <a name="create-a-solution-and-add-the-flow"></a>Criar uma solução e adicionar o fluxo

Para que o agente encontre e se conectem ao fluxo, o fluxo deve ser incluído numa solução Power Automate.

1. Ainda no portal Power Automate, selecione **Soluções** a partir da navegação do lado esquerdo.

1. Selecione **+ Nova solução**.

1. Introduza um nome a apresentar. A lista de soluções inclui todas as soluções da sua organização ou escola. Escolha uma convenção de nomeação que o ajude a filtrar apenas as suas soluções. Por exemplo, pode preser o seu `jondoe-power-virtual-agent-qnamaker-fallback`e-mail no seu nome de solução: .

1. Selecione o seu editor na lista de escolhas.

1. Aceite os valores predefinidos para o nome e versão.

1. Selecione **Criar** para terminar o processo.

## <a name="add-your-flow-to-the-solution"></a>Adicione o seu fluxo à solução

1. Na lista de soluções, selecione a solução que acabou de criar. Deve estar no topo da lista. Caso não seja, procure pelo seu nome de e-mail, que faz parte do nome da solução.

1. Na solução, selecione **+ Adicione o existente,** e, em seguida, selecione **Flow** da lista.

1. Encontre o seu fluxo e, em seguida, selecione **Adicionar** para terminar o processo. Se houver muitos fluxos, olhe para a coluna **Modificada** para encontrar o fluxo mais recente.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Adicione o fluxo da sua solução a Agentes Virtuais De Energia

1. Volte ao separador do navegador com o seu agente em Agentes Virtuais Power. A tela de autoria ainda deve estar aberta.

1. Para inserir um novo passo no fluxo, sob **+** a caixa de ação **Mensagem,** selecione o conector. Em seguida, selecione **Chamar uma ação**.

1. Na nova ação, selecione o valor de entrada de **UnrecognizedTriggerPhrase**. Isto passa o texto do agente para o fluxo.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da opção Agentes Virtuais de Poder para selecionar frase de gatilho não reconhecida](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Para inserir um novo passo no fluxo, sob **+** a caixa **de ação,** selecione o conector. Em seguida, selecione **Mostrar uma mensagem**.

1. Introduza o `Your answer is:`texto da mensagem, . Selecione `FinalAnswer` como variável de contexto utilizando a função da barra de ferramentas no local.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da opção Agentes Virtuais de Poder para introduzir o texto da mensagem](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. A partir da barra de ferramentas de contexto, selecione **Guardar,** para guardar os detalhes da tela de autor para o tópico.

Aqui está o aspeto da tela final do agente.

> [!div class="mx-imgBorder"]
> ![Screenshot da tela do agente final](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Teste o agente

1. No painel de teste, **alternar faixa entre tópicos**. Isto permite-lhe assistir à progressão entre tópicos, bem como dentro de um único tópico.

1. Teste o agente inserindo o texto do utilizador na seguinte ordem. A tela de autoria relata os passos bem sucedidos com uma marca de verificação verde.

    |Ordem de perguntas|Perguntas de teste|Objetivo|
    |--|--|--|
    |1|Hello|Iniciar conversa|
    |2|Horário de loja|Tópico de amostra. Isto está configurado para si sem qualquer trabalho adicional da sua parte.|
    |3|Sim|Em resposta a`Did that answer your question?`|
    |4|Excelente|Em resposta a`Please rate your experience.`|
    |5|Sim|Em resposta a`Can I help with anything else?`|
    |6|O que é uma base de conhecimento?|Esta pergunta desencadeia a ação de recuo, que envia o texto para a sua base de conhecimentos para responder. Então a resposta é mostrada. |

> [!div class="mx-imgBorder"]
> ![Screenshot da tela do agente final](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publique o seu bot

Para disponibilizar o agente a todos os membros da sua escola ou organização, tem de publicá-lo.

1. A partir da navegação à esquerda, selecione **Publicar**. Em seguida, selecione **Publicar** na página.

1. Experimente o seu bot no site da demonstração (procure o link em **Publicar).**

    Uma nova página web abre com o seu bot. Faça ao bot a mesma pergunta de teste:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Screenshot da tela do agente final](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Partilhe o seu bot

Para partilhar o site da demonstração, configure-o como um canal.

1. A partir da navegação à esquerda, selecione**Canais** **de Gestão** > .

1. Selecione **o site da Demo** na lista de canais.

1. Copie o link e selecione **Guardar**. Repasse o link para o seu site de demonstração num e-mail para a sua escola ou membros da organização.

## <a name="clean-up-resources"></a>Limpar recursos

Quando terminar com a base de conhecimento, remova os recursos do Fabricante QnA no portal Azure.

## <a name="next-step"></a>Passo seguinte

[Obter análises da base de dados de conhecimento](../How-To/get-analytics-knowledge-base.md)

Saiba mais sobre:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Conector QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) e [as definições para o conector](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)