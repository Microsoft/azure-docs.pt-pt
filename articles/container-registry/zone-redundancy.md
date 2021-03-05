---
title: Registo redundante de zona para elevada disponibilidade
description: Saiba como permitir a redundância da zona no Registo de Contentores Azure. Crie um registo de contentores ou uma replicação numa zona de disponibilidade de Azure. O despedimento de zona é uma característica do nível de serviço Premium.
ms.topic: article
ms.date: 02/23/2021
ms.custom: references_regions
ms.openlocfilehash: a190ea68f41196fb11c20259b9953f516d6f5370
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102203866"
---
# <a name="enable-zone-redundancy-in-azure-container-registry-for-resiliency-and-high-availability"></a>Permitir redundância de zona no Registo de Contentores de Azure para resiliência e elevada disponibilidade

Para além da [geo-replicação,](container-registry-geo-replication.md)que replica dados de registo em uma ou mais regiões do Azure para fornecer disponibilidade e reduzir a latência para operações regionais, o Registo de Contentores Azure suporta *a redundância* de zona opcional. [A redundância da zona](../availability-zones/az-overview.md#availability-zones) proporciona resiliência e elevada disponibilidade a um recurso de registo ou replicação (réplica) numa região específica.

Este artigo mostra como configurar um registo ou réplica de contentores redundantes de zona utilizando o modelo Azure CLI, portal Azure ou Azure Resource Manager. 

O despedimento de zona é uma característica de **pré-visualização** do nível de serviço de registo de contentores Premium. Para obter informações sobre os níveis e limites do serviço de registo de registo, consulte [os níveis de serviço de registo de contentores Azure](container-registry-skus.md).

## <a name="preview-limitations"></a>Limitações de pré-visualização

* Atualmente apoiado nas seguintes regiões: Leste dos EUA, Leste dos EUA 2, Oeste dos EUA 2, Norte da Europa, Europa Ocidental, Japão Leste.
* As conversões da região para zonas de disponibilidade não são suportadas atualmente. Para permitir o apoio à zona de disponibilidade numa região, o registo deve ser criado na região desejada, com apoio de zona de disponibilidade ativado, ou uma região replicada deve ser adicionada com suporte de zona de disponibilidade ativado.
* A redundância da zona não pode ser desativada numa região.
* [As tarefas ACR](container-registry-tasks-overview.md) ainda não suportam zonas de disponibilidade.

## <a name="about-zone-redundancy"></a>Sobre a redundância de zona

Utilize [zonas de disponibilidade](../availability-zones/az-overview.md) Azure para criar um registo de contentores Azure resiliente e de alta disponibilidade dentro de uma região de Azure. Por exemplo, as organizações podem criar um registo de contentores Azure redundantes com outros [recursos Azure suportados](../availability-zones/az-region.md) para satisfazer a residência de dados ou outros requisitos de conformidade, ao mesmo tempo que fornecem uma elevada disponibilidade dentro de uma região.

O Registo de Contentores Azure também suporta [a geo-replicação,](container-registry-geo-replication.md)que replica o serviço em várias regiões, permitindo a redundância e localidade para calcular recursos em outros locais. A combinação de zonas de disponibilidade para redundância dentro de uma região, e a geo-replicação em várias regiões, aumenta tanto a fiabilidade como o desempenho de um registo.

As zonas de disponibilidade são localizações físicas únicas dentro de uma região de Azure. Para garantir a resiliência, há um mínimo de três zonas separadas em todas as regiões ativadas. Cada zona tem um ou mais datacenters equipados com potência, arrefecimento e networking independentes. Quando configurado para redundância de zona, um registo (ou uma réplica de registo em uma região diferente) é replicado em todas as zonas de disponibilidade da região, mantendo-o disponível se houver falhas no datacenter.

## <a name="create-a-zone-redundant-registry---cli"></a>Criar um registo redundante de zona - CLI

Para utilizar o CLI Azure para permitir a redundância da zona, precisa da versão 2.17.0 ou posterior do Azure CLI. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli).

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, executar o [grupo az criar](/cli/azure/group#az_group_create) comando para criar um grupo de recursos para o registo.

```azurecli
az group create --name <resource-group-name> --location <location>
```

### <a name="create-zone-enabled-registry"></a>Criar registos via zonas

Executar o [az acr criar](/cli/azure/acr#az_acr_create) comando para criar um registo redundante de zona no nível de serviço Premium. Escolha uma região que [suporte zonas de disponibilidade](../availability-zones/az-region.md) para registo de contentores Azure. No exemplo seguinte, o despedimento por zona é permitido na região *leste.* Consulte a ajuda de `az acr create` comando para obter mais opções de registo.

```azurecli
az acr create \
  --resource-group <resource-group-name> \
  --name <container-registry-name> \
  --location eastus \
  --zone-redundancy enabled \
  --sku Premium
```

Na saída do comando, note a `zoneRedundancy` propriedade para o registo. Quando ativado, o registo é redundante:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

### <a name="create-zone-redundant-replication"></a>Criar replicação redundante de zona

Executar a [replicação az acr criar](/cli/azure/acr/replication#az_acr_replication_create) comando para criar uma réplica de registo redundante numa região que [suporte zonas de disponibilidade](../availability-zones/az-region.md) para registo de contentores Azure, como *westus2*. 

```azurecli
az acr replication create \
  --location westus2 \
  --resource-group <resource-group-name> \
  --registry <container-registry-name> \
  --zone-redundancy enabled
```
 
Na saída do comando, note a `zoneRedundancy` propriedade para a réplica. Quando ativada, a réplica é redundante:

```JSON
{
 [...]
"zoneRedundancy": "Enabled",
}
```

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
1. No mapa que aparece, selecione um hexágono verde numa região que apoie a redundância de zona para o Registo de Contentores de Azure, como **o West US 2**. Ou selecione **+ Adicionar**.
1. Na janela **de replicação Criar,** confirme a **Localização**. Em **zonas de disponibilidade**, selecione **Ativado** e, em seguida, selecione **Criar**.

    :::image type="content" source="media/zone-redundancy/enable-availability-zones-replication-portal.png" alt-text="Permitir a replicação redundante em zona no portal Azure":::

## <a name="create-a-zone-redundant-registry---template"></a>Criar um registo redundante de zona - modelo

### <a name="create-a-resource-group"></a>Criar um grupo de recursos

Se necessário, executar o [grupo az criar](/cli/azure/group#az_group_create) comando para criar um grupo de recursos para o registo numa região que [suporte zonas de disponibilidade](../availability-zones/az-region.md) para registo de contentores Azure, como *eastus*. Esta região é usada pelo modelo para definir a localização do registo.

```azurecli
az group create --name <resource-group-name> --location eastus
```

### <a name="deploy-the-template"></a>Implementar o modelo 

Pode utilizar o seguinte modelo de Gestor de Recursos para criar um registo geo-replicado e redundante. O modelo por defeito permite a redundância de zona no registo e uma réplica regional. 

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

Executar o [grupo de implementação az](/cli/azure/group/deployment#az_group_deployment_create) seguinte criar comando para criar o registo usando o ficheiro de modelo anterior. Quando indicado, fornecer:

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
