---
title: Atualização automática do serviço de Mobilidade na Recuperação do Sítio Azure
description: Visão geral da atualização automática do serviço mobility ao replicar VMs Azure utilizando a Recuperação do Local de Azure.
services: site-recovery
author: sideeksh
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 04/02/2020
ms.author: sideeksh
ms.openlocfilehash: 42d3c74229ab7eeec0ac716073a9e631775fd002
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/26/2020
ms.locfileid: "96187345"
---
# <a name="automatic-update-of-the-mobility-service-in-azure-to-azure-replication"></a>Atualização automática do serviço de mobilidade na replicação Azure-to-Azure

A Azure Site Recovery utiliza uma cadência de lançamento mensal para corrigir quaisquer problemas e melhorar as funcionalidades existentes ou adicionar novas. Para se manter atual com o serviço, tem de planear a implantação de remendos todos os meses. Para evitar a sobrecarga associada a cada atualização, pode permitir a Recuperação do Site para gerir atualizações de componentes.

Como mencionado na [arquitetura de recuperação de desastres Azure-to-Azure,](azure-to-azure-architecture.md)o serviço de Mobilidade é instalado em todas as máquinas virtuais Azure (VMs) que têm replicação ativada de uma região de Azure para outra. Quando utiliza atualizações automáticas, cada novo lançamento atualiza a extensão do serviço Mobility.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="how-automatic-updates-work"></a>Como funcionam as atualizações automáticas

Quando utiliza a Recuperação do Site para gerir atualizações, implementa um runbook global (utilizado pelos serviços Azure) através de uma conta de automação, criada na mesma subscrição que o cofre. Cada cofre usa uma conta de automação. Para cada VM num cofre, o livro verifica as atualizações automáticas ativas. Se estiver disponível uma versão mais recente da extensão do serviço Mobility, a atualização será instalada.

O horário padrão ocorre diariamente às 00:00 no fuso horário da geografia do VM replicado. Também pode alterar o horário do livro através da conta de automação.

> [!NOTE]
> A partir do [Update Rollup 35,](site-recovery-whats-new.md#updates-march-2019)pode escolher uma conta de automatização existente para utilizar para atualizações. Antes de atualizar o Rollup 35, a Recuperação do Site criou a conta de automação por padrão. Só pode selecionar esta opção quando ativar a replicação de um VM. Não está disponível para um VM que já tenha replicação ativada. A definição selecionada aplica-se a todos os VMs Azure protegidos no mesmo cofre.

Ligar atualizações automáticas não requer um reinício dos seus VMs Azure ou afetar a replicação em curso.

A faturação de emprego na conta de automação baseia-se no número de minutos de trabalho utilizados num mês. A execução de emprego leva alguns segundos a cerca de um minuto por dia e é coberta como unidades livres. Por padrão, 500 minutos são incluídos como unidades gratuitas para uma conta de automação, como mostra o seguinte quadro:

| Unidades gratuitas incluídas (todos os meses) | Preço |
|---|---|
| Tempo de execução de trabalho 500 minutos | ₹0.14/minuto

## <a name="enable-automatic-updates"></a>Ativar atualizações automáticas

Existem várias formas de a Recuperação do Site gerir as atualizações de extensão:

- [Gerir como parte do passo de replicação de ativação](#manage-as-part-of-the-enable-replication-step)
- [Alternar as definições de atualização de extensão dentro do cofre](#toggle-the-extension-update-settings-inside-the-vault)
- [Gerir atualizações manualmente](#manage-updates-manually)

### <a name="manage-as-part-of-the-enable-replication-step"></a>Gerir como parte do passo de replicação de ativação

Quando ativa a replicação de um VM a [partir da vista VM](azure-to-azure-quickstart.md) ou [do cofre dos serviços de recuperação,](azure-to-azure-how-to-enable-replication.md)pode permitir que a Recuperação do Local gere atualizações para a extensão de Recuperação do Site ou a gere manualmente.

:::image type="content" source="./media/azure-to-azure-autoupdate/enable-rep.png" alt-text="Definições de extensão":::

### <a name="toggle-the-extension-update-settings-inside-the-vault"></a>Alternar as definições de atualização de extensão dentro do cofre

1. A partir do cofre dos Serviços de Recuperação, vá para **a**  >  **Infraestrutura de Recuperação do Local.**
1. Em **Baixo para Azure Configurações de** atualização de extensão de máquinas virtuais permitem a  >  **Extension Update Settings**  >  **recuperação do site para gerir**, selecione **On**.

   Para gerir a extensão manualmente, selecione **Off**.

1. Selecione **Guardar**.

:::image type="content" source="./media/azure-to-azure-autoupdate/vault-toggle.png" alt-text="Definições de atualização de extensão":::

> [!IMPORTANT]
> Ao escolher **Permitir a recuperação do local para gerir,** a definição é aplicada a todos os VMs no cofre.

> [!NOTE]
> Qualquer uma das opções notifica-o da conta de automatização utilizada para gerir atualizações. Se estiver a utilizar esta funcionalidade num cofre pela primeira vez, uma nova conta de automação é criada por padrão. Alternadamente, pode personalizar a configuração e escolher uma conta de automação existente. Uma vez definidas, todas as ações subsequentes para permitir a replicação no mesmo cofre utilizarão essa conta de automatização selecionada. Atualmente, o menu suspenso apenas listará contas de automação que estão no mesmo Grupo de Recursos que o cofre.

> [!IMPORTANT]
> O seguinte script tem de ser executado no contexto de uma conta de automação.
Para uma conta de automação personalizada, utilize o seguinte script:

```azurepowershell
param(
    [Parameter(Mandatory=$true)]
    [String] $VaultResourceId,
    [Parameter(Mandatory=$true)]
    [ValidateSet("Enabled",'Disabled')]
    [Alias("Enabled or Disabled")]
    [String] $AutoUpdateAction,
    [Parameter(Mandatory=$false)]
    [String] $AutomationAccountArmId
)
$SiteRecoveryRunbookName = "Modify-AutoUpdateForVaultForPatner"
$TaskId = [guid]::NewGuid().ToString()
$SubscriptionId = "00000000-0000-0000-0000-000000000000"
$AsrApiVersion = "2018-01-10"
$RunAsConnectionName = "AzureRunAsConnection"
$ArmEndPoint = "https://management.azure.com"
$AadAuthority = "https://login.windows.net/"
$AadAudience = "https://management.core.windows.net/"
$AzureEnvironment = "AzureCloud"
$Timeout = "160"
function Throw-TerminatingErrorMessage
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    throw ("Message: {0}, TaskId: {1}.") -f $Message, $TaskId
}
function Write-Tracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [ValidateSet("Informational", "Warning", "ErrorLevel", "Succeeded", IgnoreCase = $true)]
                [String]
        $Level,
        [Parameter(Mandatory=$true)]
        [String]
        $Message,
            [Switch]
        $DisplayMessageToUser
        )
    Write-Output $Message
}
function Write-InformationTracing
{
        Param
    (
        [Parameter(Mandatory=$true)]
        [String]
        $Message
        )
    Write-Tracing -Message $Message -Level Informational -DisplayMessageToUser
}
function ValidateInput()
{
    try
    {
        if(!$VaultResourceId.StartsWith("/subscriptions", [System.StringComparison]::OrdinalIgnoreCase))
        {
            $ErrorMessage = "The vault resource id should start with /subscriptions."
            throw $ErrorMessage
        }
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        if(!($Tokens.Count % 2 -eq 0))
        {
            $ErrorMessage = ("Odd Number of tokens: {0}." -f $Tokens.Count)
            throw $ErrorMessage
        }
        if(!($Tokens.Count/2 -eq 4))
        {
            $ErrorMessage = ("Invalid number of resource in vault ARM id expected:4, actual:{0}." -f ($Tokens.Count/2))
            throw $ErrorMessage
        }
        if($AutoUpdateAction -ieq "Enabled" -and [string]::IsNullOrEmpty($AutomationAccountArmId))
        {
            $ErrorMessage = ("The automation account ARM id should not be null or empty when AutoUpdateAction is enabled.")
            throw $ErrorMessage
        }
    }
    catch
    {
        $ErrorMessage = ("ValidateInput failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Initialize-SubscriptionId()
{
    try
    {
        $Tokens = $VaultResourceId.SubString(1).Split("/")
        $Count = 0
                $ArmResources = @{}
        while($Count -lt $Tokens.Count)
        {
            $ArmResources[$Tokens[$Count]] = $Tokens[$Count+1]
            $Count = $Count + 2
        }
                return $ArmResources["subscriptions"]
    }
    catch
    {
        Write-Tracing -Level ErrorLevel -Message ("Initialize-SubscriptionId: failed with [Exception: {0}]." -f $_.Exception) -DisplayMessageToUser
        throw
    }
}
function Invoke-InternalRestMethod($Uri, $Headers, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-RestMethod -Uri $Uri -Headers $Headers
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Invoke-InternalWebRequest($Uri, $Headers, $Method, $Body, $ContentType, [ref]$Result)
{
    $RetryCount = 0
    $MaxRetry = 3
    do
    {
        try
        {
            $ResultObject = Invoke-WebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -UseBasicParsing
            ($Result.Value) += ($ResultObject)
            break
        }
        catch
        {
            Write-InformationTracing ("Retry Count: {0}, Exception: {1}." -f $RetryCount, $_.Exception)
            $RetryCount++
            if(!($RetryCount -le $MaxRetry))
            {
                throw
            }
            Start-Sleep -Milliseconds 2000
        }
    }while($true)
}
function Get-Header([ref]$Header, $AadAudience, $AadAuthority, $RunAsConnectionName){
    try
    {
        $RunAsConnection = Get-AutomationConnection -Name $RunAsConnectionName
        $TenantId = $RunAsConnection.TenantId
        $ApplicationId = $RunAsConnection.ApplicationId
        $CertificateThumbprint = $RunAsConnection.CertificateThumbprint
        $Path = "cert:\CurrentUser\My\{0}" -f $CertificateThumbprint
        $Secret = Get-ChildItem -Path $Path
        $ClientCredential = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.ClientAssertionCertificate(
                $ApplicationId,
                $Secret)
        # Trim the forward slash from the AadAuthority if it exist.
        $AadAuthority = $AadAuthority.TrimEnd("/")
        $AuthContext = New-Object Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext(
            "{0}/{1}" -f $AadAuthority, $TenantId )
        $AuthenticationResult = $authContext.AcquireToken($AadAudience, $Clientcredential)
        $Header.Value['Content-Type'] = 'application\json'
        $Header.Value['Authorization'] = $AuthenticationResult.CreateAuthorizationHeader()
        $Header.Value["x-ms-client-request-id"] = $TaskId + "/" + (New-Guid).ToString() + "-" + (Get-Date).ToString("u")
    }
    catch
    {
        $ErrorMessage = ("Get-BearerToken: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
function Get-ProtectionContainerToBeModified([ref] $ContainerMappingList)
{
    try
    {
        Write-InformationTracing ("Get protection container mappings : {0}." -f $VaultResourceId)
        $ContainerMappingListUrl = $ArmEndPoint + $VaultResourceId + "/replicationProtectionContainerMappings" + "?api-version=" + $AsrApiVersion
        Write-InformationTracing ("Getting the bearer token and the header.")
        Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
        $Result = @()
        Invoke-InternalRestMethod -Uri $ContainerMappingListUrl -Headers $header -Result ([ref]$Result)
        $ContainerMappings = $Result[0]
        Write-InformationTracing ("Total retrieved container mappings: {0}." -f $ContainerMappings.Value.Count)
        foreach($Mapping in $ContainerMappings.Value)
        {
            if(($Mapping.properties.providerSpecificDetails -eq $null) -or ($Mapping.properties.providerSpecificDetails.instanceType -ine "A2A"))
            {
                Write-InformationTracing ("Mapping properties: {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the provider does not match." -f ($Mapping.Id))
                continue;
            }
            if($Mapping.Properties.State -ine "Paired")
            {
                Write-InformationTracing ("Ignoring container mapping: {0} as the state is not paired." -f ($Mapping.Id))
                continue;
            }
            Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties.providerSpecificDetails))
            $MappingAutoUpdateStatus = $Mapping.properties.providerSpecificDetails.agentAutoUpdateStatus
            $MappingAutomationAccountArmId = $Mapping.properties.providerSpecificDetails.automationAccountArmId
            $MappingHealthErrorCount = $Mapping.properties.HealthErrorDetails.Count
            if($AutoUpdateAction -ieq "Enabled" -and
                ($MappingAutoUpdateStatus -ieq "Enabled") -and
                ($MappingAutomationAccountArmId -ieq $AutomationAccountArmId) -and
                ($MappingHealthErrorCount -eq 0))
            {
                Write-InformationTracing ("Provider specific details {0}." -f ($Mapping.properties))
                Write-InformationTracing ("Ignoring container mapping: {0} as the auto update is already enabled and is healthy." -f ($Mapping.Id))
                continue;
            }
            ($ContainerMappingList.Value).Add($Mapping.id)
        }
    }
    catch
    {
        $ErrorMessage = ("Get-ProtectionContainerToBeModified: failed with [Exception: {0}]." -f $_.Exception)
        Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
        Throw-TerminatingErrorMessage -Message $ErrorMessage
    }
}
$OperationStartTime = Get-Date
$ContainerMappingList = New-Object System.Collections.Generic.List[System.String]
$JobsInProgressList = @()
$JobsCompletedSuccessList = @()
$JobsCompletedFailedList = @()
$JobsFailedToStart = 0
$JobsTimedOut = 0
$Header = @{}
$AzureRMProfile = Get-Module -ListAvailable -Name AzureRM.Profile | Select Name, Version, Path
$AzureRmProfileModulePath = Split-Path -Parent $AzureRMProfile.Path
Add-Type -Path (Join-Path $AzureRmProfileModulePath "Microsoft.IdentityModel.Clients.ActiveDirectory.dll")
$Inputs = ("Tracing inputs VaultResourceId: {0}, Timeout: {1}, AutoUpdateAction: {2}, AutomationAccountArmId: {3}." -f $VaultResourceId, $Timeout, $AutoUpdateAction, $AutomationAccountArmId)
Write-Tracing -Message $Inputs -Level Informational -DisplayMessageToUser
$CloudConfig = ("Tracing cloud configuration ArmEndPoint: {0}, AadAuthority: {1}, AadAudience: {2}." -f $ArmEndPoint, $AadAuthority, $AadAudience)
Write-Tracing -Message $CloudConfig -Level Informational -DisplayMessageToUser
$AutomationConfig = ("Tracing automation configuration RunAsConnectionName: {0}." -f $RunAsConnectionName)
Write-Tracing -Message $AutomationConfig -Level Informational -DisplayMessageToUser
ValidateInput
$SubscriptionId = Initialize-SubscriptionId
Get-ProtectionContainerToBeModified ([ref]$ContainerMappingList)
$Input = @{
  "properties"= @{
    "providerSpecificInput"= @{
        "instanceType" = "A2A"
        "agentAutoUpdateStatus" = $AutoUpdateAction
        "automationAccountArmId" = $AutomationAccountArmId
    }
  }
}
$InputJson = $Input |  ConvertTo-Json
if ($ContainerMappingList.Count -eq 0)
{
    Write-Tracing -Level Succeeded -Message ("Exiting as there are no container mappings to be modified.") -DisplayMessageToUser
    exit
}
Write-InformationTracing ("Container mappings to be updated has been retrieved with count: {0}." -f $ContainerMappingList.Count)
try
{
    Write-InformationTracing ("Start the modify container mapping jobs.")
    ForEach($Mapping in $ContainerMappingList)
    {
    try {
            $UpdateUrl = $ArmEndPoint + $Mapping + "?api-version=" + $AsrApiVersion
            Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
            $Result = @()
            Invoke-InternalWebRequest -Uri $UpdateUrl -Headers $Header -Method 'PATCH' `
                -Body $InputJson  -ContentType "application/json" -Result ([ref]$Result)
            $Result = $Result[0]
            $JobAsyncUrl = $Result.Headers['Azure-AsyncOperation']
            Write-InformationTracing ("The modify container mapping job invoked with async url: {0}." -f $JobAsyncUrl)
            $JobsInProgressList += $JobAsyncUrl;
            # Rate controlling the set calls to maximum 60 calls per minute.
            # ASR throttling for set calls is 200 in 1 minute.
            Start-Sleep -Milliseconds 1000
        }
        catch{
            Write-InformationTracing ("The modify container mappings job creation failed for: {0}." -f $Ru)
            Write-InformationTracing $_
            $JobsFailedToStart++
        }
    }
    Write-InformationTracing ("Total modify container mappings has been initiated: {0}." -f $JobsInProgressList.Count)
}
catch
{
    $ErrorMessage = ("Modify container mapping jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
try
{
    while($JobsInProgressList.Count -ne 0)
    {
        Sleep -Seconds 30
        $JobsInProgressListInternal = @()
        ForEach($JobAsyncUrl in $JobsInProgressList)
        {
            try
            {
                Get-Header ([ref]$Header) $AadAudience $AadAuthority $RunAsConnectionName
                $Result = Invoke-RestMethod -Uri $JobAsyncUrl -Headers $header
                $JobState = $Result.Status
                if($JobState -ieq "InProgress")
                {
                    $JobsInProgressListInternal += $JobAsyncUrl
                }
                elseif($JobState -ieq "Succeeded" -or `
                    $JobState -ieq "PartiallySucceeded" -or `
                    $JobState -ieq "CompletedWithInformation")
                {
                    Write-InformationTracing ("Jobs succeeded with state: {0}." -f $JobState)
                    $JobsCompletedSuccessList += $JobAsyncUrl
                }
                else
                {
                    Write-InformationTracing ("Jobs failed with state: {0}." -f $JobState)
                    $JobsCompletedFailedList += $JobAsyncUrl
                }
            }
            catch
            {
                Write-InformationTracing ("The get job failed with: {0}. Ignoring the exception and retrying the next job." -f $_.Exception)
                # The job on which the tracking failed, will be considered in progress and tried again later.
                $JobsInProgressListInternal += $JobAsyncUrl
            }
            # Rate controlling the get calls to maximum 120 calls each minute.
            # ASR throttling for get calls is 10000 in 60 minutes.
            Start-Sleep -Milliseconds 500
        }
        Write-InformationTracing ("Jobs remaining {0}." -f $JobsInProgressListInternal.Count)
        $CurrentTime = Get-Date
        if($CurrentTime -gt $OperationStartTime.AddMinutes($Timeout))
        {
            Write-InformationTracing ("Tracing modify cloud pairing jobs has timed out.")
            $JobsTimedOut = $JobsInProgressListInternal.Count
            $JobsInProgressListInternal = @()
        }
        $JobsInProgressList = $JobsInProgressListInternal
    }
}
catch
{
    $ErrorMessage = ("Tracking modify cloud pairing jobs failed with [Exception: {0}]." -f $_.Exception)
    Write-Tracing -Level ErrorLevel -Message $ErrorMessage  -DisplayMessageToUser
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-InformationTracing ("Tracking modify cloud pairing jobs completed.")
Write-InformationTracing ("Modify cloud pairing jobs success: {0}." -f $JobsCompletedSuccessList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed: {0}." -f $JobsCompletedFailedList.Count)
Write-InformationTracing ("Modify cloud pairing jobs failed to start: {0}." -f $JobsFailedToStart)
Write-InformationTracing ("Modify cloud pairing jobs timedout: {0}." -f $JobsTimedOut)
if($JobsTimedOut -gt  0)
{
    $ErrorMessage = "One or more modify cloud pairing jobs has timedout."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
elseif($JobsCompletedSuccessList.Count -ne $ContainerMappingList.Count)
{
    $ErrorMessage = "One or more modify cloud pairing jobs failed."
    Write-Tracing -Level ErrorLevel -Message ($ErrorMessage)
    Throw-TerminatingErrorMessage -Message $ErrorMessage
}
Write-Tracing -Level Succeeded -Message ("Modify cloud pairing completed.") -DisplayMessageToUser
```

### <a name="manage-updates-manually"></a>Gerir atualizações manualmente

1. Se houver novas atualizações para o serviço mobility instalado nos seus VMs, verá a seguinte notificação: A nova atualização do **agente de replicação do site está disponível. Clique para instalar.**

   :::image type="content" source="./media/vmware-azure-install-mobility-service/replicated-item-notif.png" alt-text="Janela de itens replicados":::

1. Selecione a notificação para abrir a página de seleção VM.
1. Escolha os VMs que pretende atualizar e, em seguida, selecione **OK**. O serviço de Mobilidade de Atualização começará para cada VM selecionado.

   :::image type="content" source="./media/vmware-azure-install-mobility-service/update-okpng.png" alt-text="Lista de VM de itens replicados":::

## <a name="common-issues-and-troubleshooting"></a>Questões comuns e resolução de problemas

Se houver algum problema com as atualizações automáticas, verá uma notificação de erro sob problemas de Configuração no painel de **instrumentos** do cofre.

Se não conseguir ativar atualizações automáticas, consulte os seguintes erros comuns e ações recomendadas:

- **Erro**: Não tem permissões para criar uma conta Azure Run As (principal serviço) e conceder o papel de Contribuinte ao titular do serviço.

  **Ação recomendada**: Certifique-se de que a conta de assinatura é atribuída como Contribuinte e tente novamente. Para obter mais informações sobre a atribuição de permissões, consulte a secção de permissões necessárias de [Como: Utilizar o portal para criar uma aplicação AD AD Azure que possa aceder aos recursos.](../active-directory/develop/howto-create-service-principal-portal.md#permissions-required-for-registering-an-app)

  Para corrigir a maioria dos problemas depois de ativar atualizações automáticas, selecione **Reparação**. Se o botão de reparação não estiver disponível, consulte a mensagem de erro exibida no painel de definições de atualização de extensão.

  :::image type="content" source="./media/azure-to-azure-autoupdate/repair.png" alt-text="Botão de reparação de serviço de recuperação de local nas definições de atualização de extensão":::

- **Erro**: A conta Run As não tem a permissão para aceder ao recurso de serviços de recuperação.

  **Ação recomendada**: Eliminar e, em seguida, [recriar a conta Run As](../automation/manage-runas-account.md). Ou, certifique-se de que a aplicação Azure Ative Directy da conta Azure Ative da conta pode aceder ao recurso dos serviços de recuperação.

- **Erro**: Executar Como a conta não é encontrada. Qualquer uma delas foi eliminada ou não foi criada - Aplicação de Diretório Ativo Azure, Diretor de Serviço, Papel, Certificado de Automação, Ativo de Ligação de Automação - ou a impressão digital não é idêntica entre Certificado e Conexão.

  **Ação recomendada**: Eliminar e, em seguida, [recriar a conta Run As](../automation/manage-runas-account.md).

- **Erro**: O Azure Run como Certificado utilizado pela conta de automação está prestes a expirar.

  O certificado auto-assinado que é criado para a conta Run As expira um ano a contar da data de criação. Pode renová-lo em qualquer altura antes de expirar. Se se inscreveu para notificações por e-mail, também receberá e-mails quando for necessária uma ação do seu lado. Este erro será mostrado dois meses antes da data de validade e será alterado para um erro crítico se o certificado tiver expirado. Uma vez expirado o certificado, a atualização automática não estará funcional até que renove o mesmo.

  **Ação recomendada**: Para resolver este problema, selecione **Reparar** e, em seguida, Renovar **o Certificado**.

  :::image type="content" source="./media/azure-to-azure-autoupdate/automation-account-renew-runas-certificate.PNG" alt-text="renovar-cert":::

  > [!NOTE]
  > Depois de renovar o certificado, refresque a página para mostrar o estado atual.
