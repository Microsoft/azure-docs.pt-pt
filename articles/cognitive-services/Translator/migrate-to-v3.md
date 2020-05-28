---
title: Migrar para V3 - Tradutor
titleSuffix: Azure Cognitive Services
description: Este artigo fornece os passos para ajudá-lo a migrar de V2 para V3 do Tradutor de Serviços Cognitivos Azure.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 05/26/2020
ms.author: swmachan
ms.openlocfilehash: 8fae863c03ccbc17e9ec6621e73ddf475f759569
ms.sourcegitcommit: fc718cc1078594819e8ed640b6ee4bef39e91f7f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/27/2020
ms.locfileid: "83996215"
---
# <a name="translator-v2-to-v3-migration"></a>Tradutor V2 para Migração V3

> [!NOTE]
> A V2 foi depreciada a 30 de abril de 2018. Por favor, emigre as suas aplicações para v3 de forma a tirar partido das novas funcionalidades disponíveis exclusivamente em V3.
> 
> O Microsoft Tradutor Hub será reformado a 17 de maio de 2019. [Ver informações e datas importantes da migração.](https://www.microsoft.com/translator/business/hub/)  

A equipa do Microsoft Tradutor lançou a Versão 3 (V3) do Tradutor. Esta versão inclui novas funcionalidades, métodos depreciados e um novo formato para envio e receção de dados do Microsoft Tradutor Service. Este documento fornece informações para alterar aplicações para utilizar o V3. 

O final deste documento contém links úteis para que possa saber mais.

## <a name="summary-of-features"></a>Resumo de características

* No Trace - Em V3 No-Trace aplica-se a todos os níveis de preços no portal Azure. Esta funcionalidade significa que nenhum texto submetido à API V3 será guardado pela Microsoft.
* JSON - XML é substituído pela JSON. Todos os dados enviados para o serviço e recebidos do serviço estão em formato JSON.
* Várias línguas-alvo num único pedido - O método Translate aceita várias línguas 'to' para tradução num único pedido. Por exemplo, um único pedido pode ser "do" inglês e "para" alemão, espanhol e japonês, ou qualquer outro grupo de línguas.
* Dicionário bilingue - Um método bilingue do dicionário foi adicionado à API. Este método inclui "olhar" e "exemplos".
* Transliterado - Foi adicionado um método transliterado à API. Este método converterá palavras e frases num só guião (por exemplo. Árabe) em outro script (E.g. Latim, latim.
* Línguas - Um novo método de "línguas" fornece informação linguística, em formato JSON, para utilização com os métodos 'traduzir', 'dicionário' e 'transliterado'.
* Novo para Traduzir - Novas capacidades foram adicionadas ao método 'traduzir' para suportar algumas das funcionalidades que estavam na API V2 como métodos separados. Um exemplo é TranslateArray.
* Método de fala - A funcionalidade de texto para a fala já não é suportada no Tradutor da Microsoft. A funcionalidade de texto para fala está disponível no [Microsoft Speech Service](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech).

A seguinte lista de métodos V2 e V3 identifica os métodos V3 e APIs que fornecerão a funcionalidade que veio com V2.

| Método V2 API   | Compatibilidade V3 API |
|:----------- |:-------------|
| `Translate`     | [Traduzir](reference/v3-0-translate.md)          |
| `TranslateArray`      | [Traduzir](reference/v3-0-translate.md)        |
| `GetLanguageNames`      | [Linguagens](reference/v3-0-languages.md)         |
| `GetLanguagesForTranslate`     | [Linguagens](reference/v3-0-languages.md)       |
| `GetLanguagesForSpeak`      | [Serviço de Discurso da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#text-to-speech)         |
| `Speak`     | [Serviço de Discurso da Microsoft](https://docs.microsoft.com/azure/cognitive-services/speech-service/text-to-speech)          |
| `Detect`     | [Detetar](reference/v3-0-detect.md)         |
| `DetectArray`     | [Detetar](reference/v3-0-detect.md)         |
| `AddTranslation`     | A funcionalidade já não é suportada       |
| `AddTranslationArray`    | A funcionalidade já não é suportada          |
| `BreakSentences`      | [BreakSentence](reference/v3-0-break-sentence.md)       |
| `GetTranslations`      | A funcionalidade já não é suportada         |
| `GetTranslationsArray`      | A funcionalidade já não é suportada         |

## <a name="move-to-json-format"></a>Mude para o formato JSON

Microsoft Tradutor Tradução V2 aceitou e devolveu dados em formato XML. No V3 todos os dados enviados e recebidos utilizando a API estão em formato JSON. O XML deixará de ser aceite ou devolvido em V3.

Esta alteração afetará vários aspetos de uma aplicação escrita para a API de Tradução de Texto V2. Como exemplo: A API das Línguas devolve informações linguísticas para tradução de texto, transliteração e os dois métodos dicionários. Pode solicitar todas as informações linguísticas para todos os métodos numa chamada ou solicitá-las individualmente.

O método das línguas não requer autenticação; clicando no seguinte link, pode ver todas as informações linguísticas para V3 em JSON:

[https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dicionário,transliteração](https://api.cognitive.microsofttranslator.com/languages?api-version=3.0&scope=translation,dictionary,transliteration)

## <a name="authentication-key"></a>Chave de Autenticação

A chave de autenticação que está a usar para V2 será aceite para V3. Não precisará de obter uma nova subscrição. Poderá misturar V2 e V3 nas suas apps durante o período de migração durante o ano, facilitando a sua divulgação de novas versões enquanto ainda está a migrar de V2-XML para V3-JSON.

## <a name="pricing-model"></a>Modelo de Preços

O Microsoft Tradutor V3 tem um preço da mesma forma que o V2 foi avaliado; por personagem, incluindo espaços. As novas funcionalidades em V3 fazem algumas alterações no que os caracteres são contados para faturação.

| Método V3   | Caracteres contados para faturação |
|:----------- |:-------------|
| `Languages`     | Sem caracteres apresentados, nenhum contado, nenhum custo.          |
| `Translate`     | O Conde baseia-se em quantos caracteres são submetidos para tradução, e em quantas línguas os personagens são traduzidos. 50 caracteres submetidos, e 5 línguas solicitadas serão 50x5.           |
| `Transliterate`     | O número de caracteres submetidos à transliteração é contado.         |
| `Dictionary lookup & example`     | Número de caracteres submetidos para a procura do Dicionário e exemplos são contados.         |
| `BreakSentence`     | Sem custo.       |
| `Detect`     | Sem custo.      |

## <a name="v3-end-points"></a>V3 Pontos finais

Global

* api.cognitive.microsofttranslator.com

## <a name="v3-api-text-translations-methods"></a>Métodos v3 de tradução de texto DaPi

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
> O Microsoft Tradutor Hub será reformado a 17 de maio de 2019. [Ver informações e datas importantes da migração.](https://www.microsoft.com/translator/business/hub/)   

O Microsoft Tradutor V3 utiliza a tradução da máquina neural por padrão. Como tal, não pode ser utilizado com o Microsoft Tradutor Hub. O Tradutor Hub apenas suporta a tradução de máquinas estatísticas antigas. A personalização para tradução neural está agora disponível usando o Tradutor Personalizado. [Saiba mais sobre personalizar a tradução da máquina neural](custom-translator/overview.md)

A tradução neural com o texto V3 API não suporta a utilização de categorias padrão (SMT, fala, tecnologia, generalnn).

| |Ponto Final|    Conformidade do processador do RGPD|    Use hub tradutor|    Utilizar tradutor personalizado (pré-visualização)|
|:-----|:-----|:-----|:-----|:-----|
|Versão tradutora 2|    api.microsofttranslator.com|    Não    |Sim    |Não|
|Versão tradutora 3|    api.cognitive.microsofttranslator.com|    Sim|    Não|    Sim|

**Versão tradutora 3**
* É geralmente disponível e totalmente apoiado.
* O RGPD é conforme como processador e satisfaz todos os requisitos de certificação ISO 20001 e 20018, bem como os requisitos de certificação SOC 3. 
* Permite-lhe invocar os sistemas de tradução de rede neural que personalizou com tradutor personalizado (Pré-visualização), a nova funcionalidade de personalização De MMT tradutor. 
* Não fornece acesso a sistemas de tradução personalizados criados usando o Microsoft Tradutor Hub.

Está a utilizar a versão 3 do Tradutor se estiver a utilizar o ponto final api.cognitive.microsofttranslator.com.

**Versão tradutora 2**
* Não satisfaz todos os requisitos de certificação ISO 20001.20018 e SOC 3. 
* Não lhe permite invocar os sistemas de tradução de rede neural que personalizou com a funcionalidade de personalização do Tradutor.
* Fornece acesso a sistemas de tradução personalizados criados usando o Microsoft Tradutor Hub.
* Está a utilizar a versão 2 do Tradutor se estiver a utilizar o ponto final api.microsofttranslator.com.

Nenhuma versão do Tradutor cria um registo das suas traduções. As tuas traduções nunca são partilhadas com ninguém. Mais informações na página web do [Tradutor No-Trace.](https://www.aka.ms/NoTrace)

## <a name="links"></a>Ligações

* [Política de Privacidade da Microsoft](https://privacy.microsoft.com/privacystatement)
* [Informações Legais do Microsoft Azure](https://azure.microsoft.com/support/legal)
* [Termos de Serviços Online](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)

## <a name="next-steps"></a>Próximos passos

> [!div class="nextstepaction"]
> [Ver V3.0 Documentação](reference/v3-0-reference.md)
