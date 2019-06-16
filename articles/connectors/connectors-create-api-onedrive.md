---
title: Ligar ao OneDrive - Azure Logic Apps | Documentos da Microsoft
description: Carregar e gerir ficheiros com as APIs REST do OneDrive e o Azure Logic Apps
author: ecfan
manager: jeconnoc
ms.author: estfan
ms.date: 10/18/2016
ms.topic: article
ms.service: logic-apps
services: logic-apps
ms.reviewer: klam, LADocs
ms.suite: integration
tags: connectors
ms.openlocfilehash: 840a010f8606387a250552d884621a96d0031f90
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "62106232"
---
# <a name="get-started-with-the-onedrive-connector"></a>Começar com o conector do OneDrive
Ligar ao OneDrive para gerir os seus ficheiros, incluindo o carregamento, obter, eliminar ficheiros e muito mais. 

Com o OneDrive,: 

* Criar o seu fluxo de trabalho ao armazenamento de ficheiros no OneDrive ou atualizar os ficheiros existentes no OneDrive. 
* Utilize os acionadores para iniciar o seu fluxo de trabalho quando um ficheiro é criado ou atualizado no seu OneDrive.
* Utilize ações para criar um ficheiro, eliminar um ficheiro e muito mais. Por exemplo, quando um novo e-mail do Office 365 é recebido com um anexo (um acionador), crie um novo ficheiro no OneDrive (uma ação).

Este artigo mostra-lhe como utilizar o conector do OneDrive numa aplicação lógica e também lista os acionadores e ações.

Para saber mais sobre o Logic Apps, veja [o que são as aplicações lógicas](../logic-apps/logic-apps-overview.md) e [criar uma aplicação lógica](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Ligar ao OneDrive
Antes da aplicação lógica pode aceder a qualquer serviço, primeiro tem de criar uma *ligação* ao serviço. Uma ligação fornece conectividade entre uma aplicação lógica e outro serviço. Por exemplo, para ligar ao OneDrive, primeiro tem de um OneDrive *ligação*. Para criar uma ligação, introduza as credenciais que normalmente utiliza para aceder ao serviço que deseja se conectar. Então, com o OneDrive, introduza as credenciais para a sua conta do OneDrive para criar a ligação.

### <a name="create-the-connection"></a>Criar a ligação
> [!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]
> 
> 

## <a name="use-a-trigger"></a>Utilizar um acionador
Um acionador é um evento que pode ser utilizado para iniciar o fluxo de trabalho definido numa aplicação lógica. Acionadores "consultam" o serviço num intervalo e a frequência que pretenda. [Saiba mais sobre os acionadores](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Na aplicação lógica, escreva "onedrive" para obter uma lista dos acionadores:  
   
    ![](./media/connectors-create-api-onedrive/onedrive-1.png)
2. Selecione **quando um ficheiro é modificado**. Se já existir uma ligação, em seguida, selecione o botão de mostrar Seletor para selecionar uma pasta.
   
    ![](./media/connectors-create-api-onedrive/sample-folder.png)
   
    Se lhe for pedido para iniciar sessão, em seguida, introduza o início de sessão nos detalhes para criar a ligação. [Criar a ligação](connectors-create-api-onedrive.md#create-the-connection) neste artigo lista os passos. 
   
   > [!NOTE]
   > Neste exemplo, a aplicação lógica executa quando um ficheiro na pasta escolhida é atualizado. Para ver os resultados deste acionador, adicione outra ação que envia um e-mail. Por exemplo, adicionar o Office 365 Outlook *enviar um e-mail* ação que envia um e-mail quando um ficheiro é atualizado. 

3. Selecione o **edite** botão e defina a **frequência** e **intervalo** valores. Por exemplo, se pretender que o acionador para consultar a cada 15 minutos, em seguida, defina o **frequência** ao **minuto**e defina o **intervalo** para **15**. 
   
    ![](./media/connectors-create-api-onedrive/trigger-properties.png)
4. **Guardar** as alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.

## <a name="use-an-action"></a>Utilize uma ação
Uma ação é uma operação levada a cabo pelo fluxo de trabalho definido numa aplicação lógica. [Saiba mais sobre as ações](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecione o sinal. Verá várias opções: **Adicionar uma ação**, **adicionar uma condição**, ou uma da **mais** opções.
   
    ![](./media/connectors-create-api-onedrive/add-action.png)
2. Escolher **adicionar uma ação**.
3. Na caixa de texto, digite "onedrive" para obter uma lista de todas as ações disponíveis.
   
    ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 
4. No nosso exemplo, escolha **OneDrive - criar ficheiro**. Se já existir uma ligação, em seguida, selecione o **caminho da pasta** para colocar o ficheiro, introduza o **nome do ficheiro**e selecione o **conteúdo do ficheiro** pretende:  
   
    ![](./media/connectors-create-api-onedrive/sample-action.png)
   
    Se lhe for pedido para obter as informações de ligação, em seguida, introduza os detalhes para criar a ligação. [Criar a ligação](connectors-create-api-onedrive.md#create-the-connection) este artigo descreve essas propriedades. 
   
   > [!NOTE]
   > Neste exemplo, vamos criar um novo ficheiro numa pasta do OneDrive. Pode utilizar a saída de outro acionador para criar o ficheiro do OneDrive. Por exemplo, adicionar o Office 365 Outlook *quando chega um novo e-mail* acionador. Em seguida, adicione o OneDrive *criar ficheiro* ação que usa os anexos e o tipo de conteúdo campos dentro de um ForEach para criar o novo ficheiro no OneDrive. 
   > 
   > ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Guardar** as alterações (canto superior esquerdo da barra de ferramentas). A aplicação lógica é guardada e pode ser ativada automaticamente.


## <a name="connector-specific-details"></a>Detalhes específicos do conector

Ver os acionadores e as ações definidas no swagger e também ver quaisquer limites na [detalhes do conector](/connectors/onedriveconnector/).

## <a name="more-connectors"></a>Mais conectores
Volte para o [lista APIs](apis-list.md).