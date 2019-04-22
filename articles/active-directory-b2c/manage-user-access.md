---
title: Gerir o acesso de utilizador no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba como identificar os menores, recolher a data de nascimento e país dados e obter aceitação dos termos de utilização na sua aplicação com o Azure AD B2C.
services: active-directory-b2c
author: davidmu1
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: davidmu
ms.subservice: B2C
ms.openlocfilehash: 2fb9de7781fae51792e2802002a3cf68974dec83
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/18/2019
ms.locfileid: "59783740"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gerir o acesso de utilizador no Azure Active Directory B2C

Este artigo descreve como gerir o acesso de utilizador às suas aplicações com o Azure Active Directory (Azure AD) B2C. Gestão de acesso na sua aplicação inclui:

- Identificar os menores e controlar o acesso de utilizador à sua aplicação.
- Exigir consentimento dos pais para os menores utilizar as suas aplicações.
- Recolha de dados de nascimento e país de utilizadores.
- Captura de um contrato de termos de utilização e controlo de acesso.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Controlo de acesso secundário

Aplicações e organizações podem optar por bloquear menores de utilização de aplicações e serviços que não são direcionados para esse público-alvo. Em alternativa, aplicações e as organizações podem decidir aceitar os menores e, em seguida, gerir a autorização parental e proporcionar experiências permitidas para os menores, conforme definido pelas regras de negócio e permitido pela regulamentação. 

Se um utilizador é identificado como menor, pode definir o fluxo de utilizador no Azure AD B2C para uma das três opções:

- **Enviar um id_token JWT assinada para a aplicação**: O utilizador está registado no diretório e um token é retornado ao aplicativo. O aplicativo, em seguida, procede-se ao aplicar regras de negócio. Por exemplo, a aplicação pode continuar com um processo de consentimento dos pais. Para utilizar este método, optar por receber as **ageGroup** e **consentProvidedForMinor** afirmações da aplicação.

- **Enviar um token JSON não assinado para a aplicação**: O Azure AD B2C notifica a aplicação que o usuário é menor e fornece o estado de consentimento dos pais do utilizador. O aplicativo, em seguida, procede-se ao aplicar regras de negócio. Um token JSON não é concluída uma autenticação com êxito com o aplicativo. A aplicação deve processar o utilizador não autenticado, de acordo com as declarações incluídas no token JSON, que pode incluir **name**, **e-mail**, **ageGroup**e **consentProvidedForMinor**.

- **Impedir o utilizador**: Se um utilizador for menor e consentimento dos pais não foi fornecido, do Azure AD B2C pode notificar o utilizador que ele está bloqueado. Nenhum token for emitido, o acesso é bloqueado e a conta de utilizador não é criada durante uma jornada de registo. Para implementar esta notificação, é fornecer uma página de conteúdo de HTML/CSS adequada para informar o utilizador e as opções apropriadas presentes. É necessária nenhuma ação adicional por aplicação para novas inscrições.

## <a name="get-parental-consent"></a>Obter o consentimento dos pais

Dependendo regulamento do aplicativo, o consentimento dos pais poderá ter de ser concedida por um usuário que é validado como um adulto. O Azure AD B2C não fornece uma experiência para verificar a idade de uma pessoa e, em seguida, permitir que um adulto verificado conceder autorização parental a menor. Esta experiência deve ser fornecida pelo aplicativo ou outro fornecedor de serviços.

Segue-se um exemplo de um fluxo de utilizador para a coleta de consentimento dos pais:

1. Uma [do Azure Active Directory Graph API](/previous-versions/azure/ad/graph/api/api-catalog) operação identifica o utilizador como menor e devolve os dados de utilizador para o aplicativo sob a forma de um token JSON não assinado.

2. O aplicativo processa o token JSON e mostra um ecrã para o menor, notificar em contato com ele ou ela que é necessário o consentimento dos pais e solicitar o consentimento de um encarregado de educação online. 

3. O Azure AD B2C mostra uma jornada de início de sessão que o utilizador pode iniciar sessão normalmente para e emite um token para a aplicação que está definida para incluir **legalAgeGroupClassification = "minorWithParentalConsent"**. A aplicação recolhe o endereço de e-mail do principal e verifica se o elemento principal é um adulto. Para fazer isso, ele usa uma origem fidedigna, como um nacional ID office, verificação de licença ou prova de cartão de crédito. Se a verificação for bem sucedida, a aplicação pede-lhe o menor para iniciar sessão com o fluxo de utilizador do Azure AD B2C. Se o consentimento é negado (por exemplo, se **legalAgeGroupClassification = "minorWithoutParentalConsent"**), Azure AD B2C devolve um token JSON (não um início de sessão) para o aplicativo reinicie o processo de consentimento. É possível que, opcionalmente personalizar o fluxo de utilizador para que o menor ou um adulto pode recuperar o acesso à conta do menor através do envio de um código de registo para o endereço de e-mail do menor ou endereço de e-mail a adulto no registo.

4. O aplicativo oferece uma opção para o menor para revogar a autorização.

5. Quando o menor ou o adulto revoga o consentimento, o Azure AD Graph API pode ser utilizado para alterar **consentProvidedForMinor** ao **negado**. Em alternativa, o aplicativo pode optar por eliminar menor cujo consentimento foi revogado. É possível que, opcionalmente personalizar o fluxo de utilizador para que o menor autenticado (ou principal que está a utilizar conta o menor) pode revogar a autorização. Registos do Azure AD B2C **consentProvidedForMinor** como **negado**.

Para obter mais informações sobre **legalAgeGroupClassification**, **consentProvidedForMinor**, e **ageGroup**, consulte [o tipo de recurso de utilizador](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Para obter mais informações sobre os atributos personalizados, consulte [utilizar atributos personalizados para recolher informações sobre os consumidores](active-directory-b2c-reference-custom-attr.md). Quando resolver atributos expandidos, utilizando o Azure AD Graph API, tem de utilizar a versão mais longa do atributo, tal como *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00Z*.

## <a name="gather-date-of-birth-and-country-data"></a>Recolher a data de nascimento e país dados

Aplicativos podem basear-se no Azure AD B2C para reunir a data de nascimento (DOB) e informações de país de todos os utilizadores durante o registo. Se estas informações ainda não existir, o aplicativo pode solicitá-la do usuário durante a próxima jornada de autenticação (início de sessão). Os utilizadores não podem continuar sem fornecer seus DOB e informações de país. O Azure AD B2C utiliza as informações para determinar se a pessoa é considerada menor, de acordo com os padrões normativos desse país. 

Um fluxo de utilizador personalizada pode reunir DOB e informações de país e a utilização do Azure AD B2C afirmações de transformação para determinar a **ageGroup** e manter o resultado (ou manter o DOB e informações de país diretamente) no diretório.

Os passos seguintes mostram a lógica que é utilizada para calcular **ageGroup** a contar da data do usuário de nascimento:

1. Tente encontrar o país, o código de país na lista. Se o país não for encontrado, de contingência **predefinido**.

2. Se o **MinorConsent** nó está presente no elemento país:

    a. Calcule a data em que o utilizador deve ter sido born para ser considerado um adulto. Por exemplo, se a data atual for 14 de Março de 2015, e **MinorConsent** é 18, a data de nascimento tem de ser não posterior ao dia 14 de Março de 2000.

    b. Compare a data de nascimento mínima com a data de nascimento real. Se a data de nascimento mínimo é antes da data de nascimento do usuário, o cálculo devolve **pequenas** como o cálculo de grupo etário.

3. Se o **MinorNoConsentRequired** nó está presente no elemento de país, repita os passos 2a e 2b com o valor do **MinorNoConsentRequired**. Devolve o resultado de 2b **MinorNoConsentRequired** se a data de nascimento mínimo é antes da data de nascimento do usuário. 

4. Se nenhum cálculo retorna true, o cálculo devolve **adulto**.

Se um aplicativo tiver DOB fiável recolhido ou dados de país por outros métodos, a aplicação pode utilizar a Graph API para atualizar o registo de utilizador com essas informações. Por exemplo:

- Se um utilizador é conhecido por ser um adulto, atualizar o atributo de diretório **ageGroup** com um valor de **adulto**.
- Se um utilizador é conhecido por ser menor, atualizar o atributo de diretório **ageGroup** com um valor de **pequenas** e defina **consentProvidedForMinor**, conforme adequado.

Para obter mais informações sobre a recolha de dados de DOB, consulte [utilizar o controlo de idade no Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Captura de termos do contrato de utilização

Ao desenvolver seu aplicativo, normalmente captura aceitação de utilizadores dos termos de utilização nas respetivas aplicações sem qualquer ou apenas o menor, a participação de diretório do utilizador. É possível, no entanto, para utilizar um fluxo de utilizador do Azure AD B2C para reunir a aceitação de um utilizador dos termos de utilização, restringir o acesso se aceitação não for concedido e impor a aceitação das alterações futuras para os termos de utilização, com base na data da aceitação de mais recente e a data das  versão mais recente dos termos de utilização.

**Termos de utilização** também pode incluir "Consentimento para partilhar dados com terceiros". Dependendo das regulamentações locais e regras de negócio, pode recolher aceitação de um utilizador de ambas as condições combinado ou pode permitir que o utilizador aceitar uma condição e não na outra.

Os passos seguintes descrevem como pode gerir os termos de utilização:

1. Registe a aceitação dos termos de utilização e a data de aceitação com o Graph API e atributos expandidos. Pode fazê-lo através da utilização de ambos os fluxos de utilizador incorporadas e personalizadas. Recomendamos que crie e utilize o **extension_termsOfUseConsentDateTime** e **extension_termsOfUseConsentVersion** atributos.

2. Criar uma caixa de verificação necessária rotulada como "Aceitar termos de utilização" e registrar o resultado de durante a inscrição. Pode fazê-lo através da utilização de ambos os fluxos de utilizador incorporadas e personalizadas.

3. O Azure AD B2C armazena os termos do contrato de utilização e a aceitação do usuário. Pode utilizar a Graph API para consultar o status de qualquer utilizador ao ler o atributo de extensão que é utilizado para registar a resposta (por exemplo, ler **termsOfUseTestUpdateDateTime**). Pode fazê-lo através da utilização de ambos os fluxos de utilizador incorporadas e personalizadas.

4. Exigir a aceitação dos termos de utilização atualizados ao comparar a data de aceitação para a data da última versão dos termos de utilização. Pode comparar as datas utilizando apenas a um fluxo de utilizador personalizada. Usar o atributo expandido **extension_termsOfUseConsentDateTime**e comparar o valor para a afirmação de **termsOfUseTextUpdateDateTime**. Se a aceitação é antiga, força uma aceitação de novo ao exibir uma tela de declaração própria. Caso contrário, bloqueie o acesso ao utilizar a lógica da política.

5. Exigir a aceitação dos termos de utilização atualizados ao comparar o número de versão de aceitação para o número de versão aceite mais recente. É possível comparar os números de versão utilizando apenas a um fluxo de utilizador personalizada. Usar o atributo expandido **extension_termsOfUseConsentDateTime**e comparar o valor para a afirmação de **extension_termsOfUseConsentVersion**. Se a aceitação é antiga, força uma aceitação de novo ao exibir uma tela de declaração própria. Caso contrário, bloqueie o acesso ao utilizar a lógica da política.

Pode capturar os termos de utilização de aceitação nos seguintes cenários:

- Um novo utilizador é inscrever-se. Os termos de utilização são apresentados, e o resultado de aceitação é armazenado.
- Um utilizador está a iniciar sessão que anteriormente aceitou os termos de Active Directory ou mais recente de utilização. Os termos de utilização não são apresentados.
- Um utilizador está a iniciar sessão que já não aceitar os termos de Active Directory ou mais recente de utilização. Os termos de utilização são apresentados, e o resultado de aceitação é armazenado.
- Um utilizador está a iniciar sessão que já aceitou uma versão mais antiga dos termos de utilização, que são atualizadas para a versão mais recente. Os termos de utilização são apresentados, e o resultado de aceitação é armazenado.

A imagem seguinte mostra o fluxo de utilizador recomendada:

![Fluxo de utilizador de aceitação](./media/manage-user-access/user-flow.png) 

Segue-se um exemplo de DateTime com base em termos de utilização de consentimento de uma afirmação:

```
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

Segue-se um exemplo de termos de consentimento de utilização numa declaração de versão com base:

```
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

## <a name="next-steps"></a>Passos Seguintes

- Para saber como eliminar e exportar dados de utilizador, veja [gerir os dados de utilizador](manage-user-data.md).
