---
title: Implantar Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como implantar Sincronização de Arquivos do Azure, do início ao fim.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 4f9a2842f99c7f8b0bb9f820584fb2cd4e41a2b2
ms.sourcegitcommit: a5ebf5026d9967c4c4f92432698cb1f8651c03bb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/08/2019
ms.locfileid: "74927883"
---
# <a name="deploy-azure-file-sync"></a>Implementar Azure File Sync
Use Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em arquivos do Azure, mantendo, ao mesmo tempo, a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Você pode usar qualquer protocolo que esteja disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter quantos caches forem necessários em todo o mundo.

É altamente recomendável que você leia [planejando uma implantação de arquivos do Azure](storage-files-planning.md) e [planeje uma implantação de sincronização de arquivos do Azure](storage-sync-files-planning.md) antes de concluir as etapas descritas neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
* Um compartilhamento de arquivos do Azure na mesma região que você deseja implantar Sincronização de Arquivos do Azure. Para obter mais informações, consulte:
    - [Disponibilidade de região](storage-sync-files-planning.md#region-availability) para sincronização de arquivos do Azure.
    - [Crie um compartilhamento de arquivos](storage-how-to-create-file-share.md) para obter uma descrição passo a passo de como criar um compartilhamento de arquivos.
* Pelo menos uma instância do Windows Server ou cluster do Windows Server com suporte para sincronização com o Sincronização de Arquivos do Azure. Para obter mais informações sobre as versões com suporte do Windows Server, consulte [interoperabilidade com o Windows Server](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* O módulo AZ PowerShell pode ser usado com o PowerShell 5,1 ou o PowerShell 6 +. Você pode usar o módulo AZ PowerShell para Sincronização de Arquivos do Azure em qualquer sistema com suporte, incluindo sistemas não Windows, no entanto, o cmdlet de registro do servidor sempre deve ser executado na instância do Windows Server que você está registrando (isso pode ser feito diretamente ou por meio do PowerShell comunicação remota). No Windows Server 2012 R2, você pode verificar se está executando pelo menos o PowerShell 5,1.\* examinando o valor da propriedade **psversion** do objeto **$psversiontable** :

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se o valor de PSVersion for menor que 5,1.\*, como será o caso com a maioria das instalações mais recentes do Windows Server 2012 R2, você pode atualizar facilmente baixando e instalando o [Windows Management Framework (WMF) 5,1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para baixar e instalar o Windows Server 2012 R2 é **win 8.1 andw2k12r2-KB\*\*\*\*\*\*\*-x64. msu**. 

    O PowerShell 6 + pode ser usado com qualquer sistema com suporte e pode ser baixado por meio de sua [página do GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se você planeja usar a interface do usuário de registro do servidor, em vez de registrá-la diretamente do PowerShell, você deve usar o PowerShell 5,1.

* Se você tiver optado por usar o PowerShell 5,1, verifique se pelo menos o .NET 4.7.2 está instalado. Saiba mais sobre [as versões do .NET Framework e as dependências](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) em seu sistema.

    > [!Important]  
    > Se você estiver instalando o .NET 4.7.2 + no Windows Server Core, será necessário instalar o com os sinalizadores `quiet` e `norestart` ou a instalação falhará. Por exemplo, se estiver instalando o .NET 4,8, o comando seria semelhante ao seguinte:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* O módulo AZ PowerShell, que pode ser instalado seguindo as instruções aqui: [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > O módulo AZ. StorageSync agora é instalado automaticamente quando você instala o módulo AZ PowerShell.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar o Windows Server para ser utilizado com o Azure File Sync
Para cada servidor que você pretende usar com Sincronização de Arquivos do Azure, incluindo cada nó de servidor em um cluster de failover, desabilite a **configuração de segurança reforçada do Internet Explorer**. Isso é necessário apenas para o registro inicial do servidor. Pode reativá-la depois de o servidor estar registado.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Você pode ignorar esta etapa se estiver implantando Sincronização de Arquivos do Azure no Windows Server Core.

1. Abra o Gestor de Servidor.
2. Clique em **servidor local**:  
    !["servidor local" no lado esquerdo da interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. No subpainel **Propriedades**, selecione a ligação para **Configuração de Segurança Avançada do IE**.  
    ![o painel "configuração de segurança aprimorada do IE" na interface do usuário do Gerenciador do Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Na caixa de diálogo **configuração de segurança reforçada do Internet Explorer** , selecione **desativado** para **Administradores** e **usuários**:  
    ![a janela pop-up de configuração de segurança reforçada do Internet Explorer com "desativado" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para desabilitar a configuração de segurança reforçada do Internet Explorer, execute o seguinte em uma sessão do PowerShell com privilégios elevados:

```powershell
$installType = (Get-ItemProperty "HKLM:\SOFTWARE\Microsoft\Windows NT\CurrentVersion\").InstallationType

# This step is not required for Server Core
if ($installType -ne "Server Core") {
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
}
``` 

---

## <a name="deploy-the-storage-sync-service"></a>Implementar o Serviço de Sincronização de Armazenamento 
A implantação do Sincronização de Arquivos do Azure começa com o posicionamento de um recurso de **serviço de sincronização de armazenamento** em um grupo de recursos da sua assinatura selecionada. Recomendamos o provisionamento como poucas delas, conforme necessário. Você criará uma relação de confiança entre os servidores e esse recurso, e um servidor só poderá ser registrado em um serviço de sincronização de armazenamento. Como resultado, é recomendável implantar quantos serviços de sincronização de armazenamento forem necessários para separar grupos de servidores. Tenha em mente que os servidores de diferentes serviços de sincronização de armazenamento não podem sincronizar uns com os outros.

> [!Note]
> O serviço de sincronização de armazenamento herda as permissões de acesso da assinatura e do grupo de recursos em que foi implantado. Recomendamos que você verifique cuidadosamente quem tem acesso a ele. As entidades com acesso de gravação podem iniciar a sincronização de novos conjuntos de arquivos de servidores registrados para esse serviço de sincronização de armazenamento e fazem com que os dados fluam para o armazenamento do Azure que esteja acessível a eles.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para implantar um serviço de sincronização de armazenamento, vá para o [portal do Azure](https://portal.azure.com/), clique em *criar um recurso* e procure por sincronização de arquivos do Azure. Nos resultados da pesquisa, selecione **sincronização de arquivos do Azure**e, em seguida, selecione **criar** para abrir a guia **implantar sincronização de armazenamento** .

No painel que se abre, introduza as informações seguintes:

- **Nome**: um nome exclusivo (por assinatura) para o serviço de sincronização de armazenamento.
- **Assinatura**: a assinatura na qual você deseja criar o serviço de sincronização de armazenamento. Dependendo da estratégia de configuração da sua organização, você pode ter acesso a uma ou mais assinaturas. Uma assinatura do Azure é o contêiner mais básico para cobrança para cada serviço de nuvem (como arquivos do Azure).
- **Grupo de recursos**: um grupo de recursos é um grupo lógico de recursos do Azure, como uma conta de armazenamento ou um serviço de sincronização de armazenamento. Você pode criar um novo grupo de recursos ou usar um grupo de recursos existente para Sincronização de Arquivos do Azure. (É recomendável usar grupos de recursos como contêineres para isolar recursos logicamente para sua organização, como agrupamento de recursos ou recursos de RH para um projeto específico.)
- **Local**: a região na qual você deseja implantar sincronização de arquivos do Azure. Somente regiões com suporte estão disponíveis nesta lista.

Quando tiver terminado, selecione **criar** para implantar o serviço de sincronização de armazenamento.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Substitua **< Az_Region >** , **< RG_Name**> e **<** my_storage_sync_service de > com seus próprios valores e, em seguida, use o seguinte CMDS para criar e implantar um serviço de sincronização de armazenamento:

```powershell
$hostType = (Get-Host).Name

if ($installType -eq "Server Core" -or $hostType -eq "ServerRemoteHost") {
    Connect-AzAccount -UseDeviceAuthentication
}
else {
    Connect-AzAccount
}

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

$storageSyncName = "<my_storage_sync_service>"
$storageSync = New-AzStorageSyncService -ResourceGroupName $resourceGroup -Name $storageSyncName -Location $region
```

---

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente do Azure File Sync
O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure. 

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Você pode baixar o agente do [centro de download da Microsoft](https://go.microsoft.com/fwlink/?linkid=858257). Quando o download for concluído, clique duas vezes no pacote MSI para iniciar a instalação do agente de Sincronização de Arquivos do Azure.

> [!Important]  
> Se você pretende usar Sincronização de Arquivos do Azure com um cluster de failover, o agente de Sincronização de Arquivos do Azure deve ser instalado em todos os nós do cluster. Cada nó do cluster deve ser registrado para trabalhar com Sincronização de Arquivos do Azure.

Recomendamos que você faça o seguinte:
- Deixe o caminho de instalação padrão (C:\Program Files\Azure\StorageSyncAgent) para simplificar a solução de problemas e a manutenção do servidor.
- Habilite Microsoft Update para manter Sincronização de Arquivos do Azure atualizado. Todas as atualizações, para o agente de Sincronização de Arquivos do Azure, incluindo atualizações de recursos e hotfixes, ocorrem em Microsoft Update. É recomendável instalar a atualização mais recente para Sincronização de Arquivos do Azure. Para obter mais informações, consulte [sincronização de arquivos do Azure Update Policy](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente de Sincronização de Arquivos do Azure for concluída, a interface do usuário de registro do servidor será aberta automaticamente. Você deve ter um serviço de sincronização de armazenamento antes de se registrar; consulte a próxima seção sobre como criar um serviço de sincronização de armazenamento.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Execute o seguinte código do PowerShell para baixar a versão apropriada do agente de Sincronização de Arquivos do Azure para o seu sistema operacional e instale-o no sistema.

> [!Important]  
> Se você pretende usar Sincronização de Arquivos do Azure com um cluster de failover, o agente de Sincronização de Arquivos do Azure deve ser instalado em todos os nós do cluster. Cada nó do cluster deve ser registrado para funcionar com Sincronização de Arquivos do Azure.

```powershell
# Gather the OS version
$osver = [System.Environment]::OSVersion.Version

# Download the appropriate version of the Azure File Sync agent for your OS.
if ($osver.Equals([System.Version]::new(10, 0, 17763, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2019 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(10, 0, 14393, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2016 `
        -OutFile "StorageSyncAgent.msi" 
} elseif ($osver.Equals([System.Version]::new(6, 3, 9600, 0))) {
    Invoke-WebRequest `
        -Uri https://aka.ms/afs/agent/Server2012R2 `
        -OutFile "StorageSyncAgent.msi" 
} else {
    throw [System.PlatformNotSupportedException]::new("Azure File Sync is only supported on Windows Server 2012 R2, Windows Server 2016, and Windows Server 2019")
}

# Install the MSI. Start-Process is used to PowerShell blocks until the operation is complete.
# Note that the installer currently forces all PowerShell sessions closed - this is a known issue.
Start-Process -FilePath "StorageSyncAgent.msi" -ArgumentList "/quiet" -Wait

# Note that this cmdlet will need to be run in a new session based on the above comment.
# You may remove the temp folder containing the MSI and the EXE installer
Remove-Item -Path ".\StorageSyncAgent.msi" -Recurse -Force
```

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registar o Windows Server no Serviço de Sincronização de Armazenamento
Registar o Windows Server num Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e aquele serviço. Os servidores só podem ser registados num Serviço de Sincronização de Armazenamento e podem ser sincronizados com outros servidores e com partilhas de ficheiros do Azure associados ao mesmo Serviço de Sincronização de Armazenamento.

> [!Note]
> O registro de servidor usa suas credenciais do Azure para criar uma relação de confiança entre o serviço de sincronização de armazenamento e o Windows Server, no entanto, posteriormente, o servidor cria e usa sua própria identidade que é válida, desde que o servidor permaneça registrado e o o token de assinatura de acesso compartilhado (SAS de armazenamento) atual é válido. Um novo token SAS não pode ser emitido para o servidor após o cancelamento do registro do servidor, removendo assim a capacidade do servidor de acessar seus compartilhamentos de arquivos do Azure, interrompendo qualquer sincronização.

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
A interface do usuário de registro do servidor deve ser aberta automaticamente após a instalação do agente de Sincronização de Arquivos do Azure. Se isso não acontecer, pode abri-la manualmente na localização do ficheiro, em C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quando a interface do usuário de registro do servidor for aberta, selecione **entrar** para começar.

Depois de entrar, você será solicitado a fornecer as seguintes informações:

![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Assinatura do Azure**: a assinatura que contém o serviço de sincronização de armazenamento (consulte [implantar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service)). 
- **Grupo de recursos**: o grupo de recursos que contém o serviço de sincronização de armazenamento.
- **Serviço de sincronização de armazenamento**: o nome do serviço de sincronização de armazenamento com o qual você deseja registrar.

Depois de selecionar as informações apropriadas, selecione **registrar** para concluir o registro do servidor. Como parte do processo de registo, é-lhe pedido que volte a iniciar sessão novamente.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Criar um grupo de sincronização e um ponto final na cloud
Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Os grupos de sincronização têm de conter um ponto final da cloud, que representa uma partilha de ficheiros do Azure e um ou mais pontos finais de servidor. Um ponto de extremidade do servidor representa um caminho em um servidor registrado. Um servidor pode ter pontos de extremidade de servidor em vários grupos de sincronização. Você pode criar quantos grupos de sincronização precisar para descrever adequadamente a topologia de sincronização desejada.

Um ponto de extremidade de nuvem é um ponteiro para um compartilhamento de arquivos do Azure. Todos os pontos de extremidade do servidor serão sincronizados com um ponto final da nuvem, tornando o ponto de extremidade da nuvem o Hub. A conta de armazenamento para o compartilhamento de arquivos do Azure deve estar localizada na mesma região que o serviço de sincronização de armazenamento. A totalidade do compartilhamento de arquivos do Azure será sincronizada, com uma exceção: uma pasta especial, comparável à pasta oculta "informações de volume do sistema" em um volume NTFS, será provisionada. Esse diretório é chamado de ". SystemShareInformation". Ele contém metadados de sincronização importantes que não serão sincronizados com outros pontos de extremidade. Não use nem exclua!

> [!Important]  
> Você pode fazer alterações em qualquer ponto de extremidade de nuvem ou ponto de extremidade de servidor no grupo de sincronização e fazer com que os arquivos sejam sincronizados com os outros pontos no grupo de sincronização. Se você fizer uma alteração no ponto de extremidade de nuvem (compartilhamento de arquivos do Azure) diretamente, as alterações primeiro precisarão ser descobertas por um trabalho de detecção de Sincronização de Arquivos do Azure alteração. Um trabalho de detecção de alteração é iniciado para um ponto de extremidade de nuvem somente uma vez a cada 24 horas. Para obter mais informações, consulte [perguntas frequentes sobre os arquivos do Azure](storage-files-faq.md#afs-change-detection).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para criar um grupo de sincronização, na [portal do Azure](https://portal.azure.com/), vá para o serviço de sincronização de armazenamento e, em seguida, selecione **+ grupo de sincronização**:

![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel que se abre, introduza as informações seguintes para criar um grupo de sincronização com um ponto final da cloud:

- **Nome do grupo de sincronização**: o nome do grupo de sincronização a ser criado. Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico.
- **Assinatura**: a assinatura na qual você implantou o serviço de sincronização de armazenamento em [implantar o serviço de sincronização de armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: se você selecionar **selecionar conta de armazenamento**, será exibido outro painel no qual é possível selecionar a conta de armazenamento que tem o compartilhamento de arquivos do Azure com o qual você deseja sincronizar.
- **Compartilhamento de arquivos do Azure**: o nome do compartilhamento de arquivos do Azure com o qual você deseja sincronizar.

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar o grupo de sincronização, execute o PowerShell a seguir. Lembre-se de substituir `<my-sync-group>` pelo nome desejado do grupo de sincronização.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Depois que o grupo de sincronização tiver sido criado com êxito, você poderá criar seu ponto de extremidade de nuvem. Certifique-se de substituir `<my-storage-account>` e `<my-file-share>` pelos valores esperados.

```powershell
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
New-AzStorageSyncCloudEndpoint `
    -Name $fileShare.Name `
    -ParentObject $syncGroup `
    -StorageAccountResourceId $storageAccount.Id `
    -AzureFileShareName $fileShare.Name
```

---

## <a name="create-a-server-endpoint"></a>Criar um ponto final de servidor
Os pontos finais de servidor representam uma localização específica num servidor registado, como uma pasta num volume do servidor. Um ponto de extremidade do servidor deve ser um caminho em um servidor registrado (em vez de um compartilhamento montado) e, para usar camadas de nuvem, o caminho deve estar em um volume que não seja do sistema. Não há suporte para o NAS (armazenamento anexado à rede).

# <a name="portaltabazure-portal"></a>[Portal](#tab/azure-portal)
Para adicionar um ponto de extremidade do servidor, vá até o grupo de sincronização recém-criado e selecione **Adicionar ponto de extremidade do servidor**.

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No painel **Adicionar ponto final de servidor**, introduza as informações seguintes para criar um ponto final de servidor:

- **Servidor registrado**: o nome do servidor ou cluster em que você deseja criar o ponto de extremidade do servidor.
- **Caminho**: o caminho do Windows Server a ser sincronizado como parte do grupo de sincronização.
- **Camada de nuvem**: uma opção para habilitar ou desabilitar a disposição em camadas de nuvem. Com a disposição em camadas da nuvem, os arquivos usados raramente ou acessados podem ser em camadas para os arquivos do Azure.
- **Espaço livre no volume**: a quantidade de espaço livre a ser reservada no volume no qual o ponto de extremidade do servidor está localizado. Por exemplo, se o espaço livre do volume for definido como 50% em um volume que tenha um único ponto de extremidade do servidor, aproximadamente metade da quantidade de dados será em camadas para os arquivos do Azure. Independentemente de a disposição em camadas da nuvem estar habilitada, o compartilhamento de arquivos do Azure sempre terá uma cópia completa dos dados no grupo de sincronização.

Para adicionar o ponto de extremidade do servidor, selecione **criar**. Os arquivos agora são mantidos em sincronia em seu compartilhamento de arquivos do Azure e no Windows Server. 

# <a name="powershelltabazure-powershell"></a>[PowerShell](#tab/azure-powershell)
Execute os comandos do PowerShell a seguir para criar o ponto de extremidade do servidor e certifique-se de substituir `<your-server-endpoint-path>` e `<your-volume-free-space>` pelos valores desejados.

```powershell
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
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -CloudTiering `
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath 
}
```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Definir configurações de rede virtual e de firewall

### <a name="portal"></a>Portal
Se você quiser configurar a sincronização de arquivos do Azure para trabalhar com as configurações de firewall e rede virtual, faça o seguinte:

1. No portal do Azure, navegue até a conta de armazenamento que você deseja proteger.
1. Selecione o botão **firewalls e redes virtuais** no menu à esquerda.
1. Selecione **redes selecionadas** em **permitir acesso de**.
1. Verifique se os servidores IP ou rede virtual estão listados na seção apropriada.
1. Certifique-se **de que permitir que serviços da Microsoft confiáveis acessem esta conta de armazenamento** esteja marcado.
1. Selecione **salvar** para salvar as configurações.

![Definindo configurações de rede virtual e de firewall para trabalhar com a sincronização de arquivos do Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Integração com o Sincronização de Arquivos do Azure
As etapas recomendadas para a integração em Sincronização de Arquivos do Azure para o primeiro com zero tempo de inatividade enquanto preserva a fidelidade de arquivo completo e a ACL (lista de controle de acesso) são as seguintes:
 
1. Implantar um serviço de sincronização de armazenamento.
2. Crie um grupo de sincronização.
3. Instale o agente de Sincronização de Arquivos do Azure no servidor com o conjunto de dados completo.
4. Registre esse servidor e crie um ponto de extremidade do servidor no compartilhamento. 
5. Permitir que a sincronização faça o upload completo para o compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).  
6. Após a conclusão do upload inicial, instale Sincronização de Arquivos do Azure agente em cada um dos servidores restantes.
7. Crie novos compartilhamentos de arquivos em cada um dos servidores restantes.
8. Crie pontos de extremidade do servidor em novos compartilhamentos de arquivos com a política de camadas de nuvem, se desejado. (Essa etapa requer que o armazenamento adicional esteja disponível para a configuração inicial.)
9. Permitir que o Sincronização de Arquivos do Azure Agent faça uma restauração rápida do namespace completo sem a transferência de dados real. Após a sincronização completa do namespace, o mecanismo de sincronização preencherá o espaço em disco local com base na política de camadas de nuvem para o ponto de extremidade do servidor. 
10. Certifique-se de que a sincronização seja concluída e teste sua topologia conforme desejado. 
11. Redirecione usuários e aplicativos para esse novo compartilhamento.
12. Opcionalmente, você pode excluir quaisquer compartilhamentos duplicados nos servidores.
 
Se você não tiver armazenamento extra para integração inicial e quiser anexar aos compartilhamentos existentes, você poderá propagar previamente os dados nos compartilhamentos de arquivos do Azure. Essa abordagem é sugerida, se e somente se você puder aceitar o tempo de inatividade e garantir que nenhuma alteração de dados nos compartilhamentos do servidor seja feita durante o processo de integração inicial. 
 
1. Verifique se os dados em qualquer um dos servidores não podem ser alterados durante o processo de integração.
2. Pré-propagar compartilhamentos de arquivos do Azure com os dados do servidor usando qualquer ferramenta de transferência de dados no SMB, por exemplo, Robocopy, Direct SMB Copy. Como o AzCopy não carrega dados por meio do SMB para que ele não possa ser usado para a pré-propagação.
3. Crie Sincronização de Arquivos do Azure topologia com os pontos de extremidade do servidor desejados apontando para os compartilhamentos existentes.
4. Permita que a sincronização conclua o processo de reconciliação em todos os pontos de extremidade. 
5. Depois que a reconciliação for concluída, você poderá abrir compartilhamentos para alterações.
 
Atualmente, a abordagem de pré-propagação tem algumas limitações – 
- A fidelidade total nos arquivos não é preservada. Por exemplo, arquivos perdem ACLs e carimbos de data/hora.
- As alterações de dados no servidor antes que a topologia de sincronização esteja totalmente ativa e em execução podem causar conflitos nos pontos de extremidade do servidor.  
- Depois que o ponto de extremidade de nuvem é criado, o Sincronização de Arquivos do Azure executa um processo para detectar os arquivos na nuvem antes de iniciar a sincronização inicial. O tempo necessário para concluir esse processo varia de acordo com os vários fatores, como velocidade de rede, largura de banda disponível e número de arquivos e pastas. Para a estimativa aproximada da versão de visualização, o processo de detecção é executado aproximadamente em 10 arquivos/s.  Portanto, mesmo que a pré-propagação seja executada rapidamente, o tempo geral para obter um sistema totalmente em execução pode ser significativamente maior quando os dados são previamente propagados na nuvem.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Restauração de autoatendimento por meio de versões anteriores e VSS (Serviço de Cópias de Sombra de Volume)

> [!IMPORTANT]
> As informações a seguir só podem ser usadas com a versão 9 (ou superior) do agente de sincronização de armazenamento. As versões inferiores a 9 não terão os cmdlets StorageSyncSelfService.

As versões anteriores são um recurso do Windows que permite que você utilize instantâneos VSS do lado do servidor de um volume para apresentar versões restauráveis de um arquivo a um cliente SMB.
Isso permite um cenário poderoso, comumente conhecido como restauração de autoatendimento, diretamente para operadores de informações, em vez de depender da restauração de um administrador de ti.

Os instantâneos do VSS e as versões anteriores funcionam independentemente do Sincronização de Arquivos do Azure. No entanto, a camada da nuvem deve ser definida como um modo compatível. Muitos pontos de extremidade do Sincronização de Arquivos do Azure Server podem existir no mesmo volume. Você precisa fazer a seguinte chamada do PowerShell por volume que tenha até mesmo um ponto de extremidade do servidor no qual você planeja ou está usando camadas de nuvem.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

Os instantâneos do VSS são tirados de um volume inteiro. Por padrão, até 64 instantâneos podem existir para um determinado volume, contanto que haja espaço suficiente para armazenar os instantâneos. O VSS trata isso automaticamente. A agenda de instantâneo padrão usa dois instantâneos por dia, de segunda-feira a sexta-feira. Essa agenda pode ser configurada por meio de uma tarefa agendada do Windows. O cmdlet do PowerShell acima faz duas coisas:
1. Ele configura as sincronizações de arquivos do Azure em camadas de nuvem no volume especificado para serem compatíveis com as versões anteriores e garante que um arquivo possa ser restaurado de uma versão anterior, mesmo que ele tenha sido em camadas para a nuvem no servidor. 
2. Ele habilita a agenda padrão do VSS. Em seguida, você pode decidir modificá-lo mais tarde. 

> [!Note]  
> Há duas coisas importantes a serem observadas:
>- Se você usar o parâmetro-Force e o VSS estiver habilitado no momento, ele substituirá o agendamento atual do instantâneo do VSS e o substituirá pelo agendamento padrão. Certifique-se de salvar sua configuração personalizada antes de executar o cmdlet.
> - Se você estiver usando esse cmdlet em um nó de cluster, também deverá executá-lo em todos os outros nós no cluster! 

Para ver se a compatibilidade de restauração de autoatendimento está habilitada, você pode executar o cmdlet a seguir.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Ele listará todos os volumes no servidor, bem como o número de dias compatíveis em camadas de nuvem para cada um. Esse número é calculado automaticamente com base no máximo de instantâneos possíveis por volume e no agendamento de instantâneo padrão. Portanto, por padrão, todas as versões anteriores apresentadas a um operador de informações podem ser usadas para restaurar do. O mesmo será verdadeiro se você alterar o agendamento padrão para obter mais instantâneos.
No entanto, se você alterar o agendamento de uma maneira que resultará em um instantâneo disponível no volume que seja mais antigo que o valor de dias compatíveis, os usuários não poderão usar esse instantâneo mais antigo (versão anterior) para restaurar.

> [!Note]
> Habilitar a restauração de autoatendimento pode afetar o consumo e a fatura do armazenamento do Azure. Esse impacto é limitado aos arquivos atualmente em camadas no servidor. Habilitar esse recurso garante que haja uma versão de arquivo disponível na nuvem que pode ser referenciada por meio de uma entrada de versões anteriores (instantâneo do VSS).
>
> Se você desabilitar o recurso, o consumo de armazenamento do Azure diminuirá lentamente até que a janela de dias compatíveis tenha passado. Não há como acelerar isso. 

O número máximo padrão de instantâneos VSS por volume (64), bem como o agendamento padrão para levá-los, resulta em um máximo de 45 dias de versões anteriores que um operador de informações pode restaurar, dependendo de quantos instantâneos VSS você pode armazenar em seu volume.

Se for Max. 64 os instantâneos do VSS por volume não são a configuração correta para você, você pode [alterar esse valor por meio de uma chave do registro](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Para que o novo limite entre em vigor, você precisa executar novamente o cmdlet para habilitar a compatibilidade de versão anterior em cada volume que foi habilitado anteriormente, com o sinalizador-Force para obter o novo número máximo de instantâneos do VSS por volume em conta. Isso resultará em um número calculado recentemente de dias compatíveis. Observe que essa alteração só entrará em vigor em arquivos em camadas recentemente e substituirá as personalizações na agenda do VSS que você tenha feito.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrar uma implantação Replicação do DFS (DFS-R) para Sincronização de Arquivos do Azure
Para migrar uma implantação de DFS-R para Sincronização de Arquivos do Azure:

1. Crie um grupo de sincronização para representar a topologia do DFS-R que você está substituindo.
2. Inicie o no servidor que tem o conjunto completo de dados em sua topologia do DFS-R para migrar. Instale Sincronização de Arquivos do Azure nesse servidor.
3. Registre esse servidor e crie um ponto de extremidade do servidor para o primeiro servidor a ser migrado. Não habilite a camada de nuvem.
4. Permita que todos os dados sejam sincronizados com o compartilhamento de arquivos do Azure (ponto de extremidade de nuvem).
5. Instale e registre o agente de Sincronização de Arquivos do Azure em cada um dos servidores DFS-R restantes.
6. Desabilite o DFS-R. 
7. Crie um ponto de extremidade do servidor em cada um dos servidores DFS-R. Não habilite a camada de nuvem.
8. Certifique-se de que a sincronização seja concluída e teste sua topologia conforme desejado.
9. Desativar o DFS-R.
10. A disposição em camadas da nuvem agora pode ser habilitada em qualquer ponto de extremidade do servidor, conforme desejado.

Para obter mais informações, consulte [interoperabilidade de sincronização de arquivos do Azure com sistema de arquivos distribuído (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Passos seguintes
- [Adicionar ou remover um ponto de extremidade do Sincronização de Arquivos do Azure Server](storage-sync-files-server-endpoint.md)
- [Registrar ou cancelar o registro de um servidor com o Sincronização de Arquivos do Azure](storage-sync-files-server-registration.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
