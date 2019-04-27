---
title: Monitorizar gateways de VPN na resolução de problemas do observador de rede do Azure | Documentos da Microsoft
description: Este artigo descreve como diagnosticar conectividade no local com a automatização do Azure e o observador de rede
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
ms.openlocfilehash: 476fc9aa98eef300b981d2009fcee9244db25e8a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60532950"
---
# <a name="monitor-vpn-gateways-with-network-watcher-troubleshooting"></a>Monitorizar gateways de VPN na resolução de problemas do observador de rede

Obter informações aprofundadas sobre o seu desempenho de rede é essencial para oferecer serviços fiáveis aos clientes. Portanto, é fundamental detetar condições de falha de rede rápida e tomar medidas corretivas para mitigar a condição de falha. A automatização do Azure permite-lhe implementar e executar uma tarefa de forma programática através de runbooks. Utilizar a automatização do Azure cria uma receita perfeita para a execução de alertas e monitorização de rede proativa e contínuo.

## <a name="scenario"></a>Cenário

O cenário na imagem seguinte é um aplicativo de várias camadas, na conectividade de locais estabelecida com um Gateway de VPN e túnel. Garantir que o Gateway de VPN está a funcionar e em execução são fundamental para o desempenho de aplicações.

Um runbook é criado com um script para verificar a existência de estado da ligação do túnel VPN, com a API de resolução de problemas de recursos para verificar a existência de estado de ligação do túnel. Se o estado não está em bom estado, um acionador de e-mail é enviado para os administradores.

![Exemplo de cenário][scenario]

Neste cenário irão:

- Criar um runbook de chamar o `Start-AzureRmNetworkWatcherResourceTroubleshooting` cmdlet para resolver problemas do Estado de ligação
- Ligar uma agenda ao runbook

## <a name="before-you-begin"></a>Antes de começar

Antes de começar este cenário, tem de ter os seguintes pré-requisitos:

- Uma conta de automatização do Azure no Azure. Certifique-se de que a conta de automatização tem os módulos mais recentes e também tem o módulo azurerm. Network. O módulo azurerm. Network está disponível na galeria do módulo se precisar de adicioná-lo à sua conta de automatização.
- Tem de ter um conjunto de credenciais que configurar na automatização do Azure. Saiba mais em [segurança de automatização do Azure](../automation/automation-security-overview.md)
- Um servidor de SMTP válido (Office 365, o seu e-mail no local ou outro) e as credenciais definidas na automatização do Azure
- Um configurado Gateway de rede Virtual no Azure.
- Uma conta de armazenamento existente com um contentor existente para armazenar os registos no.

> [!NOTE]
> A infraestrutura representada na imagem anterior é para fins de ilustração e não são criados com os passos contidos neste artigo.

### <a name="create-the-runbook"></a>Criar o runbook

É o primeiro passo para configurar o exemplo criar o runbook. Este exemplo utiliza uma conta run as. Para saber mais sobre contas Run as, visite [autenticar Runbooks com a conta Run As do Azure](../automation/automation-create-runas-account.md)

### <a name="step-1"></a>Passo 1

Navegue para a automatização do Azure no [portal do Azure](https://portal.azure.com) e clique em **Runbooks**

![Descrição geral da conta de automatização][1]

### <a name="step-2"></a>Passo 2

Clique em **adicionar um runbook** para iniciar o processo de criação do runbook.

![Painel de runbooks][2]

### <a name="step-3"></a>Passo 3

Sob **criação rápida**, clique em **criar um runbook novo** para criar o runbook.

![Adicionar um painel do runbook][3]

### <a name="step-4"></a>Passo 4

Neste passo, vamos atribua ao runbook um nome, o exemplo é chamado **Get-VPNGatewayStatus**. É importante atribua ao runbook um nome descritivo e recomendados, dando a ele um nome que se segue o padrão padrões de nomenclatura do PowerShell. O tipo de runbook para este exemplo é **PowerShell**, as outras opções são gráfico, fluxo de trabalho do PowerShell e o fluxo de trabalho de PowerShell gráfico.

![painel do runbook][4]

### <a name="step-5"></a>Passo 5

Neste passo, que o runbook é criado, o seguinte exemplo de código fornece todo o código necessário para o exemplo. Os itens no código que contêm \<valor\> precisam ser substituídos pelos valores da sua subscrição.

Utilize o seguinte código como clique **guardar**

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

Depois do runbook é guardado, uma agenda têm de ser associada a ele para automatizar o início do runbook. Para iniciar o processo, clique em **agenda**.

![Passo 6][6]

## <a name="link-a-schedule-to-the-runbook"></a>Ligar uma agenda ao runbook

Tem de ser criada uma nova agenda. Clique em **ligar uma agenda ao runbook**.

![Passo 7][7]

### <a name="step-1"></a>Passo 1

Sobre o **agenda** painel, clique em **criar uma nova agenda**

![Passo 8][8]

### <a name="step-2"></a>Passo 2

Sobre o **nova agenda** painel, preencha as informações de agenda. Se os valores que podem ser definidos na lista seguinte:

- **Nome** -o nome amigável da agenda.
- **Descrição** -uma descrição da agenda.
- **Inicia** -este valor é uma combinação de data, hora e fuso horário que compõem o tempo de acionadores de agenda.
- **Periodicidade** -este valor determina a repetição de agendas.  Os valores válidos são **uma vez** ou **periódico**.
- **Repetir cada** -o intervalo de periodicidade da agenda em horas, dias, semanas ou meses.
- **Definir expiração** -o valor determina se a agenda deve expirar ou não. Pode ser definido como **Sim** ou **não**. Uma data válida e a hora estão a ser fornecido se Sim, é escolhido.

> [!NOTE]
> Se precisar de ter um runbook execute com mais frequência do que a cada hora, várias agendas têm de ser criadas em intervalos diferentes (ou seja, 15, 30, 45 minutos após a hora)

![Passo 9][9]

### <a name="step-3"></a>Passo 3

Clique em Guardar para guardar a agenda ao runbook.

![Passo 10][10]

## <a name="next-steps"></a>Passos Seguintes

Agora que tem uma compreensão sobre como integrar a solução de problemas de observador de rede com a automatização do Azure, saiba como acionar capturas de pacotes em alertas VM ao visitar [criar uma captura de pacotes acionadas alerta com o observador de rede do Azure](network-watcher-alert-triggered-packet-capture.md).

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
