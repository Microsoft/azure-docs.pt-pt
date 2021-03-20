---
title: Gerir políticas personalizadas com a PowerShell
titleSuffix: Azure AD B2C
description: Utilize o cmdlet PowerShell powershell do Azure Ative para gestão programática das suas políticas personalizadas Azure AD B2C. Criar, ler, atualizar e eliminar políticas personalizadas com o PowerShell.
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: fcc482e215e646fec20516f35641bd05398d2f2d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96928720"
---
# <a name="manage-azure-ad-b2c-custom-policies-with-azure-powershell"></a>Gerir as políticas personalizadas Azure AD B2C com a Azure PowerShell

A Azure PowerShell fornece vários cmdlets para a gestão de políticas personalizadas baseada em linha de comando e scripts no seu inquilino Azure AD B2C. Saiba como utilizar o módulo Azure AD PowerShell para:

* Listar as políticas personalizadas num inquilino Azure AD B2C
* Faça o download de uma apólice de um inquilino
* Atualizar uma política existente, sobreescrevendo o seu conteúdo
* Faça o upload de uma nova política para o seu inquilino Azure AD B2C
* Excluir uma política personalizada de um inquilino

## <a name="prerequisites"></a>Pré-requisitos

* [Inquilino Azure AD B2C,](tutorial-create-tenant.md)e credenciais para um utilizador no diretório com a função [de Administrador de Política B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Políticas personalizadas](custom-policy-get-started.md) enviadas para o seu inquilino
* [Azure AD PowerShell para **módulo de pré-visualização** de gráfico](/powershell/azure/active-directory/install-adv2)

## <a name="connect-powershell-session-to-b2c-tenant"></a>Ligue a sessão PowerShell ao inquilino B2C

Para trabalhar com políticas personalizadas no seu inquilino Azure AD B2C, primeiro precisa ligar a sua sessão PowerShell ao inquilino utilizando o comando [Connect-AzureAD.][Connect-AzureAD]

Execute o seguinte comando, `{b2c-tenant-name}` substituindo-o pelo nome do seu inquilino Azure AD B2C. Inscreva-se com uma conta que atribuiu o papel de [Administrador de Política B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator) no diretório.

```PowerShell
Connect-AzureAD -Tenant "{b2c-tenant-name}.onmicrosoft.com"
```

Saída de comando de exemplo mostrando um sucesso de in:

```Console
PS C:\> Connect-AzureAD -Tenant "contosob2c.onmicrosoft.com"

Account               Environment TenantId                             TenantDomain                 AccountType
-------               ----------- --------                             ------------                 -----------
azureuser@contoso.com AzureCloud  00000000-0000-0000-0000-000000000000 contosob2c.onmicrosoft.com   User
```

## <a name="list-all-custom-policies-in-the-tenant"></a>Listar todas as políticas personalizadas no inquilino

A descoberta de políticas personalizadas permite a um administrador AD B2C rever, gerir e adicionar lógica de negócio às suas operações. Utilize o comando [Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] para devolver uma lista dos IDs das políticas personalizadas num inquilino Azure AD B2C.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy
```

Saída de comando de exemplo:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy

Id
--
B2C_1A_TrustFrameworkBase
B2C_1A_TrustFrameworkExtensions
B2C_1A_signup_signin
B2C_1A_ProfileEdit
B2C_1A_PasswordReset
```

## <a name="download-a-policy"></a>Faça o download de uma apólice

Depois de rever a lista de IDs de política, pode direcionar uma política específica com [a Get-AzureADMSTrustFrameworkPolicy][Get-AzureADMSTrustFrameworkPolicy] para descarregar o seu conteúdo.

```PowerShell
Get-AzureADMSTrustFrameworkPolicy [-Id <policyId>]
```

Neste exemplo, a política com *id B2C_1A_signup_signin* é descarregada:

```Console
PS C:\> Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
<TrustFrameworkPolicy xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06" PolicySchemaVersion="0.3.0.0" TenantId="contosob2c.onmicrosoft.com" PolicyId="B2C_1A_signup_signin" PublicPolicyUri="http://contosob2c.onmicrosoft.com/B2C_1A_signup_signin" TenantObjectId="00000000-0000-0000-0000-000000000000">
  <BasePolicy>
    <TenantId>contosob2c.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  <RelyingParty>
    <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
    <TechnicalProfile Id="PolicyProfile">
      <DisplayName>PolicyProfile</DisplayName>
      <Protocol Name="OpenIdConnect" />
      <OutputClaims>
        <OutputClaim ClaimTypeReferenceId="displayName" />
        <OutputClaim ClaimTypeReferenceId="givenName" />
        <OutputClaim ClaimTypeReferenceId="surname" />
        <OutputClaim ClaimTypeReferenceId="email" />
        <OutputClaim ClaimTypeReferenceId="objectId" PartnerClaimType="sub" />
        <OutputClaim ClaimTypeReferenceId="identityProvider" />
        <OutputClaim ClaimTypeReferenceId="tenantId" AlwaysUseDefaultValue="true" DefaultValue="{Policy:TenantObjectId}" />
      </OutputClaims>
      <SubjectNamingInfo ClaimType="sub" />
    </TechnicalProfile>
  </RelyingParty>
</TrustFrameworkPolicy>
```

Para editar o conteúdo da política localmente, encaneie a saída de comando num ficheiro com o `-OutputFilePath` argumento e, em seguida, abra o ficheiro no seu editor favorito.

Exemplo de envio de comando para um ficheiro:

```PowerShell
# Download and send policy output to a file
Get-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -OutputFilePath C:\RPPolicy.xml
```

## <a name="update-an-existing-policy"></a>Atualizar uma política existente

Depois de editar um ficheiro de política que criou ou descarregou, pode publicar a política atualizada para Azure AD B2C utilizando o comando [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

Se emitir o `Set-AzureADMSTrustFrameworkPolicy` comando com o ID de uma política que já existe no seu inquilino Azure AD B2C, o conteúdo dessa política está substituído.

```PowerShell
Set-AzureADMSTrustFrameworkPolicy [-Id <policyId>] -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Comando de exemplo:

```PowerShell
# Update an existing policy from file
Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
```

Para outros exemplos, consulte a referência de comando [Set-AzureADMSTrustFrameworkPolicy.][Set-AzureADMSTrustFrameworkPolicy]

## <a name="upload-a-new-policy"></a>Faça upload de uma nova política

Quando fizer uma alteração para uma política personalizada que está em execução em produção, talvez queira publicar várias versões da política para cenários de testes de recuo ou A/B. Ou, talvez queira fazer uma cópia de uma política existente, modificá-la com algumas pequenas alterações e, em seguida, carregá-la como uma nova política para uso por uma aplicação diferente.

Utilize o comando [New-AzureADMSTrustFrameworkPolicy][New-AzureADMSTrustFrameworkPolicy] para carregar uma nova política:

```PowerShell
New-AzureADMSTrustFrameworkPolicy -InputFilePath <inputpolicyfilePath> [-OutputFilePath <outputFilePath>]
```

Comando de exemplo:

```PowerShell
# Add new policy from file
New-AzureADMSTrustFrameworkPolicy -InputFilePath C:\SignUpOrSignInv2.xml
```

## <a name="delete-a-custom-policy"></a>Excluir uma política personalizada

Para manter um ciclo de vida de operações limpas, recomendamos que remova periodicamente as políticas personalizadas não habituais. Por exemplo, é melhor remover as versões políticas antigas depois de realizar uma migração para um novo conjunto de políticas e verificar a funcionalidade das novas políticas. Além disso, se tentar publicar um conjunto de políticas personalizadas e receber um erro, poderá fazer sentido remover as políticas que foram criadas como parte da versão falhada.

Utilize o comando [Remove-AzureADMSTrustFrameworkPolicy][Remove-AzureADMSTrustFrameworkPolicy] para eliminar uma apólice do seu inquilino.

```PowerShell
Remove-AzureADMSTrustFrameworkPolicy -Id <policyId>
```

Comando de exemplo:

```PowerShell
# Delete an existing policy
Remove-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin
```

## <a name="troubleshoot-policy-upload"></a>Upload de política de resolução de problemas

Quando tenta publicar uma nova política personalizada ou atualizar uma política existente, a formatação imprópria do XML e os erros na cadeia de herança de ficheiros de política podem causar falhas de validação.

Por exemplo, aqui está uma tentativa de atualizar uma política com conteúdo que contém XML mal formado (a saída é truncada para a brevidade):

```Console
PS C:\> Set-AzureADMSTrustFrameworkPolicy -Id B2C_1A_signup_signin -InputFilePath C:\B2C_1A_signup_signin.xml
Set-AzureADMSTrustFrameworkPolicy : Error occurred while executing PutTrustFrameworkPolicy
Code: AADB2C
Message: Validation failed: 1 validation error(s) found in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com".Schema validation error found at line
14 col 55 in policy "B2C_1A_SIGNUP_SIGNIN" of tenant "contosob2c.onmicrosoft.com": The element 'OutputClaims' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06' cannot contain text. List of possible elements expected: 'OutputClaim' in namespace
'http://schemas.microsoft.com/online/cpim/schemas/2013/06'.
...
```

Para obter informações sobre a resolução de problemas de políticas personalizadas, consulte [as políticas personalizadas do Azure AD B2C e o Quadro de Experiência de Identidade](./troubleshoot-custom-policies.md).

## <a name="next-steps"></a>Passos seguintes

Para obter informações sobre a utilização do PowerShell para implementar políticas personalizadas como parte de um pipeline de integração contínua/entrega contínua (CI/CD), consulte [implementar políticas personalizadas a partir de um oleoduto Azure DevOps](deploy-custom-policies-devops.md).

<!-- LINKS - External -->
[Connect-AzureAD]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[Get-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/get-azureadmstrustframeworkpolicy
[New-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/new-azureadmstrustframeworkpolicy
[Remove-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/remove-azureadmstrustframeworkpolicy
[Set-AzureADMSTrustFrameworkPolicy]: /powershell/module/azuread/set-azureadmstrustframeworkpolicy
