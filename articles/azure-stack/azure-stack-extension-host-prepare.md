---
title: Preparar para o anfitrião de extensão para o Azure Stack | Documentos da Microsoft
description: Aprenda a preparar para o anfitrião de extensão, ativada automaticamente com um pacote de atualização futuro do Azure Stack.
services: azure-stack
keywords: ''
author: mattbriggs
ms.author: mabrigg
ms.date: 03/07/2019
ms.topic: article
ms.service: azure-stack
ms.reviewer: thoroet
manager: femila
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 47cc7d9f09b7fb22cf99ad010f1dc75e6388c314
ms.sourcegitcommit: 1902adaa68c660bdaac46878ce2dec5473d29275
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2019
ms.locfileid: "57731918"
---
# <a name="prepare-for-extension-host-for-azure-stack"></a>Preparar para o anfitrião de extensão para o Azure Stack

O anfitrião de extensão protege o Azure Stack, reduzindo o número de portas de TCP/IP necessárias. Este artigo analisa a preparar o Azure Stack para o anfitrião de extensão é ativado automaticamente por meio de um pacote de atualização do Azure Stack após a atualização 1808. Este artigo aplica-se para as atualizações do Azure Stack 1808 1809 e 1811.

## <a name="certificate-requirements"></a>Requisitos de certificado

O anfitrião de extensão implementa dois novos namespaces de domínios para garantir entradas de anfitrião exclusivo para cada extensão do portal. Os espaços de nomes de domínio novo exigem dois certificados de caráter universal adicionais para garantir uma comunicação segura.

A tabela mostra os novos espaços de nomes e os certificados associados:

| Pasta de implementação | Assunto do certificado necessário e os nomes alternativos do requerente (SAN) | Âmbito (por região) | Espaço de nomes do subdomínio |
|-----------------------|------------------------------------------------------------------|-----------------------|------------------------------|
| Anfitrião de extensão de administração | *.adminhosting. \<região >. \<fqdn > (certificados de SSL de caráter universal) | Anfitrião de extensão de administração | adminhosting.\<region>.\<fqdn> |
| Anfitrião de pública de extensão | * .hosting. \<região >. \<fqdn > (certificados de SSL de caráter universal) | Anfitrião de pública de extensão | hosting.\<region>.\<fqdn> |

Os requisitos de certificado detalhadas podem ser encontrados no [requisitos de certificados de infraestrutura de chaves públicas do Azure Stack](azure-stack-pki-certs.md) artigo.

## <a name="create-certificate-signing-request"></a>Criar pedido de assinatura de certificado

A ferramenta de verificação da preparação do Azure Stack fornece a capacidade de criar um pedido para os dois certificados SSL de novo, obrigatórios de assinatura de certificado. Siga os passos no artigo [geração de pedido de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md).

> [!Note]  
> Pode ignorar este passo consoante a forma como tiver solicitado que os certificados SSL.

## <a name="validate-new-certificates"></a>Validar novos certificados

1. Abra o PowerShell com a permissão elevada no anfitrião de ciclo de vida de hardware ou a estação de trabalho de gestão do Azure Stack.
2. Execute o cmdlet seguinte para instalar a ferramenta de verificação de preparação do Azure Stack.

    ```PowerShell  
    Install-Module -Name Microsoft.AzureStack.ReadinessChecker
    ```

3. Execute o seguinte script para criar a estrutura de pasta necessária:

    ```PowerShell  
    New-Item C:\Certificates -ItemType Directory

    $directories = 'ACSBlob','ACSQueue','ACSTable','Admin Portal','ARM Admin','ARM Public','KeyVault','KeyVaultInternal','Public Portal', 'Admin extension host', 'Public extension host'

    $destination = 'c:\certificates'

    $directories | % { New-Item -Path (Join-Path $destination $PSITEM) -ItemType Directory -Force}
    ```

    > [!Note]  
    > Se implementar com o Azure Active Directory Federated Services (AD FS) os seguintes diretórios tem de ser adicionados ao **$directories** no script: `ADFS`, `Graph`.

4. Coloque os certificados existentes, o que estiver a utilizar no Azure Stack, nos diretórios apropriados. Por exemplo, colocar o **ARM de administrador** in de certificado a `Arm Admin` pasta. E, em seguida, colocar os certificados de alojamento recém-criado a `Admin extension host` e `Public extension host` diretórios.
5. Execute o seguinte cmdlet para iniciar a verificação de certificado:

    ```PowerShell  
    $pfxPassword = Read-Host -Prompt "Enter PFX Password" -AsSecureString 

    Start-AzsReadinessChecker -CertificatePath c:\certificates -pfxPassword $pfxPassword -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
    ```

6. Verificar a saída e todos os certificados passam todos os testes.


## <a name="import-extension-host-certificates"></a>Importar certificados de anfitrião de extensão

Utilize um computador que pode ligar-se ao ponto final do Azure Stack com privilégios para os passos seguintes. Certifique-se de que tem acesso a novos ficheiros de certificado a partir desse computador.

1. Utilize um computador que pode ligar-se ao ponto final do Azure Stack com privilégios para os passos seguintes. Certifique-se de que aceder para os novos ficheiros de certificado a partir desse computador.
2. Abra o ISE do PowerShell para executar o seguinte script permanece bloqueado
3. Importe o certificado para a ponto final de alojamento de administração.

    ```PowerShell  

    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$AdminHostingCertContent = [Byte[]](Get-Content c:\certificate\myadminhostingcertificate.pfx -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($AdminHostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($AdminHostingCertContent, $CertPassword)
            Import-AdminHostingServiceCert $AdminHostingCertContent $certPassword
    }
    ```
4. Importe o certificado para o ponto final de alojamento.
    ```PowerShell  
    $CertPassword = read-host -AsSecureString -prompt "Certificate Password"

    $CloudAdminCred = Get-Credential -UserName <Privileged endpoint credentials> -Message "Enter the cloud domain credentials to access the privileged endpoint."

    [Byte[]]$HostingCertContent = [Byte[]](Get-Content c:\certificate\myhostingcertificate.pfx  -Encoding Byte)

    Invoke-Command -ComputerName <PrivilegedEndpoint computer name> `
    -Credential $CloudAdminCred `
    -ConfigurationName "PrivilegedEndpoint" `
    -ArgumentList @($HostingCertContent, $CertPassword) `
    -ScriptBlock {
            param($HostingCertContent, $CertPassword)
            Import-UserHostingServiceCert $HostingCertContent $certPassword
    }
    ```

### <a name="update-dns-configuration"></a>Atualizar a configuração de DNS

> [!Note]  
> Este passo não é necessário se tiver utilizado a delegação de zona de DNS para a integração do DNS.
Se os registos anfitriões individuais tenham sido configurados para publicar os pontos finais do Azure Stack, terá de criar dois registos de um anfitrião adicional:

| IP | Nome de Anfitrião | Type |
|----|------------------------------|------|
| \<IP> | *.Adminhosting.\<Region>.\<FQDN> | A |
| \<IP> | *.Hosting.\<Region>.\<FQDN> | A |

IPs alocado pode ser obtido com o ponto final com privilégios ao executar o cmdlet **Get-AzureStackStampInformation**.

### <a name="ports-and-protocols"></a>Portas e protocolos

O artigo [integração no datacenter do Azure Stack - publicar pontos de extremidade](azure-stack-integrate-endpoints.md), abrange as portas e protocolos que necessitam de comunicação de entrada para publicar o Azure Stack anteriores à implementação de anfitrião de extensão.

### <a name="publish-new-endpoints"></a>Publicar novos pontos de extremidade

Existem dois novos pontos de extremidade devem ser publicado através da firewall. Os IPs alocado do conjunto VIP público pode ser obtido com o seguinte código que deve ser executado a partir do Azure Stack [ambiente do privilegiado do ponto de extremidade](https://docs.microsoft.com/azure/azure-stack/azure-stack-privileged-endpoint).

```PowerShell
# Create a PEP Session
winrm s winrm/config/client '@{TrustedHosts= "<IpOfERCSMachine>"}'
$PEPCreds = Get-Credential
$PEPSession = New-PSSession -ComputerName <IpOfERCSMachine> -Credential $PEPCreds -ConfigurationName "PrivilegedEndpoint"

# Obtain DNS Servers and Extension Host information from Azure Stack Stamp Information and find the IPs for the Host Extension Endpoints
$StampInformation = Invoke-Command $PEPSession {Get-AzureStackStampInformation} | Select-Object -Property ExternalDNSIPAddress01, ExternalDNSIPAddress02, @{n="TenantHosting";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://*.","testdnsentry"-replace "/"}},  @{n="AdminHosting";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://*.","testdnsentry"-replace "/"}},@{n="TenantHostingDNS";e={($_.TenantExternalEndpoints.TenantHosting) -replace "https://",""-replace "/"}},  @{n="AdminHostingDNS";e={($_.AdminExternalEndpoints.AdminHosting)-replace "https://",""-replace "/"}}
If (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting -ErrorAction SilentlyContinue) {
    Write-Host "Can access AZS DNS" -ForegroundColor Green
    $AdminIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress02 -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Server $StampInformation.ExternalDNSIPAddress01 -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Else {
    Write-Host "Cannot access AZS DNS" -ForegroundColor Yellow
    $AdminIP = (Resolve-DnsName -Name $StampInformation.AdminHosting).IPAddress
    Write-Host "The IP for the Admin Extension Host is: $($StampInformation.AdminHostingDNS) - is: $($AdminIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.AdminHostingDNS), Value: $($AdminIP)" -ForegroundColor Green
    $TenantIP = (Resolve-DnsName -Name $StampInformation.TenantHosting).IPAddress
    Write-Host "The IP address for the Tenant Extension Host is $($StampInformation.TenantHostingDNS) - is: $($TenantIP)" -ForegroundColor Yellow
    Write-Host "The Record to be added in the DNS zone: Type A, Name: $($StampInformation.TenantHostingDNS), Value: $($TenantIP)" -ForegroundColor Green
}
Remove-PSSession -Session $PEPSession
```

#### <a name="sample-output"></a>Saída de exemplo

```PowerShell
Can access AZS DNS
The IP for the Admin Extension Host is: *.adminhosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.adminhosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
The IP address for the Tenant Extension Host is *.hosting.\<region>.\<fqdn> - is: xxx.xxx.xxx.xxx
The Record to be added in the DNS zone: Type A, Name: *.hosting.\<region>.\<fqdn>, Value: xxx.xxx.xxx.xxx
```

> [!Note]  
> Efetue esta alteração antes de ativar o anfitrião de extensão. Isso permite que os portais do Azure Stack a estar acessíveis de forma contínua.

| Ponto final (VIP) | Protocolo | Portas |
|----------------|----------|-------|
| Admin Hosting | HTTPS | 443 |
| Alojamento | HTTPS | 443 |

### <a name="update-existing-publishing-rules-post-enablement-of-extension-host"></a>Atualizar as regras de publicação existentes (ativação de postagem de anfitrião de extensão)

> [!Note]  
> O 1808 faz de pacote de atualização do Azure Stack **não** ativar ainda o anfitrião de extensão. Ele permite que a preparar para o anfitrião de extensão ao importar os certificados necessários. Não feche as portas antes de anfitrião de extensão é ativada automaticamente por meio de um pacote de atualização de pilha do Azure após a atualização 1808.

As seguintes portas de ponto final existente tem de ser fechadas nas suas regras de firewall existentes.

> [!Note]  
> Recomenda-se para fechar as portas após a validação com êxito.

| Ponto final (VIP) | Protocolo | Portas |
|----------------------------------------|----------|-------------------------------------------------------------------------------------------------------------------------------------|
| Portal (administrador) | HTTPS | 12495<br>12499<br>12646<br>12647<br>12648<br>12649<br>12650<br>13001<br>13003<br>13010<br>13011<br>13012<br>13020<br>13021<br>13026<br>30015 |
| Portal (utilizador) | HTTPS | 12495<br>12649<br>13001<br>13010<br>13011<br>13012<br>13020<br>13021<br>30015<br>13003 |
| O Azure Resource Manager (administrador) | HTTPS | 30024 |
| Azure Resource Manager (user) | HTTPS | 30024 |

## <a name="next-steps"></a>Passos Seguintes

- Saiba mais sobre [integração da Firewall](azure-stack-firewall.md).
- Saiba mais sobre [geração de pedido de assinatura de certificados do Azure Stack](azure-stack-get-pki-certs.md)
