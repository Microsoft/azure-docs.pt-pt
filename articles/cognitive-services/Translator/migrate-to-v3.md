---
title: Migrar para v3-API de Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Este artigo fornece as etapas para ajudá-lo a migrar do v2 para a V3 do API de Tradução de Texto de serviços cognitivas do Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 02/01/2019
ms.author: swmachan
ms.openlocfilehash: eb43d549d3e0cd449c865d533fc8701c4c3912fd
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837317"
---
# <a name="translator-text-api-v2-to-v3-migration"></a>Migração de API de Tradução de Texto v2 para v3

> [!NOTE]
> O v2 foi preterido em 30 de abril de 2018. Migre seus aplicativos para v3 a fim de aproveitar as novas funcionalidades disponíveis exclusivamente na v3.
> 
> O Hub do Microsoft Translator será desativado em 17 de maio de 2019. [Exiba datas e informações importantes de migração](https://www.microsoft.com/translator/business/hub/).  

A equipe do Microsoft Translator lançou a versão 3 (v3) do API de Tradução de Texto. Esta versão inclui novos recursos, métodos preteridos e um novo formato para enviar e receber dados do serviço Microsoft Translator. Este documento fornece informações para alterar aplicativos para usar o v3. 

O fim deste documento contém links úteis para que você saiba mais.

## <a name="summary-of-features"></a>Resumo dos recursos

* Nenhum Trace-in v3 sem rastreamento se aplica a todos os tipos de preço no portal do Azure. Esse recurso significa que nenhum texto enviado para a API v3 será salvo pela Microsoft.
* JSON-XML é substituído por JSON. Todos os dados enviados ao serviço e recebidos do serviço estão no formato JSON.
* Vários idiomas de destino em uma única solicitação – o método translate aceita vários idiomas ' to ' para tradução em uma única solicitação. Por exemplo, uma única solicitação pode ser ' de ' em inglês e ' para ' alemão, espanhol e japonês ou qualquer outro grupo de idiomas.
* Dicionário bilíngüe-um método de dicionário bilíngüe foi adicionado à API. Esse método inclui ' Lookup ' e ' examples '.
* Transliterate-um método transliterate foi adicionado à API. Esse método converterá palavras e frases em um script (por exemplo, Árabe) em outro script (por exemplo, Latino).
* Idiomas – um novo método ' Languages ' fornece informações de idioma, no formato JSON, para uso com os métodos ' translate ', ' Dictionary ' e ' transliterate '.
* Novo para traduzir-novos recursos foram adicionados ao método ' translate ' para dar suporte a alguns dos recursos que estavam na API v2 como métodos separados. Um exemplo é TranslateArray.
* Método de fala – não há mais suporte para a funcionalidade de conversão de texto em fala na API do Microsoft Translator. A funcionalidade de conversão de texto em fala está disponível no [serviço de fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

A lista de métodos V2 e v3 a seguir identifica os métodos v3 e as APIs que fornecerão a funcionalidade fornecida com a v2.

| Método de API v2   | Compatibilidade de API v3 |
|:----------- |:-------------|
| `Translate`     | [Traduzir](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Traduzir](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Idiomas](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Idiomas](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Serviço de fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Serviço de fala da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Ocorre](reference/v3-0-detect.md)         |
| `DetectArray`     | [Ocorre](reference/v3-0-detect.md)         |
| `AddTranslation`     | Não há mais suporte para o recurso       |
| `AddTranslationArray`    | Não há mais suporte para o recurso          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Não há mais suporte para o recurso         |
| `GetTranslationsArray`      | Não há mais suporte para o recurso         |

## <a name="move-to-json-format"></a>Mover para o formato JSON

O Microsoft Tradução de Texto translation v2 aceitou e retornou dados em formato XML. Em v3, todos os dados enviados e recebidos usando a API estão no formato JSON. O XML não será mais aceito ou retornado em v3.

Essa alteração afetará vários aspectos de um aplicativo escrito para a API de tradução de texto v2. Por exemplo: a API de idiomas retorna informações de idioma para tradução de texto, transliteração e os dois métodos de dicionário. Você pode solicitar todas as informações de idioma de todos os métodos em uma chamada ou solicitá-las individualmente.

O método Languages não requer autenticação; ao clicar no link a seguir, você poderá ver todas as informações de idioma para v3 em JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation, dicionário, transliteração](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chave de autenticação

A chave de autenticação que você está usando para a v2 será aceita para v3. Não será necessário obter uma nova assinatura. Você poderá misturar V2 e v3 em seus aplicativos durante o período de migração do yearlong, facilitando a liberação de novas versões enquanto você ainda estiver migrando de v2-XML para v3-JSON.

## <a name="pricing-model"></a>Modelo de Determinação de Preço

O Microsoft Translator v3 é cobrado da mesma forma que a v2 foi cobrada; por caractere, incluindo espaços. Os novos recursos do v3 fazem algumas alterações em quais caracteres são contados para cobrança.

| Método v3   | Caracteres contados para cobrança |
|:----------- |:-------------|
| `Languages`     | Nenhum caractere enviado, nenhum contado, nenhum encargo.          |
| `Translate`     | A contagem é baseada em quantos caracteres são enviados para tradução e em quantos idiomas os caracteres são convertidos. 50 caracteres enviados e 5 idiomas solicitados serão 50x5.           |
| `Transliterate`     | O número de caracteres enviados para transliteração são contados.         |
| `Dictionary lookup & example`     | Número de caracteres enviados para pesquisa de dicionário e exemplos são contados.         |
| `BreakSentence`     | Sem encargos.       |
| `Detect`     | Sem encargos.      |

## <a name="v3-end-points"></a>Pontos de extremidade v3

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Métodos de tradução de texto da API v3

[`Languages`](reference/v3-0-languages.md)

[`Translate`](reference/v3-0-translate.md)

[`Transliterate`](reference/v3-0-transliterate.md)

[`BreakSentence`](reference/v3-0-break-sentence.md)

[`Detect`](reference/v3-0-detect.md)

[`Dictionary/lookup`](reference/v3-0-dictionary-lookup.md)

[`Dictionary/example`](reference/v3-0-dictionary-examples.md)

## <a name="compatibility-and-customization"></a>Compatibilidade e personalização

> [!NOTE]
> 
> O Hub do Microsoft Translator será desativado em 17 de maio de 2019. [Exiba datas e informações importantes de migração](https://www.microsoft.com/translator/business/hub/).   

O Microsoft Translator v3 usa a conversão de máquina neural por padrão. Como tal, ele não pode ser usado com o Hub do Microsoft Translator. O Hub do tradutor dá suporte apenas à tradução da máquina estatística herdada. A personalização para tradução neural agora está disponível usando o tradutor personalizado. [Saiba mais sobre como personalizar a tradução automática da máquina neural](custom-translator/overview.md)

A conversão neural com a API de texto v3 não dá suporte ao uso de categorias padrão (SMT, Speech, Tech, generalnn).

| |Ponto Final|    GDPR conformidade do processador|  Usar Hub do Tradutor| Usar o tradutor personalizado (visualização)|
|:-----|:-----|:-----|:-----|:-----|
|API de Tradução de Texto versão 2| api.microsofttranslator.com|    Não  |Sim    |Não|
|API de Tradução de Texto versão 3| api.cognitive.microsofttranslator.com|  Sim|    Não| Sim|

**API de Tradução de Texto versão 3**
* O está em disponibilidade geral e tem suporte total.
* O é compatível com GDPR como um processador e satisfaz todos os requisitos de certificação ISO 20001 e 20018, bem como o SOC 3. 
* Permite invocar os sistemas de conversão de rede neural personalizados com o tradutor personalizado (versão prévia), o novo tradutor NMT recurso de personalização. 
* Não fornece acesso a sistemas de tradução personalizados criados usando o Hub do Microsoft Translator.

Você está usando a versão 3 do API de Tradução de Texto se estiver usando o ponto de extremidade api.cognitive.microsofttranslator.com.

**API de Tradução de Texto versão 2**
* O não atende a todos os requisitos de certificação ISO 20001, 20018 e SOC 3. 
* Não permite que você invoque os sistemas de conversão de rede neural personalizados com o recurso de personalização do tradutor.
* Fornece acesso a sistemas de tradução personalizados criados usando o Hub do Microsoft Translator.
* Você está usando a versão 2 do API de Tradução de Texto se estiver usando o ponto de extremidade api.microsofttranslator.com.

Nenhuma versão da API do tradutor cria um registro de suas traduções. Suas traduções nunca são compartilhadas com ninguém. Mais informações sobre a página da Web do [tradutor no-Trace](https://www.aka.ms/NoTrace) .

## <a name="links"></a>Ligações

* [Política de privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure informações legais](https://azure.microsoft.com/support/legal)
* [Termos de serviços online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Exibir a documentação do V 3.0](reference/v3-0-reference.md)
