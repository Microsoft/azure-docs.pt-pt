---
title: Implementar sincronização de ficheiros Azure  Microsoft Docs
description: Saiba como implementar o Azure File Sync, do início ao fim.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f2c4e762ebf10a5ca2120c13a52750a7781d60b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268070"
---
# <a name="deploy-azure-file-sync"></a>Implementar Azure File Sync
Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Podes ter as caches que precisares em todo o mundo.

Recomendamos vivamente que leia planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md) e Planeamento para uma implementação de Sincronização de [Ficheiros Azure](storage-sync-files-planning.md) antes de completar os passos descritos neste artigo.

## <a name="prerequisites"></a>Pré-requisitos
* Uma participação de ficheiros Azure na mesma região que pretende implementar o Azure File Sync. Para mais informações, consulte:
    - [Disponibilidade da região](storage-sync-files-planning.md#azure-file-sync-region-availability) para O Sincronizado de Ficheiros Azure.
    - [Crie uma partilha](storage-how-to-create-file-share.md) de ficheiros para uma descrição passo a passo de como criar uma partilha de ficheiros.
* Pelo menos uma instância suportada do Windows Server ou do Windows Server para sincronizar com o Sync de Ficheiros Azure. Para obter mais informações sobre as versões suportadas do Windows Server, consulte [interoperabilidade com o Windows Server](storage-sync-files-planning.md#windows-file-server-considerations).
* O módulo Az PowerShell pode ser utilizado com powerShell 5.1 ou PowerShell 6+. Pode utilizar o módulo Az PowerShell para o Azure File Sync em qualquer sistema suportado, incluindo sistemas não Windows, no entanto o cmdlet de registo do servidor deve ser sempre executado na instância do Servidor do Windows que está a registar (isto pode ser feito diretamente ou via PowerShell remoting). No Windows Server 2012 R2, pode verificar se está a executar pelo menos o PowerShell 5.1.\* olhando para o valor da propriedade **PSVersion** **do** $PSVersionTable objeto:

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se o seu valor DE PSVersion for inferior a 5.1.\*, como será o caso da maioria das instalações recentes do Windows Server 2012 R2, pode facilmente fazer o upgrade descarregando e instalando o [Windows Management Framework (WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para descarregar e instalar para o Windows Server 2012 R2 é **Win8.1AndW2K12R2-KB\*\*\*\*\*\*\*-x64.msu**. 

    O PowerShell 6+ pode ser utilizado com qualquer sistema suportado e pode ser descarregado através da sua [página GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se planeia utilizar a UI de Registo do Servidor, em vez de se registar diretamente a partir do PowerShell, tem de utilizar o PowerShell 5.1.

* Se optou por utilizar o PowerShell 5.1, certifique-se de que está instalado pelo menos .NET 4.7.2. Saiba mais sobre [versões e dependências .NET Framework](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) no seu sistema.

    > [!Important]  
    > Se estiver a instalar .NET 4.7.2+ no Núcleo do Servidor do Windows, tem de instalar com as bandeiras `quiet` e `norestart` ou a instalação falhará. Por exemplo, se instalar .NET 4.8, o comando seria o seguinte:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

* O módulo Az PowerShell, que pode ser instalado seguindo as instruções aqui: [Instale e configure o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > O módulo Az.StorageSync está agora instalado automaticamente quando instala o módulo Az PowerShell.

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar o Windows Server para ser utilizado com o Azure File Sync
Para cada servidor que pretende utilizar com o Azure File Sync, incluindo cada nó de servidor num Cluster Failover, desative a **configuração de segurança melhorada**do Internet Explorer . Isto é necessário apenas para o registo inicial do servidor. Pode reativá-la depois de o servidor estar registado.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Pode saltar este passo se estiver a implementar o Sync de Ficheiros Azure no Windows Server Core.

1. Gestor de servidoraberto.
2. Clique no **Servidor Local:**  
    !["Servidor Local" no lado esquerdo do Gestor de Servidores UI](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. No subpainel **Propriedades**, selecione a ligação para **Configuração de Segurança Avançada do IE**.  
    ![o painel "IE Enhanced Security Configuration" no](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG) do Gestor de Servidores
4. Na caixa de diálogo **de configuração de segurança melhorada** do Internet Explorer, selecione **Off** para **Administradores** e **Utilizadores:**  
    ![A janela pop de configuração de segurança melhorada do Internet Explorer com "Off" selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para desativar a configuração de segurança melhorada do Internet Explorer, execute o seguinte de uma sessão elevada powerShell:

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
A implementação do Azure File Sync começa com a colocação de um recurso **do Serviço de Sincronização** de Armazenamento num grupo de recursos da sua subscrição selecionada. Recomendamos o fornecimento de tão poucos destes quanto necessário. Criará uma relação de confiança entre os seus servidores e este recurso e um servidor só pode ser registado num Serviço de Sincronização de Armazenamento. Como resultado, recomenda-se a implementação de todos os serviços de sincronização de armazenamento que necessitar para separar grupos de servidores. Tenha em mente que servidores de diferentes serviços de sincronização de armazenamento não podem sincronizar uns com os outros.

> [!Note]
> O Serviço de Sincronização de Armazenamento herda permissões de acesso do grupo de subscrição e recursos em que foi implantado. Recomendamos que verifique cuidadosamente quem tem acesso a ele. As entidades com acesso por escrito podem começar a sincronizar novos conjuntos de ficheiros a partir de servidores registados neste serviço de sincronização de armazenamento e fazer com que os dados fluam para o armazenamento do Azure que lhes seja acessível.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para implementar um Serviço de Sincronização de Armazenamento, vá ao [portal Azure,](https://portal.azure.com/)clique em *Criar um recurso* e, em seguida, procurar por Azure File Sync. Nos resultados da pesquisa, selecione O Sincronizado de **Ficheiros Azure**e, em seguida, selecione **Criar** para abrir o **separador 'Sincronização de Armazenamento'.**

No painel que se abre, introduza as informações seguintes:

- **Nome**: Um nome único (por subscrição) para o Serviço de Sincronização de Armazenamento.
- **Subscrição**: A subscrição na qual pretende criar o Serviço de Sincronização de Armazenamento. Dependendo da estratégia de configuração da sua organização, poderá ter acesso a uma ou mais subscrições. Uma subscrição Azure é o recipiente mais básico para faturação para cada serviço na nuvem (como O Azure Files).
- **Grupo**de recursos : Um grupo de recursos é um grupo lógico de recursos Azure, como uma conta de armazenamento ou um Serviço de Sincronização de Armazenamento. Pode criar um novo grupo de recursos ou utilizar um grupo de recursos existente para o Azure File Sync. (Recomendamos a utilização de grupos de recursos como contentores para isolar os recursos logicamente para a sua organização, como agrupar recursos de RH ou recursos para um projeto específico.)
- **Localização**: A região em que pretende implantar o Azure File Sync. Só estão disponíveis regiões apoiadas nesta lista.

Quando terminar, selecione **Criar** para implementar o Serviço de Sincronização de Armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Substitua `<Az_Region>`, `<RG_Name>`e `<my_storage_sync_service>` com os seus próprios valores, em seguida, use os seguintes comandos para criar e implementar um Serviço de Sincronização de Armazenamento:

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

# <a name="portal"></a>[Portal](#tab/azure-portal)
Pode descarregar o agente do [Microsoft Download Center](https://go.microsoft.com/fwlink/?linkid=858257). Quando o download estiver concluído, clique duas vezes no pacote MSI para iniciar a instalação do agente Azure File Sync.

> [!Important]  
> Se pretender utilizar o Azure File Sync com um Cluster Failover, o agente Dessincronização de Ficheiros Azure deve ser instalado em todos os nós do cluster. Cada nó do cluster deve estar registado para trabalhar com o Azure File Sync.

Recomendamos que faça o seguinte:
- Deixe o caminho de instalação predefinido (C:\Program Files\Azure\StorageSyncAgent), para simplificar a resolução de problemas e a manutenção do servidor.
- Ativar o Microsoft Update para manter o Ficheiro Sync do Azure atualizado. Todas as atualizações, para o agente Doscão de Ficheiros Azure, incluindo atualizações de funcionalidades e hotfixes, ocorrem a partir do Microsoft Update. Recomendamos a instalação da última atualização para o Azure File Sync. Para mais informações, consulte a política de [atualização do Ficheiro Azure](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente Dessincronização de ficheiros Azure estiver terminada, a UI de Registo do Servidor abre automaticamente. Deve ter um Serviço de Sincronização de Armazenamento antes de se registar; ver a próxima secção sobre como criar um Serviço de Sincronização de Armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Execute o seguinte código PowerShell para descarregar a versão apropriada do agente Doscão de Ficheiros Azure para o seu SISTEMA e instale-o no seu sistema.

> [!Important]  
> Se pretender utilizar o Azure File Sync com um Cluster Failover, o agente Dessincronização de Ficheiros Azure deve ser instalado em todos os nós do cluster. Cada nó do cluster deve estar registado para trabalhar com o Azure File Sync.

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
> O registo do servidor utiliza as suas credenciais Azure para criar uma relação de confiança entre o Serviço de Sincronização de Armazenamento e o seu Servidor Windows, no entanto, posteriormente, o servidor cria e utiliza a sua própria identidade que é válida desde que o servidor permaneça registado e o atual ficha de assinatura de acesso partilhado (Storage SAS) é válida. Um novo token SAS não pode ser emitido para o servidor uma vez que o servidor não esteja registado, removendo assim a capacidade do servidor de aceder às suas partilhas de ficheiros Azure, impedindo qualquer sincronização.

# <a name="portal"></a>[Portal](#tab/azure-portal)
O UI de Registo do Servidor deve ser aberto automaticamente após a instalação do agente Dessincronização de ficheiros Azure. Se isso não acontecer, pode abri-la manualmente na localização do ficheiro, em C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quando o UI de Registo do Servidor abrir, selecione Iniciar o **'Iniciar' o 'Iniciar' o 'Iniciar' o 'Sign-in'.**

Depois de iniciar sessão, é solicitado para obter as seguintes informações:

![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Assinatura Azure**: A subscrição que contém o Serviço de Sincronização de Armazenamento (ver [Implementar o Serviço de Sincronização de Armazenamento).](#deploy-the-storage-sync-service) 
- **Grupo de Recursos**: O grupo de recursos que contém o Serviço de Sincronização de Armazenamento.
- **Serviço de Sincronização**de Armazenamento : O nome do Serviço de Sincronização de Armazenamento com o qual pretende registar-se.

Depois de ter selecionado as informações apropriadas, selecione **Registar** para completar o registo do servidor. Como parte do processo de registo, é-lhe pedido que volte a iniciar sessão novamente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Criar um grupo de sincronização e um ponto final na cloud
Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Os grupos de sincronização têm de conter um ponto final da cloud, que representa uma partilha de ficheiros do Azure e um ou mais pontos finais de servidor. Um ponto final do servidor representa um caminho num servidor registado. Um servidor pode ter pontos finais do servidor em vários grupos de sincronização. Pode criar o número de grupos de sincronização que precisar para descrever adequadamente a sua topologia de sincronização desejada.

Um ponto final em nuvem é um ponteiro para uma partilha de ficheiros Azure. Todos os pontos finais do servidor sincronizar-se-ão com um ponto final de nuvem, fazendo com que o ponto final da nuvem seja o hub. A conta de armazenamento da parte do ficheiro Azure deve estar localizada na mesma região que o Serviço de Sincronização de Armazenamento. A totalidade da parte de ficheiro Azure será sincronizada, com uma exceção: será disponibilizada uma pasta especial, comparável à pasta "Informação de Volume do Sistema" escondida num volume NTFS. Este diretório chama-se ". SystemShareInformation". Contém importantes metadados de sincronização que não sincronizam com outros pontos finais. Não utilize nem elimine!

> [!Important]  
> Pode efetuar alterações em qualquer ponto final de nuvem ou ponto final do servidor no grupo de sincronização e ter os seus ficheiros sincronizados com os outros pontos finais do grupo de sincronização. Se fizer uma alteração direta mente no ponto final da nuvem (partilha de ficheiros Azure), as alterações têm primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de mudanças é iniciado para um ponto final de nuvem apenas uma vez a cada 24 horas. Para mais informações, consulte [os Ficheiros Azure frequentemente questionados](storage-files-faq.md#afs-change-detection).

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar um grupo de sincronização, no [portal Azure,](https://portal.azure.com/)vá ao seu Serviço de Sincronização de Armazenamento e, em seguida, selecione **+ Sync group**:

![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel que se abre, introduza as informações seguintes para criar um grupo de sincronização com um ponto final da cloud:

- **Nome do grupo sync**: O nome do grupo de sincronização a criar. Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico.
- **Subscrição**: A subscrição onde implementou o Serviço de Sincronização de Armazenamento na [Implementação do Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: Se selecionar a conta de **armazenamento Select,** aparece outro painel no qual pode selecionar a conta de armazenamento que tem a partilha de ficheiros Azure com a qual pretende sincronizar.
- **Partilha de ficheiros Azure**: O nome da partilha de ficheiros Azure com o qual pretende sincronizar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar o grupo de sincronização, execute o seguinte PowerShell. Lembre-se de substituir `<my-sync-group>` pelo nome desejado do grupo de sincronização.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Uma vez criado o grupo de sincronização com sucesso, pode criar o seu ponto final em nuvem. Certifique-se de substituir `<my-storage-account>` e `<my-file-share>` com os valores esperados.

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
Os pontos finais de servidor representam uma localização específica num servidor registado, como uma pasta num volume do servidor. Um ponto final do servidor deve ser um caminho num servidor registado (em vez de uma parte montada), e para utilizar o tiering em nuvem, o caminho deve estar num volume não-sistema. O armazenamento ligado à rede (NAS) não é suportado.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para adicionar um ponto final do servidor, vá ao grupo de sincronização recém-criado e, em seguida, selecione **Adicionar ponto final**do servidor .

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No painel **Adicionar ponto final de servidor**, introduza as informações seguintes para criar um ponto final de servidor:

- **Servidor registado**: O nome do servidor ou cluster onde pretende criar o ponto final do servidor.
- **Caminho**: O caminho do Windows Server a sincronizar como parte do grupo de sincronização.
- **Cloud Tiering**: Um interruptor para ativar ou desativar o tiering da nuvem. Com o tiering em nuvem, ficheiros pouco utilizados ou acedidos podem ser nidificados para Ficheiros Azure.
- **Espaço Livre**de Volume : A quantidade de espaço livre para reservar no volume em que se encontra o ponto final do servidor. Por exemplo, se o espaço livre de volume for definido para 50% num volume que tem um único ponto final do servidor, cerca de metade da quantidade de dados é nivida para O Ficheiro Sino. Independentemente de o tiering em nuvem estar ativado, a sua quota de ficheiroS Azure tem sempre uma cópia completa dos dados do grupo de sincronização.

Para adicionar o ponto final do servidor, selecione **Criar**. Os seus ficheiros são agora mantidos em sincronização através da sua partilha de ficheiros Azure e do Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Execute os seguintes comandos PowerShell para criar o ponto final do servidor e certifique-se de substituir `<your-server-endpoint-path>` e `<your-volume-free-space>` com os valores desejados.

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

## <a name="configure-firewall-and-virtual-network-settings"></a>Configure as definições de firewall e rede virtual

### <a name="portal"></a>Portal
Se quiser configurar a sincronização do Seu Ficheiro Azure para funcionar com definições de firewall e rede virtual, faça o seguinte:

1. A partir do portal Azure, navegue até à conta de armazenamento que pretende proteger.
1. Selecione o botão **Firewalls e redes virtuais** no menu esquerdo.
1. Selecione **redes selecionadas** sob **permitir o acesso a partir de**.
1. Certifique-se de que os seus servidores IP ou rede virtual estão listados na secção apropriada.
1. Certifique-se de **que os serviços fidedignos da Microsoft acedem a esta conta de armazenamento.**
1. Selecione **Guardar** para guardar as suas definições.

![Configurar definições de firewall e rede virtual para funcionar com sincronização de Ficheiros Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Embarque com Sincronização de Ficheiros Azure
As etapas recomendadas para embarcar no Azure File Sync para o primeiro com zero tempo de inatividade, preservando a fidelidade total do ficheiro e a lista de controlo de acesso (ACL) são as seguintes:
 
1. Implemente um Serviço de Sincronização de Armazenamento.
2. Crie um grupo de sincronização.
3. Instale o agente Dessincronização de ficheiros Azure no servidor com o conjunto de dados completo.
4. Registe esse servidor e crie um ponto final do servidor na parte. 
5. Deixe sincronizar o upload completo para a partilha de ficheiros Azure (cloud endpoint).  
6. Depois de concluída a carga inicial, instale o agente Dessincronização do Ficheiro Azure em cada um dos restantes servidores.
7. Crie novas partilhas de ficheiros em cada um dos restantes servidores.
8. Crie pontos finais do servidor em novas partilhas de ficheiros com a política de tiering de nuvem, se desejar. (Este passo requer armazenamento adicional para a configuração inicial.)
9. Deixe o agente Azure File Sync fazer uma rápida restauração do espaço completo sem a transferência de dados reais. Após a sincronização completa do espaço de nome, o motor sincronizado preencherá o espaço do disco local com base na política de tiering de nuvem para o ponto final do servidor. 
10. Certifique-se de que a sincronização completa e teste a sua topologia conforme desejado. 
11. Redirecione os utilizadores e aplicações para esta nova partilha.
12. Pode eliminar opcionalmente quaisquer partilhas duplicadas nos servidores.
 
Se não tiver armazenamento extra para o embarque inicial e quiser anexar-se às ações existentes, pode pré-semente os dados nas partilhas dos ficheiros Azure. Esta abordagem é sugerida, se e apenas se puder aceitar o tempo de inatividade e garantir absolutamente nenhuma alteração de dados nas partilhas do servidor durante o processo inicial de embarque. 
 
1. Certifique-se de que os dados de qualquer um dos servidores não podem ser alterados durante o processo de embarque.
2. O ficheiro Azure pré-semente partilha com os dados do servidor usando qualquer ferramenta de transferência de dados sobre o SMB, por exemplo, Robocopy, cópia SMB direta. Uma vez que o AzCopy não faz o upload de dados sobre o SMB, pelo que não pode ser utilizado para pré-semente.
3. Crie topologia de sincronização de ficheiros Azure com os pontos finais do servidor desejados apontando para as ações existentes.
4. Deixe sincronizar o processo de reconciliação em todos os pontos finais. 
5. Uma vez concluída a reconciliação, pode abrir ações para alterações.
 
Atualmente, a abordagem pré-sementeing tem algumas limitações - 
- A fidelidade total nos ficheiros não é preservada. Por exemplo, os ficheiros perdem ACLs e selos temporais.
- As alterações de dados no servidor antes da topologia de sincronização estar totalmente em funcionamento podem causar conflitos nos pontos finais do servidor.  
- Após a criação do ponto final da nuvem, o Azure File Sync executa um processo para detetar os ficheiros na nuvem antes de iniciar a sincronização inicial. O tempo de conclusão deste processo varia consoante os vários fatores, como a velocidade de rede, a largura de banda disponível e o número de ficheiros e pastas. Para a estimativa aproximada no lançamento de pré-visualização, o processo de deteção é executado aproximadamente a 10 ficheiros/seg.  Assim, mesmo que a pré-sementeira seja rápida, o tempo total para obter um sistema de funcionamento completo pode ser significativamente mais longo quando os dados são pré-semeados na nuvem.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Restauro de self-service através de versões anteriores e VSS (Serviço de Cópia de Sombra de Volume)

> [!IMPORTANT]
> As seguintes informações só podem ser utilizadas com a versão 9 (ou superior) do agente de sincronização de armazenamento. Versões inferiores a 9 não terão os cmdlets StorageSyncSelfService.

Versões anteriores é uma funcionalidade do Windows que permite utilizar instantâneos VSS do lado do servidor de um volume para apresentar versões restoráveis de um ficheiro a um cliente SMB.
Isto permite um cenário poderoso, vulgarmente referido como restauração de self-service, diretamente para os trabalhadores da informação em vez de depender da restauração de um administrador de TI.

Os instantâneos VSS e as versões anteriores funcionam independentemente do Sync de Ficheiros Azure. No entanto, o tiering em nuvem deve ser definido para um modo compatível. Muitos pontos finais do servidor Do ficheiro Azure Sync podem existir no mesmo volume. Tem de fazer a seguinte chamada PowerShell por volume que tenha até um ponto final do servidor onde planeia ou está a utilizar o tiering da nuvem.

```powershell
Import-Module ‘<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll’
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

As fotos vss são tiradas de um volume inteiro. Por padrão, até 64 instantâneos podem existir para um determinado volume, dado que há espaço suficiente para armazenar as imagens. O VSS trata-o automaticamente. O horário de instantâneo padrão leva duas fotos por dia, de segunda a sexta-feira. Esta programação é configurável através de uma Tarefa Agendada para o Windows. O cmdlet de PowerShell acima faz duas coisas:
1. Configura o tiering de nuvem do Azure File Syncs no volume especificado para ser compatível com versões anteriores e garante que um ficheiro pode ser restaurado a partir de uma versão anterior, mesmo que tenha sido nivelado para a nuvem no servidor. 
2. Ativa o horário vss predefinido. Em seguida, pode decidir modificá-lo mais tarde. 

> [!Note]  
> Há duas coisas importantes a notar:
>- Se utilizar o parâmetro -Força e o VSS estiver ativado, então irá substituir o calendário instantâneo VSS atual e substituí-lo pelo horário predefinido. Certifique-se de que guarda a configuração personalizada antes de executar o cmdlet.
> - Se estiver a usar este cmdlet num nó de cluster, também deve executá-lo em todos os outros nós do cluster! 

Para ver se a compatibilidade de restauração de self-service está ativada, pode executar o seguinte cmdlet.

```powershell
    Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Ele listará todos os volumes no servidor, bem como o número de dias compatíveis com o tiering em nuvem para cada um. Este número é automaticamente calculado com base nas imagens máximas possíveis por volume e no calendário de instantâneos predefinido. Assim, por padrão, todas as versões anteriores apresentadas a um trabalhador da informação podem ser usadas para restaurar a partir de. O mesmo acontece se alterar o horário padrão para tirar mais fotos.
No entanto, se alterar o horário de uma forma que resulte num instantâneo disponível no volume mais antigo do que o valor dos dias compatíveis, então os utilizadores não poderão utilizar este instantâneo mais antigo (versão anterior) para restaurar.

> [!Note]
> Permitir a restauração do self-service pode ter um impacto no seu consumo de armazenamento e fatura do Seu Azure. Este impacto limita-se aos ficheiros atualmente nidificados no servidor. Ativar esta funcionalidade garante que existe uma versão de ficheiro disponível na nuvem que pode ser referenciada através de uma entrada de versões anteriores (INSTANTÂNEO VSS).
>
> Se desativar a funcionalidade, o consumo de armazenamento Azure diminuirá lentamente até que a janela de dias compatível tenha passado. Não há como acelerar isto. 

O número máximo de instantâneos VSS por volume (64), bem como o horário padrão para os tomar, resultam num máximo de 45 dias de versões anteriores de que um trabalhador da informação pode restaurar, dependendo de quantas fotografias VSS pode armazenar no seu volume.

Se for no máximo. 64 Instantâneos VSS por volume não são a definição correta para si, pode [alterar esse valor através](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies)de uma chave de registo .
Para que o novo limite entre em vigor, é necessário reexecutar o cmdlet para permitir a compatibilidade da versão anterior em todos os volumes anteriores previamente ativados, com a bandeira -Force a ter em conta o novo número máximo de instantâneos VSS por volume. Isto resultará num número recentemente calculado de dias compatíveis. Por favor, note que esta alteração só produzirá efeitos em ficheiros recém-tiered e substitui quaisquer personalizações na programação VSS que possa ter feito.

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrar uma implementação de replicação DFS (DFS-R) para o Azure File Sync
Para migrar uma implantação DFS-R para o Azure File Sync:

1. Crie um grupo de sincronização para representar a topologia DFS-R que está a substituir.
2. Comece no servidor que tem o conjunto completo de dados na sua topologia DFS-R para migrar. Instale o Sync de Ficheiros Azure nesse servidor.
3. Registe esse servidor e crie um ponto final do servidor para o primeiro servidor a migrar. Não permita o tiering da nuvem.
4. Deixe todos os dados sincronizarem a sua partilha de ficheiros Azure (cloud endpoint).
5. Instale e registe o agente Dessincronização de ficheiros Azure em cada um dos restantes servidores DFS-R.
6. Desativar dfs-R. 
7. Crie um ponto final do servidor em cada um dos servidores DFS-R. Não permita o tiering da nuvem.
8. Certifique-se de que a sincronização completa e teste a sua topologia conforme desejado.
9. Aposentado DFS-R.
10. O tiering de nuvem pode agora ser ativado em qualquer ponto final do servidor, conforme desejado.

Para mais informações, consulte [O Ficheiro Sincronizado azure interop com sistema de ficheiros distribuídos (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Passos seguintes
- [Adicione ou remova um ponto final do servidor de sincronização de ficheiros Azure](storage-sync-files-server-endpoint.md)
- [Registe ou desregilhe um servidor com O Sincronizado de Ficheiros Azure](storage-sync-files-server-registration.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
