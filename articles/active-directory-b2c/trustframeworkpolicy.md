---
title: TrustFrameworkPolicy - Azure Ative Directory B2C / Microsoft Docs
description: Especificar o elemento TrustFrameworkPolicy de uma política personalizada no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 29eddbcfb7c0da98e5438f968dd3976b77a44680
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85203100"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma política personalizada é representada como um ou mais ficheiros com formato XML, que se referem uns aos outros numa cadeia hierárquica. Os elementos XML definem elementos da política, tais como o esquema de reclamações, as transformações de reclamações, as definições de conteúdo, os fornecedores de sinistros, os perfis técnicos, a viagem do utilizador e as etapas de orquestração. Cada ficheiro de política é definido dentro do elemento **TrustFrameworkPolicy** de alto nível de um ficheiro de política.

```xml
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

| Atributo | Obrigatório | Descrição |
|---------- | -------- | ----------- |
| PolíticaSesoversão | Sim | A versão do esquema que deve ser usada para executar a apólice. O valor deve ser `0.3.0.0` |
| TenantObjectId | Não | O identificador de objetos único do inquilino Azure Ative Directory B2C (Azure AD B2C). |
| TenantId | Sim | O identificador único do inquilino a que esta política pertence. |
| PolicyId | Sim | O identificador único para a apólice. Este identificador deve ser pré-fixado por *B2C_1A_* |
| PublicPolicyUri | Sim | O URI para a apólice, que é a combinação da identificação do inquilino e a identificação da apólice. |
| Envio de munições | Não | Valores possíveis: `Production` , ou `Development` . . `Production` é a predefinição. Use esta propriedade para depurar a sua apólice. Para obter mais informações, consulte [a Recolha de Registos.](troubleshoot-with-application-insights.md) |
| UserJourneyRecorderEndpoint | Não | O ponto final que é utilizado quando **o DeploymentMode** está definido para `Development` . O valor deve `urn:journeyrecorder:applicationinsights` ser. Para obter mais informações, consulte [a Recolha de Registos.](troubleshoot-with-application-insights.md) |


O exemplo a seguir mostra como especificar o elemento **TrustFrameworkPolicy:**

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

Estes tipos de ficheiros de política são normalmente utilizados numa viagem de utilizador:

- Um ficheiro **Base** que contém a maioria das definições. Para ajudar na resolução de problemas e na manutenção a longo prazo das suas políticas, recomenda-se que faça um número mínimo de alterações neste ficheiro.
- Um ficheiro **Extensões** que contém as alterações de configuração únicas para o seu inquilino. Este ficheiro de política é derivado do ficheiro Base. Utilize este ficheiro para adicionar uma nova funcionalidade ou sobrepor-se à funcionalidade existente. Por exemplo, utilize este ficheiro para federar com novos fornecedores de identidade.
- Um ficheiro **Relying Party (RP)** que é o único ficheiro focado em tarefas que é invocado diretamente pela aplicação do partido, como as aplicações web, mobile ou desktop. Cada tarefa única, como inscrição ou inscrição, reset de palavra-passe ou edição de perfil, requer o seu próprio ficheiro de política rp. Este ficheiro de política é derivado do ficheiro Extensões.

Uma aplicação de partido dependente chama o ficheiro de política rp para executar uma tarefa específica. Por exemplo, para iniciar o fluxo de entrada. O Quadro de Experiência de Identidade em Azure AD B2C adiciona todos os elementos primeiro do ficheiro Base, e depois do ficheiro Extensões, e finalmente do ficheiro de política rp para montar a política atual em vigor. Elementos do mesmo tipo e nome no ficheiro RP substituem os elementos nas Extensões e extensões sobrepõe-se à Base. O diagrama que se segue mostra a relação entre os ficheiros políticos e as aplicações do partido em gestão.

![Diagrama mostrando o modelo de herança de política de confiança](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

O modelo de herança é o seguinte:

- A política dos pais e a política infantil são do mesmo esquema.
- A política infantil a qualquer nível pode herdar da política dos pais e alargá-la adicionando novos elementos.
- Não há limite para o número de níveis.

Para mais informações, consulte [Começar com as políticas personalizadas.](custom-policy-get-started.md)

## <a name="base-policy"></a>Política de base

Para herdar uma política de outra política, um elemento **basePolicy** deve ser declarado sob o elemento **TrustFrameworkPolicy** do ficheiro político. O elemento **BasePolicy** é uma referência à política de base da qual esta política é derivada.

O elemento **BasePolicy** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | --------|
| TenantId | 1:1 | O identificador do seu inquilino Azure AD B2C. |
| PolicyId | 1:1 | O identificador da política dos pais. |


O exemplo a seguir mostra como especificar uma política de base. Esta **política B2C_1A_TrustFrameworkExtensions** deriva da política **B2C_1A_TrustFrameworkBase.**

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

## <a name="policy-execution"></a>Execução política

Uma aplicação do partido dependente, como uma aplicação web, móvel ou desktop, chama a política do [partido dependente (RP).](relyingparty.md) O ficheiro de política RP executa uma tarefa específica, como iniciar sessão, redefinir uma palavra-passe ou editar um perfil. A política rp configura a lista de reclamações que a aplicação do partido em gestão recebe como parte do símbolo emitido. Várias aplicações podem usar a mesma política. Todas as aplicações recebem o mesmo token com reclamações, e o utilizador passa pela mesma viagem de utilizador. Uma única aplicação pode usar várias políticas.

Dentro do ficheiro de política RP, especifique o elemento **DefaultUserJourney,** que aponta para a [Jornada do Utilizador](userjourneys.md). A viagem do utilizador geralmente é definida na política base ou extensões.

B2C_1A_signup_signin política:

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase ou B2C_1A_TrustFrameworkExtensionPolicy:

```xml
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
  ...
```

Uma viagem de utilizador define a lógica de negócio do que um utilizador passa. Cada viagem de utilizador é um conjunto de etapas de orquestração que realiza uma série de ações, em sequência em termos de autenticação e recolha de informação.

O ficheiro de política **SocialAndLocalAccounts** no [pacote inicial](custom-policy-get-started.md#custom-policy-starter-pack) contém as viagens de utilizador SignUpOrSignIn, ProfileEdit, PasswordReset. Pode adicionar mais viagens de utilizador para outros cenários, como alterar um endereço de e-mail ou ligar e desvincular uma conta social.

As etapas de orquestração podem chamar um [Perfil Técnico](technicalprofiles.md). Um perfil técnico fornece um quadro com um mecanismo integrado para comunicar com diferentes tipos de partes. Por exemplo, um perfil técnico pode realizar estas ações entre outras:

- Torne uma experiência de utilizador.
- Permitir que os utilizadores assinem com uma conta social ou empresarial, como Facebook, Conta Microsoft, Google, Salesforce ou qualquer outro fornecedor de identidade.
- Configurar a verificação telefónica para o MFA.
- Leia e escreva dados de e para uma loja de identidade Azure AD B2C.
- Ligue para um serviço de API restful personalizado.

![Diagrama mostrando o fluxo de execução da política](./media/trustframeworkpolicy/custom-policy-execution.png)

 O elemento **TrustFrameworkPolicy** contém os seguintes elementos:

- BasePolicy conforme especificado acima
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
