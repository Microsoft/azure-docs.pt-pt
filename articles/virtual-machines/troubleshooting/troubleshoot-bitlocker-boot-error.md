---
title: Erros de arranque BitLocker na resolução de problemas da bota BitLocker num | VM Azure Microsoft Docs
description: Saiba como resolver erros de arranque BitLocker num Azure VM
services: virtual-machines-windows
documentationCenter: ''
author: genlin
manager: dcscontentpm
editor: v-jesits
ms.service: virtual-machines-windows
ms.topic: troubleshooting
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 11/20/2020
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: 87bf311b5199ec187c24c28a42314d9dc6787998
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98633032"
---
# <a name="bitlocker-boot-errors-on-an-azure-vm"></a>Erros de arranque BitLocker num Azure VM

 Este artigo descreve os erros do BitLocker que poderá experimentar quando iniciar uma máquina virtual (VM) do Windows no Microsoft Azure.

## <a name="symptom"></a>Sintoma

 Um VM do Windows não começa. Quando verifica as imagens na janela de [diagnóstico boot,](./boot-diagnostics.md) vê uma das seguintes mensagens de erro:

- Ligue o controlador USB que tem a chave BitLocker

- Está trancado fora! Introduza a chave de recuperação para voltar a funcionar (Layout do Teclado: EUA) A informação de entrada errada foi inserida demasiadas vezes, pelo que o seu PC foi bloqueado para proteger a sua privacidade. Para recuperar a chave de recuperação, vá para https://windows.microsoft.com/recoverykeyfaq outro pc ou dispositivo móvel. Caso precise, o ID chave é XXXXXXX. Ou pode redefinir o seu PC.

- Introduza a palavra-passe para desbloquear esta unidade [ ] Prima a tecla de inserir para ver a palavra-passe à medida que digita.
- Introduza a sua chave de recuperação Carregue a sua chave de recuperação a partir de um dispositivo USB.

## <a name="cause"></a>Causa

Este problema pode ocorrer se o VM não conseguir localizar o ficheiro BitLocker Recovery Key (BEK) para desencriptar o disco encriptado.

## <a name="solution"></a>Solução

> [!TIP]
> Se tiver uma cópia de segurança recente do VM, poderá tentar [restaurar o VM da cópia de segurança](../../backup/backup-azure-arm-restore-vms.md) para corrigir o problema da bota.

Para resolver este problema, pare e transloja o VM e, em seguida, inicie-o. Esta operação força o VM a recuperar o ficheiro BEK do Cofre da Chave Azure e, em seguida, colocá-lo no disco encriptado. 

Se este método não resolver o problema, siga estes passos para restaurar manualmente o ficheiro BEK:

1. Tire uma foto do disco do sistema do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Ligue o disco do sistema a um VM de recuperação](troubleshoot-recovery-disks-portal-windows.md). Para executar o comando [de gestão bde](/windows-server/administration/windows-commands/manage-bde) no passo 7, a **função de encriptação BitLocker Drive** deve ser ativada no VM de recuperação.

    Quando anexar um disco gerido, poderá receber uma mensagem de erro "contém definições de encriptação e, portanto, não pode ser utilizada como um disco de dados". Nesta situação, execute o seguinte script para tentar novamente anexar o disco:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"
    # Set the EncryptionSettingsEnabled property to false, so you can attach the disk to the recovery VM.
    New-AzDiskUpdateConfig -EncryptionSettingsEnabled $false |Update-AzDisk -diskName $osDiskName -ResourceGroupName $rgName

    $recoveryVMName = "myRecoveryVM" 
    $recoveryVMRG = "RecoveryVMRG" 
    $OSDisk = Get-AzDisk -ResourceGroupName $rgName -DiskName $osDiskName;

    $vm = get-AzVM -ResourceGroupName $recoveryVMRG -Name $recoveryVMName 

    Add-AzVMDataDisk -VM $vm -Name $osDiskName -ManagedDiskId $osDisk.Id -Caching None -Lun 3 -CreateOption Attach 

    Update-AzVM -VM $vm -ResourceGroupName $recoveryVMRG
    ```
     Não é possível anexar um disco gerido a um VM que foi restaurado a partir de uma imagem blob.

3. Depois de o disco estar ligado, faça uma ligação remota ao ambiente de trabalho à VM de recuperação para que possa executar alguns scripts Azure PowerShell. Certifique-se de que tem a [versão mais recente do Azure PowerShell](/powershell/azure/) instalada no VM de recuperação.

4. Abra uma sessão Azure PowerShell elevada (Executar como administrador). Executar os seguintes comandos para iniciar seduca na subscrição do Azure:

    ```Powershell
    Add-AzAccount -SubscriptionID [SubscriptionID]
    ```

5. Execute o seguinte script para verificar o nome do ficheiro BEK:

    ```powershell
    $vmName = "myVM"
    $vault = "myKeyVault"
    Get-AzKeyVaultSecret -VaultName $vault | where {($_.Tags.MachineName -eq $vmName) -and ($_.ContentType -match 'BEK')} `
            | Sort-Object -Property Created `
            | ft  Created, `
                @{Label="Content Type";Expression={$_.ContentType}}, `
                @{Label ="MachineName"; Expression = {$_.Tags.MachineName}}, `
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Segue-se a amostra da saída. Neste caso, assumimos que o nome do ficheiro é EF7B2F5A-50C6-4637-0001-7F599C12F85C. O BEK.

    ```
    Created               Content Type Volume MachineName DiskEncryptionKeyFileName
    -------               ------------ ------ ----------- -------------------------
    11/20/2020 7:41:56 AM BEK          C:\    myVM   EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Se vir dois volumes duplicados, o volume que tem o novo timetamp é o ficheiro BEK atual que é utilizado pelo VM de recuperação.

    Se o valor **do tipo de conteúdo** for embrulhado **BEK,** vá para os [cenários da Chave de Encriptação (KEK).](#key-encryption-key-scenario)

    Agora que tem o nome do ficheiro BEK para a unidade, tem de criar o nome do ficheiro secreto. Ficheiro BEK para desbloquear a unidade.

6.  Descarregue o ficheiro BEK para o disco de recuperação. A amostra seguinte guarda o ficheiro BEK na pasta C:\BEK. Certifique-se de que o `C:\BEK\` caminho existe antes de executar os scripts.

    ```powershell
    $vault = "myKeyVault"
    $bek = "EF7B2F5A-50C6-4637-0001-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $bekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Para desbloquear o disco anexo utilizando o ficheiro BEK, executar o seguinte comando.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-0001-7F599C12F85C.BEK
    ```
    Nesta amostra, o disco de so em anexo é unidade F. Certifique-se de que utiliza a letra de acionamento correta. 

8. Depois de o disco ter sido desbloqueado com sucesso utilizando a tecla BEK, retire o disco do VM de recuperação e, em seguida, recrie o VM utilizando este novo disco DE.

    > [!NOTE]
    > A troca de discos OS não é suportada por VMs utilizando encriptação de disco.

9. Se o novo VM ainda não conseguir arrancar normalmente, experimente um dos seguintes passos depois de desbloquear a unidade:

    - Suspender a proteção para desligar temporariamente o BitLocker, executando o seguinte:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Desencriptar completamente a unidade. Para tal, execute o seguinte comando:

    ```console
    manage-bde -off F:
    ```

### <a name="key-encryption-key-scenario"></a>Cenário chave da chave de encriptação

Para um cenário chave de encriptação, siga estes passos:

1. Certifique-se de que a conta de utilizador iniciada requer a permissão "desembrulhada" nas políticas de Acesso ao Cofre de Chaves no **UTILIZADOR| Permissões chave| Operações criptográficas| Chave de desembrulhar**.
2. Guarde o seguinte script para um . Ficheiro PS1:

    ```powershell
    #Set the Parameters for the script
    param (
            [Parameter(Mandatory=$true)]
            [string] 
            $keyVaultName,
            [Parameter(Mandatory=$true)]
            [string] 
            $kekName,
            [Parameter(Mandatory=$true)]
            [string]
            $secretName,
            [Parameter(Mandatory=$true)]
            [string]
            $bekFilePath,
            [Parameter(Mandatory=$true)]
            [string] 
            $adTenant
            )
    # Load ADAL Assemblies
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    If ((Test-Path -Path $adal) -and (Test-Path -Path $adalforms)) { 

    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }
     else
     {
    $adal="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms ="${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts\$(((dir ${env:userprofile}\Documents\WindowsPowerShell\Modules\Az.Accounts).name) | select -last 1)\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)
     }  

    # Set well-known client ID for AzurePowerShell
    $clientId = "1950a258-227b-4e31-a9cf-717495945fc2" 
    # Set redirect URI for Azure PowerShell
    $redirectUri = "urn:ietf:wg:oauth:2.0:oob"
    # Set Resource URI to Azure Service Management API
    $resourceAppIdURI = "https://vault.azure.net"
    # Set Authority to Azure AD Tenant
    $authority = "https://login.windows.net/$adtenant"
    # Create Authentication Context tied to Azure AD Tenant
    $authContext = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.AuthenticationContext" -ArgumentList $authority
    # Acquire token
    $platformParameters = New-Object "Microsoft.IdentityModel.Clients.ActiveDirectory.PlatformParameters" -ArgumentList "Auto"
    $authResult = $authContext.AcquireTokenAsync($resourceAppIdURI, $clientId, $redirectUri, $platformParameters).result
    # Generate auth header 
    $authHeader = $authResult.CreateAuthorizationHeader()
    # Set HTTP request headers to include Authorization header
    $headers = @{'x-ms-version'='2014-08-01';"Authorization" = $authHeader}

    ########################################################################################################################
    # 1. Retrieve wrapped BEK
    # 2. Make KeyVault REST API call to unwrap the BEK
    # 3. Convert the Base64Url string returned by KeyVault unwrap to Base64 string 
    # 4. Convert Base64 string to bytes and write to the BEK file
    ########################################################################################################################

    #Get wrapped BEK and place it in JSON object to send to KeyVault REST API
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $keyVaultName -Name $secretName
    $bstr = [Runtime.InteropServices.Marshal]::SecureStringToBSTR($keyVaultSecret.SecretValue)
    $wrappedBekSecretBase64 = [Runtime.InteropServices.Marshal]::PtrToStringAuto($bstr)
    $jsonObject = @"
    {
    "alg": "RSA-OAEP",
    "value" : "$wrappedBekSecretBase64"
    }
    "@

    #Get KEK Url
    $kekUrl = (Get-AzKeyVaultKey -VaultName $keyVaultName -Name $kekName).Key.Kid;
    $unwrapKeyRequestUrl = $kekUrl+ "/unwrapkey?api-version=2015-06-01";

    #Call KeyVault REST API to Unwrap 
    $result = Invoke-RestMethod -Method POST -Uri $unwrapKeyRequestUrl -Headers $headers -Body $jsonObject -ContentType "application/json" -Debug

    #Convert Base64Url string returned by KeyVault unwrap to Base64 string
    $base64UrlBek = $result.value;
    $base64Bek = $base64UrlBek.Replace('-', '+');
    $base64Bek = $base64Bek.Replace('_', '/');
    if($base64Bek.Length %4 -eq 2)
    {
        $base64Bek+= '==';
    }
    elseif($base64Bek.Length %4 -eq 3)
    {
        $base64Bek+= '=';
    }

    #Convert base64 string to bytes and write to BEK file
    $bekFileBytes = [System.Convert]::FromBase64String($base64Bek);
    [System.IO.File]::WriteAllBytes($bekFilePath,$bekFileBytes)

    #Delete the key from the memory
    [Runtime.InteropServices.Marshal]::ZeroFreeBSTR($bstr)
    clear-variable -name wrappedBekSecretBase64
    ```
3. Desa parte dos parâmetros. O script processará o segredo KEK para criar a chave BEK e, em seguida, guardá-lo-á para uma pasta local no VM de recuperação. Se receber erros ao executar o script, consulte a secção [de resolução de problemas](#script-troubleshooting) do script.

4. Vê-se a seguinte saída quando o script começa:

    Localização da versão GAC                                                                              
    ---    -------        --------                                                                              
    Falso v4.0.30319 C:\Ficheiros do programa\WindowsPowerShell\Modules\Az.Accounts \. ..  Falso v4.0.30319 C:\Ficheiros do programa\WindowsPowerShell\Modules\Az.Accounts \. ..

    Quando o guião terminar, vê-se a seguinte saída:

    ```output
    VERBOSE: POST https://myvault.vault.azure.net/keys/rondomkey/<KEY-ID>/unwrapkey?api-
    version=2015-06-01 with -1-byte payload
    VERBOSE: received 360-byte response of content type application/json; charset=utf-8
    ```

5. Para desbloquear o disco anexo utilizando o ficheiro BEK, execute o seguinte comando:

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    ```
    Nesta amostra, o disco de so em anexo é unidade F. Certifique-se de que utiliza a letra de acionamento correta. 

6. Depois de o disco ter sido desbloqueado com sucesso utilizando a tecla BEK, retire o disco do VM de recuperação e, em seguida, recrie o VM utilizando este novo disco DE. 

    > [!NOTE]
    > A troca de discos OS não é suportada por VMs utilizando encriptação de disco.

7. Se o novo VM ainda não conseguir arrancar normalmente, experimente um dos seguintes passos depois de desbloquear a unidade:

    - Suspender a proteção para desligar temporariamente o BitLocker, executando o seguinte comando:

    ```console
    manage-bde -protectors -disable F: -rc 0
    ```

    - Desencriptar completamente a unidade. Para tal, execute o seguinte comando:

    ```console
    manage-bde -off F:
    ```

## <a name="script-troubleshooting"></a>Resolução de problemas do script

**Erro: Não foi possível carregar ficheiro ou montagem**

Este erro ocorre porque os caminhos das ADAL Assemblies estão errados. Pode procurar `Az.Accounts` por pasta para encontrar o caminho certo.

**Erro: Get-AzKeyVaultSecret ou Get-AzKeyVaultSecret não é reconhecido como o nome de um cmdlet**

Se estiver a utilizar o antigo módulo AZ PowerShell, tem de alterar os dois comandos para `Get-AzureKeyVaultSecret` e `Get-AzureKeyVaultSecret` .

**Amostras de parâmetros**

| Parâmetros  | Amostra de valor  |Comentários   |
|---|---|---|
|  $keyVaultName | myKeyVault2112852926  | O nome da chave Vault que armazena a chave |
|$kekName   |mykey   | O nome da chave que é usada para encriptar o VM|
|$secretName   |7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D  | O nome do segredo da chave VM|
|$bekFilePath   |c:\bek\7EB4F531-5FBA-4970-8E2D-C11FD6B0C69D. BEK |O caminho para a escrita do arquivo BEK.|
|$adTenant  |contoso.onmicrosoft.com   | FQDN ou GUID do seu Diretório Ativo Azure que acolhe o cofre-chave |
