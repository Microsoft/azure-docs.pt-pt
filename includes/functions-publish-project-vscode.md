---
title: incluir ficheiro
description: incluir ficheiro
services: functions
author: ggailey777
manager: jeconnoc
ms.service: azure-functions
ms.topic: include
ms.date: 04/16/2019
ms.author: glenga
ms.custom: include file
ms.openlocfilehash: 66c100ecd40e9f0899f7686397339ab849315c3e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76021059"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

O Visual Studio Code permite-lhe publicar o projeto de funções diretamente no Azure. No processo, vai criar uma aplicação de funções e recursos relacionados na sua subscrição do Azure. A aplicação de funções proporciona um contexto de execução para as suas funções. O projeto é empacotado e implementado na nova aplicação de funções na sua subscrição do Azure.

Por padrão, Visual Studio Code cria todos os recursos do Azure necessários para criar seu aplicativo de funções. Os nomes desses recursos se baseiam no nome do aplicativo de funções que você escolher. Se precisar ter controle total dos recursos criados, você poderá [publicar usando opções avançadas](../articles/azure-functions/functions-develop-vs-code.md#enable-publishing-with-advanced-create-options).

Esta seção pressupõe que você está criando um novo aplicativo de funções no Azure.

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.

1. Em Visual Studio Code, pressione F1 para abrir a paleta de comandos. Na paleta de comandos, procure e selecione `Azure Functions: Deploy to function app...`.

1. Se não estiver conectado, será solicitado que você entre no **Azure**. Você também pode **criar uma conta gratuita do Azure**. Depois de entrar com êxito no navegador, volte para Visual Studio Code. 

1. Se você tiver várias assinaturas, **Selecione uma assinatura** para o aplicativo de funções e, em seguida, escolha **+ criar novo aplicativo de funções no Azure**.

1. Escreva um nome globalmente exclusivo que identifique a sua aplicação de funções e prima Enter. Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`.

    Quando você pressiona ENTER, os seguintes recursos do Azure são criados em sua assinatura:

    * **[Grupo de recursos](../articles/azure-resource-manager/management/overview.md)** : contém todos os recursos do Azure criados. O nome é baseado no nome do aplicativo de funções.
    * **[Conta de armazenamento](../articles/storage/common/storage-account-create.md)** : uma conta de armazenamento padrão é criada com um nome exclusivo com base no nome do aplicativo de funções.
    * **[Plano de hospedagem](../articles/azure-functions/functions-scale.md)** : um plano de consumo é criado na região oeste dos EUA para hospedar seu aplicativo de funções sem servidor.
    * **Aplicativo de funções**: seu projeto é implantado e executado nesse novo aplicativo de funções.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **Exibir saída** nesta notificação para exibir os resultados de criação e implantação, incluindo os recursos do Azure que você criou.

1. De volta à área **Azure: Functions** , expanda o novo aplicativo de funções em sua assinatura. Expanda **funções**, clique com o botão direito do mouse em **HttpTrigger**e escolha **Copiar URL da função**.

    ![Copiar a URL da função para o novo gatilho HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
