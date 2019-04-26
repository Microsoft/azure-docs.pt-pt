---
title: TrustFrameworkPolicy - Azure Active Directory B2C | Documentos da Microsoft
description: Especifique o elemento de TrustFrameworkPolicy de uma política personalizada no Azure Active Directory B2C.
services: active-directory-b2c
author: davidmu1
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 09/10/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 558e9c3a3bfd43f6ceb958bc3be55d58e1eb7f91
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60360491"
---
# <a name="trustframeworkpolicy"></a>TrustFrameworkPolicy

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

Uma política personalizada é representada como um ou mais formatada em XML ficheiros, o que fazer referência uns aos outros numa cadeia hierárquica. Os elementos XML definem elementos de diretiva, como o esquema de afirmações, transformações de afirmações, definições de conteúdo, fornecedores de afirmações, perfis técnicos, o percurso do utilizador e etapas da orquestração. Cada ficheiro de política é definido dentro de nível superior **TrustFrameworkPolicy** elemento de um ficheiro de política. 

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


O **TrustFrameworkPolicy** elemento contém os seguintes atributos:

| Atributo | Necessário | Descrição |
|---------- | -------- | ----------- |
| PolicySchemaVersion | Sim | A versão de esquema que está a ser utilizado para executar a política. O valor tem de ser `0.3.0.0` |
| TenantObjectId | Não | O identificador de objeto exclusivo do inquilino do Azure Active Directory (Azure AD) B2C. |
| TenantId | Sim | O identificador exclusivo do inquilino ao qual pertence esta política. |
| PolicyId | Sim | O identificador exclusivo para a política. Este identificador deve ser antecedido por *B2C_1A_* |
| PublicPolicyUri | Sim | O URI para a política, o que é a combinação do ID de inquilino e o ID de política. |
| DeploymentMode | Não | Valores possíveis: `Production`, `Debugging`, ou `Development`. `Production` é a predefinição. Use essa propriedade para depurar a sua política. Para obter mais informações, consulte [recolher registos](active-directory-b2c-troubleshoot-custom.md). |
| UserJourneyRecorderEndpoint | Não | O ponto final que é utilizado quando **DeploymentMode** está definida como `Development`. O valor tem de ser `urn:journeyrecorder:applicationinsights`. Para obter mais informações, consulte [recolher registos](active-directory-b2c-troubleshoot-custom.md). |


O exemplo seguinte mostra como especificar a **TrustFrameworkPolicy** elemento:

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

Esses tipos de arquivos de diretiva são normalmente usados num percurso do utilizador:

- R **Base** ficheiro que contém a maioria das definições de. Para ajudar na resolução de problemas e a longo prazo manutenção das suas políticas, recomenda-se que faça um número mínimo de alterações a este ficheiro.
- Uma **extensões** ficheiro que contém as alterações de configuração exclusiva para o seu inquilino. Este ficheiro de política é obtido a partir do ficheiro de Base. Utilize este ficheiro para adicionar novas funcionalidades ou substituir a funcionalidade existente. Por exemplo, use esse arquivo para federar com novos fornecedores de identidade.
- R **terceiros entidade Confiadora (RP)** ficheiros, ou seja, o único ficheiro centrados na tarefa que é invocado diretamente pela aplicação de terceiros entidade confiadora, como seus aplicativos de web, móveis ou ambiente de trabalho. Cada tarefa exclusiva, como a reposição de palavra-passe de inscrição ou início de sessão, ou edição de perfil, requer seu próprio arquivo de diretiva da RP. Este ficheiro de política é derivado do arquivo de extensões. 

Um aplicativo de parte confiável chama o arquivo de diretiva da RP para executar uma tarefa específica. Por exemplo, para iniciar o fluxo de início de sessão. O Framework de experiência de identidade no Azure AD B2C adiciona todos os elementos, ou primeiro do ficheiro de Base e, em seguida, do arquivo de extensões e, finalmente, do arquivo de diretiva da RP para montar a política atual em vigor. Elementos do mesmo tipo e nome no ficheiro de RP substituam esses elementos nas extensões e extensões substitui Base. O diagrama seguinte mostra a relação entre os ficheiros de política e as aplicações de terceiros entidade confiadora.

![Modelo de herança](./media/trustframeworkpolicy/custom-policy-Inheritance-model.png)

O modelo de herança é o seguinte:

- A política de principal e a política de subordinados são o mesmo esquema.
- A política de subordinado em qualquer nível pode herdar da política de principal e estendê-lo através da adição de novos elementos.
- Não existe nenhum limite no número de níveis.

Para obter mais informações, consulte [introdução às políticas personalizadas](active-directory-b2c-get-started-custom.md).

## <a name="base-policy"></a>Política base

Herdar de uma política de outra diretiva, uma **BasePolicy** elemento tem de ser declarado sob a **TrustFrameworkPolicy** elemento do arquivo de política. O **BasePolicy** elemento é uma referência à política de base do qual esta política é derivada.  

O **BasePolicy** elemento contém os seguintes elementos:

| Elemento | Ocorrências | Descrição |
| ------- | ----------- | --------|
| TenantId | 1:1 | O identificador do inquilino do Azure AD B2C. |
| PolicyId | 1:1 | O identificador da política de principal. |


O exemplo seguinte mostra como especificar uma política de base. Isso **B2C_1A_TrustFrameworkExtensions** deriva a política a **B2C_1A_TrustFrameworkBase** política. 

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

## <a name="policy-execution"></a>Execução de políticas

Uma aplicação da entidade confiadora de terceiros, tais como aplicações web, móveis ou ambiente de trabalho, chama o [da entidade confiadora política de terceiros (RP)](relyingparty.md). O ficheiro de política RP executa uma tarefa específica, como iniciar sessão, repor uma palavra-passe ou editar um perfil. A política RP configura a lista de afirmações que o aplicativo de terceiros entidade confiadora recebe como parte do token que é emitido. Vários aplicativos podem usar a mesma política. Todos os aplicativos recebem o mesmo token com declarações e o utilizador atravessa o mesma percurso do utilizador. Um único aplicativo pode usar várias políticas.

Dentro do arquivo de política RP, especifique a **DefaultUserJourney** elemento, que aponta para o [UserJourney](userjourneys.md). Normalmente, o percurso do utilizador é definido na política de Base ou extensões.

Política de B2C_1A_signup_signin:

```XML
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn">
  ...
```

B2C_1A_TrustFrameWorkBase ou B2C_1A_TrustFrameworkExtensionPolicy:

```XML
<UserJourneys>
  <UserJourney Id="SignOrSignIn">
  ...
```

Um percurso do utilizador define a lógica de negócios do que atravessa um utilizador. Cada percurso do utilizador é um conjunto de etapas da orquestração que realiza uma série de ações, na sequência em termos de recolha de informações de autenticação e. 

O **SocialAndLocalAccounts** ficheiro de política na [pacote de iniciante](https://docs.microsoft.com/azure/active-directory-b2c/active-directory-b2c-get-started-custom#download-starter-pack-and-modify-policies) contém SignUpOrSignIn, ProfileEdit, PasswordReset jornadas de utilizador. Pode adicionar mais jornadas de utilizador para cenários de outro, tal como alterar um endereço de e-mail, ligar e desligar uma conta de redes sociais ou repor uma palavra-passe. 

Os passos de orquestração podem chamar uma [perfil técnico](technicalprofiles.md). Um perfil técnico fornece uma estrutura com um mecanismo interno para comunicar com diferentes tipos de partes. Por exemplo, um perfil técnico pode efetuar estas ações entre outras:

- Compor uma experiência de utilizador.
- Permitir aos utilizadores iniciar sessão social ou uma conta de empresa, como o Facebook, conta Microsoft, Google, Salesforce ou qualquer outro fornecedor de identidade.
- Configure a verificação do telefone para a MFA.
- Ler e escrever dados de e para um repositório de identidades do Azure AD B2C.
- Chame um serviço de Restful API personalizado.

![Execução de políticas](./media/trustframeworkpolicy/custom-policy-execution.png)

 O **TrustFrameworkPolicy** elemento contém os seguintes elementos:

- BasePolicy conforme especificado acima
- [BuildingBlocks](buildingblocks.md)
- [ClaimsProviders](claimsproviders.md)
- [UserJourneys](userjourneys.md)
- [RelyingParty](relyingparty.md)

