---
title: Conectar-se ao OneDrive
description: Carregar e gerenciar arquivos com as APIs REST do OneDrive e os aplicativos lógicos do Azure
services: logic-apps
ms.suite: integration
ms.reviewer: klam, logicappspm
ms.topic: article
ms.date: 10/18/2016
tags: connectors
ms.openlocfilehash: 93528e257ab45644a79c58fbf600dca10317eb0b
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/03/2019
ms.locfileid: "74789674"
---
# <a name="get-started-with-the-onedrive-connector"></a>Introdução ao conector do OneDrive

Conecte-se ao OneDrive para gerenciar seus arquivos, incluindo carregar, obter, excluir arquivos e muito mais. Com o OneDrive, você pode executar estas tarefas:

* Crie seu fluxo de trabalho armazenando arquivos no OneDrive ou atualize os arquivos existentes no OneDrive. 
* Use gatilhos para iniciar o fluxo de trabalho quando um arquivo for criado ou atualizado no OneDrive.
* Use ações para criar um arquivo, excluir um arquivo e muito mais. Por exemplo, quando um novo email do Office 365 é recebido com um anexo (um gatilho), crie um novo arquivo no OneDrive (uma ação).

Este artigo mostra como usar o conector do OneDrive em um aplicativo lógico e também lista os gatilhos e as ações.

Para saber mais sobre os aplicativos lógicos, consulte [o que são aplicativos lógicos](../logic-apps/logic-apps-overview.md) e [criar um aplicativo lógico](../logic-apps/quickstart-create-first-logic-app-workflow.md).

## <a name="connect-to-onedrive"></a>Conectar-se ao OneDrive

Antes que seu aplicativo lógico possa acessar qualquer serviço, você primeiro cria uma *conexão* com o serviço. Uma conexão fornece conectividade entre um aplicativo lógico e outro serviço. Por exemplo, para se conectar ao OneDrive, você precisa primeiro de uma *conexão*do onedrive. Para criar uma conexão, insira as credenciais que você normalmente usa para acessar o serviço ao qual você deseja se conectar. Portanto, com o OneDrive, insira as credenciais para sua conta do OneDrive para criar a conexão.

### <a name="create-the-connection"></a>Criar a ligação

[!INCLUDE [Steps to create a connection to OneDrive](../../includes/connectors-create-api-onedrive.md)]

## <a name="use-a-trigger"></a>Usar um gatilho

Um gatilho é um evento que pode ser usado para iniciar o fluxo de trabalho definido em um aplicativo lógico. Dispara a "sondagem" do serviço em um intervalo e uma frequência que você deseja. [Saiba mais sobre gatilhos](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. No aplicativo lógico, digite "onedrive" para obter uma lista dos gatilhos:  

   ![](./media/connectors-create-api-onedrive/onedrive-1.png)

2. Selecione **quando um arquivo é modificado**. Se já existir uma conexão, selecione o botão Mostrar seletor para selecionar uma pasta.

   ![](./media/connectors-create-api-onedrive/sample-folder.png)

   Se você for solicitado a entrar, insira os detalhes de entrada para criar a conexão. [Criar a conexão](connectors-create-api-onedrive.md#create-the-connection) neste artigo lista as etapas.

   Neste exemplo, o aplicativo lógico é executado quando um arquivo na pasta escolhida é atualizado. Para ver os resultados desse gatilho, adicione outra ação que lhe envie um email. Por exemplo, adicione a ação *enviar um email* do Outlook do Office 365 que envia emails para você quando um arquivo é atualizado.

3. Selecione o botão **Editar** e defina os valores de **frequência** e **intervalo** . Por exemplo, se você quiser que o gatilho seja sondado a cada 15 minutos, defina a **frequência** como **minuto**e defina o **intervalo** como **15**. 

   ![](./media/connectors-create-api-onedrive/trigger-properties.png)

4. **Salve** suas alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="use-an-action"></a>Usar uma ação

Uma ação é uma operação executada pelo fluxo de trabalho definido em um aplicativo lógico. [Saiba mais sobre ações](../logic-apps/logic-apps-overview.md#logic-app-concepts).

1. Selecione o sinal de adição. Você verá várias opções: **Adicionar uma ação**, **Adicionar uma condição**ou uma das **mais** opções.

   ![](./media/connectors-create-api-onedrive/add-action.png)

2. Escolha **Adicionar uma ação**.

3. Na caixa de texto, digite "onedrive" para obter uma lista de todas as ações disponíveis.

   ![](./media/connectors-create-api-onedrive/onedrive-actions.png) 

4. Em nosso exemplo, escolha **onedrive – criar arquivo**. Se já existir uma conexão, selecione o **caminho da pasta** para colocar o arquivo, insira o **nome do arquivo**e escolha o **conteúdo do arquivo** desejado:  

   ![](./media/connectors-create-api-onedrive/sample-action.png)

   Se as informações de conexão forem solicitadas, insira os detalhes para [criar a conexão, conforme descrito](#create-the-connection) neste tópico.

   Neste exemplo, você cria um novo arquivo em uma pasta do OneDrive. Você pode usar a saída de outro gatilho para criar o arquivo do OneDrive. Por exemplo, adicione o Outlook do Office 365 *quando um novo email chegar ao* gatilho. Em seguida, adicione a ação *criar arquivo* do onedrive que usa os campos anexos e tipo de conteúdo em um foreach para criar o novo arquivo no onedrive.

   ![](./media/connectors-create-api-onedrive/foreach-action.png)

5. **Salve** suas alterações (canto superior esquerdo da barra de ferramentas). Seu aplicativo lógico é salvo e pode ser habilitado automaticamente.

## <a name="connector-specific-details"></a>Detalhes específicos do conector

Exiba todos os gatilhos e ações definidos no Swagger e também veja os limites nos [detalhes do conector](/connectors/onedriveconnector/).

## <a name="next-steps"></a>Passos seguintes

* [Conectores para Azure Logic Apps](apis-list.md)