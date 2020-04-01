---
title: 'Tutorial: Integrar com power virtual agent - QnA Maker'
description: Neste tutorial, melhore a qualidade da sua base de conhecimento com aprendizagem ativa. Reveja, aceite ou rejeite, adicione sem remover ou alterar as questões existentes.
ms.topic: tutorial
ms.date: 03/11/2020
ms.openlocfilehash: 283667c587e395a1d712f82f3385582b4c5c3227
ms.sourcegitcommit: 632e7ed5449f85ca502ad216be8ec5dd7cd093cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2020
ms.locfileid: "80398878"
---
# <a name="tutorial-add-knowledge-base-to-power-virtual-agent"></a>Tutorial: Adicionar base de conhecimento ao Power Virtual Agent
Crie e estenda um bot [power virtual agent](https://powervirtualagents.microsoft.com/) para fornecer respostas da sua base de conhecimento.

**Neste tutorial, vai aprender a:**

<!-- green checkmark -->
> [!div class="checklist"]
> * Criar power virtual agent
> * Criar tópico de recuo do Sistema
> * Adicione o Fabricante qnA como ação ao tópico como fluxo power automate
> * Criar a solução Power Automate
> * Adicionar fluxo automate de potência para a solução
> * Publicar Power Virtual Agent
> * Test Power Virtual Agent, receba resposta da base de conhecimento do QnA Maker

## <a name="integrate-a-power-virtual-agent-with-a-knowledge-base"></a>Integrar um Agente Virtual Power com uma base de conhecimento

[O Power Virtual Agents](https://powervirtualagents.microsoft.com/) permite que as equipas criem facilmente bots poderosos usando uma interface gráfica guiada e sem código sem a necessidade de cientistas de dados ou desenvolvedores.

Um Power Virtual Agent é criado com uma série de tópicos (áreas temáticas), de forma a responder às perguntas dos utilizadores através da realização de ações. Se não for possível encontrar uma resposta, um recuo do sistema pode devolver uma resposta.

Configure o agente para enviar a questão para a sua base de conhecimento como parte da ação de um tópico ou como parte do caminho tópico do Recuo do **Sistema.** Ambos usam o mesmo mecanismo de uma ação para se ligarem à sua base de conhecimentos e devolverem uma resposta.

## <a name="power-automate-connects-to-generateanswer-action"></a>Power Automate liga-se à ação GenerateAnswer

Para ligar o seu agente à sua base de conhecimentos, utilize power automate para criar a ação. Power Automate fornece um **fluxo**de processo , que se conecta à API GenerateAnswer da QnA Maker.

Uma vez que o **fluxo** é projetado e guardado, ele está disponível a partir de uma **Solução**Power Automate .  Assim que o fluxo GenerateAnswer for adicionado a uma solução, use essa solução como uma ação no seu agente.

## <a name="process-steps-to-connect-an-agent-to-your-knowledge-base"></a>Processe passos para ligar um agente à sua base de conhecimento

Os seguintes passos são apresentados como uma visão geral para ajudá-lo a entender como os passos se relacionam com o objetivo de ligar um Agente Virtual power a uma base de conhecimento do QnA Maker.

Passos para usar um agente Power Virtual com O Fabricante QnA:
* No portal [QnA Maker](https://www.qnamaker.ai/)
    * Construir e publicar base de conhecimento
    * Copie detalhes da base de conhecimento, incluindo id base de conhecimento, chave final do ponto de execução e anfitrião do ponto final do tempo de execução.
* No portal [power virtual agent](https://powerva.microsoft.com/)
    * Construir tópico de agente
    * Chamar uma ação (para Power Automate Flow)
* No portal [Power Automate](https://us.flow.microsoft.com/)
    * Construa um fluxo com conector para [A Resposta Gerada do Fabricante qnA](https://docs.microsoft.com/connectors/cognitiveservicesqnamaker/)
        * QnA Maker publicou informações da base de conhecimento
            * ID base de conhecimento
            * Hospedeiro de fim de recurso QnA Maker
            * Chave final do ponto final do qnA Maker
        * Entrada - consulta do utilizador
        * Saída - resposta base de conhecimento
    * Criar solução e adicionar fluxo
* Regresso ao Power Virtual Agent
    * Selecione a saída da solução como mensagem para tópico

## <a name="create-and-publish-a-knowledge-base"></a>Criar e publicar uma base de dados de conhecimento

1. Siga o [início rápido](../Quickstarts/create-publish-knowledge-base.md) para criar uma base de conhecimento. Não complete a última secção para criar um bot. Este tutorial é um substituto para a última secção do quickstart porque este tutorial usa o Power Virtual Agent para criar um bot, em vez do bot Bot Framework no arranque rápido.

    > [!div class="mx-imgBorder"]
    > ![Introduza as definições de base de conhecimento publicadas encontradas nahttps://www.qnamaker.ai/) página **Settings** no portal [QnA Maker].](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

    Você precisará desta informação para o [passo Power Automate](#create-power-automate-flow-to-connect-to-your-knowledge-base) para configurar a sua ligação QnA Maker GenerateAnswer.

1. Encontre a chave de ponto final, o anfitrião do ponto final e o ID da base de conhecimento na página **Definições** no portal QnA Maker.

## <a name="create-power-virtual-agent"></a>Criar power virtual agent

1. [Inscreva-se no](https://go.microsoft.com/fwlink/?LinkId=2108000&clcid=0x409) Power Virtual Agent com a sua escola ou conta de e-mail de trabalho.
1. Se este é o seu primeiro bot, deve estar na página **inicial** do agente. Se este não for o seu primeiro Power Virtual Agent, selecione o bot da navegação superior direita e selecione **+ Novo Bot**.

    > [!div class="mx-imgBorder"]
    > ![Introduza as definições de base de conhecimento publicadas encontradas nahttps://www.qnamaker.ai/) página **Settings** no portal [QnA Maker].](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-home.png)

## <a name="several-topics-are-provided-in-the-bot"></a>Vários tópicos são fornecidos no bot

O agente usa a coleção de tópicos para responder a perguntas na sua área de assunto. Neste tutorial, o agente tem muitos tópicos previstos para si, divididos em **Tópicos** de Utilizador e **Tópicos do Sistema.**

> [!div class="mx-imgBorder"]
> ![O agente usa a coleção de tópicos para responder a perguntas na sua área de assunto. Neste tutorial, o agente tem muitos tópicos previstos para divididos em **Tópicos de Utilizador** e **Tópicos do Sistema***.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topics-provided.png)


## <a name="create-power-virtual-agents-system-fallback-topic"></a>Criar o tópico de recuo do sistema do agente virtual power

Enquanto o agente pode ligar-se à sua base de conhecimento a partir de qualquer tópico, este tutorial usa o tópico **De Recuo** do Sistema. O tópico de recuo é usado quando o agente não consegue encontrar uma resposta. O agente transmite o texto do utilizador à API GenerateAnswer da QnA Maker, recebe a resposta da sua base de conhecimentos e mostra-a de volta ao utilizador como mensagem.

1. No portal [Power Virtual Agents,](https://powerva.microsoft.com/#/) no canto superior direito da navegação, selecione a página **Definições.** O ícone desta página é o equipamento. Selecione **O Recuo do Sistema**.

    > [!div class="mx-imgBorder"]
    > ![Power Virtual agent menu item for System Fallback](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-system-fallback.png)

1. Na janela pop-up **Definições,** selecione **+ Adicione** para adicionar um tópico de recuo do sistema.

    > [!div class="mx-imgBorder"]
    > ![Na janela Definições, adicione tópico de recuo.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-settings-add-fallback-topic.png)

1. Depois de adicionado o tópico, selecione **Go to Fallback tópico** para autor do tema Fallback na tela de autoria.

    > [!TIP]
    > Se precisar de voltar ao tema Fallback, está disponível na secção **Tópicos,** como parte dos tópicos do **Sistema.**

## <a name="use-authoring-canvas-to-add-an-action"></a>Use tela de autoria para adicionar uma ação

Utilize a tela de autor de Agentes Virtuais Power para ligar o tópico de Recuo à sua base de conhecimentos. O tópico começa com o texto do **utilizador não reconhecido.** Adicione uma ação que passa esse texto ao QnA Maker e, em seguida, mostra a resposta como uma mensagem. O último passo de mostrar uma resposta é tratado como um [passo separado](#add-solutions-flow-to-power-virtual-agent) mais tarde neste tutorial.

Esta secção cria o fluxo de conversação tópico de recuo.

1. A nova ação do Fallback pode já ter elementos de fluxo de conversação. Elimine o item **Escalate** selecionando o menu Opções.

    > [!div class="mx-imgBorder"]
    > ![Inicie a ação de recuo com frases de gatilho](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-fallback-topic-delete-escalate.png)

1. Selecione o **+** conector que flui **da** caixa mensagem e, em seguida, selecione Chamar **uma ação**.

    > [!div class="mx-imgBorder"]
    > ![Chame uma ação](../media/how-to-integrate-power-virtual-agent/create-new-item-call-an-action.png)

1. Selecione **Criar um fluxo**. O processo leva-o ao **Power Automate**, um portal diferente baseado no navegador.

    > [!div class="mx-imgBorder"]
    > ![Chame uma ação](../media/how-to-integrate-power-virtual-agent/create-a-flow.png)

## <a name="create-power-automate-flow-to-connect-to-your-knowledge-base"></a>Crie power automate Flow para ligar à sua base de conhecimento

O seguinte procedimento cria um fluxo **power automate** que:
* toma o texto do utilizador que está a chegar
* envia-o para o QnA Maker
* atribui a resposta superior do Fabricante QnA a uma variável
* envia a variável (resposta superior) como a resposta de volta ao seu agente

1. Em **Power Automate,** o modelo de **fluxo** é iniciado para si. No item de fluxo de **agentes virtuais power,** selecione **Editar** para configurar a variável de entrada proveniente do agente para a sua base de conhecimentos. A variável de entrada baseada em texto é a pergunta de texto submetida pelo utilizador do seu agente.

    > [!div class="mx-imgBorder"]
    > ![Configure a sua variável de entrada como uma cadeia de texto](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable.png)

1. Adicione uma entrada de texto `InputText` e nomeie a variável com uma descrição de `IncomingUserQuestion`. Este naming ajuda a distinguir o texto de entrada do texto de saída que cria mais tarde.

    > [!div class="mx-imgBorder"]
    > ![Adicione uma entrada de texto e nomeie a variável 'InputText' com uma descrição de 'UserQuestion'](../media/how-to-integrate-power-virtual-agent/power-automate-configure-input-variable-name-and-description.png)

1. Selecione **+** o conector que flui da caixa **Power Virtual Agents,** para inserir um novo passo no fluxo (antes do valor de retorno para o Agente Virtual **power),** em seguida, selecione **Adicionar uma ação**.

1. Procure `Qna` encontrar as ações do **Fabricante qnA** e, em seguida, selecione **Generate answer**.

    > [!div class="mx-imgBorder"]
    > ![Procure 'Qna' para encontrar as ações **QnA Maker** e, em seguida, selecione **Generate resposta**](../media/how-to-integrate-power-virtual-agent/generate-answer-action-selected.png)

    As três (3) definições de ligação necessárias para o Fabricante QnA aparecem na ação e as definições de perguntas do Agente Virtual Power.

    > [!div class="mx-imgBorder"]
    > ![As definições de ligações para o Fabricante QnA aparecem na ação.](../media/how-to-integrate-power-virtual-agent/generate-answer-knowledge-base-settings.png)

1. Configure a ação com o id da base de conhecimento, anfitrião de ponto final e chave de ponto final. Estes encontram-se na página **Definições** da sua base de conhecimentos, no portal QnA Maker.

    > [!div class="mx-imgBorder"]
    > ![Introduza as definições de base de conhecimento publicadas encontradas nahttps://www.qnamaker.ai/) página **Settings** no portal [QnA Maker].](../media/how-to-integrate-power-virtual-agent/published-knowledge-base-settings.png)

1. Para configurar a **pergunta,** selecione `InputText` a caixa de texto e, em seguida, selecione a lista.

1. Para inserir um novo passo no **+** fluxo, selecione o conector que flui da caixa de ação **de resposta Generate** e, em seguida, selecione Adicionar uma **ação**.

1. Para adicionar uma variável para capturar o texto de `Initialize variable` resposta devolvido da GenerateAnswer, procure e selecione a ação.

    Desloque o `OutgoingQnAAnswer`nome da variável para, e selecione o tipo de **String**. Não desloque o **Valor.**

    > [!div class="mx-imgBorder"]
    > ![Detete o nome da variável para 'QnAAnswer', e selecione o tipo como **String**](../media/how-to-integrate-power-virtual-agent/initialize-output-variable-for-qna-answer.png)

1. Para inserir um novo passo no **+** fluxo, selecione o conector que flui da caixa de ação **variável Inicialize** e, em seguida, selecione **Adicionar uma ação**.

1. Para definir toda a base de conhecimento Resposta JSON à variável, procure e selecione a`Apply to each` ação. Selecione `answers`o GenerateAnswer .

1. Para devolver apenas a resposta superior, na mesma **Aplicar a cada** caixa, selecione Adicionar uma **ação**. Procure e selecione **variável set**.

    Na caixa **variável set,** selecione a caixa de texto para **Nome**e, em seguida, selecione **OutgoingQnAAnswer** da lista.

    Selecione a caixa de texto para **Valor**e, em seguida, selecione **Resposta resposta** resposta da lista.

    > [!div class="mx-imgBorder"]
    > ![Detete o nome e o valor para a variável](../media/how-to-integrate-power-virtual-agent/power-automate-flow-apply-to-each-set-variable.png)

1. Para devolver a variável (e o seu valor), selecione o **valor de Retorno(s) para o** produto de fluxo do Agente Virtual power e, em seguida, selecione **Editar,** em seguida, **Adicionar uma saída**. Selecione um tipo de `FinalAnswer`saída de **texto** e introduza o **título** de . Selecione a **Value**caixa de texto `OutgoingQnAAnswer` para o Valor e, em seguida, selecione a variável.

    > [!div class="mx-imgBorder"]
    > ![Definir o valor de retorno](../media/how-to-integrate-power-virtual-agent/power-automate-flow-return-value.png)

1. Selecione **Guardar** para salvar o Fluxo.

## <a name="create-solution-and-add-flow"></a>Criar solução e adicionar fluxo

Para que o Agente Virtual power encontre e se conectem ao fluxo, o fluxo deve ser incluído numa Solução power automate.

1. Ainda no portal Power Automate, selecione **Soluções** a partir da navegação do lado esquerdo.

1. Selecione **+ Nova solução**.

1. Introduza um nome a apresentar. A lista de soluções inclui todas as soluções da sua organização ou escola. Escolha uma convenção de nomeação que o ajude a filtrar apenas `jondoe-power-virtual-agent-qnamaker-fallback`as suas soluções, como pré-fixação do seu e-mail para o seu nome de solução: .

1. Selecione o seu editor na lista de escolhas.

1. Aceite os valores predefinidos para o nome e versão.

1. Selecione **Criar** para terminar o processo.

## <a name="add-flow-to-solution"></a>Adicionar fluxo para a solução

1. Na lista de soluções, selecione a solução que acabou de criar. Deve estar no topo da lista. Caso não seja, procure pelo seu nome de e-mail, que faz parte do nome da solução.

1. Na solução, selecione **+ Adicione o existente**e, em seguida, selecione **Flow** da lista.

1. Encontre o seu fluxo e, em seguida, selecione **Adicionar** para terminar o processo. Se houver muitos fluxos, olhe para a coluna **Modificada** para encontrar o fluxo mais recente.

## <a name="add-solutions-flow-to-power-virtual-agent"></a>Adicione o fluxo da solução para power virtual agent

1. Volte ao separador do navegador com o seu Agente Virtual Power. A tela de autoria ainda deve estar aberta.

1. Selecione o **+** conector sob a caixa de ação **Mensagem** para inserir um novo passo no fluxo e, em seguida, selecione Chamar **uma ação**.

1. Na nova ação, selecione o valor de entrada de **UnrecognizedTriggerPhrase**. Isto passa o texto do agente para o fluxo.

    > [!div class="mx-imgBorder"]
    > ![Na nova ação, selecione o valor de entrada de **UnrecognizedTriggerPhrase**.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-select-unrecognized-trigger-phrase.png)

1. Selecione o **+** conector sob a caixa **de ação** para inserir um novo passo no fluxo e, em seguida, selecione Mostrar uma **mensagem**.

1. Introduza o `Your answer is:`texto da `FinalAnswer` mensagem e selecione como uma variável de contexto utilizando a função da barra de ferramentas no local.

    > [!div class="mx-imgBorder"]
    > ![Introduza o texto da mensagem e a 'Resposta Final' do Power Automate Flow.](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-show-message-final-answer.png)

1. Selecione **Guardar** a partir da barra de ferramentas de contexto para guardar os detalhes da tela de autor para o tópico.

A tela final é mostrada abaixo.

> [!div class="mx-imgBorder"]
> ![Tela de agente final](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-topic-authoring-canvas-full-flow.png)

## <a name="test-power-virtual-agent"></a>Teste de poder de agente virtual

1. No painel de teste, **alternar faixa entre tópicos**. Isto permite-lhe assistir à progressão entre tópicos e num único tópico.

1. Teste o agente inserindo o texto do utilizador na ordem fornecida abaixo. A tela de autoria relata os passos bem sucedidos com uma marca de verificação verde.

|Ordem de perguntas|Perguntas de teste|Objetivo|
|--|--|--|
|1|Hello|Iniciar conversa|
|2|Horário de loja|Tópico da amostra - configurado para si sem qualquer trabalho adicional da sua parte.|
|3|Sim|Em resposta a`Did that answer your question?`|
|4|Excelente|Em resposta a`Please rate your experience.`|
|5|Sim|Em resposta a`Can I help with anything else?`|
|6|O que é uma base de conhecimento?|Esta pergunta desencadeia a ação de recuo, que envia o texto para a sua base de conhecimentos para responder, então a resposta é exibida. |

> [!div class="mx-imgBorder"]
> ![Tela de agente final](../media/how-to-integrate-power-virtual-agent/power-virtual-agent-test-tracked.png)

## <a name="publish-your-bot"></a>Publique o seu bot

Para disponibilizar o agente a todos os membros da sua escola ou organização, tem de publicá-lo.

1. **Selecione Publicar** a partir da navegação à esquerda e, em seguida, selecione **Publicar** na página.

1. Experimente o seu bot no site da demonstração, fornecido como um link abaixo do botão **Publicar** .

    Uma nova página web abre com o seu bot. Faça ao bot a mesma pergunta de teste:`What is a knowledge base?`

    > [!div class="mx-imgBorder"]
    > ![Tela de agente final](../media/how-to-integrate-power-virtual-agent/demo-chat-bot.png)

## <a name="share-your-bot"></a>Partilhe o seu bot

Para partilhar o site da demonstração, configure-o como um canal.

1. Selecione **Gerir** **canais** e depois a partir da navegação à esquerda.

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