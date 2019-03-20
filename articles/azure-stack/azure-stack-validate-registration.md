---
title: Validar o registo do Azure para o Azure Stack | Documentos da Microsoft
description: Utilize o Verificador de preparação do Azure Stack para validar o registo do Azure.
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
ms.openlocfilehash: a4f41f5cf4e2775e3c300a2e83794e35d7328965
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58104238"
---
# <a name="validate-azure-registration"></a>Validar o registo do Azure
 
Utilize a ferramenta de Verificador de preparação do Azure Stack (AzsReadinessChecker) para validar se a sua subscrição do Azure está pronta para utilizar com o Azure Stack. Valide o registo antes de iniciar uma implementação do Azure Stack. O Verificador de preparação valida que:

- A subscrição do Azure que utiliza é um tipo suportado. As assinaturas tem de ser um fornecedor de serviços Cloud (CSP) ou o Enterprise Agreement (EA). 
- A conta que utiliza para registar a sua subscrição com o Azure pode iniciar sessão no Azure e é um proprietário da subscrição. 

Para obter mais informações sobre o registo do Azure Stack, veja [registar o Azure Stack com o Azure](azure-stack-registration.md). 

## <a name="get-the-readiness-checker-tool"></a>Obter a ferramenta de verificação de preparação

Transferir a versão mais recente da ferramenta Verificador de preparação do Azure Stack (AzsReadinessChecker) a partir da [galeria do PowerShell](https://aka.ms/AzsReadinessChecker).  

## <a name="prerequisites"></a>Pré-requisitos

Devem ser cumpridos os seguintes pré-requisitos:

**O computador onde a ferramenta é executada:**
 - Windows 10 ou Windows Server 2016, com ligação à internet.
 - PowerShell 5.1 ou posterior. Para verificar a sua versão, execute o seguinte cmdlet do PowerShell e, em seguida, reveja os *principais* e *pequenas* versões:  

    ```powershell
    $PSVersionTable.PSVersion
    ``` 
 - Configurar [PowerShell para o Azure Stack](azure-stack-powershell-install.md). 
 - Baixe a versão mais recente dos [Verificador de preparação do Microsoft Azure Stack](https://aka.ms/AzsReadinessChecker) ferramenta.  

**Ambiente do Active Directory do Azure:**
 - Identifique o nome de utilizador e palavra-passe para uma conta que seja o proprietário da subscrição do Azure que irá utilizar com o Azure Stack.  
 - Identifica o ID de subscrição para a subscrição do Azure que irá utilizar. 
 - Identificar os **AzureEnvironment** irá utilizar. Os valores suportados para o parâmetro de nome de ambiente são **AzureCloud**, **AzureChinaCloud** ou **AzureUSGovernment**, consoante a subscrição do Azure está a utilizar.

## <a name="validate-azure-registration"></a>Validar o registo do Azure

1. Num computador que cumpra os pré-requisitos, abra uma linha de comandos do PowerShell administrativa e, em seguida, execute o seguinte comando para instalar o **AzsReadinessChecker**.

    ```powershell
    Install-Module Microsoft.AzureStack.ReadinessChecker -Force
    ```

2. A partir da linha do PowerShell, execute o seguinte para definir `$registrationCredential` como a conta que seja o proprietário da subscrição. Substitua `subscriptionowner@contoso.onmicrosoft.com` com sua conta e o inquilino: 
   ```powershell
   $registrationCredential = Get-Credential subscriptionowner@contoso.onmicrosoft.com -Message "Enter Credentials for Subscription Owner"
   ```
   > [!NOTE]
   > Como um CSP, ao utilizar uma subscrição de serviços compartilhados ou IUR, terá de fornecer as credenciais de um utilizador a partir desse respectivo AAD. Normalmente, este será semelhante ao `subscriptionowner@iurcontoso.onmicrosoft.com`. Esse utilizador tem de ser ter as credenciais apropriadas, conforme descrito acima.

3. A partir da linha do PowerShell, execute o seguinte para definir `$subscriptionID` como a subscrição do Azure que irá utilizar. Substitua `xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx` com o seu próprio ID de subscrição:
   ```powershell
   $subscriptionID = "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
   ``` 

4. A partir da linha de comandos do PowerShell, execute o seguinte procedimento para iniciar a validação da sua subscrição: 
   - Especificar o valor para AzureEnvironment como **AzureCloud**, **AzureGermanCloud**, ou **AzureChinaCloud**.  
   - Forneça o administrador do Azure Active Directory e o nome do seu inquilino do Azure Active Directory. 

   ```powershell
   Invoke-AzsRegistrationValidation -RegistrationAccount $registrationCredential -AzureEnvironment AzureCloud -RegistrationSubscriptionID $subscriptionID
   ```

5. Depois da ferramenta é executada, reveja o resultado. Confirme que o estado está OK para início de sessão e os requisitos de registo. Uma validação com êxito parece ser semelhante ao seguinte exemplo:
  
   ```shell
   Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
   Checking Registration Requirements: OK
   Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
   Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
   Invoke-AzsRegistrationValidation Completed
   ```

## <a name="report-and-log-file"></a>Arquivo de log e relatório

Cada validação de tempo de execução, regista os resultados para **AzsReadinessChecker.log** e **AzsReadinessCheckerReport.json**. A localização dos ficheiros é apresentada com os resultados de validação no PowerShell. 

Estes ficheiros podem ajudá-lo a compartilhar o estado de validação antes de implementar o Azure Stack ou investigar problemas de validação. Ambos os arquivos de mantêm os resultados de cada verificação de validação subsequentes. O relatório fornece a sua confirmação da equipe de implementação da configuração da identidade. O ficheiro de registo pode ajudar a sua equipa de suporte de implementação ou investigar problemas de validação. 

Por predefinição, ambos os ficheiros são escritos *C:\Users\<nome de utilizador > \AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json*.  
 - Utilize o **- OutputPath** ***&lt;caminho&gt;*** parâmetro no final da linha de comandos de execução para especificar uma localização diferente do relatório.   
 - Utilize o **- CleanReport** parâmetro no final do comando de execução para limpar as informações de *AzsReadinessCheckerReport.json*.  sobre execuções anteriores da ferramenta. Para obter mais informações, [relatório de validação do Azure Stack](azure-stack-validation-report.md).

## <a name="validation-failures"></a>Falhas de validação
Se uma verificação de validação falhar, exibir detalhes sobre a falha na janela do PowerShell. A ferramenta também regista informações para o ficheiro de AzsReadinessChecker.log.

Os exemplos seguintes fornecem orientações sobre falhas de validação comuns:

### <a name="user-must-be-an-owner-of-the-subscription"></a>Utilizador tem de ser um proprietário da subscrição   

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
The user admin@contoso.onmicrosoft.com is role(s) Reader for subscription 3f961d1c-d1fb-40c3-99ba-44524b56df2d. User must be an owner of the subscription to be used for registration.
Additional help URL https://aka.ms/AzsRemediateRegistration

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -a conta não é um administrador da subscrição do Azure.   

**Resolução** -utilizar uma conta que seja um administrador da subscrição do Azure que será cobrada a utilização da implementação do Azure Stack.

### <a name="expired-or-temporary-password"></a>Palavra-passe expirada ou temporário
 
```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription [subscription ID] using account admin@contoso.onmicrosoft.com failed with AADSTS50055: Force Change P
assword.
Trace ID: [Trace ID]
Correlation ID: [Correlation ID]
Timestamp: 2018-10-22 11:16:56Z: The remote server returned an error: (401) Unauthorized.

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -a conta não pode iniciar sessão porque a palavra-passe a expirou ou é temporária.     

**Resolução** - a no PowerShell executar e siga as instruções para repor a palavra-passe. 

```powershell
Login-AzureRMAccount
``` 

Em alternativa, inicie sessão para https://portal.azure.com como a conta e o utilizador serão forçados a alterar a palavra-passe.

### <a name="unknown-user-type"></a>Tipo de utilizador desconhecido  

```shell
Invoke-AzsRegistrationValidation v1.1809.1005.1 started.
Checking Registration Requirements: Fail 
Error Details for registration account admin@contoso.onmicrosoft.com:
Checking Registration failed with: Retrieving TenantId for subscription <subscription ID> using <account> failed with unknown_user_type: Unknown User Type

Log location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessChecker.log
Report location (contains PII): C:\Users\username\AppData\Local\Temp\AzsReadinessChecker\AzsReadinessCheckerReport.json
Invoke-AzsRegistrationValidation Completed
```

**Causa** -a conta não pode iniciar sessão para o ambiente do Azure Active Directory especificado. Neste exemplo, *AzureChinaCloud* está especificado como o *AzureEnvironment*.  

**Resolução** -confirme que a conta é válida para o ambiente do Azure especificado. No PowerShell, execute o seguinte para verificar se que a conta é válida para um ambiente específico:
     
```powershell
Login-AzureRmAccount -EnvironmentName AzureChinaCloud
```

## <a name="next-steps"></a>Próximos Passos

- [Validar a identidade do Azure](azure-stack-validate-identity.md)
- [Ver o relatório de preparação](azure-stack-validation-report.md)
- [Considerações sobre a integração geral do Azure Stack](azure-stack-datacenter-integration.md)

