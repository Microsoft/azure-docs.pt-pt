---
title: Permitir a sessão de registo de recursos no Azure Traffic Manager
description: Saiba como ativar a sessão de registo de recursos para o seu perfil de Gestor de Tráfego e aceda aos ficheiros de registo que são criados como resultado.
services: traffic-manager
author: duongau
ms.service: traffic-manager
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 01/25/2019
ms.author: duau
ms.openlocfilehash: f24a4a0d982ff78ca4d6726e950825ed2c784e67
ms.sourcegitcommit: 0aec60c088f1dcb0f89eaad5faf5f2c815e53bf8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98184548"
---
# <a name="enable-resource-logging-in-azure-traffic-manager"></a>Permitir a sessão de registo de recursos no Azure Traffic Manager

Este artigo descreve como permitir a recolha de registos de recursos de diagnóstico e aceder aos dados de registo para um perfil de Gestor de Tráfego.

Os registos de recursos do Azure Traffic Manager podem fornecer informações sobre o comportamento do recurso de perfil do Gestor de Tráfego. Por exemplo, pode utilizar os dados de registo do perfil para determinar por que as sondas individuais têm cronometrado contra um ponto final.

## <a name="enable-resource-logging"></a>Ativar registos de recursos

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Pode executar os comandos que seguem na [Azure Cloud Shell,](https://shell.azure.com/powershell)ou executando o PowerShell a partir do seu computador. O Azure Cloud Shell é uma concha interativa gratuita. Tem as ferramentas comuns do Azure pré-instaladas e configuradas para utilização com a sua conta. Se executar o PowerShell a partir do computador, precisa do módulo Azure PowerShell, 1.0.0 ou mais tarde. Pode correr `Get-Module -ListAvailable Az` para encontrar a versão instalada. Se precisar de instalar ou atualizar, veja [Install Azure PowerShell module](/powershell/azure/install-az-ps)(Instalar o módulo do Azure PowerShell). Se estiver a executar o PowerShell localmente, também tem de correr `Login-AzAccount` para entrar no Azure.

1. **Recuperar o perfil do Gestor de Tráfego:**

    Para ativar a sessão de registo de recursos, precisa da identificação de um perfil de Gestor de Tráfego. Recupere o perfil de Gestor de Tráfego para o que pretende ativar a sessão de registo de recursos com [o Get-AzTrafficManagerProfile](/powershell/module/az.TrafficManager/Get-azTrafficManagerProfile). A saída inclui a informação de identificação do perfil do Gestor de Tráfego.

    ```azurepowershell-interactive
    Get-AzTrafficManagerProfile -Name <TrafficManagerprofilename> -ResourceGroupName <resourcegroupname>
    ```

2. **Ativar a sessão de registo de recursos para o perfil do Gestor de Tráfego:**

    Ativar a marcação de recursos para o perfil do Gestor de Tráfego utilizando o ID obtido no passo anterior com [Set-AzDiagnosticSetting](/powershell/module/az.monitor/set-azdiagnosticsetting?view=latest). As seguintes lojas de comandos verbose logse para o perfil de Gestor de Tráfego para uma conta de Armazenamento Azure especificada. 

      ```azurepowershell-interactive
    Set-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId> -StorageAccountId <storageAccountId> -Enabled $true
      ``` 
3. **Verifique as definições de diagnóstico:**

      Verifique as definições de diagnóstico do perfil do Gestor de Tráfego utilizando [a Configuração Get-AzDiagnostic .](/powershell/module/az.monitor/get-azdiagnosticsetting?view=latest) O comando a seguir mostra as categorias que são registadas para um recurso.

     ```azurepowershell-interactive
     Get-AzDiagnosticSetting -ResourceId <TrafficManagerprofileResourceId>
     ```  
      Certifique-se de que todas as categorias de registos associadas ao ecrã de perfil do Gestor de Tráfego, conforme ativado. Além disso, verifique se a conta de armazenamento está corretamente definida.

## <a name="access-log-files"></a>Aceder a ficheiros de registos
1. Inicie sessão no [portal do Azure](https://portal.azure.com). 
1. Navegue para a sua conta de Armazenamento Azure no portal.
2. Na página **geral** da sua conta de armazenamento Azure, em **Serviços** selecione **Blobs**.
3. Para **recipientes**, selecione **insights-logs-probehealthstatuses**, e navegue até ao PT1H.jsno ficheiro e clique em **Baixar** para descarregar e guardar uma cópia deste ficheiro de registo.

    ![Aceder aos ficheiros de registo do seu perfil de Gestor de Tráfego a partir de um armazenamento de bolhas](./media/traffic-manager-logs/traffic-manager-logs.png)


## <a name="traffic-manager-log-schema"></a>Esquema de registo do gestor de tráfego

Todos os registos de recursos disponíveis através do Azure Monitor partilham um esquema comum de alto nível, com flexibilidade para cada serviço emitir propriedades únicas para os seus próprios eventos. Para esquemas de registos de recursos de alto nível, consulte [serviços, esquemas e categorias de registos de recursos Azure](../azure-monitor/platform/resource-logs-schema.md).

A tabela seguinte inclui esquema de registos específico do recurso de perfil do Gestor de Tráfego Azure.

|Nome do Campo|Tipo de Campo|Definição|Exemplo|
|----|----|---|---|
|Nome de ponto final|Cadeia|O nome do ponto final do Gestor de Tráfego cujo estado de saúde está a ser registado.|*myPrimaryEndpoint*|
|Estado|Cadeia|O estado de saúde do ponto final do Gerente de Trânsito que foi sondado. O estado pode ser **para cima** ou **para baixo**.|**Cima**|
|||||

## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre [monitorização do Gestor de Tráfego](traffic-manager-monitoring.md)