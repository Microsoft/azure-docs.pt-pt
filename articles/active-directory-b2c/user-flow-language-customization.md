---
title: Personalização linguística no Azure Ative Directory B2C
description: Saiba como personalizar a experiência linguística nos fluxos do seu utilizador.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 08/13/2019
ms.custom: project-no-code
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 6b3bb3648fff6bc893ea07204fad79d2830a1b47
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91355098"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização linguística no Azure Ative Directory B2C

A personalização de linguagem no Azure Ative Directory B2C (Azure AD B2C) permite que o seu fluxo de utilizador acomode diferentes idiomas de acordo com as necessidades do seu cliente. A Microsoft fornece as traduções para [36 idiomas](#supported-languages), mas também pode fornecer as suas próprias traduções para qualquer idioma. Mesmo que a sua experiência seja fornecida apenas para um único idioma, pode personalizar qualquer texto nas páginas.

## <a name="how-language-customization-works"></a>Como funciona a personalização da linguagem

Utiliza a personalização de idiomas para selecionar em que idiomas o seu fluxo de utilizador está disponível. Depois de a funcionalidade estar ativada, pode fornecer o parâmetro de cadeia de consulta, `ui_locales` a partir da sua aplicação. Quando liga para o Azure AD B2C, a sua página é traduzida para o local que indicou. Este tipo de configuração dá-lhe controlo total sobre os idiomas no fluxo do seu utilizador e ignora as definições de idioma do navegador do cliente.

Pode não precisar desse nível de controlo sobre os idiomas que o seu cliente vê. Se não fornecer um `ui_locales` parâmetro, a experiência do cliente é ditada pelas definições do seu navegador. Ainda pode controlar quais os idiomas a que o seu fluxo de utilizador é traduzido adicionando-o como um idioma suportado. Se o navegador de um cliente estiver definido para mostrar um idioma que não quer suportar, então o idioma que selecionou como padrão em culturas apoiadas é mostrado em vez disso.

* **Ui-locais especificado linguagem**: Depois de ativar a personalização do idioma, o fluxo do utilizador é traduzido para o idioma especificado aqui.
* **Idioma solicitado pelo navegador**: Se nenhum `ui_locales` parâmetro foi especificado, o fluxo do utilizador é traduzido para o idioma solicitado pelo navegador, *se o idioma for suportado*.
* **Linguagem por defeito de política**: Se o navegador não especificar um idioma, ou especificar um que não é suportado, o fluxo do utilizador é traduzido para o idioma predefinido do fluxo do utilizador.

> [!NOTE]
> Se estiver a utilizar atributos personalizados do utilizador, tem de fornecer as suas próprias traduções. Para mais informações, consulte [Personalizar as suas cordas.](#customize-your-strings)

## <a name="support-requested-languages-for-ui_locales"></a>Apoiar línguas solicitadas para ui_locales

As políticas que foram criadas antes da disponibilidade geral de personalização linguística precisam de ativar este recurso primeiro. As políticas e os fluxos de utilizador que foram criados após a personalização da linguagem ativadas por padrão.

Quando ativa a personalização do idioma num fluxo de utilizador, pode controlar a linguagem do fluxo do utilizador adicionando o `ui_locales` parâmetro.

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
1. Clique no fluxo de utilizador que deseja ativar para traduções.
1. Selecione **Línguas**.
1. Selecione **Habilitar a personalização do idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecione quais os idiomas no fluxo do seu utilizador ativados

Ative um conjunto de idiomas para o fluxo do seu utilizador ser traduzido para quando solicitado pelo navegador sem o `ui_locales` parâmetro.

1. Certifique-se de que o fluxo do utilizador tem a personalização do idioma ativada a partir de instruções anteriores.
1. Na página **Idiomas** para o fluxo do utilizador, selecione um idioma que pretende suportar.
1. No painel de propriedades, **altere Ativado** para **Sim**.
1. **Selecione Guarde** na parte superior do painel de propriedades.

>[!NOTE]
>Se não for fornecido um `ui_locales` parâmetro, a página só será traduzida para o idioma do navegador do cliente se estiver ativada.
>

## <a name="customize-your-strings"></a>Personalize as suas cordas

A personalização de linguagem permite-lhe personalizar qualquer cadeia no fluxo do seu utilizador.

1. Certifique-se de que o fluxo do utilizador tem a personalização do idioma ativada a partir das instruções anteriores.
1. Na página **idiomas** para o fluxo do utilizador, selecione o idioma que pretende personalizar.
1. Nos **ficheiros de recursos de nível de página,** selecione a página que pretende editar.
1. Selecione **Descarregamento predefinido** (ou **Descarregue sobreposições** se já tiver editado este idioma).

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

```json
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

```json
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

1. Depois de completar as alterações ao seu ficheiro JSON, volte para o seu inquilino B2C.
1. Selecione **os fluxos** do Utilizador e clique no fluxo do utilizador que pretende ativar para traduções.
1. Selecione **Línguas**.
1. Selecione o idioma para o quais pretende traduzir.
1. Selecione a página onde pretende fornecer traduções.
1. Selecione o ícone da pasta e selecione o ficheiro JSON para carregar.

As alterações são guardadas automaticamente no fluxo do utilizador.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalize a UI da página utilizando a personalização da linguagem

Existem duas formas de localizar o seu conteúdo HTML. Uma maneira é ligar a [personalização da linguagem.](user-flow-language-customization.md) Ativar esta função permite que o Azure AD B2C encaminhe o parâmetro OpenID `ui-locales` Connect, para o seu ponto final. O seu servidor de conteúdo pode usar este parâmetro para fornecer páginas HTML personalizadas que são específicas da linguagem.

Em alternativa, pode retirar conteúdo de diferentes lugares com base no local que é usado. No seu ponto final ativado pelo CORS, pode configurar uma estrutura de pasta para hospedar conteúdo para idiomas específicos. Você vai chamar o certo se você usar o valor wildcard `{Culture:RFC5646}` . Por exemplo, assuma que esta é a sua página personalizada URI:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```

Pode carregar a página em `fr` . Quando a página puxa o conteúdo HTML e CSS, está a puxar de:

```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Adicionar idiomas personalizados

Também pode adicionar idiomas para os quais a Microsoft não fornece traduções. Terá de fornecer as traduções para todas as cordas do fluxo do utilizador. Os códigos linguísticos e locais estão limitados aos da norma ISO 639-1.

1. No seu inquilino Azure AD B2C, selecione **fluxos de utilizador**.
2. Clique no fluxo do utilizador onde pretende adicionar idiomas personalizados e, em seguida, clique em **Idiomas**.
3. **Selecione Adicionar o idioma personalizado** a partir do topo da página.
4. No painel de contexto que se abre, identifique para que língua está a fornecer traduções introduzindo um código local válido.
5. Para cada página, você pode baixar um conjunto de overrides para inglês e trabalhar nas traduções.
6. Depois de terminar os ficheiros JSON, pode carregá-los para cada página.
7. **Selecione Enable**, e o fluxo do utilizador pode agora mostrar este idioma para os seus utilizadores.
8. Salve a língua.

>[!IMPORTANT]
>Tem de ativar os idiomas personalizados ou fazer upload de sobreposições antes de poder guardar.

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

Azure AD B2C inclui suporte para as seguintes línguas. As línguas de fluxo do utilizador são fornecidas pelo Azure AD B2C. As línguas de notificação de autenticação multi-factor (MFA) são fornecidas pela [Azure MFA](../active-directory/authentication/concept-mfa-howitworks.md).

| Idioma              | Código do idioma | Fluxos do utilizador         | Notificações de MFA  |
|-----------------------| :-----------: | :----------------: | :----------------: |
| Árabe                | ar            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Búlgaro             | bg            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Bangla                | bn            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Catalão               | ca            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Checo                 | cs            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Dinamarquês                | da            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Alemão                | de            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Grego                 | el            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Em inglês               | en            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Em espanhol               | es            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Estónio              | et            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Basco                | eu            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Finlandês               | fi            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Francês                | fr            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Galego              | gl            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Guzerate              | gu            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Hebraico                | ele            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Hindi                 | Olá            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Croata              | hr            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Húngaro             | hu            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Indonésio            | ID            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Italiano               | que            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Japonês              | ja            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Cazaque                | kk            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Canarês               | kn            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Coreano                | ko            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Lituano            | lt            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Letão               | lv            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Malaiala             | ml            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Marata               | sr.            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Malaio                 | ms            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Norueguês Bokmal      | nb            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Neerlandês                 | nl            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Norueguês             | não            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Punjabi               | pa            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Polaco                | pl            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Português (Brasil)   | pt-br         | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Português (Portugal) | pt-pt         | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Romeno              | ro            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Russo               | ru            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Eslovaco                | sk            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Esloveno             | sl            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Sérvio - Cirílico    | sr-cryl-cs    | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Sérvio - Latim       | sr-latn-cs    | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Sueco               | sv            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Tâmil                 | ta            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Telugu                | te            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![X indicando não.](./media/user-flow-language-customization/no.png) |
| Tailandês                  | th            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Turco               | tr            | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Ucraniano             | Reino Unido            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Vietnamita            | vi            | ![X indicando não.](./media/user-flow-language-customization/no.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Chinês - Simplificado  | zh-hans       | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
| Chinês - Tradicional | zh-hant       | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) | ![Marca verde de verificação.](./media/user-flow-language-customization/yes.png) |
