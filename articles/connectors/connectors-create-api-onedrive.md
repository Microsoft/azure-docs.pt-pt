---
title: Aceder e gerir ficheiros no Microsoft OneDrive
description: Upload e gestão de ficheiros no OneDrive criando fluxos de trabalho automatizados em Aplicações lógicas do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: edfbf090c3409d583cda6fd2c9957c37be5dfb7a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75378437"
---
# <a name="access-and-manage-files-in-onedrive-connector-by-using-azure-logic-apps"></a>Aceda e gerencie ficheiros no conector OneDrive utilizando aplicações da Lógica Azure

Ao utilizar [aplicações da Lógica Azure](../logic-apps/logic-apps-overview.md) e o [conector OneDrive,](/connectors/onedriveconnector/)pode criar tarefas automatizadas e fluxos de trabalho para gerir os seus ficheiros, incluindo upload, obter, eliminar ficheiros e muito mais. Com o OneDrive, pode executar estas tarefas:

* Construa o seu fluxo de trabalho armazenando ficheiros no OneDrive ou atualize os ficheiros existentes no OneDrive. 
* Utilize gatilhos para iniciar o seu fluxo de trabalho quando um ficheiro é criado ou atualizado dentro do seu OneDrive.
* Use ações para criar um ficheiro, eliminar um ficheiro e muito mais. Por exemplo, quando um novo e-mail do Office 365 é recebido com um anexo (um gatilho), crie um novo ficheiro no OneDrive (uma ação).

Este artigo mostra-lhe como usar o conector OneDrive numa aplicação lógica, e também lista os gatilhos e ações.

Para saber mais sobre Apps Lógicas, veja [o que são aplicações lógicas](../logic-apps/logic-apps-overview.md) e [criar uma aplicação lógica.](../logic-apps/quickstart-create-first-logic-app-workflow.md)

## <a name="connect-to-onedrive"></a>Ligar ao OneDrive

Antes que a sua aplicação lógica possa aceder a qualquer serviço, cria primeiro uma *ligação* ao serviço. Uma ligação proporciona conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar ao OneDrive, primeiro precisa de uma *ligação*OneDrive . Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço a que pretende ligar. Assim, com o OneDrive, introduza as credenciais na sua conta OneDrive para criar a ligação.

### <a name="create-the-connection"></a>Criar a ligação

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Use um gatilho

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido numa aplicação lógica. Aciona a "sondagem" do serviço num intervalo e frequência que pretende. [Saiba mais sobre os gatilhos.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. No Logic App Designer, escreva `onedrive` para obter uma lista dos gatilhos:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selecione **Quando um ficheiro for modificado**. Se já existir uma ligação, selecione o botão Show Picker para selecionar uma pasta.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Se for solicitado a iniciar sessão, insira o sinal em detalhes para criar a ligação. [Criar a ligação](connectors-create-api-onedrive.md#create-the-connection) neste artigo lista os passos.

   Neste exemplo, a aplicação lógica funciona quando um ficheiro na pasta que escolher é atualizado. Para ver os resultados deste gatilho, adicione outra ação que lhe envie um e-mail. Por exemplo, adicione o Office 365 Outlook Enviar uma ação *de e-mail* que lhe envia um e-mail quando um ficheiro é atualizado.

3. Selecione o botão **Editar** e desloque os valores **de Frequência** e **Intervalo.** Por exemplo, se quiser que o gatilho faça uma sondagem a cada 15 minutos, em seguida, defino a **Frequência** para **o Minuto**, e desloque o **intervalo** para **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Guarde** as suas alterações (canto superior esquerdo da barra de ferramentas). A sua aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="use-an-action"></a>Use uma ação

Uma ação é uma operação realizada pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre ações.](../logic-apps/logic-apps-overview.md#logic-app-concepts)

1. Selecione o sinal de mais. Você vê várias escolhas: **Adicionar uma ação**, **Adicionar uma condição**, ou uma das opções **Mais.**

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Escolha **Adicionar uma ação**.

3. Na caixa de `onedrive` pesquisa, escreva para obter uma lista de todas as ações disponíveis.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. No nosso exemplo, escolha **O OneDrive - Criar ficheiros**. Se já existir uma ligação, selecione o Caminho da **Pasta** para colocar o ficheiro, introduza o Nome do **Ficheiro**e escolha o Conteúdo de **Ficheiro** que deseja:  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Se for solicitado para obter as informações de ligação, introduza os detalhes para [criar a ligação conforme descrito](#create-the-connection) neste tópico.

   Neste exemplo, cria-se um novo ficheiro numa pasta OneDrive. Pode utilizar a saída de outro gatilho para criar o ficheiro OneDrive. Por exemplo, adicione o Office 365 Outlook *Quando um novo e-mail chegar.* Em seguida, adicione a ação de ficheiro OneDrive *Create* que utiliza os campos de Anexos e Tipos de Conteúdo dentro de um ForEach para criar o novo ficheiro no OneDrive.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Guarde** as suas alterações (canto superior esquerdo da barra de ferramentas). A sua aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Veja quaisquer gatilhos e ações definidas no swagger, e consulte também quaisquer limites nos detalhes do [conector](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Passos seguintes

* [Conectores para Azure Logic Apps](apis-list.md)