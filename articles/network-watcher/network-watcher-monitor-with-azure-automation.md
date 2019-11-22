---
title: Solucionar problemas e monitorar gateways de VPN-automação do Azure
titleSuffix: Azure Network Watcher
description: Este artigo descreve como diagnosticar a conectividade local com a automação do Azure e o observador de rede
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 07847289c156aaa48b9d15c40d4135ce2cf39c10
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74275915"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorar gateways de VPN com a solução de problemas do observador de rede

Obter informações aprofundadas sobre o desempenho da rede é essencial para fornecer serviços confiáveis aos clientes. Portanto, é essencial detectar as condições de interrupção da rede rapidamente e tomar uma ação corretiva para reduzir a condição de interrupção. A automação do Azure permite que você implemente e execute uma tarefa de maneira programática por meio de runbooks. Usar a automação do Azure cria uma receita perfeita para executar alertas e monitoramento de rede proativo e contínuo.

## <a name="scenario"></a>Cenário

O cenário na imagem a seguir é um aplicativo de várias camadas, com conectividade local estabelecida usando um gateway de VPN e um túnel. Garantir que o gateway de VPN esteja em funcionamento é essencial para o desempenho dos aplicativos.

Um runbook é criado com um script para verificar o status da conexão do túnel VPN, usando a API de solução de problemas de recursos para verificar o status do túnel de conexão. Se o status não for íntegro, um gatilho de email será enviado aos administradores.

![Exemplo de cenário][scenario]

Este cenário irá:

- Criar um runbook chamando o cmdlet `Start-AzureRmNetworkWatcherResourceTroubleshooting` para solucionar problemas de status de conexão
- Vincular uma agenda ao runbook

## <a name="before-you-begin"></a>Antes de começar

Antes de iniciar este cenário, você deve ter os seguintes pré-requisitos:

- Uma conta de automação do Azure no Azure. Verifique se a conta de automação tem os módulos mais recentes e também tem o módulo AzureRM. Network. O módulo AzureRM. Network estará disponível na Galeria de módulos se você precisar adicioná-lo à sua conta de automação.
- Você deve ter um conjunto de credenciais configuradas na automação do Azure. Saiba mais em [segurança de automação do Azure](../automation/automation-security-overview.md)
- Um servidor SMTP válido (Office 365, seu email local ou outro) e credenciais definidas na automação do Azure
- Um gateway de rede virtual configurado no Azure.
- Uma conta de armazenamento existente com um contêiner existente para armazenar os logs.

> [!NOTE]
> A infraestrutura descrita na imagem anterior é para fins de ilustração e não é criada com as etapas contidas neste artigo.

### <a name="create-the-runbook"></a>Criar o runbook

A primeira etapa para configurar o exemplo é criar o runbook. Este exemplo usa uma conta Executar como. Para saber mais sobre contas Executar como, visite [autenticar Runbooks com a conta Executar como do Azure](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Passo 1

Navegue até a automação do Azure no [portal do Azure](https://portal.azure.com) e clique em **Runbooks**

![Visão geral da conta de automação][1]

### <a name="step-2"></a>Passo 2

Clique em **Adicionar um runbook** para iniciar o processo de criação do runbook.

![folha de runbooks][2]

### <a name="step-3"></a>Passo 3

Em **criação rápida**, clique em **criar um novo runbook** para criar o runbook.

![Adicionar uma folha de runbook][3]

### <a name="step-4"></a>Passo 4

Nesta etapa, damos um nome ao runbook, no exemplo, ele é chamado **Get-VPNGatewayStatus**. É importante dar um nome descritivo ao runbook e é recomendável dar a ele um nome que siga os padrões de nomenclatura padrão do PowerShell. O tipo de runbook para este exemplo é **PowerShell**, as outras opções são gráfico, fluxo de trabalho do PowerShell e fluxo de trabalho gráfico do PowerShell.

![folha do runbook][4]

### <a name="step-5"></a>Passo 5

Nesta etapa, o runbook é criado, o exemplo de código a seguir fornece todo o código necessário para o exemplo. Os itens no código que contêm \<valor\> precisam ser substituídos pelos valores de sua assinatura.

Use o código a seguir como clique em **salvar**

```powershell
# Set these variables to the proper values for your environment
$o365AutomationCredential = "<Office 365 account>"
$fromEmail = "<from email address>"
$toEmail = "<to email address>"
$smtpServer = "<smtp.office365.com>"
$smtpPort = 587
$runAsConnectionName = "<AzureRunAsConnection>"
$subscriptionId = "<subscription id>"
$region = "<Azure region>"
$vpnConnectionName = "<vpn connection name>"
$vpnConnectionResourceGroup = "<resource group name>"
$storageAccountName = "<storage account name>"
$storageAccountResourceGroup = "<resource group name>"
$storageAccountContainer = "<container name>"

# Get credentials for Office 365 account
$cred = Get-AutomationPSCredential -Name $o365AutomationCredential

# Get the connection "AzureRunAsConnection "
$servicePrincipalConnection=Get-AutomationConnection -Name $runAsConnectionName

"Logging in to Azure..."
Connect-AzureRmAccount `
    -ServicePrincipal `
    -TenantId $servicePrincipalConnection.TenantId `
    -ApplicationId $servicePrincipalConnection.ApplicationId `
    -CertificateThumbprint $servicePrincipalConnection.CertificateThumbprint
"Setting context to a specific subscription"
Set-AzureRmContext -SubscriptionId $subscriptionId

$nw = Get-AzurermResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $region }
$networkWatcher = Get-AzureRmNetworkWatcher -Name $nw.Name -ResourceGroupName $nw.ResourceGroupName
$connection = Get-AzureRmVirtualNetworkGatewayConnection -Name $vpnConnectionName -ResourceGroupName $vpnConnectionResourceGroup
$sa = Get-AzureRmStorageAccount -Name $storageAccountName -ResourceGroupName $storageAccountResourceGroup 
$storagePath = "$($sa.PrimaryEndpoints.Blob)$($storageAccountContainer)"
$result = Start-AzureRmNetworkWatcherResourceTroubleshooting -NetworkWatcher $networkWatcher -TargetResourceId $connection.Id -StorageId $sa.Id -StoragePath $storagePath

if($result.code -ne "Healthy")
    {
        $body = "Connection for $($connection.name) is: $($result.code) `n$($result.results[0].summary) `nView the logs at $($storagePath) to learn more."
        Write-Output $body
        $subject = "$($connection.name) Status"
        Send-MailMessage `
        -To $toEmail `
        -Subject $subject `
        -Body $body `
        -UseSsl `
        -Port $smtpPort `
        -SmtpServer $smtpServer `
        -From $fromEmail `
        -BodyAsHtml `
        -Credential $cred
    }
else
    {
    Write-Output ("Connection Status is: $($result.code)")
    }
```

### <a name="step-6"></a>Passo 6

Depois que o runbook é salvo, um agendamento deve ser vinculado a ele para automatizar o início do runbook. Para iniciar o processo, clique em **agendar**.

![Passo 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Vincular uma agenda ao runbook

Uma nova agenda deve ser criada. Clique em **vincular um agendamento ao seu runbook**.

![Passo 7][7]

### <a name="step-1"></a>Passo 1

Na folha **agenda** , clique em **criar um novo agendamento**

![Passo 8][8]

### <a name="step-2"></a>Passo 2

Na folha **novo agendamento** , preencha as informações de agendamento. Os valores que podem ser definidos estão na lista a seguir:

- **Nome** -o nome amigável da agenda.
- **Descrição** -uma descrição da agenda.
- **Inicia** -esse valor é uma combinação de data, hora e fuso horário que compõem a hora em que a agenda é disparada.
- **Recorrência** -esse valor determina a repetição de agendas.  Os valores válidos são **uma vez** ou **recorrentes**.
- **Repetir a cada** -o intervalo de recorrência da agenda em horas, dias, semanas ou meses.
- **Definir expiração** -o valor determina se a agenda deve expirar ou não. Pode ser definido como **Sim** ou **não**. Uma data e hora válidas serão fornecidas se sim for escolhido.

> [!NOTE]
> Se você precisar que um runbook seja executado com mais frequência do que a cada hora, vários agendamentos deverão ser criados em intervalos diferentes (ou seja, 15, 30, 45 minutos após a hora)

![Passo 9][9]

### <a name="step-3"></a>Passo 3

Clique em salvar para salvar o agendamento no runbook.

![Etapa 10][10]

## <a name="next-steps"></a>Passos seguintes

Agora que você tem uma compreensão sobre como integrar a solução de problemas do observador de rede com a automação do Azure, saiba como disparar capturas de pacote em alertas de VM visitando [criar uma captura de pacote disparada por alerta com o observador de rede do Azure](network-watcher-alert-triggered-packet-capture.md).

<!-- images -->
[scenario]: ./media/network-watcher-monitor-with-azure-automation/scenario.png
[1]: ./media/network-watcher-monitor-with-azure-automation/figure1.png
[2]: ./media/network-watcher-monitor-with-azure-automation/figure2.png
[3]: ./media/network-watcher-monitor-with-azure-automation/figure3.png
[4]: ./media/network-watcher-monitor-with-azure-automation/figure4.png
[5]: ./media/network-watcher-monitor-with-azure-automation/figure5.png
[6]: ./media/network-watcher-monitor-with-azure-automation/figure6.png
[7]: ./media/network-watcher-monitor-with-azure-automation/figure7.png
[8]: ./media/network-watcher-monitor-with-azure-automation/figure8.png
[9]: ./media/network-watcher-monitor-with-azure-automation/figure9.png
[10]: ./media/network-watcher-monitor-with-azure-automation/figure10.png
