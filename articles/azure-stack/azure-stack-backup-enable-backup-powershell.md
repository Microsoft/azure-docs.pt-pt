---
title: Ativar cópia de segurança para o Azure Stack com o PowerShell | Documentos da Microsoft
description: Ative o serviço de cópia de segurança de infraestrutura com o Windows PowerShell para que o Azure Stack pode ser restaurado se ocorrer uma falha.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2019
ms.author: jeffgilb
ms.reviewer: hectorl
ms.lastreviewed: 02/08/2019
ms.openlocfilehash: 38ab7b80e2f03176c3bedfd98a2d0e20fc02592b
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56865897"
---
# <a name="enable-backup-for-azure-stack-with-powershell"></a>Ativar cópia de segurança para o Azure Stack com o PowerShell

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Ativar o serviço de cópia de segurança da infraestrutura com o Windows PowerShell reserve cópias de segurança periódicas de:
 - Certificado de serviço e a raiz de identidades interno
 - Planos de utilizador, ofertas, as subscrições
 - Computação, armazenamento e as quotas de utilizador de rede
 - Segredos de Cofre de chaves de utilizador
 - Políticas e funções de RBAC do utilizador
 - Contas de armazenamento do utilizador

Pode acessar os cmdlets do PowerShell para ativar cópia de segurança, inicie a cópia de segurança e obter informações de cópia de segurança através de ponto final de gestão de operador.

## <a name="prepare-powershell-environment"></a>Preparar o ambiente do PowerShell

Para obter instruções sobre como configurar o ambiente do PowerShell, consulte [instalar o PowerShell para o Azure Stack](azure-stack-powershell-install.md). Para iniciar sessão no Azure Stack, veja [configurar o ambiente de operador e inicie sessão no Azure Stack](azure-stack-powershell-configure-admin.md).

## <a name="provide-the-backup-share-credentials-and-encryption-key-to-enable-backup"></a>Forneça a chave de encriptação, credenciais e partilha de cópia de segurança para ativar a cópia de segurança

Na mesma sessão do PowerShell, edite o seguinte script do PowerShell ao adicionar as variáveis para o seu ambiente. Execute o script atualizado para fornecer a chave de encriptação, credenciais e partilha de cópia de segurança para o serviço de cópia de segurança da infraestrutura.

| Variável        | Descrição   |
|---              |---                                        |
| $username       | Tipo de **nome de utilizador** com o domínio e o nome de utilizador para o local de unidade compartilhada com acesso suficiente para ler e escrever ficheiros. Por exemplo, `Contoso\backupshareuser`. |
| $password       | Tipo de **palavra-passe** para o utilizador. |
| $sharepath      | Escreva o caminho para o **localização de armazenamento de cópia de segurança**. Tem de utilizar uma cadeia de caracteres de convenção de Nomenclatura Universal (UNC) para o caminho para uma partilha de ficheiros hospedado num dispositivo separado. Uma cadeia de caracteres UNC Especifica a localização de recursos, tais como ficheiros partilhados ou dispositivos. Para garantir a disponibilidade dos dados de cópia de segurança, o dispositivo deve estar num local separado. |
| $frequencyInHours | A frequência em horas determina com que frequência as cópias de segurança são criadas. O valor predefinido é 12. O Scheduler suporta um máximo de 12 e um mínimo de 4.|
| $retentionPeriodInDays | O período de retenção em dias determina o número de dias de cópias de segurança é mantido na localização externa. O valor predefinido é de 7. O Scheduler suporta um máximo de 14 e um mínimo de 2. Mais antigas do que o período de retenção de cópias de segurança são eliminadas automaticamente da localização externa.|
| $encryptioncertpath | O caminho do certificado de encriptação Especifica o caminho de ficheiro para o. Arquivo CER com a chave pública utilizada para encriptação de dados. |
|     |     |

```powershell
    # Example username:
    $username = "domain\backupadmin"
 
    # Example share path:
    $sharepath = "\\serverIP\AzSBackupStore\contoso.com\seattle"

    $password = Read-Host -Prompt ("Password for: " + $username) -AsSecureString

    # Create a self-signed certificate using New-SelfSignedCertificate, export the public key portion and save it locally.

    $cert = New-SelfSignedCertificate `
        -DnsName "www.contoso.com" `
        -CertStoreLocation "cert:\LocalMachine\My" 

    New-Item -Path "C:\" -Name "Certs" -ItemType "Directory" 

    #make sure to export the PFX format of the certificate with the public and private keys and then delete the certifcate from the local certificate store of the machine where you created the certificate
    
    Export-Certificate `
        -Cert $cert `
        -FilePath c:\certs\AzSIBCCert.cer 

    # Set the backup settings with the name, password, share, and CER certificate file.
    Set-AzsBackupConfiguration -BackupShare $sharepath -Username $username -Password $password -EncryptionCertPath "c:\temp\cert.cer"
```
   
##  <a name="confirm-backup-settings"></a>Confirme as definições de cópia de segurança

Na mesma sessão do PowerShell, execute os seguintes comandos:

   ```powershell
    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName
   ```

O resultado deverá ser semelhante à saída de exemplo seguinte:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
   ```

## <a name="update-backup-settings"></a>Atualizar as definições de cópia de segurança
Na mesma sessão do PowerShell, pode atualizar os valores predefinidos para o período de retenção e a frequência de cópias de segurança. 

   ```powershell
    #Set the backup frequency and retention period values.
    $frequencyInHours = 10
    $retentionPeriodInDays = 5

    Set-AzsBackupConfiguration -BackupFrequencyInHours $frequencyInHours -BackupRetentionPeriodInDays $retentionPeriodInDays

    Get-AzsBackupConfiguration | Select-Object -Property Path, UserName, AvailableCapacity, BackupFrequencyInHours, BackupRetentionPeriodInDays
   ```

O resultado deverá ser semelhante à saída de exemplo seguinte:

   ```powershell
    Path                        : \\serverIP\AzsBackupStore\contoso.com\seattle
    UserName                    : domain\backupadmin
    AvailableCapacity           : 60 GB
    BackupFrequencyInHours      : 10
    BackupRetentionPeriodInDays : 5
   ```

###<a name="azure-stack-powershell"></a>Azure Stack do PowerShell 
O cmdlet do PowerShell para configurar a cópia de segurança da infraestrutura é AzsBackupConfiguration do conjunto. Em versões anteriores, o cmdlet foi AzsBackupShare do conjunto. Este cmdlet necessita de fornecer um certificado. Se a cópia de segurança de infra-estrutura estiver configurada com uma chave de encriptação, não é possível atualizar a chave de encriptação ou ver a propriedade. Terá de utilizar a versão 1.6 do PowerShell de administrador. 

Se a cópia de segurança de infra-estrutura foi configurada antes de atualizar para 1901, pode utilizar a versão 1.6 do PowerShell de administrador para configurar e ver a chave de encriptação. Versão 1.6 não permitirá a atualização da chave de encriptação para um ficheiro de certificado.
Consulte a [instalar o Azure Stack do PowerShell](azure-stack-powershell-install.md) para obter mais informações sobre como instalar a versão correta do módulo. 


## <a name="next-steps"></a>Passos Seguintes

Aprenda a executar uma cópia de segurança, consulte [cópia de segurança do Azure Stack](azure-stack-backup-back-up-azure-stack.md)

Saiba como verificar se executou a cópia de segurança, consulte [Confirmar cópia de segurança foi concluída no portal de administração](azure-stack-backup-back-up-azure-stack.md)
