---
title: Sessão de escala acolhe Azure Automation - Azure
description: Como escalar automaticamente os anfitriões da sessão de desktop virtual do Windows com a Automação Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 02/06/2020
ms.author: helohr
ms.openlocfilehash: f38fc45411c89351eb9a50a48f22d22905ee34e6
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77367246"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Anfitriões de sessão de escala usando a Automação Azure

Pode reduzir o custo total de implementação do Windows Virtual Desktop, dimensionando as suas máquinas virtuais (VMs). Isto significa desligar e fazer o acordo de sessão de vMs durante as horas de utilização fora do pico, em seguida, ligá-los e realocá-los durante as horas de ponta.

Neste artigo, você vai aprender sobre a ferramenta de escala construída com Azure Automation e Azure Logic Apps que escalará automaticamente as máquinas virtuais de sessão no seu ambiente de ambiente de trabalho virtual Windows. Para aprender a utilizar a ferramenta de dimensionamento, salte para os [Pré-requisitos](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Como funciona a ferramenta de dimensionamento

A ferramenta de escala ção fornece uma opção de automação de baixo custo para os clientes que querem otimizar os custos vm de anfitrião da sessão.

Pode utilizar a ferramenta de dimensionamento para:
 
- Agende VMs para começar e parar com base em horas de trabalho de Pico e Off-Peak.
- Escamas para fora Com base no número de sessões por núcleo CPU.
- Escala em VMs durante as horas off-Peak, deixando o número mínimo de VMs anfitrião da sessão em execução.

A ferramenta de escala utiliza uma combinação de livros de execução Da PowerShell de Automação Azure, webhooks e Aplicações Lógicas Azure para funcionar. Quando a ferramenta funciona, a Azure Logic Apps chama um webhook para iniciar o livro de execução da Automação Azure. O livro de corridas cria então um emprego.

Durante o tempo de utilização máxima, o trabalho verifica o número atual de sessões e a capacidade vm do atual anfitrião da sessão de execução para cada piscina anfitriã. Utiliza estas informações para calcular se os VMs hospedeiros da sessão de execução podem suportar as sessões existentes com base no parâmetro *SessionThresholdPerCPU* definido para o ficheiro **createazurelogicapp.ps1.** Se os VMs anfitriãos da sessão não puderem suportar as sessões existentes, o trabalho inicia vMs anfitriões de sessão adicionais na piscina anfitriã.

>[!NOTE]
>*SessionThresholdPerCPU* não restringe o número de sessões no VM. Este parâmetro só determina quando é necessário começar a carregar as ligações. Para restringir o número de sessões, é necessário seguir as instruções [Set-RdsHostPool](/powershell/module/windowsvirtualdesktop/set-rdshostpool/) para configurar o parâmetro *MaxSessionLimit* em conformidade.

Durante o tempo de utilização fora do pico, o trabalho determina quais os VMs hospedeiros da sessão devem desligar-se com base no parâmetro *NúmeroDeRDSH mínimo.* O trabalho definirá os VMs hospedeiros da sessão para drenar o modo de drenagem para evitar que novas sessões se liguem aos anfitriões. Se definir o parâmetro *LimitSecondsToForceLogOffUser* para um valor positivo não nulo, o script notificará qualquer pessoa atualmente assinada nos utilizadores para salvar o seu trabalho, aguardar o tempo configurado e, em seguida, forçar os utilizadores a assinar. Uma vez assinadas todas as sessões de utilizador da sessão vM, o script desligará o VM.

Se definir o parâmetro *LimitSecondsToForceLogOffUser* para zero, o trabalho permitirá a definição de configuração da sessão em políticas específicas do grupo para lidar com a assinatura de sessões de utilizador. Para ver estas políticas de grupo, vá às **políticas** de **configuração** de computador >  > **modelos administrativos** > componentes do Windows > **Serviços** **de Terminal** > servidor **de terminais** > prazo de **sessão**. Se houver sessões ativas numa vm anfitriã de sessão, o trabalho deixará a vm anfitriã da sessão em funcionamento. Se não houver sessões ativas, o trabalho encerrará a vm anfitriã da sessão.

O trabalho funciona periodicamente com base num intervalo de recorrência. Pode alterar este intervalo com base no tamanho do seu ambiente de trabalho virtual do Windows, mas lembre-se que iniciar e desligar máquinas virtuais pode demorar algum tempo, por isso lembre-se de ter em conta o atraso. Recomendamos que o intervalo de recorrência a cada 15 minutos.

No entanto, a ferramenta também tem as seguintes limitações:

- Esta solução aplica-se apenas aos VMs de anfitrião da sessão.
- Esta solução gere VMs em qualquer região, mas só pode ser usada na mesma subscrição que a sua conta Azure Automation e As Aplicações Lógicas Azure.

>[!NOTE]
>A ferramenta de dimensionamento controla o modo de equilíbrio de carga da piscina hospedeira que está a escalonar. Coloca-o em equilíbrio de carga de primeira para as horas de pico e de pico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar a ferramenta de escala, certifique-se de que tem as seguintes coisas prontas:

- Um [inquilino virtual do Windows e piscina de anfitrião](create-host-pools-arm-template.md)
- VMs de piscina de anfitriões de sessão configurados e registados com o serviço de ambiente de trabalho virtual do Windows
- Um utilizador com [acesso a Contribuinte](../role-based-access-control/role-assignments-portal.md) na subscrição do Azure

A máquina que utiliza para implantar a ferramenta deve ter: 

- Windows PowerShell 5.1 ou mais tarde
- O módulo Microsoft Az PowerShell

Se tem tudo pronto, vamos começar.

## <a name="create-an-azure-automation-account"></a>Crie uma conta de Automatização do Azure

Primeiro, vai precisar de uma conta Azure Automation para executar o livro de execução PowerShell. Aqui está como configurar a sua conta:

1. Abra o Windows PowerShell como administrador.
2. Execute o seguinte cmdlet para iniciar sessão na sua Conta Azure.

     ```powershell
     Login-AzAccount
     ```

     >[!NOTE]
     >A sua conta deve ter direitos contributivos sobre a subscrição do Azure que pretende implementar a ferramenta de escalação.

3. Executar o seguinte cmdlet para descarregar o script para criar a conta De automação Azure:

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazureautomationaccount.ps1" -OutFile "your local machine path\ createazureautomationaccount.ps1"
     ```

4. Execute o seguinte cmdlet para executar o script e crie a conta De Automação Azure:

     ```powershell
     .\createazureautomationaccount.ps1 -SubscriptionID <azuresubscriptionid> -ResourceGroupName <resourcegroupname> -AutomationAccountName <name of automation account> -Location "Azure region for deployment"
     ```

5. A saída do cmdlet incluirá um webhook URI. Certifique-se de manter um registo do URI porque vai usá-lo como parâmetro quando configurar o calendário de execução das aplicações Azure Logic.

Depois de configurar a sua conta Azure Automation, inscreva-se na sua subscrição Azure e verifique se a sua conta De automação Azure e o livro de execução relevante apareceram no seu grupo de recursos especificado, como mostra a seguinte imagem:

![Uma imagem da página geral do Azure mostrando a conta de automação recém-criada e o livro de execução.](media/automation-account.png)

Para verificar se o seu webhook está onde deveria estar, vá à lista de Recursos do lado esquerdo do ecrã e selecione **Webhook**.

## <a name="create-an-azure-automation-run-as-account"></a>Criar uma corrida de automação Azure Como conta

Agora que tem uma conta Azure Automation, também terá de criar uma Corrida de Automação Azure Como conta para aceder aos seus recursos Azure.

Uma execução de [automação Azure Como conta](../automation/manage-runas-account.md) fornece autenticação para gestão de recursos em Azure com os cmdlets Azure. Ao criar uma conta Run As, cria um novo utilizador principal de serviço no Diretório Ativo do Azure e atribui a função de Colaborador ao principal utilizador do serviço ao nível da subscrição, a Conta Azure Run As é uma ótima forma de autenticar de forma segura com certificados e um nome principal de serviço sem necessidade de armazenar um nome de utilizador e senha num objeto credencial. Para saber mais sobre executar Como autenticação, consulte [Limiting Run As permissões](../automation/manage-runas-account.md#limiting-run-as-account-permissions)de conta .

Qualquer utilizador que seja membro da função de Administrador de Subscrição e coadministrador da subscrição pode criar uma conta Run As seguindo as instruções da próxima secção.

Para criar uma conta Run As na sua conta Azure:

1. No portal do Azure, selecione **Todos os serviços**. Na lista de recursos, insira e selecione **Contas de Automação.**

2. Na página Contas de **Automação,** selecione o nome da sua conta Desmase.

3. No painel do lado esquerdo da janela, selecione **Executar Como Contas** sob a secção Definições da Conta.

4. Selecione **Azure Executar como conta**. Quando aparecer o painel **Add Azure Run As Account,** reveja as informações de visão geral e, em seguida, selecione **Criar** para iniciar o processo de criação de conta.

5. Aguarde alguns minutos para azure criar a conta Run As. Pode acompanhar o progresso da criação no menu em Notificações.

6. Quando o processo terminar, criará um ativo chamado AzureRunAsConnection na conta de Automação especificada. O ativo de ligação detém o ID da aplicação, id do inquilino, ID de subscrição e impressão digital do certificado. Lembre-se da identificação da inscrição, porque vai usá-lo mais tarde.

### <a name="create-a-role-assignment-in-windows-virtual-desktop"></a>Criar uma atribuição de funções no Windows Virtual Desktop

Em seguida, é necessário criar uma atribuição de funções para que o AzureRunAsConnection possa interagir com o Windows Virtual Desktop. Certifique-se de usar o PowerShell para iniciar sessão com uma conta que tenha permissões para criar atribuições de papéis.

Primeiro, descarregue e importe o [módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez. Execute os seguintes cmdlets PowerShell para ligar ao Windows Virtual Desktop e mostrar os seus inquilinos.

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"

Get-RdsTenant
```

Quando encontrar o inquilino com as piscinas hospedeiras que pretende escalar, siga as instruções na [Create a Azure Automation account](#create-an-azure-automation-account) e use o nome de inquilino que obteve do cmdlet anterior no seguinte cmdlet para criar a atribuição de funções:

```powershell
New-RdsRoleAssignment -RoleDefinitionName "RDS Contributor" -ApplicationId <applicationid> -TenantName <tenantname>
```

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Criar a App Lógica Azure e o calendário de execução

Finalmente, terá de criar a App Lógica Azure e configurar um calendário de execução para a sua nova ferramenta de escala.

1.  Abra o PowerShell do Windows como administrador

2.  Execute o seguinte cmdlet para iniciar sessão na sua Conta Azure.

     ```powershell
     Login-AzAccount
     ```

3. Execute o seguinte cmdlet para descarregar o ficheiro script createazurelogicapp.ps1 na sua máquina local.

     ```powershell
     Invoke-WebRequest -Uri "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/createazurelogicapp.ps1" -OutFile "your local machine path\ createazurelogicapp.ps1"
     ```

4. Execute o seguinte cmdlet para iniciar sessão no Windows Virtual Desktop com uma conta que tenha permissões RDS Owner ou RDS Contributor.

     ```powershell
     Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
     ```

5. Execute o seguinte script PowerShell para criar a aplicação e o calendário de execução da Azure Logic.

     ```powershell
     $resourceGroupName = Read-Host -Prompt "Enter the name of the resource group for the new Azure Logic App"
     
     $aadTenantId = Read-Host -Prompt "Enter your Azure AD tenant ID"

     $subscriptionId = Read-Host -Prompt "Enter your Azure Subscription ID"

     $tenantName = Read-Host -Prompt "Enter the name of your WVD tenant"

     $hostPoolName = Read-Host -Prompt "Enter the name of the host pool you’d like to scale"

     $recurrenceInterval = Read-Host -Prompt "Enter how often you’d like the job to run in minutes, e.g. ‘15’"

     $beginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"

     $endPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"

     $timeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"

     $sessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"

     $minimumNumberOfRdsh = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"

     $limitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, users will be signed out immediately"

     $logOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"

     $logOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

     $location = Read-Host -Prompt "Enter the name of the Azure region where you will be creating the logic app"

     $connectionAssetName = Read-Host -Prompt "Enter the name of the Azure RunAs connection asset"

     $webHookURI = Read-Host -Prompt "Enter the URI of the WebHook returned by when you created the Azure Automation Account"

     $automationAccountName = Read-Host -Prompt "Enter the name of the Azure Automation Account"

     $maintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don’t want to be managed by this scaling tool"

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

     Depois de executar o script, a Aplicação Lógica deve aparecer num grupo de recursos, como mostra a imagem seguinte.

     ![Uma imagem da página geral para um exemplo Azure Logic App.](media/logic-app.png)

Para fazer alterações no calendário de execução, como alterar o intervalo de recorrência ou fuso horário, vá ao programador de escala automática e selecione **Editar** para ir ao Logic Apps Designer.

![Uma imagem do Designer de Aplicações Lógicas. Os menus Recurrence e Webhook que permitem ao utilizador editar tempos de recorrência e o ficheiro webhook estão abertos.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gerencie a sua ferramenta de dimensionamento

Agora que criou a sua ferramenta de escala, pode aceder à sua saída. Esta secção descreve algumas funcionalidades que poderá achar úteis.

### <a name="view-job-status"></a>Ver o estado de tarefa

Pode ver um estado resumido de todos os trabalhos de livro ou ver um estado mais aprofundado de um trabalho específico de livro de corridas no portal Azure.

À direita da sua conta de Automação selecionada, em "Job Statistics", pode ver uma lista de resumos de todos os trabalhos de livro. A abertura da página **Jobs** no lado esquerdo da janela mostra o estado atual do emprego, os horários de início e os tempos de conclusão.

![Uma imagem da página do estado do emprego.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Ver registos e saída de ferramentas de escala

Pode visualizar os registos de operações de escala e escala, abrindo o seu livro de execução e selecionando o nome do seu trabalho.

Navegue para o livro de execução (o nome predefinido é WVDAutoScaleRunbook) no seu grupo de recursos que acolhe a conta De automação Azure e selecione **visão geral**. Na página geral, selecione um trabalho no Recent Jobs para ver a sua saída de ferramentas de escala, como mostra a imagem seguinte.

![Uma imagem da janela de saída para a ferramenta de escala.](media/tool-output.png)
