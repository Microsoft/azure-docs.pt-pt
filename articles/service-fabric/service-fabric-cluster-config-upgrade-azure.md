---
title: Atualizar a configuração de um cluster de tecido de serviço Azure
description: Aprenda a atualizar a configuração que executa um cluster de tecido de serviço em Azure usando um modelo de Gestor de Recursos.
author: dkkapur
ms.topic: conceptual
ms.date: 11/09/2018
ms.author: dekapur
ms.openlocfilehash: 476a2d910b916ea29132b108478d06f756454813
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75463282"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Atualizar a configuração de um cluster em Azure 

Este artigo descreve como personalizar as várias definições de tecido para o seu cluster De Tecido de Serviço. Para clusters hospedados em Azure, pode personalizar as definições através do [portal Azure](https://portal.azure.com) ou utilizando um modelo de Gestor de Recursos Azure.

> [!NOTE]
> Nem todas as configurações estão disponíveis no portal, e é uma [boa prática personalizá-lo usando um modelo de Gestor de Recursos Azure;](https://docs.microsoft.com/azure/service-fabric/service-fabric-best-practices-infrastructure-as-code) Portal é apenas para o cenário de teste de tecido de serviço.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalize as definições de cluster usando modelos de Gestor de Recursos
Os clusters Azure podem ser configurados através do modelo jSON Resource Manager. Para saber mais sobre as diferentes definições, consulte as definições de [Configuração para clusters](service-fabric-cluster-fabric-settings.md). Como exemplo, os passos abaixo mostram como adicionar uma nova definição *MaxDiskQuotaInMB* à secção *de Diagnósticoutilizando* o Azure Resource Explorer.

1. Ir para https://resources.azure.com
2. Navegue para a sua subscrição expandindo **subscrições** -> **\<A sua subscrição>**  ->  **recursosGroup** -> **\<Your Resource Group>**  ->  **fornecedores** -> **Microsoft.ServiceFabric** -> **clusters** -> **\<Your Cluster Name>**
3. No canto superior direito, selecione **Ler/Escrever.**
4. Selecione **Editar** e atualizar o `fabricSettings` elemento JSON e adicionar um novo elemento:

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

Também pode personalizar as definições de cluster de uma das seguintes formas com o Gestor de Recursos Azure:

- Utilize o [portal Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template) para exportar e atualizar o modelo De Manjedoura de Recursos.
- Utilize o [PowerShell](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-powershell) para exportar e atualizar o modelo de Gestor de Recursos.
- Utilize o [Azure CLI](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-export-template-cli) para exportar e atualizar o modelo de Gestor de Recursos.
- Utilize os comandos Azure PowerShell [Set-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Set-azServiceFabricSetting) e [Remove-AzServiceFabricSetting](https://docs.microsoft.com/powershell/module/az.servicefabric/Remove-azServiceFabricSetting) para modificar a definição diretamente.
- Utilize os comandos de definição de cluster Azure CLI [az sf](https://docs.microsoft.com/cli/azure/sf/cluster/setting) para modificar a regulação diretamente.

## <a name="next-steps"></a>Passos seguintes
* Conheça as [definições](service-fabric-cluster-fabric-settings.md)do cluster do Tecido de Serviço .
* Aprenda a [escalar o seu cluster para dentro e para fora.](service-fabric-cluster-scale-up-down.md)
