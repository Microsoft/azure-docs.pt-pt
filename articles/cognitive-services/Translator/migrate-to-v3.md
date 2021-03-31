---
title: Migrar para V3 - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo fornece os passos para ajudá-lo a migrar de V2 para V3 do Azure Cognitive Services Tradutor.
services: cognitive-services
author: laujan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: lajanuar
ms.openlocfilehash: 13c4d39284fad293c945f8b7e31076dccee84fda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "98896838"
---
# <a name="translator-v2-to-v3-migration"></a>Tradução V2 para V3 Migração

> [!NOTE]
> A V2 foi depreciada a 30 de abril de 2018. Por favor, emordene as suas aplicações para v3 de forma a tirar partido das novas funcionalidades disponíveis exclusivamente na V3. A V2 será reformada no dia 24 de maio de 2021. 

A equipa da Microsoft Tradutor lançou a Versão 3 (V3) do Tradutor. Esta versão inclui novas funcionalidades, métodos preprecados e um novo formato para envio e receção de dados do Serviço de Tradutores da Microsoft. Este documento fornece informações para alterar aplicações para utilizar v3. 

O final deste documento contém links úteis para que possa saber mais.

## <a name="summary-of-features"></a>Resumo das características

* No Trace - Em V3 No-Trace aplica-se a todos os níveis de preços no portal Azure. Esta funcionalidade significa que nenhum texto submetido à API V3, será guardado pela Microsoft.
* JSON - XML é substituído por JSON. Todos os dados enviados para o serviço e recebidos do serviço estão em formato JSON.
* Várias línguas-alvo num único pedido - O método Translate aceita várias línguas 'a' para tradução num único pedido. Por exemplo, um único pedido pode ser 'de' inglês e 'para' alemão, espanhol e japonês, ou qualquer outro grupo de línguas.
* Dicionário bilingue - Um método bilingue de dicionário foi adicionado à API. Este método inclui "procura" e "exemplos".
* Transliterado - Foi adicionado um método transliterado à API. Este método converterá palavras e frases num só roteiro (por exemplo. Árabe) em outro script (por exemplo. Latim).
* Línguas - Um novo método de "línguas" fornece informações linguísticas, em formato JSON, para utilização com os métodos de "tradução", "dicionário" e "transliterado".
* Novo para traduzir - Foram adicionadas novas capacidades ao método de 'traduzir' para suportar algumas das funcionalidades que estavam na API V2 como métodos separados. Um exemplo é TranslateArray.
* Método de fala - A funcionalidade de texto para fala já não é suportada no Tradutor da Microsoft. A funcionalidade de texto para fala está disponível no [Microsoft Speech Service](../speech-service/text-to-speech.md).

A seguinte lista de métodos V2 e V3 identifica os métodos V3 e APIs que fornecerão a funcionalidade que veio com V2.

| Método API V2   | Compatibilidade da V3 API |
|:----------- |:-------------|
| `Translate`     | [Traduzir](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Traduzir](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Idiomas](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Idiomas](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Serviço de Fala da Microsoft](../speech-service/language-support.md#text-to-speech)         |
| `Speak`     | [Serviço de Fala da Microsoft](../speech-service/text-to-speech.md)          |
| `Detect`     | [Detetar](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detetar](reference/v3-0-detect.md)         |
| `AddTranslation`     | Recurso já não é suportado       |
| `AddTranslationArray`    | Recurso já não é suportado          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | Recurso já não é suportado         |
| `GetTranslationsArray`      | Recurso já não é suportado         |

## <a name="move-to-json-format"></a>Mude para o formato JSON

Tradução do Tradutor microsoft V2 aceitou e devolveu dados em formato XML. Na V3 todos os dados enviados e recebidos utilizando a API estão em formato JSON. O XML deixará de ser aceite ou devolvido em V3.

Esta alteração afetará vários aspetos de uma aplicação escrita para a API de Tradução de Texto V2. Como exemplo: A API das Línguas devolve informações linguísticas para tradução de texto, transliteração e dois métodos de dicionário. Pode solicitar todas as informações linguísticas para todos os métodos numa chamada ou solicitá-las individualmente.

O método das línguas não requer autenticação; clicando no seguinte link, pode ver todas as informações linguísticas para V3 em JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translationdicionário,transliteração](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chave de Autenticação

A chave de autenticação que está a utilizar para v2 será aceite para v3. Não precisará de uma nova subscrição. Poderá misturar V2 e V3 nas suas apps durante o período de migração do ano, facilitando o lançamento de novas versões enquanto ainda está a migrar de V2-XML para V3-JSON.

## <a name="pricing-model"></a>Modelo de preços

O Microsoft Tradutor V3 tem o mesmo preço que o V2; por caráter, incluindo espaços. As novas funcionalidades na V3 fazem algumas alterações no que os caracteres são contados para a faturação.

| Método V3   | Personagens contados para faturação |
|:----------- |:-------------|
| `Languages`     | Nenhum personagem submetido, nenhum contado, sem custo.          |
| `Translate`     | O conde baseia-se em quantos caracteres são submetidos para tradução, e em quantas línguas os caracteres são traduzidos. 50 caracteres submetidos, e 5 idiomas solicitados serão 50x5.           |
| `Transliterate`     | O número de caracteres submetidos para transliteração é contado.         |
| `Dictionary lookup & example`     | O número de caracteres submetidos para a procura do Dicionário e exemplos são contados.         |
| `BreakSentence`     | Sem acusação.       |
| `Detect`     | Sem acusação.      |

## <a name="v3-end-points"></a>Pontos finais V3

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Métodos de tradução de texto V3 API

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
> O Microsoft Tradutor Hub será reformado no dia 17 de maio de 2019. [Consulte informações e datas de migração importantes.](https://www.microsoft.com/translator/business/hub/)   

O Microsoft Tradutor V3 utiliza a tradução da máquina neural por predefinição. Como tal, não pode ser utilizado com o Microsoft Tradutor Hub. O Centro tradutor só suporta a tradução de máquinas estatísticas antigas. A personalização para tradução neural está agora disponível através do Tradutor Personalizado. [Saiba mais sobre a personalização da tradução de máquinas neurais](custom-translator/overview.md)

A tradução neural com o texto V3 API não suporta a utilização de categorias padrão (SMT, fala, tecnologia, generalnn).

| Versão | Ponto final | Conformidade do processador do RGPD | Use Centro de Tradutor | Use tradutor personalizado (pré-visualização) |
| :------ | :------- | :------------------------ | :----------------- | :------------------------------ |
|Tradução Versão 2|    api.microsofttranslator.com|    No    |Yes    |No|
|Tradução Versão 3|    api.cognitive.microsofttranslator.com|    Yes|    No|    Yes|

**Tradução Versão 3**
* Geralmente está disponível e totalmente suportado.
* O RGPD está em conformidade com o RGPD como processador e satisfaz todos os requisitos de certificação ISO 20001 e 20018, bem como os requisitos de certificação SOC 3. 
* Permite-lhe invocar os sistemas de tradução da rede neural que personalizou com o Custom Tradutor (Preview), a nova funcionalidade de personalização do Tradutor NMT. 
* Não fornece acesso a sistemas de tradução personalizados criados através do Microsoft Tradutor Hub.

Está a utilizar a versão 3 do Tradutor se estiver a utilizar o api.cognitive.microsofttranslator.com ponto final.

**Tradução Versão 2**
* Não satisfaz todos os requisitos de certificação ISO 20001,20018 e SOC 3. 
* Não lhe permite invocar os sistemas de tradução da rede neural que personalizou com a funcionalidade de personalização do Tradutor.
* Fornece acesso a sistemas de tradução personalizados criados usando o Microsoft Tradutor Hub.
* Está a utilizar a versão 2 do Tradutor se estiver a utilizar o api.microsofttranslator.com ponto final.

Nenhuma versão do Tradutor cria um registo das suas traduções. As suas traduções nunca são partilhadas com ninguém. Mais informações na página do [Tradutor No-Trace.](https://www.aka.ms/NoTrace)

## <a name="links"></a>Ligações

* [Política de Privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)
* [Microsoft Azure Informação Jurídica](https://azure.microsoft.com/support/legal)
* [Termos dos Serviços Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Ver Documentação V3.0](reference/v3-0-reference.md)
