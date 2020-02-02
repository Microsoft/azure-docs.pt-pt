---
title: Ativar a exploração de diagnóstico em Azure Traffic Manager
description: Saiba como ativar o registo de diagnóstico para o seu perfil de Gestor de Tráfego e aceda aos ficheiros de registo que são criados como resultado.
services: traffic-manager
author: rohinkoul
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: rohink
ms.openlocfilehash: 0ed2ecef86795f62aa3fe5798dcd0d07adbaf9cc
ms.sourcegitcommit: fa6fe765e08aa2e015f2f8dbc2445664d63cc591
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/01/2020
ms.locfileid: "76938680"
---
# <a name="enable-diagnostic-logging-in-azure-traffic-manager"></a>Ativar a exploração de diagnóstico em Azure Traffic Manager

Este artigo descreve como permitir o registo de diagnóstico e o acesso aos dados de registo para um perfil do Gestor de Tráfego.

Os registos de diagnóstico do Gestor de Tráfego Azure podem fornecer informações sobre o comportamento do recurso de perfil do Gestor de Tráfego. Por exemplo, pode utilizar os dados de registo do perfil para determinar por que razão as sondas individuais têm cronometrado contra um ponto final.

## <a name="enable-diagnostic-logging"></a>Ativar o registo de diagnósticos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Você pode executar os comandos a seguir no [Azure cloud Shell](https://shell.azure.com/powershell)ou executando o PowerShell do seu computador. O Azure Cloud Shell é um shell interativo gratuito. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do seu computador, precisa do módulo Azure PowerShell, 1.0.0 ou mais tarde. Pode executá`Get-Module -ListAvailable Az` para encontrar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell). Se estiver a executar a PowerShell localmente, também precisa de correr `Login-AzAccount` para iniciar sessão no Azure.

1. **Recuperar o perfil do Gestor de Tráfego:**

    Para permitir o registo de diagnóstico, precisa da identificação de um perfil do Gestor de Tráfego. Recupere o perfil do Gestor de Tráfego que pretende ativar o registo de diagnóstico com [o Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). A saída inclui as informações de identificação do perfil do Gestor de Tráfego.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Ativar o registo de diagnóstico para o perfil do Gestor de Tráfego:**

    Ative o registo de diagnóstico para o perfil do Gestor de Tráfego utilizando o ID obtido no passo anterior com [set-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). Os seguintes registos de comando saem registos verbosos para o perfil do Gestor de Tráfego para uma determinada conta de Armazenamento Azure. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verifique as definições de diagnóstico:**

      Verifique as definições de diagnóstico do perfil do Gestor de Tráfego utilizando [o Get-AzDiagnosticSetting](https://docs.microsoft.com/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest). O comando seguinte exibe as categorias que são registadas para um recurso.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Certifique-se de que todas as categorias de registo associadas ao ecrã de recursos do Gestor de Tráfego estão ativadas. Além disso, verifique se a conta de armazenamento está corretamente definida.

## <a name="access-log-files"></a>Acessar arquivos de log
1. Inicie sessão no [Portal do Azure](https://portal.azure.com). 
1. Navegue para a sua conta de Armazenamento Azure no portal.
2. Na página **de visão geral** da sua conta de armazenamento Azure, em **Serviços** selecione **Blobs**.
3. Para **contentores**, selecione **insights-logs-probehealthstatusevents**, e navegue até ao ficheiro PT1H.json e clique em **Baixar** para descarregar e guardar uma cópia deste ficheiro de registo.

    ![Aceda a ficheiros de registo do seu perfil do Gestor de Tráfego a partir de um armazenamento de blob](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Esquema de log do Gestor de Tráfego

Todos os registos de diagnóstico disponíveis através do Azure Monitor partilham um esquema comum de alto nível, com flexibilidade para cada serviço emitir propriedades únicas para os seus próprios eventos. Para obter registos de diagnóstico de alto nível, consulte [serviços suportados, schemas e categorias para Registos](../azure-monitor/platform/tutorial-dashboards.md)de Diagnóstico Azure .

A tabela seguinte inclui logs schema específico para o recurso de perfil do Gestor de Tráfego Azure.

|||||
|----|----|---|---|
|**Nome do campo**|**Tipo de campo**|**Definição**|**Exemplo**|
|EndpointName|Cadeia|O nome do ponto final do Gestor de Tráfego cujo estado de saúde está a ser registado.|*myPrimaryEndpoint*|
|Estado|Cadeia|O estado de saúde do ponto final do Gestor de Tráfego que foi sondado. O estado pode ser **para cima** ou **para baixo.**|**Para cima**|
|||||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [monitorização do Gestor de Tráfego](traffic-manager-monitoring.md)

