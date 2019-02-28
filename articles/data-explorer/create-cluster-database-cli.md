---
title: 'Início rápido: Criar um cluster do Explorador de dados do Azure e a base de dados com a CLI'
description: Saiba como criar um cluster do Explorador de dados do Azure e a base de dados com a CLI do Azure
services: data-explorer
author: radennis
ms.author: radennis
ms.reviewer: orspod
ms.service: data-explorer
ms.topic: quickstart
ms.date: 2/4/2019
ms.openlocfilehash: 357f0efcf7300545d10113c92702d9fed4aad049
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2019
ms.locfileid: "56958027"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-the-cli"></a>Criar um cluster do Explorador de dados do Azure e a base de dados com a CLI

Este início rápido descreve como criar um cluster do Explorador de dados do Azure e a base de dados com a CLI do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, precisa de uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Se optar por instalar e utilizar a CLI do Azure localmente, este início rápido requer a CLI do Azure versão 2.0.4 ou posterior. Execute `az --version` para verificar a sua versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure](https://docs.microsoft.com/en-us/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="configure-the-cli-parameters"></a>Configure os parâmetros CLI

Os passos seguintes não são necessários se estiver a executar comandos no Azure Cloud Shell. Se estiver a executar a CLI localmente, siga estes passos para iniciar sessão no Azure e para definir sua assinatura atual:

1. Execute o seguinte comando para iniciar sessão no Azure:

    ```azurecli-interactive
    az login
    ```

2. Defina a subscrição onde pretende que o cluster ser criado. Substitua `MyAzureSub` com o nome da subscrição do Azure que pretende utilizar:

    ```azurecli-interactive
    az account set --subscription MyAzureSub
    ```

## <a name="create-the-azure-data-explorer-cluster"></a>Criar o cluster do Explorador de dados do Azure

1. Crie o cluster utilizando o seguinte comando:

    ```azurecli-interactive
    az kusto cluster create --name azureclitest --sku D11_v2 --resource-group testrg
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | nome | *azureclitest* | O nome do cluster pretendido.|
   | sku | *D13_v2* | O SKU que será utilizado para o seu cluster. |
   | resource-group | *testrg* | O nome do grupo de recursos onde será criado o cluster. |

    Existem parâmetros opcionais adicionais que pode utilizar, como a capacidade do cluster.

2. Execute o seguinte comando para verificar se o cluster foi criado com êxito:

    ```azurecli-interactive
    az kusto cluster show --name azureclitest --resource-group testrg
    ```

Se o resultado contém `provisioningState` com o `Succeeded` valor, em seguida, o cluster foi criado com êxito.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>Criar a base de dados do cluster do Explorador de dados do Azure

1. Crie a base de dados, utilize o seguinte comando:

    ```azurecli-interactive
    az kusto database create --cluster-name azureclitest --name clidatabase --resource-group testrg --soft-delete-period 3650:00:00:00 --hot-cache-period 3650:00:00:00
    ```

   |**Definição** | **Valor sugerido** | **Descrição do campo**|
   |---|---|---|
   | cluster-name | *azureclitest* | O nome do cluster onde a base de dados será criado.|
   | nome | *clidatabase* | O nome da base de dados.|
   | resource-group | *testrg* | O nome do grupo de recursos onde será criado o cluster. |
   | soft-delete-period | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos disponíveis para consulta. |
   | armazenamento frequente-cache-período | *3650:00:00:00* | A quantidade de tempo que os dados serão mantidos na cache. |

2. Execute o seguinte comando para ver a base de dados que criou:

    ```azurecli-interactive
    az kusto database show --name clidatabase --resource-group testrg --cluster-name azureclitest
    ```

Agora tem um cluster e uma base de dados.

## <a name="clean-up-resources"></a>Limpar recursos

* Se tenciona seguir os nossos inícios rápidos e tutoriais, mantenha os recursos que criou.
* Para limpar os recursos, elimine o cluster. Quando eliminar um cluster, também elimina todos os bancos de dados no mesmo. Utilize o seguinte comando para eliminar o cluster:

    ```azurecli-interactive
    az kusto cluster delete --name azureclitest --resource-group testrg
    ```

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Quickstart: Ingestão de dados usando a biblioteca de Python de Explorador de dados do Azure](python-ingest-data.md)
