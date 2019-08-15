---
title: Personalização de idioma no Azure Active Directory B2C
description: Saiba mais sobre como personalizar a experiência de idioma em seus fluxos de usuário.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: bced7a4b994172a1a2076149d6f25adb39c99b54
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/14/2019
ms.locfileid: "69015570"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização de idioma no Azure Active Directory B2C

A personalização da linguagem no Azure Active Directory B2C (Azure AD B2C) permite que o fluxo do usuário acomode diferentes idiomas para atender às necessidades do cliente. A Microsoft fornece as traduções para [36 idiomas](#supported-languages), mas você também pode fornecer suas próprias traduções para qualquer idioma. Mesmo que sua experiência seja fornecida apenas para um único idioma, você pode personalizar qualquer texto nas páginas.

## <a name="how-language-customization-works"></a>Como funciona a personalização de idioma

Use a personalização de idioma para selecionar em quais idiomas o seu fluxo de usuário está disponível. Depois que o recurso estiver habilitado, você poderá fornecer o parâmetro de cadeia `ui_locales`de caracteres de consulta,, do seu aplicativo. Quando você chama o Azure AD B2C, sua página é traduzida para a localidade que você indicou. Esse tipo de configuração lhe dá controle total sobre os idiomas no seu fluxo de usuário e ignora as configurações de idioma do navegador do cliente.

Talvez você não precise desse nível de controle sobre quais idiomas seu cliente vê. Se você não fornecer um `ui_locales` parâmetro, a experiência do cliente será determinada pelas configurações do navegador. Você ainda pode controlar em quais idiomas seu fluxo de usuário está traduzido adicionando-o como um idioma com suporte. Se o navegador de um cliente estiver definido para mostrar um idioma para o qual você não deseja dar suporte, o idioma que você selecionou como padrão nas culturas com suporte será mostrado em vez disso.

* **idioma especificado da interface do usuário**: Depois de habilitar a personalização de idioma, o fluxo do usuário é convertido no idioma especificado aqui.
* **Idioma solicitado pelo navegador**: Se nenhum `ui_locales` parâmetro tiver sido especificado, seu fluxo de usuário será convertido para o idioma solicitado pelo navegador, *se houver suporte para o idioma*.
* **Idioma padrão da política**: Se o navegador não especificar um idioma ou especificar um que não tenha suporte, o fluxo do usuário será convertido para o idioma padrão do fluxo de usuário.

> [!NOTE]
> Se você estiver usando atributos de usuário personalizados, precisará fornecer suas próprias traduções. Para obter mais informações, consulte [personalizar suas cadeias de caracteres](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Suporte a idiomas solicitados para ui_locales

As políticas que foram criadas antes da disponibilidade geral da personalização de idioma precisam primeiro habilitar esse recurso. Políticas e fluxos de usuário que foram criados depois de ter a personalização de idioma habilitada por padrão.

Ao habilitar a personalização de idioma em um fluxo de usuário, você pode controlar o idioma do fluxo do usuário adicionando `ui_locales` o parâmetro.

1. No locatário do Azure AD B2C, selecione **fluxos de usuário**.
1. Clique no fluxo de usuário que você deseja habilitar para traduções.
1. Selecione **idiomas**.
1. Selecione **habilitar personalização de idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecione quais idiomas no seu fluxo de usuário estão habilitados

Habilite um conjunto de idiomas para que seu fluxo de usuário seja traduzido quando solicitado pelo navegador sem `ui_locales` o parâmetro.

1. Verifique se o fluxo do usuário tem a personalização de idioma habilitada nas instruções anteriores.
1. Na página **idiomas** do fluxo do usuário, selecione um idioma para o qual você deseja dar suporte.
1. No painel Propriedades, altere **habilitado** para **Sim**.
1. Selecione **salvar** na parte superior do painel Propriedades.

>[!NOTE]
>Se um `ui_locales` parâmetro não for fornecido, a página será convertida para o idioma do navegador do cliente somente se estiver habilitada.
>

## <a name="customize-your-strings"></a>Personalizar suas cadeias de caracteres

A personalização de idioma permite que você personalize qualquer cadeia de caracteres em seu fluxo de usuário.

1. Verifique se o fluxo do usuário tem a personalização de idioma habilitada nas instruções anteriores.
1. Na página **idiomas** do fluxo de usuário, selecione o idioma que você deseja personalizar.
1. Em **nível de página-arquivos de recursos**, selecione a página que você deseja editar.
1. Selecione **baixar padrões** (ou **baixar substituições** se você editou anteriormente este idioma).

Essas etapas fornecem um arquivo JSON que você pode usar para começar a editar suas cadeias de caracteres.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer cadeia de caracteres na página

1. Abra o arquivo JSON baixado de instruções anteriores em um editor de JSON.
1. Localize o elemento que você deseja alterar. Você pode encontrar `StringId` a cadeia de caracteres que está procurando ou procurar o `Value` atributo que deseja alterar.
1. Atualize o `Value` atributo com o que você deseja exibir.
1. Para cada cadeia de caracteres que você deseja alterar, `Override` Altere `true`para.
1. Salve o arquivo e carregue suas alterações. (Você pode encontrar o controle de carregamento no mesmo local em que você baixou o arquivo JSON.)

> [!IMPORTANT]
> Se você precisar substituir uma cadeia de caracteres, certifique-se de `Override` definir o `true`valor como. Se o valor não for alterado, a entrada será ignorada.

### <a name="change-extension-attributes"></a>Alterar atributos de extensão

Se você quiser alterar a cadeia de caracteres de um atributo de usuário personalizado ou se quiser adicionar um ao JSON, ele estará no seguinte formato:

```JSON
{
  "LocalizedStrings": [
    {
      "ElementType": "ClaimType",
      "ElementId": "extension_<ExtensionAttribute>",
      "StringId": "DisplayName",
      "Override": true,
      "Value": "<ExtensionAttributeValue>"
    }
    [...]
}
```

Substitua `<ExtensionAttribute>` pelo nome do seu atributo de usuário personalizado.

Substituir `<ExtensionAttributeValue>` pela nova cadeia de caracteres a ser exibida.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Forneça uma lista de valores usando LocalizedCollections

Se você quiser fornecer uma lista de valores para respostas, precisará criar um `LocalizedCollections` atributo. `LocalizedCollections`é uma matriz de `Name` pares `Value` e. A ordem dos itens será a ordem em que eles são exibidos. Para adicionar `LocalizedCollections`, use o seguinte formato:

```JSON
{
  "LocalizedStrings": [...],
  "LocalizedCollections": [{
      "ElementType":"ClaimType",
      "ElementId":"<UserAttribute>",
      "TargetCollection":"Restriction",
      "Override": true,
      "Items":[
           {
                "Name":"<Response1>",
                "Value":"<Value1>"
           },
           {
                "Name":"<Response2>",
                "Value":"<Value2>"
           }
     ]
  }]
}
```

* `ElementId`é o atributo de usuário ao `LocalizedCollections` qual este atributo é uma resposta.
* `Name`é o valor que é mostrado ao usuário.
* `Value`é o que é retornado na declaração quando essa opção é selecionada.

### <a name="upload-your-changes"></a>Carregar suas alterações

1. Depois de concluir as alterações no arquivo JSON, volte ao seu locatário B2C.
1. Selecione **fluxos de usuário** e clique no fluxo de usuário que você deseja habilitar para traduções.
1. Selecione **idiomas**.
1. Selecione o idioma para o qual você deseja traduzir.
1. Selecione a página na qual você deseja fornecer traduções.
1. Selecione o ícone de pasta e selecione o arquivo JSON a ser carregado.

As alterações são salvas automaticamente no fluxo do usuário.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizar a interface do usuário da página usando a personalização de idioma

Há duas maneiras de localizar seu conteúdo HTML. Uma maneira é ativar a [personalização da linguagem](active-directory-b2c-reference-language-customization.md). Habilitar esse recurso permite que Azure ad B2C encaminhe o parâmetro OpenID Connect, `ui-locales`, para seu ponto de extremidade. O servidor de conteúdo pode usar esse parâmetro para fornecer páginas HTML personalizadas que são específicas do idioma.

Como alternativa, você pode extrair o conteúdo de locais diferentes com base na localidade usada. No ponto de extremidade habilitado para CORS, você pode configurar uma estrutura de pastas para hospedar conteúdo para idiomas específicos. Você chamará o correto se usar o valor `{Culture:RFC5646}`de curinga. Por exemplo, suponha que este é o URI da página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Você pode carregar a página no `fr`. Quando a página extrai o conteúdo HTML e CSS, ele está puxando de:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Adicionar idiomas personalizados

Você também pode adicionar idiomas para os quais a Microsoft não fornece traduções no momento. Você precisará fornecer as traduções para todas as cadeias de caracteres no fluxo do usuário. Os códigos de idioma e localidade são limitados àqueles no padrão ISO 639-1.

1. No locatário do Azure AD B2C, selecione **fluxos de usuário**.
2. Clique no fluxo de usuário onde você deseja adicionar idiomas personalizados e clique em **idiomas**.
3. Selecione **Adicionar idioma personalizado** na parte superior da página.
4. No painel de contexto que é aberto, identifique em qual idioma você está fornecendo traduções inserindo um código de localidade válido.
5. Para cada página, você pode baixar um conjunto de substituições para inglês e trabalhar nas traduções.
6. Depois de terminar os arquivos JSON, você poderá carregá-los para cada página.
7. Selecione **habilitar**e seu fluxo de usuário agora pode mostrar esse idioma para seus usuários.
8. Salve o idioma.

>[!IMPORTANT]
>Você precisa habilitar os idiomas personalizados ou carregar substituições para ele antes de poder salvar.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Rótulos de personalização da interface do usuário da página como substituições

Quando você habilita a personalização de idioma, suas edições anteriores para rótulos usando a personalização da interface do usuário da página são mantidas em um arquivo JSON para inglês (EN). Você pode continuar a alterar seus rótulos e outras cadeias de caracteres carregando recursos de idioma na personalização do idioma.

### <a name="up-to-date-translations"></a>Traduções atualizadas

A Microsoft está comprometida em fornecer as traduções mais atualizadas para seu uso. A Microsoft aprimora continuamente as traduções e as mantém em conformidade para você. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão diretamente no seu fluxo de usuário.

### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda

Atualmente, a Microsoft não fornece suporte para idiomas da direita para a esquerda. Você pode fazer isso usando localidades personalizadas e usando CSS para alterar a forma como as cadeias de caracteres são exibidas. Se você precisar desse recurso, vote nele nos [comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções do provedor de identidade social

A Microsoft fornece `ui_locales` o parâmetro OIDC para logons sociais. Mas alguns provedores de identidade social, incluindo o Facebook e o Google, não os respeitam.

### <a name="browser-behavior"></a>Comportamento do navegador

O Chrome e o Firefox solicitam o idioma definido. Se for um idioma com suporte, ele será exibido antes do padrão. No momento, o Microsoft Edge não solicita um idioma e passa diretamente para o idioma padrão.

## <a name="supported-languages"></a>Idiomas suportados

O Azure AD B2C inclui suporte para os seguintes idiomas. As linguagens de fluxo de usuário são fornecidas pelo Azure AD B2C. As linguagens de notificação da MFA (autenticação multifator) são fornecidas pelo [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Idioma              | Código do idioma | Fluxos do utilizador         | Notificações de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | multi-hop            | w.x.y.                | :heavy_check_mark: |
| Búlgaro             | BG            | w.x.y.                | :heavy_check_mark: |
| Bangla                | bilhão            | :heavy_check_mark: | w.x.y.                |
| Catalão               | AC            | w.x.y.                | :heavy_check_mark: |
| Checo                 | cs            | :heavy_check_mark: | :heavy_check_mark: |
| Dinamarquês                | da            | :heavy_check_mark: | :heavy_check_mark: |
| Alemão                | de            | :heavy_check_mark: | :heavy_check_mark: |
| Grego                 | el            | :heavy_check_mark: | :heavy_check_mark: |
| Português               | pt-PT            | :heavy_check_mark: | :heavy_check_mark: |
| Espanhol               | es            | :heavy_check_mark: | :heavy_check_mark: |
| Estónio              | os/390            | w.x.y.                | :heavy_check_mark: |
| Basco (Basco)                | UE            | w.x.y.                | :heavy_check_mark: |
| Finlandês               | fi            | :heavy_check_mark: | :heavy_check_mark: |
| Francês                | fr            | :heavy_check_mark: | :heavy_check_mark: |
| Galego              | GL            | w.x.y.                | :heavy_check_mark: |
| Guzarate              | gu            | :heavy_check_mark: | w.x.y.                |
| Hebraico                | Paul            | w.x.y.                | :heavy_check_mark: |
| Hindi                 | Oi            | :heavy_check_mark: | :heavy_check_mark: |
| Croata              | hr            | :heavy_check_mark: | :heavy_check_mark: |
| Húngaro             | hu            | :heavy_check_mark: | :heavy_check_mark: |
| Indonésio            | id            | w.x.y.                | :heavy_check_mark: |
| Italiano               | it            | :heavy_check_mark: | :heavy_check_mark: |
| Japonês              | ja            | :heavy_check_mark: | :heavy_check_mark: |
| Cazaquistanês                | kk            | w.x.y.                | :heavy_check_mark: |
| Canarim               | kN            | :heavy_check_mark: | w.x.y.                |
| Coreano                | ko            | :heavy_check_mark: | :heavy_check_mark: |
| Lituano            | lt            | w.x.y.                | :heavy_check_mark: |
| Letão               | lv            | w.x.y.                | :heavy_check_mark: |
| Malayalam             | ml            | :heavy_check_mark: | w.x.y.                |
| Marata               | mr            | :heavy_check_mark: | w.x.y.                |
| Malaio                 | ms            | :heavy_check_mark: | :heavy_check_mark: |
| Norueguês Bokmal      | nb            | :heavy_check_mark: | w.x.y.                |
| Neerlandês                 | nl            | :heavy_check_mark: | :heavy_check_mark: |
| Norueguês             | não            | w.x.y.                | :heavy_check_mark: |
| Punjabi               | PA            | :heavy_check_mark: | w.x.y.                |
| Polaco                | pl            | :heavy_check_mark: | :heavy_check_mark: |
| Português-Brasil   | pt-br         | :heavy_check_mark: | :heavy_check_mark: |
| Português-Portugal | pt-pt         | :heavy_check_mark: | :heavy_check_mark: |
| Romeno              | ro            | :heavy_check_mark: | :heavy_check_mark: |
| Russo               | ru            | :heavy_check_mark: | :heavy_check_mark: |
| Eslovaco                | SK            | :heavy_check_mark: | :heavy_check_mark: |
| Esloveno             | sl            | w.x.y.                | :heavy_check_mark: |
| Sérvio-cirílico    | Sr-CRYL-cs    | w.x.y.                | :heavy_check_mark: |
| Sérvio-latino       | Sr-LATN-CS    | w.x.y.                | :heavy_check_mark: |
| Sueco               | sv            | :heavy_check_mark: | :heavy_check_mark: |
| Tamil                 | ta            | :heavy_check_mark: | w.x.y.                |
| Télego                | te            | :heavy_check_mark: | w.x.y.                |
| Tailandês                  | º            | :heavy_check_mark: | :heavy_check_mark: |
| Turco               | tr            | :heavy_check_mark: | :heavy_check_mark: |
| Ucraniano             | uk            | w.x.y.                | :heavy_check_mark: |
| Vietnamita            | vi            | w.x.y.                | :heavy_check_mark: |
| Chinês-simplificado  | zh-Hans       | :heavy_check_mark: | :heavy_check_mark: |
| Chinês-tradicional | zh-Hant       | :heavy_check_mark: | :heavy_check_mark: |
