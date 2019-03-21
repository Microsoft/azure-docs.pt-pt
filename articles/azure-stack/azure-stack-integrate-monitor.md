---
title: Integrar a solução de monitorização externa com o Azure Stack | Documentos da Microsoft
description: Saiba como integrar o Azure Stack com uma solução de monitorização externa no seu datacenter.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: article
ms.date: 02/06/2019
ms.author: jeffgilb
ms.reviewer: thoroet
ms.lastreviewed: 02/06/2019
ms.openlocfilehash: 64a31e0c8a36b7ea8b60f65caefba9ba15b91777
ms.sourcegitcommit: aa3be9ed0b92a0ac5a29c83095a7b20dd0693463
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58258739"
---
# <a name="integrate-external-monitoring-solution-with-azure-stack"></a>Integrar a solução de monitorização externa com o Azure Stack

Para a monitorização externos da infraestrutura do Azure Stack, terá de monitorizar o software do Azure Stack, os computadores físicos e os comutadores de rede física. Cada uma dessas áreas oferece um método para obter informações de estado de funcionamento e o alerta:

- Software de pilha do Azure oferece uma API baseada em REST para obter o estado de funcionamento e alertas. A utilização de tecnologias definidas pelo software, como espaços de armazenamento direto, o estado de funcionamento do armazenamento e alertas são a parte da monitorização de software.
- Computadores físicos podem disponibilizar estado de funcionamento e informações de alerta por controladores de gestão da placa base (BMCs).
- Dispositivos de rede físicos podem disponibilizar informações de alerta e de estado de funcionamento através do protocolo SNMP.

Cada solução do Azure Stack é fornecido com um host de ciclo de vida do hardware. Este anfitrião é executado o software de monitorização do fornecedor de hardware de fabricante de equipamento Original (OEM) para os servidores físicos e os dispositivos de rede. Se assim o desejar, pode ignorar estas soluções de monitorização e integrar diretamente com soluções de monitorização existentes no seu datacenter.

> [!IMPORTANT]
> A solução de monitorização externa que utilizar tem de ser sem agente. Não é possível instalar agentes de terceiros dentro de componentes do Azure Stack.

O diagrama seguinte mostra o fluxo de tráfego entre um sistema integrado do Azure Stack, o anfitrião de ciclo de vida do hardware, uma solução de monitorização externa e um sistema de coleção de concessão de permissões/dados externos.

![Diagrama que mostra o tráfego entre o Azure Stack, monitoramento e solução de emissão de permissões.](media/azure-stack-integrate-monitor/MonitoringIntegration.png)  

> [!NOTE]
> Integração de monitorização externos diretamente com servidores físicos e dispositivos de rede não é permitida e ativamente bloqueada por listas de controlo de acesso (ACL). 

Este artigo explica como integrar o Azure Stack com soluções de monitorização externas, como o System Center Operations Manager e Nagios. Ele também inclui como trabalhar com alertas por meio de programação com o PowerShell ou por meio de chamadas de REST API.

## <a name="integrate-with-operations-manager"></a>Integrar com o Operations Manager

Pode utilizar o Gestor de operações para monitorização externos do Azure Stack. O pacote de gestão do System Center para o Microsoft Azure Stack permite-lhe monitorizar várias implementações do Azure Stack com uma única instância do Operations Manager. O pacote de gestão utiliza o fornecedor de recursos do Estado de funcionamento e o fornecedor de recursos de atualização REST APIs para comunicar com o Azure Stack. Se quiser ignorar o OEM software que está a ser executado no anfitrião de ciclo de vida do hardware de monitorização, é possível instalar pacotes de gestão do fornecedor para monitorar os servidores físicos. Também pode utilizar a deteção de dispositivos de rede do Operations Manager para monitorizar os comutadores de rede.

O pacote de gestão para o Azure Stack fornece as seguintes capacidades:

- Pode gerir várias implementações do Azure Stack
- Há suporte para o Azure Active Directory (Azure AD) e serviços de Federação do Active Directory (AD FS)
- Pode obter e fechar alertas
- Existe um Estado de funcionamento e um dashboard de capacidade
- Inclui a deteção de modo de manutenção automática para quando os patches e atualizações (P & U) está em curso
- Inclui tarefas de atualização de imposição para implementação e região
- Pode adicionar informações personalizadas para uma região
- Suporta notificações e relatórios

Pode transferir o pacote de gestão do System Center para o Microsoft Azure Stack e associada [Guia do usuário](https://www.microsoft.com/en-us/download/details.aspx?id=55184), ou diretamente a partir do Operations Manager.

Para uma solução de emissão de permissões, pode integrar o Operations Manager com o System Center Service Manager. O conector de produto integrado permite comunicação bidirecional, que permite-lhe fechar um alerta no Azure Stack e o Operations Manager depois de resolver um pedido de serviço no Service Manager.

O diagrama seguinte mostra a integração do Azure Stack com uma implementação existente do System Center. Pode automatizar o Service Manager ainda mais com o System Center Orchestrator ou o Service Management Automation (SMA) para executar operações no Azure Stack.

![Diagrama que mostra a integração com o OM, o Service Manager e o SMA.](media/azure-stack-integrate-monitor/SystemCenterIntegration.png)

## <a name="integrate-with-nagios"></a>Integrar com o Nagios

Um Nagios Plug-in de monitorização foi desenvolvido em conjunto com as soluções de Cloudbase de parceiros, que está disponível sob a licença permissiva software gratuito – MIT (Massachusetts Institute of Technology).

O plug-in é escrito em Python e utiliza a API de REST do fornecedor de recursos de estado de funcionamento. Ele oferece funcionalidade básica para recuperar e fechar alertas no Azure Stack. Como o pacote de gestão do System Center, permite-lhe para adicionar várias implementações do Azure Stack e para enviar notificações.

O plug-in funciona com Nagios Enterprise e Nagios Core. Pode baixá-lo [aqui](https://exchange.nagios.org/directory/Plugins/Cloud/Monitoring-AzureStack-Alerts/details). O site de download também inclui detalhes de instalação e configuração.

### <a name="plugin-parameters"></a>Parâmetros de plug-in

Configure o ficheiro de plug-in "Azurestack_plugin.py" com os seguintes parâmetros:

| Parâmetro | Descrição | Exemplo |
|---------|---------|---------|
| *arm_endpoint* | Ponto final do Gestor de recursos (administrador) do Azure |https:\//adminmanagement.local.azurestack.external |
| *api_endpoint* | Ponto final do Gestor de recursos (administrador) do Azure  | https:\//adminmanagement.local.azurestack.external |
| *Tenant_id* | ID de subscrição de administrador | Obter através do portal de administrador ou o PowerShell |
| *User_name* | O nome de utilizador do operador subscrição | operator@myazuredirectory.onmicrosoft.com |
| *User_password* | Palavra-passe de subscrição de operador | mypassword |
| *Client_id* | Cliente | 0a7bdc5c-7b57-40be-9939-d4c5fc7cd417* |
| *region* |  Nome da região do Azure Stack | local |
|  |  |

* O GUID do PowerShell que é fornecida é universal. Pode usá-lo para cada implementação.

## <a name="use-powershell-to-monitor-health-and-alerts"></a>Utilizar o PowerShell para o estado de funcionamento do monitor e alertas

Se não estiver a utilizar o Operations Manager, Nagios ou uma solução baseada em Nagios, pode utilizar o PowerShell para ativar uma vasta gama de soluções para integrar com o Azure Stack de monitorização.

1. Para utilizar o PowerShell, certifique-se de que tenha [PowerShell instalado e configurado](azure-stack-powershell-configure-quickstart.md) para um ambiente de operador do Azure Stack. Instalar o PowerShell num computador local que pode alcançar o ponto de final do Gestor de recursos (administrador) (https:\//adminmanagement. [ Região]. [External_FQDN]).

2. Execute os seguintes comandos para estabelecer ligação ao ambiente do Azure Stack como um operador do Azure Stack:

   ```PowerShell  
    Add-AzureRMEnvironment -Name "AzureStackAdmin" -ArmEndpoint https:\//adminmanagement.[Region].[External_FQDN]

   Add-AzureRmAccount -EnvironmentName "AzureStackAdmin"
   ```

3. Utilizar comandos, como os exemplos seguintes para trabalhar com alertas:
   ```PowerShell
    #Retrieve all alerts
    Get-AzsAlert

    #Filter for active alerts
    $Active=Get-AzsAlert | Where {$_.State -eq "active"}
    $Active

    #Close alert
    Close-AzsAlert -AlertID "ID"

    #Retrieve resource provider health
    Get-AzsRPHealth

    #Retrieve infrastructure role instance health
    $FRPID=Get-AzsRPHealth|Where-Object {$_.DisplayName -eq "Capacity"}
    Get-AzsRegistrationHealth -ServiceRegistrationId $FRPID.RegistrationId

    ```

## <a name="learn-more"></a>Saiba mais

Para obter informações sobre a monitorização de estado de funcionamento incorporado, consulte [monitorizar o estado de funcionamento e alertas no Azure Stack](azure-stack-monitor-health.md).

## <a name="next-steps"></a>Passos Seguintes

[Integração de segurança](azure-stack-integrate-security.md)
