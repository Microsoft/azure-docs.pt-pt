---
title: Resolução de problemas Erros de arranque BitLocker num VM Azure Microsoft Docs
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
ms.date: 08/23/2019
ms.author: genli
ms.custom: has-adal-ref
ms.openlocfilehash: ac1105f1fce2ac04abfa8a809161580104952917
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91404906"
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

Para resolver este problema, pare e transloja o VM e, em seguida, inicie-o. Esta operação força o VM a recuperar o ficheiro BEK do Cofre da Chave Azure e, em seguida, colocá-lo no disco encriptado. 

Se este método não resolver o problema, siga estes passos para restaurar manualmente o ficheiro BEK:

1. Tire uma foto do disco do sistema do VM afetado como cópia de segurança. Para mais informações, consulte [Snapshot um disco](../windows/snapshot-copy-managed-disk.md).
2. [Ligue o disco do sistema a um VM de recuperação](troubleshoot-recovery-disks-portal-windows.md). Para executar o comando [de gestão bde](/windows-server/administration/windows-commands/manage-bde) no passo 7, a **função de encriptação BitLocker Drive** deve ser ativada no VM de recuperação.

    Quando anexar um disco gerido, poderá receber uma mensagem de erro "contém definições de encriptação e, portanto, não pode ser utilizada como um disco de dados". Nesta situação, execute o seguinte script para tentar novamente anexar o disco:

    ```Powershell
    $rgName = "myResourceGroup"
    $osDiskName = "ProblemOsDisk"

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
                @{Label ="Volume"; Expression = {$_.Tags.VolumeLetter}}, `
                @{Label ="DiskEncryptionKeyFileName"; Expression = {$_.Tags.DiskEncryptionKeyFileName}}
    ```

    Segue-se a amostra da saída. Localize o nome do ficheiro BEK para o disco anexo. Neste caso, assumimos que a letra de unidade do disco anexo é F, e o ficheiro BEK é EF7B2F5A-50C6-4637-9F13-7F599C12F85C. O BEK.

    ```
    Created             Content Type Volume DiskEncryptionKeyFileName               
    -------             ------------ ------ -------------------------               
    4/5/2018 7:14:59 PM Wrapped BEK  C:\    B4B3E070-836C-4AF5-AC5B-66F6FDE6A971.BEK
    4/7/2018 7:21:16 PM Wrapped BEK  F:\    EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
    4/7/2018 7:26:23 PM Wrapped BEK  G:\    70148178-6FAE-41EC-A05B-3431E6252539.BEK
    4/7/2018 7:26:26 PM Wrapped BEK  H:\    5745719F-4886-4940-9B51-C98AFABE5305.BEK
    ```

    Se vir dois volumes duplicados, o volume que tem o novo timetamp é o ficheiro BEK atual que é utilizado pelo VM de recuperação.

    Se o valor **do tipo de conteúdo** for embrulhado **BEK,** vá para os [cenários da Chave de Encriptação (KEK).](#key-encryption-key-scenario)

    Agora que tem o nome do ficheiro BEK para a unidade, tem de criar o nome do ficheiro secreto. Ficheiro BEK para desbloquear a unidade.

6.  Descarregue o ficheiro BEK para o disco de recuperação. A amostra seguinte guarda o ficheiro BEK na pasta C:\BEK. Certifique-se de que o `C:\BEK\` caminho existe antes de executar os scripts.

    ```powershell
    $vault = "myKeyVault"
    $bek = " EF7B2F5A-50C6-4637-9F13-7F599C12F85C"
    $keyVaultSecret = Get-AzKeyVaultSecret -VaultName $vault -Name $bek
    $bekSecretBase64 = $keyVaultSecret.SecretValueText
    $bekFileBytes = [Convert]::FromBase64String($bekSecretbase64)
    $path = "C:\BEK\DiskEncryptionKeyFileName.BEK"
    [System.IO.File]::WriteAllBytes($path,$bekFileBytes)
    ```

7.  Para desbloquear o disco anexo utilizando o ficheiro BEK, executar o seguinte comando.

    ```powershell
    manage-bde -unlock F: -RecoveryKey "C:\BEK\EF7B2F5A-50C6-4637-9F13-7F599C12F85C.BEK
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

1. Certifique-se de que a conta de utilizador com sessão registada requer a permissão "desembrulhada" nas políticas de Acesso ao Cofre de Chaves no **UTILIZADOR. Permissões-chave. Operações Criptográficas Chave de desembrulhar**.
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
    # Load ADAL Assemblies. The following script assumes that the Azure PowerShell version you installed is 1.0.0. 
    $adal = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.dll"
    $adalforms = "${env:ProgramFiles}\WindowsPowerShell\Modules\Az.Accounts\1.0.0\PreloadAssemblies\Microsoft.IdentityModel.Clients.ActiveDirectory.Platform.dll"
    [System.Reflection.Assembly]::LoadFrom($adal)
    [System.Reflection.Assembly]::LoadFrom($adalforms)

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
    $wrappedBekSecretBase64 = $keyVaultSecret.SecretValueText
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

Este erro ocorre porque os caminhos das ADAL Assemblies estão errados. Se o módulo AZ for instalado apenas para o utilizador atual, as ADAL Assemblies situar-se-ão em `C:\Users\<username>\Documents\WindowsPowerShell\Modules\Az.Accounts\<version>` .

Também pode procurar `Az.Accounts` por pasta para encontrar o caminho correto.

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
