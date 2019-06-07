---
title: Personalização de idioma no Azure Active Directory B2C | Documentos da Microsoft
description: Saiba mais sobre como personalizar a experiência de idioma.
services: active-directory-b2c
author: mmacy
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 11/30/2018
ms.author: marsma
ms.subservice: B2C
ms.openlocfilehash: 48633b195df997f0e9a8f06bd4f5c553ca620e98
ms.sourcegitcommit: adb6c981eba06f3b258b697251d7f87489a5da33
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/04/2019
ms.locfileid: "66509399"
---
# <a name="language-customization-in-azure-active-directory-b2c"></a>Personalização de idioma no Azure Active Directory B2C

Personalização de idioma no Azure Active Directory B2C (Azure AD B2C) permite que o fluxo de utilizador acomodar idiomas diferentes de acordo com as necessidades dos clientes.  As traduções para a Microsoft fornece [36 idiomas](#supported-languages), mas também pode fornecer suas próprias traduções para qualquer idioma. Mesmo que sua experiência é fornecida para um único idioma, pode personalizar qualquer texto nas páginas.  

## <a name="how-language-customization-works"></a>Como funciona a personalização de idioma
Utilizar a personalização de idioma para selecionar que o fluxo de utilizador está disponível de idiomas. Depois da funcionalidade está ativada, pode fornecer o parâmetro de cadeia de caracteres de consulta, `ui_locales`, desde a sua aplicação. Quando chama-se para o Azure AD B2C, a página é traduzida para a localidade que indicou. Este tipo de configuração dá-lhe controlo total sobre os idiomas no seu fluxo de utilizador e ignora as definições de idioma do navegador do cliente. 

Poderá não ter esse nível de controle sobre quais idiomas vê do seu cliente. Se não fornecer um `ui_locales` parâmetro, experiência do cliente é ditada por configurações do navegador.  Pode continuar a controlar que o fluxo de utilizador é traduzido ao adicioná-la como um idioma com suporte de idiomas. Se o browser de um cliente está configurado para mostrar um idioma que não pretende oferecer apoio técnico, em seguida, o idioma que selecionou como uma predefinição na culturas suportadas é apresentado em vez disso.

- **localidades da interface do usuário especificado linguagem**: Depois de ativar a personalização de idioma, o fluxo de utilizador é traduzido para o idioma que é especificado aqui.
- **Idioma de pedido de navegador**: Se nenhum `ui_locales` foi especificado um parâmetro, o fluxo de utilizador é traduzido para a linguagem de pedido de browser *se o idioma é suportado*.
- **Idioma de padrão de política**: Se o navegador não especifica um idioma ou especifica um que não é suportado, o fluxo de utilizador é traduzido para o idioma de padrão de fluxo de utilizador.

>[!NOTE]
>Se estiver usando os atributos de utilizador personalizada, terá de fornecer suas próprias traduções. Para obter mais informações, consulte [personalizar suas cadeias de caracteres](#customize-your-strings).
>

## <a name="support-requested-languages-for-uilocales"></a>Suporte a idiomas pedidos para ui_locales 
Políticas que foram criadas antes da disponibilidade geral da personalização de idioma tem de ativar esta funcionalidade em primeiro lugar. Políticas e fluxos de utilizador que foram criados depois de tem a personalização de idioma ativada por predefinição. 

Ao ativar a personalização de idioma num fluxo de utilizador, pode controlar o idioma do fluxo de utilizador ao adicionar o `ui_locales` parâmetro.
1. No seu inquilino do Azure AD B2C, selecione **fluxos de utilizador**.
2. Clique em do fluxo de utilizador que pretende ativar para traduções.
3. Selecione **idiomas**.  
4. Selecione **ativar a personalização de idioma**.

## <a name="select-which-languages-in-your-user-flow-are-enabled"></a>Selecione os idiomas no seu fluxo de utilizador que estão ativados 
Ativar um conjunto de idiomas para o fluxo de utilizador ser convertido para quando solicitado pelo navegador sem o `ui_locales` parâmetro.
1. Certifique-se de que o fluxo de utilizador tem de personalização de idioma ativada a partir de instruções anteriores.
2. Sobre o **idiomas** para o fluxo de utilizador, selecione um idioma que pretende suportar.
3. No painel de propriedades, altere **Enabled** ao **Sim**.  
4. Selecione **guardar** na parte superior do painel de propriedades.

>[!NOTE]
>Se um `ui_locales` parâmetro não for fornecido, a página é traduzida para o idioma do navegador do cliente apenas se estiver ativada.
>

## <a name="customize-your-strings"></a>Personalizar suas cadeias de caracteres
Personalização de idioma permite-lhe personalizar a qualquer cadeia de caracteres no seu fluxo de utilizador.
1. Certifique-se de que o fluxo de utilizador tem de personalização de idioma ativada a partir das instruções anteriores.
2. Sobre o **idiomas** para o fluxo de utilizador, selecione o idioma que pretende personalizar.
3. Sob **arquivos de recursos de nível de página**, selecione a página que pretende editar.
4. Selecione **descarregar predefinições** (ou **transferir substituições** se anteriormente tiver editado neste idioma).

Estes passos dão-lhe um ficheiro JSON que pode utilizar para começar a editar suas cadeias de caracteres.

### <a name="change-any-string-on-the-page"></a>Alterar qualquer cadeia de caracteres na página
1. Abra o ficheiro JSON que transferiu do instruções anteriores num editor de JSON.
2. Localize o elemento que pretende alterar.  Pode encontrar `StringId` para a cadeia de procura, ou procure o `Value` atributo que pretende alterar.
3. Atualização do `Value` atributo com o que pretende apresentar.
4. Para cada cadeia de caracteres que deseja alterar, altere `Override` para `true`.
5. Guarde o ficheiro e carregar as suas alterações. (Pode encontrar o controle de carregamento no mesmo local como onde transferiu o ficheiro JSON.) 

>[!IMPORTANT]
>Se precisar de substituir uma cadeia de caracteres, certifique-se definir o `Override` valor a `true`.  Se o valor não é alterado, a entrada é ignorada. 
>

### <a name="change-extension-attributes"></a>Altere os atributos de extensão
Se pretender alterar a cadeia de caracteres para um atributo de utilizador personalizada ou que pretende adicionar um para o JSON, está no seguinte formato:
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

Substitua `<ExtensionAttribute>` com o nome do seu atributo de utilizador personalizada.  

Substitua `<ExtensionAttributeValue>` com a nova cadeia de caracteres a serem exibidos.

### <a name="provide-a-list-of-values-by-using-localizedcollections"></a>Fornecer uma lista de valores, utilizando LocalizedCollections
Se quiser fornecer uma lista de conjunto de valores para as respostas, terá de criar um `LocalizedCollections` atributo.  `LocalizedCollections` é uma matriz de `Name` e `Value` pares. A ordem dos itens será a ordem que elas são exibidas.  Para adicionar `LocalizedCollections`, utilize o seguinte formato:

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

* `ElementId` é o utilizador de atributo que este `LocalizedCollections` atributo é uma resposta.
* `Name` é o valor que é mostrado ao usuário.
* `Value` é o que é devolvido na afirmação quando esta opção está selecionada.

### <a name="upload-your-changes"></a>Carregar as suas alterações
1. Depois de concluir as alterações ao seu ficheiro JSON, regresse ao seu inquilino do B2C.
2. Selecione **fluxos de utilizador** e clique em do fluxo de utilizador que pretende ativar para traduções.
3. Selecione **idiomas**.
4. Selecione o idioma que queira traduzir para.
5. Selecione a página onde deseja fornecer traduções.
6. Selecione o ícone de pasta e selecione o ficheiro JSON para carregar.
 
As alterações são guardadas automaticamente o fluxo de utilizador.

## <a name="customize-the-page-ui-by-using-language-customization"></a>Personalizar a página da interface do Usuário, utilizando a personalização de idioma

Existem duas formas de localizar o seu conteúdo HTML. É uma forma ativar a [personalização de idioma](active-directory-b2c-reference-language-customization.md). Ativar esta funcionalidade permite que o Azure AD B2C para reencaminhar o parâmetro abrir ID Connect, `ui-locales`, para o ponto final.  O servidor de conteúdo pode utilizar este parâmetro para fornecer páginas HTML personalizadas que são específicas do idioma.

Em alternativa, pode solicitar conteúdo a partir de locais diferentes com base na localidade que é utilizada. O ponto final CORS ativado, pode configurar uma estrutura de pastas para alojar conteúdo para idiomas específicos. Chamará o um certo, se usar o valor de caráter universal `{Culture:RFC5646}`.  Por exemplo, suponha que este é o URI da página personalizada:

```
https://wingtiptoysb2c.blob.core.windows.net/{Culture:RFC5646}/wingtip/unified.html
```
Pode carregar a página no `fr`. Quando a página recebe o conteúdo HTML e CSS, ele está obtendo a partir de:
```
https://wingtiptoysb2c.blob.core.windows.net/fr/wingtip/unified.html
```

## <a name="add-custom-languages"></a>Adicionar idiomas personalizados

Também pode adicionar idiomas que a Microsoft não fornece atualmente traduções para. Terá de fornecer as traduções para todas as cadeias de caracteres no fluxo de utilizador.  Códigos de idioma e região são limitados no padrão ISO 639-1. 

1. No seu inquilino do Azure AD B2C, selecione **fluxos de utilizador**.
2. Clique no fluxo de utilizador onde pretende adicionar idiomas personalizados e, em seguida, clique em **idiomas**.
3. Selecione **Adicionar idioma personalizado** da parte superior da página.
4. No painel de contexto que se abre, identifique o idioma que está a oferecer traduções para ao introduzir um código de região válido.
5. Para cada página, pode transferir um conjunto de substituições para inglês e funcionam nas traduções.
6. Depois de terminar com os ficheiros JSON, pode carregá-los para cada página.
7. Selecione **ativar**, e o fluxo de utilizador pode agora mostrar neste idioma para os seus utilizadores.
8. Guarde o idioma.

>[!IMPORTANT]
>Tem de ativar os idiomas personalizados ou carregar substituições para o mesmo para poder guardar.
>

## <a name="additional-information"></a>Informações adicionais

### <a name="page-ui-customization-labels-as-overrides"></a>Etiquetas de personalização da IU da página como substituições
Ao ativar a personalização de idioma, as edições anteriores para etiquetas utilizando a personalização da interface do Usuário da página são mantidas num ficheiro JSON para inglês (en). Pode continuar a alterar as etiquetas e outras cadeias de caracteres através do carregamento de recursos de idioma na personalização de idioma.
### <a name="up-to-date-translations"></a>Traduções atualizadas
A Microsoft está empenhada em fornecer as traduções mais atualizadas para a sua utilização. A Microsoft continuamente melhora traduções e mantém-em conformidade para. A Microsoft irá identificar bugs e as alterações na terminologia global e disponibilizar atualizações de funcionarão de forma totalmente integrada no seu fluxo de utilizador.
### <a name="support-for-right-to-left-languages"></a>Suporte para idiomas da direita para a esquerda
Microsoft atualmente não oferece suporte para idiomas da direita para a esquerda. Isso pode ser feito usando as localidades personalizadas e usando a CSS para alterar a forma como as cadeias de caracteres são exibidas.  Se precisar desta funcionalidade, vote em [comentários do Azure](https://feedback.azure.com/forums/169401-azure-active-directory/suggestions/19393000-provide-language-support-for-right-to-left-languag).
### <a name="social-identity-provider-translations"></a>Traduções de fornecedor de identidade de redes sociais
A Microsoft fornece o `ui_locales` parâmetro OIDC para inícios de sessão sociais. Mas alguns fornecedores de identidade de redes sociais, incluindo o Facebook e o Google, não respeitam-los. 
### <a name="browser-behavior"></a>Comportamento do navegador
Chrome e Firefox que ambos do pedido para o seu idioma de conjunto. Se se trata de um idioma suportado, ele é exibido antes que a predefinição. Atualmente, o Microsoft Edge não solicita um idioma e vai diretamente para o idioma predefinido.

### <a name="supported-languages"></a>Idiomas suportados

| Idioma              | Código do idioma |
|-----------------------|---------------|
| Bangla                | bn            |
| Checo                 | cs            |
| Dinamarquês                | da            |
| Alemão                | de            |
| Grego                 | el            |
| Português               | en            |
| Espanhol               | es            |
| Finlandês               | fi            |
| Francês                | fr            |
| Guzarate              | gu            |
| Hindi                 | hi            |
| Croata              | hr            |
| Húngaro             | hu            |
| Italiano               | it            |
| Japonês              | ja            |
| Canarim               | kN            |
| Coreano                | ko            |
| Malayalam             | ml            |
| Marati               | mr            |
| Malaio                 | ms            |
| Norwegian Bokmal      | nb            |
| Neerlandês                 | nl            |
| Punjabi               | pa            |
| Polaco                | pl            |
| Português (Brasil)   | pt-br         |
| Português (Portugal) | pt-pt         |
| Romeno              | ro            |
| Russo               | ru            |
| Eslovaco                | SK            |
| Sueco               | sv            |
| Tamil                 | ta            |
| Télego                | te            |
| Tailandês                  | TH            |
| Turco               | tr            |
| Chinês - simplificado  | zh-hans       |
| Chinês - tradicional | zh-hant       |
