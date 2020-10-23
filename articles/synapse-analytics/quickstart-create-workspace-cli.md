---
title: 'Quickstart: Criar um espaço de trabalho synapse usando Azure CLI'
description: Crie um espaço de trabalho Azure Synapse utilizando o Azure CLI seguindo os passos deste guia.
services: synapse-analytics
author: alehall
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 08/25/2020
ms.author: alehall
ms.reviewer: jrasnick
ms.openlocfilehash: a5c9b47bf5d638f3c15416416a435653eeb68505
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/19/2020
ms.locfileid: "92172060"
---
# <a name="quickstart-create-an-azure-synapse-workspace-with-azure-cli"></a>Quickstart: Criar um espaço de trabalho sinapse Azure com Azure CLI

A CLI do Azure é a experiência da linha de comandos do Azure para a gestão de recursos do Azure. Pode utilizá-la no seu browser com o Azure Cloud Shell. Também pode instalá-la no macOS, Linux ou Windows e executá-la a partir da linha de comandos.

Neste arranque rápido, aprende-se a criar um espaço de trabalho synapse utilizando o CLI Azure.

Se não tiver uma subscrição do Azure, [crie uma conta gratuita antes de começar](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Descarregue e instale [o JQ,](https://stedolan.github.io/jq/download/)um processador JSON de linha de comando leve e flexível
- [Conta de armazenamento de Azure Data Lake Gen2](../storage/common/storage-account-create.md?toc=/azure/synapse-analytics/toc.json&bc=/azure/synapse-analytics/breadcrumb/toc.json)

    > [!IMPORTANT]
    > O espaço de trabalho Azure Synapse precisa de ser capaz de ler e escrever para a conta ADLS Gen2 selecionada. Além disso, para qualquer conta de armazenamento que ligue como conta de armazenamento primário, deve ter ativado o **espaço hierárquico** na criação da conta de armazenamento, conforme descrito na página [Criar um Accout de Armazenamento.](https://docs.microsoft.com/azure/storage/common/storage-account-create?tabs=azure-portal#create-a-storage-account) 

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="install-the-azure-cli-locally"></a>Instale o Azure CLI localmente

Se optar por instalar e utilizar o Azure CLI localmente, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

Se estiver a executar o Azure CLI localmente, tem de fazer login e autenticar. Este passo não é necessário se estiver a utilizar a Azure Cloud Shell. Para iniciar sessão no Azure CLI, corra `az login` e autente na janela do navegador:

```azurecli
az login
```

Para obter mais informações sobre a autenticação» com o Azure [CLI, consulte Iniciar súm em Azure CLI](/cli/azure/authenticate-azure-cli).

## <a name="install-azure-synapse-extension-for-azure-cli"></a>Instale a extensão Azure Synapse para O Azure CLI

```azurecli
az extension add --name synapse
```

> [!WARNING]
> A extensão Azure Synapse para Azure CLI está em pré-visualização.

## <a name="create-an-azure-synapse-workspace-using-the-azure-cli"></a>Criar um espaço de trabalho Azure Synapse utilizando o Azure CLI

1. Definir variáveis ambientais necessárias para criar recursos para o espaço de trabalho Azure Synapse.

    | Nome da Variável de Ambiente | Descrição |
    |---|---|---|
    |StorageAccountName| Nome da sua conta de armazenamento ADLS Gen2 existente.|
    |StorageAccountResourceGroup| Nome do seu grupo de recursos de conta de armazenamento ADLS Gen2 existente. |
    |Nome de FileShare| Nome do seu sistema de ficheiros de armazenamento existente.|
    |Grupo SynapseResource| Escolha um novo nome para o seu grupo de recursos Azure Synapse. |
    |Região| Escolha uma das [regiões de Azure.](https://azure.microsoft.com/global-infrastructure/geographies/#overview) |
    |SinapseWorkspaceName| Escolha um nome único para o seu novo espaço de trabalho Azure Synapse. |
    |SqlUser| Escolha um valor para um novo nome de utilizador.|
    |SqlPassword| Escolha uma senha segura.|
    |||

2. Crie um grupo de recursos como recipiente para o seu espaço de trabalho Azure Synapse:
    ```azurecli
    az group create --name $SynapseResourceGroup --location $Region
    ```
3. Recupere a chave da conta de armazenamento ADLS Gen 2:
    ```azurecli
    StorageAccountKey=$(az storage account keys list \
      --account-name $StorageAccountName \
      | jq -r '.[0] | .value')
    ```
4. Recupere o URL de ponto final de armazenamento ADLS Gen 2:
    ```azurecli
    StorageEndpointUrl=$(az storage account show \
      --name $StorageAccountName \
      --resource-group $StorageAccountResourceGroup \
      | jq -r '.primaryEndpoints | .dfs')
    ```

5. (Opcional) Pode sempre verificar qual é a sua chave de conta de armazenamento ADLS Gen2 e ponto final:
    ```azurecli
    echo "Storage Account Key: $StorageAccountKey"
    echo "Storage Endpoint URL: $StorageEndpointUrl"
    ```

6. Criar um espaço de trabalho Azure Synapse:
    ```azurecli
    az synapse workspace create \
      --name $SynapseWorkspaceName \
      --resource-group $SynapseResourceGroup \
      --storage-account $StorageAccountName \
      --file-system $FileShareName \
      --sql-admin-login-user $SqlUser \
      --sql-admin-login-password $SqlPassword \
      --location $Region
    ```

7. Obtenha Web e Dev URL para O espaço de trabalho Azure Synapse:
    ```azurecli
    WorkspaceWeb=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .web')

    WorkspaceDev=$(az synapse workspace show --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup | jq -r '.connectivityEndpoints | .dev')
    ```

8. Crie uma Regra de Firewall para permitir o seu acesso ao Espaço de Trabalho Azure Synapse a partir da sua máquina:

    ```azurecli
    ClientIP=$(curl -sb -H "Accept: application/json" "$WorkspaceDev" | jq -r '.message')
    ClientIP=${ClientIP##'Client Ip address : '}
    echo "Creating a firewall rule to enable access for IP address: $ClientIP"

    az synapse workspace firewall-rule create --end-ip-address $ClientIP --start-ip-address $ClientIP --name "Allow Client IP" --resource-group $SynapseResourceGroup --workspace-name $SynapseWorkspaceName
    ```

9. Abra o endereço WEB URL do Espaço de Trabalho Azure Synapse armazenado em variável ambiente `WorkspaceWeb` para aceder ao seu espaço de trabalho:

    ```azurecli
    echo "Open your Azure Synapse Workspace Web URL in the browser: $WorkspaceWeb"
    ```
    
    [![Azure Synapse workspace web ](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png)](media/quickstart-create-synapse-workspace-cli/create-workspace-cli-1.png#lightbox)


## <a name="clean-up-resources"></a>Limpar recursos

Siga os passos abaixo para eliminar o espaço de trabalho Azure Synapse.
> [!WARNING]
> A eliminação de um espaço de trabalho Azure Synapse removerá os motores de análise e os dados armazenados na base de dados dos pools SQL contidos e metadados do espaço de trabalho. Deixará de ser possível ligar-se aos pontos finais SQL ou Apache Spark. Todos os artefactos de código serão eliminados (consultas, cadernos, definições de emprego e oleodutos).
>
> A eliminação do espaço de trabalho **não** afetará os dados da Data Lake Store Gen2 ligada ao espaço de trabalho.

Se pretender eliminar o espaço de trabalho Azure Synapse, complete o seguinte comando:

```azurecli
az synapse workspace delete --name $SynapseWorkspaceName --resource-group $SynapseResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Em seguida, você pode [criar piscinas SQL](quickstart-create-sql-pool-studio.md) ou [criar piscinas Apache Spark](quickstart-create-apache-spark-pool-studio.md) para começar a analisar e explorar seus dados.
