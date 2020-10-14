---
title: Implementar Azure File Sync / Microsoft Docs
description: Saiba como implementar o Azure File Sync, do início ao fim, utilizando o portal Azure, o PowerShell ou o Azure CLI.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 10/14/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 012b5c76a025e6dc6ae1fbd5aedddf9ea3d2a4f0
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057829"
---
# <a name="deploy-azure-file-sync"></a>Implementar Azure File Sync
Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos dados localmente, incluindo SMB, NFS e FTPS. Podes ter o número de caches que precisares em todo o mundo.

Recomendamos vivamente que leia Planeamento para uma implementação e Planeamento de [Ficheiros Azure](storage-files-planning.md) [antes de](storage-sync-files-planning.md) completar os passos descritos neste artigo.

## <a name="prerequisites"></a>Pré-requisitos

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Uma partilha de ficheiros Azure na mesma região que pretende implementar O Azure File Sync. Para mais informações, consulte:
    - [Disponibilidade da região](storage-sync-files-planning.md#azure-file-sync-region-availability) para Azure File Sync.
    - [Crie uma partilha](storage-how-to-create-file-share.md) de ficheiros para uma descrição passo a passo de como criar uma partilha de ficheiros.
1. Pelo menos uma instância suportada do Windows Server ou do Windows Server para sincronizar com o Azure File Sync. Para obter mais informações sobre versões suportadas do Windows Server e recursos de sistema [recomendados, consulte as considerações do servidor de ficheiros do Windows](storage-sync-files-planning.md#windows-file-server-considerations).

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Uma partilha de ficheiros Azure na mesma região que pretende implementar O Azure File Sync. Para mais informações, consulte:
    - [Disponibilidade da região](storage-sync-files-planning.md#azure-file-sync-region-availability) para Azure File Sync.
    - [Crie uma partilha](storage-how-to-create-file-share.md) de ficheiros para uma descrição passo a passo de como criar uma partilha de ficheiros.
1. Pelo menos uma instância suportada do Windows Server ou do Windows Server para sincronizar com o Azure File Sync. Para obter mais informações sobre versões suportadas do Windows Server e recursos de sistema [recomendados, consulte as considerações do servidor de ficheiros do Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. O módulo Az PowerShell pode ser utilizado com o PowerShell 5.1 ou o PowerShell 6+. Pode utilizar o módulo Az PowerShell para Azure File Sync em qualquer sistema suportado, incluindo sistemas não Windows, no entanto o cmdlet de registo do servidor deve ser sempre executado na instância do Windows Server que está a registar (isto pode ser feito diretamente ou através do remoamento powerShell). No Windows Server 2012 R2, pode verificar se está a executar pelo menos o PowerShell 5.1. \* olhando para o valor da propriedade **PSVersion** do objeto **$PSVersionTable:**

    ```powershell
    $PSVersionTable.PSVersion
    ```

    Se o seu valor **de PSVersion** for inferior a 5.1, \* como será o caso da maioria das instalações recentes do Windows Server 2012 R2, pode facilmente atualizar-se descarregando e instalando o Windows Management Framework [(WMF) 5.1](https://www.microsoft.com/download/details.aspx?id=54616). O pacote apropriado para descarregar e instalar para o Windows Server 2012 R2 é **Win8.1AndW2K12R2-KB \* \* \* \* \* \* \* -x64.msu**. 

    PowerShell 6+ pode ser utilizado com qualquer sistema suportado, e pode ser descarregado através da sua [página GitHub](https://github.com/PowerShell/PowerShell#get-powershell). 

    > [!Important]  
    > Se planeia utilizar a UI de Registo do Servidor, em vez de se registar diretamente a partir do PowerShell, tem de utilizar o PowerShell 5.1.

1. Se tiver optado por utilizar o PowerShell 5.1, certifique-se de que está instalado pelo menos .NET 4.7.2. Saiba mais sobre [as versões e dependências do quadro .NET](https://docs.microsoft.com/dotnet/framework/migration-guide/versions-and-dependencies) no seu sistema.

    > [!Important]  
    > Se estiver a instalar .NET 4.7.2+ no Windows Server Core, tem de ser instalado com as `quiet` bandeiras e `norestart` a instalação falhará. Por exemplo, se instalar .NET 4.8, o comando será semelhante ao seguinte:
    > ```PowerShell
    > Start-Process -FilePath "ndp48-x86-x64-allos-enu.exe" -ArgumentList "/q /norestart" -Wait
    > ```

1. O módulo Az PowerShell, que pode ser instalado seguindo as instruções aqui: [Instale e configuure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).
     
    > [!Note]  
    > O módulo Az.StorageSync é agora instalado automaticamente quando instalar o módulo Az PowerShell.

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

1. Uma partilha de ficheiros Azure na mesma região que pretende implementar O Azure File Sync. Para mais informações, consulte:
    - [Disponibilidade da região](storage-sync-files-planning.md#azure-file-sync-region-availability) para Azure File Sync.
    - [Crie uma partilha](storage-how-to-create-file-share.md) de ficheiros para uma descrição passo a passo de como criar uma partilha de ficheiros.
1. Pelo menos uma instância suportada do Windows Server ou do Windows Server para sincronizar com o Azure File Sync. Para obter mais informações sobre versões suportadas do Windows Server e recursos de sistema [recomendados, consulte as considerações do servidor de ficheiros do Windows](storage-sync-files-planning.md#windows-file-server-considerations).

1. [Instalar a CLI do Azure](/cli/azure/install-azure-cli)

   Se preferir, também pode usar a Azure Cloud Shell para completar os passos neste tutorial.  Azure Cloud Shell é um ambiente de conchas interativas que utiliza através do seu navegador.  Inicie a Cloud Shell utilizando um destes métodos:

   - Selecione **Experimentar** no canto superior direito de um bloco de código. **Experimente** abrir o Azure Cloud Shell, mas não copia automaticamente o código para cloud Shell.

   - Open Cloud Shell indo para [https://shell.azure.com](https://shell.azure.com)

   - Selecione o botão **Cloud Shell** na barra de menu no canto superior direito no [portal Azure](https://portal.azure.com)

1. Inicie sessão.

   Inicie sessão com o comando [az login](/cli/azure/reference-index#az-login) se estiver a utilizar uma instalação local da CLI.

   ```azurecli
   az login
   ```

    Siga os passos apresentados no seu terminal para concluir o processo de autenticação.

1. Instale a extensão [Az Filesync](/cli/azure/ext/storagesync/storagesync) Azure CLI.

   ```azurecli
   az extension add --name storagesync
   ```

   Depois de instalar a referência de extensão **storagesync,** receberá o seguinte aviso.

   ```output
   The installed extension 'storagesync' is experimental and not covered by customer support. Please use with discretion.
   ```

---

## <a name="prepare-windows-server-to-use-with-azure-file-sync"></a>Preparar o Windows Server para ser utilizado com o Azure File Sync
Para cada servidor que pretende utilizar com Azure File Sync, incluindo cada nó de servidor num Cluster failover, desative a **configuração de segurança melhorada do Internet Explorer .** Isto é necessário apenas para o registo inicial do servidor. Pode reativá-la depois de o servidor estar registado.

# <a name="portal"></a>[Portal](#tab/azure-portal)
> [!Note]  
> Pode saltar este passo se estiver a implementar o Azure File Sync no Núcleo do Servidor do Windows.

1. Abra o Gestor de Servidores.
2. Clique **no Servidor Local:**  
    ![“Servidor Local” no lado esquerdo da IU do Gestor de Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-1.PNG)
3. No subpainel **Propriedades**, selecione a ligação para **Configuração de Segurança Avançada do IE**.  
    ![Painel “Configuração de Segurança Avançada do IE”, na IU do Gestor de Servidor](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-2.PNG)
4. Na caixa de diálogo **de configuração de segurança melhorada** do Internet Explorer, selecione **Off** for **Administrators** and **Users**:  
    ![Janela pop-up da Configuração de Segurança Avançada do Internet Explorer com “Desativado” selecionado](media/storage-sync-files-deployment-guide/prepare-server-disable-IEESC-3.png)

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para desativar a configuração de segurança melhorada do Internet Explorer, execute o seguinte a partir de uma sessão PowerShell elevada:

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal Azure ou PowerShell.

---

## <a name="deploy-the-storage-sync-service"></a>Implementar o Serviço de Sincronização de Armazenamento 
A implementação do Azure File Sync começa por colocar um recurso **de Serviço de Sincronização de Armazenamento** num grupo de recursos da sua subscrição selecionada. Recomendamos o provisionamento de alguns destes, se necessário. Criará uma relação de confiança entre os seus servidores e este recurso e um servidor só podem ser registados num Serviço de Sincronização de Armazenamento. Como resultado, é aconselhável implementar tantos serviços de sincronização de armazenamento como necessário para separar grupos de servidores. Tenha em mente que servidores de diferentes serviços de sincronização de armazenamento não podem sincronizar-se entre si.

> [!Note]
> O Serviço de Sincronização de Armazenamento herda permissões de acesso do grupo de subscrição e recursos em que foi implantado. Recomendamos que verifique cuidadosamente quem tem acesso a ele. As entidades com acesso por escrito podem começar a sincronizar novos conjuntos de ficheiros de servidores registados neste serviço de sincronização de armazenamento e fazer com que os dados fluam para o armazenamento Azure que lhes seja acessível.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para implementar um Serviço de Sincronização de Armazenamento, vá ao [portal Azure,](https://portal.azure.com/)clique em *Criar um recurso* e, em seguida, procure por Azure File Sync. Nos resultados da pesquisa, selecione **Azure File Sync**e, em seguida, selecione **Criar** para abrir o **separador Sync de Implementação.**

No painel que se abre, introduza as informações seguintes:

- **Nome**: Nome único (por região) para o Serviço de Sincronização de Armazenamento.
- **Subscrição**: A subscrição na qual pretende criar o Serviço de Sincronização de Armazenamento. Dependendo da estratégia de configuração da sua organização, poderá ter acesso a uma ou mais subscrições. Uma subscrição Azure é o recipiente mais básico para faturação para cada serviço em nuvem (como ficheiros Azure).
- **Grupo de recursos**: Um grupo de recursos é um grupo lógico de recursos Azure, como uma conta de armazenamento ou um Serviço de Sincronização de Armazenamento. Pode criar um novo grupo de recursos ou utilizar um grupo de recursos existente para o Azure File Sync. (Recomendamos a utilização de grupos de recursos como contentores para isolar os recursos logicamente para a sua organização, como agrupar recursos de RH ou recursos para um projeto específico.)
- **Localização**: A região em que pretende implantar O Azure File Sync. Só estão disponíveis nesta lista regiões apoiadas.

Quando terminar, selecione **Criar** para implementar o Serviço de Sincronização de Armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Substitua `<Az_Region>` , e pelos seus `<RG_Name>` `<my_storage_sync_service>` próprios valores, em seguida, use os seguintes comandos para criar e implementar um Serviço de Sincronização de Armazenamento:

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal Azure ou PowerShell.

---

## <a name="install-the-azure-file-sync-agent"></a>Instalar o agente do Azure File Sync
O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure. 

# <a name="portal"></a>[Portal](#tab/azure-portal)
Pode descarregar o agente do [Microsoft Download Center.](https://go.microsoft.com/fwlink/?linkid=858257) Quando o download estiver terminado, clique duas vezes no pacote MSI para iniciar a instalação do agente Azure File Sync.

> [!Important]  
> Se pretender utilizar o Azure File Sync com um Cluster Failover, o agente Azure File Sync deve ser instalado em todos os nós do cluster. Cada nó no cluster deve estar registado para funcionar com o Azure File Sync.

Recomendamos que faça o seguinte:
- Deixe o caminho de instalação predefinido (C:\Program Files\Azure\StorageSyncAgent), para simplificar a resolução de problemas e a manutenção do servidor.
- Ativar o Microsoft Update para manter o Azure File Sync atualizado. Todas as atualizações, para o agente Azure File Sync, incluindo atualizações de funcionalidades e hotfixes, ocorrem a partir do Microsoft Update. Recomendamos a instalação da última atualização para O Azure File Sync. Para obter mais informações, consulte [a política de atualização do Azure File Sync](storage-sync-files-planning.md#azure-file-sync-agent-update-policy).

Quando a instalação do agente Azure File Sync estiver concluída, o UI de registo do servidor abre automaticamente. Tem de ter um Serviço de Sincronização de Armazenamento antes de se registar; consulte a secção seguinte sobre como criar um Serviço de Sincronização de Armazenamento.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Execute o seguinte código PowerShell para descarregar a versão apropriada do agente Azure File Sync para o seu SISTEMA e instalá-lo no seu sistema.

> [!Important]  
> Se pretender utilizar o Azure File Sync com um Cluster Failover, o agente Azure File Sync deve ser instalado em todos os nós do cluster. Cada nó no cluster deve estar registado para trabalhar com o Azure File Sync.

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
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal Azure ou PowerShell.

---

## <a name="register-windows-server-with-storage-sync-service"></a>Registar o Windows Server no Serviço de Sincronização de Armazenamento
Registar o Windows Server num Serviço de Sincronização de Armazenamento estabelece uma relação de confiança entre o servidor (ou cluster) e aquele serviço. Os servidores só podem ser registados num Serviço de Sincronização de Armazenamento e podem ser sincronizados com outros servidores e com partilhas de ficheiros do Azure associados ao mesmo Serviço de Sincronização de Armazenamento.

> [!Note]
> O registo do servidor utiliza as suas credenciais Azure para criar uma relação de confiança entre o Serviço de Sincronização de Armazenamento e o seu Servidor Do Windows, no entanto, posteriormente, o servidor cria e utiliza a sua própria identidade que é válida desde que o servidor permaneça registado e o token de assinatura de acesso partilhado atual (Storage SAS) seja válido. Um novo token SAS não pode ser emitido para o servidor uma vez que o servidor não está registado, removendo assim a capacidade do servidor de aceder às suas ações de ficheiros Azure, impedindo qualquer sincronização.

O administrador que regista o servidor deve ser membro das funções de gestão **Proprietário** ou **Contribuinte** para o serviço de sincronização de armazenamento dado. Isto pode ser configurado no Controlo **de Acesso (IAM)** no portal Azure para o Serviço de Sincronização de Armazenamento.

Também é possível diferenciar os administradores capazes de registar servidores daqueles que também são autorizados a configurar a sincronização num Serviço de Sincronização de Armazenamento. Para isso, você precisaria de criar uma função personalizada onde lista os administradores que só podem registar servidores e dar à sua função personalizada as seguintes permissões:

* "Microsoft.StorageSync/storageSyncServices/registeredServers/write"
* "Microsoft.StorageSync/storageSyncServices/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/read"
* "Microsoft.StorageSync/storageSyncServices/workflows/operations/read"

# <a name="portal"></a>[Portal](#tab/azure-portal)
O UI de Registo do Servidor deve abrir-se automaticamente após a instalação do agente Azure File Sync. Se isso não acontecer, pode abri-la manualmente na localização do ficheiro, em C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe. Quando o UI de Registo do Servidor abrir, selecione Iniciar o início do Início do Início do **Início.**

Depois de iniciar sedutado, é solicitado para as seguintes informações:

![Uma captura de ecrã da IU do Registo do Servidor](media/storage-sync-files-deployment-guide/register-server-scubed-1.png)

- **Assinatura Azure**: A subscrição que contém o Serviço de Sincronização de Armazenamento (ver [Implementar o Serviço de Sincronização de Armazenamento).](#deploy-the-storage-sync-service) 
- **Grupo de Recursos**: O grupo de recursos que contém o Serviço de Sincronização de Armazenamento.
- **Serviço de Sincronização**de Armazenamento : O nome do Serviço de Sincronização de Armazenamento com o qual pretende registar-se.

Depois de ter selecionado as informações apropriadas, **selecione Registar-se** para completar o registo do servidor. Como parte do processo de registo, é-lhe pedido que volte a iniciar sessão novamente.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
```powershell
$registeredServer = Register-AzStorageSyncServer -ParentObject $storageSync
```
# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Siga as instruções para o portal Azure ou PowerShell.

---

## <a name="create-a-sync-group-and-a-cloud-endpoint"></a>Criar um grupo de sincronização e um ponto final na cloud
Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Os grupos de sincronização têm de conter um ponto final da cloud, que representa uma partilha de ficheiros do Azure e um ou mais pontos finais de servidor. Um ponto final do servidor representa um caminho num servidor registado. Um servidor pode ter pontos finais do servidor em vários grupos de sincronização. Pode criar os grupos de sincronização que precisar para descrever adequadamente a sua topologia de sincronização desejada.

Um ponto final em nuvem é um ponteiro para uma partilha de ficheiros Azure. Todos os pontos finais do servidor sincronizarão com um ponto final de nuvem, fazendo com que o ponto final da nuvem seja o hub. A conta de armazenamento da parte de ficheiros Azure deve estar localizada na mesma região que o Serviço de Sincronização de Armazenamento. A totalidade da partilha de ficheiros Azure será sincronizada, com uma exceção: Será acoplada uma pasta especial, comparável à pasta "Informação do Volume do Sistema" escondida num volume NTFS. Este diretório chama-se ". SystemShareInformation". Contém metadados sincronizados importantes que não sincronizam com outros pontos finais. Não o utilize nem elimine!

> [!Important]  
> Pode escoar alterações em qualquer ponto final da nuvem ou ponto final do servidor no grupo de sincronização e ter os seus ficheiros sincronizados com os outros pontos finais do grupo de sincronização. Se fizer uma alteração direta no ponto final da nuvem (partilha de ficheiros Azure), as alterações têm primeiro de ser descobertas por um trabalho de deteção de alteração de ficheiros Azure. Um trabalho de deteção de alterações é iniciado para um ponto final de nuvem apenas uma vez a cada 24 horas. Para obter mais informações, consulte [a Azure Files com frequência a fazer perguntas.](storage-files-faq.md#afs-change-detection)

O administrador que cria o ponto final da nuvem deve ser um membro da função de gestão **Proprietário** para a conta de armazenamento que contém o ficheiro Azure partilhar o ponto final da nuvem está apontando. Isto pode ser configurado no Controlo **de Acesso (IAM)** no portal Azure para a conta de armazenamento.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para criar um grupo de sincronização, no [portal Azure,](https://portal.azure.com/)vá ao seu Serviço de Sincronização de Armazenamento e, em seguida, selecione **+ Sync group**:

![Criar um grupo de sincronização novo no portal do Azure](media/storage-sync-files-deployment-guide/create-sync-group-1.png)

No painel que se abre, introduza as informações seguintes para criar um grupo de sincronização com um ponto final da cloud:

- **Sync nome de grupo**: O nome do grupo de sincronização a criar. Este nome tem de ser exclusivo no Serviço de Sincronização de Armazenamento, mas pode ser qualquer nome que lhe pareça lógico.
- **Subscrição**: A subscrição onde implementou o Serviço de Sincronização de Armazenamento em [Implementar o Serviço de Sincronização de Armazenamento](#deploy-the-storage-sync-service).
- **Conta de armazenamento**: Se **selecionar Selecione a conta de armazenamento,** aparece outro painel no qual pode selecionar a conta de armazenamento que tem a parte de ficheiro Azure com a qual pretende sincronizar.
- **Partilha de ficheiros Azure**: O nome da partilha de ficheiros Azure com a qual pretende sincronizar.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Para criar o grupo de sincronização, execute o seguinte PowerShell. Lembre-se de substituir `<my-sync-group>` pelo nome desejado do grupo de sincronização.

```powershell
$syncGroupName = "<my-sync-group>"
$syncGroup = New-AzStorageSyncGroup -ParentObject $storageSync -Name $syncGroupName
```

Uma vez criado o grupo de sincronização com sucesso, pode criar o seu ponto final de nuvem. Certifique-se de que substitui `<my-storage-account>` e `<my-file-share>` pelos valores esperados.

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

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando [do grupo de sincronização az storagesync](/cli/azure/ext/storagesync/storagesync/sync-group#ext-storagesync-az-storagesync-sync-group-create) para criar um novo grupo de sincronização.  Para desproportuar um grupo de recursos para todos os comandos CLI, utilize [a configuração az](/cli/azure/reference-index#az-configure).

```azurecli
az storagesync sync-group create --resource-group myResourceGroupName \
                                 --name myNewSyncGroupName \
                                 --storage-sync-service myStorageSyncServiceName \
```

Utilize o comando [az storagesync sync-group cloud-endpoint](/cli/azure/ext/storagesync/storagesync/sync-group/cloud-endpoint#ext-storagesync-az-storagesync-sync-group-cloud-endpoint-create) para criar um novo ponto final em nuvem.

```azurecli
az storagesync sync-group cloud-endpoint create --resource-group myResourceGroup \
                                                --storage-sync-service myStorageSyncServiceName \
                                                --sync-group-name mySyncGroupName \
                                                --name myNewCloudEndpointName \
                                                --storage-account mystorageaccountname \
                                                --azure-file-share-name azure-file-share-name
```

---

## <a name="create-a-server-endpoint"></a>Criar um ponto final de servidor
Os pontos finais de servidor representam uma localização específica num servidor registado, como uma pasta num volume do servidor. Um ponto final do servidor deve ser um caminho num servidor registado (em vez de uma partilha montada) e para utilizar o tiering da nuvem, o caminho deve estar num volume não-sistema. O armazenamento ligado à rede (NAS) não é suportado.

> [!NOTE]
> Alterar o caminho ou a letra de unidade depois de ter estabelecido um ponto final do servidor num volume não é suportado. Certifique-se de que está a utilizar um caminho final no seu servidor registado.

# <a name="portal"></a>[Portal](#tab/azure-portal)
Para adicionar um ponto final do servidor, vá ao grupo de sincronização recém-criado e, em seguida, **selecione Adicionar ponto final do servidor**.

![Adicionar um ponto final de servidor novo no painel do grupo de sincronização](media/storage-sync-files-deployment-guide/create-sync-group-2.png)

No painel **Adicionar ponto final de servidor**, introduza as informações seguintes para criar um ponto final de servidor:

- **Servidor registado**: O nome do servidor ou cluster onde pretende criar o ponto final do servidor.
- **Caminho**: O caminho do Servidor do Windows a ser sincronizado como parte do grupo de sincronização.
- **Cloud Tiering**: Um interruptor para ativar ou desativar o nível da nuvem. Com o tiering de nuvem, os ficheiros pouco utilizados ou acedidos podem ser hierárquicos para ficheiros Azure.
- **Volume Espaço Livre**: A quantidade de espaço livre para reservar no volume em que se encontra o ponto final do servidor. Por exemplo, se o espaço livre de volume estiver definido para 50% num volume que tenha um único ponto final do servidor, cerca de metade da quantidade de dados é tiered para Azure Files. Independentemente de o tiering de nuvem estar ativado, a sua partilha de ficheiros Azure tem sempre uma cópia completa dos dados do grupo de sincronização.
- **Modo de descarregamento inicial**: Esta é uma seleção opcional, a começar pela versão 11 do agente, que pode ser útil quando existem ficheiros na partilha de ficheiros Azure, mas não no servidor. Tal situação pode existir, por exemplo, se criar um ponto final do servidor para adicionar outro servidor de filial a um grupo de sincronização ou quando recuperar um servidor falhado. Se o tiering da nuvem estiver ativado, o padrão é apenas recordar o espaço de nome, sem conteúdo de ficheiro inicialmente. Isto é útil se você acredita que os pedidos de acesso ao utilizador devem decidir que conteúdo de ficheiro é recolhido para o servidor. Se o tiering da nuvem for desativado, o padrão é que o espaço de identificação será descarregado primeiro e, em seguida, os ficheiros serão recolhidos com base no último tempo de marcação modificado até que a capacidade local seja atingida. No entanto, pode alterar o modo de descarregamento inicial apenas para o espaço de nome. Um terceiro modo só pode ser utilizado se o nível da nuvem for desativado para este ponto final do servidor. Este modo evita recordar primeiro o espaço de nome. Os ficheiros só aparecerão no servidor local se tiverem a oportunidade de descarregar totalmente. Este modo é útil se, por exemplo, uma aplicação requer que os ficheiros completos estejam presentes e não puder tolerar ficheiros hierárquicos no seu espaço de nome.

Para adicionar o ponto final do servidor, **selecione Criar**. Os seus ficheiros são agora mantidos sincronizados através da sua partilha de ficheiros Azure e do Windows Server. 

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)
Execute os seguintes comandos PowerShell para criar o ponto final do servidor, e certifique-se de que substitui `<your-server-endpoint-path>` , e com os `<your-volume-free-space>` valores pretendidos e verifique a definição opcional para a política de descarregamento inicial opcional.

```powershell
$serverEndpointPath = "<your-server-endpoint-path>"
$cloudTieringDesired = $true
$volumeFreeSpacePercentage = <your-volume-free-space>
# Optional property. Choose from: [NamespaceOnly] default when cloud tiering is enabled. [NamespaceThenModifiedFiles] default when cloud tiering is disabled. [AvoidTieredFiles] only available when cloud tiering is disabled.
$initialDownloadPolicy = NamespaceOnly

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
        -VolumeFreeSpacePercent $volumeFreeSpacePercentage `
        -InitialDownloadPolicy $initialDownloadPolicy
} else {
    # Create server endpoint
    New-AzStorageSyncServerEndpoint `
        -Name $registeredServer.FriendlyName `
        -SyncGroup $syncGroup `
        -ServerResourceId $registeredServer.ResourceId `
        -ServerLocalPath $serverEndpointPath `
        -InitialDownloadPolicy $initialDownloadPolicy
}
```

# <a name="azure-cli"></a>[CLI do Azure](#tab/azure-cli)

Utilize o comando de [fim de servidor do grupo de sincronização az](/cli/azure/ext/storagesync/storagesync/sync-group/server-endpoint#ext-storagesync-az-storagesync-sync-group-server-endpoint-create) para criar um novo ponto final do servidor.

```azurecli
# Create a new sync group server endpoint 
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --name myNewServerEndpointName
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0
                                                 --server-local-path d:\myPath
                                                 --storage-sync-service myStorageSyncServiceNAme
                                                 --sync-group-name mySyncGroupName

# Create a new sync group server endpoint with additional optional parameters
az storagesync sync-group server-endpoint create --resource-group myResourceGroupName \
                                                 --storage-sync-service myStorageSyncServiceName \
                                                 --sync-group-name mySyncGroupName \
                                                 --name myNewServerEndpointName \
                                                 --registered-server-id 91beed22-7e9e-4bda-9313-fec96c286e0 \
                                                 --server-local-path d:\myPath \
                                                 --cloud-tiering on \
                                                 --volume-free-space-percent 85 \
                                                 --tier-files-older-than-days 15 \
                                                 --initial-download-policy NamespaceOnly [OR] NamespaceThenModifiedFiles [OR] AvoidTieredFiles
                                                 --offline-data-transfer on \
                                                 --offline-data-transfer-share-name myfilesharename \

```

---

## <a name="configure-firewall-and-virtual-network-settings"></a>Configurar firewall e configurações de rede virtual

### <a name="portal"></a>Portal
Se quiser configurar a sua sincronização de ficheiros Azure para trabalhar com firewall e definições de rede virtual, faça o seguinte:

1. A partir do portal Azure, navegue para a conta de armazenamento que deseja proteger.
1. Selecione o botão **Firewalls e redes virtuais** no menu esquerdo.
1. Selecione **redes selecionadas** em **Permitir o acesso a partir de**.
1. Certifique-se de que os seus servidores IP ou rede virtual estão listados na secção apropriada.
1. Certifique-se **de que os serviços fidedignos da Microsoft acedam a esta conta de armazenamento.**
1. **Selecione Guardar** para guardar as suas definições.

![Configurar firewall e configurações de rede virtuais para trabalhar com a sincronização do Ficheiro Azure](media/storage-sync-files-deployment-guide/firewall-and-vnet.png)

## <a name="onboarding-with-azure-file-sync"></a>Embarque com Azure File Sync
As etapas recomendadas para embarcar no Azure File Sync para o primeiro com zero tempo de inatividade, preservando a fidelidade completa do ficheiro e a lista de controlo de acesso (ACL) são as seguintes:
 
1. Implementar um serviço de sincronização de armazenamento.
1. Crie um grupo de sincronização.
1. Instale o agente Azure File Sync no servidor com o conjunto completo de dados.
1. Registe esse servidor e crie um ponto final do servidor na partilha. 
1. Deixe sincronizar fazer o upload completo para a partilha de ficheiros Azure (ponto final da nuvem).  
1. Após a conclusão do upload inicial, instale o agente Azure File Sync em cada um dos restantes servidores.
1. Crie novas ações de ficheiros em cada um dos restantes servidores.
1. Crie pontos finais de servidores em novas partilhas de ficheiros com a política de tiering da nuvem, se desejar. (Este passo requer armazenamento adicional disponível para a configuração inicial.)
1. Deixe o agente Azure File Sync fazer uma rápida restauração do espaço de nome completo sem a transferência de dados real. Após a sincronização completa do espaço de nome, o motor de sincronização preencherá o espaço do disco local com base na política de tiering da nuvem para o ponto final do servidor. 
1. Certifique-se de que a sincronização completa e teste a sua topologia conforme desejado. 
1. Redirecione os utilizadores e aplicações para esta nova parte.
1. Pode eliminar opcionalmente quaisquer ações duplicadas nos servidores.
 
Se não tiver armazenamento extra para o embarque inicial e quiser anexar as ações existentes, pode pré-sedição dos dados nas partilhas de ficheiros Azure. Esta abordagem é sugerida, se e somente se puder aceitar tempo de inatividade e garantir absolutamente nenhuma alteração de dados nas ações do servidor durante o processo inicial de embarque. 
 
1. Certifique-se de que os dados em qualquer um dos servidores não podem ser alterados durante o processo de embarque.
1. O ficheiro Azure pré-seed partilha com os dados do servidor utilizando qualquer ferramenta de transferência de dados sobre o SMB. Robocopia, por exemplo. YOu também pode usar AzCopy em vez de REST. Certifique-se de que utiliza o AzCopy com os interruptores apropriados para preservar os timetamps e atributos ACLs.
1. Crie topologia de Sincronização de Ficheiros Azure com os pontos finais do servidor pretendidos apontando para as ações existentes.
1. Deixe sincronizar o processo de reconciliação em todos os pontos finais. 
1. Uma vez concluída a reconciliação, pode abrir ações para alterações.
 
Atualmente, a abordagem pré-seeding tem algumas limitações - 
- As alterações de dados no servidor antes da topologia da sincronização estar completamente em funcionamento podem causar conflitos nos pontos finais do servidor.  
- Após a criação do ponto final da nuvem, o Azure File Sync executa um processo para detetar os ficheiros na nuvem antes de iniciar a sincronização inicial. O tempo necessário para concluir este processo varia consoante os vários fatores, como a velocidade da rede, a largura de banda disponível e o número de ficheiros e pastas. Para a estimativa aproximada na versão de pré-visualização, o processo de deteção é executado aproximadamente a 10 ficheiros/seg.  Assim, mesmo que a pré-sementeira seja rápida, o tempo geral para obter um sistema totalmente funcional pode ser significativamente mais longo quando os dados são pré-semeados na nuvem.

## <a name="self-service-restore-through-previous-versions-and-vss-volume-shadow-copy-service"></a>Restauração de self-service através de versões anteriores e VSS (Serviço de Cópia sombra de volume)

> [!IMPORTANT]
> As seguintes informações só podem ser utilizadas com a versão 9 (ou acima) do agente de sincronização de armazenamento. Versões inferiores a 9 não terão os cmdlets StorageSyncSelfService.

As versões anteriores são uma funcionalidade do Windows que permite utilizar imagens VSS do lado do servidor de um volume para apresentar versões ressarcidas de um ficheiro a um cliente SMB.
Isto permite um cenário poderoso, vulgarmente referido como restauração de autosserviço, diretamente para os trabalhadores da informação em vez de depender da restauração de um administrador de TI.

As imagens VSS e as versões anteriores funcionam independentemente do Azure File Sync. No entanto, o nível da nuvem deve ser definido para um modo compatível. Muitos pontos finais do servidor Azure File Sync podem existir no mesmo volume. Tem de fazer a seguinte chamada PowerShell por volume que tenha até um ponto final do servidor onde planeia ou está a utilizar o tiering de nuvem.

```powershell
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'
Enable-StorageSyncSelfServiceRestore [-DriveLetter] <string> [[-Force]] 
```

As fotos vss são tiradas de um volume inteiro. Por padrão, até 64 instantâneos podem existir para um determinado volume, desde que haja espaço suficiente para armazenar os instantâneos. O VSS trata disto automaticamente. O horário de instantâneo padrão leva duas fotos por dia, de segunda a sexta-feira. Esse horário é configurável através de uma Tarefa Agendada do Windows. O cmdlet PowerShell acima faz duas coisas:
1. Configura o tiering de nuvem Azure File Syncs no volume especificado para ser compatível com versões anteriores e garante que um ficheiro pode ser restaurado a partir de uma versão anterior, mesmo que tenha sido nivelado para a nuvem no servidor. 
1. Permite o horário vss predefinido. Em seguida, pode decidir modificá-lo mais tarde. 

> [!Note]  
> Há duas coisas importantes a notar:
>- Se utilizar o parâmetro -Force e o VSS estiver atualmente ativado, irá substituir o atual calendário de instantâneos VSS e substituí-lo pelo horário predefinido. Certifique-se de que guarda a sua configuração personalizada antes de executar o cmdlet.
> - Se estiver a utilizar este cmdlet num nó de cluster, também deve executá-lo em todos os outros nós do cluster! 

Para ver se a compatibilidade de autosserviço está ativada, pode executar o seguinte cmdlet.

```powershell
Get-StorageSyncSelfServiceRestore [[-Driveletter] <string>]
```

Ele listará todos os volumes no servidor, bem como o número de dias compatíveis com o tiering da nuvem para cada um. Este número é calculado automaticamente com base no máximo possível de instantâneos por volume e no horário de instantâneo predefinido. Assim, por padrão, todas as versões anteriores apresentadas a um trabalhador da informação podem ser usadas para restaurar a partir de. O mesmo acontece se alterar o horário padrão para tirar mais fotografias.
No entanto, se alterar o horário de uma forma que resultará num instantâneo disponível no volume mais antigo do que o valor dos dias compatíveis, então os utilizadores não poderão utilizar este instantâneo mais antigo (versão anterior) para restaurar.

> [!Note]
> Permitir a restauração do autosserviço pode ter um impacto no seu consumo de armazenamento Azure e na conta. Este impacto está limitado a ficheiros atualmente nivelados no servidor. Ativar esta funcionalidade garante que existe uma versão de ficheiro disponível na nuvem que pode ser referenciada através de uma entrada anterior (VSS snapshot).
>
> Se desativar a função, o consumo de armazenamento Azure diminuirá lentamente até que a janela de dias compatíveis passe. Não há como acelerar isto. 

O número máximo predefinido de instantâneos VSS por volume (64) bem como o horário predefinido para os tomar, resultam num máximo de 45 dias de versões anteriores que um trabalhador da informação pode restaurar, dependendo do número de instantâneos VSS que pode armazenar no seu volume.

Se as imagens de 64 VSS por volume não forem a definição correta, pode [alterar esse valor através de uma chave de registo](https://docs.microsoft.com/windows/win32/backup/registry-keys-for-backup-and-restore#maxshadowcopies).
Para que o novo limite produza efeitos, é necessário reencaminhar o cmdlet para permitir a compatibilidade da versão anterior em todos os volumes previamente ativados, com a bandeira da Força a ter em conta o novo número máximo de instantâneos VSS por volume. Isto resultará num número recém-calculado de dias compatíveis. Por favor, note que esta alteração só produzirá efeito em ficheiros recém-hierarquizados e substituirá quaisquer personalizações no calendário VSS que possa ter feito.

<a id="proactive-recall"></a>
## <a name="proactively-recall-new-and-changed-files-from-an-azure-file-share"></a>Recorde-se proativamente novos e alterados ficheiros a partir de uma partilha de ficheiros Azure

Com a versão 11 do agente, um novo modo fica disponível num ponto final do servidor. Este modo permite que as empresas distribuídas globalmente tenham a cache do servidor numa região remota pré-povoada mesmo antes de os utilizadores locais acederem a quaisquer ficheiros. Quando ativado num ponto final do servidor, este modo fará com que este servidor revoe ficheiros que foram criados ou alterados na partilha de ficheiros Azure.

### <a name="scenario"></a>Cenário

Uma empresa globalmente distribuída tem sucursais nos EUA e na Índia. De manhã (hora dos EUA) os trabalhadores da informação criam uma nova pasta e novos ficheiros para um novo projeto e trabalham o dia todo nele. O Azure File Sync sincronizará pastas e ficheiros para a partilha de ficheiros Azure (ponto final da nuvem). Os trabalhadores da informação na Índia continuarão a trabalhar no projeto no seu timezone. Quando chegam de manhã, o servidor local Azure File Sync, na Índia, precisa de ter estes novos ficheiros disponíveis localmente, de modo a que a equipa da Índia possa trabalhar eficientemente a partir de uma cache local. Permitir este modo impede que o acesso inicial do ficheiro seja mais lento devido à recolha a pedido e permite que o servidor relembrá-lo de forma proactiva assim que estes foram alterados ou criados na partilha de ficheiros Azure.

> [!IMPORTANT]
> É importante perceber que as mudanças de rastreio na partilha de ficheiros Azure que, de perto, no servidor podem aumentar o tráfego e a conta da Azure. Se os ficheiros recolhidos para o servidor não forem realmente necessários localmente, então a recolha desnecessária para o servidor pode ter consequências negativas. Utilize este modo quando souber que pré-povoar a cache num servidor com alterações recentes na nuvem terá um efeito positivo nos utilizadores ou aplicações que utilizam os ficheiros desse servidor.

### <a name="enable-a-server-endpoint-to-proactively-recall-what-changed-in-an-azure-file-share"></a>Ativar um ponto final do servidor para recordar proativamente o que mudou numa partilha de ficheiros Azure

# <a name="portal"></a>[Portal](#tab/proactive-portal)

1. No [portal Azure](https://portal.azure.com/), vá ao seu Serviço de Sincronização de Armazenamento, selecione o grupo de sincronização correto e, em seguida, identifique o ponto final do servidor para o qual pretende rastrear de perto as alterações na partilha de ficheiros Azure (ponto final da nuvem).
1. Na secção de nivelamento de nuvens, encontre o tópico "Azure file share download". Verá o modo atualmente selecionado e poderá alterá-lo para rastrear as alterações da partilha de ficheiros Azure de forma mais próxima e proactivamente relembrá-las para o servidor.

:::image type="content" source="media/storage-sync-files-deployment-guide/proactive-download.png" alt-text="Uma imagem que mostra o comportamento de descarregamento de ficheiros Azure para um ponto final do servidor atualmente em vigor e um botão para abrir um menu que permite alterá-lo.":::

# <a name="powershell"></a>[PowerShell](#tab/proactive-powershell)

Pode modificar as propriedades do ponto final do servidor em PowerShell através do [cmdlet Set-AzStorageSyncServerEndpoint.](https://docs.microsoft.com/powershell/module/az.storagesync/set-azstoragesyncserverendpoint)

```powershell
# Optional parameter. Default: "UpdateLocallyCachedFiles", alternative behavior: "DownloadNewAndModifiedFiles"
$recallBehavior = "DownloadNewAndModifiedFiles"

Set-AzStorageSyncServerEndpoint -InputObject <PSServerEndpoint> -LocalCacheMode $recallBehavior
```

---

## <a name="migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync"></a>Migrar uma implantação de replicação DFS (DFS-R) para Azure File Sync
Para migrar uma implementação DFS-R para Azure File Sync:

1. Crie um grupo de sincronização para representar a topologia DFS-R que está a substituir.
2. Comece no servidor que tem o conjunto completo de dados na sua topologia DFS-R para migrar. Instale o Azure File Sync nesse servidor.
3. Registe esse servidor e crie um ponto final do servidor para o primeiro servidor ser migrado. Não ative o nível da nuvem.
4. Deixe que todos os dados se sincronizem com a sua partilha de ficheiros Azure (ponto final da nuvem).
5. Instale e registe o agente Azure File Sync em cada um dos restantes servidores DFS-R.
6. Desative o DFS-R. 
7. Crie um ponto final do servidor em cada um dos servidores DFS-R. Não ative o nível da nuvem.
8. Certifique-se de que a sincronização completa e teste a sua topologia conforme desejado.
9. Aposentar-se DFS-R.
10. O tiering da nuvem pode agora ser ativado em qualquer ponto final do servidor, conforme desejado.

Para obter mais informações, consulte [o interop Azure File Sync com o Distributed File System (DFS)](storage-sync-files-planning.md#distributed-file-system-dfs).

## <a name="next-steps"></a>Passos seguintes
- [Adicionar ou remover um ponto de final do servidor de sincronização de ficheiros Azure](storage-sync-files-server-endpoint.md)
- [Registar ou não registar um servidor com Azure File Sync](storage-sync-files-server-registration.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
