---
title: Registo redundante de zona para elevada disponibilidade
description: Saiba como permitir a redundância da zona no Registo de Contentores Azure, criando um registo de contentores ou replicação numa zona de disponibilidade de Azure. O despedimento de zona é uma característica do nível de serviço Premium.
ms.topic: article
ms.date: 12/11/2020
ms.openlocfilehash: f94d5a8d61c42e8833e21f035303be173c81764d
ms.sourcegitcommit: 66b0caafd915544f1c658c131eaf4695daba74c8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/18/2020
ms.locfileid: "97681815"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Permitir redundância de zona no Registo de Contentores de Azure para resiliência e elevada disponibilidade

Para além da [geo-replicação,](container-registry-geo-replication.md)que replica dados de registo em uma ou mais regiões do Azure para fornecer disponibilidade e reduzir a latência para operações regionais, o Registo de Contentores Azure suporta *a redundância* de zona opcional. [A redundância da zona](../availability-zones/az-overview.md#availability-zones) proporciona resiliência e elevada disponibilidade a um recurso de registo ou replicação (réplica) numa região específica.

Este artigo mostra como configurar um registo de contentores redundante de zona ou uma réplica redundante de zona usando o portal Azure ou um modelo de Gestor de Recursos Azure. 

O despedimento de zona é uma característica de **pré-visualização** do nível de serviço de registo de contentores Premium. Para obter informações sobre os níveis e limites do serviço de registo de registo, consulte [os níveis de serviço de registo de contentores Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Atualmente apoiado nas seguintes regiões: Leste dos EUA, Leste dos EUA 2 e Oeste dos EUA 2.
* As conversões da região para zonas de disponibilidade não são suportadas atualmente. Para permitir o apoio à zona de disponibilidade numa região, o registo deve ser criado na região desejada, com apoio de zona de disponibilidade ativado, ou uma região replicada deve ser adicionada com suporte de zona de disponibilidade ativado.
* A redundância da zona não pode ser desativada numa região.
* [As tarefas ACR](container-registry-tasks-overview.md) ainda não suportam zonas de disponibilidade.
* Atualmente suportado através de modelos Azure Resource Manager ou do portal Azure. O suporte do Azure CLI será ativado numa futura versão.
* Atualmente, quando se desloca um registo de contentores redundantes para outro grupo de recursos, a definição de redundância da zona mostra como `Disabled` .

## <a name="about-zone-redundancy"></a>Sobre a redundância de zona

Utilize [zonas de disponibilidade](../availability-zones/az-overview.md) Azure para criar um registo de contentores Azure resiliente e de alta disponibilidade dentro de uma região de Azure. Por exemplo, as organizações podem criar um registo de contentores Azure redundantes com outros [recursos Azure suportados](../availability-zones/az-region.md) para satisfazer a residência de dados ou outros requisitos de conformidade, ao mesmo tempo que fornecem uma elevada disponibilidade dentro de uma região.

O Registo de Contentores Azure também suporta [a geo-replicação,](container-registry-geo-replication.md)que replica o serviço em várias regiões, permitindo a redundância e localidade para calcular recursos em outros locais. A combinação de zonas de disponibilidade para redundância dentro de uma região, e a geo-replicação em várias regiões, aumenta tanto a fiabilidade como o desempenho de um registo.

As zonas de disponibilidade são localizações físicas únicas dentro de uma região de Azure. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. Cada zona tem um ou mais datacenters equipados com potência, arrefecimento e networking independentes. Quando configurado para redundância de zona, um registo (ou uma réplica de registo em uma região diferente) é replicado em todas as zonas de disponibilidade da região, mantendo-o disponível se houver falhas no datacenter.

## <a name="create-a-zone-redundant-registry---portal"></a>Criar um registo de zona-redundante - portal

1. Inicie sessão no Portal do Azure em [https://portal.azure.com](https://portal.azure.com).
1. **Selecione Criar um** registo  >  **de contentores de**  >  recursos.
1. No separador **Básicos,** selecione ou crie um grupo de recursos e introduza um nome de registo único. 
1. In **Location**, selecione uma região que suporta a redundância de zona para o Registo de Contentores de Azure, como *os EUA Orientais.*
1. No **SKU,** selecione **Premium**.
1. Em **zonas de disponibilidade**, selecione **Actived**.
1. Opcionalmente, configurar definições adicionais de registo e, em seguida, selecione **Review + create**.
1. Selecione **Criar** para implementar a instância de registo.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-portal.png" alt-text="Permitir redundância de zona no portal Azure":::

Para criar uma replicação redundante de zona:

1. Navegue para o seu registo de contentores de nível Premium e selecione **Replications**.
1. No mapa que aparece, selecione um hexágono verde numa região que apoie a redundância de zona para o Registo de Contentores de Azure, como **o West US 2**. Em seguida, selecione **Criar**.
1. Na janela **de replicação Criar,** em **zonas de disponibilidade,** selecione **Ativado** e, em seguida, selecione **Criar**.

## <a name="create-a-zone-redundant-registry---template"></a>Criar um registo redundante de zona - modelo

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, executar o [grupo az criar](/cli/az/group#az_group_create) comando para criar um grupo de recursos para o registo numa região que [suporte zonas de disponibilidade](../availability-zones/az-region.md) para registo de contentores Azure, como *eastus*.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="deploy-the-template"></a>Implementar o modelo 

Pode utilizar o seguinte modelo de Gestor de Recursos para criar um registo geo-replicado e redundante. O modelo por defeito permite a redundância de zona no registo e uma réplica regional adicional. 

Copie o seguinte conteúdo para um novo ficheiro e guarde-o utilizando um nome de ficheiro como `registryZone.json` .

```JSON
{
    "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "acrName": {
        "type": "string",
        "defaultValue": "[concat('acr', uniqueString(resourceGroup().id))]",
        "minLength": 5,
        "maxLength": 50,
        "metadata": {
          "description": "Globally unique name of your Azure Container Registry"
        }
      },
      "location": {
        "type": "string",
        "defaultValue": "[resourceGroup().location]",
        "metadata": {
          "description": "Location for registry home replica."
        }
      },
      "acrSku": {
        "type": "string",
        "defaultValue": "Premium",
        "allowedValues": [
          "Premium"
        ],
        "metadata": {
          "description": "Tier of your Azure Container Registry. Geo-replication and zone redundancy require Premium SKU."
        }
      },
      "acrZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry's home replica. Requires registry location to support availability zones."
        }
      },
      "acrReplicaLocation": {
        "type": "string",
        "metadata": {
          "description": "Short name for registry replica location."
        }
      },
      "acrReplicaZoneRedundancy": {
        "type": "string",
        "defaultValue": "Enabled",
        "metadata": {
          "description": "Enable zone redundancy of registry replica. Requires replica location to support availability zones."
        }
      }
    },
    "resources": [
      {
        "comments": "Container registry for storing docker images",
        "type": "Microsoft.ContainerRegistry/registries",
        "apiVersion": "2020-11-01-preview",
        "name": "[parameters('acrName')]",
        "location": "[parameters('location')]",
        "sku": {
          "name": "[parameters('acrSku')]",
          "tier": "[parameters('acrSku')]"
        },
        "tags": {
          "displayName": "Container Registry",
          "container.registry": "[parameters('acrName')]"
        },
        "properties": {
          "adminUserEnabled": "[parameters('acrAdminUserEnabled')]",
          "zoneRedundancy": "[parameters('acrZoneRedundancy')]"
        }
      },
      {
        "type": "Microsoft.ContainerRegistry/registries/replications",
        "apiVersion": "2020-11-01-preview",
        "name": "[concat(parameters('acrName'), '/', parameters('acrReplicaLocation'))]",
        "location": "[parameters('acrReplicaLocation')]",
          "dependsOn": [
          "[resourceId('Microsoft.ContainerRegistry/registries/', parameters('acrName'))]"
        ],
        "properties": {
          "zoneRedundancy": "[parameters('acrReplicaZoneRedundancy')]"
        }
      }
    ],
    "outputs": {
      "acrLoginServer": {
        "value": "[reference(resourceId('Microsoft.ContainerRegistry/registries',parameters('acrName')),'2019-12-01-preview').loginServer]",
        "type": "string"
      }
    }
  }
```

Executar o [grupo de implementação az](/cli/az/deployment#az_group_deployment_create) seguinte criar comando para criar o registo usando o ficheiro de modelo anterior. Quando indicado, fornecer:

* um nome de registo único, ou implementar o modelo sem parâmetros e criará um nome único para si
* uma localização para a réplica que suporta zonas de disponibilidade, como *westus2*

```azurecli
az deployment group create \
  --resource-group <resource-group-name> \
  --template-file registryZone.json \
  --parameters acrName=<registry-name> acrReplicaLocation=<replica-location>
```

Na saída do comando, observe a `zoneRedundancy` propriedade para o registo e a réplica. Quando ativado, cada recurso é redundante:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [as regiões que suportam zonas de disponibilidade.](../availability-zones/az-region.md)
* Saiba mais sobre a construção para [a fiabilidade](/azure/architecture/framework/resiliency/overview) em Azure.