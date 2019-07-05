---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 4f3d31fa25ea5781bc3af7297719b77723597e17
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/28/2019
ms.locfileid: "67444611"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

O Visual Studio Code permite-lhe publicar o projeto de funções diretamente no Azure. No processo, vai criar uma aplicação de funções e recursos relacionados na sua subscrição do Azure. A aplicação de funções proporciona um contexto de execução para as suas funções. O projeto é empacotado e implementado na nova aplicação de funções na sua subscrição do Azure.

Por predefinição, o Visual Studio cria todos os recursos do Azure necessários para criar a sua aplicação de função. Os nomes destes recursos baseiam-se o nome de aplicação de função que escolher. Se precisar de ter controlo total sobre os recursos criados, em vez disso, pode [publicar utilizando opções avançadas](../articles/azure-functions/functions-develop-vs-code.md#enabled-publishing-with-advanced-create-options).

Esta secção assume que está a criar uma nova aplicação de função no Azure.

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.

1. No Visual Studio Code, prima F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Deploy to function app...`.

1. Se não sessão iniciada, deverá **iniciar sessão no Azure**. Também pode **criar uma conta gratuita do Azure**. Depois de concluída com êxito de início de sessão do navegador, volte ao Visual Studio Code. 

1. Se tiver várias subscrições, **Selecione uma subscrição** para a aplicação de função, em seguida, escolha **+ criar nova aplicação de funções no Azure**.

1. Escreva um nome globalmente exclusivo que identifique a sua aplicação de funções e prima Enter. Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`.

    Quando pressiona Enter, os seguintes recursos do Azure são criados na sua subscrição:

    * **[Grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md)** : Contém todos os recursos do Azure criados. O nome é baseado no nome da aplicação de função.
    * **[Conta de armazenamento](../articles/storage/common/storage-quickstart-create-account.md)** : É criada uma conta de armazenamento standard com um nome exclusivo com base no nome da aplicação de função.
    * **[Plano de alojamento](../articles/azure-functions/functions-scale.md)** : Plano de consumo é criado na região E.U.A. oeste para alojar a sua aplicação de função sem servidor.
    * **Aplicação de funções**: Seu projeto será implementado e é executado nesta nova aplicação de função.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **vista de saída** na notificação para ver a criação e os resultados de implementação, incluindo os recursos do Azure que criou.

1. De volta o **Azure: As funções** área, expanda a nova aplicação de função na sua subscrição. Expanda **funções**, clique com botão direito **HttpTrigger**e, em seguida, escolha **copiar o URL da função**.

    ![Copie o URL de função para o novo acionador HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
