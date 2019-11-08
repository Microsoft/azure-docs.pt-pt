---
title: Usar ferramentas remotas para solucionar problemas de VM do Azure | Microsoft Docs
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: dcscontentpm
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: fab1e0b6f3b01446baed974b4be9b7295af4f837
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73749716"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Usar ferramentas remotas para solucionar problemas de VM do Azure

Ao solucionar problemas em uma VM (máquina virtual) do Azure, você pode se conectar à VM usando as ferramentas remotas que são discutidas neste artigo em vez de usar protocolo RDP (RDP).

## <a name="serial-console"></a>Consola de Série

Use o [console serial da máquina virtual](serial-console-windows.md) para executar comandos na VM remota do Azure.

## <a name="remote-cmd"></a>CMD remoto

Baixe o [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Conecte-se à VM executando o seguinte comando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* O comando deve ser executado em um computador que esteja na mesma VNET.
>* O DIP ou o nome do host pode ser usado para substituir \<computador >.
>* O parâmetro-s garante que o comando seja invocado usando a conta do sistema (permissão de administrador).
>* O PsExec usa as portas TCP 135 e 445. Portanto, as duas portas precisam estar abertas no firewall.

## <a name="run-commands"></a>Executar comandos

Consulte [executar scripts do PowerShell em sua VM do Windows com o comando executar](../windows/run-command.md) para obter mais informações sobre como usar o recurso executar comandos para executar scripts na VM.

## <a name="customer-script-extension"></a>Extensão de script do cliente

Você pode usar o recurso de extensão de script personalizado para executar um script personalizado na VM de destino. Para usar esse recurso, as seguintes condições devem ser atendidas:

* A VM tem conectividade.

* O agente do Azure está instalado e funcionando conforme o esperado na VM.

* A extensão não foi instalada anteriormente na VM.
 
  A extensão injetará o script somente na primeira vez que ele for usado. Se você usar esse recurso mais tarde, a extensão reconhecerá que ele já foi usado e não carregará o novo script.

Você precisa carregar seu script para uma conta de armazenamento e gerar seu próprio contêiner. Em seguida, execute o seguinte script em Azure PowerShell em um computador que tenha conectividade com a VM.

### <a name="for-v1-vms"></a>Para VMs v1

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group/Cloud Service where the VM is hosted. I.E.: For "demo305.cloudapp.net" the cloud service is going to be demo305

#Setup the Azure Powershell module and ensure the access to the subscription
Import-Module Azure
Add-AzureAccount  #Ensure Login with account associated with subscription ID
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Setup the access to the storage account and upload the script
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-v2-vms"></a>Para VMs v2

 

```powershell
#Setup the basic variables
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for blob in storage
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Setup the Azure Powershell module and ensure the access to the subscription
Login-AzAccount #Ensure Login with account associated with subscription ID
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Setup the access to the storage account and upload the script 
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>PowerShell remoto

>[!Note]
>A porta TCP 5986 (HTTPS) deve estar aberta para que você possa usar essa opção.
>
>Para VMs ARM, você deve abrir a porta 5986 no grupo de segurança de rede (NSG). Para obter mais informações, consulte grupos de segurança. 
>
>Para VMs RDFE, você deve ter um ponto de extremidade que tenha uma porta privada (5986) e uma porta pública. Em seguida, você também precisa abrir essa porta pública no NSG.

### <a name="set-up-the-client-computer"></a>Configurar o computador cliente

Para usar o PowerShell para se conectar à VM remotamente, primeiro você precisa configurar o computador cliente para permitir a conexão. Para fazer isso, adicione a VM à lista de hosts confiáveis do PowerShell executando o comando a seguir, conforme apropriado.

Para adicionar uma VM à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Para adicionar várias VMs à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Para adicionar todos os computadores à lista de hosts confiáveis:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Habilitar RemotePS na VM

Para VMs clássicas, use a extensão de script personalizado para executar o seguinte script:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Para VMs ARM, use comandos de execução do portal para executar o script EnableRemotePS:

![Executar o Comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Ligar à VM

Execute o seguinte comando, dependendo do local do computador cliente:

* Fora da VNET ou da implantação

  * Para uma VM clássica, execute o seguinte comando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Para uma VM ARM, primeiro adicione um nome DNS ao endereço IP público. Para obter etapas detalhadas, consulte [criar um nome de domínio totalmente qualificado no portal do Azure para uma VM do Windows](../windows/portal-create-fqdn.md). Depois, execute o comando seguinte:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Dentro da VNET ou da implantação, execute o seguinte comando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Definir o sinalizador SkipCaCheck ignora a necessidade de importar um certificado para a VM quando você inicia a sessão.

Você também pode usar o cmdlet Invoke-Command para executar um script na VM remotamente:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registro remoto

>[!Note]
>A porta TCP 135 ou 445 deve estar aberta para que você possa usar essa opção.
>
>Para VMs ARM, você precisa abrir a porta 5986 no NSG. Para obter mais informações, consulte grupos de segurança. 
>
>Para VMs RDFE, você deve ter um ponto de extremidade que tenha uma porta privada 5986 e uma porta pública. Você também precisa abrir essa porta voltada para o público no NSG.

1. Em outra VM na mesma VNET, abra o editor do registro (regedit. exe).

2. Selecione **arquivo** >**conectar registro de rede**.

   ![Opção remota](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Localize a VM de destino pelo **nome do host** ou **IP dinâmico** (preferível) inserindo-a na caixa "digite o nome do objeto a ser selecionado".

   ![Opção remota](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Insira as credenciais para a VM de destino.

5. Faça as alterações de Registro necessárias.

## <a name="remote-services-console"></a>Console de serviços remotos

>[!Note]
>As portas TCP 135 ou 445 devem estar abertas para usar essa opção.
>
>Para VMs ARM, você precisa abrir a porta 5986 no NSG. Para obter mais informações, consulte grupos de segurança. 
>
>Para VMs RDFE, você deve ter um ponto de extremidade que tenha uma porta privada 5986 e uma porta pública. Em seguida, você também precisa abrir essa porta voltada para o público no NSG.

1. De outra VM na mesma VNET, abra uma instância de **Services. msc**.

2. Clique com o botão direito do mouse em **Serviços (local)** .

3. Selecione **conectar a outro computador**.

   ![Serviço remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Insira o IP dinâmico da VM de destino.

   ![DIP de entrada](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Faça as alterações necessárias nos serviços.

## <a name="next-steps"></a>Próximos Passos

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Extensão de script personalizado para Windows usando o modelo de implantação clássico](../extensions/custom-script-classic.md)

O PsExec faz parte do [conjunto PsTools](https://download.sysinternals.com/files/PSTools.zip).

Para obter mais informações sobre o conjunto de PSTools, consulte [PsTools Suite](https://docs.microsoft.com/sysinternals/downloads/pstools).


