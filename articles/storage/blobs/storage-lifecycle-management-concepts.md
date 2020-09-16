---
title: Gestão do ciclo de vida do Azure Storage
description: Aprenda a criar regras de política de ciclo de vida para transitar dados de envelhecimento dos níveis Hot to Cool e Archive.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 09/15/2020
ms.service: storage
ms.subservice: common
ms.topic: conceptual
ms.reviewer: yzheng
ms.custom: devx-track-azurepowershell, references_regions
ms.openlocfilehash: be5d86fe690d60f687622243a2f1d7771b8af7d0
ms.sourcegitcommit: 80b9c8ef63cc75b226db5513ad81368b8ab28a28
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/16/2020
ms.locfileid: "90604034"
---
# <a name="manage-the-azure-blob-storage-lifecycle"></a>Gerir o ciclo de vida do Armazenamento de Blobs do Azure

Os conjuntos de dados têm ciclos de vida únicos. No início do ciclo de vida, as pessoas acedem frequentemente a alguns dados. Mas a necessidade de acesso diminui drasticamente à medida que os dados envelhecem. Alguns dados permanecem inativos na nuvem e raramente são acedidos uma vez armazenados. Alguns dados expiram dias ou meses após a criação, enquanto outros conjuntos de dados são lidos e modificados ativamente ao longo da sua vida útil. A gestão do ciclo de vida de armazenamento Azure Blob oferece uma política rica e baseada em regras para contas de armazenamento GPv2 e Blob. Utilize a política para transitar os seus dados para os níveis de acesso apropriados ou expire no final do ciclo de vida dos dados.

A política de gestão do ciclo de vida permite-lhe:

- Bolhas de transição para um nível de armazenamento mais fresco (quente a fresco, quente para arquivar, ou fresco para arquivar) para otimizar para desempenho e custo
- Apagar bolhas no final dos seus ciclos de vida
- Definir regras a serem executadas uma vez por dia ao nível da conta de armazenamento
- Aplicar regras a recipientes ou a um subconjunto de bolhas (utilizando prefixos de nome ou [etiquetas de índice de bolhas](storage-manage-find-blobs.md) como filtros)

Considere um cenário em que os dados tenham acesso frequente durante as fases iniciais do ciclo de vida, mas apenas ocasionalmente após duas semanas. Para além do primeiro mês, o conjunto de dados raramente é acedido. Neste cenário, o armazenamento quente é o melhor durante as fases iniciais. O armazenamento fresco é mais adequado para acessos ocasionais. O armazenamento de arquivo é a melhor opção de nível após a idade dos dados ao longo de um mês. Ao ajustar os níveis de armazenamento em relação à idade dos dados, pode conceber as opções de armazenamento menos dispendiosas para as suas necessidades. Para alcançar esta transição, as regras de política de gestão do ciclo de vida estão disponíveis para mover dados de envelhecimento para níveis mais frios.

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="availability-and-pricing"></a>Disponibilidade e preços

A funcionalidade de gestão do ciclo de vida está disponível em todas as regiões Azure para as contas de Fins Gerais v2 (GPv2), contas de armazenamento Blob e contas de armazenamento Premium Block Blob. No portal Azure, pode atualizar uma conta de Final geral (GPv1) existente para uma conta GPv2. Para obter mais informações sobre contas de armazenamento, consulte [a visão geral da conta de armazenamento Azure](../common/storage-account-overview.md).

A função de gestão do ciclo de vida é gratuita. Os clientes são cobrados o custo regular de operação para as chamadas [API de Nível Blob.](https://docs.microsoft.com/rest/api/storageservices/set-blob-tier) A operação de eliminação é gratuita. Para obter mais informações sobre preços, consulte [os preços do Block Blob](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="add-or-remove-a-policy"></a>Adicionar ou remover uma política

Pode adicionar, editar ou remover uma política utilizando qualquer um dos seguintes métodos:

* [Portal do Azure](https://portal.azure.com)
* [Azure PowerShell](https://github.com/Azure/azure-powershell/releases)
* [CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli)
* [APIs REST](https://docs.microsoft.com/rest/api/storagerp/managementpolicies)

Uma política pode ser lida ou escrita na íntegra. As atualizações parciais não são suportadas. 

> [!NOTE]
> Se ativar as regras de firewall para a sua conta de armazenamento, os pedidos de gestão do ciclo de vida podem ser bloqueados. Pode desbloquear estes pedidos fornecendo exceções para serviços de confiança da Microsoft. Para obter mais informações, consulte a secção Exceções em [firewalls Configure e redes virtuais.](https://docs.microsoft.com/azure/storage/common/storage-network-security#exceptions)

Este artigo mostra como gerir a política utilizando o portal e os métodos PowerShell.

# <a name="portal"></a>[Portal](#tab/azure-portal)

Há duas formas de adicionar uma política através do portal Azure. 

* [Vista do portal Azure](#azure-portal-list-view)
* [Vista de código do portal Azure](#azure-portal-code-view)

#### <a name="azure-portal-list-view"></a>Vista do portal Azure

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione a sua conta de armazenamento. 

1. No **serviço Blob,** selecione **Gestão do Ciclo de Vida** para visualizar ou alterar as suas regras.

1. Selecione o separador **Lista Ver.**

1. **Selecione Adicione uma regra** e nomeie a sua regra no formulário **Detalhes.** Também pode definir o **âmbito de regras,** **tipo Blob**e valores **do subtipo Blob.** O exemplo a seguir define o âmbito para filtrar as bolhas. Isto faz com que o **separador conjunto do filtro** seja adicionado.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-details.png" alt-text="Gestão de ciclo de vida adicionar uma página de detalhes de regras no portal Azure":::

1. Selecione **as bolhas de base** para definir as condições para a sua regra. No exemplo seguinte, as bolhas são movidas para o armazenamento fresco se não tiverem sido modificadas durante 30 dias.

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-base-blobs.png" alt-text="Página de blobs de base de gestão de ciclo de vida no portal Azure":::

   A última opção **acedida** está disponível em pré-visualização nas seguintes regiões:

    - França Central
    - Leste do Canadá
    - Canadá Central

   > [!IMPORTANT]
   > A última pré-visualização do rastreio do tempo de acesso destina-se apenas à utilização não-produção. Os contratos de serviços de produção (SLAs) não estão atualmente disponíveis.
   
   Para obter mais informações sobre a última opção **acedida,** consulte [os dados do Movimento com base na última data acedida (pré-visualização)](#move-data-based-on-last-accessed-date-preview).

1. Se selecionou **bolhas de limite com filtros** na página **Detalhes,** selecione **o conjunto de filtros** para adicionar um filtro opcional. O exemplo seguinte filtra-se em bolhas no recipiente *mylifecyclecontainer* que começam com "log".

   :::image type="content" source="media/storage-lifecycle-management-concepts/lifecycle-management-filter-set.png" alt-text="Página definida de filtro de gestão de ciclo de vida no portal Azure":::

1. **Selecione Adicionar** para adicionar a nova política.

#### <a name="azure-portal-code-view"></a>Vista de código do portal Azure
1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. No portal Azure, procure e selecione a sua conta de armazenamento.

1. No **serviço Blob,** selecione **Gestão do Ciclo de Vida** para visualizar ou alterar a sua política.

1. O JSON seguinte é um exemplo de uma política que pode ser colada no separador **Code View.**

   ```json
   {
     "rules": [
       {
         "enabled": true,
         "name": "move-to-cool",
         "type": "Lifecycle",
         "definition": {
           "actions": {
             "baseBlob": {
               "tierToCool": {
                 "daysAfterModificationGreaterThan": 30
               }
             }
           },
           "filters": {
             "blobTypes": [
               "blockBlob"
             ],
             "prefixMatch": [
               "mylifecyclecontainer/log"
             ]
           }
         }
       }
     ]
   }
   ```

1. Selecione **Guardar**.

1. Para mais informações sobre este exemplo do JSON, consulte as secções [De Política](#policy) e [Regras.](#rules)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

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

Pode definir a gestão do ciclo de vida utilizando modelos do Gestor de Recursos Azure. Aqui está um modelo de amostra para implementar uma conta de armazenamento RA-GRS GPv2 com uma política de gestão do ciclo de vida.

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
| `rules`        | Uma variedade de objetos de regra | Pelo menos uma regra é necessária numa política. Pode definir até 100 regras numa apólice.|

Cada regra dentro da política tem vários parâmetros:

| Nome do parâmetro | Tipo de parâmetro | Notas | Necessário |
|----------------|----------------|-------|----------|
| `name`         | String |Um nome de regra pode incluir até 256 caracteres alfanuméricos. O nome da regra é sensível a casos. Deve ser único dentro de uma política. | Verdadeiro |
| `enabled`      | Booleano | Um boolean opcional para permitir que uma regra seja temporariamente desativada. O valor predefinido é verdadeiro se não estiver definido. | Falso | 
| `type`         | Um valor enum | O tipo válido atual é `Lifecycle` . | Verdadeiro |
| `definition`   | Um objeto que define a regra do ciclo de vida | Cada definição é composta por um conjunto de filtros e um conjunto de ação. | Verdadeiro |

## <a name="rules"></a>Regras

Cada definição de regras inclui um conjunto de filtros e um conjunto de ação. O [filtro estabelece](#rule-filters) limites de regras a um determinado conjunto de objetos dentro de um recipiente ou nomes de objetos. O [conjunto de ação](#rule-actions) aplica o nível ou elimina as ações ao conjunto filtrado de objetos.

### <a name="sample-rule"></a>Regra da amostra

A seguinte regra de amostra filtra a conta para executar as ações em objetos que existem no interior `container1` e começar por `foo` .

>[!NOTE]
>- A gestão do ciclo de vida suporta os tipos de blob de bloco e apêndice.<br>
>- A gestão do ciclo de vida não afeta recipientes do sistema como $logs e $web.

- Bolha de nível para arrefecer nível 30 dias após a última modificação
- Bolha de nível para arquivar nível 90 dias após a última modificação
- Apagar bolha 2.555 dias (sete anos) após última modificação
- Apagar snapshots blob 90 dias após a criação de instantâneos

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

Os filtros limitam as ações de regra a um subconjunto de bolhas dentro da conta de armazenamento. Se for definido mais de um filtro, um sistema lógico `AND` funciona em todos os filtros.

Os filtros incluem:

| Nome do filtro | Tipo de filtro | Notas | é necessário |
|-------------|-------------|-------|-------------|
| blobTypes   | Uma matriz de valores de enum predefinidos. | O lançamento atual suporta `blockBlob` e `appendBlob` . Apenas a eliminação é suportada para `appendBlob` , o nível definido não é suportado. | Yes |
| prefixOSatch | Uma série de cordas para prefixos a combinar. Cada regra pode definir até 10 prefixos. Uma corda de prefixo deve começar com um nome de recipiente. Por exemplo, se quiser combinar todas as bolhas `https://myaccount.blob.core.windows.net/container1/foo/...` por uma regra, o prefixoMatch é `container1/foo` . | Se não definir prefixoSatch, a regra aplica-se a todas as bolhas dentro da conta de armazenamento. | No |
| blobIndexMatch | Uma matriz de valores dicionários que consistem na chave de etiqueta do Índice Blob e condições de valor a combinar. Cada regra pode definir até 10 condições de etiqueta de índice blob. Por exemplo, se quiser combinar todas as bolhas com `Project = Contoso` uma `https://myaccount.blob.core.windows.net/` regra, o blobIndexMatch é `{"name": "Project","op": "==","value": "Contoso"}` . | Se não definir blobIndexMatch, a regra aplica-se a todas as bolhas dentro da conta de armazenamento. | No |

> [!NOTE]
> O Blob Index está em pré-visualização pública, e está disponível nas regiões central do **Canadá,** **Canadá Oriental,** **France Central**e **França Sul.** Para saber mais sobre esta funcionalidade juntamente com questões e limitações conhecidas, consulte [Gerir e encontrar dados sobre o Armazenamento de Blob Azure blob com Índice blob (Preview)](storage-manage-find-blobs.md).

### <a name="rule-actions"></a>Ações de regra

As ações são aplicadas às bolhas filtradas quando a condição de funcionamento é satisfeita.

A gestão do ciclo de vida suporta o tiering e a eliminação de bolhas e a eliminação de instantâneos blob. Defina pelo menos uma ação para cada regra em blobs ou snapshots blob.

| Ação                      | Base Blob                                   | Instantâneo      |
|-----------------------------|---------------------------------------------|---------------|
| tierToCool                  | Blobs de suporte atualmente em nível quente         | Não suportado |
| enableAutoTierToHotFromCool | Blobs de suporte atualmente em nível fresco        | Não suportado |
| tierToArchive               | Bolhas de suporte atualmente em nível quente ou fresco | Não suportado |
| delete                      | Apoiado `blockBlob` e `appendBlob`  | Suportado     |

>[!NOTE]
>Se definir mais do que uma ação na mesma bolha, a gestão do ciclo de vida aplica a ação menos dispendiosa à bolha. Por exemplo, a ação `delete` é mais barata do que a `tierToArchive` ação. A ação `tierToArchive` é mais barata do que a `tierToCool` ação.

As condições de execução baseiam-se na idade. As bolhas de base usam o último tempo modificado para rastrear a idade, e os instantâneos blob usam o tempo de criação instantâneo para rastrear a idade.

| Condição de execução de ação               | Valor da condição                          | Description                                                                      |
|------------------------------------|------------------------------------------|----------------------------------------------------------------------------------|
| dias Após aModificaçãoGreaterThan   | Valor inteiro indicando a idade em dias | A condição para as ações de blob base                                              |
| dias Após ACreationGreaterThan       | Valor inteiro indicando a idade em dias | A condição para as ações de snapshot blob                                          |
| dias Depois deLastAccessTimeGreaterThan | Valor inteiro indicando a idade em dias | (pré-visualização) A condição para as ações de blob base quando o tempo de acesso último é ativado |

## <a name="examples"></a>Exemplos

Os exemplos que se seguem demonstram como abordar cenários comuns com regras de política do ciclo de vida.

### <a name="move-aging-data-to-a-cooler-tier"></a>Mover dados de envelhecimento para um nível mais frio

Este exemplo mostra como transitar blocos de bolhas prefixadas com `container1/foo` ou `container2/bar` . As transições políticas blobs que não foram modificadas em mais de 30 dias para arrefecer o armazenamento, e bolhas não modificadas em 90 dias para o nível de arquivo:

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

### <a name="move-data-based-on-last-accessed-date-preview"></a>Mover dados com base na última data acedida (pré-visualização)

Pode ativar o rastreio da última hora de acesso para manter um registo de quando a sua bolha é lida ou escrita pela última vez. Pode utilizar o último tempo de acesso como filtro para gerir o tiering e a retenção dos seus dados blob.

A última opção **acedida** está disponível em pré-visualização nas seguintes regiões:

 - França Central
 - Leste do Canadá
 - Canadá Central

> [!IMPORTANT]
> A última pré-visualização do rastreio do tempo de acesso destina-se apenas à utilização não-produção. Os contratos de serviços de produção (SLAs) não estão atualmente disponíveis.

#### <a name="how-last-access-time-tracking-works"></a>Como funciona o último rastreio do tempo de acesso

Quando o rastreio da hora de acesso do último acesso está ativado, a propriedade blob chamada `LastAccessTime` é atualizada quando uma bolha é lida ou escrita. Uma operação [Get Blob](/rest/api/storageservices/get-blob) é considerada uma operação de acesso. [Obtenha Blob Properties](/rest/api/storageservices/get-blob-properties), [Obtenha Metadados Blob](/rest/api/storageservices/get-blob-metadata)e [Obtenha Tags Blob](/rest/api/storageservices/get-blob-tags) não são operações de acesso e, portanto, não atualize a última hora de acesso.

Para minimizar o impacto na latência de acesso à leitura, apenas a primeira leitura das últimas 24 horas atualiza a última hora de acesso. Leituras posteriores no mesmo período de 24 horas não atualizam o último tempo de acesso. Se uma bolha for modificada entre leituras, o último tempo de acesso é o mais recente dos dois valores.

No exemplo seguinte, as bolhas são movidas para o armazenamento fresco se não tiverem sido acedidas durante 30 dias. A `enableAutoTierToHotFromCool` propriedade é um valor Boolean que indica se uma bolha deve ser automaticamente nivelada de volta a quente se for acedida novamente depois de ser nivelada para arrefecer.

```json
{
  "enabled": true,
  "name": "last-accessed-thirty-days-ago",
  "type": "Lifecycle",
  "definition": {
    "actions": {
      "baseBlob": {
        "enableAutoTierToHotFromCool": true,
        "tierToCool": {
          "daysAfterLastAccessTimeGreaterThan": 30
        }
      }
    },
    "filters": {
      "blobTypes": [
        "blockBlob"
      ],
      "prefixMatch": [
        "mylifecyclecontainer/log"
      ]
    }
  }
}
```

#### <a name="storage-account-support"></a>Suporte à conta de armazenamento

O rastreio da última hora de acesso está disponível para os seguintes tipos de contas de armazenamento:

 - Contas de armazenamento v2 para fins gerais
 - Contas de armazenamento de blob bloqueada
 - Contas do Blob Storage

Se a sua conta de armazenamento for uma conta V1 para fins gerais, utilize o portal Azure para atualizar para uma conta V2 para fins gerais.

As contas de armazenamento com um espaço hierárquico habilitado para uso com Azure Data Lake Storage Gen2 ainda não estão suportadas.

#### <a name="pricing-and-billing"></a>Preços e faturação

Cada última atualização do tempo de acesso é considerada uma [outra operação](https://azure.microsoft.com/pricing/details/storage/blobs/).

### <a name="archive-data-after-ingest"></a>Arquivar dados após ingerir

Alguns dados permanecem inativos na nuvem e raramente, se é que alguma vez, são acedidos uma vez armazenados. A seguinte política de ciclo de vida é configurada para arquivar dados logo após a sua ingestão. Este exemplo transita bolhas de bloco na conta de armazenamento dentro do recipiente `archivecontainer` para um nível de arquivo. A transição é realizada agindo em bolhas 0 dias após o último tempo modificado:

> [!NOTE] 
> Recomenda-se o upload das suas bolhas diretamente no nível de arquivo para ser mais eficiente. Pode utilizar o cabeçalho x-ms-access-tier para [PutBlob](https://docs.microsoft.com/rest/api/storageservices/put-blob) ou [PutBlockList](https://docs.microsoft.com/rest/api/storageservices/put-block-list) com a versão REST 2018-11-09 e mais recentes ou as nossas mais recentes bibliotecas de clientes de armazenamento de bolhas. 

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

### <a name="expire-data-based-on-age"></a>Expire dados com base na idade

Espera-se que alguns dados expirem dias ou meses após a criação. Pode configurar uma política de gestão do ciclo de vida para expirar dados por eliminação com base na idade dos dados. O exemplo a seguir mostra uma política que elimina todas as bolhas de bloco com mais de 365 dias.

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

### <a name="delete-data-with-blob-index-tags"></a>Eliminar dados com tags Blob Index
Alguns dados só devem ser expirados se explicitamente marcados para eliminação. Pode configurar uma política de gestão do ciclo de vida para expirar dados que estão marcados com atributos de chave/valor do índice blob. O exemplo a seguir mostra uma política que elimina todas as bolhas de blocos marcadas com `Project = Contoso` . Para saber mais sobre o Índice Blob, consulte [Gerir e encontrar dados sobre o armazenamento de blob azure com índice blob (pré-visualização)](storage-manage-find-blobs.md).

```json
{
    "rules": [
        {
            "enabled": true,
            "name": "DeleteContosoData",
            "type": "Lifecycle",
            "definition": {
                "actions": {
                    "baseBlob": {
                        "delete": {
                            "daysAfterModificationGreaterThan": 0
                        }
                    }
                },
                "filters": {
                    "blobIndexMatch": [
                        {
                            "name": "Project",
                            "op": "==",
                            "value": "Contoso"
                        }
                    ],
                    "blobTypes": [
                        "blockBlob"
                    ]
                }
            }
        }
    ]
}
```

### <a name="delete-old-snapshots"></a>Apagar instantâneos antigos

Para dados que são modificados e acedidos regularmente ao longo da sua vida útil, os instantâneos são frequentemente utilizados para rastrear versões mais antigas dos dados. Pode criar uma política que elimina instantâneos antigos com base na idade do instantâneo. A idade do instantâneo é determinada avaliando o tempo de criação do instantâneo. Esta regra de política elimina as imagens blob bloqueadas dentro do recipiente `activedata` que são 90 dias ou mais após a criação do instantâneo.

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

**Criei uma nova política, porque é que as ações não funcionam imediatamente?**

A plataforma executa a política do ciclo de vida uma vez por dia. Uma vez configurada uma apólice, pode levar até 24 horas para algumas ações funcionarem pela primeira vez.

**Se eu atualizar uma política existente, quanto tempo demora as ações a decorrer?**

A política atualizada leva até 24 horas para entrar em vigor. Uma vez que a política esteja em vigor, pode levar até 24 horas para as ações funcionarem. Por conseguinte, as ações políticas podem demorar até 48 horas a ser concluídas.

**Rehisquirei manualmente uma bolha arquivada, como posso evitar que seja transferida temporariamente para o nível do Arquivo?**

Quando uma bolha é movida de um nível de acesso para outro, o seu último tempo de modificação não muda. Se reidratar manualmente uma bolha arquivada para o nível quente, ela seria transferida de volta para o nível de arquivo pelo motor de gestão do ciclo de vida. Desative a regra que afeta esta bolha temporariamente para evitar que seja arquivada novamente. Volte a ativar a regra quando a bolha pode ser deslocal para o nível de arquivo com segurança. Também pode copiar a bolha para outro local se precisar de permanecer permanentemente em camadas quentes ou frias.

## <a name="next-steps"></a>Passos seguintes

Saiba como recuperar dados após a eliminação acidental:

- [Eliminação de forma recuperável dos blobs do Armazenamento do Microsoft Azure](../blobs/storage-blob-soft-delete.md)

Saiba como gerir e encontrar dados com o Blob Index:

- [Gerir e encontrar dados sobre o armazenamento de blob Azure com índice blob](storage-manage-find-blobs.md)
