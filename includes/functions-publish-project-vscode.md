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
ms.openlocfilehash: ec0425ff2188ecf1816d5f5841394c8e32f301d2
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2019
ms.locfileid: "64744482"
---
## <a name="publish-the-project-to-azure"></a>Publicar o projeto no Azure

O Visual Studio Code permite-lhe publicar o projeto de funções diretamente no Azure. No processo, vai criar uma aplicação de funções e recursos relacionados na sua subscrição do Azure. A aplicação de funções proporciona um contexto de execução para as suas funções. O projeto é empacotado e implementado na nova aplicação de funções na sua subscrição do Azure.

Este artigo pressupõe que está a criar uma nova aplicação de funções. 

> [!IMPORTANT]
> A publicação de uma aplicação de funções existente substitui o conteúdo dessa aplicação no Azure.

1. Na **Azure: As funções** área, selecione a implementar no ícone de aplicação de funções.

    ![Definições da Aplicação de funções](./media/functions-publish-project-vscode/function-app-publish-project.png)

1. Se não sessão iniciada, deverá **iniciar sessão no Azure**. Também pode **criar uma conta gratuita do Azure**. Depois de concluída com êxito de início de sessão do navegador, volte ao Visual Studio Code. 

1. Se tiver várias subscrições, **Selecione uma subscrição** para a aplicação de função, em seguida, escolha **+ criar nova aplicação de funções no Azure**.

1. Escreva um nome globalmente exclusivo que identifique a sua aplicação de funções e prima Enter. Os carateres válidos para um nome de aplicação de funções são `a-z`, `0-9` e `-`.

1. Escolha **+ Criar Novo Grupo de Recursos**, escreva um nome de grupo de recursos, como `myResourceGroup`, e prima enter. Também pode utilizar um grupo de recursos existente.

1. Escolher **+ criar nova conta de armazenamento**, tipo de um nome globalmente exclusivo da nova conta de armazenamento utilizado pela sua aplicação de funções e prima Enter. Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas. Também pode utilizar uma conta já existente.

1. Escolha uma localização numa [região](https://azure.microsoft.com/regions/) perto de si ou de outros serviços aos quais as suas funções acedem.

    Quando pressiona Enter, os seguintes recursos do Azure são criados na sua subscrição:

    * **[Grupo de recursos](../articles/azure-resource-manager/resource-group-overview.md)**: Contém todos os recursos do Azure criados. O nome é baseado no nome da aplicação de função.
    * **[Conta de armazenamento](../articles/storage/common/storage-quickstart-create-account.md)**: É criada uma conta de armazenamento standard com um nome exclusivo com base no nome da aplicação de função.
    * **[Plano de alojamento](../articles/azure-functions/functions-scale.md)**: Plano de consumo é criado na região E.U.A. oeste para alojar a sua aplicação de função sem servidor.
    * **Aplicação de funções**: Seu projeto será implementado e é executado nesta nova aplicação de função.

    Depois de criar a aplicação de funções, é apresentada uma notificação e o pacote de implementação é aplicado. Selecione **vista de saída** na notificação para ver a criação e os resultados de implementação, incluindo os recursos do Azure que criou.

1. De volta o **Azure: As funções** área, expanda a nova aplicação de função na sua subscrição. Expanda **funções**, clique com botão direito **HttpTrigger**e, em seguida, escolha **copiar o URL da função**.

    ![Copie o URL de função para o novo acionador HTTP](./media/functions-publish-project-vscode/function-copy-endpoint-url.png)
