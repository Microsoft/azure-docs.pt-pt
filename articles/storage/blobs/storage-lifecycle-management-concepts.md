---
title: Gestão do ciclo de vida do Armazenamento Azure
description: Aprenda a criar regras de política de ciclo de vida para transitar dados de envelhecimento de hot para cool e archive tiers.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 05/21/2019
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.openlocfilehash: 238c12baf55b525a24107a727d09588ef06a6bef
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77598311"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerir o ciclo de vida do Armazenamento de Blobs do Azure

Os conjuntos de dados têm ciclos de vida únicos. No início do ciclo de vida, as pessoas acedem frequentemente a alguns dados. Mas a necessidade de acesso diminui drasticamente à medida que os dados envelhecem. Alguns dados permanecem inativos na nuvem e raramente são acedidos uma vez armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente ao longo da sua vida. A gestão do ciclo de vida de armazenamento Azure Blob oferece uma política rica e baseada em regras para contas de armazenamento GPv2 e Blob. Utilize a política para transitar os seus dados para os níveis de acesso adequados ou expirar no final do ciclo de vida dos dados.

A política de gestão do ciclo de vida permite::

- Bolhas de transição para um nível de armazenamento mais frio (quente para arrefecer, quente para arquivar ou fresco para arquivar) para otimizar para o desempenho e custo
- Eliminar bolhas no final dos seus ciclos de vida
- Definir regras a executar uma vez por dia ao nível da conta de armazenamento
- Aplicar regras aos contentores ou a um subconjunto de bolhas (utilizando prefixos como filtros)

Considere um cenário em que os dados tenham acesso frequente durante as fases iniciais do ciclo de vida, mas apenas ocasionalmente após duas semanas. Para além do primeiro mês, o conjunto de dados raramente é acedido. Neste cenário, o armazenamento quente é o melhor durante as fases iniciais. O armazenamento legal é mais adequado para acesso ocasional. O armazenamento de arquivo é a melhor opção de nível após a idade dos dados ao longo de um mês. Ao ajustar os níveis de armazenamento em relação à idade dos dados, pode desenhar as opções de armazenamento menos dispendiosas para as suas necessidades. Para alcançar esta transição, estão disponíveis regras de política de gestão de ciclos de vida para mover dados de envelhecimento para níveis mais frios.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-account-support"></a>Suporte de conta de armazenamento

A política de gestão do ciclo de vida está disponível com contas General Purpose v2 (GPv2), contas de armazenamento Blob e contas de armazenamento Premium Block Blob. No portal Azure, pode atualizar uma conta de Propósito Geral (GPv1) existente para uma conta GPv2. Para obter mais informações sobre contas de armazenamento, consulte a visão geral da conta de [armazenamento do Azure.](../common/storage-account-overview.md)  

## <a name="pricing"></a>Preços

A funcionalidade de gestão do ciclo de vida é gratuita. Os clientes são cobrados o custo de operação regular para as chamadas [List Blobs](https://docs.microsoft.com/rest/api/storageservices/list-blobs) e [set Blob Tier](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) API. Eliminar a operação é gratuito. Para obter mais informações sobre preços, consulte [preços block Blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="regional-availability"></a>Disponibilidade regional

A funcionalidade de gestão do ciclo de vida está disponível em todas as regiões do Azure.

## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política

Pode adicionar, editar ou remover uma apólice utilizando qualquer um dos seguintes métodos:

* [Portal do Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [APIs REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Uma política pode ser lida ou escrita na íntegra. As atualizações parciais não são suportadas. 

> [!NOTE]
> Se ativar as regras de firewall para a sua conta de armazenamento, os pedidos de gestão do ciclo de vida podem ser bloqueados. Pode desbloquear estes pedidos fornecendo exceções para serviços confiáveis da Microsoft. Para mais informações, consulte a secção Exceções em [firewalls e redes virtuais Configuradas](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions).

Este artigo mostra como gerir a política utilizando os métodos portal e PowerShell.  

# <a name="portal"></a>[Portal](#tab/azure-portal)

Há duas formas de adicionar uma política através do portal Azure. 

* [Vista da lista do portal Azure](#azure-portal-list-view)
* [Vista de código do portal Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Vista da lista do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal Azure, procure e selecione a sua conta de armazenamento. 

3. No **serviço Blob,** selecione **gestão do Ciclo de Vida** para visualizar ou alterar as suas regras.

4. Selecione o separador **de visualização lista.**

5. Selecione **Adicionar regra** e, em seguida, preencher os campos de formulário de **conjunto de ação.** No exemplo seguinte, as bolhas são movidas para armazenamento fresco se não tiverem sido modificadas por 30 dias.

   ![Página definida de ação de gestão de ciclo de vida no portal Azure](media/storage-lifecycle-management-concepts/lifecycle-management-action-set.png)

6. Selecione **o conjunto de filtros** para adicionar um filtro opcional. Em seguida, **selecione 'Navegar'** para especificar um recipiente e uma pasta para filtrar.

   ![Página definida de filtro de gestão de ciclo de vida no portal Azure](media/storage-lifecycle-management-concepts/lifecycle-management-filter-set-browse.png)

8. Selecione **Review + adicione** para rever as definições de política.

9. Selecione **Adicionar** para adicionar a nova política.

#### <a name="azure-portal-code-view"></a>Vista de código do portal Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

2. No portal Azure, procure e selecione a sua conta de armazenamento.

3. No **Serviço Blob,** selecione **gestão do Ciclo de Vida** para visualizar ou alterar a sua política.

4. O JSON seguinte é um exemplo de uma política que pode ser colada no separador visão de **Código.**

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

6. Para obter mais informações sobre este exemplo da JSON, consulte as secções [política](#policy) e [de regras.](#rules)

# <a name="powershell"></a>[Powershell](#tab/azure-powershell)

O seguinte script PowerShell pode ser usado para adicionar uma apólice à sua conta de armazenamento. A `$rgname` variável deve ser inicializada com o nome do seu grupo de recursos. A `$accountName` variável deve ser inicializada com o nome da sua conta de armazenamento.

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

# <a name="template"></a>[Modelo](#tab/template)

Pode definir a gestão do ciclo de vida utilizando modelos do Gestor de Recursos Azure. Aqui está um modelo de amostra para implementar uma conta de armazenamento RA-GRS GPv2 com uma política de gestão de ciclo de vida.

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

Uma política de gestão de ciclos de vida é uma coleção de regras num documento da JSON:

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

| Nome do parâmetro | Tipo parâmetro | Notas |
|----------------|----------------|-------|
| `rules`        | Uma variedade de objetos de regra | Pelo menos uma regra é exigida numa política. Pode definir até 100 regras numa política.|

Cada regra dentro da política tem vários parâmetros:

| Nome do parâmetro | Tipo parâmetro | Notas | Necessário |
|----------------|----------------|-------|----------|
| `name`         | String |Um nome de regra pode incluir até 256 caracteres alfanuméricos. O nome da regra é sensível ao caso.  Deve ser único dentro de uma política. | Verdadeiro |
| `enabled`      | Booleano | Uma boolean opcional para permitir que uma regra seja desativada temporária. O valor padrão é verdadeiro se não estiver definido. | Falso | 
| `type`         | Um valor enum | O tipo válido `Lifecycle`atual é . | Verdadeiro |
| `definition`   | Um objeto que define a regra do ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ação. | Verdadeiro |

## <a name="rules"></a>Regras

Cada definição de regra inclui um conjunto de filtros e um conjunto de ação. O [conjunto de filtros](#rule-filters) limita as ações de regra a um determinado conjunto de objetos dentro de um recipiente ou nomes de objetos. O [conjunto de ação](#rule-actions) aplica o nível ou apaga as ações ao conjunto de objetos filtrados.

### <a name="sample-rule"></a>Regra da amostra

A regra da amostra seguinte filtra a conta para `container1` executar `foo`as ações em objetos que existem no interior e começar com .  

>[!NOTE]
>A gestão do ciclo de vida apenas suporta o tipo de bloco blob.  

- Blob tier para arrefecer nível 30 dias após a última modificação
- Blob de nível para arquivar nível 90 dias após a última modificação
- Eliminar a bolha 2.555 dias (sete anos) após a última modificação
- Eliminar imagens blob 90 dias após a criação de instantâneos

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

### <a name="rule-filters"></a>Filtros de regras

Os filtros limitam as ações de regra a um subconjunto de bolhas dentro da conta de armazenamento. Se for definido mais de `AND` um filtro, corre-se um plano lógico em todos os filtros.

Os filtros incluem:

| Nome do filtro | Tipo de filtro | Notas | É necessário |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma variedade de valores enum pré-definidos. | A versão atual `blockBlob`suporta . | Sim |
| prefixoMatch | Uma série de cordas para os prefixos serem compatíveis. Cada regra pode definir até 10 prefixos. Uma corda de prefixo deve começar com um nome de recipiente. Por exemplo, se quiser combinar todas `https://myaccount.blob.core.windows.net/container1/foo/...` as bolhas por regra, `container1/foo`o prefixoMatch é . | Se não definir o prefixoMatch, a regra aplica-se a todas as bolhas dentro da conta de armazenamento.  | Não |

### <a name="rule-actions"></a>Ações de regras

As ações são aplicadas às bolhas filtradas quando a condição de execução é satisfeita.

A gestão do ciclo de vida suporta o tiering e a eliminação de bolhas e a eliminação de instantâneos blob. Defina pelo menos uma ação para cada regra em bolhas ou imagens de bolhas.

| Ação        | Bolha base                                   | Instantâneo      |
|---------------|---------------------------------------------|---------------|
| tierToCool    | As bolhas de suporte atualmente em nível quente         | Não suportado |
| tierToArchive | Apoie bolhas atualmente em nível quente ou fresco | Não suportado |
| delete        | Suportado                                   | Suportado     |

>[!NOTE]
>Se definir mais do que uma ação na mesma bolha, a gestão do ciclo de vida aplica a ação menos dispendiosa para a bolha. Por exemplo, `delete` a ação `tierToArchive`é mais barata do que a ação. A `tierToArchive` ação é `tierToCool`mais barata do que a ação.

As condições de execução baseiam-se na idade. As bolhas de base usam o último tempo modificado para rastrear a idade, e as imagens blob usam o tempo de criação de instantâneos para rastrear a idade.

| Condição de execução de ação             | Valor da condição                          | Descrição                             |
|----------------------------------|------------------------------------------|-----------------------------------------|
| diasAfterModificationGreaterThan | Valor inteiro indicando a idade em dias | A condição para as ações de bolha de base     |
| daysAfterCreationGreaterThan     | Valor inteiro indicando a idade em dias | A condição para as ações de instantâneo blob |

## <a name="examples"></a>Exemplos

Os exemplos que se seguem demonstram como abordar cenários comuns com regras políticas de ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados de envelhecimento para um nível mais frio

Este exemplo mostra como transitar blocos `container1/foo` `container2/bar`blobs pré-fixados com ou . A política transita bolhas que não foram modificadas há mais de 30 dias para arrefecer o armazenamento, e bolhas não modificadas em 90 dias para o nível de arquivo:

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

### <a name="archive-data-after-ingest"></a>Arquivar dados após ingerir

Alguns dados permanecem inativos na nuvem e raramente são, se é que alguma vez, acedidos uma vez armazenados. A seguinte política de ciclo de vida é configurada para arquivar dados logo após a sua ingestão. Este exemplo transita bolhas de blocona `archivecontainer` conta de armazenamento dentro do contentor para um nível de arquivo. A transição é realizada agindo em blobs 0 dias após o último tempo modificado:

> [!NOTE] 
> Recomenda-se que carregue as suas bolhas diretamente no nível de arquivo para ser mais eficiente. Pode utilizar o cabeçalho x-ms-asss-tier para [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) ou [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) com a versão REST 2018-11-09 e mais recente ou as nossas mais recentes bibliotecas de clientes de armazenamento blob. 

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

Espera-se que alguns dados expirem dias ou meses após a criação. Pode configurar uma política de gestão do ciclo de vida para expirar dados por eliminação com base na idade dos dados. O exemplo que se segue mostra uma política que elimina todas as bolhas de blococom mais de 365 dias.

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

### <a name="delete-old-snapshots"></a>Eliminar fotos antigas

Para dados que são modificados e acedidos regularmente ao longo da sua vida útil, as imagens são frequentemente usadas para rastrear versões mais antigas dos dados. Pode criar uma política que apague imagens antigas com base na idade instantânea. A idade instantânea é determinada avaliando o tempo de criação de instantâneos. Esta regra de política elimina os `activedata` instantâneos de blocos dentro do recipiente que têm 90 dias ou mais após a criação instantânea.

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

**Criei uma nova política, porque é que as ações não são executadas imediatamente?**  
A plataforma executa a política de ciclo de vida uma vez por dia. Uma vez configurado uma apólice, pode levar até 24 horas para algumas ações correrem pela primeira vez.  

**Se eu atualizar uma política existente, quanto tempo demora as ações a decorrer?**  
A política atualizada leva até 24 horas para entrar em vigor. Uma vez em vigor a política, pode levar até 24 horas para que as ações se descorram. Por conseguinte, as ações políticas podem demorar até 48 horas a ser concluídas.   

**Reibifiquei manualmente uma bolha arquivada, como posso evitar que seja transferida temporariamente para o nível de Arquivo?**  
Quando uma bolha é movida de um nível de acesso para outro, o seu último tempo de modificação não muda. Se reidratar manualmente uma bolha arquivada para o nível quente, ela seria transferida de volta para o nível de arquivo pelo motor de gestão do ciclo de vida. Desative a regra que afeta temporariamente esta bolha para evitar que seja arquivada novamente. Volte a ativar a regra quando a bolha puder ser transferida com segurança para o nível de arquivo. Também pode copiar a bolha para outro local se precisar de permanecer permanentemente em nível quente ou fresco.

## <a name="next-steps"></a>Passos seguintes

Aprenda a recuperar dados após a eliminação acidental:

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../blobs/storage-blob-soft-delete.md)
