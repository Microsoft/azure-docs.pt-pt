---
title: Gerenciando o ciclo de vida do armazenamento do Azure
description: Saiba como criar regras de política de ciclo de vida para fazer a transição de dados de envelhecimento de camadas quentes para frias e de arquivo morto.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: f5578d00d633b4b1ccce41236526e1696744f59f
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851779"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerir o ciclo de vida do Armazenamento de Blobs do Azure

Os conjuntos de dados têm ciclos de vida exclusivos. No início do ciclo de vida, as pessoas acessam alguns dados com frequência. Mas a necessidade de acesso cai drasticamente à medida que os dados são expirados. Alguns dados permanecem ociosos na nuvem e raramente são acessados uma vez armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente durante seus tempos de vida. O gerenciamento do ciclo de vida do armazenamento de BLOBs do Azure oferece uma política avançada baseada em regras para contas de armazenamento de BLOBs e GPv2. Use a política para fazer a transição dos dados para as camadas de acesso apropriadas ou expirar no final do ciclo de vida dos dados.

A política de gerenciamento do ciclo de vida permite:

- Fazer a transição de BLOBs para uma camada de armazenamento mais fria (quente para fria, quente para arquivamento ou fria para arquivo) para otimizar o desempenho e o custo
- Excluir BLOBs ao final de seus ciclos de vida
- Definir regras a serem executadas uma vez por dia no nível da conta de armazenamento
- Aplicar regras a contêineres ou a um subconjunto de BLOBs (usando prefixos como filtros)

Considere um cenário em que os dados recebem acesso frequente durante os estágios iniciais do ciclo de vida, mas apenas ocasionalmente após duas semanas. Além do primeiro mês, o conjunto de dados é raramente acessado. Nesse cenário, o armazenamento dinâmico é o melhor durante os estágios iniciais. O armazenamento frio é mais apropriado para acesso ocasional. O armazenamento de arquivos é a melhor opção de camada depois que os dados ficam em um mês. Ao ajustar as camadas de armazenamento em relação à idade dos dados, você pode criar as opções de armazenamento menos caras para suas necessidades. Para obter essa transição, as regras de política de gerenciamento do ciclo de vida estão disponíveis para mover os dados de vencimento para as camadas mais frias.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Suporte à conta de armazenamento

A política de gerenciamento do ciclo de vida está disponível com contas do Uso Geral v2 (GPv2), contas de armazenamento de BLOBs e contas de armazenamento de blob de blocos Premium. No portal do Azure, você pode atualizar uma conta de Uso Geral (GPv1) existente para uma conta do GPv2. Para obter mais informações sobre as contas de armazenamento, veja [Visão geral da conta de armazenamento do Azure](../common/storage-account-overview.md).  

## <a name="pricing"></a>Preços

O recurso de gerenciamento do ciclo de vida é gratuito. Os clientes são cobrados pelo custo de operação regular para os [blobs de lista](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [definem](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) chamadas de API de camada de BLOB. A operação de exclusão é gratuita. Para obter mais informações sobre preços, consulte [blocos de preços de blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilidade regional

O recurso de gerenciamento do ciclo de vida está disponível em todas as regiões do Azure.

## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política

Você pode adicionar, editar ou remover uma política usando qualquer um dos seguintes métodos:

* [Portal do Azure](https://portal.azure.com)
* [O Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [APIs REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Uma política pode ser lida ou gravada por completo. Não há suporte para atualizações parciais. 

> [!NOTE]
> Se você habilitar regras de firewall para sua conta de armazenamento, as solicitações de gerenciamento de ciclo de vida poderão ser bloqueadas. Você pode desbloquear essas solicitações fornecendo exceções para serviços confiáveis da Microsoft. Para obter mais informações, consulte a seção exceções em [Configurar firewalls e redes virtuais](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Este artigo mostra como gerenciar a política usando os métodos do portal e do PowerShell.  

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)

Há duas maneiras de adicionar uma política por meio do portal do Azure. 

* [Exibição de lista de portal do Azure](#azure-portal-list-view)
* [Exibição de código portal do Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Exibição de lista de portal do Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione sua conta de armazenamento.

3. Em **serviço blob**, selecione **Gerenciamento de ciclo de vida** para exibir ou alterar suas regras.

4. Selecione a guia **exibição de lista** .

5. Selecione **Adicionar regra** e preencha os campos de formulário do **conjunto de ações** . No exemplo a seguir, os BLOBs são movidos para o armazenamento frio, caso não tenham sido modificados por 30 dias.

   ![Página conjunto de ações de gerenciamento do ciclo de vida no portal do Azure](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selecione **conjunto de filtros** para adicionar um filtro opcional. Em seguida, selecione **procurar** para especificar um contêiner e uma pasta pela qual filtrar.

   ![Página conjunto de filtros de gerenciamento do ciclo de vida no portal do Azure](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selecione **examinar + adicionar** para examinar as configurações de política.

9. Selecione **Adicionar** para adicionar a nova política.

#### <a name="azure-portal-code-view"></a>Exibição de código portal do Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. Selecione **todos os recursos** e, em seguida, selecione sua conta de armazenamento.

3. Em **serviço blob**, selecione **Gerenciamento de ciclo de vida** para exibir ou alterar sua política.

4. O JSON a seguir é um exemplo de uma política que pode ser colada na guia **exibição de código** .

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

5. Selecione **Guardar**.

6. Para obter mais informações sobre este exemplo de JSON, consulte as seções [política](#policy) e [regras](#rules) .

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)

O script do PowerShell a seguir pode ser usado para adicionar uma política à sua conta de armazenamento. A variável `$rgname` deve ser inicializada com o nome do grupo de recursos. A variável `$accountName` deve ser inicializada com o nome da conta de armazenamento.

```powershell
#Install the latest module
Install-Module -Name Az -Repository PSGallery

#Initialize the following with your resource group and storage account names
$rgname = ""
$accountName = ""

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

# <a name="templatetabtemplate"></a>[Modelo](#tab/template)

Você pode definir o gerenciamento do ciclo de vida usando modelos de Azure Resource Manager. Aqui está um modelo de exemplo para implantar uma conta de armazenamento RA-GRS GPv2 com uma política de gerenciamento do ciclo de vida.

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

---

## <a name="policy"></a>Política

Uma política de gerenciamento do ciclo de vida é uma coleção de regras em um documento JSON:

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
| `rules`        | Uma matriz de objetos de regra | Pelo menos uma regra é necessária em uma política. Você pode definir até 100 regras em uma política.|

Cada regra na política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Notas | Obrigatório |
|----------------|----------------|-------|----------|
| `name`         | String |Um nome de regra pode incluir até 256 caracteres alfanuméricos. O nome da regra diferencia maiúsculas de minúsculas.  Ele deve ser exclusivo dentro de uma política. | Verdadeiro |
| `enabled`      | Booleano | Um booliano opcional para permitir que uma regra seja temporariamente desabilitada. O valor padrão será true se não estiver definido. | Falso | 
| `type`         | Um valor de enumeração | O tipo válido atual é `Lifecycle`. | Verdadeiro |
| `definition`   | Um objeto que define a regra de ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ações. | Verdadeiro |

## <a name="rules"></a>Regras

Cada definição de regra inclui um conjunto de filtros e um conjunto de ações. O [conjunto de filtros](#rule-filters) limita as ações de regra a um determinado conjunto de objetos dentro de um contêiner ou nomes de objetos. O [conjunto de ações](#rule-actions) aplica a camada ou as ações de exclusão ao conjunto filtrado de objetos.

### <a name="sample-rule"></a>Regra de exemplo

A regra de exemplo a seguir filtra a conta para executar as ações em objetos que existem dentro `container1` e começam com `foo`.  

- Blob de camada para camada fria 30 dias após a última modificação
- Camada blob para o arquivo de nível 90 dias após a última modificação
- Excluir o blob 2.555 dias (sete anos) após a última modificação
- Excluir instantâneos de blob 90 dias após a criação do instantâneo

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

Filtra ações de regra de limite para um subconjunto de BLOBs na conta de armazenamento. Se mais de um filtro for definido, um `AND` lógico será executado em todos os filtros.

Os filtros incluem:

| Nome do filtro | Tipo de filtro | Notas | É necessário |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enumeração predefinidos. | A versão atual oferece suporte a `blockBlob`. | Sim |
| prefixMatch | Uma matriz de cadeias de caracteres para correspondência de prefixos. Cada regra pode definir até 10 prefixos. Uma cadeia de caracteres de prefixo deve começar com um nome de contêiner. Por exemplo, se você quiser corresponder a todos os BLOBs em `https://myaccount.blob.core.windows.net/container1/foo/...` para uma regra, o prefixMatch será `container1/foo`. | Se você não definir prefixMatch, a regra se aplicará a todos os BLOBs na conta de armazenamento.  | Não |

### <a name="rule-actions"></a>Ações de regra

As ações são aplicadas aos BLOBs filtrados quando a condição de execução é atendida.

O gerenciamento do ciclo de vida dá suporte a camadas e exclusão de BLOBs e exclusão de instantâneos de BLOB. Defina pelo menos uma ação para cada regra em BLOBs ou instantâneos de BLOB.

| Ação        | Blob de base                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | Suporte a BLOBs atualmente na camada quente         | Não suportado |
| tierToArchive | Suporte a BLOBs atualmente na camada quente ou fria | Não suportado |
| delete        | Suportadas                                   | Suportadas     |

>[!NOTE]
>Se você definir mais de uma ação no mesmo BLOB, o gerenciamento do ciclo de vida aplicará a ação menos dispendiosa ao blob. Por exemplo, a ação `delete` é mais barata que a ação `tierToArchive`. A ação `tierToArchive` é mais barata que a ação `tierToCool`.

As condições de execução se baseiam na idade. Os blobs de base usam a hora da última modificação para acompanhar a idade e os instantâneos de blob usam o tempo de criação do instantâneo para acompanhar a idade.

| Condição de execução de ação             | Valor da condição                          | Descrição                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| daysAfterModificationGreaterThan | Valor inteiro que indica a idade em dias | A condição para ações de blob de base     |
| daysAfterCreationGreaterThan     | Valor inteiro que indica a idade em dias | A condição para ações de instantâneo de BLOB |

## <a name="examples"></a>Exemplos

Os exemplos a seguir demonstram como tratar cenários comuns com regras de política de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados de vencimento para uma camada mais fria

Este exemplo mostra como fazer a transição de blobs de bloco prefixados com `container1/foo` ou `container2/bar`. A política faz a transição de BLOBs que não foram modificados em mais de 30 dias para o armazenamento frio, e os BLOBs não são modificados em 90 dias para a camada de arquivo morto:

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

### <a name="archive-data-at-ingest"></a>Arquivar dados em ingestão

Alguns dados permanecem ociosos na nuvem e raramente são acessados uma vez armazenados. A política de ciclo de vida a seguir é configurada para arquivar dados após sua ingestão. Este exemplo faz a transição de blobs de blocos na conta de armazenamento dentro do contêiner `archivecontainer` para uma camada de arquivo morto. A transição é realizada agindo em BLOBs 0 dias após a hora da última modificação:

> [!NOTE] 
> É recomendável carregar seus BLOBs diretamente a camada de arquivo para ser mais eficiente. Você pode usar o cabeçalho x-MS-acesso-Tier para [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) ou [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) com a versão REST 2018-11-09 e mais recente ou nossas bibliotecas de cliente de armazenamento de blob mais recentes. 

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

Espera-se que alguns dados expirem dias ou meses após a criação. Você pode configurar uma política de gerenciamento de ciclo de vida para expirar dados por exclusão com base na idade dos dados. O exemplo a seguir mostra uma política que exclui todos os blobs de bloco com mais de 365 dias.

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

### <a name="delete-old-snapshots"></a>Excluir instantâneos antigos

Para dados que são modificados e acessados regularmente durante todo o seu tempo de vida, os instantâneos costumam ser usados para rastrear versões mais antigas dos dados. Você pode criar uma política que exclui os instantâneos antigos com base na duração do instantâneo. A idade do instantâneo é determinada pela avaliação da hora de criação do instantâneo. Esta regra de política exclui instantâneos de blob de blocos dentro de `activedata` de contêiner que são 90 dias ou mais antigos após a criação do instantâneo.

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

**Eu criei uma nova política, por que as ações não são executadas imediatamente?**  
A plataforma executa a política de ciclo de vida uma vez por dia. Depois de configurar uma política, pode levar até 24 horas para que algumas ações sejam executadas pela primeira vez.  

**Se eu atualizar uma política existente, quanto tempo levará para que as ações sejam executadas?**  
A política atualizada leva até 24 horas para entrar em vigor. Depois que a política estiver em vigor, poderá levar até 24 horas para que as ações sejam executadas. Portanto, a política pode levar até 48 horas para ser executada.   

**Eu resalimentava manualmente um blob arquivado, como impedir que ele fosse movido de volta para a camada de arquivamento temporariamente?**  
Quando um blob é movido de uma camada de acesso para outra, sua hora da última modificação não é alterada. Se você reidratar manualmente um blob arquivado na camada quente, ele seria movido de volta para a camada de arquivo pelo mecanismo de gerenciamento do ciclo de vida. Desabilite a regra que afeta esse blob temporariamente para impedir que ele seja arquivado novamente. Habilite novamente a regra quando o blob puder ser movido com segurança de volta para a camada de arquivo morto. Você também poderá copiar o blob para outro local se ele precisar permanecer na camada quente ou fria permanentemente.

## <a name="next-steps"></a>Passos seguintes

Saiba como recuperar dados após a exclusão acidental:

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../blobs/storage-blob-soft-delete.md)
