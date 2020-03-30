---
title: Configure Blockchain Data Manager usando O Serviço Azure CLI - Azure Blockchain
description: Crie e gerencie um Gestor de Dados blockchain para o Serviço Azure Blockchain utilizando o Azure CLI
ms.date: 11/04/2019
ms.topic: article
ms.reviewer: chroyal
ms.openlocfilehash: a8061aad6d6a1513de70e7c2bc57aa109c666611
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74455934"
---
# <a name="configure-blockchain-data-manager-using-azure-cli"></a>Configurar o Blockchain Data Manager com a CLI do Azure

Configure O Gestor de Dados blockchain para o Serviço Azure Blockchain para capturar dados blockchain envie-os para um tópico de grelha de eventos Azure.

Para configurar uma instância do Gestor de Dados blockchain, você:

* Criar uma instância de Blockchain Manager
* Criar uma entrada para um nó de transação do Serviço Blockchain Azure
* Criar uma saída para um tópico de grelha de eventos Azure
* Adicione uma aplicação blockchain
* Iniciar uma instância

## <a name="prerequisites"></a>Pré-requisitos

* Instale o mais recente [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli) e assinou a utilização. `az login`
* Complete [Quickstart: Use visual studio code para ligar a uma rede](connect-vscode.md) de consórcio azure Blockchain Service
* Criar um tópico de grelha de [eventos](../../event-grid/custom-event-quickstart-portal.md#create-a-custom-topic)
* Saiba mais sobre [os manipuladores de eventos na Grelha de Eventos Azure](../../event-grid/event-handlers.md)

## <a name="launch-azure-cloud-shell"></a>Iniciar o Azure Cloud Shell

O Azure Cloud Shell é um shell interativo gratuito que pode utilizar para executar os passos neste artigo. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta.

Para abrir o Cloud Shell, basta selecionar **Experimente** no canto superior direito de um bloco de código. Também pode lançar cloud Shell em um [https://shell.azure.com/bash](https://shell.azure.com/bash)separado separado browser, indo para . Selecione **Copiar** para copiar os blocos de código, cole-o no Cloud Shell e prima Enter para executá-lo.

Se preferir instalar e utilizar o CLI localmente, este quickstart requer a versão Azure CLI 2.0.51 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, consulte [a instalação do Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli).

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](https://docs.microsoft.com/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo seguinte cria um grupo de recursos chamado *myResourceGroup* na localização *oriental:*

```azurecli-interactive
az group create --name myRG --location eastus
```

## <a name="create-instance"></a>Criar instância

Uma instância do Gestor de Dados blockchain monitoriza um nó de transações do Serviço Blockchain Azure. Um caso captura todos os dados de transações brutas e blocos brutos do nó de transações.

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
| nome | Nome da instância do Gestor de Dados blockchain. |
| tipo de recurso | O tipo de recurso para uma instância do Blockchain Data Manager é **Microsoft.blockchain/watchers**. |
| é objeto cheio | Indica que as propriedades contêm opções para o recurso do observador. |
| propriedades | Cadeia formatada JSON contendo propriedades para o recurso observador. Pode ser passado como uma corda ou um arquivo.  |

### <a name="create-instance-examples"></a>Criar exemplos de exemplo

Exemplo de configuração jSON para criar uma instância de Blockchain Manager na região **leste dos EUA.**

``` json
{
    "location": "eastus",
    "properties": {
    }
}
```

| Elemento | Descrição |
|---------|-------------|
| localização | Região onde criar o recurso de observador |
| propriedades | Propriedades a definir ao criar o recurso do observador |

Crie uma instância de Blockchain Data Manager chamada *mywatcher* usando uma corda JSON para configuração.

``` azurecli-interactive
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                     --is-full-object \
                     --properties '{"location":"eastus"}'
```

Crie uma instância de Blockchain Data Manager chamada *mywatcher* usando um ficheiro de configuração JSON.

``` azurecli
az resource create \
                    --resource-group myRG \
                    --name mywatcher \
                    --resource-type Microsoft.blockchain/watchers \
                    --is-full-object \
                    --properties @watcher.json
```

## <a name="create-input"></a>Criar entrada

Uma entrada liga o Gestor de Dados blockchain a um nó de transações do Serviço Blockchain Azure. Apenas os utilizadores com acesso ao nó de transações podem criar uma ligação.

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
| espaço de nomes | Utilize o espaço de nome do fornecedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma entrada do Gestor de Dados blockchain são **inputs**. |
| progenitor | O caminho para o observador ao qual a entrada está associada. Por exemplo, **observadores/mywatcher**. |
| é objeto cheio | Indica que as propriedades contêm opções para o recurso de entrada. |
| propriedades | Cadeia formatada JSON contendo propriedades para o recurso de entrada. Pode ser passado como uma corda ou um arquivo. |

### <a name="input-examples"></a>Exemplos de entrada

Configuração JSON exemplo para criar um recurso de entrada \<na\>região leste *dos EUA* que está ligado ao membro blockchain .

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
| localização | Região onde criar o recurso de entrada. |
| inputType | Tipo de livro-razão do membro do Serviço Azure Blockchain. Atualmente, **ethereum** é apoiado. |
| resourceId | Nó de transação ao qual a entrada está ligada. \<Substitua\>o \<ID\>de \<subscrição, o grupo de recursos e o membro\> blockchain pelos valores do recurso do nó de transação. A entrada liga-se ao nó de transações padrão para o membro do Serviço Azure Blockchain. |

Crie uma entrada chamada *myInput* para *mywatcher* usando uma corda JSON para configuração.

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

Crie uma entrada chamada *myInput* para *mywatcher* usando um ficheiro de configuração JSON.

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

Uma ligação de saída envia dados blockchain para a Rede de Eventos Azure. Pode enviar dados blockchain para um único destino ou enviar dados blockchain para vários destinos. O Blockchain Data Manager suporta várias ligações de saída do Tópico de Evento para qualquer instância do Gestor de Dados blockchain.

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
| resource-group | Nome de grupo de recursos onde criar o recurso de saída. |
| nome | Nome da saída. |
| espaço de nomes | Utilize o espaço de nome do fornecedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma saída do Gestor de Dados blockchain são **saídas**. |
| progenitor | O caminho para o observador ao qual a saída está associada. Por exemplo, **observadores/mywatcher**. |
| é objeto cheio | Indica que as propriedades contêm opções para o recurso de saída. |
| propriedades | Cadeia formatada JSON contendo propriedades para o recurso de saída. Pode ser passado como uma corda ou um arquivo. |

### <a name="output-examples"></a>Exemplos de saída

Configuração JSON exemplo para criar um recurso de saída na região leste\>dos *EUA* que está ligado a um tópico de grelha de eventos nomeado \<tópico de grelha de eventos .

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
| localização | Região onde criar o recurso de saída. |
| outputType | Tipo de saída. Atualmente, **o EventGrid** é apoiado. |
| resourceId | Recurso ao qual a saída está ligada. Substitua \<\>o \<ID\>de \<subscrição, o grupo de recursos e o membro\> blockchain pelos valores do recurso da grelha de eventos. |

Crie uma saída chamada *myoutput* para *mywatcher* que se conecta a um tópico de rede de eventos usando uma cadeia de configuração JSON.

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

Crie uma saída chamada *myoutput* para *mywatcher* que se conecta a um tópico de grelha de eventousando um ficheiro de configuração JSON.

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

Se adicionar uma aplicação blockchain, o Blockchain Data Manager descodifica o estado de eventos e propriedades para a aplicação. Caso contrário, apenas são enviados dados relativos a blocos brutos e transações brutas. O Blockchain Data Manager também descobre endereços de contrato quando o contrato é implementado. Pode adicionar várias aplicações blockchain a uma instância do Blockchain Data Manager.


> [!IMPORTANT]
> Atualmente, as aplicações blockchain que declaram tipos de [matriz](https://solidity.readthedocs.io/en/v0.5.12/types.html#arrays) solidez ou tipos de [mapeamento](https://solidity.readthedocs.io/en/v0.5.12/types.html#mapping-types) não são totalmente suportadas. As propriedades declaradas como tipos de matriz ou mapeamento não serão descodificadas em mensagens *ContractPropertiesMsg* ou *DecodedContractEventsMsg.*

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
| resource-group | Nome de grupo de recursos onde criar o recurso de aplicação. |
| nome | Nome da aplicação. |
| espaço de nomes | Utilize o espaço de nome do fornecedor **Microsoft.Blockchain.** |
| tipo de recurso | O tipo de recurso para uma aplicação blockchain Data Manager é **artefactos.** |
| progenitor | O caminho para o observador ao qual a aplicação está associada. Por exemplo, **observadores/mywatcher**. |
| é objeto cheio | Indica que as propriedades contêm opções para o recurso de aplicação. |
| propriedades | Cadeia formatada JSON contendo propriedades para o recurso de aplicação. Pode ser passado como uma corda ou um arquivo. |

### <a name="blockchain-application-examples"></a>Exemplos de aplicação blockchain

Configuração JSON exemplo para criar um recurso de aplicação na região *leste dos EUA* que monitoriza um contrato inteligente definido pelo contrato ABI e bytecode.

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
| localização | Região onde criar o recurso de aplicação. |
| artefactoTipo | Tipo de aplicação. Atualmente, a **EthereumSmartContract** é suportada. |
| abiFileUrl | URL para contrato inteligente ABI JSON ficheiro. Para obter mais informações sobre a obtenção do contrato ABI e a criação de um URL, consulte [Obter Contrato ABI e bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e Criar contrato ABI e [bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). |
| bytecodeFileUrl | URL para contrato inteligente implementado ficheiro JSON bytecode. Para obter mais informações sobre a obtenção do contrato inteligente implementado bytecode e a criação de um URL, consulte [Obter Contrato ABI e bytecode](data-manager-portal.md#get-contract-abi-and-bytecode) e Criar contrato ABI e [bytecode URL](data-manager-portal.md#create-contract-abi-and-bytecode-url). Nota: O Gestor de Dados blockchain requer o **código de envio implantado**. |
| consultaTargetTypes | Tipos de mensagens publicados. Especificar **ContractProperties** publica tipo de mensagem *ContractPropertiesMsg.* Especificar **ContratoSEvents** publica o tipo de mensagem *DecodedContractEventsMsg.* Nota: Os tipos de mensagens *RawBlockAndTransactionMsg* e *RawTransactionContractCreationMsg* são sempre publicados. |

Crie uma aplicação chamada *myApplication* para *mywatcher* que monitorize um contrato inteligente definido por uma cadeia JSON.

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

Crie uma aplicação chamada *myApplication* para *mywatcher* que assista a um contrato inteligente definido usando um ficheiro de configuração JSON.

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

Ao correr, uma instância do Blockchain Manager monitoriza os eventos blockchain a partir das inputs definidas e envia dados para as saídas definidas.

``` azurecli
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| action | Use **começar** a executar o observador. |
| ids | Identificação do recurso do Observador. \<Substitua\>o \<ID\>de \<Subscrição, o grupo de recursos e o nome\> do Observador pelos valores do recurso observador.|

### <a name="start-instance-example"></a>Iniciar exemplo de exemplo

Inicie uma instância de Blockchain Data Manager chamada *mywatcher*.

``` azurecli-interactive
az resource invoke-action \
                          --action start \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="stop-instance"></a>Parar instância

Pare uma instância do Gestor de Dados blockchain.

``` azurecli
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/<Watcher name>
```

| Parâmetro | Descrição |
|-----------|-------------|
| action | Use **stop** parar para parar o observador. |
| ids | Nome do observador. \<Substitua\>o \<ID\>de \<Subscrição, o grupo de recursos e o nome\> do Observador pelos valores do recurso observador. |

### <a name="stop-watcher-example"></a>Pare o exemplo do observador

Pare uma instância chamada *mywatcher.*

``` azurecli-interactive
az resource invoke-action \
                          --action stop \
                          --ids /subscriptions/<Subscription ID>/resourceGroups/<Resource group>/providers/Microsoft.Blockchain/watchers/mywatcher
```

## <a name="delete-instance"></a>Excluir instância

Elimine uma instância do Gestor de Dados blockchain.

``` azurecli
az resource delete \
                   --resource-group <Resource group> \
                   --name <Watcher name> \
                   --resource-type Microsoft.Blockchain/watchers
```

| Parâmetro | Descrição |
|-----------|-------------|
| resource-group | Nome de grupo de recursos do observador para apagar. |
| nome | Nome do observador para apagar. |
| tipo de recurso | O tipo de recurso para um observador de dados blockchain é **Microsoft.blockchain/watchers**. |

### <a name="delete-instance-example"></a>Eliminar exemplo de instância

Elimine uma instância chamada *mywatcher* no grupo de recursos *myRG.*

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
