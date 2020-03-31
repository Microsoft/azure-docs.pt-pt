---
title: Personalização linguística no Diretório Ativo Azure B2C
description: Aprenda a personalizar a experiência linguística nos fluxos de utilizador.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 5b3af812b2b78c276b5345b9b19226e6e1dba80b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78185765"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização linguística no Diretório Ativo Azure B2C

A personalização linguística no Azure Ative Directory B2C (Azure AD B2C) permite ao seu utilizador fluir para acomodar diferentes idiomas de acordo com as necessidades do seu cliente. A Microsoft fornece as traduções para [36 idiomas,](#supported-languages)mas também pode fornecer as suas próprias traduções para qualquer idioma. Mesmo que a sua experiência seja fornecida apenas para uma única língua, pode personalizar qualquer texto nas páginas.

## <a name="how-language-customization-works"></a>Como funciona a personalização da linguagem

Utiliza a personalização do idioma para selecionar em que idioma está disponível o fluxo do utilizador. Depois de ativada a funcionalidade, pode fornecer o `ui_locales`parâmetro de corda de consulta, a partir da sua aplicação. Quando liga para o Azure AD B2C, a sua página é traduzida para o local que indicou. Este tipo de configuração dá-lhe controlo total sobre os idiomas no fluxo do utilizador e ignora as definições de idioma do navegador do cliente.

Pode não precisar desse nível de controlo sobre as línguas que o seu cliente vê. Se não fornecer um `ui_locales` parâmetro, a experiência do cliente é ditada pelas definições do seu navegador. Ainda pode controlar quais as línguas para as quais o fluxo do utilizador é traduzido adicionando-o como uma linguagem suportada. Se o navegador de um cliente está definido para mostrar um idioma que não quer suportar, então o idioma que selecionou como padrão em culturas suportadas é mostrado em vez disso.

* **ui-locales especificado idioma**: Depois de ativar a personalização da linguagem, o fluxo do utilizador é traduzido para o idioma que é especificado aqui.
* **Idioma solicitado pelo**navegador `ui_locales` : Se nenhum parâmetro foi especificado, o fluxo do utilizador é traduzido para o idioma solicitado pelo navegador, *se o idioma for suportado*.
* **Idioma padrão de política**: Se o navegador não especificar um idioma, ou especificar um que não é suportado, o fluxo do utilizador é traduzido para o idioma padrão do fluxo do utilizador.

> [!NOTE]
> Se estiver a utilizar atributos de utilizador personalizados, tem de fornecer as suas próprias traduções. Para mais informações, consulte [Personalizar as suas cordas](#customize-your-strings).

## <a name="support-requested-languages-for-ui_locales"></a>Apoio às línguas solicitadas para ui_locales

As políticas criadas antes da disponibilidade geral da personalização linguística precisam de ativar esta funcionalidade primeiro. Políticas e fluxos de utilizadores que foram criados após ter personalização de idioma ativado por padrão.

Quando ativa a personalização do idioma num fluxo de utilizador, `ui_locales` pode controlar o idioma do fluxo do utilizador adicionando o parâmetro.

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que pretende ativar para traduções.
1. Selecione **Idiomas**.
1. Selecione **Ativar a personalização do idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecione quais idiomas no fluxo do utilizador estão ativados

Ative um conjunto de idiomas para o fluxo do utilizador para `ui_locales` ser traduzido quando solicitado pelo navegador sem o parâmetro.

1. Certifique-se de que o fluxo do utilizador tem personalização linguística ativada a partir de instruções anteriores.
1. Na página **Idiomas** para o fluxo do utilizador, selecione um idioma que pretende suportar.
1. No painel de propriedades, altere **Habilitado** a **Sim**.
1. Selecione **Guardar** na parte superior do painel de propriedades.

>[!NOTE]
>Se `ui_locales` não for fornecido um parâmetro, a página é traduzida apenas para o idioma do navegador do cliente se esta estiver ativada.
>

## <a name="customize-your-strings"></a>Personalize as suas cordas

A personalização do idioma permite personalizar qualquer cadeia no fluxo do utilizador.

1. Certifique-se de que o fluxo do utilizador tem personalização linguística ativada a partir das instruções anteriores.
1. Na página **Idiomas** para o fluxo do utilizador, selecione o idioma que pretende personalizar.
1. Em **ficheiros de recursos de nível de página,** selecione a página que pretende editar.
1. Selecione **descarregamento de predefinições** (ou **descarregue** se tiver editado previamente este idioma).

Estes passos dão-lhe um ficheiro JSON que pode usar para começar a editar as suas cordas.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer corda na página

1. Abra o ficheiro JSON descarregado de instruções anteriores num editor da JSON.
1. Encontre o elemento que quer mudar. Pode encontrar `StringId` o fio que procura, ou procurar `Value` o atributo que quer mudar.
1. Atualize `Value` o atributo com o que pretende exibido.
1. Por cada corda que quiser `Override` mudar, mude para. `true`
1. Guarde o ficheiro e faça upload das suas alterações. (Pode encontrar o controlo de upload no mesmo local onde descarregou o ficheiro JSON.)

> [!IMPORTANT]
> Se precisar de anular uma corda, certifique-se de definir o `Override` valor para `true`. Se o valor não for alterado, a entrada é ignorada.

### <a name="change-extension-attributes"></a>Alterar atributos de extensão

Se quiser alterar a cadeia para um atributo personalizado do utilizador, ou se quiser adicionar uma ao JSON, está no seguinte formato:

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

Substitua-o `<ExtensionAttribute>` pelo nome do seu atributo de utilizador personalizado.

Substitua-a `<ExtensionAttributeValue>` com a nova corda a exibir.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Forneça uma lista de valores utilizando Coleções Localizadas

Se quiser fornecer uma lista de valores definidos para `LocalizedCollections` respostas, precisa criar um atributo. `LocalizedCollections`é uma `Name` variedade `Value` de e pares. A encomenda dos itens será a ordem que são apresentadas. Para `LocalizedCollections`adicionar, utilize o seguinte formato:

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

* `ElementId`é o atributo `LocalizedCollections` do utilizador a que este atributo é uma resposta.
* `Name`é o valor que é mostrado ao utilizador.
* `Value`é o que é devolvido na reclamação quando esta opção é selecionada.

### <a name="upload-your-changes"></a>Faça upload das suas alterações

1. Depois de completar as alterações no seu ficheiro JSON, volte para o seu inquilino B2C.
1. Selecione **fluxos de utilizador** e clique no fluxo de utilizador que pretende ativar para traduções.
1. Selecione **Idiomas**.
1. Selecione o idioma para o que pretende traduzir.
1. Selecione a página onde pretende fornecer traduções.
1. Selecione o ícone da pasta e selecione o ficheiro JSON para carregar.

As alterações são guardadas automaticamente no fluxo do utilizador.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalize a página UI usando a personalização da linguagem

Existem duas formas de localizar o seu conteúdo HTML. Uma maneira é ligar a [personalização da linguagem.](user-flow-language-customization.md) Ativar esta funcionalidade permite que o Azure AD B2C `ui-locales`endere o parâmetro OpenID Connect, até ao seu ponto final. O seu servidor de conteúdo pode usar este parâmetro para fornecer páginas HTML personalizadas que sejam específicas do idioma.

Em alternativa, pode retirar conteúdo de diferentes lugares com base no local que é usado. No seu ponto final ativado pelo CORS, pode configurar uma estrutura de pasta para hospedar conteúdo para idiomas específicos. Chamará o certo se usar o `{Culture:RFC5646}`valor wildcard. Por exemplo, assuma que esta é a sua página personalizada URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Pode carregar a `fr`página em . Quando a página puxa o conteúdo HTML e CSS, está a retirar de:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Adicionar idiomas personalizados

Também pode adicionar idiomas para os quais a Microsoft não fornece atualmente traduções. Terá de fornecer as traduções para todas as cordas do fluxo do utilizador. Os códigos linguísticos e locais limitam-se aos da norma ISO 639-1.

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
2. Clique no fluxo do utilizador onde pretende adicionar idiomas personalizados e, em seguida, clique em **Idiomas**.
3. Selecione **Adicionar idioma personalizado** a partir do topo da página.
4. No painel de contexto que se abre, identifique a linguagem para a qual está a fornecer traduções ao introduzir um código local válido.
5. Para cada página, pode baixar um conjunto de substituições para inglês e trabalhar nas traduções.
6. Depois de terminar com os ficheiros JSON, pode carregá-los para cada página.
7. Selecione **Enable**, e o fluxo do utilizador pode agora mostrar este idioma para os seus utilizadores.
8. Salve a linguagem.

>[!IMPORTANT]
>Tem de ativar os idiomas personalizados ou fazer upload para os mesmos antes de poder economizar.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalização da página UI como sobreposições

Ao ativar a personalização da linguagem, as suas edagens anteriores para etiquetas utilizando a personalização da ui página são persistidas num ficheiro JSON para inglês (en). Pode continuar a alterar os seus rótulos e outras cordas, carregando recursos linguísticos na personalização da linguagem.

### <a name="up-to-date-translations"></a>Traduções atualizadas

A Microsoft está empenhada em fornecer as traduções mais atualizadas para a sua utilização. A Microsoft melhora continuamente as traduções e mantém-nas em conformidade consigo. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão perfeitamente no fluxo do utilizador.

### <a name="support-for-right-to-left-languages"></a>Apoio às línguas da direita para a esquerda

AMicrosoft atualmente não fornece suporte para idiomas da direita para a esquerda. Pode fazê-lo usando locais personalizados e usando CSS para alterar a forma como as cordas são exibidas. Se precisar desta funcionalidade, por favor vote nele no [Feedback Do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções de fornecedorde identidade social

A Microsoft `ui_locales` fornece o parâmetro OIDC aos logins sociais. Mas alguns fornecedores de identidade social, incluindo o Facebook e o Google, não os honram.

### <a name="browser-behavior"></a>Comportamento do navegador

Chrome e Firefox ambos pedem a sua linguagem definida. Se for uma língua apoiada, é exibida antes do padrão. O Microsoft Edge atualmente não solicita um idioma e vai diretamente para o idioma padrão.

## <a name="supported-languages"></a>Linguagens suportadas

O Azure AD B2C inclui suporte para as seguintes línguas. Os idiomas de fluxo do utilizador são fornecidos pelo Azure AD B2C. As línguas de notificação de autenticação multifactor (MFA) são fornecidas pelo [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Idioma              | Código do idioma | Fluxos do utilizador         | Notificações de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Búlgaro             | bg            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Rio Bangla                | bn            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Catalão               | ca            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Checo                 | cs            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Dinamarquês                | da            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Alemão                | de            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Grego                 | el            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Inglês               | en            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Espanhol               | es            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Estónio              | et            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Basco                | eu            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Finlandês               | fi            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Francês                | fr            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Galego              | gl            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Guzerate              | gu            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Hebraico                | ele            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Olá            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Croata              | hr            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Húngaro             | hu            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Indonésio            | ID            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Italiano               | lo            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Japonês              | ja            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Cazaque                | kk            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Canarês               | kn            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Coreano                | ko            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Lituano            | lt            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Letão               | lv            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Malaiala             | ml            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Marata               | mr            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Malaio                 | ms            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Norueguês Bokmal      | nb            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Neerlandês                 | nl            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Norueguês             | não            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Polaco                | pl            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Português (Brasil)   | pt-br         | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Português (Portugal) | pt-pt         | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Romeno              | ro            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Russo               | ru            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Eslovaco                | sk            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Esloveno             | sl            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Sérvio - Cirílico    | sr-cryl-cs    | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Sérvio - Latim       | sr-latn-cs    | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Sueco               | sv            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Tâmil                 | ta            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![sim](./media/user-flow-language-customization/yes.png) | ![não](./media/user-flow-language-customization/no.png) |
| Tailandês                  | th            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Turco               | tr            | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Ucraniano             | Reino Unido            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Vietnamita            | vi            | ![não](./media/user-flow-language-customization/no.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Chinês - Simplificado  | zh-hans       | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
| Chinês - Tradicional | zh-hant       | ![sim](./media/user-flow-language-customization/yes.png) | ![sim](./media/user-flow-language-customization/yes.png) |
