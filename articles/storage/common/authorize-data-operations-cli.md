---
title: Autorizar acesso a dados de blob ou fila com o Azure CLI
titleSuffix: Azure Storage
description: Especifique como autorizar operações de dados contra dados blob ou fila com o Azure CLI. Pode autorizar operações de dados utilizando credenciais De AD Azure, com a chave de acesso à conta, ou com um símbolo de assinatura de acesso partilhado (SAS).
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 02/26/2020
ms.author: tamram
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: c7091592f8806b6f6655315ae1faace286c2c1f5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78207696"
---
# <a name="authorize-access-to-blob-or-queue-data-with-azure-cli"></a>Autorizar acesso a dados de blob ou fila com o Azure CLI

O Azure Storage fornece extensões para o Azure CLI que lhe permitem especificar como pretende autorizar operações em dados de blob ou fila. Pode autorizar operações de dados das seguintes formas:

- Com um diretor de segurança Azure Ative Directory (Azure AD). A Microsoft recomenda a utilização de credenciais De AD Azure para uma segurança superior e facilidade de utilização.
- Com a chave de acesso à conta ou um símbolo de assinatura de acesso partilhado (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Especificar como as operações de dados são autorizadas

Os comandos Azure CLI para leitura e escrita `--auth-mode` de blob e dados de fila incluem o parâmetro opcional. Especifique este parâmetro para indicar como uma operação de dados deve ser autorizada:

- Defina `--auth-mode` o `login` parâmetro para iniciar sessão utilizando um diretor de segurança Azure AD (recomendado).
- Defina `--auth-mode` o parâmetro `key` para o valor legado para tentar recuperar a chave de acesso à conta a utilizar para autorização. Se omitir `--auth-mode` o parâmetro, o AZURE CLI também tenta recuperar a chave de acesso.

Para utilizar `--auth-mode` o parâmetro, certifique-se de que instalou a versão Azure CLI 2.0.46 ou posterior. Corra `az --version` para verificar a sua versão instalada.

> [!IMPORTANT]
> Se omitir `--auth-mode` o parâmetro ou `key`o definir, então o Azure CLI tenta utilizar a chave de acesso à conta para autorização. Neste caso, a Microsoft recomenda que forneça a chave de acesso no comando ou na variável **ambiente AZURE_STORAGE_KEY.** Para obter mais informações sobre variáveis ambientais, consulte a secção intitulada [Definir variáveis ambientais para parâmetros de autorização](#set-environment-variables-for-authorization-parameters).
>
> Se não fornecer a chave de acesso, o Azure CLI tenta chamar o fornecedor de recursos de armazenamento Azure para recuperá-la para cada operação. A realização de muitas operações de dados que exijam uma chamada para o fornecedor de recursos pode resultar em estrangulamento. Para obter mais informações sobre os limites do fornecedor de recursos, consulte os objetivos de [escalabilidade e desempenho para o fornecedor](scalability-targets-resource-provider.md)de recursos de armazenamento azure .

## <a name="authorize-with-azure-ad-credentials"></a>Autorizar com credenciais da AD Azure

Quando você inscreveu no Azure CLI com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Esta ficha é automaticamente utilizada pelo Azure CLI para autorizar operações de dados subsequentes contra o armazenamento de Blob ou Fila. Para operações apoiadas, já não precisa de passar uma chave de conta ou um símbolo SAS com o comando.

Pode atribuir permissões a dados de blob e fila a um diretor de segurança da AD Azure através do controlo de acesso baseado em funções (RBAC). Para obter mais informações sobre as funções RBAC no Armazenamento Azure, consulte Gerir os direitos de acesso aos dados de [Armazenamento Azure com RBAC](storage-auth-aad-rbac.md).

### <a name="permissions-for-calling-data-operations"></a>Permissões para chamadas de operações de dados

As extensões de Armazenamento Azure são suportadas para operações em dados de blob e fila. Quais as operações que pode ligar depende das permissões concedidas ao diretor de segurança da AD Azure com as quais se inscreveu no Azure CLI. As permissões para recipientes ou filas de armazenamento Azure são atribuídas via RBAC. Por exemplo, se lhe for atribuída a função **Blob Data Reader,** então pode executar comandos de script que lêem dados de um recipiente ou fila. Se lhe for atribuída a função **Blob Data Contributor,** então pode executar comandos de script que leiam, escrevem ou apaguem um recipiente ou fila ou os dados que contêm.

Para obter mais informações sobre as permissões necessárias para cada operação de armazenamento azure num recipiente ou fila, consulte as operações de armazenamento de [chamadas com fichas OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-container-with-azure-ad-credentials"></a>Exemplo: Autorizar uma operação para criar um contentor com credenciais DaD Azure

O exemplo que se segue mostra como criar um recipiente a partir do Azure CLI utilizando as suas credenciais De AD Azure. Para criar o recipiente, terá de iniciar sessão no Azure CLI, e precisará de um grupo de recursos e de uma conta de armazenamento. Para aprender a criar estes recursos, consulte [Quickstart: Criar, descarregar e listar bolhas com o Azure CLI](../blobs/storage-quickstart-blobs-cli.md).

1. Antes de criar o recipiente, atribua a função de Contribuinte de [Dados blob](../../role-based-access-control/built-in-roles.md#storage-blob-data-contributor) de armazenamento a si mesmo. Apesar de ser o proprietário da conta, necessita de permissões explícitas para efetuar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções RBAC, consulte o Acesso ao Grant à blob Azure e aos dados de [fila com rBAC no portal Azure.](storage-auth-aad-rbac.md)

    > [!IMPORTANT]
    > As atribuições de funções RBAC podem demorar alguns minutos a propagar-se.

1. Ligue para o recipiente de `--auth-mode` [armazenamento az criar](/cli/azure/storage/container#az-storage-container-create) comando com o parâmetro definido para `login` criar o recipiente usando as suas credenciais AD Azure. Lembre-se de substituir os valores do espaço reservado em suportes angulares por valores próprios:

    ```azurecli
    az storage container create \
        --account-name <storage-account> \
        --name sample-container \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizar com a chave de acesso à conta

Se possuir a chave da conta, pode ligar para qualquer operação de dados do Armazenamento Azure. Em geral, a utilização da chave da conta é menos segura. Se a chave da conta estiver comprometida, todos os dados da sua conta podem estar comprometidos.

O exemplo que se segue mostra como criar um recipiente utilizando a chave de acesso à conta. Especifique a chave `--auth-mode` da conta `key` e forneça o parâmetro com o valor:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizar com um token SAS

Se possuir um token SAS, pode ligar para operações de dados que são permitidas pelo SAS. O exemplo que se segue mostra como criar um recipiente utilizando um símbolo SAS:

```azurecli
az storage container create \
    --account-name <storage-account> \
    --name sample-container \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Definir variáveis ambientais para parâmetros de autorização

Pode especificar parâmetros de autorização em variáveis ambientais para evitar incluí-los em todas as chamadas para uma operação de dados de Armazenamento Azure. A tabela que se segue descreve as variáveis ambientais disponíveis.

| Variável de ambiente                  | Descrição                                                                                                                                                                                                                                                                                                                                                                     |
|---------------------------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
|    AZURE_STORAGE_ACCOUNT              |    O nome da conta de armazenamento. Esta variável deve ser utilizada em conjunto com a chave da conta de armazenamento ou com um token SAS. Se nenhum dos dois estiver presente, o Azure CLI tenta recuperar a chave de acesso à conta de armazenamento utilizando a conta AD Azure autenticada. Se um grande número de comandos forexecutado de uma só vez, o limite de estrangulamento do fornecedor de recursos de armazenamento Azure pode ser atingido. Para obter mais informações sobre os limites do fornecedor de recursos, consulte os objetivos de [escalabilidade e desempenho para o fornecedor](scalability-targets-resource-provider.md)de recursos de armazenamento azure .             |
|    AZURE_STORAGE_KEY                  |    A chave da conta de armazenamento. Esta variável deve ser utilizada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                                                                          |
|    AZURE_STORAGE_CONNECTION_STRING    |    Uma cadeia de ligação que inclui a chave da conta de armazenamento ou um token SAS. Esta variável deve ser utilizada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                       |
|    AZURE_STORAGE_SAS_TOKEN            |    Um símbolo de assinatura de acesso partilhado (SAS). Esta variável deve ser utilizada em conjunto com o nome da conta de armazenamento.                                                                                                                                                                                                                                                            |
|    AZURE_STORAGE_AUTH_MODE            |    O modo de autorização com o qual executar o comando. Os valores `login` permitidos `key`são (recomendados) ou . Se especificar, `login`o Azure CLI utiliza as suas credenciais De AD Azure para autorizar a operação de dados. Se especificar o `key` modo legado, o Azure CLI tenta consultar a chave de acesso à conta e autorizar o comando com a chave.    |

## <a name="next-steps"></a>Passos seguintes

- [Utilize o Azure CLI para atribuir uma função RBAC para acesso a dados de blob e fila](storage-auth-aad-rbac-cli.md)
- [Autorizar acesso a dados blob e fila com identidades geridas para recursos Azure](storage-auth-aad-msi.md)
