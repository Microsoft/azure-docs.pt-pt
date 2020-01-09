---
title: Executar comandos de CLI do Azure com as credenciais do Azure AD para acessar dados de BLOB ou fila
titleSuffix: Azure Storage
description: CLI do Azure suporte à entrada com as credenciais do Azure AD para executar comandos no blob de armazenamento do Azure e dados de filas. Um token de acesso é fornecido para a sessão e usado para autorizar operações de chamada. As permissões dependem da função RBAC atribuída à entidade de segurança do Azure AD.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 12/30/2019
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: bfbb466b7679e841c9c0154dbe28de14574d4282
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75553465"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Executar comandos de CLI do Azure com as credenciais do Azure AD para acessar dados de BLOB ou fila

O armazenamento do Azure fornece extensões para CLI do Azure que permitem que você entre e execute comandos de script com as credenciais do Azure Active Directory (AD do Azure). Quando você entra no CLI do Azure com as credenciais do Azure AD, um token de acesso OAuth 2,0 é retornado. Esse token é usado automaticamente pelo CLI do Azure para autorizar operações de dados subsequentes no armazenamento de BLOBs ou filas. Para operações com suporte, você não precisa mais passar uma chave de conta ou um token SAS com o comando.

Você pode atribuir permissões a dados de BLOB e de fila a uma entidade de segurança do Azure AD por meio do RBAC (controle de acesso baseado em função). Para obter mais informações sobre as funções RBAC no armazenamento do Azure, consulte [gerenciar direitos de acesso aos dados do armazenamento do Azure com o RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operações com suporte

As extensões de armazenamento do Azure têm suporte para operações em dados de BLOB e de fila. As operações que você pode chamar dependem das permissões concedidas à entidade de segurança do Azure AD com a qual você entra no CLI do Azure. As permissões para contêineres ou filas de armazenamento do Azure são atribuídas por meio de RBAC. Por exemplo, se você tiver atribuído a função de **leitor de dados de blob** , poderá executar comandos de script que lêem dados de um contêiner ou fila. Se você tiver atribuído a função **colaborador de dados de blob** , poderá executar comandos de script que lêem, gravam ou excluem um contêiner ou uma fila ou os dados que eles contêm.

Para obter detalhes sobre as permissões necessárias para cada operação de armazenamento do Azure em um contêiner ou fila, consulte [chamar operações de armazenamento com tokens OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Chamar comandos do CLI do Azure usando as credenciais do Azure AD

CLI do Azure dá suporte ao parâmetro `--auth-mode` para operações de dados de BLOB e de fila:

- Defina o parâmetro `--auth-mode` como `login` para entrar usando uma entidade de segurança do Azure AD.
- Defina o parâmetro `--auth-mode` como o valor de `key` herdado para tentar consultar uma chave de conta se nenhum parâmetro de autenticação para a conta for fornecido.

O exemplo a seguir mostra como criar um contêiner em uma nova conta de armazenamento de CLI do Azure usando suas credenciais do Azure AD. Lembre-se de substituir valores de espaço reservado entre colchetes angulares por seus próprios valores:

1. Certifique-se de ter instalado CLI do Azure versão 2.0.46 ou posterior. Execute `az --version` para verificar a versão instalada.

1. Execute `az login` e autentique na janela do navegador:

    ```azurecli
    az login
    ```

1. Especifique a assinatura desejada. Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Crie uma conta de armazenamento dentro desse grupo de recursos usando [AZ Storage Account Create](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create):

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_LRS \
        --encryption-services blob
    ```

1. Antes de criar o contêiner, atribua a função de [colaborador de dados do blob de armazenamento](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) a si mesmo. Embora você seja o proprietário da conta, você precisa de permissões explícitas para executar operações de dados na conta de armazenamento. Para obter mais informações sobre como atribuir funções RBAC, consulte [conceder acesso ao blob do Azure e dados de fila com RBAC no portal do Azure](storage-auth-aad-rbac.md).

    > [!IMPORTANT]
    > As atribuições de função do RBAC podem levar alguns minutos para serem propagadas.

1. Chame o comando [AZ Storage container Create](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) com o parâmetro `--auth-mode` definido como `login` para criar o contêiner usando suas credenciais do Azure AD:

    ```azurecli
    az storage container create \ 
        --account-name <storage-account> \ 
        --name sample-container \
        --auth-mode login
    ```

A variável de ambiente associada ao parâmetro `--auth-mode` é `AZURE_STORAGE_AUTH_MODE`. Você pode especificar o valor apropriado na variável de ambiente para evitar incluí-lo em cada chamada para uma operação de dados do armazenamento do Azure.

## <a name="next-steps"></a>Passos seguintes

- [Use CLI do Azure para atribuir uma função de RBAC para acesso a dados de BLOB e de fila](storage-auth-aad-rbac-cli.md)
- [Autorizar o acesso a dados de BLOB e de fila com identidades gerenciadas para recursos do Azure](storage-auth-aad-msi.md)
