---
title: Executar comandos Do CLI Da Run Azure com credenciais AD Azure para aceder a dados de blob ou fila
titleSuffix: Azure Storage
description: Suporte azure CLI assinando com credenciais Azure AD para executar comandos em blob de armazenamento Azure e dados de filas. É fornecido um sinal de acesso para a sessão e utilizado para autorizar operações de chamada. As permissões dependem da função RBAC atribuída ao diretor de segurança da AD Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 4133c081823f1cc319480c1bc847b672df0374d4
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77660619"
---
# <a name="run-azure-cli-commands-with-azure-ad-credentials-to-access-blob-or-queue-data"></a>Executar comandos Do CLI Da Run Azure com credenciais AD Azure para aceder a dados de blob ou fila

O Azure Storage fornece extensões para o Azure CLI que lhe permitem iniciar sessão e executar comandos de scriptcom credenciais Azure Ative Directory (Azure AD). Quando você inscreveu no Azure CLI com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Esta ficha é automaticamente utilizada pelo Azure CLI para autorizar operações de dados subsequentes contra o armazenamento de Blob ou Fila. Para operações apoiadas, já não precisa de passar uma chave de conta ou um símbolo SAS com o comando.

Pode atribuir permissões a dados de blob e fila a um diretor de segurança da AD Azure através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre as funções RBAC no Armazenamento Azure, consulte Gerir os direitos de acesso aos dados de [Armazenamento Azure com RBAC](storage-auth-aad-rbac.md).

## <a name="supported-operations"></a>Operações apoiadas

As extensões de Armazenamento Azure são suportadas para operações em dados de blob e fila. Quais as operações que pode ligar depende das permissões concedidas ao diretor de segurança da AD Azure com as quais se inscreveu no Azure CLI. As permissões para recipientes ou filas de armazenamento Azure são atribuídas via RBAC. Por exemplo, se lhe for atribuída a função **Blob Data Reader,** então pode executar comandos de script que lêem dados de um recipiente ou fila. Se lhe for atribuída a função **Blob Data Contributor,** então pode executar comandos de script que leiam, escrevem ou apaguem um recipiente ou fila ou os dados que contêm.

Para obter mais informações sobre as permissões necessárias para cada operação de armazenamento azure num recipiente ou fila, consulte as operações de armazenamento de [chamadas com fichas OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

## <a name="call-azure-cli-commands-using-azure-ad-credentials"></a>Ligue para comandos Azure CLI usando credenciais Azure AD

O Azure CLI suporta o parâmetro `--auth-mode` para operações de dados de blob e fila:

- Defina o parâmetro de `--auth-mode` para `login` para assinar com um diretor de segurança da AD Azure.
- Defina o parâmetro `--auth-mode` para o legado `key` valor para tentar consultar uma chave de conta se não forem fornecidos parâmetros de autenticação para a conta.

O exemplo que se segue mostra como criar um recipiente numa nova conta de armazenamento do Azure CLI utilizando as suas credenciais Azure AD. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

1. Certifique-se de que instalou a versão 2.0.46 do Azure CLI ou posterior. Faça `az --version` para verificar a sua versão instalada.

1. Executar `az login` e autenticar na janela do navegador:

    ```azurecli
    az login
    ```

1. Especifique a subscrição desejada. Crie um grupo de recursos com [az group create](https://docs.microsoft.com/cli/azure/group?view=azure-cli-latest#az-group-create). Criar uma conta de armazenamento dentro desse grupo de recursos utilizando a conta de [armazenamento az criar:](https://docs.microsoft.com/cli/azure/storage/account?view=azure-cli-latest#az-storage-account-create)

    ```azurecli
    az account set --subscription <subscription-id>

    az group create \
        --name sample-resource-group-cli \
        --location eastus

    az storage account create \
        --name <storage-account> \
        --resource-group sample-resource-group-cli \
        --location eastus \
        --sku Standard_ZRS \
        --encryption-services blob
    ```

1. Antes de criar o recipiente, atribua a função de Contribuinte de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) de armazenamento a si mesmo. Apesar de ser o proprietário da conta, necessita de permissões explícitas para efetuar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções RBAC, consulte o Acesso ao Grant à blob Azure e aos dados de [fila com rBAC no portal Azure.](storage-auth-aad-rbac.md)

    > [!IMPORTANT]
    > As atribuições de funções RBAC podem demorar alguns minutos a propagar-se.

1. Ligue para o recipiente de [armazenamento az criar](https://docs.microsoft.com/cli/azure/storage/container?view=azure-cli-latest#az-storage-container-create) comando com o parâmetro `--auth-mode` definido para `login` para criar o recipiente usando as suas credenciais AD Azure:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

A variável ambiental associada ao parâmetro `--auth-mode` é `AZURE_STORAGE_AUTH_MODE`. Pode especificar o valor adequado na variável ambiental para evitar incluí-lo em cada chamada para uma operação de dados de Armazenamento Azure.

## <a name="next-steps"></a>Passos seguintes

- [Utilize o Azure CLI para atribuir uma função RBAC para acesso a dados de blob e fila](storage-auth-aad-rbac-cli.md)
- [Autorizar acesso a dados blob e fila com identidades geridas para recursos Azure](storage-auth-aad-msi.md)
