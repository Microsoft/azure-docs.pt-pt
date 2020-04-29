---
title: Use ferramentas remotas para resolver problemas com problemas da Azure VM [ Microsoft Docs
description: Saiba mais sobre os scripts PsExec, PowerShell e outras ferramentas remotas que pode usar para resolver problemas remotos do Azure VM sem usar RDP.
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
ms.openlocfilehash: d29b2b7c2b9194f20afe4c74d117847f0e343b12
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80422613"
---
# <a name="use-remote-tools-to-troubleshoot-azure-vm-issues"></a>Use ferramentas remotas para resolver problemas com problemas de Azure VM

Quando resolver problemas numa máquina virtual Azure (VM), pode ligar-se ao VM utilizando as ferramentas remotas que são discutidas neste artigo em vez de utilizar o Protocolo de Ambiente de Trabalho Remoto (RDP).

## <a name="serial-console"></a>Consola de série

Utilize uma [consola em série para máquinas virtuais Azure](serial-console-windows.md) para executar comandos no Telecomando Azure VM.

## <a name="remote-cmd"></a>CMD remoto

Baixar [PsExec](https://docs.microsoft.com/sysinternals/downloads/psexec). Ligue-se ao VM executando o seguinte comando:

```cmd
psexec \\<computer>-u user -s cmd
```

>[!NOTE]
>* O comando deve ser executado num computador que esteja na mesma rede virtual.
>* Dip ou HostName podem \<ser usados para substituir> de computador.
>* O parâmetro -s certifica-se de que o comando é invocado utilizando a Conta do Sistema (permissão do administrador).
>* O PsExec utiliza as portas TCP 135 e 445. Como resultado, as duas portas têm de estar abertas na firewall.

## <a name="run-command"></a>Executar comando

Para obter mais informações sobre como utilizar a funcionalidade de comando de execução para executar scripts no VM, consulte [scripts Run PowerShell no seu Windows VM com comando](../windows/run-command.md)de execução .

## <a name="custom-script-extension"></a>Extensão de Script Personalizado

Pode utilizar a funcionalidade de extensão de script personalizado para executar um script personalizado no VM alvo. Para utilizar esta função, devem ser satisfeitas as seguintes condições:

* O VM tem conectividade.
* O Agente De Máquina Virtual Azure está instalado e está a trabalhar como esperado no VM.
* A extensão não foi previamente instalada no VM.
 
  A extensão injeta o guião apenas na primeira vez que é usado. Se utilizar esta funcionalidade mais tarde, a extensão reconhece que já foi utilizada e não faz o upload do novo script.

Faça upload do seu script para uma conta de armazenamento e gere o seu próprio recipiente. Em seguida, execute o seguinte script em Azure PowerShell num computador que tenha conectividade com o VM.

### <a name="for-classic-deployment-model-vms"></a>Para VMs modelo de implementação clássico

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>" 
$storageAccount = "<<STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmCloudService = "<<CLOUD SERVICE>>" #Resource group or cloud service where the VM is hosted. For example, for "demo305.cloudapp.net" the cloud service is going to be demo305.

#Set up the Azure PowerShell module, and ensure the access to the subscription.
Import-Module Azure
Add-AzureAccount  #Ensure login with the account associated with the subscription ID.
Get-AzureSubscription -SubscriptionId $subscriptionID | Select-AzureSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzureStorageKey -StorageAccountName $storageAccount).Primary
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)<
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
$vm = Get-AzureVM -ServiceName $vmCloudService -Name $vmName
Set-AzureVMCustomScriptExtension "CustomScriptExtension" -VM $vm -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName | Update-AzureVM
```

### <a name="for-azure-resource-manager-vms"></a>Para VMs do Gestor de Recursos Azure

 

```powershell
#Set up the basic variables.
$subscriptionID = "<<SUBSCRIPTION ID>>"
$storageAccount = "<<STORAGE ACCOUNT>>"
$storageRG = "<<RESOURCE GROUP OF THE STORAGE ACCOUNT>>" 
$localScript = "<<FULL PATH OF THE PS1 FILE TO EXECUTE ON THE VM>>" 
$blobName = "file.ps1" #Name you want for the blob in the storage.
$vmName = "<<VM NAME>>" 
$vmResourceGroup = "<<RESOURCE GROUP>>"
$vmLocation = "<<DATACENTER>>" 
 
#Set up the Azure PowerShell module, and ensure the access to the subscription.
Login-AzAccount #Ensure login with the account associated with the subscription ID.
Get-AzSubscription -SubscriptionId $subscriptionID | Select-AzSubscription

#Set up the access to the storage account, and upload the script.
$storageKey = (Get-AzStorageAccountKey -ResourceGroupName $storageRG -Name $storageAccount).Value[0]
$context = New-AzureStorageContext -StorageAccountName $storageAccount -StorageAccountKey $storageKey
$container = "cse" + (Get-Date -Format yyyyMMddhhmmss)
New-AzureStorageContainer -Name $container -Permission Off -Context $context
Set-AzureStorageBlobContent -File $localScript -Container $container -Blob $blobName  -Context $context

#Push the script into the VM.
Set-AzVMCustomScriptExtension -Name "CustomScriptExtension" -ResourceGroupName $vmResourceGroup -VMName $vmName -Location $vmLocation -StorageAccountName $storageAccount -StorageAccountKey $storagekey -ContainerName $container -FileName $blobName -Run $blobName
```

## <a name="remote-powershell"></a>PowerShell remoto

>[!NOTE]
>A Porta TCP 5986 (HTTPS) deve estar aberta para que possa utilizar esta opção.
>
>Para os VMs do Gestor de Recursos Azure, deve abrir a porta 5986 no grupo de segurança da rede (NSG). Para mais informações, consulte os grupos de segurança. 
>
>Para os VMs RDFE, você deve ter um ponto final que tem um porto privado (5986) e um porto público. Depois, também tens de abrir aquela porta virada para o público no NSG.

### <a name="set-up-the-client-computer"></a>Configurar o computador cliente

Para utilizar o PowerShell para ligar remotamente ao VM, tem primeiro de configurar o computador cliente para permitir a ligação. Para tal, adicione o VM à lista de anfitriões fidedignos da PowerShell executando o seguinte comando, conforme apropriado.

Para adicionar um VM à lista de anfitriões fidedignos:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName>
```

Para adicionar vários VMs à lista de anfitriões fidedignos:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value <ComputerName1>,<ComputerName2>
```

Para adicionar todos os computadores à lista de anfitriões fidedignos:

```powershell
Set-Item wsman:\localhost\Client\TrustedHosts -value *
```

### <a name="enable-remoteps-on-the-vm"></a>Ativar RemotePS no VM

Para VMs criados usando o modelo de implementação clássico, use a extensão do script personalizado para executar o seguinte script:

```powershell
Enable-PSRemoting -Force
New-NetFirewallRule -Name "Allow WinRM HTTPS" -DisplayName "WinRM HTTPS" -Enabled True -Profile Any -Action Allow -Direction Inbound -LocalPort 5986 -Protocol TCP
$thumbprint = (New-SelfSignedCertificate -DnsName $env:COMPUTERNAME -CertStoreLocation Cert:\LocalMachine\My).Thumbprint
$command = "winrm create winrm/config/Listener?Address=*+Transport=HTTPS @{Hostname=""$env:computername""; CertificateThumbprint=""$thumbprint""}"
cmd.exe /C $command
```

Para VMs do Gestor de Recursos Azure, utilize comandos de execução do portal para executar o script EnableRemotePS:

![Executar comando](./media/remote-tools-troubleshoot-azure-vm-issues/run-command.png)

### <a name="connect-to-the-vm"></a>Ligar à VM

Executar o seguinte comando com base na localização do computador do cliente:

* Fora da rede virtual ou implantação

  * Para um VM criado usando o modelo de implantação clássico, executar o seguinte comando:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName  "<<CLOUDSERVICENAME.cloudapp.net>>" -port "<<PUBLIC PORT NUMBER>>" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

  * Para um VM do Gestor de Recursos Azure, adicione primeiro um nome DNS ao endereço IP público. Para passos detalhados, consulte Criar um nome de [domínio totalmente qualificado no portal Azure para um Windows VM](../windows/portal-create-fqdn.md). Depois, execute o comando seguinte:

    ```powershell
    $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
    Enter-PSSession -ComputerName "<<DNSname.DataCenter.cloudapp.azure.com>>" -port "5986" -Credential (Get-Credential) -useSSL -SessionOption $Skip
    ```

* Dentro da rede virtual ou implantação, executar o seguinte comando:
  
  ```powershell
  $Skip = New-PSSessionOption -SkipCACheck -SkipCNCheck
  Enter-PSSession -ComputerName  "<<HOSTNAME>>" -port 5986 -Credential (Get-Credential) -useSSL -SessionOption $Skip
  ```

>[!NOTE] 
>A definição da bandeira SkipCaCheck ignora a obrigação de importar um certificado para o VM quando iniciar a sessão.

Também pode utilizar o cmdlet Invoke-Command para executar um script no VM remotamente.

```powershell
Invoke-Command -ComputerName "<<COMPUTERNAME>" -ScriptBlock {"<<SCRIPT BLOCK>>"}
```

## <a name="remote-registry"></a>Registo Remoto

>[!NOTE]
>A porta TCP 135 ou 445 deve estar aberta para utilizar esta opção.
>
>Para os VMs do Gestor de Recursos Azure, você tem que abrir o porto 5986 no NSG. Para mais informações, consulte os grupos de segurança. 
>
>Para os VMs RDFE, você deve ter um ponto final que tem um porto privado 5986 e um porto público. Também tem de abrir aquele porto virado para o público no NSG.

1. De outro VM na mesma rede virtual, abra o editor de registo (regedit.exe).

2. Selecione**Registo de Rede de Rede de Ligação**de **Ficheiros** > .

   ![Editor de registo](./media/remote-tools-troubleshoot-azure-vm-issues/remote-registry.png) 

3. Localize o VM alvo pelo nome do **anfitrião** ou **IP dinâmico** (preferível) inserindo-o no **nome do objeto Para selecionar a** caixa.

   ![Introduza o nome do objeto para selecionar a caixa](./media/remote-tools-troubleshoot-azure-vm-issues/input-computer-name.png) 
 
4. Introduza as credenciais para o alvo VM.

5. Faça as alterações de registo necessárias.

## <a name="remote-services-console"></a>Consola de serviços remotos

>[!NOTE]
>As portas TCP 135 ou 445 devem estar abertas para utilizar esta opção.
>
>Para os VMs do Gestor de Recursos Azure, você tem que abrir o porto 5986 no NSG. Para mais informações, consulte os grupos de segurança. 
>
>Para os VMs RDFE, você deve ter um ponto final que tem um porto privado 5986 e um porto público. Também tem de abrir aquele porto virado para o público no NSG.

1. De outro VM na mesma rede virtual, abra uma instância de **Services.msc**.

2. Serviços de clique à direita **(Local)**.

3. Selecione **Ligar a outro computador**.

   ![Serviço remoto](./media/remote-tools-troubleshoot-azure-vm-issues/remote-services.png)

4. Introduza o IP dinâmico do Alvo VM.

   ![IP dinâmico de entrada](./media/remote-tools-troubleshoot-azure-vm-issues/input-ip-address.png)

5. Faça as alterações necessárias aos serviços.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações sobre o cmdlet Enter-PSSession, consulte [Enter-PSSession](https://technet.microsoft.com/library/hh849707.aspx).
- Para obter mais informações sobre a extensão personalizada do script para windows utilizando o modelo de implementação clássico, consulte [a extensão do script personalizado para windows](../extensions/custom-script-classic.md).
- O PsExec faz parte da [Suite PSTools.](https://download.sysinternals.com/files/PSTools.zip)
- Para mais informações sobre a Suite PSTools, consulte [PSTools](https://docs.microsoft.com/sysinternals/downloads/pstools).


