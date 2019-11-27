---
title: Configurar Gerenciador de Dados Blockchain usando CLI do Azure-serviço Blockchain do Azure
description: Criar e gerenciar um Gerenciador de Dados Blockchain para o serviço Blockchain do Azure usando CLI do Azure
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/24/2019
ms.locfileid: "74455934"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Configurar o Blockchain Data Manager com a CLI do Azure

Configure o Blockchain Gerenciador de Dados para o serviço Blockchain do Azure para capturar dados Blockchain enviá-los para um tópico da grade de eventos do Azure.

Para configurar uma instância de Gerenciador de Dados do Blockchain, você:

* Criar uma instância do Gerenciador de Blockchain
* Criar uma entrada para um nó de transação do serviço Blockchain do Azure
* Criar uma saída para um tópico da grade de eventos do Azure
* Adicionar um aplicativo blockchain
* Iniciar uma instância

## <a name="prerequisites"></a>Pré-requisitos

* Instale a [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) mais recente e tenha entrado usando `az login`.
* Concluir [o início rápido: usar Visual Studio Code para se conectar a uma rede do Azure Blockchain Service Consortium](connect-vscode.md)
* Criar um [tópico de grade de eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [manipuladores de eventos na grade de eventos do Azure](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimentar** no canto superior direito de um bloco de código. Também pode iniciar o Cloud Shell num separador do browser separado ao aceder a [https://shell.azure.com/bash](https://shell.azure.com/bash). Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se você preferir instalar e usar a CLI localmente, este início rápido exigirá CLI do Azure versão 2.0.51 ou posterior. Executar `az --version` para localizar a versão. Se você precisar instalar ou atualizar, consulte [instalar CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos com o nome *myResourceGroup* na localização *eastus*:

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Criar instância

Uma instância de Gerenciador de Dados do Blockchain monitora um nó de transação do serviço Blockchain do Azure. Uma instância captura todos os dados brutos de bloqueio e de transação do nó de transação.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Blockchain Data Manager instance name> \
                   --resource-type Microsoft.blockchain/watchers \
                   --is-full-object \
                   --properties <watcher resource properties>
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar a instância de Gerenciador de Dados do Blockchain. |
| nome | Nome da instância de Gerenciador de Dados de Blockchain. |
| tipo de recurso | O tipo de recurso para uma instância de Gerenciador de Dados Blockchain é **Microsoft. Blockchain/inspetores**. |
| é-Full-Object | Indica que as propriedades contêm opções para o recurso do Inspetor. |
| propriedades | Cadeia de caracteres formatada em JSON que contém propriedades para o recurso do Inspetor. Pode ser passado como uma cadeia de caracteres ou um arquivo.  |

### <a name="create-instance-examples"></a>Criar exemplos de instância

Exemplo de configuração JSON para criar uma instância do Blockchain Manager na região **leste dos EUA** .

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Região onde criar o recurso do Inspetor |
| propriedades | Propriedades a serem definidas ao criar o recurso do Inspetor |

Crie uma instância de Gerenciador de Dados do Blockchain chamada *myassister* usando uma cadeia de caracteres JSON para configuração.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Crie uma instância de Gerenciador de Dados do Blockchain chamada *myassister* usando um arquivo de configuração JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Criar entrada

Uma entrada conecta o Blockchain Gerenciador de Dados a um nó de transação do serviço Blockchain do Azure. Somente os usuários com acesso ao nó de transação podem criar uma conexão.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Input name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <input resource properties>
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar o recurso de entrada. |
| nome | Nome da entrada. |
| espaço de nomes | Use o namespace do provedor **Microsoft. Blockchain** . |
| tipo de recurso | O tipo de recurso para um Blockchain Gerenciador de Dados entrada é **entradas**. |
| primária | O caminho para o observador ao qual a entrada está associada. Por exemplo, **inspetores/myassister**. |
| é-Full-Object | Indica que as propriedades contêm opções para o recurso de entrada. |
| propriedades | Cadeia de caracteres formatada em JSON que contém propriedades para o recurso de entrada. Pode ser passado como uma cadeia de caracteres ou um arquivo. |

### <a name="input-examples"></a>Exemplos de entrada

Exemplo de JSON de configuração para criar um recurso de entrada na região *leste dos EUA* que está conectada a \<membro Blockchain\>.

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Região onde criar o recurso de entrada. |
| inputType | Tipo de razão do membro do serviço Blockchain do Azure. Atualmente, há suporte para **Ethereum** . |
| resourceId | Nó de transação ao qual a entrada está conectada. Substitua \<ID de assinatura\>, \<\>de grupo de recursos e \<membro Blockchain\> com os valores para o recurso de nó de transação. A entrada se conecta ao nó de transação padrão para o membro do serviço Blockchain do Azure. |

Crie uma entrada chamada *MyInput* para *myassister* usando uma cadeia de caracteres JSON para configuração.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myInput \
                   --namespace Microsoft.Blockchain \
                   --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus", "properties":{"inputType":"Ethereum","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/BlockchainMembers/<Blockchain member>/transactionNodes/transaction-node"}}}'
```

Crie uma entrada chamada *MyInput* para *myassister* usando um arquivo de configuração JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name input \
                   --namespace Microsoft.Blockchain \ --resource-type inputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @input.json
```

## <a name="create-output"></a>Criar saída

Uma conexão de saída envia dados blockchain para a grade de eventos do Azure. Você pode enviar dados do blockchain para um único destino ou enviar dados do blockchain para vários destinos. Blockchain Gerenciador de Dados dá suporte a várias conexões de saída de tópico de grade de eventos para qualquer instância de Gerenciador de Dados de Blockchain especificada.

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Output name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <output resource properties>
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar o recurso de saída. |
| nome | Nome da saída. |
| espaço de nomes | Use o namespace do provedor **Microsoft. Blockchain** . |
| tipo de recurso | O tipo de recurso para um Blockchain Gerenciador de Dados saída é **saídas**. |
| primária | O caminho para o observador ao qual a saída está associada. Por exemplo, **inspetores/myassister**. |
| é-Full-Object | Indica que as propriedades contêm opções para o recurso de saída. |
| propriedades | Cadeia de caracteres formatada em JSON que contém propriedades para o recurso de saída. Pode ser passado como uma cadeia de caracteres ou um arquivo. |

### <a name="output-examples"></a>Exemplos de saída

Exemplo de JSON de configuração para criar um recurso de saída na região *leste dos EUA* que está conectada a um tópico da grade de eventos chamado \<tópico da grade de eventos\>.

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Região onde criar o recurso de saída. |
| outputType | Tipo de saída. Atualmente, há suporte para **EventGrid** . |
| resourceId | Recurso ao qual a saída está conectada. Substitua \<ID de assinatura\>, \<\>de grupo de recursos e \<membro Blockchain\> com os valores para o recurso de grade de eventos. |

Crie uma saída chamada *MyOutput* para *myassister* que se conecta a um tópico da grade de eventos usando uma cadeia de caracteres de configuração JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"outputType":"EventGrid","dataSource":{"resourceId":"/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"}}}'
```

Crie uma saída chamada *MyOutput* para *myassister* que se conecta a um tópico da grade de eventos usando um arquivo de configuração JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myoutput \
                   --namespace Microsoft.Blockchain \
                   --resource-type outputs \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @output.json
```

## <a name="add-blockchain-application"></a>Adicionar aplicativo blockchain

Se você adicionar um aplicativo blockchain, Blockchain Gerenciador de Dados decodificará o evento e o estado da propriedade para o aplicativo. Caso contrário, somente os dados brutos de bloco e de transação bruto serão enviados. Blockchain Gerenciador de Dados também descobre os endereços de contrato quando o contrato é implantado. Você pode adicionar vários aplicativos blockchain a uma instância de Gerenciador de Dados do Blockchain.


> [!IMPORTANT]
> Atualmente, os aplicativos blockchain que declaram [tipos de matriz](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) de solidez ou [tipos de mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não têm suporte total. As propriedades declaradas como tipos de matriz ou de mapeamento não serão decodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg* .

``` azurecli
az resource create \
                   --resource-group <Resource group> \
                   --name <Application name> \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/<Watcher name> \
                   --is-full-object \
                   --properties <Application resource properties>
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos onde criar o recurso de aplicativo. |
| nome | Nome do aplicativo. |
| espaço de nomes | Use o namespace do provedor **Microsoft. Blockchain** . |
| tipo de recurso | O tipo de recurso para um aplicativo de Gerenciador de Dados Blockchain é **artefatos**. |
| primária | O caminho para o observador ao qual o aplicativo está associado. Por exemplo, **inspetores/myassister**. |
| é-Full-Object | Indica que as propriedades contêm opções para o recurso de aplicativo. |
| propriedades | Cadeia de caracteres formatada em JSON que contém propriedades para o recurso de aplicativo. Pode ser passado como uma cadeia de caracteres ou um arquivo. |

### <a name="blockchain-application-examples"></a>Exemplos de aplicativos Blockchain

Exemplo de JSON de configuração para criar um recurso de aplicativo na região *leste dos EUA* que monitora um contrato inteligente definido pela Abi e o código de bytes do contrato.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
        "content": {
            "abiFileUrl": "<ABI URL>",
            "bytecodeFileUrl": "<Bytecode URL>",
            "queryTargetTypes": [
                "ContractProperties",
                "ContractEvents"
            ]
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| location | Região onde criar o recurso de aplicativo. |
| artefatotype | Tipo de aplicativo. Atualmente, há suporte para **EthereumSmartContract** . |
| abiFileUrl | URL para o arquivo JSON da ABI do Smart Contract. Para obter mais informações sobre como obter a ABI do contrato e criar uma URL, consulte [obter a Abi do contrato e o código de bytes](data-manager-portal.md#get-contract-abi-and-bytecode) e criar o Abi do [contrato e a URL do código](data-manager-portal.md#create-contract-abi-and-bytecode-url) |
| bytecodeFileUrl | URL do arquivo JSON do código de bytes implantado pelo contrato inteligente. Para obter mais informações sobre como obter o código de bytes implantado do contrato inteligente e criar uma URL, consulte [obter a Abi do contrato e o código de bytes](data-manager-portal.md#get-contract-abi-and-bytecode) e criar a URL do [contrato Abi](data-manager-portal.md#create-contract-abi-and-bytecode-url) Observação: Blockchain Gerenciador de Dados requer o **código de bytes implantado**. |
| queryTargetTypes | Tipos de mensagem publicados. Especificar **Contraiproperties** publica o tipo de mensagem *ContractPropertiesMsg* . Especificar **ContractEvents** publica o tipo de mensagem *DecodedContractEventsMsg* . Observação: os tipos de mensagem *RawBlockAndTransactionMsg* e *RawTransactionContractCreationMsg* são sempre publicados. |

Crie um aplicativo chamado *MyApplication* para *myassister* que monitora um contrato inteligente definido por uma cadeia de caracteres JSON.

``` azurecli-interactive
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties '{"location":"eastus","properties":{"artifactType":"EthereumSmartContract","content":{"abiFileUrl":"<ABI URL>","bytecodeFileUrl":"<Bytecode URL>","queryTargetTypes":["ContractProperties","ContractEvents"]}}}'
```

Crie um aplicativo chamado *MyApplication* para *myassister* que observa um contrato inteligente definido usando um arquivo de configuração JSON.

``` azurecli
az resource create \
                   --resource-group myRG \
                   --name myApplication \
                   --namespace Microsoft.Blockchain \
                   --resource-type artifacts \
                   --parent watchers/mywatcher \
                   --is-full-object \
                   --properties @artifact.json
```

## <a name="start-instance"></a>Iniciar instância

Durante a execução, uma instância do Gerenciador de Blockchain monitora eventos de Blockchain das entradas definidas e envia dados para as saídas definidas.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| ação | Use **Iniciar** para executar o Inspetor. |
| identidade | ID do recurso do Inspetor. Substitua \<ID da assinatura\>, \<\>do grupo de recursos e \<nome do Inspetor\> pelos valores do recurso do Inspetor.|

### <a name="start-instance-example"></a>Exemplo de inicialização de instância

Inicie uma instância de Gerenciador de Dados do Blockchain chamada *myassister*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Parar instância

Parar uma instância de Gerenciador de Dados do Blockchain.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| ação | Use **parar** para interromper o Inspetor. |
| identidade | Nome do Inspetor. Substitua \<ID da assinatura\>, \<\>do grupo de recursos e \<nome do Inspetor\> pelos valores do recurso do Inspetor. |

### <a name="stop-watcher-example"></a>Parar exemplo do observador

Interrompa uma instância chamada *myassister*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Excluir instância

Excluir uma instância de Gerenciador de Dados do Blockchain.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos do Inspetor a ser excluído. |
| nome | Nome do Inspetor a ser excluído. |
| tipo de recurso | O tipo de recurso para um inspetor de Gerenciador de Dados Blockchain é **Microsoft. Blockchain/Watchers**. |

### <a name="delete-instance-example"></a>Exemplo de excluir instância

Exclua uma instância chamada *myassister* no grupo de recursos *myRG* .

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Passos Seguintes

Experimente o próximo tutorial Criando um Gerenciador de mensagens de transação blockchain usando o Blockchain Gerenciador de Dados e o Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Usar o Blockchain Gerenciador de Dados para enviar dados para Azure Cosmos DB](data-manager-cosmosdb.md)
