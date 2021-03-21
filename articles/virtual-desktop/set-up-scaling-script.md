---
title: Sessão de escala acolhe Azure Automation - Azure
description: Como escalar automaticamente os anfitriões da sessão virtual do Windows desktop com a Azure Automation.
author: Heidilohr
ms.topic: how-to
ms.date: 03/09/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: f60341ea51f1cf4e856b1b4598887da3dc37ebb2
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102613124"
---
# <a name="scale-session-hosts-using-azure-automation"></a>Anfitriões de sessão de escala usando Azure Automation

Pode reduzir o custo total de implementação do Ambiente de Trabalho Virtual do Windows escalando as suas máquinas virtuais (VMs). Isto significa desligar e lidar com vMs anfitriões de sessão durante as horas de utilização fora do pico, em seguida, ligá-los novamente e realocar-los durante as horas de ponta.

Neste artigo, você vai aprender sobre a ferramenta de escala construído com a conta Azure Automation e Azure Logic App que escala automaticamente os VMs anfitriões de sessão no seu ambiente de ambiente de trabalho virtual do Windows. Para aprender a utilizar a ferramenta de escala, avance para [pré-requisitos](#prerequisites).

## <a name="how-the-scaling-tool-works"></a>Como funciona a ferramenta de escala

A ferramenta de escala fornece uma opção de automação de baixo custo para clientes que querem otimizar os custos de VM do anfitrião da sessão.

Pode utilizar a ferramenta de escala para:

- Agende VMs para iniciar e parar com base no Peak e Off-Peak horas de trabalho.
- Dimensione os VMs com base no número de sessões por núcleo de CPU.
- Escala em VMs durante Off-Peak horas, deixando o número mínimo de VMs de sessão em funcionamento.

A ferramenta de escala utiliza uma combinação de uma conta Azure Automation, um runbook PowerShell, um webhook e a App Azure Logic para funcionar. Quando a ferramenta é executada, a Azure Logic App chama um webhook para iniciar o runbook Azure Automation. O livro de bordo cria então um emprego.

Durante o tempo de utilização do pico, o trabalho verifica o número atual de sessões e a capacidade VM do anfitrião atual da sessão de funcionamento para cada piscina hospedeira. Utiliza estas informações para calcular se os VMs do anfitrião da sessão de funcionamento podem suportar as sessões existentes com base no parâmetro *SessionThresholdPerCPU* definido para o ficheiro **CreateOrUpdateAzLogicApp.ps1.** Se os VMs do anfitrião da sessão não puderem suportar as sessões existentes, o trabalho inicia vMs adicionais de anfitrião na piscina de anfitriões.

>[!NOTE]
>*SessionThresholdPerCPU* não restringe o número de sessões no VM. Este parâmetro só determina quando é necessário iniciar novos VMs para equilibrar as ligações. Para restringir o número de sessões, tem de seguir as instruções [Update-AzWvdHostPool](configure-host-pool-load-balancing.md#configure-breadth-first-load-balancing) para configurar o parâmetro *MaxSessionLimit* em conformidade.

Durante o tempo de utilização fora do pico, o trabalho determina quantas VMs de anfitrião de sessão devem ser desligados com base no parâmetro *Número Mínimo DeRDSH.* Se definir o parâmetro *LimitSecondsToForceLogOffUser* para um valor positivo não zero, o trabalho definirá os VMs do anfitrião da sessão para drenar o modo para evitar que novas sessões se conectem aos anfitriões. O trabalho notificará então qualquer pessoa atualmente assinada nos utilizadores para salvar o seu trabalho, aguardar o tempo configurado e, em seguida, forçar os utilizadores a assinar. Uma vez que todas as sessões de utilizador no VM do anfitrião da sessão tenham sido assinadas, o trabalho irá encerrar o VM. Depois de o VM desligar, o trabalho irá reiniciar o modo de drenagem do anfitrião da sessão.

>[!NOTE]
>Se definir manualmente o VM do anfitrião da sessão para o modo de drenagem, o trabalho não gerirá o VM do anfitrião da sessão. Se o VM do anfitrião da sessão estiver em funcionamento e definido para o modo de drenagem, será tratado como indisponível, o que fará com que o trabalho inicie VMs adicionais para lidar com a carga. Recomendamos que marque quaisquer VMs Azure antes de os definir manualmente para o modo de drenagem. Pode nomear a etiqueta com o parâmetro *ManutençãoTagName* quando criar o Azure Logic App Scheduler mais tarde. As etiquetas ajudar-lhe-ão a distinguir estes VMs daqueles que a ferramenta de escalagem gere. A definição da etiqueta de manutenção também impede que a ferramenta de escalação faça alterações no VM até remover a etiqueta.

Se definir o parâmetro *LimitSecondsToForceLogOffUser* para zero, o trabalho permite que a definição de configuração da sessão em políticas de grupo especificadas lide com a assinatura de sessões de utilizador. Para ver estas políticas de grupo, aceda a políticas **de configuração de computador**  >    >  **Modelos administrativos** Windows  >  **Components Remote** Desktop Services Remote  >  **Desktop** Session  >  **Host**  >  **Time Limits**. Se houver sessões ativas num VM anfitrião de sessão, o trabalho deixará o VM anfitrião da sessão em funcionamento. Se não houver sessões ativas, o trabalho encerrará o VM anfitrião da sessão.

Durante qualquer tempo, o trabalho também leva em conta o *MaxSessionLimit* do anfitrião para determinar se o número atual de sessões é mais de 90% da capacidade máxima. Se for, o trabalho iniciará vMs de anfitrião adicional.

O trabalho funciona periodicamente com base num intervalo de recorrência definido. Pode alterar este intervalo com base no tamanho do ambiente de ambiente de trabalho virtual do Windows, mas lembre-se que iniciar e desligar VMs pode demorar algum tempo, por isso lembre-se de ter em conta o atraso. Recomendamos que se ajuste o intervalo de recorrência a cada 15 minutos.

No entanto, a ferramenta também tem as seguintes limitações:

- Esta solução aplica-se apenas aos VM do anfitrião de múltiplas sessões em conjunto.
- Esta solução gere VMs em qualquer região, mas só pode ser usada na mesma subscrição que a sua conta Azure Automation e Azure Logic App.
- O tempo máximo de funcionamento de um trabalho no livro de corridas é de 3 horas. Se começar ou parar os VMs na piscina de anfitrião demorar mais tempo do que isso, o trabalho falhará. Para mais detalhes, consulte [recursos partilhados.](../automation/automation-runbook-execution.md#fair-share)
- Pelo menos um VM ou anfitrião de sessão precisa de ser ligado para que o algoritmo de escala funcione corretamente.
- A ferramenta de escala não suporta o escalonamento com base no CPU ou na memória.
- A escala só funciona com os anfitriões existentes na piscina de anfitriões. A ferramenta de escala não suporta a escala de novos anfitriões de sessão.

>[!NOTE]
>A ferramenta de escala controla o modo de equilíbrio de carga da piscina hospedeira que está atualmente a escalonar. A ferramenta utiliza o modo de equilíbrio de primeira carga para horas de pico e de pico.

## <a name="prerequisites"></a>Pré-requisitos

Antes de começar a configurar a ferramenta de escala, certifique-se de que tem as seguintes coisas prontas:

- Uma [piscina de anfitriões virtual do Windows Desktop](create-host-pools-azure-marketplace.md)
- VMs de piscina de anfitriões de sessão configurados e registados com o serviço de desktop virtual do Windows
- Um utilizador com [acesso do Contribuinte](../role-based-access-control/role-assignments-portal.md) à subscrição do Azure

A máquina que utiliza para implantar a ferramenta deve ter:

- Windows PowerShell 5.1 ou mais tarde
- O módulo Microsoft Az PowerShell

Se tem tudo pronto, vamos começar.

## <a name="create-or-update-an-azure-automation-account"></a>Criar ou atualizar uma conta Azure Automation

>[!NOTE]
>Se já tem uma conta Azure Automation com um runbook a executar uma versão mais antiga do script de escala, tudo o que precisa de fazer é seguir as instruções abaixo para se certificar de que está atualizado.

Primeiro, vai precisar de uma conta Azure Automation para executar o livro de execução PowerShell. O processo que esta secção descreve é válido mesmo que tenha uma conta Azure Automation existente que pretende utilizar para configurar o livro de execução PowerShell. Veja aqui como configurá-lo:

1. Abra o Windows PowerShell.

2. Execute o cmdlet seguinte para iniciar súm na sua conta Azure.

    ```powershell
    Login-AzAccount
    ```

    >[!NOTE]
    >A sua conta deve ter direitos de contribuinte sobre a subscrição do Azure onde pretende implementar a ferramenta de escala.

3. Execute o seguinte cmdlet para descarregar o script para criar a conta Azure Automation:

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzAutoAccount.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzAutoAccount.ps1"
    ```

4. Execute o cmdlet seguinte para executar o script e criar a conta Azure Automation. Pode preencher valores para os parâmetros ou comentá-los para utilizar os seus predefinidos.

    ```powershell
    $Params = @{
         "AADTenantId"           = "<Azure_Active_Directory_tenant_ID>"   # Optional. If not specified, it will use the current Azure context
         "SubscriptionId"        = "<Azure_subscription_ID>"              # Optional. If not specified, it will use the current Azure context
         "UseARMAPI"             = $true
         "ResourceGroupName"     = "<Resource_group_name>"                # Optional. Default: "WVDAutoScaleResourceGroup"
         "AutomationAccountName" = "<Automation_account_name>"            # Optional. Default: "WVDAutoScaleAutomationAccount"
         "Location"              = "<Azure_region_for_deployment>"
         "WorkspaceName"         = "<Log_analytics_workspace_name>"       # Optional. If specified, Log Analytics will be used to configure the custom log table that the runbook PowerShell script can send logs to
    }

    .\CreateOrUpdateAzAutoAccount.ps1 @Params
    ```

5. A saída do cmdlet incluirá um webhook URI. Certifique-se de manter um registo do URI porque irá usá-lo como parâmetro quando configurar o calendário de execução para a App Azure Logic.

6. Se especificou o parâmetro **WorkspaceName** para Log Analytics, a saída do cmdlet também incluirá o ID do Espaço de Trabalho do Log Analytics e a sua Chave Primária. Certifique-se de que se lembra do URI porque terá de o voltar a usar mais tarde como parâmetro quando configurar o calendário de execução para a App Azure Logic.

7. Depois de configurar a sua conta Azure Automation, inscreva-se na sua subscrição Azure e verifique se a sua conta Azure Automation e o livro de verificação relevante apareceram no seu grupo de recursos especificado, como mostra a seguinte imagem:

    >[!div class="mx-imgBorder"]
    >![Uma imagem da página geral do Azure mostrando a conta e o runbook da Azure Automation recém-criado.](media/automation-account.png)

    Para verificar se o seu webhook está onde deveria estar, selecione o nome do seu runbook. Em seguida, vá à secção de Recursos do seu runbook e selecione **Webhooks**.

## <a name="create-an-azure-automation-run-as-account"></a>Criar uma execução de automação Azure Como conta

Agora que tem uma conta Azure Automation, também terá de criar uma conta Azure Automation Como conta se ainda não tiver uma. Esta conta permitirá que a ferramenta aceda aos seus recursos Azure.

Uma [Azure Automation Run Como conta](../automation/manage-runas-account.md) fornece autenticação para gestão de recursos em Azure com cmdlets Azure. Quando cria uma conta Run As, cria um novo utilizador principal de serviço no Azure Ative Directory e atribui a função contribuinte ao utilizador principal do serviço ao nível da subscrição. Uma conta Azure Run As é uma ótima maneira de autenticar de forma segura com certificados e um nome principal de serviço sem precisar de armazenar um nome de utilizador e senha num objeto credencial. Para saber mais sobre a autenticação da conta Run As, consulte [Limite executar como permissões de conta](../automation/manage-runas-account.md#limit-run-as-account-permissions).

Qualquer utilizador que seja membro da função de Administrador de Subscrição e coadministrator da subscrição pode criar uma conta Run As.

Para criar uma conta Run As na sua conta Azure Automation:

1. No portal Azure, selecione **Todos os serviços**. Na lista de recursos, insira e selecione **contas Automation .**

2. Na página **de contas Automation,** selecione o nome da sua conta Azure Automation.

3. No painel do lado esquerdo da janela, selecione **Executar como contas** na secção **Definições de Conta.**

4. Selecione **Azure Run As account**. Quando o **Add Azure Run Como** o painel de conta aparece, reveja as informações gerais e, em seguida, selecione **Criar** para iniciar o processo de criação de conta.

5. Aguarde alguns minutos para que o Azure crie a conta Run As. Pode acompanhar o progresso da criação no menu em notificações.

6. Quando o processo terminar, criará um ativo chamado **AzureRunAsConnection** na conta Azure Automation especificada. Selecione **Azure Run As account**. O ativo de ligação detém o ID da aplicação, iD do inquilino, ID de assinatura e impressão digital de certificado. Também pode encontrar a mesma informação na página **'Ligações'.** Para ir a esta página, no painel do lado esquerdo da janela, selecione **Connections** sob a secção Recursos **Partilhados** e clique no ativo de ligação denominado **AzureRunAsConnection**.

## <a name="create-the-azure-logic-app-and-execution-schedule"></a>Crie a App Azure Logic e o calendário de execução

Por fim, terá de criar a App Azure Logic e definir um calendário de execução para a sua nova ferramenta de escala. Em primeiro lugar, descarregue e importe o [módulo PowerShell de Virtualização desktop](powershell-module.md) para utilizar na sessão PowerShell se ainda não o fez.

1. Abra o Windows PowerShell.

2. Execute o cmdlet seguinte para iniciar súm na sua conta Azure.

    ```powershell
    Login-AzAccount
    ```

3. Execute o cmdlet seguinte para descarregar o script para criar a App Azure Logic.

    ```powershell
    New-Item -ItemType Directory -Path "C:\Temp" -Force
    Set-Location -Path "C:\Temp"
    $Uri = "https://raw.githubusercontent.com/Azure/RDS-Templates/master/wvd-templates/wvd-scaling-script/CreateOrUpdateAzLogicApp.ps1"
    # Download the script
    Invoke-WebRequest -Uri $Uri -OutFile ".\CreateOrUpdateAzLogicApp.ps1"
    ```

4. Executa o seguinte script PowerShell para criar a App Azure Logic e programação de execução para a sua piscina anfitriã

    >[!NOTE]
    >Você precisará executar este script para cada piscina de anfitrião que você deseja fazer auto-escala, mas você precisa apenas de uma conta Azure Automation.

    ```powershell
    $AADTenantId = (Get-AzContext).Tenant.Id

    $AzSubscription = Get-AzSubscription | Out-GridView -OutputMode:Single -Title "Select your Azure Subscription"
    Select-AzSubscription -Subscription $AzSubscription.Id

    $ResourceGroup = Get-AzResourceGroup | Out-GridView -OutputMode:Single -Title "Select the resource group for the new Azure Logic App"

    $WVDHostPool = Get-AzResource -ResourceType "Microsoft.DesktopVirtualization/hostpools" | Out-GridView -OutputMode:Single -Title "Select the host pool you'd like to scale"

    $LogAnalyticsWorkspaceId = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Workspace ID returned by when you created the Azure Automation account, otherwise leave it blank"
    $LogAnalyticsPrimaryKey = Read-Host -Prompt "If you want to use Log Analytics, enter the Log Analytics Primary Key returned by when you created the Azure Automation account, otherwise leave it blank"
    $RecurrenceInterval = Read-Host -Prompt "Enter how often you'd like the job to run in minutes, e.g. '15'"
    $BeginPeakTime = Read-Host -Prompt "Enter the start time for peak hours in local time, e.g. 9:00"
    $EndPeakTime = Read-Host -Prompt "Enter the end time for peak hours in local time, e.g. 18:00"
    $TimeDifference = Read-Host -Prompt "Enter the time difference between local time and UTC in hours, e.g. +5:30"
    $SessionThresholdPerCPU = Read-Host -Prompt "Enter the maximum number of sessions per CPU that will be used as a threshold to determine when new session host VMs need to be started during peak hours"
    $MinimumNumberOfRDSH = Read-Host -Prompt "Enter the minimum number of session host VMs to keep running during off-peak hours"
    $MaintenanceTagName = Read-Host -Prompt "Enter the name of the Tag associated with VMs you don't want to be managed by this scaling tool"
    $LimitSecondsToForceLogOffUser = Read-Host -Prompt "Enter the number of seconds to wait before automatically signing out users. If set to 0, any session host VM that has user sessions, will be left untouched"
    $LogOffMessageTitle = Read-Host -Prompt "Enter the title of the message sent to the user before they are forced to sign out"
    $LogOffMessageBody = Read-Host -Prompt "Enter the body of the message sent to the user before they are forced to sign out"

    $AutoAccount = Get-AzAutomationAccount | Out-GridView -OutputMode:Single -Title "Select the Azure Automation account"
    $AutoAccountConnection = Get-AzAutomationConnection -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName | Out-GridView -OutputMode:Single -Title "Select the Azure RunAs connection asset"

    $WebhookURIAutoVar = Get-AzAutomationVariable -Name 'WebhookURIARMBased' -ResourceGroupName $AutoAccount.ResourceGroupName -AutomationAccountName $AutoAccount.AutomationAccountName

    $Params = @{
         "AADTenantId"                   = $AADTenantId                             # Optional. If not specified, it will use the current Azure context
         "SubscriptionID"                = $AzSubscription.Id                       # Optional. If not specified, it will use the current Azure context
         "ResourceGroupName"             = $ResourceGroup.ResourceGroupName         # Optional. Default: "WVDAutoScaleResourceGroup"
         "Location"                      = $ResourceGroup.Location                  # Optional. Default: "West US2"
         "UseARMAPI"                     = $true
         "HostPoolName"                  = $WVDHostPool.Name
         "HostPoolResourceGroupName"     = $WVDHostPool.ResourceGroupName           # Optional. Default: same as ResourceGroupName param value
         "LogAnalyticsWorkspaceId"       = $LogAnalyticsWorkspaceId                 # Optional. If not specified, script will not log to the Log Analytics
         "LogAnalyticsPrimaryKey"        = $LogAnalyticsPrimaryKey                  # Optional. If not specified, script will not log to the Log Analytics
         "ConnectionAssetName"           = $AutoAccountConnection.Name              # Optional. Default: "AzureRunAsConnection"
         "RecurrenceInterval"            = $RecurrenceInterval                      # Optional. Default: 15
         "BeginPeakTime"                 = $BeginPeakTime                           # Optional. Default: "09:00"
         "EndPeakTime"                   = $EndPeakTime                             # Optional. Default: "17:00"
         "TimeDifference"                = $TimeDifference                          # Optional. Default: "-7:00"
         "SessionThresholdPerCPU"        = $SessionThresholdPerCPU                  # Optional. Default: 1
         "MinimumNumberOfRDSH"           = $MinimumNumberOfRDSH                     # Optional. Default: 1
         "MaintenanceTagName"            = $MaintenanceTagName                      # Optional.
         "LimitSecondsToForceLogOffUser" = $LimitSecondsToForceLogOffUser           # Optional. Default: 1
         "LogOffMessageTitle"            = $LogOffMessageTitle                      # Optional. Default: "Machine is about to shutdown."
         "LogOffMessageBody"             = $LogOffMessageBody                       # Optional. Default: "Your session will be logged off. Please save and close everything."
         "WebhookURI"                    = $WebhookURIAutoVar.Value
    }

    .\CreateOrUpdateAzLogicApp.ps1 @Params
    ```

    Depois de executar o script, a App Azure Logic deve aparecer num grupo de recursos, como mostra a imagem seguinte.

    >[!div class="mx-imgBorder"]
    >![Uma imagem da página de visão geral para um exemplo Azure Logic App.](media/logic-app.png)

    Para escoar alterações no calendário de execução, como alterar o intervalo de recorrência ou fuso horário, vá ao programador de escala automática da Azure Logic App e selecione **Edit** para ir ao Azure Logic App Designer.

    >[!div class="mx-imgBorder"]
    >![Uma imagem do Azure Logic App Designer. Os menus Recurrence e Webhook que permitem ao utilizador editar tempos de recorrência e o ficheiro webhook estão abertos.](media/logic-apps-designer.png)

## <a name="manage-your-scaling-tool"></a>Gerencie a sua ferramenta de escala

Agora que criou a sua ferramenta de escala, pode aceder à sua saída. Esta secção descreve algumas funcionalidades que poderá achar úteis.

### <a name="view-job-status"></a>Ver o estado de tarefa

Pode ver um estado resumido de todos os trabalhos de runbook ou ver um estado mais aprofundado de um trabalho específico no portal Azure.

À direita da sua conta de Automação Azure selecionada, em "Estatísticas de Emprego", pode ver uma lista de resumos de todos os trabalhos de runbook. A abertura da página **Jobs** no lado esquerdo da janela mostra o estado atual do trabalho, os horários de início e os tempos de conclusão.

>[!div class="mx-imgBorder"]
>![Uma imagem da página do estado do trabalho.](media/jobs-status.png)

### <a name="view-logs-and-scaling-tool-output"></a>Ver registos e saída de ferramenta de escala

Pode visualizar os registos de operações de escala e dimensionamento, abrindo o seu runbook e selecionando o trabalho.

Navegue para o runbook no seu grupo de recursos que hospeda a conta Azure Automation e selecione **Overview**. Na página geral, selecione um trabalho em **Empregos Recentes** para ver a sua saída de ferramenta de escala, como mostra a imagem seguinte.

>[!div class="mx-imgBorder"]
>![Uma imagem da janela de saída para a ferramenta de escala.](media/tool-output.png)

### <a name="check-the-runbook-script-version-number"></a>Verifique o número da versão do script do livro de execução

Pode verificar qual a versão do script do livro de execuções que está a utilizar abrindo o ficheiro runbook na sua conta Azure Automation e selecionando **Ver**. Aparecerá um guião para o livro de recortes no lado direito do ecrã. No script, verá o número da versão no formato `v#.#.#` sob a `SYNOPSIS` secção. Pode encontrar o número da versão mais recente [aqui.](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/wvd-scaling-script/ARM_based/basicScale.ps1#L1) Se não vir um número de versão no seu script de runbook, isso significa que está a executar uma versão anterior do script e deve atualizá-lo imediatamente. Se necessitar de atualizar o seu script de runbook, siga as instruções na [Criar ou atualize uma conta Azure Automation](#create-or-update-an-azure-automation-account).

### <a name="reporting-issues"></a>Questões de reporte

Quando reportar um problema, terá de fornecer as seguintes informações para nos ajudar a resolver problemas:

- Um registo completo do **separador Todos os Registos** no trabalho que causou o problema. Para aprender a obter o registo, siga as instruções em [Ver registos e saída de ferramenta de escala](#view-logs-and-scaling-tool-output). Se houver alguma informação sensível ou privada no registo, pode removê-la antes de nos submeter o problema.

- A versão do script do livro de execuções que está a usar. Para saber como obter o número da versão, consulte [o número da versão do script do runbook](#check-the-runbook-script-version-number)

- O número de versão de cada um dos seguintes módulos PowerShell instalados na sua conta Azure Automation. Para encontrar estes módulos, abra a conta Azure Automation, selecione **Módulos** sob a secção **Recursos Partilhados** no painel do lado esquerdo da janela e, em seguida, procure o nome do módulo.
    - Az.Accounts
    - Az.Compute
    - Az.Resources
    - Az.Automation
    - OmSIngestionAPI
    - Az.DesktopVirtualization

- A data de validade da sua [conta Run As](#create-an-azure-automation-run-as-account). Para encontrar isto, abra a sua conta Azure Automation e, em seguida, selecione **Executar como contas** em **Definições** de Conta no painel do lado esquerdo da janela. A data de validade deve estar na **conta Azure Run As**.

### <a name="log-analytics"></a>Log Analytics

Se decidiu utilizar o Log Analytics, pode visualizar todos os dados de registo num registo personalizado denominado **WVDTenantScale_CL** em **Registos Personalizados** na vista de **Logs** do seu Espaço de Trabalho de Log Analytics. Listamos algumas consultas de amostra que pode achar útil.

- Para ver todos os registos de uma piscina de anfitriões, insira a seguinte consulta

    ```Kusto
    WVDTenantScale_CL
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Para ver o número total de VMs anfitriões de sessão atualmente em execução e sessões de utilizador ativas na sua piscina de anfitrião, insira a seguinte consulta

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Number of running session hosts:"
         or logmessage_s contains "Number of user sessions:"
         or logmessage_s contains "Number of user sessions per Core:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Para ver o estado de todos os VMs anfitriões de sessão numa piscina de anfitriões, insira a seguinte consulta

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "Session host:"
    | where hostpoolName_s == "<host_pool_name>"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

- Para ver quaisquer erros e avisos, insira a seguinte consulta

    ```Kusto
    WVDTenantScale_CL
    | where logmessage_s contains "ERROR:" or logmessage_s contains "WARN:"
    | project TimeStampUTC = TimeGenerated, TimeStampLocal = TimeStamp_s, HostPool = hostpoolName_s, LineNumAndMessage = logmessage_s, AADTenantId = TenantId
    ```

## <a name="report-issues"></a>Comunicar problemas

Os relatórios de emissão da ferramenta de escala estão atualmente a ser tratados pelo Microsoft Support. Quando escruisse um relatório de emissão, certifique-se de seguir as instruções em [questões de reporte](#reporting-issues). Se tiver feedback sobre a ferramenta ou quiser solicitar novas funcionalidades, abra uma emissão do GitHub com o rótulo "ferramenta de escala de 4 WVD" na [página RDS GitHub](https://github.com/Azure/RDS-Templates/issues?q=is%3Aissue+is%3Aopen+label%3A4-WVD-scaling-tool).
