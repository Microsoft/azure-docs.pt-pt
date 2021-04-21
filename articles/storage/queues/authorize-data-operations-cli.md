---
title: Escolha como autorizar o acesso aos dados da fila com o Azure CLI
titleSuffix: Azure Storage
description: Especificar como autorizar operações de dados contra dados de fila com o Azure CLI. Pode autorizar operações de dados utilizando credenciais Azure AD, com a chave de acesso à conta, ou com um token de assinatura de acesso partilhado (SAS).
author: tamram
services: storage
ms.author: tamram
ms.reviewer: ozgun
ms.date: 02/10/2021
ms.topic: how-to
ms.service: storage
ms.subservice: common
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b3ac012da97194134f58d061dd9d84e945db554
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107774472"
---
# <a name="choose-how-to-authorize-access-to-queue-data-with-azure-cli"></a>Escolha como autorizar o acesso aos dados da fila com o Azure CLI

O Azure Storage fornece extensões para o Azure CLI que lhe permitem especificar como pretende autorizar operações em dados de fila. Pode autorizar operações de dados das seguintes formas:

- Com um diretor de segurança Azure Ative (Azure AD). A Microsoft recomenda a utilização de credenciais Azure AD para uma segurança superior e facilidade de utilização.
- Com a chave de acesso à conta ou um sinal de assinatura de acesso partilhado (SAS).

## <a name="specify-how-data-operations-are-authorized"></a>Especificar como as operações de dados são autorizadas

Os comandos Azure CLI para ler e escrever dados de fila incluem o `--auth-mode` parâmetro opcional. Especifique este parâmetro para indicar como uma operação de dados deve ser autorizada:

- Descreva o `--auth-mode` parâmetro para iniciar a `login` sação utilizando um princípio de segurança Azure AD (recomendado).
- Desa estade o `--auth-mode` parâmetro ao valor do legado para tentar recuperar a chave de acesso à conta para usar para `key` autorização. Se omitir o `--auth-mode` parâmetro, o CLI Azure também tenta recuperar a chave de acesso.

Para utilizar o `--auth-mode` parâmetro, certifique-se de que instalou o Azure CLI v2.0.46 ou mais tarde. Corra `az --version` para verificar a sua versão instalada.

> [!NOTE]
> Quando uma conta de armazenamento é bloqueada com um bloqueio **de leitura do** Gestor de Recursos Azure, a operação ['Lista Chaves'](/rest/api/storagerp/storageaccounts/listkeys) não é permitida para essa conta de armazenamento. **List Keys** é uma operação POST, e todas as operações POST são impedidas quando um bloqueio **ReadOnly** é configurado para a conta. Por esta razão, quando a conta está bloqueada com um bloqueio **ReadOnly,** os utilizadores que ainda não possuam as chaves da conta devem utilizar credenciais AZure AD para aceder aos dados da fila.

> [!IMPORTANT]
> Se omitir o `--auth-mode` parâmetro ou o `key` definir, então o CLI Azure tenta utilizar a chave de acesso à conta para autorização. Neste caso, a Microsoft recomenda que forneça a chave de acesso no comando ou na `AZURE_STORAGE_KEY` variável ambiente. Para obter mais informações sobre variáveis ambientais, consulte a secção intitulada [Conjunto de variáveis ambientais para parâmetros de autorização](#set-environment-variables-for-authorization-parameters).
>
> Se não fornecer a chave de acesso, o CLI Azure tenta ligar para o fornecedor de recursos de armazenamento Azure para recuperá-la para cada operação. A realização de muitas operações de dados que requerem uma chamada para o fornecedor de recursos pode resultar em estrangulamento. Para obter mais informações sobre os limites do fornecedor de recursos, consulte [os objetivos de Escalabilidade e desempenho para o fornecedor de recursos de armazenamento Azure](../common/scalability-targets-resource-provider.md).

## <a name="authorize-with-azure-ad-credentials"></a>Autorizar com credenciais AD AZure

Quando você iniciar súm na Azure CLI com credenciais Azure AD, um token de acesso OAuth 2.0 é devolvido. Este token é automaticamente utilizado pela Azure CLI para autorizar operações de dados subsequentes contra o Armazenamento de Filas. Para operações apoiadas, já não precisa de passar uma chave de conta ou um sinal SAS com o comando.

Pode atribuir permissões para os dados de fila a um diretor de segurança Azure AD através do controlo de acesso baseado em funções Azure (Azure RBAC). Para obter mais informações sobre as funções Azure no Azure Storage, consulte [Gerir os direitos de acesso aos dados de armazenamento Azure com o Azure RBAC](../common/storage-auth-aad-rbac-portal.md).

### <a name="permissions-for-calling-data-operations"></a>Permissões para chamadas de operações de dados

As extensões de Armazenamento Azure são suportadas para operações em dados de fila. Quais as operações que pode ligar dependem das permissões concedidas ao diretor de segurança da AZure AD com as quais se inscreve no Azure CLI. As permissões às filas são atribuídas através do Azure RBAC. Por exemplo, se lhe for atribuída a função **de Leitor de Dados de Fila de Armazenamento,** então pode executar comandos de script que lêem dados a partir de uma fila. Se lhe for atribuída a **função de Contribuinte de Dados de Fila de Armazenamento,** poderá executar comandos de script que leiam, escrevam ou apaguem uma fila ou os dados que contêm.

Para obter mais informações sobre as permissões necessárias para cada operação de Armazenamento Azure em uma fila, consulte [as operações de armazenamento de chamadas com fichas OAuth](/rest/api/storageservices/authorize-with-azure-active-directory#call-storage-operations-with-oauth-tokens).  

### <a name="example-authorize-an-operation-to-create-a-queue-with-azure-ad-credentials"></a>Exemplo: Autorizar uma operação para criar uma fila com credenciais AZure AD

O exemplo a seguir mostra como criar uma fila a partir do Azure CLI utilizando as suas credenciais AZure AD. Para criar a fila, terá de iniciar sessão no Azure CLI e precisará de um grupo de recursos e de uma conta de armazenamento.

1. Antes de criar a fila, atribua o papel [de Contribuinte de Dados de Fila de Armazenamento](../../role-based-access-control/built-in-roles.md#storage-queue-data-contributor) para si mesmo. Apesar de ser o proprietário da conta, necessita de permissões explícitas para realizar operações de dados contra a conta de armazenamento. Para obter mais informações sobre a atribuição de funções Azure, consulte [utilizar o portal Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-portal.md).

    > [!IMPORTANT]
    > As atribuições de funções azure podem demorar alguns minutos a propagar-se.

1. Ligue para o [`az storage queue create`](/cli/azure/storage/queue#az_storage_queue_create) comando com o parâmetro definido para criar a fila `--auth-mode` `login` usando as suas credenciais AZure AD. Lembre-se de substituir os valores do espaço reservado nos suportes angulares com os seus próprios valores:

    ```azurecli
    az storage queue create \
        --account-name <storage-account> \
        --name sample-queue \
        --auth-mode login
    ```

## <a name="authorize-with-the-account-access-key"></a>Autorizar com a chave de acesso à conta

Se possuir a chave da conta, pode ligar para qualquer operação de dados de armazenamento Azure. Em geral, a utilização da chave de conta é menos segura. Se a chave da conta estiver comprometida, todos os dados da sua conta poderão ficar comprometidos.

O exemplo a seguir mostra como criar uma fila utilizando a chave de acesso à conta. Especifique a chave da conta e forneça o `--auth-mode` parâmetro com o `key` valor:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --account-key <key>
    --auth-mode key
```

## <a name="authorize-with-a-sas-token"></a>Autorizar com um token SAS

Se possuir um token SAS, pode ligar para operações de dados que são permitidas pelo SAS. O exemplo a seguir mostra como criar uma fila utilizando um token SAS:

```azurecli
az storage queue create \
    --account-name <storage-account> \
    --name sample-queue \
    --sas-token <token>
```

## <a name="set-environment-variables-for-authorization-parameters"></a>Definir variáveis ambientais para parâmetros de autorização

Pode especificar parâmetros de autorização em variáveis ambientais para evitar incluí-los em cada chamada para uma operação de dados de armazenamento Azure. A tabela a seguir descreve as variáveis ambientais disponíveis.

| Variável de ambiente | Description |
|--|--|
| **AZURE_STORAGE_ACCOUNT** | O nome da conta de armazenamento. Esta variável deve ser utilizada em conjunto com a chave da conta de armazenamento ou com um token SAS. Se nenhum dos dois estiver presente, o Azure CLI tenta recuperar a chave de acesso à conta de armazenamento utilizando a conta Azure AD autenticada. Se um grande número de comandos for executado de uma só vez, o limite de estrangulamento do fornecedor de recursos de armazenamento Azure pode ser atingido. Para obter mais informações sobre os limites do fornecedor de recursos, consulte [os objetivos de Escalabilidade e desempenho para o fornecedor de recursos de armazenamento Azure](../common/scalability-targets-resource-provider.md). |
| **AZURE_STORAGE_KEY** | A chave da conta de armazenamento. Esta variável deve ser utilizada em conjunto com o nome da conta de armazenamento. |
| **AZURE_STORAGE_CONNECTION_STRING** | Uma cadeia de ligação que inclui a chave da conta de armazenamento ou um token SAS. Esta variável deve ser utilizada em conjunto com o nome da conta de armazenamento. |
| **AZURE_STORAGE_SAS_TOKEN** | Um token de assinatura de acesso partilhado (SAS). Esta variável deve ser utilizada em conjunto com o nome da conta de armazenamento. |
| **AZURE_STORAGE_AUTH_MODE** | O modo de autorização com o qual executar o comando. Os valores permitidos são `login` (recomendados) ou `key` . . Se `login` especificar, o Azure CLI utiliza as suas credenciais Azure AD para autorizar a operação de dados. Se especificar o `key` modo legado, o CLI Azure tenta consultar a chave de acesso à conta e autorizar o comando com a chave. |

## <a name="next-steps"></a>Passos seguintes

- [Use o CLI Azure para atribuir uma função Azure para acesso a dados de bolhas e filas](../common/storage-auth-aad-rbac-cli.md)
- [Autorizar o acesso a dados de blob e fila com identidades geridas para recursos Azure](../common/storage-auth-aad-msi.md)
