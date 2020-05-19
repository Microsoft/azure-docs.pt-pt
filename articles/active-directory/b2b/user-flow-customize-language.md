---
title: Personalização linguística nos fluxos de utilizadores da AD Azure
description: Aprenda a personalizar a experiência linguística nos fluxos de utilizador.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: conceptual
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: ed56acc9276177951919a3bc63c8a1dc3876e1c4
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83597444"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Personalização linguística em Diretório Ativo Azure (Pré-visualização)
|     |
| --- |
| A inscrição em self-service é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).|
|     |

A personalização linguística no Azure Ative Directory (Azure AD) permite ao seu utilizador acomodar diferentes idiomas de acordo com as necessidades do seu utilizador. A Microsoft fornece as traduções para [36 idiomas.](#supported-languages) Mesmo que a sua experiência seja fornecida apenas para uma única língua, pode personalizar os nomes de atributos na página de recolha de atributos.

## <a name="how-language-customization-works"></a>Como funciona a personalização da linguagem

Por padrão, a personalização do idioma está ativada para que os utilizadores se inscrevam para garantir uma experiência de inscrição consistente. Pode utilizar idiomas para modificar as cordas apresentadas aos utilizadores como parte do processo de recolha de atributos durante a inscrição.

> [!NOTE]
> Se estiver a utilizar atributos de utilizador personalizados, tem de fornecer as suas próprias traduções. Para mais informações, consulte [Personalizar as suas cordas](#customize-your-strings).

## <a name="customize-your-strings"></a>Personalize as suas cordas

A personalização do idioma permite personalizar qualquer cadeia no fluxo do utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Sob **os serviços azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **os fluxos de utilizador (Pré-visualização)**.
3. Selecione o fluxo de utilizador que pretende ativar para traduções.
4. Selecione **Idiomas**.
5. Na página **Idiomas** para o fluxo do utilizador, selecione o idioma que pretende personalizar.
6. Expandir **página de recolha de atributos.**
7. Selecione **descarregamento de predefinições** (ou **descarregue** se tiver editado previamente este idioma).

Estes passos dão-lhe um ficheiro JSON que pode usar para começar a editar as suas cordas.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer corda na página

1. Abra o ficheiro JSON descarregado de instruções anteriores num editor da JSON.
1. Encontre o elemento que quer mudar. Pode encontrar `StringId` o fio que procura, ou procurar o `Value` atributo que quer mudar.
1. Atualize o `Value` atributo com o que pretende exibido.
1. Por cada corda que quiser mudar, mude `Override` `true` para.
1. Guarde o ficheiro e faça upload das suas alterações. (Pode encontrar o controlo de upload no mesmo local onde descarregou o ficheiro JSON.)

> [!IMPORTANT]
> Se precisar de anular uma corda, certifique-se de definir o `Override` valor para `true` . Se o valor não for alterado, a entrada é ignorada.

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

`<ExtensionAttribute>`Substitua-o pelo nome do seu atributo de utilizador personalizado.

`<ExtensionAttributeValue>`Substitua-a com a nova corda a exibir.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Forneça uma lista de valores utilizando Coleções Localizadas

Se quiser fornecer uma lista de valores definidos para respostas, precisa criar um `LocalizedCollections` atributo. `LocalizedCollections`é uma variedade de `Name` e `Value` pares. A encomenda dos itens será a ordem que são apresentadas. Para `LocalizedCollections` adicionar, utilize o seguinte formato:

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

* `ElementId`é o atributo do utilizador a que este `LocalizedCollections` atributo é uma resposta.
* `Name`é o valor que é mostrado ao utilizador.
* `Value`é o que é devolvido na reclamação quando esta opção é selecionada.

### <a name="upload-your-changes"></a>Faça upload das suas alterações

1. Depois de completar as alterações no seu ficheiro JSON, volte para o seu inquilino.
1. Selecione **fluxos de utilizador** e clique no fluxo de utilizador que pretende ativar para traduções.
1. Selecione **Idiomas**.
1. Selecione o idioma para o que pretende traduzir.
1. Selecione **página de recolha de Atributos.**
1. Selecione o ícone da pasta e selecione o ficheiro JSON para carregar.

As alterações são guardadas automaticamente no fluxo do utilizador.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalização da página UI como sobreposições

Ao ativar a personalização da linguagem, as suas edagens anteriores para etiquetas utilizando a personalização da ui página são persistidas num ficheiro JSON para inglês (en). Pode continuar a alterar os seus rótulos e outras cordas, carregando recursos linguísticos na personalização da linguagem.

### <a name="up-to-date-translations"></a>Traduções atualizadas

A Microsoft está empenhada em fornecer as traduções mais atualizadas para a sua utilização. A Microsoft melhora continuamente as traduções e mantém-nas em conformidade consigo. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão perfeitamente no fluxo do utilizador.

### <a name="support-for-right-to-left-languages"></a>Apoio às línguas da direita para a esquerda

AMicrosoft atualmente não fornece suporte para idiomas da direita para a esquerda. Pode fazê-lo usando locais personalizados e usando CSS para alterar a forma como as cordas são exibidas. Se precisar desta funcionalidade, por favor vote nele no [Feedback Do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções de fornecedorde identidade social

A Microsoft fornece o `ui_locales` parâmetro OIDC aos logins sociais. Mas alguns fornecedores de identidade social, incluindo o Facebook e o Google, não os honram.

### <a name="browser-behavior"></a>Comportamento do navegador

Chrome e Firefox ambos pedem a sua linguagem definida. Se for uma língua apoiada, é exibida antes do padrão. O Microsoft Edge atualmente não solicita um idioma e vai diretamente para o idioma padrão.

## <a name="supported-languages"></a>Linguagens suportadas

A Azure AD inclui suporte para as seguintes línguas. Os idiomas de fluxo do utilizador são fornecidos pela Azure AD. As línguas de notificação de autenticação multifactor (MFA) são fornecidas pelo [Azure MFA](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks).

| Linguagem              | Código do idioma | Fluxos do utilizador         | Notificações de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Búlgaro             | bg            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Rio Bangla                | bn            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Catalão               | ca            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Checo                 | cs            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Dinamarquês                | da            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Alemão                | de            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Grego                 | el            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Inglês               | en            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Espanhol               | es            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Estónio              | et            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Basco                | eu            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Finlandês               | fi            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Francês                | fr            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Galego              | gl            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Guzerate              | gu            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Hebraico                | ele            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Hindi                 | Olá            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Croata              | hr            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Húngaro             | hu            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Indonésio            | ID            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Italiano               | lo            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Japonês              | ja            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Cazaque                | kk            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Canarês               | kn            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Coreano                | ko            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Lituano            | lt            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Letão               | lv            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Malaiala             | ml            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Marata               | mr            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Malaio                 | ms            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Norueguês Bokmal      | nb            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Neerlandês                 | nl            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Norueguês             | não            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Punjabi               | pa            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Polaco                | pl            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Português (Brasil)   | pt-br         | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Português (Portugal) | pt-pt         | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Romeno              | ro            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Russo               | ru            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Eslovaco                | sk            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Esloveno             | sl            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Sérvio - Cirílico    | sr-cryl-cs    | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Sérvio - Latim       | sr-latn-cs    | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Sueco               | sv            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Tâmil                 | ta            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Telugu                | te            | ![sim](./media/user-flow-customize-language/yes.png) | ![não](./media/user-flow-customize-language/no.png) |
| Tailandês                  | th            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Turco               | tr            | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Ucraniano             | Reino Unido            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Vietnamita            | vi            | ![não](./media/user-flow-customize-language/no.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Chinês - Simplificado  | zh-hans       | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |
| Chinês - Tradicional | zh-hant       | ![sim](./media/user-flow-customize-language/yes.png) | ![sim](./media/user-flow-customize-language/yes.png) |