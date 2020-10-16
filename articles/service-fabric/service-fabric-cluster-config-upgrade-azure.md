---
title: Atualize a configuração de um cluster de tecido de serviço Azure
description: Saiba como atualizar a configuração que executa um cluster de Tecido de Serviço em Azure usando um modelo de Gestor de Recursos.
ms.topic: conceptual
ms.date: 11/09/2018
ms.openlocfilehash: 692dc2162159ab61a3ac527e12dac43438084a60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91842721"
---
# <a name="upgrade-the-configuration-of-a-cluster-in-azure"></a>Atualizar a configuração de um cluster em Azure 

Este artigo descreve como personalizar as várias definições de tecido para o seu cluster de Tecido de Serviço. Para os clusters alojados no Azure, pode personalizar as definições através do [portal Azure](https://portal.azure.com) ou utilizando um modelo de Gestor de Recursos Azure.

> [!NOTE]
> Nem todas as definições estão disponíveis no portal, e é uma [boa prática personalizá-la usando um modelo de Gestor de Recursos Azure;](./service-fabric-best-practices-infrastructure-as-code.md) Portal é apenas para o cenário de Dev\Test de Tecido de Serviço.
> 


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personalize as definições do cluster usando modelos de Gestor de Recursos
Os clusters Azure podem ser configurados através do modelo JSON Resource Manager. Para saber mais sobre as diferentes definições, consulte [as definições de Configuração para clusters](service-fabric-cluster-fabric-settings.md). Como exemplo, os passos abaixo mostram como adicionar uma nova definição *MaxDiskQuotaInMB* à secção *de Diagnósticos* utilizando o Azure Resource Explorer.

1. Ir para https://resources.azure.com
2. Navegue para a sua subscrição expandindo os fornecedores de recursos de **subscrições**  ->  **\<Your Subscription>**  ->  **resourceGroups**  ->  **\<Your Resource Group>**  ->  **providers**  ->  **Microsoft.ServiceFabric**  ->  **clusters** -> **\<Your Cluster Name>**
3. No canto superior direito, selecione **Ler/Escrever.**
4. **Selecione Editar** e atualizar o `fabricSettings` elemento JSON e adicione um novo elemento:

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

Também pode personalizar as definições do cluster de uma das seguintes formas com o Azure Resource Manager:

- Utilize o [portal Azure](../azure-resource-manager/templates/export-template-portal.md) para exportar e atualizar o modelo de Manger de Recursos.
- Utilize [o PowerShell](../azure-resource-manager/management/manage-resources-powershell.md) para exportar e atualizar o modelo de Gestor de Recursos.
- Utilize o [CLI Azure](../azure-resource-manager/management/manage-resources-cli.md) para exportar e atualizar o modelo de Gestor de Recursos.
- Utilize os comandos Azure PowerShell [Set-AzServiceFabricSetting](/powershell/module/az.servicefabric/set-azservicefabricsetting) e [Remove-AzServiceFabricSetting](/powershell/module/az.servicefabric/remove-azservicefabricsetting) para modificar a definição diretamente.
- Utilize os comandos de definição de cluster Azure CLI [az sf](/cli/azure/sf/cluster/setting) para modificar a definição diretamente.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre as [definições](service-fabric-cluster-fabric-settings.md)do cluster de tecido de serviço .
* Aprenda a [escalar o seu cluster para dentro e para fora](service-fabric-cluster-scale-in-out.md).
