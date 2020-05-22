---
title: Gerir o acesso ao utilizador no Diretório Ativo Azure B2C [ Microsoft Docs
description: Saiba identificar menores, recolher dados data de nascimento e país/região e obter a aceitação dos termos de utilização na sua aplicação utilizando o Azure AD B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 07/24/2018
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ad681f4996f713b8bb0c85b07a3f38f0dcb6708a
ms.sourcegitcommit: 493b27fbfd7917c3823a1e4c313d07331d1b732f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83738239"
---
# <a name="manage-user-access-in-azure-active-directory-b2c"></a>Gerir o acesso ao utilizador no Diretório Ativo Azure B2C

Este artigo discute como gerir o acesso dos utilizadores às suas aplicações utilizando o Azure Ative Directory B2C (Azure AD B2C). A gestão de acesso na sua aplicação inclui:

- Identificar menores e controlar o acesso do utilizador à sua aplicação.
- Exigindo o consentimento dos pais para que os menores utilizem as suas aplicações.
- Recolha de dados de nascimento e país/região dos utilizadores.
- Capturar um acordo de termos de uso e acesso a marcha.

[!INCLUDE [active-directory-b2c-advanced-audience-warning](../../includes/active-directory-b2c-advanced-audience-warning.md)]

## <a name="control-minor-access"></a>Controlar o acesso menor

As aplicações e organizações podem decidir impedir os menores de utilizarem aplicações e serviços que não sejam direcionados para este público. Em alternativa, as candidaturas e organizações podem decidir aceitar menores e, posteriormente, gerir o consentimento dos pais e proporcionar experiências admissíveis para menores, conforme ditaas pelas regras empresariais e permitidas pela regulamentação.

Se um utilizador for identificado como menor, pode definir o fluxo de utilizador em Azure AD B2C para uma de três opções:

- **Envie um JWT assinado id_token de volta à aplicação**: O utilizador está registado no diretório e um sinal é devolvido à aplicação. O pedido prossegue então aplicando regras comerciais. Por exemplo, o pedido pode prosseguir com um processo de consentimento parental. Para utilizar este método, opte por receber o **grupo** etário e **consentEProvidedForMinor** reclamações da aplicação.

- **Envie um token JSON não assinado para a aplicação**: Azure AD B2C notifica a aplicação de que o utilizador é menor e fornece o estado do consentimento parental do utilizador. O pedido prossegue então aplicando regras comerciais. Um token JSON não completa uma autenticação bem sucedida com a aplicação. A aplicação deve processar o utilizador não autenticado de acordo com as reclamações incluídas no símbolo JSON, que podem incluir **nome**, **e-mail,** **ageGroup,** e **consentProvidedForMinor**.

- **Bloqueie o utilizador**: Se um utilizador for menor e o consentimento dos pais não tiver sido fornecido, o Azure AD B2C pode notificar o utilizador de que está bloqueado. Não é emitido nenhum símbolo, o acesso é bloqueado e a conta de utilizador não é criada durante uma viagem de registo. Para implementar esta notificação, fornece uma página de conteúdo HTML/CSS adequada para informar o utilizador e apresentar opções apropriadas. Não são necessárias mais medidas pelo pedido de novos registos.

## <a name="get-parental-consent"></a>Obtenha o consentimento dos pais

Dependendo da regulação da aplicação, o consentimento dos pais pode ter de ser concedido por um utilizador que seja verificado como adulto. O Azure AD B2C não fornece uma experiência para verificar a idade de um indivíduo e, em seguida, permitir que um adulto verificado conceda o consentimento dos pais a um menor. Esta experiência deve ser fornecida pela aplicação ou por outro prestador de serviços.

Segue-se um exemplo de fluxo de utilizador para recolher o consentimento dos pais:

1. Uma operação [DaPi](https://docs.microsoft.com/graph/use-the-api) do Microsoft Graph identifica o utilizador como menor e devolve os dados do utilizador à aplicação sob a forma de um símbolo JSON não assinado.

2. A aplicação processa o símbolo JSON e mostra um ecrã ao menor, notificando-os de que o consentimento dos pais é necessário e solicitando o consentimento de um progenitor online.

3. O Azure AD B2C mostra uma viagem de entrada em que o utilizador pode iniciar sessão normalmente e emite um símbolo da aplicação que está definida para incluir **legalAgeGroupClassification = "minorWithParentConsent".** A aplicação recolhe o endereço de e-mail do progenitor e verifica que o progenitor é um adulto. Para tal, utiliza uma fonte de confiança, como um escritório nacional de identificação, verificação de licença ou prova de cartão de crédito. Se a verificação for bem sucedida, a aplicação solicita ao menor que assine o fluxo do utilizador Azure AD B2C. Se o consentimento for negado (por exemplo, se **legalAgeGroupClassification = "minorWithoutParentConsent"**), Azure AD B2C devolve um símbolo JSON (não um login) ao pedido para reiniciar o processo de consentimento. É opcionalmente possível personalizar o fluxo do utilizador para que um menor ou um adulto possa recuperar o acesso à conta de um menor enviando um código de registo para o endereço de e-mail do menor ou o endereço de e-mail do adulto registado.

4. O pedido oferece uma opção ao menor para revogar o consentimento.

5. Quando o menor ou o adulto revogam o consentimento, a Microsoft Graph API pode ser usada para alterar o **consentimentoProvidedForMinor** para **negado**. Em alternativa, o pedido pode optar por eliminar um menor cujo consentimento tenha sido revogado. É opcionalmente possível personalizar o fluxo do utilizador para que o menor autenticado (ou pai que está a usar a conta do menor) possa revogar o consentimento. Os registos azure AD B2C **consentProvidedForMinor** foram **negados.**

Para obter mais informações sobre **a legalAgeGroupClassification**, **consentProvidedForMinor**, e **ageGroup**, consulte o tipo de [recurso do utilizador](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/user). Para obter mais informações sobre atributos personalizados, consulte [Use atributos personalizados para recolher informações sobre os seus consumidores.](user-flow-custom-attributes.md) Quando aborda atributos estendidos utilizando a API do Microsoft Graph, deve utilizar a versão longa do atributo, como *extension_18b70cf9bb834edd8f38521c2583cd86_dateOfBirth*: *2011-01-01T00:00:00:00Z*.

## <a name="gather-date-of-birth-and-countryregion-data"></a>Recolher data de nascimento e dados de país/região

As aplicações podem contar com o Azure AD B2C para recolher a data de nascimento (DOB) e informações país/região de todos os utilizadores durante o registo. Se estas informações ainda não existirem, a aplicação pode solicitá-la ao utilizador durante a próxima viagem de autenticação (iniciar o sessão). Os utilizadores não podem proceder sem fornecer as suas informações DOB e país/região. O Azure AD B2C utiliza as informações para determinar se o indivíduo é considerado menor de acordo com as normas regulamentares desse país/região.

Um fluxo de utilizador personalizado pode recolher informações do DOB e país/região e utilizar a transformação de reivindicações do Azure AD B2C para determinar a **idade Group** e persistir o resultado (ou persistir diretamente a informação do DOB e país/região) no diretório.

Os seguintes passos mostram a lógica que é usada para calcular o **ageGroup** a partir da data de nascimento do utilizador:

1. Tente encontrar o país/região pelo código país/região na lista. Se o país/região não for encontrado, recue para **o Incumprimento**.

2. Se o nó **MinorConsent** estiver presente no elemento país/região:

    a. Calcular a data em que o utilizador deve ter nascido para ser considerado adulto. Por exemplo, se a data atual for 14 de março de 2015, e **minorConsent** for 18, a data de nascimento deve ser o mais tardar em 14 de março de 2000.

    b. Compare a data mínima de nascimento com a data de nascimento real. Se a data mínima de nascimento for antes da data de nascimento do utilizador, o cálculo devolve **Menor** como cálculo da faixa etária.

3. Se o nó **MinorNoConsentRequired** estiver presente no elemento país/região, repita os passos 2a e 2b utilizando o valor de **MinorNoConsentRequired**. A saída de 2b devolve **MinorNoConsentRequired** se a data mínima de nascimento for antes da data de nascimento do utilizador.

4. Se nenhum dos cálculos for verdadeiro, o cálculo devolve **Adulto**.

Se uma aplicação tiver recolhido dados DOB ou país/região de forma fiável por outros métodos, a aplicação poderá utilizar a API graphia para atualizar o registo do utilizador com esta informação. Por exemplo:

- Se um utilizador for conhecido por ser adulto, atualize o diretório atribui o **ageGroup** com um valor de **Adulto**.
- Se um utilizador for conhecido por ser menor, atualize o diretório atribui o **ageGroup** com um valor de **Minor** e detetete o **consentimento ProvidedForMinor,** conforme apropriado.

Para obter mais informações sobre a recolha de dados DOB, consulte [A marcha da idade em Azure AD B2C](basic-age-gating.md).

## <a name="capture-terms-of-use-agreement"></a>Capturar termos de acordo de utilização

Ao desenvolver a sua aplicação, normalmente captura a aceitação dos termos de utilização dos utilizadores dentro das suas aplicações sem, ou apenas menor, participação do diretório do utilizador. No entanto, é possível utilizar um fluxo de utilizador Azure AD B2C para recolher a aceitação de termos de utilização por parte de um utilizador, restringir o acesso se não for concedida aceitação e impor a aceitação de futuras alterações aos termos de utilização, com base na data da mais recente aceitação e na data da versão mais recente dos termos de utilização.

**Os termos de utilização** também podem incluir "Consentimento para partilhar dados com terceiros". Dependendo dos regulamentos locais e das regras comerciais, pode reunir a aceitação de um utilizador de ambas as condições combinadas, ou pode permitir que o utilizador aceite uma condição e não a outra.

Os seguintes passos descrevem como pode gerir os termos de utilização:

1. Registem a aceitação dos termos de utilização e a data de aceitação utilizando a API do Gráfico e atributos alargados. Pode fazê-lo utilizando fluxos de utilizadores incorporados e personalizados. Recomendamos que crie e utilize os **atributos extension_termsOfUseConsentDateTime** e **extension_termsOfUseConsentVersion.**

2. Crie uma caixa de verificação necessária com a etiqueta "Aceitar termos de utilização", e grave o resultado durante a inscrição. Pode fazê-lo utilizando fluxos de utilizadores incorporados e personalizados.

3. O Azure AD B2C armazena os termos do acordo de utilização e a aceitação do utilizador. Pode utilizar a API do Gráfico para consultar o estado de qualquer utilizador, lendo o atributo de extensão que é usado para registar a resposta (por exemplo, ler **termosOfUseTestUpdateDateTime**). Pode fazê-lo utilizando fluxos de utilizadores incorporados e personalizados.

4. Exigir a aceitação de termos de utilização atualizados comparando a data de aceitação com a data da versão mais recente dos termos de utilização. Só é possível comparar as datas utilizando um fluxo de utilizador personalizado. Utilize o atributo alargado **extension_termsOfUseConsentDateTime**e compare o valor com a reclamação de **termosOfUseTextUpdateDateTime**. Se a aceitação for antiga, force uma nova aceitação exibindo um ecrã autoafirmado. Caso contrário, bloqueie o acesso utilizando a lógica da política.

5. Exigir a aceitação de termos de utilização atualizados comparando o número de versão da aceitação com o número de versão mais recente aceite. Só é possível comparar números de versão utilizando um fluxo de utilizador personalizado. Utilize o **atributo**alargado extension_termsOfUseConsentDateTime e compare o valor com o pedido de **extension_termsOfUseConsentVersion**. Se a aceitação for antiga, force uma nova aceitação exibindo um ecrã autoafirmado. Caso contrário, bloqueie o acesso utilizando a lógica da política.

Pode capturar termos de aceitação de uso nos seguintes cenários:

- Um novo utilizador está a inscrever-se. Os termos de utilização são apresentados e o resultado de aceitação é armazenado.
- Um utilizador está a iniciar sessão que tenha aceitado previamente os termos de utilização mais recentes ou ativos. Os termos de utilização não são apresentados.
- Um utilizador está a iniciar sessão que ainda não aceitou os termos de utilização mais recentes ou ativos. Os termos de utilização são apresentados e o resultado de aceitação é armazenado.
- Um utilizador está a iniciar sessão que já aceitou uma versão mais antiga dos termos de utilização, que agora são atualizados para a versão mais recente. Os termos de utilização são apresentados e o resultado de aceitação é armazenado.

A imagem seguinte mostra o fluxo recomendado do utilizador:

![Diagrama de gráfico de fluxo mostrando o fluxo recomendado do utilizador de aceitação](./media/manage-user-access/user-flow.png)

Segue-se um exemplo de um consentimento de utilização baseado em DataTime numa reclamação:

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

Segue-se um exemplo de um consentimento de utilização baseado em versão numa reclamação:

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

- Para aprender a eliminar e exportar dados dos utilizadores, consulte [Gerir os dados dos utilizadores](manage-user-data.md).
- Para uma política personalizada que implementa um pedido de utilização, consulte [Uma Política Personalizada B2C IEF - Inscreva-se e inscreva-se com aviso de 'Termos de utilização'.](https://github.com/azure-ad-b2c/samples/tree/master/policies/sign-in-sign-up-versioned-tou)
