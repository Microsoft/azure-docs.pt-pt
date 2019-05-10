---
title: Gerir o ciclo de vida de armazenamento do Azure
description: Saiba como criar regras de política de ciclo de vida aos dados de classificação por vencimento de transição de acesso frequente para esporádico e de arquivo.
services: storage
author: mhopkins-msft
ms.service: storage
ms.topic: conceptual
ms.date: 4/29/2019
ms.author: mhopkins
ms.reviewer: yzheng
ms.subservice: common
ms.openlocfilehash: 560f7eb8a8809cdd6ef410a610be9806f9709754
ms.sourcegitcommit: 6f043a4da4454d5cb673377bb6c4ddd0ed30672d
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/08/2019
ms.locfileid: "65409959"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerir o ciclo de vida de armazenamento de Blobs do Azure

Os conjuntos de dados têm ciclos de vida exclusivos. Logo no início do ciclo de vida, as pessoas acessar alguns dados com frequência. Mas a necessidade de acesso cai significativamente à medida que a sua idade aumenta. Alguns dados permanecem ociosos, na cloud e são raramente acedidos armazenados uma vez. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados durante seus tempos de vida ativamente. A gestão de ciclo de vida de armazenamento de Blobs do Azure oferece uma política avançada e baseada em regras para contas de armazenamento de BLOBs e GPv2. Utilize a política para fazer a transição de seus dados para os escalões de acesso apropriado ou expirar no final do ciclo de vida dos dados.

A política de gestão do ciclo de vida permite-lhe:

- Faça a transição de blobs para uma camada de armazenamento mais esporádica (frequente para esporádico, frequente para arquivo ou acesso esporádico para arquivo) para otimizar o desempenho e custo
- Eliminar blobs no final dos respetivos ciclos de vida
- Definir regras para ser executado uma vez por dia ao nível da conta de armazenamento
- Aplicar regras a contentores ou um subconjunto de blobs (utilizando o prefixos de que filtros)

Considere o cenário em que um conjunto de dados obtém acesso freqüente nos estágios iniciais do ciclo de vida, mas, em seguida, apenas ocasionalmente após duas semanas. Além do primeiro mês, o conjunto de dados são raramente acedido. Neste cenário, é melhor armazenamento frequente nos estágios iniciais. Armazenamento de acesso esporádico é mais adequado para acesso ocasional, e o armazenamento de arquivo é a melhor opção de escalão após a sua idade aumenta mais de um mês. Ao ajustar camadas de armazenamento sentido para a idade dos dados, é possível projetar as opções de armazenamento menos dispendiosas para as suas necessidades. Para obter esta transição, regras de política de gestão do ciclo de vida estão disponíveis para mover dados de classificação por vencimento para escalões torna mais esporádica.

## <a name="storage-account-support"></a>Suporte de conta de armazenamento

A política de gestão do ciclo de vida está disponível com ambos os fins gerais v2 (GPv2) contas e contas de armazenamento de Blobs. No portal do Azure, pode atualizar uma conta de fins gerais (GPv1) existente para uma conta GPv2 através de um processo de um clique simples. Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preços 

A funcionalidade de gestão do ciclo de vida de é gratuita. Os clientes são cobrados os custos de operação normais para o [listar os Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [Set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) chamadas de API. Operação de eliminação é gratuita. Para obter mais informações sobre preços, consulte [preços do Blob de blocos](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilidade regional 
A funcionalidade de gestão do ciclo de vida está disponível em todas as regiões públicas do Azure. 


## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política 

Pode adicionar, editar ou remover uma política com o portal do Azure, [do Azure PowerShell](https://github.com/Azure/azure-powershell/releases), a CLI do Azure, [REST APIs](https://docs.microsoft.com/rest/api/storagerp/managementpolicies), ou uma ferramenta de cliente. Este artigo mostra como gerir a política com o portal e métodos do PowerShell.  

> [!NOTE]
> Se ativar as regras de firewall para a sua conta de armazenamento, pedidos de gestão do ciclo de vida podem ser bloqueados. Pode desbloquear estes pedidos fornecendo exceções. A omissão necessária são: `Logging,  Metrics,  AzureServices`. Para obter mais informações, consulte a secção de exceções na [configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

### <a name="azure-portal"></a>Portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione a conta de armazenamento.

3. Sob **serviço Blob**, selecione **gestão de ciclo de vida** para ver ou alterar a sua política.

### <a name="powershell"></a>PowerShell

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery 

#Create a new action object

$action = Add-AzStorageAccountManagementPolicyAction -BaseBlobAction Delete -daysAfterModificationGreaterThan 2555
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToArchive -daysAfterModificationGreaterThan 90
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -BaseBlobAction TierToCool -daysAfterModificationGreaterThan 30
$action = Add-AzStorageAccountManagementPolicyAction -InputObject $action -SnapshotAction Delete -daysAfterCreationGreaterThan 90

# Create a new filter object
# PowerShell automatically sets BlobType as “blockblob” because it is the only available option currently
$filter = New-AzStorageAccountManagementPolicyFilter -PrefixMatch ab,cd 

#Create a new rule object
#PowerShell automatically sets Type as “Lifecycle” because it is the only available option currently
$rule1 = New-AzStorageAccountManagementPolicyRule -Name Test -Action $action -Filter $filter

#Set the policy 
$policy = Set-AzStorageAccountManagementPolicy -ResourceGroupName $rgname -StorageAccountName $accountName -Rule $rule1

```
## <a name="arm-template-with-lifecycle-management-policy"></a>Modelo ARM com a política de gestão do ciclo de vida

Pode definir e implementar a gestão de ciclo de vida como parte da sua implementação de solução do Azure através de modelos ARM. A seguir é um modelo de exemplo para implementar uma conta de armazenamento RA-GRS GPv2 com uma política de gestão do ciclo de vida. 

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {},
  "variables": {
    "storageAccountName": "[uniqueString(resourceGroup().id)]"
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "name": "[variables('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "apiVersion": "2019-04-01",
      "sku": {
        "name": "Standard_RAGRS"
      },
      "kind": "StorageV2",
      "properties": {
        "networkAcls": {}
      }
    },
    {
      "name": "[concat(variables('storageAccountName'), '/default')]",
      "type": "Microsoft.Storage/storageAccounts/managementPolicies",
      "apiVersion": "2019-04-01",
      "dependsOn": [
        "[variables('storageAccountName')]"
      ],
      "properties": {
        "policy": {...}
      }
    }
  ],
  "outputs": {}
}
```

## <a name="policy"></a>Política

Uma política de gestão do ciclo de vida é uma coleção de regras num documento JSON:

```json
{
  "rules": [
    {
      "name": "rule1",
      "enabled": true,
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


Uma política é uma coleção de regras:

| Nome do parâmetro | Tipo de parâmetro | Notas |
|----------------|----------------|-------|
| regras          | Uma matriz de objetos de regra | Pelo menos uma regra é necessária uma política. Pode definir regras de até 100 numa política.|

Cada regra na política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Notas | Necessário |
|----------------|----------------|-------|----------|
| name           | String |Um nome de regra pode incluir até 256 carateres de alfanuméricos. Nome da regra diferencia maiúsculas de minúsculas.  Tem de ser exclusivo dentro de uma política. | Verdadeiro |
| enabled | Boolean | Um booleano opcional para permitir que uma regra para ser temporário desativada. Valor predefinido é verdadeiro se não estiver definido. | Falso | 
| tipo           | Um valor de enumeração | O tipo de válido atual for `Lifecycle`. | Verdadeiro |
| definição     | Um objeto que define a regra de ciclo de vida | Cada definição é constituída por um conjunto de filtros e um conjunto de ação. | Verdadeiro |

## <a name="rules"></a>Regras

Cada definição da regra inclui um conjunto de filtros e um conjunto de ação. O [filtrar o conjunto](#rule-filters) limita as ações de regras para um determinado conjunto de objetos dentro de um contêiner ou nomes de objetos. O [conjunto de ação](#rule-actions) aplica-se a camada ou eliminar ações para o conjunto filtrado de objetos.

### <a name="sample-rule"></a>Exemplo de regra
A seguinte regra de exemplo filtra a conta para executar as ações em objetos que estão dentro `container1` **AND** começar `foo`.  

- Blob de escalão para acesso esporádico a camada de 30 dias após a última modificação
- Blob de camada para 90 dias após a última modificação da camada de arquivo
- Eliminar blob 2,555 dias (durante sete anos) após a última modificação
- Eliminar instantâneos de blob de 90 dias após a criação do instantâneo

```json
{
  "rules": [
    {
      "name": "ruleFoo",
      "enabled": true,
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

Filtros válidos incluem:

| Nome do filtro | Tipo de filtro | Notas | É Obrigatório |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enum predefinidos. | A versão atual suporta `blockBlob`. | Sim |
| prefixMatch | Uma matriz de cadeias de caracteres para prefixos ser corresponder. Cada regra pode definir até 10 prefixos. Uma cadeia de caracteres de prefixo tem de começar com um nome de contentor. Por exemplo, se deseja correspondência com todos os blobs em "https://myaccount.blob.core.windows.net/container1/foo/..." para uma regra, é o prefixMatch `container1/foo`. | Se não definir prefixMatch, a regra se aplica a todos os blobs na conta de armazenamento.  | Não |

### <a name="rule-actions"></a>Ações de regras

Ações são aplicadas para os blobs filtrados quando for cumprida a condição de execução.

Gestão de ciclo de vida oferece suporte a disposição em camadas e eliminação de blobs e a eliminação de instantâneos de blob. Defina pelo menos uma ação para cada regra em blobs ou instantâneos de blob.

| Acção        | Base de Blob                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Suporta os blobs atualmente na camada de acesso frequente         | Não suportado |
| tierToArchive | Suporta os blobs atualmente na camada de acesso frequente ou esporádica | Não suportado |
| eliminar        | Suportadas                                   | Suportadas     |

>[!NOTE] 
>Se definir mais de uma ação no mesmo blob, gerenciamento de ciclo de vida aplica-se a ação menos dispendiosa para o blob. Por exemplo, a ação `delete` é mais barato do que a ação `tierToArchive`. Ação `tierToArchive` é mais barato do que a ação `tierToCool`.

As condições de execução baseiam-se a idade. Os blobs bases utilizam a hora da última modificação para controlar a idade e a utilização de instantâneos a hora de criação de instantâneos de BLOBs para controlar a idade.

| Executar a condição de ação | Valor de condição | Descrição |
|----------------------------|-----------------|-------------|
| daysAfterModificationGreaterThan | Valor de número inteiro que indica a idade em dias | Condição válida para ações de blob de base |
| daysAfterCreationGreaterThan     | Valor de número inteiro que indica a idade em dias | Condição válida para ações de instantâneo de blob | 

## <a name="examples"></a>Exemplos
Os exemplos seguintes demonstram como abordar cenários comuns com regras de política de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados de classificação por vencimento para uma camada mais esporádica

Este exemplo mostra como fazer a transição de blobs de blocos, o prefixo `container1/foo` ou `container2/bar`. A política de transições de blobs que ainda não foram modificadas em mais de 30 dias para armazenamento de acesso esporádico e não modificados em 90 dias para a camada de armazenamento de blobs:

```json
{
  "rules": [
    {
      "name": "agingRule",
      "enabled": true,
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

Alguns dados permanecem ociosos, na cloud e é raramente, se alguma vez, uma vez a acedeu armazenados. A política de ciclo de vida seguinte está configurada para arquivar dados uma vez que é ingerido. Neste exemplo blobs na conta de armazenamento dentro do contentor de blocos de transições `archivecontainer` numa camada de arquivo. A transição é conseguida ao agir em blobs de 0 dias após a hora da última modificação:

```json
{
  "rules": [
    {
      "name": "archiveRule",
      "enabled": true,
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
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
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
  "rules": [
    {
      "name": "snapshotRule",
      "enabled": true,
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
## <a name="faq"></a>FAQ 
**Criei uma nova política, por que as ações não serão executadas imediatamente?**  
A plataforma executa a política de ciclo de vida de uma vez por dia. Depois de configurar uma política, pode demorar até 24 horas para algumas ações (como disposição em camadas e eliminação) para ser executado pela primeira vez.  

## <a name="next-steps"></a>Passos Seguintes

Saiba como recuperar dados após a eliminação acidental:

- [Eliminação de forma recuperável para blobs de armazenamento do Azure](../blobs/storage-blob-soft-delete.md)
