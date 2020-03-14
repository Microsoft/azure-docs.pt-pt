---
title: Windows Virtual Desktop inquilino anfitrião da piscina - Azure
description: Como resolver problemas e resolver problemas de piscina de inquilinos e anfitriões durante a configuração de um ambiente de inquilinos do Windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: troubleshooting
ms.date: 01/08/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 36b15b41279edc60d337a7ba70abe2ca64d4bc7f
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79371601"
---
# <a name="tenant-and-host-pool-creation"></a>Criação de inquilinos e conjuntos de anfitriões

Este artigo aborda problemas durante a configuração inicial do inquilino do Windows Virtual Desktop e a infraestrutura de piscina de hospedas de sessão relacionada.

## <a name="provide-feedback"></a>Enviar comentários

Visite o [Windows Virtual Desktop Tech Community](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual windows com a equipa de produtos e membros ativos da comunidade.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Adquirir a imagem multi-sessão do Windows 10 Enterprise

Para utilizar a imagem multi-sessão do Windows 10 Enterprise, vá ao Mercado Azure, selecione **Get Started** > Microsoft **Windows 10** > e [Windows 10 Enterprise para Desktops Virtuais, Versão 1809.](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice)

![Uma imagem de selecionar o Windows 10 Enterprise para ambientes de trabalho virtuais, versão 1809.](media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Criação de inquilino supor virtual do Windows

Esta secção aborda potenciais problemas ao criar o inquilino do Windows Virtual Desktop.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O utilizador não está autorizado a consultar o serviço de gestão

![Screenshot da janela PowerShell na qual um utilizador não está autorizado a consultar o serviço de gestão.](media/UserNotAuthorizedNewTenant.png)

Exemplo de erro bruto:

```Error
   New-RdsTenant : User isn't authorized to query the management service.
   ActivityId: ad604c3a-85c6-4b41-9b81-5138162e5559
   Powershell commands to diagnose the failure:
   Get-RdsDiagnosticActivities -ActivityId ad604c3a-85c6-4b41-9b81-5138162e5559
   At line:1 char:1
   + New-RdsTenant -Name "testDesktopTenant" -AadTenantId "01234567-89ab-c ...
   + ~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~~
       + CategoryInfo          : FromStdErr: (Microsoft.RDInf...nt.NewRdsTenant:NewRdsTenant) [New-RdsTenant], RdsPowerSh
      ellException
       + FullyQualifiedErrorId : UnauthorizedAccess,Microsoft.RDInfra.RDPowershell.Tenant.NewRdsTenant
```

**Causa:** O utilizador que assinou não foi designado o papel de Criador de Inquilinos no seu Diretório Ativo Azure.

**Correção:** Siga as instruções em atribuir a função de [candidatura do TenantCreator a um utilizador do seu inquilino de Diretório Ativo Azure](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Depois de seguir as instruções, terá um utilizador designado para o papel de Criador de Inquilinos.

![Screenshot do papel de Criador de Inquilino atribuído.](media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criação de VMs de sessão de desktop virtual do Windows

Os VMs de anfitrião de sessão podem ser criados de várias formas, mas a equipa de desktop virtual do Windows apenas suporta problemas de provisionamento vM relacionados com a oferta do [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Para mais informações, consulte [Questões utilizando o Windows Virtual Desktop - Forneça uma oferta de pool de anfitriões Azure Marketplace.](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering)

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas usando Windows Virtual Desktop – Provision a host pool Azure Marketplace oferecendo

O Windows Virtual Desktop – Fornecer um modelo de piscina anfitrião está disponível no Mercado Azure.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: Ao utilizar o link do GitHub, aparece a mensagem "Criar uma conta gratuita"

![Screenshot para criar uma conta gratuita.](media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** Não existem subscrições ativas na conta usada para iniciar sessão no Azure ou a conta usada não tem permissões para visualizar as subscrições.

**Correção 1:** Inscreva-se numa conta que tenha acesso ao contribuinte (no mínimo) para a subscrição onde os VMs anfitriãos da sessão vão ser implementados.

**Causa 2:** A subscrição que está a ser utilizada faz parte de um inquilino do Microsoft Cloud Service Provider (CSP).

**Correção 2:** Vá à localização do GitHub para criar e fornecer novo pool de **anfitriões do Windows Virtual Desktop** e siga estas instruções:

1. Clique à direita no **'Enviar para O Azure'** e selecione **'Copiar' endereço**de link .
2. Abra **o bloco** de notas e colhe o link.
3. Antes do personagem #, insira o nome do inquilino final do CSP.
4. Abra o novo link num browser e o portal Azure carregará o modelo.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Erro: Recebe "A implementação do modelo não é válida" erro

![Screenshot de "implementação do modelo ... não é válido" erro](media/troubleshooting-marketplace-validation-error-generic.png)

Antes de tomar medidas específicas, terá de verificar o registo de atividade para ver o erro detalhado para a validação de implementação falhada.

Para ver o erro no registo de atividade:

1. Saia da oferta atual de implantação do Azure Marketplace.
2. Na barra de pesquisa superior, procure e selecione **Registo de Atividades**.
3. Encontre uma atividade chamada **Validate Deployment** que tenha um estado de **Falha e** selecione a atividade.
   ![Screenshot da atividade individual **Validate Deployment** com um estado **Failed**](media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Selecione JSON e, em seguida, desloque-se para a parte inferior do ecrã até ver o campo "statusMessage".
   ![Screenshot de atividade falhada, com uma caixa vermelha em torno da propriedade statusMessage do texto JSON.](media/troubleshooting-marketplace-validation-error-json-boxed.png)

Se o seu modelo de funcionamento ultrapassar o limite de quota, pode fazer uma das seguintes coisas para corrigi-lo:

 - Execute o Azure Marketplace com os parâmetros que usou pela primeira vez, mas desta vez use menos VMs e núcleos VM.
 - Abra o link que vê no campo **statusMessage** num browser para submeter um pedido para aumentar a quota para a sua subscrição Azure para o SKU VM especificado.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Modelo de gestor de recursos azure e erros de configuração do Estado desejados powerShell (DSC)

Siga estas instruções para resolução de problemas de implementações mal sucedidas de modelos do Gestor de Recursos Azure e do PowerShell DSC.

1. Reveja os erros na implementação utilizando as operações de implantação do View com o [Gestor de Recursos Azure](../azure-resource-manager/resource-manager-deployment-operations.md).
2. Se não houver erros na implementação, reveja os erros no registo de atividade utilizando registos de atividade do [View para auditar ações sobre recursos](../azure-resource-manager/resource-group-audit.md).
3. Uma vez identificado o erro, utilize a mensagem de erro e os recursos em Troubleshoot erros comuns de implementação do [Azure com o Gestor de Recursos Azure](../azure-resource-manager/resource-manager-common-deployment-errors.md) para resolver o problema.
4. Elimine quaisquer recursos criados durante a implementação anterior e volte a implementar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: A sua implementação falhou....\<nome de anfitrião>/joindomain

![A sua imagem falhada de implantação.](media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais previstas para a adesão de VMs ao domínio estão incorretas.

**Correção 1:** Ver que o erro das "credenciais incorretas" para os VMs não está unido ao domínio na [configuração VM do anfitrião](troubleshoot-vm-configuration.md)da sessão .

**Causa 2:** O nome de domínio não resolve.

**Correção 2:** Ver [Erro: O nome](troubleshoot-vm-configuration.md#error-domain-name-doesnt-resolve) do domínio não resolve na [configuração VM do anfitrião](troubleshoot-vm-configuration.md)da sessão .

**Causa 3:** A configuração DNS da rede virtual (VNET) está definida para **Padrão**.

Para corrigir isto, faça as seguintes coisas:

1. Abra o Portal Azure e vá ao separador **redes Virtuais.**
2. Encontre o seu VNET e, em seguida, selecione **servidores DNS**.
3. O menu de servidores DNS deve aparecer no lado direito do ecrã. Nesse menu, selecione **Custom**.
4. Certifique-se de que os servidores DNS listados em Custom correspondem ao seu controlador de domínio ou ao domínio ative directory. Se não vir o seu servidor DNS, pode adicioná-lo introduzindo o seu valor no campo de **servidor DNS Add.**

### <a name="error-your-deployment-failedunauthorized"></a>Erro: A sua implementação falhou...\Não autorizada

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A subscrição que está a usar é um tipo que não consegue aceder às funcionalidades necessárias na região onde o cliente está a tentar implementar. Por exemplo, as subscrições de MSDN, Free ou Education podem mostrar este erro.

**Correção:** Altere o seu tipo de subscrição ou região para aquele que pode aceder às funcionalidades necessárias.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

![Screenshot da sua implantação falhou com o estado de provisionamento terminal falhado.](media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Erro transitório com o ambiente de ambiente de trabalho virtual do Windows.

**Causa 2:** Erro transitório com ligação.

**Correção:** Confirme que o ambiente de trabalho virtual do Windows é saudável ao iniciar sessão utilizando o PowerShell. Termine o registo VM manualmente em [Criar uma piscina de anfitriões com powerShell](create-host-pools-powershell.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O nome de utilizador do Administrador especificado não é permitido

![A imagem da sua implantação falhou na qual um administrador especificado não é permitido.](media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemplo de erro bruto:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** A palavra-passe fornecida contém substrings proibidos (administrador, administrador, raiz).

**Correção:** Atualizar o nome de utilizador ou utilizar diferentes utilizadores.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: VM reportou uma falha no processamento de extensão

![Screenshot da operação de recursos concluída com estado de provisionamento terminal na sua implementação falhou.](media/49c4a1836a55d91cd65125cf227f411f.png)

Exemplo de erro bruto:

```Error
{ "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.Resources/deployments/
 rds.wvd-provision-host-pool-20190129132410/operations/5A0757AC9E7205D2", "operationId": "5A0757AC9E7205D2", "properties":
 { "provisioningOperation": "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T21:43:05.1416423Z",
 "duration": "PT7M56.8150879S", "trackingId": "43c4f71f-557c-4abd-80c3-01f545375455", "statusCode": "Conflict",
 "statusMessage": { "status": "Failed", "error": { "code": "ResourceDeploymentFailure", "message":
 "The resource operation completed with terminal provisioning state 'Failed'.", "details": [ { "code":
 "VMExtensionProvisioningError", "message": "VM has reported a failure when processing extension 'dscextension'.
 Error message: \"DSC Configuration 'SessionHost' completed with error(s). Following are the first few:
 PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource functionality with error message:
 One or more errors occurred. The SendConfigurationApply function did not succeed.\"." } ] } }, "targetResource":
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostD/providers/Microsoft.
 Compute/virtualMachines/desktop-1/extensions/dscextension",
 "resourceType": "Microsoft.Compute/virtualMachines/extensions", "resourceName": "desktop-1/dscextension" } }}
```

**Causa:** A extensão DoDSC powerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe ter acesso administrativo na máquina virtual e executar novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: Implementação Falhada – Configuração 'FirstSessionHost' de configuração do PowerShell DSC concluída com erro(s)

![Falha na falha de implementação com configuração do DSC powerShell 'FirstSessionHost' concluída com Error(s).](media/64870370bcbe1286906f34cf0a8646ab.png)

Exemplo de erro bruto:

```Error
{
    "code": "DeploymentFailed",
   "message": "At least one resource deployment operation failed. Please list
 deployment operations for details. 4 Please see https://aka.ms/arm-debug for usage details.",
 "details": [
         { "code": "Conflict",  
         "message": "{\r\n \"status\": \"Failed\",\r\n \"error\": {\r\n \"code\":
         \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource
         operation completed with terminal provisioning state 'Failed'.\",\r\n
         \"details\": [\r\n {\r\n \"code\":
        \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has
              reported a failure when processing extension 'dscextension'.
              Error message: \\\"DSC Configuration 'FirstSessionHost'
              completed with error(s). Following are the first few:
              PowerShell DSC resource MSFT ScriptResource failed to
              execute Set-TargetResource functionality with error message:
              One or more errors occurred. The SendConfigurationApply
              function did not succeed.\\\".\"\r\n }\r\n ]\r\n }\r\n}"  }

```

**Causa:** A extensão DoDSC powerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe fornecida têm acesso administrativo na máquina virtual e executem novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: Implementação Falhada – Referência de Recursos Inválidos

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/networkInterfaces/erd. Please make sure that
the referenced resource exists, and that both resources are in the same
region.\\\",\\r\\n\\\"details\\\": []\\r\\n }\\r\\n}\"\r\n }\r\n ]\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa:** Parte do nome do grupo de recursos é usado para certos recursos criados pelo modelo. Devido ao nome que combina com os recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** Ao executar o modelo do Gestor de Recursos Azure para implementar VMs de anfitrião da sessão, torne os dois primeiros caracteres únicos para o seu nome de grupo de recursos de subscrição.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: Implementação Falhada – Referência de Recursos Inválidos

Exemplo de erro bruto:

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation
failed. Please list deployment operations for details. Please see https://aka.ms/arm-
debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\":
\"Failed\",\r\n \"error\": {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n
\"message\": \"The resource operation completed with terminal provisioning state
'Failed'.\",\r\n \"details\": [\r\n {\r\n \"code\": \"DeploymentFailed\",\r\n
\"message\": \"At least one resource deployment operation failed. Please list
deployment operations for details. Please see https://aka.ms/arm-debug for usage
details.\",\r\n \"details\": [\r\n {\r\n \"code\": \"BadRequest\",\r\n \"message\":
\"{\\r\\n \\\"error\\\": {\\r\\n \\\"code\\\": \\\"InvalidResourceReference\\\",\\r\\n
\\\"message\\\": \\\"Resource /subscriptions/EXAMPLE/resourceGroups/ernani-wvd-
demo/providers/Microsoft.Network/virtualNetworks/wvd-vnet/subnets/default
referenced by resource /subscriptions/EXAMPLE/resourceGroups/DEMO/providers/Microsoft.Network/networkInterfaces
/EXAMPLE was not found. Please make sure that the referenced resource exists, and that both
resources are in the same region.\\\",\\r\\n \\\"details\\\": []\\r\\n }\\r\\n}\"\r\n
}\r\n ]\r\n }\r\n ]\r\n }\r\n\
```

**Causa:** Este erro deve-se ao facto de o NIC criado com o modelo do Gestor de Recursos Azure ter o mesmo nome que outro NIC já no VNET.

**Correção:** Use um prefixo de hospedeiro diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Error: DeploymentFailed – Error download

Exemplo de erro bruto:

```Error
\\\"The DSC Extension failed to execute: Error downloading
https://catalogartifact.azureedge.net/publicartifacts/rds.wvd-provision-host-pool-
2dec7a4d-006c-4cc0-965a-02bbe438d6ff-prod
/Artifacts/DSC/Configuration.zip after 29 attempts: The remote name could not be
resolved: 'catalogartifact.azureedge.net'.\\nMore information about the failure can
be found in the logs located under
'C:\\\\WindowsAzure\\\\Logs\\\\Plugins\\\\Microsoft.Powershell.DSC\\\\2.77.0.0' on
the VM.\\\"
```

**Causa:** Este erro deve-se a uma rota estática, regra da firewall ou NSG bloqueando o download do ficheiro zip ligado ao modelo do Gestor de Recursos Azure.

**Correção:** Remova o bloqueio da rota estática, regra da firewall ou NSG. Opcionalmente, abra o ficheiro json modelo do Gestor de Recursos Do Azure num editor de texto, leve o link para o ficheiro zip e descarregue o recurso para uma localização permitida.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O utilizador não está autorizado a consultar o serviço de gestão

Exemplo de erro bruto:

```Error
"response": { "content": { "startTime": "2019-04-01T17:45:33.3454563+00:00", "endTime": "2019-04-01T17:48:52.4392099+00:00",
"status": "Failed", "error": { "code": "VMExtensionProvisioningError", "message": "VM has reported a failure when processing
extension 'dscextension'. Error message: \"DSC Configuration 'FirstSessionHost' completed with error(s).
Following are the first few: PowerShell DSC resource MSFT_ScriptResource failed to execute Set-TargetResource
 functionality with error message: User is not authorized to query the management service.
\nActivityId: 1b4f2b37-59e9-411e-9d95-4f7ccd481233\nPowershell commands to diagnose the failure:
\nGet-RdsDiagnosticActivities -ActivityId 1b4f2b37-59e9-411e-9d95-4f7ccd481233\n
The SendConfigurationApply function did not succeed.\"." }, "name": "2c3272ec-d25b-47e5-8d70-a7493e9dc473" } } }}
```

**Causa:** O administrador especificado do Windows Virtual Desktop não tem uma atribuição de funções válida.

**Correção:** O utilizador que criou o inquilino do Windows Virtual Desktop precisa de iniciar sessão no Windows Virtual Desktop PowerShell e atribuir ao utilizador uma atribuição de funções. Se estiver a executar os parâmetros do modelo GitHub Azure Resource Manager, siga estas instruções utilizando comandos PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-multi-factor-authentication-mfa"></a>Erro: Utilizador requer autenticação de multi-factor estoque azure (MFA)

![A imagem da sua implementação falhou devido à falta de autenticação multi-factor (MFA)](media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** O administrador especificado do Windows Virtual Desktop requer que a Autenticação Multi-Factor (MFA) do Azure se inscreva.

**Correção:** Crie um diretor de serviço e atribua-lhe uma função para o seu inquilino do Windows Virtual Desktop seguindo os passos no [Tutorial: Crie os principais de serviçoe atribuições de funções com](create-service-principal-role-powershell.md)a PowerShell . Depois de verificar se pode iniciar sessão no Windows Virtual Desktop com o diretor de serviço, refaça a oferta do Azure Marketplace ou o modelo gitHub Azure Resource Manager, dependendo do método que estiver a utilizar. Siga as instruções abaixo para introduzir os parâmetros corretos para o seu método.

Se estiver a executar a oferta do Azure Marketplace, forneça valores para os seguintes parâmetros para autenticar adequadamente o Windows Virtual Desktop:

- Windows Virtual Desktop inquilino RDS Proprietário: Diretor de serviço
- ID de aplicação: A identificação da aplicação do novo diretor de serviço que criou
- Palavra-passe/Palavra-passe de confirmação: O segredo de senha gerado para o diretor de serviço
- Id do Inquilino Azure AD: A Id do Inquilino Azure AD do principal de serviço que criou

Se estiver a executar o modelo gitHub Azure Resource Manager, forneça valores para os seguintes parâmetros para autenticar adequadamente o Windows Virtual Desktop:

- Nome principal do utilizador do Tenant Admin (UPN) ou ID de aplicação: A identificação da aplicação do novo diretor de serviço que criou
- Palavra-passe do Inquilino: O segredo de senha que gerou para o diretor de serviço
- IsServicePrincipal: **verdadeiro**
- AadTenantId: A Id do Inquilino AD Azure do principal de serviço que criou

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte a [visão geral, feedback e suporte](troubleshoot-set-up-overview.md)de Resolução de Problemas.
- Para resolver problemas ao configurar uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração virtual](troubleshoot-vm-configuration.md)do anfitrião da sessão .
- Para resolver problemas com as ligações do cliente do Windows Virtual Desktop, consulte [as ligações](troubleshoot-service-connection.md)de serviço do Windows Virtual Desktop .
- Para resolver problemas com clientes do Desktop Remoto, consulte [Troubleshoot o cliente Remote Desktop](troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell.md).
- Para saber mais sobre o serviço, consulte o ambiente de ambiente de [trabalho virtual do Windows](environment-setup.md).
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações](../azure-resource-manager/templates/template-tutorial-troubleshoot.md)do modelo de gestor de recursos de resolução de problemas .
- Para conhecer as ações de auditoria, consulte operações de [auditoria com o Gestor de Recursos.](../azure-resource-manager/management/view-activity-logs.md)
- Para aprender sobre as ações para determinar os erros durante a implementação, consulte as operações de [implantação do View](../azure-resource-manager/templates/deployment-history.md).
