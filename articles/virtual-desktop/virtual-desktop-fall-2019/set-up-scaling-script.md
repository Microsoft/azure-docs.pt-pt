---
title: Sessão de escala acolhe Azure Automation - Azure
description: Como escalar automaticamente os anfitriões da sessão virtual do Windows desktop com a Azure Automation.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f94852a99f0bc430ac193b9951de607cdd7fa933
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85362548"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Anfitriões de sessão de escala usando Azure Automation

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure.

Pode reduzir o custo total de implementação do Ambiente de Trabalho Virtual do Windows escalando as suas máquinas virtuais (VMs). Isto significa desligar e lidar com vMs anfitriões de sessão durante as horas de utilização fora do pico, em seguida, ligá-los novamente e realocar-los durante as horas de ponta.

Neste artigo, você vai aprender sobre a ferramenta de escala construído com Azure Automation e Azure Logic Apps que irão automaticamente escalar máquinas virtuais de hospedagem de sessão no seu ambiente de desktop virtual Windows. Para aprender a utilizar a ferramenta de escala, avance para [pré-requisitos](#prerequisites).

## <a name="report-issues"></a>Comunicar problemas

Os relatórios de emissão da ferramenta de escala estão atualmente a ser manipulados no GitHub em vez do Microsoft Support. Se encontrar algum problema com a ferramenta de escala, pode denunciá-los bu abrindo um problema GitHub com o rótulo "4a-WVD-scaling-logicapps" na [página RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4a-WVD-scaling-logicapps).

## <a name="how-the-scaling-tool-works"></a>Como funciona a ferramenta de escala

A ferramenta de escala fornece uma opção de automação de baixo custo para clientes que querem otimizar os custos de VM do anfitrião da sessão.

Pode utilizar a ferramenta de escala para:

- Agende VMs para iniciar e parar com base nas horas de trabalho de Peak e Off-Peak.
- Dimensione os VMs com base no número de sessões por núcleo de CPU.
- Escala em VMs durante as horas off-peak, deixando o número mínimo de VMs de sessão em execução.

A ferramenta de escala utiliza uma combinação de runbooks PowerShell, webhooks e Azure Logic Apps para funcionar. Quando a ferramenta é executada, a Azure Logic Apps chama um webhook para iniciar o runbook Azure Automation. O livro de bordo cria então um emprego.

Durante o tempo de utilização do pico, o trabalho verifica o número atual de sessões e a capacidade VM do anfitrião atual da sessão de funcionamento para cada piscina hospedeira. Utiliza estas informações para calcular se os VMs do anfitrião da sessão de funcionamento podem suportar as sessões existentes com base no parâmetro *SessionThresholdPerCPU* definido para o ficheiro **createazurelogicapp.ps1.** Se os VMs do anfitrião da sessão não puderem suportar as sessões existentes, o trabalho inicia vMs adicionais de anfitrião na piscina de anfitriões.

>[!NOTE]
>*SessionThresholdPerCPU* não restringe o número de sessões no VM. Este parâmetro só determina quando é necessário iniciar novos VMs para equilibrar as ligações. Para restringir o número de sessões, tem de seguir as instruções [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) para configurar o parâmetro *MaxSessionLimit* em conformidade.

Durante o tempo de utilização fora do pico, o trabalho determina que VMs de anfitrião de sessão devem ser desligados com base no parâmetro *Número Mínimo DeRDSH.* O trabalho irá definir os VMs do anfitrião da sessão para drenar o modo de drenagem para evitar novas sessões de ligação aos anfitriões. Se definir o parâmetro *LimitSecondsToForceLogOffUser* para um valor positivo não zero, o trabalho notificará qualquer um que esteja atualmente assinado nos utilizadores para salvar o seu trabalho, aguardará o tempo configurado e, em seguida, forçará os utilizadores a assinar. Uma vez que todas as sessões de utilizador no VM do anfitrião da sessão tenham sido assinadas, o trabalho irá encerrar o VM.

Se definir o parâmetro *LimitSecondsToForceLogOffUser* para zero, o trabalho permitirá que a definição de configuração da sessão em políticas de grupo especificadas manuseie a assinatura de sessões de utilizador. Para ver estas políticas de grupo, aceda às políticas de **configuração do computador**  >  **Policies**  >  **Modelos Administrativos**  >  **Modelos de Sistemas de**  >  **Serviços terminais de serviços terminais**de  >  **Terminal Server**  >  **serviços terminais de servidores de sessão**. Se houver sessões ativas num VM anfitrião de sessão, o trabalho deixará o VM anfitrião da sessão em funcionamento. Se não houver sessões ativas, o trabalho encerrará o VM anfitrião da sessão.

O trabalho funciona periodicamente com base num intervalo de recorrência definido. Pode alterar este intervalo com base no tamanho do ambiente de ambiente de trabalho virtual do Windows, mas lembre-se que iniciar e desligar máquinas virtuais pode demorar algum tempo, por isso lembre-se de ter em conta o atraso. Recomendamos que se ajuste o intervalo de recorrência a cada 15 minutos.

No entanto, a ferramenta também tem as seguintes limitações:

- Esta solução aplica-se apenas aos VMs de anfitrião de sessão em conjunto.
- Esta solução gere VMs em qualquer região, mas só pode ser usada na mesma subscrição que a sua conta Azure Automation e Azure Logic Apps.

>[!NOTE]
>A ferramenta de escalonamento controla o modo de equilíbrio de carga da piscina hospedeira que está a escalonar. Coloca-o no primeiro equilíbrio de carga para horas de pico e fora do pico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar a ferramenta de escala, certifique-se de que tem as seguintes coisas prontas:

- Um [inquilino virtual do Windows desktop e piscina de anfitrião](create-host-pools-arm-template.md)
- VMs de piscina de anfitriões de sessão configurados e registados com o serviço de desktop virtual do Windows
- Um utilizador com [acesso do Contribuinte](../../role-based-access-control/role-assignments-portal.md) à subscrição do Azure

A máquina que utiliza para implantar a ferramenta deve ter:

- Windows PowerShell 5.1 ou mais tarde
- O módulo Microsoft Az PowerShell

Se tem tudo pronto, vamos começar.

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

Primeiro, vai precisar de uma conta Azure Automation para executar o livro de execução PowerShell. Eis como configurar a sua conta:

1. Abra o Windows PowerShell Como um administrador.
2. Execute o cmdlet seguinte para iniciar súm na sua Conta Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >A sua conta deve ter direitos de contribuinte sobre a subscrição do Azure em que pretende implantar a ferramenta de escala.

3. Execute o seguinte cmdlet para descarregar o script para criar a conta Azure Automation:

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazureautomationaccount.ps1"
     ```

4. Execute o seguinte cmdlet para executar o script e criar a conta Azure Automation:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. A saída do cmdlet incluirá um webhook URI. Certifique-se de manter um registo do URI porque irá usá-lo como parâmetro quando configurar o calendário de execução para as aplicações Azure Logic.

6. Depois de configurar a sua conta Azure Automation, inscreva-se na sua subscrição Azure e verifique se a sua conta Azure Automation e o livro de verificação relevante apareceram no seu grupo de recursos especificado, como mostra a seguinte imagem:

> [!div class="mx-imgBorder"]
> ![Uma imagem da página geral do Azure mostrando a conta de automação e o runbook recém-criado.](../media/automation-account.png)

  Para verificar se o seu webhook está onde deveria estar, selecione o nome do seu runbook. Em seguida, vá à secção de Recursos do seu runbook e selecione **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Criar uma execução de automação Azure Como conta

Agora que tem uma conta Azure Automation, também terá de criar uma conta Azure Automation Como conta para aceder aos seus recursos Azure.

Uma [Azure Automation Run Como conta](../../automation/manage-runas-account.md) fornece autenticação para gestão de recursos em Azure com os cmdlets Azure. Quando cria uma conta Run As, cria um novo utilizador principal de serviço no Azure Ative Directory e atribui a função contribuinte ao utilizador principal do serviço ao nível da subscrição, a Conta Azure Run As é uma ótima maneira de autenticar de forma segura com certificados e um nome principal de serviço sem precisar de armazenar um nome de utilizador e senha num objeto credencial. Para saber mais sobre a autenticação Run As, consulte [Limite executar como permissões de conta](../../automation/manage-runas-account.md#limit-run-as-account-permissions).

Qualquer utilizador que seja membro da função de Administrador de Subscrição e coadministrator da subscrição pode criar uma conta Run As seguindo as instruções da secção seguinte.

Para criar uma conta Run As na sua conta Azure:

1. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, insira e selecione **Contas de Automação.**

2. Na página **'Contas de Automação',** selecione o nome da sua conta Dem automação.

3. No painel do lado esquerdo da janela, selecione **Executar como contas** na secção Definições de Conta.

4. Selecione **Azure Run as Account**. Quando o painel **de contas Add Azure Run As Appears,** reveja as informações gerais e, em seguida, selecione **Criar** para iniciar o processo de criação de conta.

5. Aguarde alguns minutos para que o Azure crie a conta Run As. Pode acompanhar o progresso da criação no menu em notificações.

6. Quando o processo terminar, criará um ativo chamado AzureRunAsConnection na conta de Automação especificada. O ativo de ligação detém o ID da aplicação, iD do inquilino, ID de assinatura e impressão digital de certificado. Lembre-se da identificação da aplicação, porque vai usá-la mais tarde.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de funções no Windows Virtual Desktop

Em seguida, precisa de criar uma atribuição de funções para que o AzureRunAsConnection possa interagir com o Windows Virtual Desktop. Certifique-se de usar o PowerShell para iniciar sessão com uma conta que tenha permissões para criar atribuições de funções.

Em primeiro lugar, descarregue e importe o [módulo PowerShell virtual do Windows Desktop](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez. Execute os seguintes cmdlets PowerShell para ligar ao Windows Virtual Desktop e exibir os seus inquilinos.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Quando encontrar o inquilino com as piscinas hospedeiras que pretende escalar, siga as instruções na [Create a Azure Automation account](#create-an-azure-automation-account) e use o nome do inquilino que obteve do cmdlet anterior no cmdlet seguinte para criar a atribuição de funções:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Crie a App Azure Logic e o calendário de execução

Por fim, terá de criar a App Azure Logic e definir um calendário de execução para a sua nova ferramenta de escala.

1.  Open Windows PowerShell como administrador

2.  Execute o cmdlet seguinte para iniciar súm na sua Conta Azure.

     ```powershell
     Login-AzAccount
     ```

3. Execute o cmdlet seguinte para descarregar o ficheiro de script createazurelogicapp.ps1 na sua máquina local.

     ```powershell
     Set-Location -Path "c:\temp"
     $uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1"
     Invoke-WebRequest -Uri $uri -OutFile ".\createazurelogicapp.ps1"
     ```

4. Execute o cmdlet seguinte para iniciar serção no Windows Virtual Desktop com uma conta que tenha permissões do Proprietário RDS ou do Contribuinte RDS.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Executa o seguinte script PowerShell para criar a app Azure Logic e o calendário de execução.

     ```powershell
     $aadTenantId = (Get-AzContext).Tenant.Id

     $azureSubscription = Get-AzSubscription | Out-GridView -PassThru -Title "Select your Azure Subscription"
     Select-AzSubscription -Subscription $azureSubscription.Id
     $subscriptionId = $azureSubscription.Id

     $resourceGroup = Get-AzResourceGroup | Out-GridView -PassThru -Title "Select the resource group for the new Azure Logic App"
     $resourceGroupName = $resourceGroup.ResourceGroupName
     $location = $resourceGroup.Location

     $wvdTenant = Get-RdsTenant | Out-GridView -PassThru -Title "Select your WVD tenant"
     $tenantName = $wvdTenant.TenantName

     $wvdHostpool = Get-RdsHostPool -TenantName $wvdTenant.TenantName | Out-GridView -PassThru -Title "Select the host pool you'd like to scale"
     $hostPoolName = $wvdHostpool.HostPoolName

     $recurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"
     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $automationAccount = Get-AzAutomationAccount -ResourceGroupName $resourceGroup.ResourceGroupName | Out-GridView -PassThru
     $automationAccountName = $automationAccount.AutomationAccountName
     $automationAccountConnection = Get-AzAutomationConnection -ResourceGroupName $resourceGroup.ResourceGroupName -AutomationAccountName $automationAccount.AutomationAccountName | Out-GridView -PassThru -Title "Select the Azure RunAs connection asset"
     $connectionAssetName = $automationAccountConnection.Name

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"
     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"

     .\createazurelogicapp.ps1 -ResourceGroupName $resourceGroupName `
       -AADTenantID $aadTenantId `
       -SubscriptionID $subscriptionId `
       -TenantName $tenantName `
       -HostPoolName $hostPoolName `
       -RecurrenceInterval $recurrenceInterval `
       -BeginPeakTime $beginPeakTime `
       -EndPeakTime $endPeakTime `
       -TimeDifference $timeDifference `
       -SessionThresholdPerCPU $sessionThresholdPerCPU `
       -MinimumNumberOfRDSH $minimumNumberOfRdsh `
       -LimitSecondsToForceLogOffUser $limitSecondsToForceLogOffUser `
       -LogOffMessageTitle $logOffMessageTitle `
       -LogOffMessageBody $logOffMessageBody `
       -Location $location `
       -ConnectionAssetName $connectionAssetName `
       -WebHookURI $webHookURI `
       -AutomationAccountName $automationAccountName `
       -MaintenanceTagName $maintenanceTagName
     ```

     Depois de executar o script, a App Lógica deve aparecer num grupo de recursos, como mostra a seguinte imagem.

     > [!div class="mx-imgBorder"]
     > ![Uma imagem da página de visão geral para um exemplo Azure Logic App.](../media/logic-app.png)

Para escoar alterações no calendário de execução, como alterar o intervalo de recorrência ou o fuso horário, vá ao programador de escala automática e selecione **Editar** para ir ao Logic Apps Designer.

> [!div class="mx-imgBorder"]
> ![Uma imagem do Designer de Aplicações Lógicas. Os menus Recurrence e Webhook que permitem ao utilizador editar tempos de recorrência e o ficheiro webhook estão abertos.](../media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gerencie a sua ferramenta de escala

Agora que criou a sua ferramenta de escala, pode aceder à sua saída. Esta secção descreve algumas funcionalidades que poderá achar úteis.

### <a name="view-job-status"></a>Ver o estado de tarefa

Pode ver um estado resumido de todos os trabalhos de runbook ou ver um estado mais aprofundado de um trabalho específico no portal Azure.

À direita da sua conta de Automação selecionada, em "Estatísticas de Emprego", pode ver uma lista de resumos de todos os trabalhos de runbook. A abertura da página **Jobs** no lado esquerdo da janela mostra o estado atual do trabalho, os horários de início e os tempos de conclusão.

> [!div class="mx-imgBorder"]
> ![Uma imagem da página do estado do trabalho.](../media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Ver registos e saída de ferramenta de escala

Pode visualizar os registos de operações de escala e dimensionamento, abrindo o seu runbook e selecionando o nome do seu trabalho.

Navegue para o runbook (o nome predefinido é WVDAutoScaleRunbook) no seu grupo de recursos que hospeda a conta Azure Automation e selecione **Overview**. Na página geral, selecione um trabalho em Empregos Recentes para ver a sua saída de ferramenta de escala, como mostra a imagem seguinte.

> [!div class="mx-imgBorder"]
> ![Uma imagem da janela de saída para a ferramenta de escala.](../media/tool-output.png)

