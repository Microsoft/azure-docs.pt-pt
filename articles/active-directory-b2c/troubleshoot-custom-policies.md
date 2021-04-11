---
title: Resolução de problemas de políticas personalizadas no Azure Ative Directory B2C
description: Saiba mais sobre abordagens para resolver erros ao trabalhar com políticas personalizadas no Azure Ative Directory B2C.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: troubleshooting
ms.date: 04/08/2021
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: bbb3bc0e34ad596c39aebb49124bb72d0b3efe6f
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/08/2021
ms.locfileid: "107103970"
---
# <a name="troubleshoot-azure-ad-b2c-custom-policies"></a>Resolução de problemas Azure AD B2C políticas personalizadas

Se utilizar [as políticas personalizadas](custom-policy-overview.md)do Azure Ative Directory B2C (Azure AD B2C), poderá experimentar desafios com o formato XML ou problemas de tempo de execução da linguagem política. Este artigo descreve algumas ferramentas e dicas que podem ajudá-lo a descobrir e resolver problemas. 

Este artigo centra-se na resolução de problemas da sua configuração de política personalizada Azure AD B2C. Não se dirige à aplicação do partido ou à sua biblioteca de identidade.

## <a name="azure-ad-b2c-correlation-id-overview"></a>Visão geral do ID da correlação Azure AD B2C

O ID de correlação Azure AD B2C é um valor identificador único que está ligado aos pedidos de autorização. Passa por todos os passos de orquestração que um utilizador é tomado. Com a iD de correlação, pode:

- Identifique a atividade de inscrição na sua aplicação e acompanhe o desempenho da sua política.
- Encontre os registos de Insights de Aplicação Azure do pedido de inscrição.
- Passe o ID de correlação para a sua API REST e use-o para identificar o fluxo de entrada. 

A identificação da correlação é alterada sempre que uma nova sessão é estabelecida. Ao depurar as suas políticas, certifique-se de que fecha os separadores de navegador existentes. Ou abrir um novo navegador de modo privado.

### <a name="get-the-azure-ad-b2c-correlation-id"></a>Obtenha o ID de correlação Azure AD B2C

Pode encontrar o ID de correlação na página de inscrição ou inscrição do Azure AD B2C. No seu navegador, selecione **ver fonte**. A correlação aparece como um comentário no topo da página.

![Screenshot da fonte de visualização da página de azure AD B2C.](./media/troubleshoot-custom-policies/find-azure-ad-b2c-correlation-id.png)

Copie o ID da correlação e, em seguida, continue o fluxo de entrada. Use a identificação da correlação para observar o comportamento de inscrição. Para obter mais informações, consulte [a resolução de problemas com insights de aplicação](#troubleshooting-with-application-insights).

### <a name="echo-the-azure-ad-b2c-correlation-id"></a>Ecoar o ID de correlação Azure AD B2C

Pode incluir o ID de correlação nas suas fichas Azure AD B2C. Para incluir o ID de correlação:

1. Abra o ficheiro de extensões da sua apólice. Por exemplo, <em>`SocialAndLocalAccounts/`**`TrustFrameworkExtensions.xml`**</em> .
1. Procure o elemento [Blocos de Construção.](buildingblocks.md) Se o elemento não existir, adicione-o.
1. Localize o elemento [ClaimsSchema.](claimsschema.md) Se o elemento não existir, adicione-o.
1. Adicione a reivindicação da cidade ao elemento **ClaimsSchema.**  

    ```xml
    <!-- 
    <BuildingBlocks>
      <ClaimsSchema> -->
        <ClaimType Id="correlationId">
          <DisplayName>correlation ID</DisplayName>
          <DataType>string</DataType>
        </ClaimType>
      <!-- 
      </ClaimsSchema>
    </BuildingBlocks>-->
    ```

1. Abra o ficheiro do partido da sua política. Por exemplo, <em>`SocialAndLocalAccounts/`**`SignUpOrSignIn.xml`**</em> arquivar. A reclamação de saída será adicionada ao token após uma viagem bem sucedida do utilizador e enviada para a aplicação. Modifique o elemento de perfil técnico na secção do partido de suporte para adicionar a cidade como uma reivindicação de saída.
 
    ```xml
    <RelyingParty>
      <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
      <TechnicalProfile Id="PolicyProfile">
        <DisplayName>PolicyProfile</DisplayName>
        <Protocol Name="OpenIdConnect" />
        <OutputClaims>
          ...
          <OutputClaim ClaimTypeReferenceId="correlationId" DefaultValue="{Context:CorrelationId}" />
        </OutputClaims>
        <SubjectNamingInfo ClaimType="sub" />
      </TechnicalProfile>
    </RelyingParty>
    ```


## <a name="troubleshooting-with-application-insights"></a>Resolução de problemas com insights de aplicações

Para diagnosticar problemas com as suas políticas personalizadas, utilize [insights de aplicação](troubleshoot-with-application-insights.md). O Application Insights traça a atividade da sua viagem de utilizador de política personalizada. Fornece uma forma de diagnosticar exceções e observar a troca de reclamações entre a Azure AD B2C e os vários fornecedores de sinistros que são definidos por perfis técnicos, tais como fornecedores de identidade, serviços baseados em API, o diretório de utilizadores Azure AD B2C, e outros serviços.  

Recomendamos a instalação da [extensão Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) para [o Código VS](https://code.visualstudio.com/). Com a extensão Azure AD B2C, os registos são organizados para si pelo nome da política, o ID de correlação (Application Insights apresenta o primeiro dígito do ID de correlação) e o registo de tempo. Esta funcionalidade ajuda-o a encontrar o registo relevante com base no timetamp local e a ver a viagem do utilizador executada por Azure AD B2C. 

> [!NOTE]
> - Há um curto atraso, normalmente menos de cinco minutos, antes de poder ver novos registos em Application Insights.
> - A comunidade desenvolveu a extensão do Código do Estúdio Visual para o Azure AD B2C para ajudar os desenvolvedores de identidade. A extensão não é suportada pela Microsoft e é disponibilizada estritamente como está.

Um único fluxo de entrada pode emitir mais de um traço de Azure Application Insights. Na imagem seguinte, a política *de B2C_1A_signup_signin* tem três registos. Cada registo representa parte do fluxo de entrada.

A imagem que se segue mostra a extensão Azure AD B2C para o Código VS com o explorador de traços Azure Application Insights.

![Screenshot da extensão AZURE AD B2C para código VS com traço de Insights de Aplicação Azure.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-trace.png)

### <a name="filter-the-trace-log"></a>Filtrar o registo de vestígios

Com o foco no explorador de vestígios Azure AD B2C, comece a digitar o primeiro dígito do ID de correlação, ou um tempo que pretende encontrar. Você verá uma caixa de filtro no topo-direito do explorador de vestígios Azure AD B2C mostrando o que você escreveu até agora, e os registos de vestígios correspondentes serão realçados.  

![Screenshot da extensão AZure AD B2C Azure AD B2C trace explorer filter realçando.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-highlight.png)

Pairar sobre a caixa do filtro e selecionar **O Filtro de Ativação no Tipo** mostrará apenas registos de vestígios correspondentes. Utilize o **botão 'X' Limpar** para limpar o filtro.

![Screenshot da extensão AZURE AD B2C Filtro de explorador de vestígios Azure AD B2C.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-filter.png)

### <a name="application-insights-trace-log-details"></a>Insights de Aplicação rastreia detalhes do registo de registo

Quando seleciona um traço de Insights de Aplicação Azure, a extensão abre a janela **de detalhes do Application Insights** com as seguintes informações:

- **Application Insights** - Informações genéricas sobre o registo de vestígios, incluindo o nome da política, iD de correlação, ID de aplicação Azure e marcação de tempo de rastreada.
- **Perfis técnicos** - Lista de perfis técnicos que aparecem no registo de vestígios.
- **Reclamações** - Lista alfabética de reclamações que aparecem no registo de vestígios e seus valores. Se uma reclamação aparecer no registo de vestígios várias vezes com valores `=>` diferentes, um sinal designa o valor mais novo. Pode rever estas reclamações para determinar se os valores de reclamação esperados estão definidos corretamente. Por exemplo, se tiver uma condição prévia que verifique um valor de reclamação, a secção de reclamações pode ajudá-lo a determinar por que um fluxo esperado se comporta de forma diferente.
- **Transformação de reclamações** - Lista de transformações de sinistros que aparecem no registo de vestígios. Cada transformação de sinistros contém as alegações de entrada, parâmetros de entrada e reivindicações de saída. A secção de transformação de sinistros dá uma visão dos dados enviados e do resultado da transformação de sinistros.
- **Tokens** - Lista de fichas que aparecem no registo de vestígios. Os tokens incluem o OAuth federado subjacente e os tokens do fornecedor de identidade OpenId Connect. O token do fornecedor de identidade federado dá detalhes sobre como o fornecedor de identidade devolve as reclamações ao Azure AD B2C para que possa mapear as alegações de saída de perfil técnico do fornecedor de identidade. 
- **Exceções** - Lista de exceções ou erros fatais que aparecem no registo de vestígios.
- **Insights de Aplicação JSON** - Os dados brutos são os retornos dos Insights de Aplicação.

A imagem que se segue mostra um exemplo da janela de detalhes do registo de registo de rastreios de rastreio de aplicações.  

![Screenshot do relatório de rastreio azure AD B2C extensão Azure AD B2C.](./media/troubleshoot-custom-policies/vscode-extension-application-insights-report.png)

## <a name="troubleshoot-jwt-tokens"></a>Fichas JWT de resolução de problemas

Para fins de validação e depuração de símbolos JWT, pode descodificar JWTs usando um site como [https://jwt.ms](https://jwt.ms) . Crie uma aplicação de teste que possa redirecionar `https://jwt.ms` para inspeção simbólica. Se ainda não o fez, [registe uma aplicação web](tutorial-register-applications.md)e [adifiquei a concessão implícita de ID](tutorial-register-applications.md#enable-id-token-implicit-grant). 

![Screenshot da pré-visualização do símbolo JWT.](./media/troubleshoot-custom-policies/jwt-token-preview.png)

Use **Executar agora** e testar as suas políticas `https://jwt.ms` independentemente da sua aplicação web ou móvel. Este site funciona como uma aplicação de partidos dependentes. Exibe o conteúdo do token web JSON (JWT) que é gerado pela sua política Azure AD B2C.

## <a name="troubleshoot-saml-protocol"></a>Protocolo SAML de resolução de problemas

Para ajudar a configurar e depurar a integração com o seu fornecedor de serviços, pode utilizar uma extensão de navegador para o protocolo SAML, por exemplo, [extensão SAML DevTools](https://chrome.google.com/webstore/detail/saml-devtools-extension/jndllhgbinhiiddokbeoeepbppdnhhio) para ferramentas Chrome, [SAML-tracer](https://addons.mozilla.org/es/firefox/addon/saml-tracer/) para FireFox ou [Edge ou IE Developer](https://techcommunity.microsoft.com/t5/microsoft-sharepoint-blog/gathering-a-saml-token-using-edge-or-ie-developer-tools/ba-p/320957).

A imagem que se segue demonstra como a extensão SAML DevTools apresenta o pedido SAML Azure AD B2C envia ao fornecedor de identidade e a resposta SAML.

![Screenshot do registo de vestígios do protocolo SAML.](./media/troubleshoot-custom-policies/saml-protocol-trace.png)

Utilizando estas ferramentas, pode verificar a integração entre a sua aplicação e o Azure AD B2C. Por exemplo:

- Verifique se o pedido DE SAML contém uma assinatura e determine que algoritmo é usado para assinar o pedido de autorização.
- Verifique se o Azure AD B2C devolve uma mensagem de erro.
- Verifique se a secção de afirmação está encriptada.
- Obtenha o nome das reclamações devolva o fornecedor de identidade.

Também pode rastrear a troca de mensagens entre o seu navegador cliente e o Azure AD B2C, com [o Fiddler.](https://www.telerik.com/fiddler) Pode ajudá-lo a obter uma indicação de onde a sua viagem de utilizador está a falhar nos seus passos de orquestração.

## <a name="troubleshoot-policy-validity"></a>Validade da política de resolução de problemas

Depois de terminar de desenvolver a sua política, faça o upload da apólice para Azure AD B2C. pode haver alguns problemas com a sua política. Utilize os seguintes métodos para garantir a sua integridade/validade da sua política.

O erro mais comum na configuração de políticas personalizadas é o XML formatado de formação inadequada. Um bom editor de XML é quase essencial. Exibe XML de forma nativa, o conteúdo de códigos de cores, pré-preenche termos comuns, mantém os elementos XML indexados e pode validar contra um esquema XML.

Recomendamos a utilização [do Código do Estúdio Visual.](https://code.visualstudio.com/) Em seguida, instale uma extensão XML, como [suporte linguístico XML por chapéu vermelho](https://marketplace.visualstudio.com/items?itemName=redhat.vscode-xml). A extensão XML permite validar o esquema XML antes de carregar o seu ficheiro XML, utilizando a definição de esquema [XSD](https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd) de política personalizada.

Pode utilizar a estratégia de associação de ficheiros XML para ligar o ficheiro XML ao XSD adicionando as seguintes definições no seu ficheiro VS `settings.json` Code. Para tal:

1. A partir do Código VS, clique nas **Definições**. Para obter mais informações, consulte [as Definições do Utilizador e do Espaço de Trabalho](https://code.visualstudio.com/docs/getstarted/settings).
1. Procure **por ficheirosAssociations**, em seguida, sob a **Extensão**, selecione o **XML**.
1. **Selecione Editar em settings.jsem**.

    ![Screenshot da validação do esquema do código VS XML.](./media/troubleshoot-custom-policies/xml-validation.png)
1. No settings.jsligado, adicione o seguinte código JSON:

    ```json
    "xml.fileAssociations": [
      {
        "pattern": "**.xml",
        "systemId": "https://raw.githubusercontent.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/master/TrustFrameworkPolicy_0.3.0.0.xsd"
      }
    ]
    ```

O exemplo a seguir mostra um erro de validação de XML. Quando deslocar o rato sobre o nome do elemento, a extensão lista os elementos esperados.

![Screenshot do indicador de erro de validação de esquemas VS Code XML.](./media/troubleshoot-custom-policies/xml-validation-error.png)

No seguinte caso, o `DisplayName` elemento está correto. Mas, na ordem errada. Deve  `DisplayName` ser antes do `Protocol` elemento. Para corrigir o problema, mova o rato sobre o `DisplayName` elemento para a ordem correta dos elementos.

![Screenshot do erro de validação de esquemas VS Code XML.](./media/troubleshoot-custom-policies/xml-validation-order-error.png)

## <a name="upload-policies-and-policy-validation"></a>Fazer upload de políticas e validação de políticas

A validação do ficheiro de política XML é executada automaticamente no upload. A maioria dos erros faz com que o upload falhe. A validação inclui o ficheiro de política que está a enviar. Também inclui a cadeia de ficheiros a que o ficheiro de upload se refere (o ficheiro de política do partido, o ficheiro de extensões e o ficheiro base).

> [!TIP]
> Azure AD B2C executa validação adicional para a política do partido. Quando se tem um problema com a sua política, mesmo que edite apenas a política de extensão, é uma boa prática carregar também a política do partido. 

Esta secção contém os erros de validação comuns e as soluções prováveis.

### <a name="schema-validation-error-found-has-invalid-child-element-name"></a>Erro de validação de esquema encontrado ... tem elemento infantil inválido '{name}'

A sua política contém um elemento XML inválido, ou o elemento XML é válido, mas parece estar na ordem errada. Para corrigir este tipo de erro, consulte a secção de validade da [política de resolução de problemas.](#troubleshoot-policy-validity)

### <a name="there-is-a-duplicate-key-sequence-number"></a>Existe uma sequência de chave duplicada '{number}' 

Uma [viagem](userjourneys.md) de utilizador ou [sub-viagem](subjourneys.md) consiste numa lista ordenada de etapas de orquestração executadas em sequência. Depois de mudar a sua jornada, renumerar os passos sequencialmente sem saltar nenhum número inteiro de 1 a N.

> [!TIP]
> Pode utilizar a [extensão Azure AD B2C](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) para o comando [VS Code](https://code.visualstudio.com/) `(Shift+Ctrl+r)` para renumerar todas as viagens de utilizador e subtrações de etapas de orquestração na sua política.

### <a name="was-expected-to-have-step-with-order-number-but-it-was-not-found"></a>... Esperava-se que tivesse um passo com a ordem "{number}" mas não foi encontrado...

Verifique o erro anterior.

### <a name="orchestration-step-order-number-in-user-journey-name-is-followed-by-a-claims-provider-selection-step-and-must-be-a-claims-exchange-but-it-is-of-type"></a>Ordem de passo de orquestração "{number}" na jornada do utilizador "{name}" ... é seguido por um passo de seleção do fornecedor de sinistros e deve ser uma troca de reclamações, mas é do tipo...

Os passos de orquestração tipo de `ClaimsProviderSelection` , e contêm uma lista de `CombinedSignInAndSignUp` opções que um utilizador pode escolher. Deve seguir por tipo de troca de `ClaimsExchange` reclamações.

Os seguintes passos de orquestração causam este tipo ou erro. O segundo passo de orquestração deve ser tipo `ClaimsExchange` de, não `ClaimsProviderSelection` .

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      <OrchestrationStep Order="1" Type="CombinedSignInAndSignUp" ContentDefinitionReferenceId="api.signuporsignin">
        <ClaimsProviderSelections>
          <ClaimsProviderSelection TargetClaimsExchangeId="FacebookExchange"/>
          <ClaimsProviderSelection ValidationClaimsExchangeId="LocalAccountSigninEmailExchange"/>
        </ClaimsProviderSelections>
        <ClaimsExchanges>
          <ClaimsExchange Id="LocalAccountSigninEmailExchange" TechnicalProfileReferenceId="SelfAsserted-LocalAccountSignin-Email"/>
        </ClaimsExchanges>
      </OrchestrationStep> 
      
      <OrchestrationStep Order="2" Type="ClaimsProviderSelection">
        ...
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="step-number-with-2-claims-exchanges-it-must-be-preceded-by-a-claims-provider-selection-in-order-to-determine-which-claims-exchange-can-be-used"></a>... passo {número} com 2 trocas de reclamações. Deve ser precedido por uma seleção do fornecedor de sinistros, a fim de determinar qual a troca de sinistros

Um tipo de passo de orquestração `ClaimsExchange` deve ter um único , a menos que o passo anterior seja tipo de , ou `ClaimsExchange` `ClaimsProviderSelection` `CombinedSignInAndSignUp` . Os seguintes passos de orquestração causam este tipo de erro. O sexto passo contém duas trocas de reclamações. 

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Para corrigir este tipo de erro, utilize dois passos de orquestração. Cada passo de orquestração com uma troca de reivindicações.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="5" Type="ClaimsExchange">
        ...
        <ClaimsExchanges>
          <ClaimsExchange Id="SelfAsserted-Social" TechnicalProfileReferenceId="SelfAsserted-Social"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="6" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-First-API" TechnicalProfileReferenceId="Call-REST-First-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-Second-API" TechnicalProfileReferenceId="Call-REST-Second-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="there-is-a-duplicate-key-sequence-name"></a>Há uma sequência de chave duplicada '{name}'

Uma viagem tem múltiplos `ClaimsExchange` com o `Id` mesmo. Os seguintes passos causam este tipo de erro. O ID *AADUserWrite* aparece duas vezes na viagem do utilizador.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

Para corrigir este tipo de erro, altere a troca de reivindicações dos oitavos de final de orquestração para um nome único, como *Call-REST-API*.

```xml
<!-- 
<UserJourneys>
  <UserJourney Id="SignUpOrSignIn">
    <OrchestrationSteps>-->
      ...
      <OrchestrationStep Order="7" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="AADUserWrite" TechnicalProfileReferenceId="AAD-UserWriteUsingAlternativeSecurityId"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      <OrchestrationStep Order="8" Type="ClaimsExchange">
        <ClaimsExchanges>
          <ClaimsExchange Id="Call-REST-API" TechnicalProfileReferenceId="Call-REST-API"/>
        </ClaimsExchanges>
      </OrchestrationStep>
      ...
    <!--
    </OrchestrationSteps>
  </UserJourney>
</UserJourneys> -->
```

### <a name="makes-a-reference-to-claimtype-with-id-claim-name-but-neither-the-policy-nor-any-of-its-base-policies-contain-such-an-element"></a>... faz uma referência ao ClaimType com id "{claim name}" mas nem a política nem nenhuma das suas políticas de base contêm tal elemento

Este tipo de erro acontece quando a sua apólice faz referência a uma reclamação que não é declarada no [esquema de reclamações](claimsschema.md). As reclamações devem ser definidas em pelo menos um dos ficheiros da apólice. 

Por exemplo, um perfil técnico com a reivindicação de saída *schoolId.* Mas a produção afirma que *a escola* nunca é declarada na política, ou numa política ancestral.

```xml
<OutputClaims>
  <OutputClaim ClaimTypeReferenceId="schoolId" />
  ...
</OutputClaims>
```

Para corrigir este tipo de erro, verifique se o `ClaimTypeReferenceId` valor está mal escrito ou se não existe no esquema. Se a alegação for definida na política de extensões, mas também está a ser usada na política de base. Certifique-se de que a reclamação é definida na política em que está usada, ou numa política de nível superior.

Adicionar a reclamação ao esquema de reclamações resolve este tipo de erro.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="schoolId">
      <DisplayName>School name</DisplayName>
      <DataType>string</DataType>
      <UserHelpText>Enter your school name</UserHelpText>
      <UserInputType>TextBox</UserInputType>
    </ClaimType>
  <!-- 
  </ClaimsSchema>
</BuildingBlocks> -->
```

### <a name="makes-a-reference-to-a-claimstransformation-with-id"></a>... faz referência a uma ReclamaçãoTransformação com ID...

A causa deste erro é semelhante à do erro de reclamação. Verifique o erro anterior.

### <a name="user-is-currently-logged-as-a-user-of-yourtenantonmicrosoftcom-tenant"></a>O utilizador está atualmente registado como um utilizador do inquilino 'yourtenant.onmicrosoft.com'...

Você inicia sessão com uma conta de um inquilino que é diferente da política que tenta carregar. Por exemplo, faça o seu s-in admin@contoso.onmicrosoft.com com , enquanto a sua política `TenantId` está definida para `fabrikam.onmicrosoft.com` .

```xml
<TrustFrameworkPolicy ...
  TenantId="fabrikam.onmicrosoft.com"
  PolicyId="B2C_1A_signup_signin"
  PublicPolicyUri="http://fabrikam.onmicrosoft.com/B2C_1A_signup_signin">

  <BasePolicy>
    <TenantId>fabrikam.onmicrosoft.com</TenantId>
    <PolicyId>B2C_1A_TrustFrameworkExtensions</PolicyId>
  </BasePolicy>
  ...
</TrustFrameworkPolicy>
```

- Verifique se o `TenantId` valor `<TrustFrameworkPolicy\>` do e `<BasePolicy\>` elementos correspondem ao seu inquilino Azure AD B2C.

### <a name="claim-type-name-is-the-output-claim-of-the-relying-partys-technical-profile-but-it-is-not-an-output-claim-in-any-of-the-steps-of-user-journey"></a>O tipo de reclamação "{name}" é a reivindicação de saída do perfil técnico da parte que conta, mas não é uma reivindicação de saída em nenhum dos passos da viagem do utilizador...

Numa política partidária em conta, adicionou uma reivindicação de saída, mas a reivindicação de saída não é uma reivindicação de saída em nenhuma das etapas da viagem do utilizador. A Azure AD B2C não consegue ler o valor da reclamação do saco de reclamações.

No exemplo seguinte, a alegação *schoolId* é uma reivindicação de saída do perfil técnico da parte contando, mas não é uma reivindicação de saída em nenhum dos passos da viagem de utilizador do *SignUpOrSignIn.*

```xml
<RelyingParty>
  <DefaultUserJourney ReferenceId="SignUpOrSignIn" />
  <TechnicalProfile Id="PolicyProfile">
    <DisplayName>PolicyProfile</DisplayName>
    <Protocol Name="OpenIdConnect" />
    <OutputClaims>
      <OutputClaim ClaimTypeReferenceId="schoolId" />
      ...
    </OutputClaims>
    <SubjectNamingInfo ClaimType="sub" />
  </TechnicalProfile>
</RelyingParty>
``` 

Para corrigir este tipo de erro, certifique-se de que as reclamações de saída aparecem em pelo menos uma coleção de pedidos de pedidos de resultados de perfil de orquestração. Se a sua viagem de utilizador não puder desausar a reclamação, no perfil técnico da parte, desafine um valor padrão, como a corda vazia.  

```xml
<OutputClaim ClaimTypeReferenceId="schoolId" DefaultValue="" />
```

### <a name="input-string-was-not-in-a-correct-format"></a>A cadeia de entrada não estava num formato correto

Define o tipo de valor incorreto para uma reclamação de outro tipo. Por exemplo, define-se uma reivindicação de inteiros.

```xml
<!--
<BuildingBlocks>
  <ClaimsSchema> -->
    <ClaimType Id="age">
      <DisplayName>Age</DisplayName>
      <DataType>int</DataType>
    </ClaimType>
  <!--
  </ClaimsSchema>
</BuildingBlocks> -->
```

Depois tenta-se definir um valor de corda:

```xml
<OutputClaim ClaimTypeReferenceId="age" DefaultValue="ABCD" />
```

Para corrigir este tipo de erro, certifique-se de que define o valor correto, tal como `DefaultValue="0"` .


### <a name="tenant-name-already-has-a-policy-with-id-name-another-policy-with-the-same-id-cannot-be-stored"></a>O inquilino "{name}" já tem uma política com id "{name}". Outra política com o mesmo id não pode ser armazenada

Tenta enviar uma apólice para o seu inquilino, mas uma apólice com o mesmo nome já está enviada para o seu inquilino. 

Para corrigir este tipo de erro, ao carregar a apólice, selecione a **política de substituição da política personalizada se já existir** caixa de verificação.

![Screenshot que demonstra como substituir a política personalizada se já existe.](./media/troubleshoot-custom-policies/overwrite-custom-policy-if-exists.png)


## <a name="next-steps"></a>Passos seguintes

- Saiba como [recolher os registos B2C do Azure Ative Directory com Insights de Aplicação](troubleshoot-with-application-insights.md).

