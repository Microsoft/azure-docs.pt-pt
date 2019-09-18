---
title: Gerenciar o acesso do usuário no Azure Active Directory B2C | Microsoft Docs
description: Saiba como identificar os pequenos, coletar dados de nascimento e de país/região e obter a aceitação dos termos de uso em seu aplicativo usando Azure AD B2C.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 98d3fa50f405658b33f879ed8e7b95667cddcedf
ms.sourcegitcommit: f209d0dd13f533aadab8e15ac66389de802c581b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71064131"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gerenciar o acesso do usuário no Azure Active Directory B2C

Este artigo discute como gerenciar o acesso do usuário aos seus aplicativos usando Azure Active Directory B2C (Azure AD B2C). O gerenciamento de acesso em seu aplicativo inclui:

- Identificar menores e controlar o acesso do usuário ao seu aplicativo.
- Exigindo o consentimento dos menores para os secundários usarem seus aplicativos.
- Coletando dados de nascimento e de país/região dos usuários.
- Capturando um contrato de termos de uso e acesso de retenção.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Controlar acesso secundário

Os aplicativos e as organizações podem decidir bloquear os menores de usar aplicativos e serviços que não são destinados a esse público. Como alternativa, os aplicativos e as organizações podem optar por aceitar menores e, subsequentemente, gerenciar o consentimento dos pais e fornecer experiências permitidas para menores, conforme determinado pelas regras de negócios e permitidas pela regulamentação.

Se um usuário for identificado como um secundário, você poderá definir o fluxo do usuário em Azure AD B2C como uma das três opções:

- **Enviar um ID_TOKEN JWT assinado de volta para o aplicativo**: O usuário é registrado no diretório e um token é retornado para o aplicativo. Em seguida, o aplicativo continua aplicando regras de negócio. Por exemplo, o aplicativo pode continuar com um processo de consentimento dos pais. Para usar esse método, escolha receber as declarações **ageGroup** e **consentProvidedForMinor** do aplicativo.

- **Enviar um token JSON não assinado para o aplicativo**: Azure AD B2C notifica o aplicativo que o usuário é um secundário e fornece o status do consentimento do usuário. Em seguida, o aplicativo continua aplicando regras de negócio. Um token JSON não conclui uma autenticação bem-sucedida com o aplicativo. O aplicativo deve processar o usuário não autenticado de acordo com as declarações incluídas no token JSON, que pode incluir **nome**, **email**, **ageGroup**e **consentProvidedForMinor**.

- **Bloquear o usuário**: Se um usuário for um secundário e o consentimento dos pais não tiver sido fornecido, Azure AD B2C poderá notificar o usuário de que eles estão bloqueados. Nenhum token é emitido, o acesso é bloqueado e a conta de usuário não é criada durante uma jornada de registro. Para implementar essa notificação, você fornece uma página de conteúdo HTML/CSS adequada para informar o usuário e apresentar as opções apropriadas. Nenhuma ação adicional é necessária para o aplicativo para novos registros.

## <a name="get-parental-consent"></a>Obter consentimento dos pais

Dependendo da regulamentação do aplicativo, o consentimento dos pais pode precisar ser concedido por um usuário que é verificado como um adulto. A Azure AD B2C não fornece uma experiência para verificar a idade de um indivíduo e, em seguida, permitir que um adulto verificado conceda a um secundário o consentimento dos pais. Essa experiência deve ser fornecida pelo aplicativo ou por outro provedor de serviços.

Veja a seguir um exemplo de um fluxo de usuário para a coleta de consentimento dos pais:

1. Uma operação de [API do Graph Azure Active Directory](/previous-versions/azure/ad/graph/api/api-catalog) identifica o usuário como um secundário e retorna os dados do usuário para o aplicativo na forma de um token JSON não assinado.

2. O aplicativo processa o token JSON e mostra uma tela para o secundário, notificando-os de que o consentimento dos pais é necessário e solicitando o consentimento de um pai online.

3. Azure AD B2C mostra uma jornada de entrada em que o usuário pode entrar normalmente e emite um token para o aplicativo que está definido para incluir **legalAgeGroupClassification = "minorWithParentalConsent"** . O aplicativo coleta o endereço de email do pai e verifica se o pai é um adulto. Para fazer isso, ele usa uma fonte confiável, como um escritório de ID nacional, verificação de licença ou prova de cartão de crédito. Se a verificação for bem-sucedida, o aplicativo solicitará o secundário para entrar usando o fluxo de usuário Azure AD B2C. Se o consentimento for negado (por exemplo, se **legalAgeGroupClassification = "minorWithoutParentalConsent"** ), Azure ad B2C retornará um token JSON (não um logon) para o aplicativo a fim de reiniciar o processo de consentimento. Opcionalmente, é possível personalizar o fluxo do usuário para que um secundário ou um adulto possa obter acesso a uma conta secundária enviando um código de registro para o endereço de email do secundário ou o endereço de email do adulto no registro.

4. O aplicativo oferece uma opção para o consentimento de secundário para revogar.

5. Quando o secundário ou o adulto revoga o consentimento, o API do Graph do Azure AD pode ser usado para alterar **consentProvidedForMinor** para **negado**. Como alternativa, o aplicativo pode optar por excluir um secundário cujo consentimento foi revogado. Opcionalmente, é possível personalizar o fluxo do usuário para que o secundário autenticado (ou o pai que está usando a conta secundária) possa revogar o consentimento. Azure AD B2C registros **consentProvidedForMinor** como **negado**.

Para obter mais informações sobre **legalAgeGroupClassification**, **consentProvidedForMinor**e **ageGroup**, consulte [tipo de recurso de usuário](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Para obter mais informações sobre atributos personalizados, consulte [usar atributos personalizados para coletar informações sobre seus consumidores](active-directory-b2c-reference-custom-attr.md). Ao endereçar atributos estendidos usando o API do Graph do Azure AD, você deve usar a versão longa do atributo, como *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Coletar dados de nascimento e país/região

Os aplicativos podem contar com Azure AD B2C para reunir a data de nascimento (DOB) e informações de país/região de todos os usuários durante o registro. Se essas informações ainda não existirem, o aplicativo poderá solicitá-la do usuário durante a próxima jornada de autenticação (entrada). Os usuários não podem continuar sem fornecer suas informações de DOB e país/região. Azure AD B2C usa as informações para determinar se o indivíduo é considerado um secundário de acordo com os padrões regulatórios desse país/região.

Um fluxo de usuário personalizado pode coletar informações de DOB e país/região e usar Azure AD B2C transformação de declarações para determinar o **ageGroup** e persistir o resultado (ou manter as informações de DOB e país/região diretamente) no diretório.

As etapas a seguir mostram a lógica que é usada para calcular **ageGroup** da data de nascimento do usuário:

1. Tente localizar o país pelo código do país na lista. Se o país não for encontrado, volte para o **padrão**.

2. Se o nó **MinorConsent** estiver presente no elemento Country:

    a. Calcule a data em que o usuário deve ter sido nascido para ser considerado um adulto. Por exemplo, se a data atual for 14 de março de 2015 e **MinorConsent** for 18, a data de nascimento não deverá ser posterior a 14 de março de 2000.

    b. Compare a data de nascimento mínima com a data de nascimento real. Se a data de nascimento mínima for anterior à data de nascimento do usuário, o cálculo retornará **menor** como o cálculo do grupo de idade.

3. Se o nó **MinorNoConsentRequired** estiver presente no elemento Country, repita as etapas 2a e 2B usando o valor de **MinorNoConsentRequired**. A saída de 2B retornará **MinorNoConsentRequired** se a data de nascimento mínima for anterior à data de nascimento do usuário.

4. Se nenhum cálculo retornar true, o cálculo retornará **adulto**.

Se um aplicativo tiver coletado de forma confiável DOB ou dados de país/região por outros métodos, o aplicativo poderá usar o API do Graph para atualizar o registro de usuário com essas informações. Por exemplo:

- Se um usuário for conhecido como adulto, atualize o atributo de diretório **ageGroup** com um valor de **adulto**.
- Se um usuário for conhecido como um secundário, atualize o atributo de diretório **ageGroup** com um valor de **Minor** e defina **consentProvidedForMinor**, conforme apropriado.

Para obter mais informações sobre como coletar dados do DOB, consulte [usar a retenção de idade em Azure ad B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Capturar contrato de termos de uso

Ao desenvolver seu aplicativo, você normalmente captura a aceitação dos termos de uso dos usuários em seus aplicativos sem ou apenas participação secundária do diretório do usuário. No entanto, é possível usar um fluxo de usuário Azure AD B2C para coletar a aceitação de um usuário dos termos de uso, restringir o acesso se a aceitação não for concedida e impor a aceitação de futuras alterações aos termos de uso, com base na data da aceitação mais recente e na data da  versão mais recente dos termos de uso.

Os **termos de uso** também podem incluir "consentimento para compartilhar dados com terceiros". Dependendo das normas locais e das regras de negócio, você pode reunir a aceitação de ambas as condições do usuário, ou pode permitir que o usuário aceite uma condição e não a outra.

As etapas a seguir descrevem como você pode gerenciar os termos de uso:

1. Registre a aceitação dos termos de uso e a data de aceitação usando o API do Graph e os atributos estendidos. Você pode fazer isso usando fluxos de usuário internos e personalizados. Recomendamos que você crie e use os atributos **extension_termsOfUseConsentDateTime** e **extension_termsOfUseConsentVersion** .

2. Crie uma caixa de seleção necessária rotulada "aceitar termos de uso" e registre o resultado durante a inscrição. Você pode fazer isso usando fluxos de usuário internos e personalizados.

3. Azure AD B2C armazena os termos de contrato de uso e a aceitação do usuário. Você pode usar o API do Graph para consultar o status de qualquer usuário lendo o atributo de extensão que é usado para registrar a resposta (por exemplo, ler **termsOfUseTestUpdateDateTime**). Você pode fazer isso usando fluxos de usuário internos e personalizados.

4. Exija a aceitação dos termos de uso atualizados comparando a data de aceitação à data da versão mais recente dos termos de uso. Você pode comparar as datas somente usando um fluxo de usuário personalizado. Use o atributo estendido **extension_termsOfUseConsentDateTime**e compare o valor com a declaração de **termsOfUseTextUpdateDateTime**. Se a aceitação for antiga, Force uma nova aceitação exibindo uma tela autodeclarada. Caso contrário, bloqueie o acesso usando a lógica de política.

5. Exigir aceitação dos termos de uso atualizados comparando o número de versão da aceitação ao número de versão aceito mais recente. Você pode comparar os números de versão somente usando um fluxo de usuário personalizado. Use o atributo estendido **extension_termsOfUseConsentDateTime**e compare o valor com a declaração de **extension_termsOfUseConsentVersion**. Se a aceitação for antiga, Force uma nova aceitação exibindo uma tela autodeclarada. Caso contrário, bloqueie o acesso usando a lógica de política.

Você pode capturar a aceitação dos termos de uso nos seguintes cenários:

- Um novo usuário está se inscrevendo. Os termos de uso são exibidos e o resultado da aceitação é armazenado.
- Um usuário está entrando em quem aceitou anteriormente os termos de uso mais recentes ou ativos. Os termos de uso não são exibidos.
- Um usuário está entrando em quem ainda não aceitou os termos de uso mais recentes ou ativos. Os termos de uso são exibidos e o resultado da aceitação é armazenado.
- Um usuário está entrando em quem já aceitou uma versão mais antiga dos termos de uso, que agora são atualizados para a versão mais recente. Os termos de uso são exibidos e o resultado da aceitação é armazenado.

A imagem a seguir mostra o fluxo de usuário recomendado:

![Diagrama do fluxograma de fluxo mostrando o fluxo de usuário de aceitação recomendado](./media/manage-user-access/user-flow.png)

Veja a seguir um exemplo de um consentimento de termos de uso com base em DateTime em uma declaração:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentDateTime" TransformationMethod="GetCurrentDateTime">
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="currentDateTime" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequired" TransformationMethod="IsTermsOfUseConsentRequired">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentDateTime" TransformationClaimType="termsOfUseConsentDateTime" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="termsOfUseTextUpdateDateTime" DataType="dateTime" Value="2098-01-30T23:03:45" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="result" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

Veja a seguir um exemplo de um consentimento de termos de uso baseado em versão em uma declaração:

```xml
<ClaimsTransformations>
  <ClaimsTransformation Id="GetEmptyTermsOfUseConsentVersionForNewUser" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value=""/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="GetNewUserAgreeToTermsOfUseConsentVersion" TransformationMethod="CreateStringClaim">
    <InputParameters>
      <InputParameter Id="value" DataType="string" Value="V1"/>
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="createdClaim" />
    </OutputClaims>
  </ClaimsTransformation>
  <ClaimsTransformation Id="IsTermsOfUseConsentRequiredForVersion" TransformationMethod="CompareClaimToValue">
    <InputClaims>
      <InputClaim ClaimTypeReferenceId="extension_termsOfUseConsentVersion" TransformationClaimType="inputClaim1" />
    </InputClaims>
    <InputParameters>
      <InputParameter Id="compareTo" DataType="string" Value="V1" />
      <InputParameter Id="operator" DataType="string" Value="not equal" />
      <InputParameter Id="ignoreCase" DataType="string" Value="true" />
    </InputParameters>
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="termsOfUseConsentRequired" TransformationClaimType="outputClaim" />
    </OutputClaims>
  </ClaimsTransformation>
</ClaimsTransformations>
```

## <a name="next-steps"></a>Passos seguintes

- Para saber como excluir e exportar dados do usuário, consulte [gerenciar dados do usuário](manage-user-data.md).
