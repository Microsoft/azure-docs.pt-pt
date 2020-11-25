---
title: Utilize o portal Azure para utilizar chaves geridas pelo cliente ou BYOK com Serviços de Mídia
description: Neste tutorial, utilize o portal Azure para ativar as chaves geridas pelo cliente ou traga a sua própria chave (BYOK) com uma conta de armazenamento Azure Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: 003b8e066a6161baedbc70e9becbca23566813ef
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2020
ms.locfileid: "96013258"
---
# <a name="tutorial-use-the-azure-portal-to-use-customer-managed-keys-or-byok-with-media-services"></a>Tutorial: Utilize o portal Azure para utilizar chaves geridas pelo cliente ou BYOK com Serviços de Mídia

Com a API 2020-05-01, pode utilizar uma chave RSA gerida pelo cliente com uma conta Azure Media Services que tem uma identidade gerida pelo sistema. Este tutorial cobre os degraus do portal Azure.

Os serviços utilizados são:

- Storage do Azure
- Azure Key Vault
- Serviços de Multimédia do Azure

Neste tutorial, você aprenderá a usar o portal Azure para:

> [!div class="checklist"]
> - Crie um grupo de recursos.
> - Criar uma conta de armazenamento com uma identidade gerida pelo sistema.
> - Criar uma conta de Serviços de Comunicação com uma identidade gerida pelo sistema.
> - Crie um cofre chave para armazenar uma chave RSA gerida pelo cliente.

## <a name="prerequisites"></a>Pré-requisitos

Uma subscrição do Azure.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free/).

## <a name="system-managed-keys"></a>Chaves geridas pelo sistema

<!-- Create a resource group -->
[!INCLUDE [create a resource group in the portal](./includes/task-create-resource-group-portal.md)]

> [!IMPORTANT]
> Para os seguintes passos de criação de conta de armazenamento, irá selecionar a escolha da chave gerida pelo sistema nas definições Avançadas.

<!-- Create a media services account -->

[!INCLUDE [create a media services account in the portal](./includes/task-create-media-services-account-portal.md)]

<!-- Create a key vault -->

[!INCLUDE [create a key vaultl](./includes/task-create-key-vault-portal.md)]

<!-- Enable CMK BYOK on the account -->
[!INCLUDE [enable CMK](./includes/task-enable-cmk-byok-portal.md)]

> [!IMPORTANT]
> Para os seguintes passos de encriptação de armazenamento, selecione a **escolha da chave gerida pelo cliente**.

<!-- Set encryption for storage account -->
[!INCLUDE [Set encryption for storage account](./includes/task-set-storage-encryption-portal.md)]

## <a name="change-the-key"></a>Mude a chave

Os Serviços de Comunicação Social detetam automaticamente quando a chave é alterada. OPCIONAL: Para testar este processo, crie outra versão chave para a mesma tecla. Os Serviços de Comunicação Social devem detetar que a chave foi alterada.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a usar os recursos que criou e *não quer continuar a ser cobrado,* apague-os.

## <a name="next-steps"></a>Passos seguintes

Vá ao próximo artigo para aprender a:
> [!div class="nextstepaction"]
> [Codificar um ficheiro remoto com base no URL e transmitir o vídeo com REST](stream-files-tutorial-with-rest.md)
