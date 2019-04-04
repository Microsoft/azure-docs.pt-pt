---
title: Referência do cmdlet Start-AzsReadinessChecker | Documentos da Microsoft
description: Ajuda do cmdlet do PowerShell para o módulo do Verificador de preparação do Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/30/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: f920059f97f43a2ac3c48dad1c8f999833f6add1
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2019
ms.locfileid: "58757776"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Referência do cmdlet Start-AzsReadinessChecker

Módulo: **Microsoft.AzureStack.ReadinessChecker**

Este módulo contém apenas um cmdlet único. O cmdlet executa uma ou mais funções de pré-manutenção ou de pré-implantação para o Azure Stack.

## <a name="syntax"></a>Sintaxe

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       [-CertificatePath <String>]
       -PfxPassword <SecureString>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegionName <String>
       -FQDN <String>
       -IdentitySystem <String>
       -Subject <OrderedDictionary>
       -RequestType <String>
       [-IncludePaaS]
       -OutputRequestPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -AADDirectoryTenantName <String>
       -IdentitySystem <String>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -AzureEnvironment <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -RegistrationAccount <PSCredential>
       -RegistrationSubscriptionID <Guid>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```powershell
Start-AzsReadinessChecker
       -ReportPath <String>
       [-ReportSections <String>]
       [-Summary]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

## <a name="description"></a>Descrição

O **Start-AzsReadinessChecker** cmdlet valida certificados, as contas do Azure, as subscrições do Azure e diretórios Active Directory do Azure. Execute testes de validação antes de implementar o Azure Stack ou antes de ações, como o segredo rotação de manutenção do Azure Stack. O cmdlet também pode ser utilizado para gerar pedidos de certificados de infraestrutura de assinatura de certificado e, opcionalmente, os certificados de PaaS. Por fim, o cmdlet pode reempacotar os certificados PFX para remediar problemas de empacotamento comuns.

## <a name="examples"></a>Exemplos

### <a name="example-generate-certificate-signing-request"></a>Exemplo: gerar o pedido de assinatura de certificado

```powershell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Neste exemplo, `Start-AzsReadinessChecker` gera vários pedidos (os CSRs) de assinatura de certificado para certificados adequados para uma implementação do AD FS Azure Stack com o nome região **Leste** e um FQDN externo do  **azurestack.contoso.com**.

### <a name="example-validate-certificates"></a>Exemplo: validar certificados

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Neste exemplo, a palavra-passe PFX é necessária para segurança, e `Start-AzsReadinessChecker` verifica a pasta relativa **certificados** certificados válido para uma implementação do AAD com o nome região **Leste** e uma FQDN externo do **azurestack.contoso.com**.

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Exemplo: validar certificados com dados de implementação (implementação e suporte)

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

Neste exemplo de implementação e suporte, a palavra-passe PFX é necessária para segurança, e `Start-AzsReadinessChecker` verifica a pasta relativa **certificados** para certificados válidos para uma implementação em que a identidade, região e FQDN externo são ler o ficheiro de JSON de dados de implementação gerado para a implementação do.

### <a name="example-validate-paas-certificates"></a>Exemplo: validar certificados de PaaS

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

Neste exemplo, uma tabela de hash é construída com caminhos e palavras-passe para cada certificado de PaaS. Certificados podem ser omitidos. `Start-AzsReadinessChecker` verifica que cada caminho PFX existe e valida-las com a região **Leste** e o FQDN externo **azurestack.contoso.com**.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Exemplo: validar certificados de PaaS com dados de implementação

```powershell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

Neste exemplo, uma tabela de hash é construída com caminhos e palavras-passe para cada certificado de PaaS. Certificados podem ser omitidos. `Start-AzsReadinessChecker` verifica se cada caminho PFX existe e valida-las com a região e FQDN externo ler o ficheiro JSON de dados de implementação gerado para a implementação.

### <a name="example-validate-azure-identity"></a>Exemplo: validar a identidade do Azure

```powershell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

Neste exemplo, as credenciais de conta de administrador de serviço são necessárias para segurança, e `Start-AzsReadinessChecker` verifica que a conta do Azure e o Azure Active Directory são válidas para uma implementação do AAD com um nome de diretório do inquilino de  **azurestack.contoso.com**.

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Exemplo: validar a identidade do Azure com dados de implementação (o suporte de implementação)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

Neste exemplo, as credenciais de conta de administrador de serviço são necessárias para segurança, e `Start-AzsReadinessChecker` verifica que a conta do Azure e o Azure Active Directory são válidas para uma implementação do AAD, onde **AzureCloud** e **TenantName** são de leitura do ficheiro JSON de dados de implementação gerado para a implementação.

### <a name="example-validate-azure-registration"></a>Exemplo: validar o registo do Azure

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

Neste exemplo, as credenciais de proprietário da subscrição são necessárias para segurança, e `Start-AzsReadinessChecker` , em seguida, executa a validação em relação a determinado conta e subscrição para garantir que ele pode ser usado para o registo do Azure Stack.

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Exemplo: validar o registo do Azure com dados de implementação (equipe de implantação)

```powershell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Neste exemplo, as credenciais de proprietário da subscrição são necessárias para segurança, e `Start-AzsReadinessChecker` , em seguida, executa a validação em relação a determinado conta e subscrição para garantir que ele pode ser usado para o registo do Azure Stack, onde detalhes adicionais são lidos a partir do ficheiro JSON do data de implementação gerado para a implementação.

### <a name="example-importexport-pfx-package"></a>Exemplo: Importar/Exportar pacote PFX

```powershell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Neste exemplo, a palavra-passe PFX é necessária para segurança. O ficheiro de Ssl.pfx é importado para o arquivo de certificados do computador local, exportado novamente com a mesma palavra-passe e guardado como Ssl_new.pfx. Este procedimento é utilizado quando sinalizado de validação do certificado que não tem uma chave privada a **computador Local** conjunto de atributos, a cadeia de certificados foi interrompida, irrelevantes certificados estão presentes no PFX ou é a cadeia de certificados na ordem errada.

### <a name="example-view-validation-report-deployment-support"></a>Exemplo: ver o relatório de validação (suporte de implementação)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Neste exemplo, a equipe de implantação ou de suporte recebe o relatório de preparação do cliente (Contoso) e utiliza `Start-AzsReadinessChecker` para ver o estado das execuções de validação executada da Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Exemplo: visualize o relatório de validação do resumo do certificado de validação única (implementação e suporte)

```powershell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Neste exemplo, a equipe de implantação ou de suporte recebe o relatório de preparação do cliente (Contoso) e utiliza `Start-AzsReadinessChecker` para ver um Estado resumido das execuções de validação de certificados efetuada da Contoso.

## <a name="required-parameters"></a>Parâmetros necessários

### <a name="-regionname"></a>-RegionName

Especifica o nome de região de implementação do Azure Stack.

|  |  |
|----------------------------|--------------|
|Escreva:                       |String        |
|Posição:                   |com o nome         |
|Valor predefinido:              |Nenhuma          |
|Aceite entrada de pipeline:      |Falso         |
|Aceite carateres universais: |Falso         |

### <a name="-fqdn"></a>-FQDN

Especifica a implementação do Azure Stack FQDN externo, também utilizada como alias **ExternalFQDN** e **ExternalDomainName**.

|  |  |
|----------------------------|--------------|
|Escreva:                       |String        |
|Posição:                   |com o nome         |
|Valor predefinido:              |ExternalFQDN, ExternalDomainName |
|Aceite entrada de pipeline:      |Falso         |
|Aceite carateres universais: |Falso         |

### <a name="-identitysystem"></a>-IdentitySystem

Especifica os Azure Stack implementação identidade sistema válidos valores, AAD ou ADFS, para o Azure Active Directory e o Active Directory Federated Services, respectivamente.

|  |  |
|----------------------------|--------------|
|Escreva:                       |String        |
|Posição:                   |com o nome         |
|Valor predefinido:              |Nenhuma          |
|Valores válidos:               |'AAD','ADFS'  |
|Aceite entrada de pipeline:      |Falso         |
|Aceite carateres universais: |Falso         |

### <a name="-pfxpassword"></a>-PfxPassword

Especifica a palavra-passe associada aos ficheiros de certificado PFX.

|  |  |
|----------------------------|---------|
|Escreva:                       |SecureString |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-paascertificates"></a>-PaaSCertificates

Especifica a tabela de hash contendo caminhos e palavras-passe para certificados de PaaS.

|  |  |
|----------------------------|---------|
|Escreva:                       |Tabela de hash |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-deploymentdatajsonpath"></a>-DeploymentDataJSONPath

Especifica o ficheiro de configuração do Azure Stack implementação dados JSON. Este ficheiro é gerado para a implementação.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-pfxpath"></a>-PfxPath

Especifica o caminho para um certificado problemático que requer uma rotina de importação/exportação para corrigir, conforme indicado pela validação de certificado nesta ferramenta.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-exportpfxpath"></a>-ExportPFXPath  

Especifica o caminho de destino para o ficheiro PFX resultante da rotina de importação/exportação.  

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-subject"></a>-Assunto

Especifica um dicionário ordenado do assunto para a geração de pedido de certificado.

|  |  |
|----------------------------|---------|
|Escreva:                       |OrderedDictionary   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-requesttype"></a>-RequestType

Especifica o tipo de SAN, o pedido de certificado. Os valores válidos são **MultipleCSR**, **SingleCSR**.

- **MultipleCSR** gera vários pedidos de certificado, uma para cada serviço.
- **SingleCSR** gera um pedido de certificado para todos os serviços.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Valores válidos:               |'MultipleCSR','SingleCSR' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-outputrequestpath"></a>-OutputRequestPath

Especifica o caminho de destino para os ficheiros de pedido de certificado. Diretório tem de existir.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-aadserviceadministrator"></a>-AADServiceAdministrator

Especifica o administrador de serviços do Azure Active Directory a utilizar para a implementação do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |PSCredential   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-aaddirectorytenantname"></a>-AADDirectoryTenantName

Especifica o nome do Azure Active Directory a utilizar para a implementação do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-azureenvironment"></a>-AzureEnvironment

Especifica a instância dos serviços do Azure que contém as contas, diretórios e subscrições para ser utilizado para implementação do Azure Stack e o Registro.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Valores válidos:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-registrationaccount"></a>-RegistrationAccount

Especifica a conta de registo a ser utilizadas para registo do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-registrationsubscriptionid"></a>-RegistrationSubscriptionID

Especifica o ID de subscrição de registo a ser utilizadas para registo do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |Guid     |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-reportpath"></a>-ReportPath

Especifica o caminho para o relatório de preparação, é predefinido para o nome do relatório atual diretório e padrão.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Todos      |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

## <a name="optional-parameters"></a>Parâmetros opcionais

### <a name="-certificatepath"></a>-CertificatePath

Especifica o caminho em que apenas o certificado necessário pastas de certificados estão presentes.

As pastas necessárias para a implementação do Azure Stack com o sistema de identidade do Azure Active Directory são:

ACSBlob, ACSQueue, ACSTable, Portal de administração, ARM de administrador, ARM público, Cofre de chaves, KeyVaultInternal, Portal público

Necessário pasta para o Azure Stack são de implementação no sistema de identificação de serviços de Federação do Active Directory:

ACSBlob, ACSQueue, ACSTable, ADFS, do Portal de administração, ARM de administrador, público ARM, gráfico, Cofre de chaves, KeyVaultInternal, Portal público

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |.\Certificates |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-includepaas"></a>-IncludePaaS  

Especifica se os nomes de anfitrião/serviços de PaaS devem ser adicionados para o pedido de certificado (s).

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

### <a name="-reportsections"></a>-ReportSections

Especifica se a Mostrar relatório de resumo, apenas omite detalhes.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Todos      |
|Valores válidos:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

### <a name="-summary"></a>-Resumo

Especifica se a Mostrar relatório de resumo, apenas omite detalhes.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

### <a name="-cleanreport"></a>-CleanReport

Remove o histórico de execução e a validação anterior e escreve validações um novo relatório.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

### <a name="-outputpath"></a>-OutputPath

Especifica um caminho personalizado para guardar o relatório de JSON de preparação e o ficheiro de registo verboso. Se o caminho ainda não existir, o comando tenta criar o diretório.

|  |  |
|----------------------------|------------------|
|Escreva:                       |String            |
|Posição:                   |com o nome             |
|Valor predefinido:              |$ENV:TEMP\AzsReadinessChecker  |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

### <a name="-confirm"></a>-Confirm

Pedidos de confirmação antes de executar o cmdlet.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

### <a name="-whatif"></a>-WhatIf

Mostra o que aconteceria se a execução do cmdlet. O cmdlet não é executado.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Aliases:                    |wi                |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |
