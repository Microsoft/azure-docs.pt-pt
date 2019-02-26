---
title: Atualizar a configuração de um cluster do Azure Service Fabric | Documentos da Microsoft
description: Saiba como atualizar a configuração que é executada um cluster do Service Fabric no Azure através de um modelo do Resource Manager.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: 66296cc6-9524-4c6a-b0a6-57c253bdf67e
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 6ddd246057b178119d520bee3bbe58117fbae610
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56820982"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Atualizar a configuração de um cluster no Azure 

Este artigo descreve como personalizar as várias configurações de recursos de infraestrutura para o seu cluster do Service Fabric. Para clusters alojados no Azure, pode personalizar as definições através da [portal do Azure](https://portal.azure.com) ou utilizando um modelo Azure Resource Manager.

> [!NOTE]
> Nem todas as definições estão disponíveis no portal, e é um [melhor prática para personalizá-lo com um modelo Azure Resource Manager](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code); Portal destina-se a Dev\Test de recursos de infraestrutura do serviço do cenário apenas.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalize as configurações de cluster utilizando modelos do Resource Manager
Clusters do Azure podem ser configurados através do modelo de Gestor de recursos de JSON. Para saber mais sobre as definições diferentes, veja [definições de configuração para clusters](service-fabric-cluster-fabric-settings.md). Por exemplo, os passos abaixo mostram como adicionar uma nova definição *MaxDiskQuotaInMB* para o *diagnóstico* secção com o Explorador de recursos do Azure.

1. Ir para https://resources.azure.com
2. Navegue até à sua subscrição ao expandir **subscrições** -> **\<subscrição Your >** -> **resourceGroups**  ->   **\<Seu grupo de recursos >** -> **fornecedores** -> **Microsoft.ServiceFabric**  ->  **clusters** -> **\<seu nome de Cluster >**
3. No canto superior direito, selecione **leitura/escrita.**
4. Selecione **edite** e atualizar o `fabricSettings` elemento JSON e adicione um novo elemento:

```json
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Também pode personalizar as definições de cluster em uma das seguintes formas com o Azure Resource Manager:

- Utilize o [portal do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) para exportar e atualizar o modelo do Resource Manager.
- Uso [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) para exportar e atualizar o modelo do Resource Manager.
- Utilize o [CLI do Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) para exportar e atualizar o modelo do Resource Manager.
- Utilizar o Azure RM PowerShell [Set-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Set-AzureRmServiceFabricSetting) e [Remove-AzureRmServiceFabricSetting](https://docs.microsoft.com/powershell/module/azurerm.servicefabric/Remove-AzureRmServiceFabricSetting) comandos para modificar a definição diretamente.
- Utilizar a CLI do Azure [definição do az sf cluster](https://docs.microsoft.com/cli/azure/sf/cluster/setting) comandos para modificar a definição diretamente.

## <a name="next-steps"></a>Passos Seguintes
* Saiba mais sobre o [definições de cluster do Service Fabric](service-fabric-cluster-fabric-settings.md).
* Saiba como [dimensionar o seu cluster e reduzir](service-fabric-cluster-scale-up-down.md).
