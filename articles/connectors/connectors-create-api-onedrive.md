---
title: Aceda e gere ficheiros no Microsoft OneDrive
description: Faça upload e gestão de ficheiros no OneDrive criando fluxos de trabalho automatizados em Azure Logic Apps
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 9fe4988b5499943f4b11ec5d640209ceb68e84ec
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87040252"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Aceda e gere ficheiros no conector OneDrive utilizando apps Azure Logic

Ao utilizar [as Apps Azure Logic](../logic-apps/logic-apps-overview.md) e o [conector OneDrive,](/connectors/onedriveconnector/)pode criar tarefas e fluxos de trabalho automatizados para gerir os seus ficheiros, incluindo upload, get, apagar ficheiros e muito mais. Com o OneDrive, pode executar estas tarefas:

* Construa o seu fluxo de trabalho armazenando ficheiros no OneDrive ou atualize ficheiros existentes no OneDrive. 
* Utilize gatilhos para iniciar o seu fluxo de trabalho quando um ficheiro for criado ou atualizado dentro do seu OneDrive.
* Use ações para criar um ficheiro, eliminar um ficheiro e muito mais. Por exemplo, quando um novo e-mail do Office 365 é recebido com um anexo (um gatilho), crie um novo ficheiro no OneDrive (uma ação).

Este artigo mostra-lhe como usar o conector OneDrive numa aplicação lógica, e também lista os gatilhos e ações.

Para saber mais sobre as Aplicações Lógicas, consulte [o que são aplicações lógicas](../logic-apps/logic-apps-overview.md) e [crie uma aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="connect-to-onedrive"></a>Ligar ao OneDrive

Antes de a sua aplicação lógica poder aceder a qualquer serviço, cria primeiro uma *ligação* ao serviço. Uma ligação proporciona conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar ao OneDrive, primeiro precisa de uma *ligação* OneDrive . Para criar uma ligação, insira as credenciais que normalmente utiliza para aceder ao serviço a que pretende ligar. Assim, com o OneDrive, insira as credenciais na sua conta OneDrive para criar a ligação.

### <a name="create-the-connection"></a>Criar a ligação

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Use um gatilho

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido numa aplicação lógica. Dispara "poll" o serviço num intervalo e frequência que deseja. [Saiba mais sobre os gatilhos.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. No Logic App Designer, `onedrive` escreva para obter uma lista dos gatilhos:  

   ![Uma caixa de diálogo intitulada "Show Microsoft managed A P I's" tem uma caixa que contém "onedrive". Abaixo está uma lista de quatro gatilhos. A primeira delas é "OneDrive - Quando um ficheiro é criado". O segundo, "OneDrive - Quando um ficheiro é modificado", foi selecionado.](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selecione **Quando um ficheiro for modificado**. Se já existir uma ligação, selecione o botão 'Show Picker' para selecionar uma pasta.

   ![Uma caixa de diálogo intitulada "Quando um ficheiro é modificado" tem uma caixa intitulada "FOLDER" com um botão de navegação associado.](./media/connectors-create-api-onedrive/sample-folder.png)

   Se for solicitado que faça o seu sessão, insira o sinal em detalhes para criar a ligação. [Crie a ligação](connectors-create-api-onedrive.md#create-the-connection) neste artigo que lista os passos.

   Neste exemplo, a aplicação lógica é executado quando um ficheiro na pasta que escolher é atualizado. Para ver os resultados deste gatilho, adicione outra ação que lhe envia um e-mail. Por exemplo, adicionar o Outlook Office 365 *Enviar por email o* artigo Enviar por email o artigo Um ficheiro.

3. Selecione o botão **Editar** e desa um modo de definir os valores **de Frequência** e **Intervalo.** Por exemplo, se quiser que o gatilho faça sondagens a cada 15 minutos, em seguida, desaccione a **Frequência** ao **Minuto**, e desaccione o **Intervalo** para **15**. 

   ![Uma caixa de diálogo intitulada "Quando um ficheiro é modificado" mostra cinco caixas rotuladas: "FOLDER", "FREQUÊNCIA", "INTERVALO", "TIMEZONE" e "START TIME". Existem listas de espera para os campos "FREQUÊNCIA" e "FUSo HORÁRIO".](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Guarde** as alterações (canto superior esquerdo da barra de ferramentas). A sua aplicação lógica é guardada e pode ser automaticamente ativada.

## <a name="use-an-action"></a>Use uma ação

Uma ação é uma operação realizada pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre ações.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. Selecione o sinal de mais. Você vê várias escolhas: **Adicionar uma ação**, **Adicionar uma condição**, ou uma das opções **Mais.**

   ![Uma imagem mostra quatro botões: "+ Novo Passo", "Adicionar uma ação", "Adicionar uma condição", e "... Mais".](./media/connectors-create-api-onedrive/add-action.png)

2. Escolha **Adicionar uma ação**.

3. Na caixa de pesquisa, `onedrive` escreva para obter uma lista de todas as ações disponíveis.

   ![Uma caixa de diálogo intitulada "Show Microsoft managed A P I's" tem uma caixa que contém "onedrive". Abaixo está uma lista de oito ações. O primeiro é "OneDrive - Criar ficheiro", e é selecionado.](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. No nosso exemplo, escolha **OneDrive - Criar ficheiro**. Se já existir uma ligação, então selecione o Caminho da **Pasta** para colocar o ficheiro, introduza o **Nome do Ficheiro** e escolha o Conteúdo de **Ficheiro** que pretende:  

   ![Uma caixa de diálogo intitulada "Criar ficheiro" mostra três caixas com a etiqueta "FOLDER PATH", "FILE NAME" e "FOLDER CONTENT". Existe um botão de navegação de diretório ao lado da caixa "FOLDER PATH".](./media/connectors-create-api-onedrive/sample-action.png)

   Se for solicitado para obter as informações de ligação, insira os detalhes para [criar a ligação tal como descrito](#create-the-connection) neste tópico.

   Neste exemplo, cria-se um novo ficheiro numa pasta OneDrive. Pode utilizar a saída de outro gatilho para criar o ficheiro OneDrive. Por exemplo, adicione o Outlook do Office 365 *Quando um novo e-mail chegar.* Em seguida, adicione a ação de ficheiro OneDrive *Create* que utiliza os campos de anexos e de tipo de conteúdo dentro de um ForEach para criar o novo ficheiro no OneDrive.

   ![Uma caixa de diálogo intitulada "Para cada um" tem uma caixa com a etiqueta "SELECT AN OUTPUT FROM PREVIOUS STEPS" que contém "Anexos". Existe uma caixa de diálogo "Criar ficheiro" que cobre o restante da caixa "Para cada", com caixas com a etiqueta "CAMINHO DA PASTA", "NOME DE FICHEIRO" e "CONTEÚDO DE FICHEIRO". ](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Guarde** as alterações (canto superior esquerdo da barra de ferramentas). A sua aplicação lógica é guardada e pode ser automaticamente ativada.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver quaisquer gatilhos e ações definidos no swagger, e também ver quaisquer limites nos detalhes do [conector](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Passos seguintes

* [Conectores do Azure Logic Apps](apis-list.md)