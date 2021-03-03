---
title: Configure Gestor de Dados blockchain usando Azure CLI - Azure Blockchain Service
description: Criar e gerir um Gestor de Dados blockchain para o Serviço Azure Blockchain utilizando o Azure CLI
ms.date: 03/30/2020
ms.topic: how-to
ms.reviewer: ravastra
ms.openlocfilehash: 867a51b60afa56005bbb297b345f8a9260160ab8
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/03/2021
ms.locfileid: "101722641"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Configurar o Blockchain Data Manager com a CLI do Azure

Configure Blockchain Data Manager para o Serviço Azure Blockchain para capturar dados blockchain enviá-lo para um tópico de grelha de evento azure.

Para configurar uma instância do Gestor de Dados blockchain, você:

* Criar uma instância blockchain Manager
* Criar uma entrada para um nó de transação do Serviço Azure Blockchain
* Criar uma saída para um tópico de grelha de eventos Azure
* Adicionar uma aplicação blockchain
* Inicie um caso

## <a name="prerequisites"></a>Pré-requisitos

* Instale o mais recente [Azure CLI](/cli/azure/install-azure-cli) e assine a utilização `az login` .
* [Quickstart completo: Utilize o Código do Estúdio Visual para ligar a uma rede de consórcios do Azure Blockchain Service](connect-vscode.md). Recomenda-se o nível *padrão* do serviço Azure Blockchain ao utilizar o Blockchain Data Manager.
* Criar um [tópico de grelha de evento](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [os manipuladores de eventos em Azure Event Grid](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell num separador de navegador indo para [https://shell.azure.com/bash](https://shell.azure.com/bash) . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este arranque rápido requer a versão 2.0.51 do CLI Azure ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [a instalação do Azure CLI](/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *myResourceGroup* na localização *este:*

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Criar exemplo

Uma instância do Gestor de Dados blockchain monitoriza um nó de transação do Serviço Azure Blockchain. Um caso captura todos os dados brutos de blocos brutos e transações brutas do nó de transação. Blockchain Data Manager publica uma mensagem **RawBlockAndTransactionMsg** que é um superconjunto de informações devolvidas a partir de web3.eth [getBlock](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#getblock) e obter consultas de [transação.](https://web3js.readthedocs.io/en/v1.2.0/web3-eth.html#gettransaction)

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
| resource-group | Nome do grupo de recursos onde criar a instância do Gestor de Dados blockchain. |
| name | Nome da instância do Gestor de Dados blockchain. |
| tipo de recurso | O tipo de recurso para uma instância do Gestor de Dados blockchain é **Microsoft.blockchain/Watchers**. |
| é objeto completo | Indica que as propriedades contêm opções para o recurso do observador. |
| propriedades | Cadeia formatada por JSON que contém propriedades para o recurso do observador. Pode ser passado como uma corda ou um arquivo.  |

### <a name="create-instance-examples"></a>Criar exemplos de exemplos

Exemplo de configuração JSON para criar uma instância blockchain Manager na região **leste dos EUA.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| localização | Região onde criar o recurso de observador |
| propriedades | Propriedades a definir ao criar o recurso observador |

Crie uma instância do Blockchain Data Manager chamada *mywatcher* usando uma cadeia JSON para configuração.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Crie uma instância do Gestor de Dados blockchain chamada *mywatcher* usando um ficheiro de configuração JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Criar entrada

Uma entrada liga o Gestor de Dados blockchain a um nó de transação do Serviço Azure Blockchain. Apenas os utilizadores com acesso ao nó de transação podem criar uma ligação.

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
| name | O nome da entrada. |
| espaço de nomes | Utilize o espaço de nome do fornecedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma entrada do Gestor de Dados blockchain são **entradas.** |
| pai | O caminho para o observador ao qual a entrada está associada. Por exemplo, **observadores/vigilantes.** |
| é objeto completo | Indica que as propriedades contêm opções para o recurso de entrada. |
| propriedades | Cadeia formatada por JSON que contém propriedades para o recurso de entrada. Pode ser passado como uma corda ou um arquivo. |

### <a name="input-examples"></a>Exemplos de entrada

Configurar exemplo JSON para criar um recurso de entrada na região *leste dos EUA* que está ligado a \<Blockchain member\> .

``` json
{
    "location": "eastus",
    "properties": {
        "inputType": "Ethereum",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/blockchainMembers/<Blockchain member>/transactionNodes/transaction-node"
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| localização | Região onde criar o recurso de entrada. |
| tipo de entrada | Tipo ledger do membro do Serviço Azure Blockchain. Atualmente, **ethereum** é apoiado. |
| resourceId | Nó de transação ao qual a entrada está ligada. Substitua \<Subscription ID\> \<Resource group\> , e pelos \<Blockchain member\> valores para o recurso de nó de transação. A entrada liga-se ao nó de transação padrão para o membro do Serviço Azure Blockchain. |

Crie uma entrada chamada *myInput* para *o mywatcher* usando uma corda JSON para configuração.

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

Crie uma entrada chamada *myInput* para *o mywatcher* utilizando um ficheiro de configuração JSON.

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

Uma ligação de saída envia dados blockchain para Azure Event Grid. Pode enviar dados blockchain para um único destino ou enviar dados blockchain para vários destinos. O Blockchain Data Manager suporta várias ligações de saída de Tópicos de Grelha de Evento para qualquer instância do Gestor de Dados blockchain.

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
| name | Nome da saída. |
| espaço de nomes | Utilize o espaço de nome do fornecedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma saída do Gestor de Dados blockchain são **saídas.** |
| pai | O caminho para o observador ao qual a saída está associada. Por exemplo, **observadores/vigilantes.** |
| é objeto completo | Indica que as propriedades contêm opções para o recurso de saída. |
| propriedades | Cadeia formatada por JSON que contém propriedades para o recurso de saída. Pode ser passado como uma corda ou um arquivo. |

### <a name="output-examples"></a>Exemplos de saída

Configurar exemplo JSON para criar um recurso de saída na região *leste dos EUA* que está ligado a um tópico de grelha de eventos chamado \<event grid topic\> .

``` json
{
    "location": "eastus",
    "properties": {
        "outputType": "EventGrid",
        "dataSource": {
            "resourceId": "/subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.EventGrid/topics/<event grid topic>"
        }
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| localização | Região onde criar o recurso de saída. |
| tipo de saída | Tipo de saída. Atualmente, **o EventGrid** é apoiado. |
| resourceId | Recurso ao qual a saída está ligada. Substitua \<Subscription ID\> \<Resource group\> , e pelos \<Blockchain member\> valores para o recurso da grelha de eventos. |

Crie uma saída chamada *myoutput* para *o mywatcher* que se conecta a um tópico de grelha de evento usando uma cadeia de configuração JSON.

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

Crie uma saída chamada *myoutput* para *o mywatcher* que se conecta a um tópico de grelha de evento usando um ficheiro de configuração JSON.

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

## <a name="add-blockchain-application"></a>Adicionar aplicação blockchain

Se adicionar uma aplicação blockchain, o Blockchain Data Manager descodifica o evento e o estado de propriedade para a aplicação. Caso contrário, apenas são enviados dados de blocos brutos e de transações brutas. O Gestor de Dados blockchain também descobre endereços de contrato quando o contrato é implementado. Pode adicionar várias aplicações blockchain a uma instância do Gestor de Dados blockchain.


> [!IMPORTANT]
> Atualmente, aplicações blockchain que declaram [tipos de matrizes](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) solidity ou [tipos de mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não são totalmente suportados. As propriedades declaradas como tipos de matriz ou mapeamento não serão descodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg.*

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
| resource-group | Nome do grupo de recursos onde criar o recurso de aplicação. |
| name | O nome do pedido. |
| espaço de nomes | Utilize o espaço de nome do fornecedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma aplicação blockchain Data Manager é **artefactos.** |
| pai | O caminho para o observador ao qual a aplicação está associada. Por exemplo, **observadores/vigilantes.** |
| é objeto completo | Indica que as propriedades contêm opções para o recurso de aplicação. |
| propriedades | Cadeia formatada por JSON que contém propriedades para o recurso de aplicação. Pode ser passado como uma corda ou um arquivo. |

### <a name="blockchain-application-examples"></a>Exemplos de aplicação blockchain

Configuração JSON exemplo para criar um recurso de aplicação na região *leste dos EUA* que monitoriza um contrato inteligente definido pelo contrato ABI e bytecode.

``` json
{
    "location": "eastus",
    "properties": {
        "artifactType": "EthereumSmartContract",
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
| localização | Região onde criar o recurso de aplicação. |
| artefactoType | Tipo de aplicação. Atualmente, **o EthereumSmartContract** é suportado. |
| abiFileUrl | URL para contrato inteligente arquivo ABI JSON. Para obter mais informações sobre a obtenção de contrato ABI e a criação de um URL, consulte [Get Contract ABI e bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e [Crie contrato ABI e BYtecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL para contrato inteligente implementado por código ficheiro JSON. Para obter mais informações sobre a obtenção do contrato inteligente implementado por código e criação de um URL, consulte [Get Contract ABI e bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) and [Create contract ABI and bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Nota: O Gestor de Dados blockchain requer o **código de informação implantado**. |
| queryTargetTypes | Tipos de mensagens publicados. Especificar **ContratosProperties** publica o tipo de mensagem *ContractPropertiesMsg.* Especificar **ContractEvents** publica o tipo de mensagem *DecodeactEventsMsg.* Nota: São sempre publicados os tipos de mensagens *RawBlockAndTransactionMsg* e *RawTransactionContrActCreationMsg.* |

Crie uma aplicação chamada *myApplication* para *o mywatcher* que monitoriza um contrato inteligente definido por uma cadeia JSON.

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

Crie uma aplicação chamada *myApplication* para *o mywatcher* que assiste a um contrato inteligente definido usando um ficheiro de configuração JSON.

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

## <a name="start-instance"></a>Início de instância

Ao correr, uma instância blockchain Manager monitoriza eventos blockchain a partir das entradas definidas e envia dados para as saídas definidas.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| ação | Use **começar** a executar o observador. |
| ids | Identificação de recursos de observadores. Substitua \<Subscription ID\> \<Resource group\> , e pelos \<Watcher name\> valores para o recurso do observador.|

### <a name="start-instance-example"></a>Exemplo de exemplo de início

Inicie uma instância do Gestor de Dados blockchain chamada *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Paragem da instância

Pare uma instância do Gestor de Dados blockchain.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| ação | Use a **paragem** para parar o observador. |
| ids | O nome do observador. Substitua \<Subscription ID\> \<Resource group\> , e pelos \<Watcher name\> valores para o recurso do observador. |

### <a name="stop-watcher-example"></a>Pare o exemplo do observador

Pare um caso chamado *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Eliminar instância

Elimine uma instância do Gestor de Dados blockchain.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome do grupo de recursos do observador para apagar. |
| name | Nome do observador para apagar. |
| tipo de recurso | O tipo de recurso para um observador do Blockchain Data Manager é **Microsoft.blockchain/Watchers**. |

### <a name="delete-instance-example"></a>Eliminar exemplo de exemplo de instância

Elimine um *mywatcher* nomeado no grupo de recursos *myRG.*

``` azurecli-interactive
az resource delete \
                   --resource-group myRG \
                   --name mywatcher \
                   --resource-type Microsoft.blockchain/watchers
```

## <a name="next-steps"></a>Passos seguintes

Experimente o próximo tutorial criando um explorador de mensagens de transação blockchain usando blockchain Data Manager e Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Utilizar o Blockchain Data Manager para enviar dados para o Azure Cosmos DB](data-manager-cosmosdb.md)
