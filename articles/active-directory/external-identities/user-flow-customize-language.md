---
title: Personalização linguística nos fluxos de utilizadores de AD Azure
description: Saiba como personalizar a experiência linguística nos fluxos de utilizador no Azure Ative Directory.
services: active-directory
author: msmimart
manager: celestedg
ms.service: active-directory
ms.subservice: B2B
ms.topic: how-to
ms.date: 05/06/2020
ms.author: mimart
ms.reviewer: elisolMS
ms.collection: M365-identity-device-management
ms.openlocfilehash: 98ead87ce2bd3b4e3ffc45763aa5151168c72078
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2020
ms.locfileid: "94576517"
---
# <a name="language-customization-in-azure-active-directory-preview"></a>Personalização linguística no Diretório Ativo Azure (Pré-visualização)

> [!NOTE]
> A inscrição de self-service é uma funcionalidade de pré-visualização pública do Azure Ative Directory. Para obter mais informações sobre pré-visualizações, veja [Termos de Utilização Suplementares do Microsoft Azure para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

A personalização de linguagem no Azure Ative Directory (Azure AD) permite que o seu fluxo de utilizador acomode diferentes idiomas de acordo com as necessidades do seu utilizador. A Microsoft fornece as traduções para [36 idiomas.](#supported-languages) Mesmo que a sua experiência seja fornecida apenas para um único idioma, pode personalizar os nomes de atributos na página de recolha de atributos.

## <a name="how-language-customization-works"></a>Como funciona a personalização da linguagem

Por predefinição, a personalização de linguagem é ativada para os utilizadores que se inscrevam para garantir uma experiência de inscrição consistente. Pode utilizar idiomas para modificar as cordas apresentadas aos utilizadores como parte do processo de recolha do atributo durante a inscrição.

> [!NOTE]
> Se estiver a utilizar atributos personalizados do utilizador, tem de fornecer as suas próprias traduções. Para mais informações, consulte [Personalizar as suas cordas.](#customize-your-strings)

## <a name="customize-your-strings"></a>Personalize as suas cordas

A personalização de linguagem permite-lhe personalizar qualquer cadeia no fluxo do seu utilizador.

1. Inicie sessão no [portal do Azure](https://portal.azure.com) como administrador do Azure Active Directory.
2. Ao abrigo **dos serviços Azure,** selecione **Azure Ative Directory**.
3. No menu esquerdo, selecione **Identidades Externas.**
4. Selecione **fluxos de utilizador (pré-visualização)**.
3. Selecione o fluxo de utilizador que deseja ativar para traduções.
4. Selecione **Línguas**.
5. Na página **idiomas** para o fluxo do utilizador, selecione o idioma que pretende personalizar.
6. Expandir **a página de recolha do Atributo**.
7. Selecione **Descarregamento predefinido** (ou **Descarregue sobreposições** se já tiver editado este idioma).

Estes passos dão-lhe um ficheiro JSON que pode usar para começar a editar as suas cordas.

### <a name="change-any-string-on-the-page"></a>Altere qualquer cadeia na página

1. Abra o ficheiro JSON descarregado de instruções anteriores num editor da JSON.
1. Encontre o elemento que pretende alterar. Pode encontrar `StringId` a corda que procura, ou procurar o atributo que quer `Value` mudar.
1. Atualize o `Value` atributo com o que pretende visualizar.
1. Para cada corda que quiser mudar, mude `Override` para `true` .
1. Guarde o ficheiro e faça o upload das suas alterações. (Pode encontrar o controlo de upload no mesmo local onde descarregou o ficheiro JSON.)

> [!IMPORTANT]
> Se precisar de sobrepor uma corda, certifique-se de que define o `Override` valor para `true` . Se o valor não for alterado, a entrada é ignorada.

### <a name="change-extension-attributes"></a>Alterar atributos de extensão

Se quiser alterar a cadeia para um atributo de utilizador personalizado, ou se pretende adicionar um ao JSON, está no seguinte formato:

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

`<ExtensionAttributeValue>`Substitua-a com a nova corda a visualizar.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornecer uma lista de valores utilizandoCollections Localizados

Se quiser fornecer uma lista definida de valores para respostas, precisa de criar um `LocalizedCollections` atributo. `LocalizedCollections` é uma matriz de `Name` `Value` pares. A encomenda dos artigos será a ordem que são apresentadas. Para adicionar `LocalizedCollections` , utilize o seguinte formato:

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

* `ElementId` é o atributo do utilizador a que este `LocalizedCollections` atributo é uma resposta.
* `Name` é o valor que é mostrado ao utilizador.
* `Value` é o que é devolvido na reclamação quando esta opção é selecionada.

### <a name="upload-your-changes"></a>Faça upload das suas alterações

1. Depois de completar as alterações ao seu ficheiro JSON, volte para o seu inquilino.
1. Selecione **os fluxos** do Utilizador e clique no fluxo do utilizador que pretende ativar para traduções.
1. Selecione **Línguas**.
1. Selecione o idioma para o quais pretende traduzir.
1. Selecione **a página de recolha de atributos**.
1. Selecione o ícone da pasta e selecione o ficheiro JSON para carregar.

As alterações são guardadas automaticamente no fluxo do utilizador.

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalização de página UI como sobreposições

Quando ativa a personalização de linguagem, as suas edições anteriores para etiquetas que utilizam a personalização da página UI são persistiu num ficheiro JSON para inglês (pt). Pode continuar a alterar as suas etiquetas e outras cordas, enviando recursos linguísticos na personalização da linguagem.

### <a name="up-to-date-translations"></a>Traduções atualizadas

A Microsoft está empenhada em fornecer as traduções mais atualizadas para a sua utilização. A Microsoft melhora continuamente as traduções e mantém-nas em conformidade consigo. A Microsoft identificará bugs e alterações na terminologia global e fará atualizações que funcionarão perfeitamente no fluxo do seu utilizador.

### <a name="support-for-right-to-left-languages"></a>Apoio às línguas da direita para a esquerda

A Microsoft não fornece suporte para línguas da direita para a esquerda. Pode fazê-lo utilizando locais personalizados e usando CSS para alterar a forma como as cordas são apresentadas. Se precisar desta funcionalidade, por favor vote no [Azure Feedback](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).

### <a name="social-identity-provider-translations"></a>Traduções de fornecedores de identidade social

A Microsoft fornece o `ui_locales` parâmetro OIDC para logins sociais. Mas alguns fornecedores de identidade social, incluindo o Facebook e o Google, não os honram.

### <a name="browser-behavior"></a>Comportamento do navegador

Chrome e Firefox solicitam ambos para o seu idioma definido. Se for uma língua suportada, é exibida antes do padrão. Atualmente, o Microsoft Edge não solicita um idioma e vai diretamente para o idioma predefinido.

## <a name="supported-languages"></a>Linguagens suportadas

A Azure AD inclui suporte para as seguintes línguas. As línguas de fluxo do utilizador são fornecidas pela Azure AD. As línguas de notificação de autenticação multi-factor (MFA) são fornecidas pela [Azure MFA](../authentication/concept-mfa-howitworks.md).

| Linguagem              | Código do idioma | Fluxos do utilizador         | Notificações de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Búlgaro             | bg            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Bangla                | bn            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Catalão               | ca            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Checo                 | cs            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Dinamarquês                | da            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Alemão                | de            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Grego                 | el            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Em inglês               | en            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Em espanhol               | es            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Estónio              | et            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Basco                | eu            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Finlandês               | fi            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Francês                | fr            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Galego              | gl            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Guzerate              | gu            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Hebraico                | ele            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Hindi                 | Olá            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Croata              | hr            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Húngaro             | hu            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Indonésio            | ID            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Italiano               | que            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Japonês              | ja            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Cazaque                | kk            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Canarês               | kn            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Coreano                | ko            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Lituano            | lt            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Letão               | lv            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Malaiala             | ml            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Marata               | sr.            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Malaio                 | ms            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Norueguês Bokmal      | nb            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Neerlandês                 | nl            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Norueguês             | não            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Punjabi               | pa            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Polaco                | pl            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Português (Brasil)   | pt-br         | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Português (Portugal) | pt-pt         | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Romeno              | ro            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Russo               | ru            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Eslovaco                | sk            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Esloveno             | sl            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Sérvio - Cirílico    | sr-cryl-cs    | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Sérvio - Latim       | sr-latn-cs    | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Sueco               | sv            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Tâmil                 | ta            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Telugu                | te            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![X indicando não.](./media/user-flow-customize-language/no.png) |
| Tailandês                  | th            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Turco               | tr            | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Ucraniano             | Reino Unido            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Vietnamita            | vi            | ![X indicando não.](./media/user-flow-customize-language/no.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Chinês - Simplificado  | zh-hans       | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |
| Chinês - Tradicional | zh-hant       | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) | ![Marca verde de verificação.](./media/user-flow-customize-language/yes.png) |