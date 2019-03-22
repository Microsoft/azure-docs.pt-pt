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
ms.date: 12/04/2018
ms.author: sethm
ms.reviewer: unknown
ms.lastreviewed: 12/04/2018
ms.openlocfilehash: 0b75085754a66fabf07076282c977acd7f10a556
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57992324"
---
# <a name="start-azsreadinesschecker-cmdlet-reference"></a>Referência do cmdlet Start-AzsReadinessChecker

Módulo: Microsoft.AzureStack.ReadinessChecker

Este módulo contém apenas um cmdlet único.  Este cmdlet executa uma ou mais funções de pré-manutenção ou de pré-implantação para o Azure Stack.

## <a name="syntax"></a>Sintaxe

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PaaSCertificates <Hashtable>
       -DeploymentDataJSONPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -PfxPassword <SecureString>
       -PfxPath <String>
       -ExportPFXPath <String>
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
Start-AzsReadinessChecker
       -AADServiceAdministrator <PSCredential>
       -DeploymentDataJSONPath <String>
       [-CleanReport]
       [-OutputPath <String>]
       [-WhatIf]
       [-Confirm]
       [<CommonParameters>]
```

```PowerShell
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

```PowerShell
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

```PowerShell
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

O **Start-AzsReadinessChecker** cmdlet valida certificados, as contas do Azure, as subscrições do Azure e diretórios Active Directory do Azure. Execute testes de validação antes de implementar o Azure Stack ou antes de ações, como o segredo de rotação de manutenção do Azure Stack. O cmdlet também pode ser utilizado para gerar pedidos de certificados de assinatura de certificados de infraestrutura e, opcionalmente, os certificados de PaaS.  Por fim, o cmdlet pode reempacotar os certificados PFX para remediar problemas de empacotamento comuns.

## <a name="examples"></a>Exemplos

### <a name="example-generate-certificate-signing-request"></a>Exemplo: Gerar o pedido de assinatura de certificado

```PowerShell
$regionName = 'east'
$externalFQDN = 'azurestack.contoso.com'
$subjectHash = [ordered]@{"OU"="AzureStack";"O"="Microsoft";"L"="Redmond";"ST"="Washington";"C"="US"}
Start-AzsReadinessChecker -regionName $regionName -externalFQDN $externalFQDN -subject $subjectHash -IdentitySystem ADFS -requestType MultipleCSR
```

Neste exemplo, início AzsReadinessChecker gera vários certificados de assinatura pedidos (CSR) para certificados adequados para uma implantação do ADFS do Azure Stack com um nome da região de "Leste" e um FQDN externo do "azurestack.contoso.com"

### <a name="example-validate-certificates"></a>Exemplo: Validar certificados

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -RegionName east -FQDN azurestack.contoso.com -IdentitySystem AAD
```

Neste exemplo, a palavra-passe PFX é pedida com segurança e início AzsReadinessChecker verifica a pasta relativa "Certificados" para os certificados válidos para uma implementação de AAD com um nome de região "Leste" e um FQDN externo do "azurestack.contoso.com"

### <a name="example-validate-certificates-with-deployment-data-deployment-and-support"></a>Exemplo: Validar certificados com dados de implementação (implementação e suporte)

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -CertificatePath .\Certificates\ -PfxPassword $password -DeploymentDataJSONPath .\deploymentdata.json
```

Neste exemplo de implementação e suporte, a palavra-passe PFX é pedida com segurança e início AzsReadinessChecker verifica a pasta relativa "Certificados" para os certificados válidos para uma implementação em que são lidos a identidade, região e FQDN externo partir do ficheiro JSON do data de implementação gerado para a implementação. 

### <a name="example-validate-paas-certificates"></a>Exemplo: Validar certificados de PaaS

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates – RegionName east -FQDN azurestack.contoso.com
```

Neste exemplo, uma tabela de hash é construída com caminhos e palavras-passe para cada certificado de PaaS. Certificados podem ser omitidos. Start-AzsReadinessChecker verifica cada caminho de PFX existe e valida-las com a região 'Leste' e o FQDN externo 'azurestack.contoso.com'.

### <a name="example-validate-paas-certificates-with-deployment-data"></a>Exemplo: Validar certificados de PaaS com dados de implementação

```PowerShell
$PaaSCertificates = @{
    'PaaSDBCert' = @{'pfxPath' = '<Path to DBAdapter PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSDefaultCert' = @{'pfxPath' = '<Path to Default PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSAPICert' = @{'pfxPath' = '<Path to API PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSFTPCert' = @{'pfxPath' = '<Path to FTP PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
    'PaaSSSOCert' = @{'pfxPath' = '<Path to SSO PFX>';'pfxPassword' = (ConvertTo-SecureString -String '<Password for PFX>' -AsPlainText -Force)}
}
Start-AzsReadinessChecker -PaaSCertificates $PaaSCertificates -DeploymentDataJSONPath .\deploymentdata.json
```

Neste exemplo, uma tabela de hash é construída com caminhos e palavras-passe para cada certificado de PaaS. Certificados podem ser omitidos. Start-AzsReadinessChecker verifica cada caminho de PFX existe e valida-las com a região e FQDN externo de leitura do ficheiro JSON de dados de implementação gerado para a implementação. 

### <a name="example-validate-azure-identity"></a>Exemplo: Validar a identidade do Azure

```PowerShell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -AzureEnvironment "<environment name>" -AzureDirectoryTenantName azurestack.contoso.com
```

Neste exemplo, as credenciais da conta de administrador de serviço recebem um pedido de forma segura e início AzsReadinessChecker verifica que a conta do Azure e Azure Active Directory são válidos para uma implementação de AAD com um nome de inquilino do diretório de "azurestack.contoso.com"

### <a name="example-validate-azure-identity-with-deployment-data-deployment-support"></a>Exemplo: Validar a identidade do Azure com dados de implementação (o suporte de implementação)

```PowerSHell
$serviceAdminCredential = Get-Credential -Message "Enter Credentials for Service Administrator of Azure Active Directory Tenant e.g. serviceadmin@contoso.onmicrosoft.com"
Start-AzsReadinessChecker -AADServiceAdministrator $serviceAdminCredential -DeploymentDataJSONPath .\contoso-depploymentdata.json
```

Neste exemplo, as credenciais da conta de administrador de serviço recebem um pedido de forma segura e início AzsReadinessChecker verifica que a conta do Azure e o Azure Active Directory são válidas para uma implementação de AAD onde a AzureCloud e TenantName são leem os dados de implementação Ficheiro JSON gerado para a implementação.

### <a name="example-validate-azure-registration"></a>Exemplo: Validar o registo do Azure

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID"
# Supported values for the <environment name> parameter are AzureCloud, AzureChinaCloud or AzureUSGovernment depending which Azure subscription you are using.
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -AzureEnvironment "<environment name>"
```

Neste exemplo, as credenciais de proprietário da subscrição recebem um pedido de forma segura e AzsReadinessChecker de início, em seguida, executa a validação em relação à determinada conta e subscrição para se certificar de que pode ser utilizada para o registo do Azure Stack. 

### <a name="example-validate-azure-registration-with-deployment-data-deployment-team"></a>Exemplo: Validar o registo do Azure com dados de implementação (equipe de implantação)

```PowerShell
$registrationCredential = Get-Credential -Message "Enter Credentials for Subscription Owner e.g. subscriptionowner@contoso.onmicrosoft.com"
$subscriptionID = "<subscription ID>"
Start-AzsReadinessChecker -RegistrationAccount $registrationCredential -RegistrationSubscriptionID $subscriptionID -DeploymentDataJSONPath .\contoso-deploymentdata.json
```

Neste exemplo, as credenciais de proprietário da subscrição recebem um pedido de forma segura e AzsReadinessChecker de início, em seguida, executa a validação em relação à determinada conta e subscrição para se certificar de que pode ser utilizada para o registo do Azure Stack onde estão os detalhes adicionais ler o ficheiro de JSON de dados de implementação gerado para a implementação do.

### <a name="example-importexport-pfx-package"></a>Exemplo: Pacote de importação/exportação PFX

```PowerShell
$password = Read-Host -Prompt "Enter PFX Password" -AsSecureString
Start-AzsReadinessChecker -PfxPassword $password -PfxPath .\certificates\ssl.pfx -ExportPFXPath .\certificates\ssl_new.pfx
```

Neste exemplo, a palavra-passe PFX é solicitada em segurança. O ficheiro de ssl.pfx será importado para o arquivo de certificados do computador local e novamente exportado com a mesma palavra-passe e guardado como ssl_new.pfx.  Este procedimento é para utilização quando a validação do certificado sinalizados que uma chave privada não tem o conjunto de atributos de computador Local, a cadeia de certificados foi interrompida, irrelevantes certificados estão presentes no PFX ou a cadeia de certificados está na ordem errada.

### <a name="example-view-validation-report-deployment-support"></a>Exemplo: Ver relatório de validação (suporte de implementação)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json
```

Neste exemplo, a equipe de implantação ou suporte recebe o relatório de preparação do cliente (Contoso) e utiliza AzsReadinessChecker de início para ver o estado das execuções de validação executada da Contoso.

### <a name="example-view-validation-report-summary-for-certificate-validation-only-deployment-and-support"></a>Exemplo: Ver o relatório de validação do resumo do certificado de validação única (implementação e suporte)

```PowerShell
Start-AzsReadinessChecker -ReportPath Contoso-AzsReadinessReport.json -ReportSections Certificate -Summary
```

Neste exemplo, a equipe de implantação ou suporte recebe o relatório de preparação do cliente Contoso e utiliza o início AzsReadinessChecker para ver um Estado resumido das execuções de validação de certificados efetuada da Contoso.

## <a name="required-parameters"></a>Parâmetros necessários

> -RegionName

Especifica o nome da região da implementação do Azure Stack.

|  |  |
|----------------------------|--------------|
|Escreva:                       |String        |
|Posição:                   |com o nome         |
|Valor predefinido:              |Nenhuma          |
|Aceite entrada de pipeline:      |Falso         |
|Aceite carateres universais: |Falso         |

> -FQDN

Especifica o FQDN de externo a implementação Azure Stack, também utilizada como alias ExternalFQDN e ExternalDomainName.

|  |  |
|----------------------------|--------------|
|Escreva:                       |String        |
|Posição:                   |com o nome         |
|Valor predefinido:              |ExternalFQDN, ExternalDomainName |
|Aceite entrada de pipeline:      |Falso         |
|Aceite carateres universais: |Falso         |

> -IdentitySystem

Especifica sistema de identidade valores válidos a implementação Azure Stack, AAD ou ADFS, para o Azure Active Directory e o Active Directory Federated Services, respetivamente.

|  |  |
|----------------------------|--------------|
|Escreva:                       |String        |
|Posição:                   |com o nome         |
|Valor predefinido:              |Nenhuma          |
|Valores válidos:               |'AAD','ADFS'  |
|Aceite entrada de pipeline:      |Falso         |
|Aceite carateres universais: |Falso         |

> -PfxPassword

Especifica a palavra-passe associada aos ficheiros de certificado PFX.

|  |  |
|----------------------------|---------|
|Escreva:                       |SecureString |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -PaaSCertificates

Especifica a tabela de hash contendo caminhos e palavras-passe para certificados de PaaS.

|  |  |
|----------------------------|---------|
|Escreva:                       |Tabela de hash |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -DeploymentDataJSONPath

Especifica o ficheiro de configuração do Azure Stack implementação dados JSON. Este ficheiro é gerado para a implementação.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -PfxPath

Especifica o caminho para um certificado problemático que requer a rotina de importação/exportação para corrigir, conforme indicado pela validação de certificado nesta ferramenta.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -ExportPFXPath  

Especifica o caminho de destino para o ficheiro PFX resultante da rotina de importação/exportação.  

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -Assunto

Especifica um dicionário ordenado do assunto para a geração de pedido de certificado.

|  |  |
|----------------------------|---------|
|Escreva:                       |OrderedDictionary   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -RequestType

Especifica o tipo de SAN, o pedido de certificado. Valid values MultipleCSR, SingleCSR.

- *MultipleCSR* gera vários pedidos de certificado, uma para cada serviço.
- *SingleCSR* gera um pedido de certificado para todos os serviços.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Valores válidos:               |'MultipleCSR','SingleCSR' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -OutputRequestPath

Especifica o caminho de destino para os ficheiros de pedido de certificado, o diretório já deve existir.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -AADServiceAdministrator

Especifica o administrador do serviço de diretório do Azure Active Directory a utilizar para a implementação do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |PSCredential   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -AADDirectoryTenantName

Especifica o nome do Azure Active Directory a utilizar para a implementação do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -AzureEnvironment

Especifica a instância dos serviços do Azure que contém as contas, diretórios e subscrições para ser utilizado para implementação do Azure Stack e o Registro.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Valores válidos:               |'AzureCloud','AzureChinaCloud','AzureUSGovernment' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -RegistrationAccount

Especifica a conta de registo a ser utilizadas para registo do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -RegistrationSubscriptionID

Especifica o ID de subscrição de registo a ser utilizadas para registo do Azure Stack.

|  |  |
|----------------------------|---------|
|Escreva:                       |Guid     |
|Posição:                   |com o nome    |
|Valor predefinido:              |Nenhuma     |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -ReportPath

Especifica o caminho para o relatório de preparação, é predefinido para o nome do relatório atual diretório e padrão.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Todos      |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

## <a name="optional-parameters"></a>Parâmetros opcionais

> -CertificatePath

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

> -IncludePaaS  

Especifica se os serviços/nomes de anfitrião de PaaS devem ser adicionadas para o pedido de certificado (s).

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

> -ReportSections

Especifica se a Mostrar relatório de resumo, apenas omite detalhes.

|  |  |
|----------------------------|---------|
|Escreva:                       |String   |
|Posição:                   |com o nome    |
|Valor predefinido:              |Todos      |
|Valores válidos:               |'Certificate','AzureRegistration','AzureIdentity','Jobs','All' |
|Aceite entrada de pipeline:      |Falso    |
|Aceite carateres universais: |Falso    |

> -Resumo

Especifica se a Mostrar relatório de resumo, apenas omite detalhes.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

> -CleanReport

Remove o histórico de execução e a validação anterior e escreve validações um novo relatório.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

> -OutputPath

Especifica o caminho personalizado para guardar o relatório de preparação para JSON e o ficheiro de registo verboso.  Se o caminho não exista, a ferramenta irá tentar criar o diretório.

|  |  |
|----------------------------|------------------|
|Escreva:                       |String            |
|Posição:                   |com o nome             |
|Valor predefinido:              |$ENV:TEMP\AzsReadinessChecker  |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

> -Confirm

Pedidos de confirmação antes de executar o cmdlet.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Aliases:                    |cf                |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

> -WhatIf

Mostra o que aconteceria se a execução do cmdlet. O cmdlet não é executado.

|  |  |
|----------------------------|------------------|
|Escreva:                       |SwitchParameter   |
|Aliases:                    |wi                |
|Posição:                   |com o nome             |
|Valor predefinido:              |Falso             |
|Aceite entrada de pipeline:      |Falso             |
|Aceite carateres universais: |Falso             |

