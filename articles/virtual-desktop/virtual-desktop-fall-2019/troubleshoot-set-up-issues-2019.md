---
title: Windows Virtual Desktop (clássico) inquilino anfitrião criação de piscina - Azure
description: Como resolver problemas e resolver problemas de inquilino e piscina de anfitrião durante a configuração de um ambiente de inquilino virtual do Windows Desktop (clássico).
author: Heidilohr
ms.topic: troubleshooting
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8c6d26de62364b6aca671d1e4283a01c1b78c397
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "95014837"
---
# <a name="tenant-and-host-pool-creation-in-windows-virtual-desktop-classic"></a>Criação de piscina de inquilino e anfitrião no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../troubleshoot-set-up-issues.md).

Este artigo cobre problemas durante a configuração inicial do inquilino virtual do Windows Desktop e da infraestrutura de piscina de anfitrião de sessão relacionada.

## <a name="provide-feedback"></a>Enviar comentários

Visite a [Comunidade Virtual desktop tech do Windows](https://techcommunity.microsoft.com/t5/Windows-Virtual-Desktop/bd-p/WindowsVirtualDesktop) para discutir o serviço de desktop virtual do Windows com a equipa de produtos e membros da comunidade ativa.

## <a name="acquiring-the-windows-10-enterprise-multi-session-image"></a>Aquisição da imagem multi-sessão do Windows 10 Enterprise

Para utilizar a imagem multi-sessão do Windows 10 Enterprise, vá ao Azure Marketplace, **selecione Get Start** Microsoft Windows  >  **10** > e Windows [10 Enterprise para Desktops Virtuais, Versão 1809](https://azuremarketplace.microsoft.com/marketplace/apps/microsoftwindowsdesktop.windows-10?tab=PlansAndPrice).

> [!div class="mx-imgBorder"]
> ![Uma imagem de seleção do Windows 10 Enterprise para Desktops Virtuais, Versão 1809.](../media/AzureMarketPlace.png)

## <a name="creating-windows-virtual-desktop-tenant"></a>Criação de inquilino virtual do Windows Desktop

Esta secção cobre potenciais problemas ao criar o inquilino virtual do Windows Desktop.

### <a name="error-aadsts650052-the-app-needs-access-to-a-service"></a>Erro: AADSTS650052 A aplicação precisa de acesso a um serviço.

Exemplo de erro bruto:

```Error
AADSTS650052 Message The app needs access to a service(\"{name}\") that your organization
\"{organization}\" has not subscribed to or enabled. Contact your IT Admin to review the
configuration of your service subscriptions.650052 Message The app needs access to a service
(\"{name}\") that your organization \"{organization}\" has not subscribed to or enabled.
Contact your IT Admin to review the configuration of your service subscriptions.
```

**Causa:** Consentimento não concedido ao Windows Virtual Desktop no caso do diretório Azure Ative.

**Correção:** [Siga este guia](./tenant-setup-azure-active-directory.md#grant-permissions-to-windows-virtual-desktop) para conceder o consentimento.

### <a name="error-the-user-isnt-authorized-to-query-the-management-service"></a>Erro: O utilizador não está autorizado a consultar o serviço de gestão

> [!div class="mx-imgBorder"]
> ![Screenshot da janela PowerShell na qual um utilizador não está autorizado a consultar o serviço de gestão.](../media/UserNotAuthorizedNewTenant.png)

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

**Causa:** O utilizador que assinou não foi designado para o cargo de InquilinoCreator no seu Diretório Ativo Azure.

**Correção:** Siga as instruções em [Atribuir a função de candidatura do TenantCreator a um utilizador no seu inquilino Azure Ative Directory](tenant-setup-azure-active-directory.md#assign-the-tenantcreator-application-role). Depois de seguir as instruções, terá um utilizador atribuído à função De InquilinoCreator.

> [!div class="mx-imgBorder"]
> ![Screenshot do papel do TenantCreator atribuído.](../media/TenantCreatorRoleAssigned.png)

## <a name="creating-windows-virtual-desktop-session-host-vms"></a>Criar VMs de sessão virtual do Windows

Os VMs do anfitrião da sessão podem ser criados de várias maneiras, mas a equipa virtual do Windows desktop apenas suporta problemas de fornecimento de VM relacionados com a oferta do [Azure Marketplace.](https://azuremarketplace.microsoft.com/) Para obter mais informações, consulte [Problemas utilizando o Windows Virtual Desktop - Forneça uma oferta de pool de anfitriões Azure Marketplace](#issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering).

## <a name="issues-using-windows-virtual-desktop--provision-a-host-pool-azure-marketplace-offering"></a>Problemas utilizando o Windows Virtual Desktop – Providenciar uma oferta de pool de anfitriões Azure Marketplace

O Windows Virtual Desktop – Provision a host pool template está disponível no Azure Marketplace.

### <a name="error-when-using-the-link-from-github-the-message-create-a-free-account-appears"></a>Erro: Ao utilizar o link do GitHub, aparece a mensagem "Criar uma conta gratuita"

> [!div class="mx-imgBorder"]
> ![Screenshot para criar uma conta gratuita.](../media/be615904ace9832754f0669de28abd94.png)

**Causa 1:** Não existem subscrições ativas na conta utilizada para iniciar súm na Azure ou a conta utilizada não tem permissões para visualizar as subscrições.

**Correção 1:** Inscreva-se com uma conta que tenha acesso ao contribuinte (no mínimo) à subscrição onde serão implantados VMs de anfitrião da sessão.

**Causa 2:** A subscrição que está a ser utilizada faz parte de um inquilino do Microsoft Cloud Service Provider (CSP).

**Correção 2:** Vá à localização do GitHub para **criar e forrê a nova piscina de anfitriões virtual do Windows Desktop** e siga estas instruções:

1. Clique com o botão direito no **Implementar para Azure** e selecione **o endereço de link copy**.
2. Abra **o Bloco de Notas** e cole o link.
3. Antes do personagem #, insira o nome do cliente final CSP.
4. Abra o novo link num browser e o portal Azure carregará o modelo.

    ```Example
    Example: https://portal.azure.com/<CSP end customer tenant name>
    #create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%
    2FRDS-Templates%2Fmaster%2Fwvd-templates%2FCreate%20and%20provision%20WVD%20host%20pool%2FmainTemplate.json
    ```

### <a name="error-you-receive-template-deployment-is-not-valid-error"></a>Erro: Recebe erro de "implementação do modelo não é válido"

> [!div class="mx-imgBorder"]
> ![Screenshot de "implantação do modelo ... não é válido" erro](../media/troubleshooting-marketplace-validation-error-generic.png)

Antes de tomar medidas específicas, terá de verificar o registo de atividade para ver o erro detalhado da validação de implementação falhada.

Para visualizar o erro no registo de atividade:

1. Saia da atual oferta de implantação do Azure Marketplace.
2. Na barra de pesquisa superior, procure e selecione **Registo de Atividades.**
3. Encontre uma atividade chamada **Implantação Validada** que tenha um estado de **Falha** e selecione a atividade.

   > [!div class="mx-imgBorder"]
   > ![Screenshot de atividade individual **Validate Deployment** com um estado **Falhado**](../media/troubleshooting-marketplace-validation-error-activity-summary.png)

4. Selecione JSON e, em seguida, desloque-se até à parte inferior do ecrã até ver o campo "statusMessage".

   > [!div class="mx-imgBorder"]
   > ![Screenshot de atividade falhada, com uma caixa vermelha em torno do status Propriedade Message do texto JSON.](../media/troubleshooting-marketplace-validation-error-json-boxed.png)

Se o seu modelo de operação ultrapassar o limite de quota, pode fazer uma das seguintes coisas para corrigi-lo:

 - Executar o Azure Marketplace com os parâmetros que usou pela primeira vez, mas desta vez use menos VMs e núcleos VM.
 - Abra o link que vê no campo **statusMessage** num browser para submeter um pedido de aumento da quota para a sua subscrição Azure para o VM SKU especificado.

## <a name="azure-resource-manager-template-and-powershell-desired-state-configuration-dsc-errors"></a>Modelo de gestor de recursos Azure e erros de configuração do estado (DSC) de PowerShell

Siga estas instruções para resolver implementações infrutíferas dos modelos do Gestor de Recursos Azure e do PowerShell DSC.

1. Rever erros na implementação utilizando [operações de implementação do View com o Azure Resource Manager](../../azure-resource-manager/templates/deployment-history.md).
2. Se não houver erros na implementação, reveja os erros no registo de atividades utilizando [registos de atividades do View para auditar ações sobre recursos](../../azure-resource-manager/management/view-activity-logs.md).
3. Assim que o erro for identificado, utilize a mensagem de erro e os recursos em [erros comuns de implementação do Azure com o Azure Resource Manager](../../azure-resource-manager/templates/common-deployment-errors.md) para resolver o problema.
4. Elimine quaisquer recursos criados durante a implementação anterior e volte a tentar implementar o modelo novamente.

### <a name="error-your-deployment-failedhostnamejoindomain"></a>Erro: A sua implantação falhou.... \<hostname> /joindomain

> [!div class="mx-imgBorder"]
> ![A sua imagem falhada de implantação falhou.](../media/e72df4d5c05d390620e07f0d7328d50f.png)

Exemplo de erro bruto:

```Error
 {"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details.
 Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Conflict","message":"{\r\n \"status\": \"Failed\",\r\n \"error\":
 {\r\n \"code\": \"ResourceDeploymentFailure\",\r\n \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.
 \",\r\n \"details\": [\r\n {\r\n \"code\": \"VMExtensionProvisioningError\",\r\n \"message\": \"VM has reported a failure when processing
 extension 'joindomain'. Error message: \\\"Exception(s) occurred while joining Domain 'diamondsg.onmicrosoft.com'\\\".\"\r\n }\r\n ]\r\n }\r\n}"}]}
```

**Causa 1:** As credenciais previstas para a junção de VMs ao domínio estão incorretas.

**Correção 1:** Consulte o erro de "credenciais incorretas" para VMs não se junta ao domínio na [configuração VM do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md).

**Causa 2:** O nome de domínio não resolve.

**Correção 2:** Ver [Erro: O nome de domínio não se resolve](troubleshoot-vm-configuration-2019.md#error-domain-name-doesnt-resolve) na [configuração VM do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md).

**Causa 3:** A configuração DE DNS da sua rede virtual (VNET) está definida como **Padrão**.

Para corrigir isto, faça as seguintes coisas:

1. Abra o Portal Azure e vá ao separador **redes Virtuais.**
2. Encontre o seu VNET e, em seguida, selecione **servidores DNS**.
3. O menu de servidores DNS deve aparecer no lado direito do seu ecrã. Nesse menu, selecione **Custom**.
4. Certifique-se de que os servidores DNS listados no Custom correspondem ao seu controlador de domínio ou ao domínio do Ative Directory. Se não vir o servidor DNS, pode adicioná-lo introduzindo o seu valor no campo do **servidor Add DNS.**

### <a name="error-your-deployment-failedunauthorized"></a>Erro: Falha na implementação...\Não autorizado

```Error
{"code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/arm-debug for usage details.","details":[{"code":"Unauthorized","message":"{\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Target\": null,\r\n \"Details\": [\r\n {\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n },\r\n {\r\n \"Code\": \"Unauthorized\"\r\n },\r\n {\r\n \"ErrorEntity\": {\r\n \"ExtendedCode\": \"52020\",\r\n \"MessageTemplate\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\",\r\n \"Parameters\": [\r\n \"default\"\r\n ],\r\n \"Code\": \"Unauthorized\",\r\n \"Message\": \"The scale operation is not allowed for this subscription in this region. Try selecting different region or scale option.\"\r\n }\r\n }\r\n ],\r\n \"Innererror\": null\r\n}"}]}
```

**Causa:** A subscrição que está a usar é um tipo que não consegue aceder às funcionalidades necessárias na região onde o cliente está a tentar implementar. Por exemplo, as assinaturas MSDN, Free ou Education podem mostrar este erro.

**Correção:** Altere o tipo de subscrição ou região para um que possa aceder às funcionalidades necessárias.

### <a name="error-vmextensionprovisioningerror"></a>Erro: VMExtensionProvisioningError

> [!div class="mx-imgBorder"]
> ![A screenshot da sua implantação falhou com o estado de provisionamento terminal falhado.](../media/7aaf15615309c18a984673be73ac969a.png)

**Causa 1:** Erro transitório com o ambiente de trabalho virtual do Windows.

**Causa 2:** Erro transitório com ligação.

**Correção:** Confirme que o ambiente de ambiente de trabalho virtual do Windows é saudável ao iniciar sessão com o PowerShell. Termine manualmente o registo VM em [Criar uma piscina de anfitriões com PowerShell](create-host-pools-powershell-2019.md).

### <a name="error-the-admin-username-specified-isnt-allowed"></a>Erro: O Nome de Utilizador do Administrador especificado não é permitido

> [!div class="mx-imgBorder"]
> ![A imagem da sua implantação falhou na qual não é permitido um administrador especificado.](../media/f2b3d3700e9517463ef88fa41875bac9.png)

Exemplo de erro bruto:

```Error
 { "id": "/subscriptions/EXAMPLE/resourceGroups/demoHostDesktop/providers/Microsoft.
  Resources/deployments/vmCreation-linkedTemplate/operations/EXAMPLE", "operationId": "EXAMPLE", "properties": { "provisioningOperation":
 "Create", "provisioningState": "Failed", "timestamp": "2019-01-29T20:53:18.904917Z", "duration": "PT3.0574505S", "trackingId":
 "1f460af8-34dd-4c03-9359-9ab249a1a005", "statusCode": "BadRequest", "statusMessage": { "error": { "code": "InvalidParameter", "message":
 "The Admin Username specified is not allowed.", "target": "adminUsername" } }, "targetResource": { "id": "/subscriptions/EXAMPLE
 /resourceGroups/demoHostDesktop/providers/Microsoft.Compute/virtualMachines/demo", "resourceType": "Microsoft.Compute/virtualMachines", "resourceName": "demo" } }}
```

**Causa:** A palavra-passe fornecida contém sublamentos proibidos (administrador, administrador, raiz).

**Correção:** Atualizar o nome de utilizador ou utilizar diferentes utilizadores.

### <a name="error-vm-has-reported-a-failure-when-processing-extension"></a>Erro: A VM reportou uma falha durante o processamento da extensão

> [!div class="mx-imgBorder"]
> ![Screenshot da operação de recursos concluída com estado de provisionamento terminal na Sua Implementação Falhou.](../media/49c4a1836a55d91cd65125cf227f411f.png)

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

**Causa:** A extensão DSC da PowerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe têm acesso administrativo na máquina virtual e execute novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--powershell-dsc-configuration-firstsessionhost-completed-with-errors"></a>Erro: ImplementaçãoFailed – Configuração DShell 'FirstSessionHost' concluída com Erro(s)

> [!div class="mx-imgBorder"]
> ![Screenshot de falha de implementação com configuração DSC 'FirstSessionHost' completada com Erro(s).](../media/64870370bcbe1286906f34cf0a8646ab.png)

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

**Causa:** A extensão DSC da PowerShell não foi capaz de obter acesso administrativo no VM.

**Correção:** Confirme o nome de utilizador e a palavra-passe fornecidas têm acesso administrativo na máquina virtual e execute novamente o modelo do Gestor de Recursos Azure.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: ImplementaçãoFailed – InvalidResourceReference

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

**Causa:** Parte do nome do grupo de recursos é usado para certos recursos criados pelo modelo. Devido ao nome que corresponde aos recursos existentes, o modelo pode selecionar um recurso existente de um grupo diferente.

**Correção:** Ao executar o modelo Azure Resource Manager para implementar VMs de anfitrião de sessão, torne os dois primeiros caracteres únicos para o nome do grupo de recursos de subscrição.

### <a name="error-deploymentfailed--invalidresourcereference"></a>Erro: ImplementaçãoFailed – InvalidResourceReference

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

**Causa:** Este erro deve-se ao facto de o NIC criado com o modelo Azure Resource Manager ter o mesmo nome que outro NIC já no VNET.

**Correção:** Use um prefixo de hospedeiro diferente.

### <a name="error-deploymentfailed--error-downloading"></a>Erro: ImplementaçãoFailed – Transferência de erros

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

**Causa:** Este erro deve-se a uma rota estática, regra de firewall ou NSG bloqueando o descarregamento do ficheiro zip ligado ao modelo Azure Resource Manager.

**Correção:** Remova a rota estática de bloqueio, a regra da firewall ou a NSG. Opcionalmente, abra o ficheiro json do modelo Azure Resource Manager num editor de texto, pegue o link para o ficheiro zip e descarregue o recurso para uma localização permitida.

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

**Causa:** O administrador de inquilino virtual do Windows virtual especificado não tem uma atribuição de função válida.

**Correção:** O utilizador que criou o inquilino virtual do Windows desktop precisa de iniciar sessão no Windows Virtual Desktop PowerShell e atribuir ao utilizador tentado uma atribuição de função. Se estiver a executar os parâmetros do modelo GitHub Azure Resource Manager, siga estas instruções utilizando os comandos PowerShell:

```PowerShell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
New-RdsRoleAssignment -TenantName <Windows Virtual Desktop tenant name> -RoleDefinitionName "RDS Contributor" -SignInName <UPN>
```

### <a name="error-user-requires-azure-ad-multi-factor-authentication-mfa"></a>Erro: O utilizador requer autenticação multi-factor Azure AD (MFA)

> [!div class="mx-imgBorder"]
> ![A screenshot da sua implementação falhou devido à falta de autenticação multi-factor (MFA)](../media/MFARequiredError.png)

Exemplo de erro bruto:

```Error
"message": "{\r\n  \"status\": \"Failed\",\r\n  \"error\": {\r\n    \"code\": \"ResourceDeploymentFailure\",\r\n    \"message\": \"The resource operation completed with terminal provisioning state 'Failed'.\",\r\n    \"details\": [\r\n      {\r\n        \"code\": \"VMExtensionProvisioningError\",\r\n        \"message\": \"VM has reported a failure when processing extension 'dscextension'. Error message: \\\"DSC Configuration 'FirstSessionHost' completed with error(s). Following are the first few: PowerShell DSC resource MSFT_ScriptResource  failed to execute Set-TargetResource functionality with error message: One or more errors occurred.  The SendConfigurationApply function did not succeed.\\\".\"\r\n      }\r\n    ]\r\n  }\r\n}"
```

**Causa:** O administrador de inquilino virtual do Windows Virtual requer que a Azure AD Multi-Factor Authentication (MFA) entre.

**Correção:** Crie um diretor de serviço e atribua-lhe uma função para o seu inquilino virtual windows desktop seguindo os passos em [Tutorial: Crie diretores de serviço e atribuições de funções com o PowerShell.](create-service-principal-role-powershell.md) Depois de verificar se pode iniciar sôm no Windows Virtual Desktop com o principal do serviço, reexaminar a oferta do Azure Marketplace ou o modelo GitHub Azure Resource Manager, dependendo do método que está a utilizar. Siga as instruções abaixo para introduzir os parâmetros corretos para o seu método.

Se estiver a executar a oferta do Azure Marketplace, forneça valores para que os seguintes parâmetros sejam autenticados adequadamente para o Windows Virtual Desktop:

- Windows Virtual Desktop inquilino RDS Proprietário: Diretor de serviço
- ID de aplicação: A identificação de aplicação do novo responsável de serviço que criou
- Senha/Confirmar Palavra-passe: O segredo de senha que gerou para o principal do serviço
- ID do inquilino Azure AD: O ID do inquilino da Azure AD do diretor de serviço que criou

Se estiver a executar o modelo GitHub Azure Resource Manager, forneça valores para que os seguintes parâmetros autentem adequadamente para o Windows Virtual Desktop:

- Nome principal do utilizador do arrendatário (UPN) ou ID de aplicação: A identificação de aplicação do novo responsável de serviço que criou
- Senha de Admin do Inquilino: O segredo de senha que gerou para o diretor de serviço
- IsServicePrincipal: **verdadeiro**
- AadTenantId: O ID do Inquilino Azure AD do diretor de serviço que criou

### <a name="error-vmsubnet-not-available-when-configuring-virtual-networks"></a>Erro: vmSubnet não disponível ao configurar redes virtuais

**Causa:** No modelo WVD Marketplace, o UI apenas apresenta sub-redes que têm pelo menos tantos endereços IP disponíveis como o número total de VMs especificados no modelo. O número real de endereços IP disponíveis na sub-rede só precisa de ser igual ao número de novos VM a ser implantado, mas isso não pode ser calculado pela UI atual.

**Correção:** Pode especificar uma sub-rede com pelo menos tantos endereços IP disponíveis como o número de VMs adicionados não utilizando o Marketplace UI, isto pode ser feito especificando o nome da sub-rede no parâmetro "**existenteSSubnetName**" quando [recolocar uma implantação](expand-existing-host-pool-2019.md#redeploy-from-azure) ou [implantação existente usando o modelo ARM subjacente do GitHub](create-host-pools-arm-template.md#run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool).

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral sobre a resolução de problemas do Windows Virtual Desktop e as faixas de escalada, consulte [a visão geral da resolução de problemas, o feedback e o suporte](troubleshoot-set-up-overview-2019.md).
- Para resolver problemas enquanto configura uma máquina virtual (VM) no Windows Virtual Desktop, consulte a [configuração da máquina virtual do anfitrião da Sessão](troubleshoot-vm-configuration-2019.md).
- Para resolver problemas com as ligações do cliente virtual do Windows Desktop, consulte [as ligações do serviço de desktop virtual do Windows](troubleshoot-service-connection-2019.md).
- Para resolver problemas com clientes de ambiente de trabalho remoto, consulte [Troubleshoot o cliente Remote Desktop](../troubleshoot-client.md)
- Para resolver problemas ao utilizar o PowerShell com o Windows Virtual Desktop, consulte [o Windows Virtual Desktop PowerShell](troubleshoot-powershell-2019.md).
- Para saber mais sobre o serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup-2019.md)
- Para passar por um tutorial de resolução de [problemas, consulte Tutorial: Implementações de modelos do Gestor de Recursos de Resolução de Problemas](../../azure-resource-manager/templates/template-tutorial-troubleshoot.md).
- Para conhecer as ações de auditoria, consulte [as operações de Auditoria com o Gestor de Recursos.](../../azure-resource-manager/management/view-activity-logs.md)
- Para obter ações para determinar os erros durante a implementação, consulte [as operações de implantação](../../azure-resource-manager/templates/deployment-history.md)da visualização .