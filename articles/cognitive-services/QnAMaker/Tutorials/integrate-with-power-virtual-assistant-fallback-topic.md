---
title: 'Tutorial: Integrar-se com agentes virtuais de potência - QnA Maker'
description: Neste tutorial, melhore a qualidade da sua base de conhecimento com aprendizagem ativa. Reveja, aceite ou rejeite, ou adicione sem remover ou alterar as questões existentes.
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: tutorial
ms.date: 06/08/2020
ms.openlocfilehash: 42b50fcf0df27ddbc3e587a7d8e038e4979935ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91777405"
---
# <a name="tutorial-add-your-knowledge-base-to-power-virtual-agents"></a>Tutorial: Adicione a sua base de conhecimentos a Agentes Virtuais de Energia
Crie e estenda um bot [de Agentes Virtuais de Energia](https://powervirtualagents.microsoft.com/) para fornecer respostas a partir da sua base de conhecimento.

Neste tutorial, ficará a saber como:

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar um bot de agentes virtuais de energia
> * Criar um tópico de recuo do sistema
> * Adicione o QnA Maker como uma ação a um tópico como um fluxo power automamate
> * Criar uma solução power automate
> * Adicione um fluxo power automamate à sua solução
> * Publicar Agentes Virtuais de Poder
> * Teste agentes virtuais de poder e receba uma resposta da sua base de conhecimento qnA Maker

## <a name="integrate-an-agent-with-a-knowledge-base"></a>Integrar um agente com uma base de conhecimento

[Os Agentes Virtuais](https://powervirtualagents.microsoft.com/) de Potência permitem que as equipas criem bots poderosos utilizando uma interface gráfica guiada e sem código. Não precisa de cientistas de dados ou desenvolvedores.

No Power Virtual Agents, cria-se um agente com uma série de tópicos (áreas de assunto), de forma a responder a perguntas dos utilizadores através da realização de ações. Se uma resposta não puder ser encontrada, um recuo do sistema pode responder.

Configure o agente para enviar a pergunta para a sua base de conhecimento como parte da ação de um tópico, ou como parte do caminho tópico *de fallback do sistema.* Ambos usam uma ação para se conectarem à sua base de conhecimento e devolverem uma resposta.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automamate conecta-se à `GenerateAnswer` ação

Para ligar o seu agente à sua base de conhecimento, utilize o Power Automamate para criar a ação. A Power Automamate fornece um fluxo de processo, que se conecta à API da QnA `GenerateAnswer` Maker.

Depois de conceber e guardar o fluxo, está disponível a partir de uma solução Power Automamate. Usa essa solução como ação no teu agente.

## <a name="connect-an-agent-to-your-knowledge-base"></a>Ligue um agente à sua base de conhecimentos

Aqui está uma visão geral dos passos para ligar um agente em Agentes Virtuais de Energia a uma base de conhecimento no QnA Maker.

* No portal [QnA Maker:](https://www.qnamaker.ai/)
    * Construa e publique a sua base de conhecimentos.
    * Copie os detalhes da base de conhecimento, incluindo o ID, a chave de ponto final de tempo de execução e o anfitrião do ponto final em tempo de execução.
* No portal [De Agentes Virtuais de Energia:](https://powerva.microsoft.com/)
    * Constrói um tópico de agente.
    * Chame uma ação (para o fluxo de automatização de energia).
* No portal [Power Automamate:](https://us.flow.microsoft.com/)
    * Pesquisa rumo à resposta Generate usando o modelo _do Criador QnA_
    * Use o modelo para configurar o fluxo para utilizar o [GenerateAnswer do Criador QnA](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/).
        * QnA Maker publicou informações sobre base de conhecimento:
            * ID da base de conhecimento
            * Anfitrião de ponto final de recursos QnA Maker
            * Chave de ponto final de recurso QnA Maker
        * Entrada - consulta do utilizador
        * Saída - resposta base de conhecimento
    * Crie uma solução e adicione o fluxo, ou adicione fluxo à solução existente.
* Regresso aos Agentes Virtuais de Energia:
    * Selecione a saída da solução como uma mensagem para um tópico.

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

1. Siga o [quickstart](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de conhecimento. Não complete a última secção sobre criar um bot. Em vez disso, use este tutorial para criar um bot com Agentes Virtuais de Energia.

    > [!div class="mx-imgBorder"]
    > ![Screenshot das definições de base de conhecimento publicadas](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Introduza as definições de base de conhecimento publicadas encontradas na página **Definições** no portal [QnA Maker.](https://www.qnamaker.ai/) Necessitará desta informação para o [passo power automamate](#create-a-power-automate-flow-to-connect-to-your-knowledge-base) para configurar a sua ligação QnA `GenerateAnswer` Maker.

1. No portal QnA Maker, na página **Definições,** encontre a chave de ponto final, o anfitrião do ponto final e o ID da base de conhecimento.

## <a name="create-an-agent-in-power-virtual-agents"></a>Criar um agente em Agentes Virtuais de Poder

1. [Inscreva-se em Agentes Virtuais de Energia](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409). Use a sua conta de e-mail de escola ou trabalho.

1. Se este é o seu primeiro bot, estará na página **inicial** do agente. Se este não for o seu primeiro bot, selecione o bot da área superior direita da página e selecione **+ Novo Bot**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot da página inicial dos agentes virtuais de energia](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="topics-provided-in-the-bot"></a>Tópicos fornecidos no bot

O agente usa a coleção de tópicos para responder a perguntas na sua área de assunto. Neste tutorial, o agente tem muitos tópicos fornecidos para si, divididos em tópicos de utilizador e tópicos do sistema.

Selecione **Tópicos** da navegação à esquerda para ver os tópicos fornecidos pelo bot.

> [!div class="mx-imgBorder"]
> ![Screenshot dos tópicos fornecidos no agente](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-the-system-fallback-topic"></a>Crie o tópico de recuo do sistema

Embora o agente possa ligar-se à sua base de conhecimento a partir de qualquer tópico, este tutorial utiliza o tópico de recuo do *sistema.* O tópico de recuo é usado quando o agente não consegue encontrar uma resposta. O agente transmite o texto do utilizador à API da QnA `GenerateAnswer` Maker, recebe a resposta da sua base de conhecimento e exibe-o ao utilizador como mensagem.

1. No portal [Agentes Virtuais de Potência,](https://powerva.microsoft.com/#/) no canto superior direito, selecione **Definições** (o ícone da engrenagem). Em seguida, selecione **System Fallback**.

    > [!div class="mx-imgBorder"]
    > ![Screenshot do item do menu de Agentes Virtuais de Potência para o Recuo do Sistema](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. **Selecione + Adicione** para adicionar um tópico de recuo do sistema.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de adicionar um tópico de retorno.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Depois de adicionar o tópico, selecione **Go to Fallback tópico** para autor do tópico de recuo sobre a tela de autoria.

    > [!TIP]
    > Se precisar de voltar ao tema do retorno, está disponível na secção **Tópicos,** como parte dos tópicos do **Sistema.**

## <a name="use-the-authoring-canvas-to-add-an-action"></a>Use a tela de autoria para adicionar uma ação

Utilize a tela de autoria de Agentes Virtuais de Potência para ligar o tópico de retorno à sua base de conhecimento. O tópico começa com o texto do utilizador não reconhecido. Adicione uma ação que passe esse texto para o QnA Maker e, em seguida, mostre a resposta como uma mensagem. O último passo para exibir uma resposta é tratado como um [passo separado](#add-your-solutions-flow-to-power-virtual-agents), mais tarde neste tutorial.

Esta secção cria o fluxo de conversação tópico de recuo.

1. A nova ação de recuo pode já ter elementos de fluxo de conversação. Elimine o item **'Escalar'** selecionando o menu **Opções.**

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/delete-escalate-action-using-option-menu.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::

1. Selecione o **+** conector que flui para a caixa **'Mensagem'** e, em seguida, selecione **Chame uma ação**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::

1. Selecione **Criar um fluxo**. O processo leva-o ao portal Power Automamate.

    > [!div class="mx-imgBorder"]
    > ![Screenshot de Criar um fluxo](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)


    Power Automamate abre-se a um novo modelo. Não vai usar este novo modelo.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-flow-initial-template.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::

## <a name="create-a-power-automate-flow-to-connect-to-your-knowledge-base"></a>Crie um fluxo power automate para ligar à sua base de conhecimento

O procedimento a seguir cria um fluxo power-automatização que:
* Pega no texto do utilizador que está a chegar e envia-o para o QnA Maker.
* Devolve a resposta máxima ao seu agente.

1. Na **Power Automamate**, selecione **Modelos** da navegação à esquerda. Se lhe perguntarem se deseja sair da página do navegador, aceite o Leave.

1. Na página de modelos, procure o modelo **Gerar resposta usando o QnA Maker** e, em seguida, selecione o modelo. Este modelo tem todos os passos para ligar para o QnA Maker com as definições da base de conhecimento e devolver a resposta superior.

1. No novo ecrã para o fluxo do QnA Maker, **selecione Continue**.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-automate-qna-flow-template-continue.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::

1. Selecione a caixa de ação **Generate Answer** e preencha as definições do QnA Maker a partir de uma secção anterior intitulada [Criar e publicar uma base de conhecimento](#create-and-publish-a-knowledge-base). O seu **Anfitrião de Serviço** na seguinte imagem refere-se ao anfitrião **da** base de conhecimento e encontra-se no formato de `https://YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker` .


    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fill-in-generate-answer-settings.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::

1. Selecione **Guardar** para guardar o fluxo.

## <a name="create-a-solution-and-add-the-flow"></a>Criar uma solução e adicionar o fluxo

Para que o agente encontre e ligue ao fluxo, o fluxo deve ser incluído numa solução power automamate.

1. Enquanto ainda está no portal Power Automamate, **selecione Soluções** a partir da navegação do lado esquerdo.

1. Selecione **+ Nova solução**.

1. Introduza um nome a apresentar. A lista de soluções inclui todas as soluções da sua organização ou escola. Escolha uma convenção de nomeação que o ajude a filtrar apenas as suas soluções. Por exemplo, pode pré-fixo o seu e-mail para o nome da sua solução: `jondoe-power-virtual-agent-qnamaker-fallback` .

1. Selecione o seu editor na lista de escolhas.

1. Aceite os valores predefinidos para o nome e versão.

1. Selecione **Criar** para terminar o processo.

## <a name="add-your-flow-to-the-solution"></a>Adicione o seu fluxo à solução

1. Na lista de soluções, selecione a solução que acabou de criar. Deve estar no topo da lista. Se não for, procure pelo seu nome de e-mail, que faz parte do nome da solução.

1. Na solução, **selecione + Adicione a existência**e, em seguida, selecione **Flow** da lista.

1. Encontre o seu fluxo a partir da lista de **soluções Externas** e, em seguida, selecione **Adicionar** para terminar o processo. Se houver muitos fluxos, olhe para a coluna **modificada** para encontrar o fluxo mais recente.

## <a name="add-your-solutions-flow-to-power-virtual-agents"></a>Adicione o fluxo da sua solução a Agentes Virtuais de Potência

1. Volte ao separador do navegador com o seu agente em Power Virtual Agents. A tela de autoria ainda deve estar aberta.

1. Para inserir um novo passo no fluxo, acima da caixa de ação **mensagem,** selecione o **+** conector. Em seguida, **selecione Chame uma ação**.

1. A partir da janela pop-up **Flow,** selecione o novo fluxo denominado **Gerar respostas usando a base de conhecimento do QnA Maker...**. A nova ação aparece no fluxo.

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-flow-after-adding-action.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::

1. Para definir corretamente a variável de entrada para a ação do QnA Maker, **selecione Selecione uma variável**e, em seguida, selecione **bot. Não reconhecidoTriggerPhrase.**

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-selection-action-input.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::


1. Para definir corretamente a variável de saída para a ação do QnA Maker, na ação **Message,** selecione **UnrecognizedTriggerPhrase,** em seguida, selecione o ícone para inserir uma variável, `{x}` em seguida, selecione **FinalAnswer**.

1. A partir da barra de ferramentas de contexto, **selecione Save**, para guardar os detalhes de tela de autoria para o tópico.

Aqui está o que o último agente tela parece.

> [!div class="mx-imgBorder"]
> ![A screenshot mostra a tela do agente final com frases de gatilho, ação e, em seguida, secções de mensagem.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-the-agent"></a>Teste o agente

1. No painel de teste, **alternar Faixa entre tópicos**. Isto permite-lhe observar a progressão entre tópicos, bem como dentro de um único tópico.

1. Teste o agente introduzindo o texto do utilizador na seguinte ordem. A tela de autoria relata os passos bem sucedidos com uma marca de verificação verde.

    |Ordem de perguntas|Perguntas de teste|Objetivo|
    |--|--|--|
    |1|Hello|Começar a conversa|
    |2|Horário de loja|Tópico da amostra. Isto está configurado para si sem qualquer trabalho adicional da sua parte.|
    |3|Sim|Em resposta a `Did that answer your question?`|
    |4|Excelente|Em resposta a `Please rate your experience.`|
    |5|Sim|Em resposta a `Can I help with anything else?`|
    |6|Como posso melhorar o desempenho da produção para previsões de consulta?|Esta pergunta desencadeia a ação de recuo, que envia o texto para a sua base de conhecimento para responder. Então a resposta é mostrada. as marcas verdes de verificação das ações individuais indicam o sucesso de cada ação.|

    :::image type="content" source="../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png" alt-text="Screenshot parcial do fluxo de conversação, com a opção de eliminação realçada.":::

## <a name="publish-your-bot"></a>Publique o seu bot

Para disponibilizar o agente a todos os membros da sua escola ou organização, tem de publicá-lo.

1. A partir da navegação à esquerda, **selecione Publicar**. Em seguida, **selecione Publicar** na página.

1. Experimente o seu bot no site de demonstração (procure o link em **Publicação).**

    Uma nova página web abre com o seu bot. Faça ao bot a mesma pergunta de teste: `How can I improve the throughput performance for query predictions?`

    > [!div class="mx-imgBorder"]
    > ![Screenshot da tela do agente final](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Partilhe o seu bot

Para partilhar o site de demonstração, configuure-o como um canal.

1. A partir da navegação à esquerda, **selecione Canais de Gestão**  >  **Channels**.

1. Selecione **o site Demo** da lista de canais.

1. Copie o link e **selecione Guardar**. Cole o link para o seu site de demonstração num e-mail para a sua escola ou membros da organização.

## <a name="clean-up-resources"></a>Limpar os recursos

Quando terminar a base de conhecimento, remova os recursos do QnA Maker no portal Azure.

## <a name="next-step"></a>Passo seguinte

[Obter análises da base de dados de conhecimento](../How-To/get-analytics-knowledge-base.md)

Saiba mais sobre:
* [Power Virtual Agents](https://docs.microsoft.com/power-virtual-agents/)
* [Power Automate](https://docs.microsoft.com/power-automate/)
* [Conector QnA Maker](https://us.flow.microsoft.com/connectors/shared_cognitiveservicesqnamaker/qna-maker/) e as [definições para o conector](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)