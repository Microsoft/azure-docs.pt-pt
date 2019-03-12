---
title: Migrar para a V3 - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Saiba como migrar do V2 para V3 da API de texto do Translator.
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: v-jansko
ms.openlocfilehash: 89a97b583407b448f296b1a54f5aabbf825b8b65
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57537750"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Microsoft Translator V2 de API de texto para a migração V3

> [!NOTE]
> V2 foi preterido no dia 30 de Abril de 2018 e será descontinuada a 30 de Abril de 2019.

> O Microsoft Translator Hub será retirado a 30 de Abril de 2019. [Ver as informações de migração de importantes e datas](https://www.microsoft.com/translator/business/hub/).  

A equipe do Microsoft Translator lançou a versão 3 (V3) da API de texto do Translator. Esta versão inclui novos recursos, métodos preteridos e um novo formato para enviar para e a receção de dados do serviço Microsoft Translator. Este documento fornece informações para alterar os aplicativos usem V3. 

O final deste documento contém links úteis para obter mais informações.

## <a name="summary-of-features"></a>Resumo das funcionalidades

* Sem rastreio - não-rastreio V3 se aplica a todos os escalões de preço no portal do Azure. Esta funcionalidade significa que nenhum texto submetido para a API V3, serão guardados pela Microsoft.
* JSON - XML é substituído pelo JSON. Todos os dados enviados para o serviço e recebidos do serviço está no formato JSON.
* Vários idiomas de destino numa única solicitação - traduzir o método aceita vários "para" idiomas para a tradução numa única solicitação. Por exemplo, um único pedido pode ser 'from' inglês e "para" alemão, espanhol e japonês ou qualquer outro grupo de idiomas.
* Dicionário bilingue - foi adicionado um método de dicionário bilingue para a API. Esse método inclui "pesquisa" e "exemplos".
* Translitere - foi adicionado um método transliterate para a API. Este método converterá palavras e frases num script (por exemplo Árabe) em outro script (por exemplo Latim).
* Linguagens – um novo método de "idiomas" fornece informações de idioma, no formato JSON, para utilização com o "traduzir", "dicionário" e "translitere" métodos.
* Novo para Translate - novos recursos foram adicionados ao método "traduzir" para dar suporte a alguns dos recursos que estavam na V2 API como métodos separados. Um exemplo é TranslateArray.
* Método de falar - funcionalidade de texto em voz já não é suportada na API do Microsoft Translator. Funcionalidade de texto em voz está disponível no [serviço de voz de Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

A seguinte lista de métodos V2 e V3 identifica as APIs que irá fornecer a funcionalidade com o V2 e V3 métodos.

| V2 o método API   | V3 Compatibilidade da API |
|:----------- |:-------------|
| `Translate`     | [Traduzir](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Traduzir](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Idiomas](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Idiomas](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Serviço de voz da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Serviço de voz da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Detect](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detect](reference/v3-0-detect.md)         |
| `AddTranslation`     | A funcionalidade já não é suportada       |
| `AddTranslationArray`    | A funcionalidade já não é suportada          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | A funcionalidade já não é suportada         |
| `GetTranslationsArray`      | A funcionalidade já não é suportada         |

## <a name="move-to-json-format"></a>Mover para o formato JSON

V2 de tradução de texto do Microsoft Translator aceites e devolveu dados em formato XML. Na V3 todos os dados enviados e recebidos através da API está no formato JSON. XML já não serão aceites ou devolvido na V3.

Esta alteração irá afetar vários aspectos de um aplicativo escrito para a API de tradução de texto do V2. Por exemplo: A API de idiomas devolve informações de idioma para tradução de texto, Transliteração e os métodos de dicionário de dois. Pode pedir a todas as informações de idioma para todos os métodos numa chamada ou pedi-los individualmente.

O método de idiomas não necessita de autenticação; ao clicar na ligação seguinte, pode ver todas as informações de idioma para V3 no JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chave de Autenticação

A chave de autenticação que estiver a utilizar para V2 serão aceites para V3. Não terá de obter uma subscrição nova. Será capaz de misturar V2 e V3 nas suas aplicações durante o período de migração yearlong, tornando mais fácil para que ofereça novas versões, embora ainda estiver a migrar do V2-XML em V3 JSON.

## <a name="pricing-model"></a>Modelo de Determinação de Preço

Microsoft Translator V3 preço baseia-se da mesma forma que v2 foi um preço; por caractere, incluindo espaços. Os novos recursos do V3 fazer algumas alterações no que carateres são contabilizados para faturação.

| Método v3   | Carateres contabilizados para faturação |
|:----------- |:-------------|
| `Languages`     | Não existem carateres submetidos, none contabilizados, sem encargos.          |
| `Translate`     | Contagem de baseia-se o número de carateres é submetido para a tradução e quantos idiomas, os caracteres são convertidos em. submetido a 50 carateres e 5 idiomas solicitados serão 50 x 5.           |
| `Transliterate`     | Número de carateres submetido para a Transliteração é contabilizado.         |
| `Dictionary lookup & example`     | Número de carateres submetido para a pesquisa de dicionário e exemplos é contabilizado.         |
| `BreakSentence`     | Sem custos.       |
| `Detect`     | Sem custos.      |

## <a name="v3-end-points"></a>V3 pontos finais

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Métodos de traduções de texto de v3 API

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Compatibilidade e personalização

> [!NOTE] 

> O Microsoft Translator Hub será retirado a 30 de Abril de 2019. [Ver as informações de migração de importantes e datas](https://www.microsoft.com/translator/business/hub/).   

Microsoft Translator V3 usa a tradução automática neural por predefinição. Como tal, não pode ser utilizado com o Microsoft Translator Hub. O Hub de Microsoft Translator suporta apenas herdados estatísticos de tradução automática. Personalização de tradução neural já está disponível com o Translator personalizado. [Saiba mais sobre a personalização de tradução automática neural](custom-translator/overview.md)

Tradução neural com a API de texto da V3 não suporta a utilização das categorias padrão (SMT, voz, tech, generalnn).

| |Ponto Final|    Compatibilidade do processador GDPR|  Utilize o Hub de Microsoft Translator| Utilize o Translator personalizado (pré-visualização)|
|:-----|:-----|:-----|:-----|:-----|
|Versão de API de texto do Translator 2| api.microsofttranslator.com|    Não  |Sim    |Não|
|Versão de API de texto do Translator 3| api.cognitive.microsofttranslator.com|  Sim|    Não| Sim|

**Versão de API de texto do Translator 3**
* Está disponível em geral e é totalmente suportado.
* É com o GDPR em conformidade, como um processador e satisfaça todos os requisitos de ISO 20001 e 20018 assim como SOC 3 certificação. 
* Permite-lhe invocar os sistemas de tradução de rede neural que tiver personalizado o tradutor de personalizado (pré-visualização), o novo recurso de personalização do Microsoft Translator NMT. 
* Isso não concede acesso a sistemas de tradução personalizadas criadas com o Microsoft Translator Hub.

Versão 3 do texto do Translator API está a utilizar se estiver a utilizar o ponto de extremidade api.cognitive.microsofttranslator.com.

**Versão de API de texto do Translator 2**
* Foi preterido. Ele será descontinuado a 30 de Abril de 2019. 
* Não satisfaz todos os requisitos de certificação de 3 de SOC e ISO 20001,20018. 
* Não permite invocar os sistemas de tradução de rede neural que tiver personalizado com a funcionalidade de personalização do Microsoft Translator.
* Fornece acesso aos sistemas de tradução personalizadas criadas com o Microsoft Translator Hub.
* Versão 2 do texto do Translator API está a utilizar se estiver a utilizar o ponto de extremidade api.microsofttranslator.com.

Nenhuma versão de API do Translator cria um registo das suas traduções. Suas traduções nunca são partilhadas com qualquer pessoa. Obter mais informações sobre o [Translator não-rastreio](https://www.aka.ms/NoTrace) página Web.

## <a name="links"></a>Ligações

* [Política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informação Legal sobre o Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Termos dos Online Services](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Passos Seguintes

> [!div class="nextstepaction"]
> [Ver documentação de V3.0](reference/v3-0-reference.md)
