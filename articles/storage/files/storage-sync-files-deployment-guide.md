---
title: Implementar Azure File Sync | Documentos da Microsoft
description: Saiba como implementar o Azure File Sync, do início ao fim.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: f871174982e965a32d5f2dca5e2e53c5dc436055
ms.sourcegitcommit: 94305d8ee91f217ec98039fde2ac4326761fea22
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57405492"
---
# <a name="deploy-azure-file-sync"></a>Implementar Azure File Sync
Utilize o Azure File Sync para centralizar as partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode usar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Pode ter o número de caches que precisar em todo o mundo.

Recomendamos vivamente que leia [planear uma implementação de ficheiros do Azure](storage-files-planning.md) e [planear uma implementação do Azure File Sync](storage-sync-files-planning.md) antes de concluir os passos descritos neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma conta de armazenamento do Azure e um arquivo do Azure partilham na mesma região que pretende implementar o Azure File Sync. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#region-availability) para o Azure File Sync.
    - [Criar uma conta de armazenamento](../common/storage-create-storage-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter uma descrição passo a passo de como criar uma conta de armazenamento.
    - [Criar uma partilha de ficheiros](storage-how-to-create-file-share.md) para obter uma descrição passo a passo de como criar uma partilha de ficheiros.
* Pelo menos uma instância suportada do Windows Server ou cluster do Windows Server para sincronizar com o Azure File Sync. Para obter mais informações sobre as versões suportadas do Windows Server, consulte [interoperabilidade com o Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Certifique-se de que o PowerShell 5.1 é instalado no seu servidor do Windows. Se estiver a utilizar o Windows Server 2012 R2, certifique-se de que está a executar, pelo menos, do PowerShell 5.1. \*. Pode ignorar esta verificação no Windows Server 2016 com segurança como PowerShell 5.1 é o padrão versão out-of-box. No Windows Server 2012 R2, pode verificar a execução do PowerShell 5.1. \* examinando o valor da **PSVersion** propriedade do **$PSVersionTable** objeto:

    ```PowerShell
    $PSVersionTable.PSVersion
    ```

    Se o valor de PSVersion for inferior a 5.1. \*, como será o caso com a maioria das instalações zero do Windows Server 2012 R2, pode facilmente atualizar ao transferir e instalar [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para transferir e instalar para o Windows Server 2012 R2 é **Win8.1AndW2K12R2 KB\*\*\*\*\*\*\*-x64.msu**.

    > [!Note]  
    > O Azure File Sync ainda não suporta o PowerShell 6 + no Windows Server 2012 R2 ou Windows Server 2016.
* O Az e os módulos AzureRM PowerShell.
    - O módulo de Az pode ser instalado ao seguir as instruções aqui: [Instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps). 
    - O módulo AzureRM PowerShell pode ser instalado, executando o seguinte cmdlet do PowerShell:
    
        ```PowerShell
        Install-Module AzureRM
        ```

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar o Windows Server para ser utilizado com o Azure File Sync
Para cada servidor que pretende utilizar com o Azure File Sync, incluindo a cada nó de servidor num Cluster de ativação pós-falha, desativar **a configuração de segurança avançada do Internet Explorer**. Isto é necessário apenas para o registo inicial do servidor. Pode reativá-la depois de o servidor estar registado.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
1. Abra o Gestor de servidor.
2. Clique em **servidor Local**:  
    !["Servidor local", no lado esquerdo da IU do Gestor de servidores](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. No subpainel **Propriedades**, selecione a ligação para **Configuração de Segurança Avançada do IE**.  
    ![O painel "Configuração avançada do IE segurança" na IU do Gestor de servidores](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Na **a configuração de segurança avançada do Internet Explorer** caixa de diálogo, selecione **desativar** para **administradores** e **utilizadores**:  
    ![A janela de pop da configuração de segurança avançada do Internet Explorer com "Off" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para desativar a configuração de segurança avançada do Internet Explorer, execute o seguinte a partir de uma sessão do PowerShell elevada:

```PowerShell
# Disable Internet Explorer Enhanced Security Configuration 
# for Administrators
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A7-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Disable Internet Explorer Enhanced Security Configuration 
# for Users
Set-ItemProperty -Path "HKLM:\SOFTWARE\Microsoft\Active Setup\Installed Components\{A509B1A8-37EF-4b3f-8CFC-4F3A74704073}" -Name "IsInstalled" -Value 0 -Force

# Force Internet Explorer closed, if open. This is required to fully apply the setting.
# Save any work you have open in the IE browser. This will not affect other browsers,
# including Microsoft Edge.
Stop-Process -Name iexplore -ErrorAction SilentlyContinue
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Implementar o Serviço de Sincronização de Armazenamento 
A implementação do Azure File Sync começa com a colocação de um **serviço de sincronização de armazenamento** recursos num grupo de recursos da sua subscrição selecionada. Recomendamos que o serviço de aprovisionamento, no mínimo um deles, conforme necessário. Irá criar uma relação de confiança entre os servidores e este recurso e um servidor só pode ser registrado para um serviço de sincronização de armazenamento. Como resultado, recomenda-se para implementar os serviços de sincronização de armazenamento que precisar para grupos de servidores separados. Tenha em atenção que os servidores a partir de serviços de sincronização de armazenamento diferente não é possível sincronizar entre si.

> [!Note]
> O serviço de sincronização de armazenamento herdadas as permissões de acesso da subscrição e grupo de recursos que tiver sido implementada no. Recomendamos que verifique quem tem acesso a ele. Entidades com acesso de escrita podem começar a sincronizar novos conjuntos de ficheiros a partir de servidores registados para este armazenamento serviço de sincronização e fazer com que dados a ser enviados para o armazenamento do Azure que seja acessível aos mesmos.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para implementar um serviço de sincronização de armazenamento, vá para o [portal do Azure](https://portal.azure.com/), clique em *criar um recurso* e, em seguida, procure a sincronização de ficheiros do Azure. Nos resultados da pesquisa, selecione **do Azure File Sync**e, em seguida, selecione **Create** para abrir o **implementar sincronização de armazenamento** separador.

No painel que se abre, introduza as informações seguintes:

- **Nome**: Um nome exclusivo (por subscrição) para o Serviço de Sincronização de Armazenamento.
- **Subscrição**: A subscrição na qual pretende criar o serviço de sincronização de armazenamento. Dependendo da estratégia de configuração da sua organização, poderá ter acesso a uma ou mais subscrições. Uma subscrição do Azure é o contentor mais básico para uma faturação para cada serviço em nuvem (por exemplo, ficheiros do Azure).
- **Grupo de recursos**: Um grupo de recursos é um grupo lógico de recursos do Azure, como uma conta de armazenamento ou um serviço de sincronização de armazenamento. Pode criar um novo grupo de recursos ou utilizar um grupo de recursos existente para o Azure File Sync. (Recomendamos que utilize grupos de recursos como contentores para isolar os recursos logicamente para a sua organização, como o agrupamento de recursos de RH ou recursos para um projeto específico.)
- **Localização**: A região na qual pretende implementar o Azure File Sync. Apenas as regiões suportadas estão disponíveis nesta lista.

Quando tiver terminado, selecione **criar** para implementar o serviço de sincronização de armazenamento.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Antes de interagir com os cmdlets de gestão do Azure File Sync, terá de importar uma DLL e criar um contexto de gestão do Azure File Sync. Isto é necessário porque os cmdlets de gestão do Azure File Sync ainda não fazem parte de módulos do Azure PowerShell.

> [!Note]  
> O pacote de StorageSync.Management.PowerShell.Cmdlets.dll, que contém os cmdlets de gestão do Azure File Sync, (intencionalmente) contém um cmdlet com um verbo não aprovado (`Login`). O nome `Login-AzureStorageSync` foi escolhido para corresponder a `Login-AzAccount` alias do cmdlet no módulo Azure PowerShell. Esta mensagem de erro (e o cmdlet) serão removidos quando o agente de sincronização de ficheiros do Azure é adicionado para o módulo Azure PowerShell.

```PowerShell
$acctInfo = Login-AzAccount

# The location of the Azure File Sync Agent. If you have installed the Azure File Sync 
# agent to a non-standard location, please update this path.
$agentPath = "C:\Program Files\Azure\StorageSyncAgent"

# Import the Azure File Sync management cmdlets
Import-Module "$agentPath\StorageSync.Management.PowerShell.Cmdlets.dll"

# this variable stores your subscription ID 
# get the subscription ID by logging onto the Azure portal
$subID = $acctInfo.Context.Subscription.Id

# this variable holds your Azure Active Directory tenant ID
# use Login-AzAccount to get the ID from that context
$tenantID = $acctInfo.Context.Tenant.Id

# this variable holds the Azure region you want to deploy 
# Azure File Sync into
$region = '<Az_Region>'

# Check to ensure Azure File Sync is available in the selected Azure
# region.
$regions = @()
Get-AzLocation | ForEach-Object { 
    if ($_.Providers -contains "Microsoft.StorageSync") { 
        $regions += $_.Location 
    } 
}

if ($regions -notcontains $region) {
    throw [System.Exception]::new("Azure File Sync is either not available in the selected Azure Region or the region is mistyped.")
}

# the resource group to deploy the Storage Sync Service into
$resourceGroup = '<RG_Name>'

# Check to ensure resource group exists and create it if doesn't
$resourceGroups = @()
Get-AzResourceGroup | ForEach-Object { 
    $resourceGroups += $_.ResourceGroupName 
}

if ($resourceGroups -notcontains $resourceGroup) {
    New-AzResourceGroup -Name $resourceGroup -Location $region
}

# the following command creates an AFS context 
# it enables subsequent AFS cmdlets to be executed with minimal 
# repetition of parameters or separate authentication 
Login-AzureRmStorageSync `
    -SubscriptionId $subID `
    -ResourceGroupName $resourceGroup `
    -TenantId $tenantID `
    -Location $region
```

Depois de criar o contexto de sincronização de ficheiros do Azure com o `Login-AzureR,StorageSync` cmdlet, pode criar o serviço de sincronização de armazenamento. Certifique-se de que substitua `<my-storage-sync-service>` com o nome pretendido do seu serviço de sincronização de armazenamento.

```PowerShell
$storageSyncName = "<my-storage-sync-service>"
New-AzureRmStorageSyncService -StorageSyncServiceName $storageSyncName
```

---

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente do Azure File Sync
O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Pode transferir o agente a partir da [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Quando o download for concluído, clique duas vezes o pacote do MSI para iniciar a instalação do agente de sincronização de ficheiros do Azure.

> [!Important]  
> Se pretender utilizar o Azure File Sync com um Cluster de ativação pós-falha, o agente do Azure File Sync tem de ser instalado em cada nó no cluster. Cada nó do cluster tem registado para trabalhar com o Azure File Sync.

Recomendamos que faça o seguinte:
- Deixe o caminho de instalação predefinido (C:\Program Files\Azure\StorageSyncAgent), para simplificar a manutenção de resolução de problemas e de servidor.
- Ative o Microsoft Update manter o Azure File Sync atualizados. Todas as atualizações, para o agente de sincronização de ficheiros do Azure, incluindo atualizações de funcionalidades e correções, ocorrerem do Microsoft Update. Recomendamos que instale a atualização mais recente para o Azure File Sync. Para obter mais informações, consulte [política de atualização do Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente do Azure File Sync estiver concluída, a interface do Usuário de registo do servidor é aberta automaticamente. Tem de ter um serviço de sincronização de armazenamento antes de registar; Veja a secção seguinte sobre como criar um serviço de sincronização de armazenamento.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Execute o seguinte código de PowerShell para transferir a versão adequada do agente do Azure File Sync para o seu sistema operacional e instalá-lo no seu sistema.

> [!Important]  
> Se pretender utilizar o Azure File Sync com um Cluster de ativação pós-falha, o agente do Azure File Sync tem de ser instalado em cada nó no cluster. Cada nó do cluster tem registado para trabalhar com o Azure File Sync.

```PowerShell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875004 `
        -OutFile "StorageSyncAgent.exe" 
}
elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://go.microsoft.com/fwlink/?linkid=875002 `
        -OutFile "StorageSyncAgent.exe" 
}
else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2 and Windows Server 2016")
}

# Extract the MSI from the install package
$tempFolder = New-Item -Path "afstemp" -ItemType Directory
Start-Process -FilePath ".\StorageSyncAgent.exe" -ArgumentList "/C /T:$tempFolder" -Wait

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "$($tempFolder.FullName)\StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.exe", ".\afstemp" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registar o Windows Server no Serviço de Sincronização de Armazenamento
Registar o Windows Server num Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e aquele serviço. Os servidores só podem ser registados num Serviço de Sincronização de Armazenamento e podem ser sincronizados com outros servidores e com partilhas de ficheiros do Azure associados ao mesmo Serviço de Sincronização de Armazenamento.

> [!Note]
> O registo do servidor utiliza as credenciais do Azure para criar uma relação de confiança entre o serviço de sincronização de armazenamento e do Windows Server, no entanto, em seguida, o servidor cria e usa sua própria identidade que é válida, desde que o servidor permanece registado e o token de assinatura de acesso partilhado (SAS do armazenamento) atual é válido. Não é possível emitir um novo token SAS para o servidor depois do servidor não está registado, isso elimina a capacidade do servidor para aceder as partilhas de ficheiros do Azure, parar a qualquer sincronização.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
A interface do Usuário de registo do servidor deverá abrir automaticamente após a instalação do agente do Azure File Sync. Se isso não acontecer, pode abri-lo manualmente da localização do ficheiro: C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quando abrir a interface do Usuário de registo do servidor, selecione **início de sessão** para começar.

Depois de iniciar sessão, lhe for pedido para as seguintes informações:

![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Subscrição do Azure**: A subscrição que contém o serviço de sincronização de armazenamento (veja [implementar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service)). 
- **Grupo de recursos**: O grupo de recursos que contém o serviço de sincronização de armazenamento.
- **Serviço de sincronização de armazenamento**: O nome do serviço de sincronização de armazenamento com a qual pretende registar.

Depois de selecionar as informações apropriadas, selecione **registar** para concluir o registo do servidor. Como parte do processo de registo, é-lhe pedido que volte a iniciar sessão novamente.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```PowerShell
$registeredServer = Register-AzureRmStorageSyncServer -StorageSyncServiceName $storageSyncName
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Criar um grupo de sincronização e um ponto final na cloud
Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Os grupos de sincronização têm de conter um ponto final da cloud, que representa uma partilha de ficheiros do Azure e um ou mais pontos finais de servidor. Um ponto de final de servidor representa um caminho de servidor registado. Um servidor pode ter pontos finais do servidor em vários grupos de sincronização. Pode criar os grupos de sincronização, conforme necessário descrever adequadamente a topologia de sincronização pretendido.

Um ponto final da cloud é um ponteiro para uma partilha de ficheiros do Azure. Todos os pontos finais de servidor irão sincronizar com um ponto final da cloud, tornando o ponto final da cloud do hub. A conta de armazenamento da partilha de ficheiros do Azure tem de estar localizada na mesma região que o serviço de sincronização de armazenamento. A totalidade da partilha de ficheiros do Azure será sincronizada, com uma exceção: Uma pasta especial, comparável para a pasta "Informações de Volume do sistema" oculta num NTFS volume, será aprovisionada. Este diretório é chamado ". SystemShareInformation". Contém metadados de sincronização importantes que não irão sincronizar com os outros pontos de extremidade. Não utilize ou eliminá-lo!

> [!Important]  
> Pode efetuar alterações a qualquer ponto final da cloud ou o ponto final do servidor no grupo de sincronização e os ficheiros sincronizados com os outros pontos finais no grupo de sincronização. Se fizer uma alteração ao ponto final da cloud (partilha de ficheiros do Azure) diretamente, as alterações primeiro tem de ser detetado por uma tarefa de deteção de alteração de sincronização de ficheiros do Azure. Uma tarefa de deteção de alteração de início de um ponto final da cloud apenas uma vez a cada 24 horas. Para obter mais informações, consulte [ficheiros do Azure, perguntas mais frequentes sobre](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para criar um grupo de sincronização, o [portal do Azure](https://portal.azure.com/), aceda ao seu serviço de sincronização de armazenamento e, em seguida, selecione **+ grupo de sincronização**:

![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel que se abre, introduza as informações seguintes para criar um grupo de sincronização com um ponto final da cloud:

- **Nome do grupo de sincronização**: O nome do grupo de sincronização a ser criada. Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico.
- **Subscrição**: A subscrição em que implementou o serviço de sincronização de armazenamento no [implementar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: Se selecionou **selecione a conta de armazenamento**, é apresentado o painel outro na qual pode selecionar a conta de armazenamento que tenha a partilha de ficheiros do Azure que pretende sincronizar com.
- **Partilha de ficheiros do Azure**: O nome da partilha de ficheiros do Azure com a qual pretende sincronizar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar o grupo de sincronização, execute o PowerShell seguinte. Não se esqueça de substituir `<my-sync-group>` com o nome pretendido do grupo de sincronização.

```PowerShell
$syncGroupName = "<my-sync-group>"
New-AzureRmStorageSyncGroup -SyncGroupName $syncGroupName -StorageSyncService $storageSyncName
```

Quando o grupo de sincronização tiver sido criado com êxito, pode criar seu ponto final da cloud. Não se esqueça de substituir `<my-storage-account>` e `<my-file-share>` com os valores esperados.

```PowerShell
# Get or create a storage account with desired name
$storageAccountName = "<my-storage-account>"
$storageAccount = Get-AzStorageAccount -ResourceGroupName $resourceGroup | Where-Object {
    $_.StorageAccountName -eq $storageAccountName
}

if ($storageAccount -eq $null) {
    $storageAccount = New-AzStorageAccount `
        -Name $storageAccountName `
        -ResourceGroupName $resourceGroup `
        -Location $region `
        -SkuName Standard_LRS `
        -Kind StorageV2 `
        -EnableHttpsTrafficOnly:$true
}

# Get or create an Azure file share within the desired storage account
$fileShareName = "<my-file-share>"
$fileShare = Get-AzStorageShare -Context $storageAccount.Context | Where-Object {
    $_.Name -eq $fileShareName -and $_.IsSnapshot -eq $false
}

if ($fileShare -eq $null) {
    $fileShare = New-AzStorageShare -Context $storageAccount.Context -Name $fileShareName
}

# Create the cloud endpoint
New-AzureRmStorageSyncCloudEndpoint `
    -StorageSyncServiceName $storageSyncName `
    -SyncGroupName $syncGroupName ` 
    -StorageAccountResourceId $storageAccount.Id `
    -StorageAccountShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Criar um ponto final de servidor
Os pontos finais de servidor representam uma localização específica num servidor registado, como uma pasta num volume do servidor. Um ponto final do servidor tem de ser um caminho de um servidor registado (em vez de uma partilha montada) e, para utilizar a camada de cloud, o caminho tem de ser num volume não pertencente ao sistema. Armazenamento ligado à rede (NAS) não é suportado.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para adicionar um ponto final do servidor, vá para o grupo de sincronização recém-criado e, em seguida, selecione **adicionar ponto final de servidor**.

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No painel **Adicionar ponto final de servidor**, introduza as informações seguintes para criar um ponto final de servidor:

- **Servidor registado**: O nome do servidor ou cluster onde pretende criar o ponto final do servidor.
- **Caminho**: O caminho do Windows Server a ser sincronizada como parte do grupo de sincronização.
- **Em camada de cloud**: Um comutador para ativar ou desativar a cloud em camadas. Com a cloud em camadas, com pouca frequência utilizada ou aceder a ficheiros pode ser colocado em camadas para ficheiros do Azure.
- **Espaço livre do volume**: A quantidade de espaço livre para reservar no volume onde se localiza o ponto final do servidor. Por exemplo, se o espaço livre do volume está definido como 50% num volume que tenha um ponto de final de servidor único, aproximadamente metade a quantidade de dados está em camadas para ficheiros do Azure. Independentemente de se cloud em camadas é ativada, a partilha de ficheiros do Azure tem sempre uma cópia completa dos dados no grupo de sincronização.

Para adicionar o ponto final do servidor, selecione **criar**. Os ficheiros agora são mantidos em sincronia entre a sua partilha de ficheiros do Azure e no Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Execute os seguintes comandos do PowerShell para criar o ponto final do servidor e não se esqueça de substituir `<your-server-endpoint-path>` e `<your-volume-free-space>` com os valores pretendidos.

```PowerShell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>

if ($cloudTieringDesired) {
    # Ensure endpoint path is not the system volume
    $directoryRoot = [System.IO.Directory]::GetDirectoryRoot($serverEndpointPath)
    $osVolume = "$($env:SystemDrive)\"
    if ($directoryRoot -eq $osVolume) {
        throw [System.Exception]::new("Cloud tiering cannot be enabled on the system volume")
    }

    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering $true `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
}
else {
    # Create server endpoint
    New-AzureRmStorageSyncServerEndpoint `
        -StorageSyncServiceName $storageSyncName `
        -SyncGroupName $syncGroupName `
        -ServerId $registeredServer.Id `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="onboarding-with-azure-file-sync"></a>Integração com o Azure File Sync
Os passos recomendados para integrar no Azure File Sync para o primeiro com zero tempo de inatividade, preservando a fidelidade de ficheiro completo e lista de controlo de acesso (ACL) são os seguintes:
 
1. Implemente um serviço de sincronização de armazenamento.
2. Crie um grupo de sincronização.
3. Instale o agente de sincronização de ficheiros do Azure no servidor com o conjunto de dados completo.
4. Registe-se de que o servidor e crie um ponto final do servidor na partilha. 
5. Permitir que a sincronização de fazer o carregamento total à partilha de ficheiros do Azure (ponto final da cloud).  
6. Depois do carregamento inicial estiver concluído, instale o agente de sincronização de ficheiros do Azure em cada um dos restantes servidores.
7. Crie novas partilhas de ficheiros em cada um dos restantes servidores.
8. Crie pontos finais do servidor em novas partilhas de ficheiros com a política de camadas na cloud, se assim o desejar. (Este passo requer armazenamento adicional para estar disponível para a configuração inicial.)
9. Deixe que o agente de sincronização de ficheiros do Azure para efetuar um restauro rápido do espaço de nomes completo sem a transferência de dados real. Após a sincronização completa do espaço de nomes, o motor de sincronização preencherá o espaço de disco local com base na política de camadas de cloud para o ponto final do servidor. 
10. Certifique-se a sincronização estiver concluída e testar a sua topologia conforme pretendido. 
11. Redirecione utilizadores e aplicações para esta nova partilha.
12. Opcionalmente, pode eliminar quaisquer partilhas duplicadas nos servidores.
 
Se não tem armazenamento extra para a integração inicial e gostaria de ligar a partilhas existentes, pode efetuar o seeding previamente os dados em partilhas de ficheiros do Azure. Essa abordagem é sugerida, se e apenas se pode aceitar o tempo de inatividade e não garantir absolutamente nenhuma alteração de dados nas partilhas de servidor durante o processo de integração inicial. 
 
1. Certifique-se de que os dados em qualquer um do servidor não é possível alterar durante o processo de integração.
2. Pré-seed partilhas de ficheiros com os dados do servidor com qualquer ferramenta de transferência de dados sobre o SMB, por exemplo, Robocopy, cópia SMB direta. Uma vez que o AzCopy não carregar os dados sobre o SMB para que não pode ser utilizada para o seeding previamente.
3. Crie a topologia de sincronização de ficheiros do Azure com os pontos finais de servidor pretendida apontando para as partilhas existentes.
4. Permitir que a sincronização de concluir o processo de reconciliação em todos os pontos finais. 
5. Depois de concluída a reconciliação, é possível abrir partilhas para que as alterações.
 
Note que, atualmente, o seeding previamente abordagem tem algumas limitações- 
- Total fidelidade em ficheiros não é preservada. Por exemplo, ficheiros de perdem as ACLs e os carimbos de data /.
- As alterações de dados no servidor antes de topologia de sincronização está totalmente operacional e em execução podem causar conflitos em pontos de extremidade dos servidor.  
- Após a criação do ponto final da cloud, o Azure File Sync executa um processo para detetar os ficheiros na cloud antes de iniciar a sincronização inicial. O tempo necessário para concluir este processo varia consoante os diversos fatores, como velocidade da rede, a largura de banda disponível e o número de ficheiros e pastas. Para a estimativa aproximada na versão de pré-visualização, o processo de deteção é executada aproximadamente em 10 ficheiros/seg.  Por conseguinte, mesmo que o seeding previamente o execuções rápida, o tempo geral para obter um sistema totalmente em execução pode ser significativamente mais tempo, quando os dados estão pré-propagados na cloud.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrar uma implementação de replicação de DFS (DFS-R) para o Azure File Sync
Para migrar uma implementação de DFS-R para o Azure File Sync:

1. Crie um grupo de sincronização para representar a topologia de DFS-R que está substituindo.
2. Iniciar no servidor que tem o conjunto completo de dados na sua topologia de DFS-R para migrar. Instale o Azure File Sync nesse servidor.
3. Registre-se de que o servidor e criar um ponto final do servidor para o primeiro servidor a serem migrados. Não ative a cloud em camadas.
4. Permitir que todos a sincronização de dados para a partilha de ficheiros do Azure (ponto final da cloud).
5. Instalar e registar o agente do Azure File Sync em cada um dos restantes servidores de DFS-R.
6. Desativar o DFS-R. 
7. Crie um ponto final do servidor em cada um dos servidores do DFS-R. Não ative a cloud em camadas.
8. Certifique-se a sincronização estiver concluída e testar a sua topologia conforme pretendido.
9. Extinguir o DFS-R.
10. Maio de camadas de cloud agora ser ativada em qualquer ponto final de servidor conforme pretendido.

Para obter mais informações, consulte [interoperabilidade do Azure File Sync com o sistema de ficheiros distribuído (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Passos Seguintes
- [Adicionar ou remover um ponto de final de servidor de sincronização de ficheiros do Azure](storage-sync-files-server-endpoint.md)
- [Registar ou anular o registo de um servidor com o Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorizar a sincronização de ficheiros do Azure](storage-sync-files-monitoring.md)
