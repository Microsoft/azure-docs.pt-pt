---
title: Reencaminhamento de syslog do Azure Stack
description: Saiba como integrar o Azure Stack com soluções de monitorização utilizando o reencaminhamento do syslog
services: azure-stack
author: PatAltimore
manager: femila
ms.service: azure-stack
ms.topic: article
ms.date: 01/28/2019
ms.author: patricka
ms.reviewer: fiseraci
ms.lastreviewed: 01/28/2019
keywords: ''
ms.openlocfilehash: a47b38acc372e6c1d215c7440657486b5babf3bb
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58009487"
---
# <a name="azure-stack-datacenter-integration---syslog-forwarding"></a>Integração de datacenter do Azure Stack - reencaminhamento do syslog

Este artigo mostra-lhe como utilizar o syslog para integrar a infraestrutura do Azure Stack com soluções de segurança externa já implementadas no seu datacenter. Por exemplo, um sistema de gestão de eventos de informações de segurança (SIEM). O canal de syslog expõe as auditorias, alertas e registos de segurança de todos os componentes da infraestrutura do Azure Stack. Utilize o reencaminhamento do syslog para integrar com soluções de monitorização de segurança e/ou para obter todas as auditorias, alertas e segurança registos de armazená-las para a retenção.

A partir da atualização de 1809, o Azure Stack tem um cliente de syslog integrada que, uma vez configurado, emite as mensagens syslog com a carga no Common Event Format (CEF).

O diagrama a seguir descreve a integração do Azure Stack com um SIEM externo. Existem dois padrões de integração que precisam de ser considerados: o primeiro uma (aquele a azul) é que a infraestrutura do Azure Stack que abrange as máquinas de virtuais de infraestrutura e os nós do Hyper-V. Todas as auditorias, registos de segurança e alertas a partir desses componentes centralmente são recolhidas e expostas por meio de syslog com payload CEF. Este padrão de integração é descrito nesta página do documento.
O segundo padrão de integração é a representado na cor de laranja e aborda os controladores de gestão da placa base (BMCs), o anfitrião de ciclo de vida do hardware (HLH), as máquinas virtuais e/ou aplicações virtuais que executam o parceiro de hardware, monitorização e gestão software e a parte superior dos comutadores (TOR rack). Uma vez que esses componentes são contacto específico, de parceiros de hardware de seu hardware parceiros para obter documentação sobre como integrá-las com um SIEM externo.

![Diagrama de reencaminhamento do syslog](media/azure-stack-integrate-security/syslog-forwarding.png)

## <a name="configuring-syslog-forwarding"></a>Configurar o reencaminhamento do syslog

O cliente de syslog no Azure Stack suporta as seguintes configurações:

1. **Syslog através de TCP, com a autenticação mútua (cliente e servidor) e a encriptação TLS 1.2:** Nesta configuração, o servidor syslog e o cliente de syslog podem verificar a identidade entre si através de certificados. As mensagens são enviadas por um canal criptografado de TLS 1.2.

2. **Syslog através de TCP com a autenticação de servidor e a encriptação TLS 1.2:** Nesta configuração, o cliente de syslog pode verificar a identidade do servidor syslog através de um certificado. As mensagens são enviadas por um canal criptografado de TLS 1.2.

3. **Syslog através de TCP, sem criptografia:** Nesta configuração, o cliente de syslog e identidades de servidor syslog não são verificadas. As mensagens são enviadas em texto não encriptado através de TCP.

4. **Syslog através do UDP, sem criptografia:** Nesta configuração, o cliente de syslog e identidades de servidor syslog não são verificadas. As mensagens são enviadas em texto não encriptado através do UDP.

> [!IMPORTANT]
> A Microsoft recomenda utilizar TCP através da autenticação e encriptação (configuração #1 ou, pelo mínimo, #2) para ambientes de produção proteger contra ataques man-in-the-middle e interceptação de mensagens.

### <a name="cmdlets-to-configure-syslog-forwarding"></a>Cmdlets para configurar o reencaminhamento do syslog
Configurar o reencaminhamento do syslog requer acesso ao ponto final com privilégios (PEP). Foram adicionados dois cmdlets do PowerShell para o PEP para configurar o reencaminhamento do syslog:


```powershell
### cmdlet to pass the syslog server information to the client and to configure the transport protocol, the encryption and the authentication between the client and the server

Set-SyslogServer [-ServerName <String>] [-ServerPort <String>] [-NoEncryption] [-SkipCertificateCheck] [-SkipCNCheck] [-UseUDP] [-Remove]

### cmdlet to configure the certificate for the syslog client to authenticate with the server

Set-SyslogClient [-pfxBinary <Byte[]>] [-CertPassword <SecureString>] [-RemoveCertificate] 
```
#### <a name="cmdlets-parameters"></a>Parâmetros de cmdlets

Parâmetros para *Set-SyslogServer* cmdlet:

| Parâmetro | Descrição | Type | Necessário |
|---------|---------|---------|---------|
|*ServerName* | Endereço IP ou FQDN do servidor syslog | String | sim|
|*ServerPort* | Está a escutar o número de porta do servidor syslog | String | sim|
|*NoEncryption*| Forçar o cliente para enviar mensagens do syslog em texto não encriptado | Sinalizador | não|
|*SkipCertificateCheck*| Ignorar a validação do certificado fornecido pelo servidor syslog durante o handshake TLS inicial | Sinalizador | não|
|*SkipCNCheck*| Ignorar a validação do valor de nome comum do certificado fornecido pelo servidor syslog durante o handshake TLS inicial | Sinalizador | não|
|*UseUDP*| Utilizar o syslog com UDP como protocolo de transporte |Sinalizador | não|
|*remover*| Remover a configuração do servidor do cliente e parar o reencaminhamento do syslog| Sinalizador | não|

Parâmetros para *Set-SyslogClient* cmdlet:

| Parâmetro | Descrição | Type |
|---------|---------| ---------|
| *pfxBinary* | ficheiro PFX que contenha o certificado a ser utilizada pelo cliente como identidade para autenticar o servidor syslog  | Byte[] |
| *CertPassword* |  Palavra-passe para importar a chave privada que está associada com o ficheiro pfx | SecureString |
|*RemoveCertificate* | Remover o certificado do cliente | Sinalizador|

### <a name="configuring-syslog-forwarding-with-tcp-mutual-authentication-and-tls-12-encryption"></a>Configurar o reencaminhamento de syslog com TCP, a autenticação mútua e a encriptação TLS 1.2

Nesta configuração, o cliente de syslog no Azure Stack reencaminha as mensagens para o servidor syslog através de TCP, com a encriptação TLS 1.2. Durante o handshake inicial, o cliente verifica que o servidor fornece um certificado válido e de confiança da mesma forma, o cliente também fornece um certificado para o servidor como prova da sua identidade. Esta configuração é a mais segura, pois fornece uma validação completa de identidade do cliente e o servidor e envia mensagens por um canal criptografado. 

> [!IMPORTANT]
> A Microsoft recomenda utilizar esta configuração para ambientes de produção. 

Configurar o reencaminhamento do syslog com TCP, a autenticação mútua e a encriptação TLS 1.2, execute ambos os cmdlets numa sessão PEP:

```powershell
# Configure the server
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>

# Provide certificate to the client to authenticate against the server
Set-SyslogClient -pfxBinary <Byte[] of pfx file> -CertPassword <SecureString, password for accessing the pfx file>
```

O certificado de cliente tem de ter a mesma raiz fornecido durante a implementação do Azure Stack. Também tem de conter uma chave privada.

```powershell
##Example on how to set your syslog client with the certificate for mutual authentication.
##This example script must be run from your hardware lifecycle host or privileged access workstation.

$ErcsNodeName = "<yourPEP>"
$password = ConvertTo-SecureString -String "<your cloudAdmin account password" -AsPlainText -Force
 
$cloudAdmin = "<your cloudAdmin account name>"
$CloudAdminCred = New-Object System.Management.Automation.PSCredential ($cloudAdmin, $password)
 
$certPassword = $password
$certContent = Get-Content -Path C:\cert\<yourClientCertificate>.pfx -Encoding Byte
 
$params = @{ 
    ComputerName = $ErcsNodeName 
    Credential = $CloudAdminCred 
    ConfigurationName = "PrivilegedEndpoint" 
}

$session = New-PSSession @params
 
$params = @{ 
    Session = $session 
    ArgumentList = @($certContent, $certPassword) 
}
Write-Verbose "Invoking cmdlet to set syslog client certificate..." -Verbose 
Invoke-Command @params -ScriptBlock { 
    param($CertContent, $CertPassword) 
    Set-SyslogClient -PfxBinary $CertContent -CertPassword $CertPassword }
```

### <a name="configuring-syslog-forwarding-with-tcp-server-authentication-and-tls-12-encryption"></a>Configurar o reencaminhamento de syslog com o TCP, autenticação de servidor e a encriptação de TLS 1.2

Nesta configuração, o cliente de syslog no Azure Stack reencaminha as mensagens para o servidor syslog através de TCP, com a encriptação TLS 1.2. Durante o handshake inicial, o cliente também verifica que o servidor fornece um certificado válido e de confiança. Esta configuração impede que o cliente para enviar mensagens para destinos não fidedignos.
Utilizar a autenticação e encriptação de TCP é a configuração predefinida e representa o nível mínimo de segurança recomendados pela Microsoft para um ambiente de produção. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
```

No caso de que pretende testar a integração do seu servidor syslog com o cliente do Azure Stack, utilizando um certificado autoassinado e/ou não fidedigno, pode utilizar estes sinalizadores para ignorar a validação de servidor realizada pelo cliente durante o handshake inicial.

```powershell
 #Skip validation of the Common Name value in the server certificate. Use this flag if you provide an IP address for your syslog server
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCNCheck

 #Skip entirely the server certificate validation
 Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on>
 -SkipCertificateCheck
```

> [!IMPORTANT]
> Microsoft não recomenda a utilização do sinalizador - SkipCertificateCheck para ambientes de produção. 

### <a name="configuring-syslog-forwarding-with-tcp-and-no-encryption"></a>Configurar o reencaminhamento de syslog com TCP e sem encriptação

Nesta configuração, o cliente de syslog no Azure Stack reencaminha as mensagens para o servidor syslog através de TCP, sem criptografia. O cliente não verifica a identidade do servidor nem fornece sua própria identidade para o servidor para a verificação. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -NoEncryption
```

> [!IMPORTANT]
> A Microsoft recomenda-se contra utilizando esta configuração para ambientes de produção. 


### <a name="configuring-syslog-forwarding-with-udp-and-no-encryption"></a>Configurar o reencaminhamento de syslog com UDP e sem encriptação

Nesta configuração, o cliente de syslog no Azure Stack reencaminha as mensagens para o servidor syslog através do UDP, sem criptografia. O cliente não verifica a identidade do servidor nem fornece sua própria identidade para o servidor para a verificação. 

```powershell
Set-SyslogServer -ServerName <FQDN or ip address of syslog server> -ServerPort <Port number on which the syslog server is listening on> -UseUDP
```

Embora seja o mais fácil de configurar o UDP sem criptografia, ele não fornece qualquer proteção contra ataques man-in-the-middle e interceptação de mensagens. 

> [!IMPORTANT]
> A Microsoft recomenda-se contra utilizando esta configuração para ambientes de produção. 


## <a name="removing-syslog-forwarding-configuration"></a>Remover configuração de reencaminhamento do syslog

Para remover a configuração do servidor syslog completamente e parar o reencaminhamento do syslog:

**Remover a configuração do servidor syslog do cliente**

```PowerShell  
Set-SyslogServer -Remove
```

**Remover o certificado de cliente do cliente**

```PowerShell  
Set-SyslogClient -RemoveCertificate
```

## <a name="verifying-the-syslog-setup"></a>A verificar a configuração do syslog

Se ligado com êxito o cliente do syslog para o servidor syslog, deve começar em breve a receção de eventos. Se não vir qualquer evento, certifique-se a configuração do seu cliente de syslog, executando os seguintes cmdlets:

**Certifique-se a configuração do servidor no cliente do syslog**

```PowerShell  
Get-SyslogServer
```

**Certifique-se a configuração de certificado no cliente do syslog**

```PowerShell  
Get-SyslogClient
```

## <a name="syslog-message-schema"></a>Esquema de mensagens do syslog

O reencaminhamento do syslog de infraestrutura do Azure Stack envia mensagens formatadas em comum o formato de evento (CEF).
Cada mensagem do syslog é estruturada com base neste esquema: 

```Syslog
<Time> <Host> <CEF payload>
```

O payload CEF baseia-se na estrutura de abaixo, mas o mapeamento para cada campo varia consoante o tipo de mensagem (eventos do Windows, alerta criado, alerta fechada).

```CEF
# Common Event Format schema
CEF: <Version>|<Device Vendor>|<Device Product>|<Device Version>|<Signature ID>|<Name>|<Severity>|<Extensions>
* Version: 0.0
* Device Vendor: Microsoft
* Device Product: Microsoft Azure Stack
* Device Version: 1.0
```

### <a name="cef-mapping-for-privileged-endpoint-events"></a>Mapeamento de CEF para eventos de ponto final com privilégios

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <PEP Event ID>
* Name: <PEP Task Name>
* Severity: mapped from PEP Level (details see the PEP Severity table below)
```

Tabela de eventos para o ponto final com privilégios:

| Evento | ID de evento PEP | Nome da tarefa PEP | Gravidade |
|-------|--------------| --------------|----------|
|PrivilegedEndpointAccessed|1000|PrivilegedEndpointAccessedEvent|5|
|SupportSessionTokenRequested |1001|SupportSessionTokenRequestedEvent|5|
|SupportSessionDevelopmentTokenRequested |1002|SupportSessionDevelopmentTokenRequestedEvent|5|
|SupportSessionUnlocked |1003|SupportSessionUnlockedEvent|10|
|SupportSessionFailedToUnlock |1004|SupportSessionFailedToUnlockEvent|10|
|PrivilegedEndpointClosed |1005|PrivilegedEndpointClosedEvent|5|
|NewCloudAdminUser |1006|NewCloudAdminUserEvent|10|
|RemoveCloudAdminUser |1007|RemoveCloudAdminUserEvent|10|
|SetCloudAdminUserPassword |1008|SetCloudAdminUserPasswordEvent|5|
|GetCloudAdminPasswordRecoveryToken |1009|GetCloudAdminPasswordRecoveryTokenEvent|10|
|ResetCloudAdminPassword |1010|ResetCloudAdminPasswordEvent|10|

Tabela de gravidade PEP:

| Gravidade | Nível | Valor numérico |
|----------|-------| ----------------|
|0|Não definido|Valor: 0. Indica os registos em todos os níveis|
|10|Crítica|Valor: 1. Indica os registos para um alerta crítico|
|8|Erro| Valor: 2. Indica os registos para um erro|
|5|Aviso|Valor: 3. Indica os registos para um aviso|
|2|Informações|Valor: 4. Indica os registos para uma mensagem meramente informativa|
|0|Verboso|Valor: 5. Indica os registos em todos os níveis|

### <a name="cef-mapping-for-recovery-endpoint-events"></a>Mapeamento de CEF para eventos de ponto final de recuperação

```
Prefix fields
* Signature ID: Microsoft-AzureStack-PrivilegedEndpoint: <REP Event ID>
* Name: <REP Task Name>
* Severity: mapped from REP Level (details see the REP Severity table below)
```

Tabela de eventos para o ponto final de recuperação:

| Evento | ID de evento do representante | Nome da tarefa de representante | Gravidade |
|-------|--------------| --------------|----------|
|RecoveryEndpointAccessed |1011|RecoveryEndpointAccessedEvent|5|
|RecoverySessionTokenRequested |1012|RecoverySessionTokenRequestedEvent |5|
|RecoverySessionDevelopmentTokenRequested |1013|RecoverySessionDevelopmentTokenRequestedEvent|5|
|RecoverySessionUnlocked |1014|RecoverySessionUnlockedEvent |10|
|RecoverySessionFailedToUnlock |1015|RecoverySessionFailedToUnlockEvent|10|
|RecoveryEndpointClosed |1016|RecoveryEndpointClosedEvent|5|

Tabela de representante gravidade:

| Gravidade | Nível | Valor numérico |
|----------|-------| ----------------|
|0|Não definido|Valor: 0. Indica os registos em todos os níveis|
|10|Crítica|Valor: 1. Indica os registos para um alerta crítico|
|8|Erro| Valor: 2. Indica os registos para um erro|
|5|Aviso|Valor: 3. Indica os registos para um aviso|
|2|Informações|Valor: 4. Indica os registos para uma mensagem meramente informativa|
|0|Verboso|Valor: 5. Indica os registos em todos os níveis|

### <a name="cef-mapping-for-windows-events"></a>Mapeamento de CEF para eventos do Windows

```
* Signature ID: ProviderName:EventID
* Name: TaskName
* Severity: Level (for details, see the severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabela de gravidade para eventos do Windows:

| Valor de gravidade CEF | Nível de evento do Windows | Valor numérico |
|--------------------|---------------------| ----------------|
|0|Não definido|Valor: 0. Indica os registos em todos os níveis|
|10|Crítica|Valor: 1. Indica os registos para um alerta crítico|
|8|Erro| Valor: 2. Indica os registos para um erro|
|5|Aviso|Valor: 3. Indica os registos para um aviso|
|2|Informações|Valor: 4. Indica os registos para uma mensagem meramente informativa|
|0|Verboso|Valor: 5. Indica os registos em todos os níveis|

Tabela de extensão personalizada para eventos do Windows no Azure Stack:

| Nome da extensão personalizada | Exemplo de eventos do Windows | 
|-----------------------|---------|
|MasChannel | Sistema|
|MasComputer | test.azurestack.contoso.com|
|MasCorrelationActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasCorrelationRelatedActivityID| C8F40D7C-3764-423B-A4FA-C994442238AF|
|MasEventData| Svchost!! 4132, G, 0!!! EseDiskFlushConsistency!! ESENT!! 0x800000|
|MasEventDescription| As definições de política de grupo para o utilizador foram processadas com êxito. Não havia alterações detetadas desde o último processamento com êxito da política de grupo.|
|MasEventID|1501|
|MasEventRecordID|26637|
|MasExecutionProcessID | 29380|
|MasExecutionThreadID |25480|
|MasKeywords |0x8000000000000000|
|MasKeywordName |Auditoria de êxitos|
|MasLevel |4|
|MasOpcode |1|
|MasOpcodeName |informações|
|MasProviderEventSourceName ||
|MasProviderGuid |AEA1B4FA-97D1-45F2-A64C-4D69FFFD92C9|
|MasProviderName |Microsoft-Windows-GroupPolicy|
|MasSecurityUserId |\<Windows SID\> |
|MasTask |0|
|MasTaskCategory| Criação de processo|
|MasUserData|KB4093112!! 5112!! Instalado!! 0x0!! WindowsUpdateAgent Xpath: /Event/UserData / *|
|MasVersion|0|

### <a name="cef-mapping-for-alerts-created"></a>Mapeamento de CEF para alertas criados

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Alert Severity (for details, see alerts severity table below)
* Extension: Custom Extension Name (for details, see the Custom Extension table below)
```

Tabela de gravidade de alertas:

| Gravidade | Nível |
|----------|-------|
|0|Não definido|
|10|Crítica|
|5|Aviso|

Tabela de extensão personalizada para os alertas criados no Azure Stack:

| Nome da extensão personalizada | Exemplo | 
|-----------------------|---------|
|MasEventDescription|DESCRIÇÃO: Uma conta de utilizador \<TestUser\> foi criado para \<TestDomain\>. É um potencial risco de segurança. – REMEDIAÇÃO: Contacte o suporte. Assistência de cliente é necessária para resolver este problema. Não tente resolver este problema sem sua assistência. Antes de abrir um pedido de suporte, iniciar o processo de recolha de ficheiros de registo através da orientação do https://aka.ms/azurestacklogfiles |

### <a name="cef-mapping-for-alerts-closed"></a>Mapeamento de CEF para os alertas fechados

```
* Signature ID: Microsoft Azure Stack Alert Creation : FaultTypeId
* Name: FaultTypeId : AlertId
* Severity: Information
```

O exemplo abaixo mostra uma mensagem do syslog com payload CEF:
```
2018:05:17:-23:59:28 -07:00 TestHost CEF:0.0|Microsoft|Microsoft Azure Stack|1.0|3|TITLE: User Account Created -- DESCRIPTION: A user account \<TestUser\> was created for \<TestDomain\>. It's a potential security risk. -- REMEDIATION: Please contact Support. Customer Assistance is required to resolve this issue. Do not try to resolve this issue without their assistance. Before you open a support request, start the log file collection process using the guidance from https://aka.ms/azurestacklogfiles|10
```

## <a name="next-steps"></a>Passos Seguintes

[Política de manutenção](azure-stack-servicing-policy.md)