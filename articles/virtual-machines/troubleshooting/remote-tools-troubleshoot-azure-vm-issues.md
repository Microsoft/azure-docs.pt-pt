---
title: Utilizar ferramentas remotas para resolver problemas de VM do Azure | Documentos da Microsoft
description: ''
services: virtual-machines-windows
documentationcenter: ''
author: Deland-Han
manager: cshepard
editor: ''
tags: ''
ms.service: virtual-machines
ms.topic: troubleshooting
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.devlang: azurecli
ms.date: 01/11/2018
ms.author: delhan
ms.openlocfilehash: 2126ed2624d735d88d52ddc1ee97bfb970cc8a74
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58119773"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Utilize as ferramentas remotas para resolver problemas de VM do Azure

Quando resolver problemas numa máquina virtual do Azure (VM), pode ligar à VM ao utilizar as ferramentas remotas que são abordadas neste artigo em vez de utilizar o protocolo RDP (Remote Desktop).

## <a name="serial-console"></a>Consola de série

Uso [consola de série de Máquina Virtual](serial-console-windows.md) para executar comandos na VM do Azure remoto.

## <a name="remote-cmd"></a>CMD remoto

Baixe [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Ligar à VM ao executar o seguinte comando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!Note]
>* O comando deve ser executado num computador que está na mesma VNET.
>* Nome de anfitrião ou DIP pode ser utilizado para substituir <computer>.
>* O parâmetro -s certifica-se de que o comando for chamado com a conta de sistema (permissão de administrador).
>* PsExec utiliza as portas TCP 135 e 445. Portanto, as duas portas têm de ser aberta na Firewall.

## <a name="run-commands"></a>Comandos de Execução

Ver [scripts do PowerShell de executar na sua VM do Windows com o comando executar](../windows/run-command.md) para obter mais informações sobre como utilizar a funcionalidade de executar comandos para executar scripts na VM.

## <a name="customer-script-extension"></a>Extensão de Script de cliente

Pode utilizar a funcionalidade de extensão de Script personalizado para executar um script personalizado na VM de destino. Para utilizar esta funcionalidade, as condições seguintes têm de ser cumpridas:

* A VM tem conectividade.

* O agente do Azure está instalado e a funcionar conforme esperado na VM.

* A extensão não foi anteriormente instalada na VM.
 
  A extensão de injetar o script apenas na primeira vez que é utilizado. Se usar esse recurso, mais tarde, a extensão reconhecerá que já foi utilizado e não irá carregar o novo script.

Terá de carregar o script para uma conta de armazenamento e gerar o seu próprio contentor. Em seguida, execute o seguinte script do Azure PowerShell num computador que tenha conectividade à VM.

### <a name="for-v1-vms"></a>Para VMs de V1

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

### <a name="for-v2-vms"></a>Para V2 VMs

[!INCLUDE [updated-for-az-vm.md](../../../includes/updated-for-az-vm.md)]

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
>Porta de TCP 5986 (HTTPS) tem de estar aberta para que possa utilizar esta opção.
>
>Para VMs do ARM, tem de abrir a porta 5986, no grupo de segurança de rede (NSG). Para obter mais informações, consulte grupos de segurança. 
>
>Para VMs de RDFE, tem de ter um ponto final que tem uma privada (5986) de porta e uma porta pública. Em seguida, precisa também abrir essa porta destinado ao pública no NSG.

### <a name="set-up-the-client-computer"></a>Configurar o computador cliente

Para utilizar o PowerShell para ligar remotamente à VM, primeiro tem de configurar o computador cliente para permitir a ligação. Para fazer isso, adicione a VM para a lista de anfitriões fidedignos do PowerShell ao executar o comando seguinte, conforme apropriado.

Para adicionar uma VM a lista de anfitriões fidedignos:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Para adicionar várias VMs à lista de anfitriões fidedignos:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Para adicionar todos os computadores à lista de anfitriões fidedignos:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Ativar RemotePS na VM

Para VMs clássicas, utilize a extensão de Script personalizado para executar o script seguinte:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Para VMs do ARM, utilize executar comandos a partir do portal para executar o script de EnableRemotePS:

![Executar o Comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Ligar à VM

Execute o seguinte comando, dependendo do cliente local do computador:

* Fora do VNET ou a implementação

  * Para uma VM clássica, execute o seguinte comando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Para uma VM de ARM, adicione um nome DNS para o endereço IP público. Para obter passos detalhados, consulte [criar um nome de domínio completamente qualificado no portal do Azure para uma VM do Windows](../windows/portal-create-fqdn.md). Depois, execute o comando seguinte:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Dentro da VNET ou a implementação, execute o seguinte comando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!Note] 
>Definir o sinalizador de SkipCaCheck ignora o requisito para importar um certificado à VM quando iniciar a sessão.

Também pode utilizar o cmdlet Invoke-Command para executar um script na VM remotamente:

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registo remoto

>[!Note]
>A porta TCP 135 ou 445 tem de estar aberta para poder utilizar esta opção.
>
>Para VMs do ARM, terá de abrir a porta 5986 no NSG. Para obter mais informações, consulte grupos de segurança. 
>
>Para VMs de RDFE, tem de ter um ponto final que possui uma porta privada 5986 e uma porta pública. Deve também abrir essa porta pública no NSG.

1. A partir de outra VM na mesma VNET, abra o editor de registo (regedit.exe).

2. Selecione **arquivo** >**ligar o registo de rede**.

   ![Opção remota](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Localize a VM de destino pelo **hostname** ou **IP dinâmico** (preferível), introduzi-la na caixa "Introduza o nome de objeto a selecionar".

   ![Opção remota](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Introduza as credenciais para a VM de destino.

5. Efetue as alterações de registo necessárias.

## <a name="remote-services-console"></a>Consola de serviços remoto

>[!Note]
>Portas TCP 135 ou 445 tem de estar abertas para poder utilizar esta opção.
>
>Para VMs do ARM, terá de abrir a porta 5986 no NSG. Para obter mais informações, consulte grupos de segurança. 
>
>Para VMs de RDFE, tem de ter um ponto final que possui uma porta privada 5986 e uma porta pública. Em seguida, precisa também abrir essa porta pública no NSG.

1. A partir de outra VM na mesma VNET, abra uma instância do **Services. msc**.

2. Com o botão direito **serviços (Local)**.

3. Selecione **ligar a outro computador**.

   ![Serviço remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Introduza o IP dinâmico da VM de destino.

   ![Entrada DIP](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Faça as alterações necessárias aos serviços.

## <a name="next-steps"></a>Próximos Passos

[Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx)

[Personalizado Script extensão para Windows usando o modelo de implementação clássica](../extensions/custom-script-classic.md)

PsExec é parte da [conjunto PSTools](https://download.sysinternals.com/files/PSTools.zip).

Para obter mais informações sobre o conjunto PSTools, consulte [conjunto PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


