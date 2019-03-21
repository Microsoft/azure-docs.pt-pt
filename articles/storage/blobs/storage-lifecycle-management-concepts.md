---
title: Gerir o ciclo de vida de armazenamento do Azure
description: Saiba como criar regras de política de ciclo de vida aos dados de classificação por vencimento de transição de acesso frequente para esporádico e de arquivo.
services: storage
author: yzheng-msft
ms.service: storage
ms.topic: article
ms.date: 11/04/2018
ms.author: yzheng
ms.subservice: common
ms.openlocfilehash: 93c19bc39f64df21dfa9db2490ab2103aba8191d
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086110"
---
# <a name="managing-the-azure-blob-storage-lifecycle-preview"></a>Gerir o ciclo de vida (pré-visualização) de armazenamento de Blobs do Azure

Os conjuntos de dados têm ciclos de vida exclusivos. Logo no início do ciclo de vida, as pessoas acessar alguns dados com frequência. Mas a necessidade de acesso cai significativamente à medida que a sua idade aumenta. Alguns dados permanecem ociosos, na cloud e são raramente acedidos armazenados uma vez. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados durante seus tempos de vida ativamente. Gestão de ciclo de vida do Azure armazenamento de BLOBs (pré-visualização) oferece uma política avançada e baseada em regras para contas de armazenamento de BLOBs e GPv2. Utilize a política para fazer a transição de seus dados para os escalões de acesso apropriado ou expirar no final do ciclo de vida dos dados.

A política de gestão do ciclo de vida permite-lhe:

- Faça a transição de blobs para uma camada de armazenamento mais esporádica (frequente para esporádico, frequente para arquivo ou acesso esporádico para arquivo) para otimizar o desempenho e custo
- Eliminar blobs no final dos respetivos ciclos de vida
- Definir regras para ser executado uma vez por dia ao nível da conta de armazenamento
- Aplicar regras a contentores ou um subconjunto de blobs (utilizando o prefixos de que filtros)

Considere o cenário em que um conjunto de dados obtém acesso freqüente nos estágios iniciais do ciclo de vida, mas, em seguida, apenas ocasionalmente após duas semanas. Além do primeiro mês, o conjunto de dados são raramente acedido. Neste cenário, é melhor armazenamento frequente nos estágios iniciais. Armazenamento de acesso esporádico é mais adequado para acesso ocasional, e o armazenamento de arquivo é a melhor opção de escalão após a sua idade aumenta mais de um mês. Ao ajustar camadas de armazenamento sentido para a idade dos dados, é possível projetar as opções de armazenamento menos dispendiosas para as suas necessidades. Para obter esta transição, regras de política de gestão do ciclo de vida estão disponíveis para mover dados de classificação por vencimento para escalões torna mais esporádica.

## <a name="storage-account-support"></a>Suporte de conta de armazenamento

A política de gestão do ciclo de vida está disponível com ambos os fins gerais v2 (GPv2) contas e contas de armazenamento de Blobs. No portal do Azure, pode atualizar uma conta de fins gerais (GPv1) existente para uma conta GPv2 através de um processo de um clique simples. Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preços 

A funcionalidade de gestão do ciclo de vida de é gratuita em pré-visualização. Os clientes são cobrados os custos de operação normais para o [listar os Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) chamadas de API. Para obter mais informações sobre preços, consulte [preços do Blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="register-for-preview"></a>Registar-se na pré-visualização 
Para se inscrever na pré-visualização pública, terá de submeter um pedido para registar esta funcionalidade para a sua subscrição. Normalmente, os pedidos são aprovados dentro de 72 horas. Após a aprovação, todas as novas e existentes BLOBs ou GPv2 contas de armazenamento nas seguintes regiões incluem a funcionalidade: E.U.A. oeste 2, EUA Centro-Oeste, E.U.A. Leste 2 e Europa Ocidental. Pré-visualização só suporta blob de blocos. Tal como acontece com a maior parte das visualizações, não deve utilizar esta funcionalidade para cargas de trabalho de produção até atingir GA.

Para submeter um pedido, execute os seguintes comandos do PowerShell ou a CLI.

### <a name="powershell"></a>PowerShell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Para submeter um pedido:

```powershell
Register-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage 
```
Pode verificar o estado de aprovação de registo com o seguinte comando:
```powershell
Get-AzProviderFeature -FeatureName DLM -ProviderNamespace Microsoft.Storage
```
Com a aprovação e registro adequadas, receberá o *registado* de estado quando submete pedidos anteriores.

### <a name="azure-cli"></a>CLI do Azure

Para submeter um pedido: 
```cli
az feature register --namespace Microsoft.Storage --name DLM
```
Pode verificar o estado de aprovação de registo com o seguinte comando:
```cli
az feature show --namespace Microsoft.Storage --name DLM
```
Com a aprovação e registro adequadas, receberá o *registado* de estado quando submete pedidos anteriores.


## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política 

Pode adicionar, editar ou remover uma política através do portal do Azure, [PowerShell](https://www.powershellgallery.com/packages/Az.Storage), [CLI do Azure](https://docs.microsoft.com/cli/azure/ext/storage-preview/storage/account/management-policy?view=azure-cli-latest#ext-storage-preview-az-storage-account-management-policy-create), [REST APIs](https://docs.microsoft.com/rest/api/storagerp/managementpolicies/createorupdate), ou ferramentas de cliente nos seguintes idiomas: [.NET ](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage/8.0.0-preview), [Python](https://pypi.org/project/azure-mgmt-storage/2.0.0rc3/), [node. js]( https://www.npmjs.com/package/azure-arm-storage/v/5.0.0), [Ruby](https://rubygems.org/gems/azure_mgmt_storage/versions/0.16.2). 

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione a conta de armazenamento.

3. Selecione **gestão de ciclo de vida (pré-visualização)** agrupados sob o serviço de BLOBs para ver ou alterar a sua política.

### <a name="powershell"></a>PowerShell

```powershell
$rules = '{ ... }'

Set-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName] -Policy $rules 

Get-AzStorageAccountManagementPolicy -ResourceGroupName [resourceGroupName] -StorageAccountName [storageAccountName]
```

### <a name="azure-cli"></a>CLI do Azure

```
az account set --subscription "[subscriptionName]”
az extension add --name storage-preview
az storage account management-policy show --resource-group [resourceGroupName] --account-name [accountName]
```

> [!NOTE]
> Se ativar as regras de firewall para a sua conta de armazenamento, pedidos de gestão do ciclo de vida podem ser bloqueados. Pode desbloquear estes pedidos fornecendo exceções. Para obter mais informações, consulte a secção de exceções na [configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

## <a name="policy"></a>Política

Uma política de gestão do ciclo de vida é uma coleção de regras num documento JSON:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "rule1",
      "type": "Lifecycle",
      "definition": {...}
    },
    {
      "name": "rule2",
      "type": "Lifecycle",
      "definition": {...}
    }
  ]
}
```


Uma política requer dois parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Notas |
|----------------|----------------|-------|
| versão        | Expressado como uma cadeia de caracteres `x.x` | O número de versão de pré-visualização é 0,5. |
| regras          | Uma matriz de objetos de regra | Pelo menos uma regra é necessária em cada política. Durante a pré-visualização, pode especificar até 4 regras de acordo com a política. |

Cada regra na política requer três parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Notas |
|----------------|----------------|-------|
| Name           | String | Um nome de regra pode incluir qualquer combinação de carateres alfanuméricos. Nome da regra diferencia maiúsculas de minúsculas. Tem de ser exclusivo dentro de uma política. |
| tipo           | Um valor de enumeração | O valor válido para a pré-visualização é `Lifecycle`. |
| definição     | Um objeto que define a regra de ciclo de vida | Cada definição é constituída por um conjunto de filtros e um conjunto de ação. |

## <a name="rules"></a>Regras

Cada definição da regra inclui um conjunto de filtros e um conjunto de ação. O [filtrar o conjunto](#rule-filters) limita as ações de regras para um determinado conjunto de objetos dentro de um contêiner ou nomes de objetos. O [conjunto de ação](#rule-actions) aplica-se a camada ou eliminar ações para o conjunto filtrado de objetos.

### <a name="sample-rule"></a>Exemplo de regra
A seguinte regra de exemplo filtra a conta para executar as ações apenas no `container1/foo`. Execute as seguintes ações para todos os objetos que estão dentro `container1` **AND** começa com `foo`: 

- Blob de escalão para acesso esporádico a camada de 30 dias após a última modificação
- Blob de camada para 90 dias após a última modificação da camada de arquivo
- Eliminar blob 2,555 dias (durante sete anos) após a última modificação
- Eliminar instantâneos de blob de 90 dias após a criação do instantâneo

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "ruleFoo",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 },
            "delete": { "daysAfterModificationGreaterThan": 2555 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}

```

### <a name="rule-filters"></a>Filtros de regra

Filtros de limitam as ações de regras a um subconjunto de blobs na conta de armazenamento. Se for definido mais do que um filtro, uma lógica `AND` é executado em todos os filtros.

Durante a pré-visualização, os filtros válidos incluem:

| Nome do filtro | Tipo de filtro | Notas | É Obrigatório |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enum predefinidos. | A pré-visualização da versão suporta apenas a `blockBlob`. | Sim |
| prefixMatch | Uma matriz de cadeias de caracteres para prefixos ser corresponder. Uma cadeia de caracteres de prefixo tem de começar com um nome de contentor. Por exemplo, se deseja correspondência com todos os blobs em "<https://myaccount.blob.core.windows.net/container1/foo/>..." para uma regra, é o prefixMatch `container1/foo`. | Se não definir prefixMatch, as regras são aplicadas a todos os blobs na conta. | Não |

### <a name="rule-actions"></a>Ações de regras

Ações são aplicadas para os blobs filtrados quando for cumprida a condição de execução.

Em pré-visualização, o gerenciamento de ciclo de vida suporta a disposição em camadas e eliminação de blobs e a eliminação de instantâneos de blob. Defina pelo menos uma ação para cada regra em blobs ou instantâneos de blob.

| Ação        | Base de Blob                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Suporta os blobs atualmente na camada de acesso frequente         | Não suportado |
| tierToArchive | Suporta os blobs atualmente na camada de acesso frequente ou esporádica | Não suportado |
| delete        | Suportadas                                   | Suportadas     |

> [!NOTE]
> Se definir mais de uma ação no mesmo blob, gerenciamento de ciclo de vida aplica-se a ação menos dispendiosa para o blob. Por exemplo, a ação `delete` é mais barato do que a ação `tierToArchive`. Ação `tierToArchive` é mais barato do que a ação `tierToCool`.

Em pré-visualização, as condições de execução da ação baseiam-se na idade. Os blobs bases utilizam a hora da última modificação para controlar a idade e a utilização de instantâneos a hora de criação de instantâneos de BLOBs para controlar a idade.

| Condição de ação de execução | Valor de condição | Descrição |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Valor de número inteiro que indica a idade em dias | Condição válida para ações de blob de base |
| daysAfterCreationGreaterThan     | Valor de número inteiro que indica a idade em dias | Condição válida para ações de instantâneo de blob | 

## <a name="examples"></a>Exemplos
Os exemplos seguintes demonstram como abordar cenários comuns com regras de política de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados de classificação por vencimento para uma camada mais esporádica

Este exemplo mostra como fazer a transição de blobs de blocos, o prefixo `container1/foo` ou `container2/bar`. A política de transições de blobs que ainda não foram modificadas em mais de 30 dias para armazenamento de acesso esporádico e não modificados em 90 dias para a camada de armazenamento de blobs:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "agingRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "container1/foo", "container2/bar" ]
        },
        "actions": {
          "baseBlob": {
            "tierToCool": { "daysAfterModificationGreaterThan": 30 },
            "tierToArchive": { "daysAfterModificationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

### <a name="archive-data-at-ingest"></a>Dados de arquivo na ingestão 

Alguns dados permanecem ociosos, na cloud e é raramente, se alguma vez, uma vez a acedeu armazenados. Arquive estes dados imediatamente uma vez que é ingerido. A política de ciclo de vida seguinte está configurada para arquivar dados na ingestão. Neste exemplo blobs na conta de armazenamento dentro do contentor de blocos de transições `archivecontainer` imediatamente numa camada de arquivo. A transição imediata é conseguida ao agir em blobs de 0 dias após a hora da última modificação:

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "archiveRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "archivecontainer" ]
        },
        "actions": {
          "baseBlob": {
              "tierToArchive": { "daysAfterModificationGreaterThan": 0 }
          }
        }
      }
    }
  ]
}

```

### <a name="expire-data-based-on-age"></a>Expirar dados com base na idade

Espera-se que alguns dados expirar dias ou meses após a criação para reduzir os custos ou atender aos requisitos da administração pública. Pode configurar uma política de gestão do ciclo de vida para expirar dados pela eliminação com base na idade de dados. O exemplo seguinte mostra uma política que elimina todos os blobs de blocos (com nenhuma prefixo especificado) anteriores a 365 dias.

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "expirationRule",
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 365 }
          }
        }
      }
    }
  ]
}
```

### <a name="delete-old-snapshots"></a>Eliminar instantâneos antigos

Para dados que são modificados e acedidos regularmente ao longo de seu ciclo de vida, os instantâneos são frequentemente utilizados para controlar as versões mais antigas dos dados. Pode criar uma política que elimina os instantâneos antigos com base na idade de instantâneo. A idade do instantâneo é determinada por avaliar o tempo de criação do instantâneo. Esta regra de política eliminações bloquear instantâneos de BLOBs no contentor `activedata` que são de 90 dias ou mais antigos após a criação do instantâneo.

```json
{
  "version": "0.5",
  "rules": [
    {
      "name": "snapshotRule",
      "type": "Lifecycle",
    "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "activedata" ]
        },
        "actions": {
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```
## <a name="faq---i-created-a-new-policy-why-are-the-actions-not-run-immediately"></a>FAQ - Criei uma nova política, por que as ações não serão executadas imediatamente? 

A plataforma executa a política de ciclo de vida de uma vez por dia. Depois de definir uma nova política, pode demorar até 24 horas para algumas ações (como disposição em camadas e eliminação) para iniciar e executar.  

## <a name="next-steps"></a>Passos Seguintes

Saiba como recuperar dados após a eliminação acidental:

- [Eliminação de forma recuperável para blobs de armazenamento do Azure](../blobs/storage-blob-soft-delete.md)
