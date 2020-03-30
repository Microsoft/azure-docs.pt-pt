---
title: TrustFrameworkPolicy - Diretório Ativo Azure B2C [ Microsoft Docs
description: Especifique o elemento TrustFrameworkPolicy de uma política personalizada no Diretório Ativo Azure B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 01/31/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: c964a7bde0b7db9357c73fc79d2df3170075fcc1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78186391"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma política personalizada é representada como um ou mais ficheiros formatos XML, que se referem uns aos outros numa cadeia hierárquica. Os elementos XML definem elementos da política, tais como o esquema de sinistros, as transformações de sinistros, as definições de conteúdo, os fornecedores de sinistros, os perfis técnicos, a viagem do utilizador e os passos de orquestração. Cada ficheiro de política é definido no elemento **trustframeworkPolicy** de alto nível de um ficheiro de política.

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

| Atributo | Necessário | Descrição |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sim | A versão do esquema que deve ser usada para executar a política. O valor deve ser`0.3.0.0` |
| InquilinoObjectid | Não | O identificador de objeto único do inquilino Azure Ative Directory B2C (Azure AD B2C). |
| TenantId | Sim | O identificador único do inquilino a que pertence esta apólice. |
| PolicyId | Sim | O identificador único para a apólice. Este identificador deve ser pré-fixado *B2C_1A_* |
| Política PúblicaUri | Sim | O URI para a apólice, que é a combinação da identificação do inquilino e da identificação política. |
| Modo de Implantação | Não | Valores `Production`possíveis: ou `Development`. `Production` é a predefinição. Use esta propriedade para desinbugijá-lo. Para mais informações, consulte [Registos de Recolha](troubleshoot-with-application-insights.md). |
| UserJourneyRecorderEndpoint | Não | O ponto final utilizado quando o `Development`Modo de **Implantação** está definido para . O valor `urn:journeyrecorder:applicationinsights`deve ser. Para mais informações, consulte [Registos de Recolha](troubleshoot-with-application-insights.md). |


O exemplo que se segue mostra como especificar o elemento **TrustFrameworkPolicy:**

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

- Um ficheiro **Base** que contém a maioria das definições. Para ajudar na resolução de problemas e manutenção a longo prazo das suas políticas, recomenda-se que efaça um número mínimo de alterações neste ficheiro.
- Um ficheiro **De Extensões** que detém as alterações de configuração únicas para o seu inquilino. Este ficheiro de política é derivado do ficheiro Base. Utilize este ficheiro para adicionar uma nova funcionalidade ou anular a funcionalidade existente. Por exemplo, utilize este ficheiro para federar com novos fornecedores de identidade.
- Um ficheiro **do Partido De Fiação (RP)** que é o ficheiro focado na tarefa única que é invocado diretamente pela aplicação do partido que confia, como as suas aplicações web, móveis ou desktop. Cada tarefa única, como inscrição ou inscrição, reset de password ou edição de perfil, requer o seu próprio ficheiro de política de RP. Este ficheiro de política é derivado do ficheiro Extensões.

Uma aplicação partidária de base chama o ficheiro de política de RP para executar uma tarefa específica. Por exemplo, para iniciar o fluxo de entrada. O Quadro de Experiência de Identidade em Azure AD B2C adiciona todos os elementos primeiro do ficheiro Base, e depois do ficheiro Extensões, e finalmente do ficheiro de política rp para montar a política atual em vigor. Elementos do mesmo tipo e nome no ficheiro RP sobrepõem-se a esses elementos nas Extensões e as Extensões sobrepõem-se à Base. O diagrama seguinte mostra a relação entre os ficheiros políticos e as aplicações partidárias que dependem.

![Diagrama mostrando o modelo de herança de política de enquadramento de confiança](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

O modelo de herança é o seguinte:

- A política dos pais e a política infantil são do mesmo esquema.
- A política infantil a qualquer nível pode herdar da política dos pais e alargá-la adicionando novos elementos.
- Não há limite para o número de níveis.

Para mais informações, consulte [Começar com políticas personalizadas.](custom-policy-get-started.md)

## <a name="base-policy"></a>Política de base

Para herdar uma política de outra política, um elemento **BasePolicy** deve ser declarado ao abrigo do elemento **TrustFrameworkPolicy** do ficheiro político. O elemento **BasePolicy** é uma referência à política de base a partir da qual esta política é derivada.

O elemento **BasePolicy** contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | --------|
| TenantId | 1:1 | O identificador do seu inquilino Azure AD B2C. |
| PolicyId | 1:1 | O identificador da política dos pais. |


O exemplo que se segue mostra como especificar uma política de base. Esta **política B2C_1A_TrustFrameworkExtensions** deriva da política **B2C_1A_TrustFrameworkBase.**

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

Uma aplicação partidária de base, como uma aplicação web, móvel ou desktop, chama a política do [partido que confia (RP).](relyingparty.md) O ficheiro de política de RP executa uma tarefa específica, como iniciar sessão, repor uma palavra-passe ou editar um perfil. A política de RP configura a lista de reclamações que a candidatura do partido que confia recebe como parte do símbolo que é emitido. Várias aplicações podem usar a mesma política. Todas as aplicações recebem o mesmo sinal com reclamações, e o utilizador passa pela mesma viagem de utilizador. Uma única aplicação pode usar múltiplas políticas.

Dentro do ficheiro de política RP, especifica o elemento **DefaultUserJourney,** que aponta para a [UserJourney](userjourneys.md). A viagem de utilizador é geralmente definida na política base ou extensões.

B2C_1A_signup_signin política:

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

Uma viagem de utilizador define a lógica de negócio do que um utilizador passa. Cada viagem do utilizador é um conjunto de passos de orquestração que executa uma série de ações, em sequência em termos de autenticação e recolha de informação.

O ficheiro de política **SocialAndLocalAccounts** no [pacote de arranque](custom-policy-get-started.md#custom-policy-starter-pack) contém as viagens de utilizador SignUpOrSignIn, ProfileEdit, PasswordReset. Pode adicionar mais viagens de utilizador para outros cenários, tais como alterar um endereço de e-mail ou ligar e desvincular uma conta social.

Os passos de orquestração podem chamar um [Perfil Técnico](technicalprofiles.md). Um perfil técnico fornece um quadro com um mecanismo incorporado para comunicar com diferentes tipos de partidos. Por exemplo, um perfil técnico pode realizar estas ações entre outras:

- Render uma experiência de utilizador.
- Permitir que os utilizadores assinem com uma conta social ou empresarial, como Facebook, conta Microsoft, Google, Salesforce ou qualquer outro fornecedor de identidade.
- Configurar a verificação do telefone para o MFA.
- Leia e escreva dados de e para uma loja de identidade Azure AD B2C.
- Ligue para um serviço personalizado de API Restful.

![Diagrama mostrando o fluxo de execução da política](./media/trustframeworkpolicy/custom-policy-execution.png)

 O elemento **TrustFrameworkPolicy** contém os seguintes elementos:

- Política de Base conforme especificado acima
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)
