---
title: TrustFrameworkPolicy-Azure Active Directory B2C | Microsoft Docs
description: Especifique o elemento TrustFrameworkPolicy de uma política personalizada em Azure Active Directory B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: b8b2cad95929d6132a4f0ae52597fb1633874ff1
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/29/2019
ms.locfileid: "68592107"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma política personalizada é representada como um ou mais arquivos formatados em XML, que se referem entre si em uma cadeia hierárquica. Os elementos XML definem elementos da política, como o esquema de declarações, transformações de declarações, definições de conteúdo, provedores de declarações, perfis técnicos, jornada do usuário e etapas de orquestração. Cada arquivo de política é definido dentro do elemento **TrustFrameworkPolicy** de nível superior de um arquivo de política.

```XML
<TrustFrameworkPolicy
  xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
  xmlns:xsd="https://www.w3.org/2001/XMLSchema"
  xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
  PolicySchemaVersion="0.3.0.0"
  TenantId="mytenant.onmicrosoft.com"
  PolicyId="B2C_1A_TrustFrameworkBase"
  PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
  ...
```


O elemento **TrustFrameworkPolicy** contém os seguintes atributos:

| Atributo | Requerido | Descrição |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sim | A versão do esquema a ser usada para executar a política. O valor deve ser`0.3.0.0` |
| TenantObjectId | Não | O identificador de objeto exclusivo do locatário do Azure Active Directory (Azure AD) B2C. |
| TenantId | Sim | O identificador exclusivo do locatário ao qual essa política pertence. |
| `PolicyId` | Sim | O identificador exclusivo da política. Esse identificador deve ser prefixado por *B2C_1A_* |
| PublicPolicyUri | Sim | O URI da política, que é a combinação da ID do locatário e a ID da política. |
| DeploymentMode | Não | Valores possíveis: `Production`, `Debugging`ou `Development`. `Production` é a predefinição. Use essa propriedade para depurar sua política. Para obter mais informações, consulte [coletando logs](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Não | O ponto de extremidade usado quando **deploymentmode** é definido como `Development`. O valor deve ser `urn:journeyrecorder:applicationinsights`. Para obter mais informações, consulte [coletando logs](active-directory-b2c-troubleshoot-custom.md). |


O exemplo a seguir mostra como especificar o elemento **TrustFrameworkPolicy** :

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkBase"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

## <a name="inheritance-model"></a>Modelo de herança

Esses tipos de arquivos de política normalmente são usados em um percurso do usuário:

- Um arquivo **base** que contém a maioria das definições. Para ajudar na solução de problemas e na manutenção de longo prazo de suas políticas, é recomendável que você faça um número mínimo de alterações nesse arquivo.
- Um arquivo de **extensões** que contém as alterações de configuração exclusivas para seu locatário. Esse arquivo de política é derivado do arquivo base. Use esse arquivo para Adicionar nova funcionalidade ou substituir a funcionalidade existente. Por exemplo, use esse arquivo para federar com novos provedores de identidade.
- Um arquivo de **RP (terceira parte confiável)** que é o único arquivo com foco em tarefa que é invocado diretamente pelo aplicativo de terceira parte confiável, como seus aplicativos Web, móveis ou de área de trabalho. Cada tarefa exclusiva, como inscrição ou entrada, redefinição de senha ou edição de perfil, requer seu próprio arquivo de política de RP. Esse arquivo de política é derivado do arquivo de extensões.

Um aplicativo de terceira parte confiável chama o arquivo de política de RP para executar uma tarefa específica. Por exemplo, para iniciar o fluxo de entrada. A estrutura de experiência de identidade no Azure AD B2C adiciona todos os elementos primeiro do arquivo base e, em seguida, do arquivo de extensões e, finalmente, do arquivo de política de RP para montar a política atual em vigor. Elementos do mesmo tipo e nome no arquivo RP substituem esses elementos nas extensões e as extensões substituem a base. O diagrama a seguir mostra a relação entre os arquivos de política e os aplicativos de terceira parte confiável.

![Diagrama mostrando o modelo de herança de política de estrutura confiável](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

O modelo de herança é o seguinte:

- A política pai e a política filho são do mesmo esquema.
- A política filho em qualquer nível pode herdar da política pai e estendê-la adicionando novos elementos.
- Não há limite para o número de níveis.

Para obter mais informações, consulte Introdução [às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Política de base

Para herdar uma política de outra política, um elemento **BasePolicy** deve ser declarado sob o elemento **TrustFrameworkPolicy** do arquivo de política. O elemento **BasePolicy** é uma referência à política de base da qual essa política é derivada.

O elemento **BasePolicy** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | --------|
| TenantId | 1:1 | O identificador do seu locatário de Azure AD B2C. |
| `PolicyId` | 1:1 | O identificador da política pai. |


O exemplo a seguir mostra como especificar uma política de base. Essa política de **B2C_1A_TrustFrameworkExtensions** é derivada da política de **B2C_1A_TrustFrameworkBase** .

``` XML
<TrustFrameworkPolicy
   xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
   xmlns:xsd="https://www.w3.org/2001/XMLSchema"
   xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
   PolicySchemaVersion="0.3.0.0"
   TenantId="mytenant.onmicrosoft.com"
   PolicyId="B2C_1A_TrustFrameworkExtensions"
   PublicPolicyUri="http://mytenant.onmicrosoft.com/B2C_1A_TrustFrameworkExtensions">

  <BasePolicy>
    <TenantId>yourtenant.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkBase</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

## <a name="policy-execution"></a>Execução da política

Um aplicativo de terceira parte confiável, como um aplicativo Web, móvel ou de área de trabalho, chama a [política de RP (terceira parte confiável)](relyingparty.md). O arquivo de política de RP executa uma tarefa específica, como entrar, redefinir uma senha ou editar um perfil. A política de RP configura a lista de declarações que o aplicativo de terceira parte confiável recebe como parte do token emitido. Vários aplicativos podem usar a mesma política. Todos os aplicativos recebem o mesmo token com declarações e o usuário passa pela mesma jornada do usuário. Um único aplicativo pode usar várias políticas.

Dentro do arquivo de política de RP, você especifica o elemento **DefaultUserJourney** , que aponta para o userjornada. [](userjourneys.md) O percurso do usuário geralmente é definido na política de base ou extensões.

Política de B2C_1A_signup_signin:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase ou B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Uma jornada do usuário define a lógica de negócios do que um usuário passa. Cada jornada do usuário é um conjunto de etapas de orquestração que executa uma série de ações, em sequência em termos de autenticação e coleta de informações.

O arquivo de política **SocialAndLocalAccounts** no [pacote inicial](active-directory-b2c-get-started-custom.md#custom-policy-starter-pack) contém os percursos do usuário SignUpOrSignIn, ProfileEdit, PasswordReset. Você pode adicionar mais viagens de usuário para outros cenários, como alterar um endereço de email ou vincular e desvincular uma conta social.

As etapas de orquestração podem chamar um [perfil técnico](technicalprofiles.md). Um perfil técnico fornece uma estrutura com um mecanismo interno para se comunicar com diferentes tipos de partes. Por exemplo, um perfil técnico pode executar essas ações entre outras:

- Renderize uma experiência do usuário.
- Permita que os usuários entrem com uma conta empresarial ou social, como Facebook, conta Microsoft, Google, Salesforce ou qualquer outro provedor de identidade.
- Configure a verificação de telefone para MFA.
- Ler e gravar dados de e para um repositório de identidades Azure AD B2C.
- Chamar um serviço de API RESTful personalizado.

![Diagrama mostrando o fluxo de execução da política](./media/trustframeworkpolicy/custom-policy-execution.png)

 O elemento **TrustFrameworkPolicy** contém os seguintes elementos:

- BasePolicy conforme especificado acima
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
