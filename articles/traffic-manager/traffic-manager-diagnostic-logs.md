---
title: Ativar o registo de diagnóstico no Gestor de tráfego do Azure
description: Saiba como ativar o registo de diagnóstico para o seu perfil do Gestor de tráfego e aceder os ficheiros de registo que são criados como resultado.
services: traffic-manager
author: asudbring
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: allensu
ms.openlocfilehash: b2ebeb41e69b7edfd43c38cc3b828069a1b3401a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67071235"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Ativar o registo de diagnóstico no Gestor de tráfego do Azure

Este artigo descreve como ativar o diagnóstico o dados de registo de acesso e de registo para um perfil do Gestor de tráfego.

Registos de diagnóstico de Gestor de tráfego do Azure podem fornecer informações sobre o comportamento do recurso de perfil do Gestor de tráfego. Por exemplo, pode utilizar os dados de registo do perfil para determinar por que as pesquisas individuais tem excedido em relação a um ponto de extremidade.

## <a name="enable-diagnostic-logging"></a>Ativar o registo de diagnósticos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que se seguem na [Azure Cloud Shell](https://shell.azure.com/powershell), ou ao executar o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, terá do módulo Azure PowerShell, 1.0.0 ou posterior. Pode executar `Get-Module -ListAvailable Az` para localizar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell). Se estiver executando o PowerShell localmente, terá também de executar `Login-AzAccount` para iniciar sessão no Azure.

1. **Obter o perfil do Gestor de tráfego:**

    Para ativar o registo de diagnósticos, precisa do ID de um perfil do Gestor de tráfego. Obter o perfil do Gestor de tráfego que pretende ativar o diagnóstico para o registo com [Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). A saída inclui informações do ID do perfil do Gestor de tráfego.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Ative o registo de diagnóstico para o perfil do Gestor de tráfego:**

    Ativar o registo de diagnóstico para o perfil de Gestor de tráfego com o ID de obteve no passo anterior com [Set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). O seguinte comando armazena registos verbosos para o perfil do Gestor de tráfego para uma conta de armazenamento do Azure especificada. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verifique se as definições de diagnóstico:**

      Verifique se as definições de diagnóstico para o perfil do Gestor de tráfego utilizando [Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). O comando seguinte apresenta as categorias que são registadas para um recurso.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Certifique-se de que todas as categorias associadas a exibição de recurso de perfil do Gestor de tráfego, como ativada de registo. Além disso, certifique-se de que a conta de armazenamento está corretamente definida.

## <a name="access-log-files"></a>Ficheiros de registo de acesso
1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue até à sua conta de armazenamento do Azure no portal.
2. Na **descrição geral** conta de página do seu armazenamento do Azure, em **serviços** selecionar **Blobs**.
3. Para **contentores**, selecione **insights-logs-probehealthstatusevents**e navegue para baixo até o ficheiro PT1H.json e clique em **transferir** para transferir e guardar uma cópia deste registo ficheiro.

    ![Ficheiros de registo de acesso do perfil do Gestor de tráfego de um armazenamento de BLOBs](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Esquema de registo do Gestor de tráfego

Todos os registos de diagnóstico disponíveis através do Azure Monitor partilham um esquema comum de nível superior, com flexibilidade para cada serviço emitir propriedades exclusivas para seus próprios eventos. Para o esquema de registos de diagnóstico de nível superior, consulte [suportada serviços, os esquemas e categorias para os registos de diagnóstico do Azure](../azure-monitor/platform/tutorial-dashboards.md).

A tabela seguinte inclui o esquema de registos específica para o recurso de perfil do Gestor de tráfego do Azure.

|||||
|----|----|---|---|
|**Nome do campo**|**Tipo de campo**|**Definição**|**Exemplo**|
|EndpointName|String|O nome do ponto final do Gestor de tráfego está a ser gravado cujo estado de funcionamento.|*myPrimaryEndpoint*|
|Estado|String|O estado de funcionamento do ponto final do Gestor de tráfego foi investigado. O estado pode ser **cópias** ou **baixo**.|**Cópia de segurança**|
|||||

## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre [monitorização do Gestor de tráfego](traffic-manager-monitoring.md)

