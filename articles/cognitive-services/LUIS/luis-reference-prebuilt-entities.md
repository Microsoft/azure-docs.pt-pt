---
title: Todas as entidades pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas das entidades pré-construídas que estão incluídas na Compreensão da Língua (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a49452653f8ac4bcc62758d801a235be0929d314
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219722"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por cultura no seu modelo LUIS

A Compreensão da Língua (LUIS) fornece entidades pré-construídas. Quando uma entidade pré-construída está incluída na sua aplicação, a LUIS inclui a previsão correspondente da entidade na resposta ao ponto final. Todas as declarações de exemplo também são rotuladas com a entidade. O comportamento de entidades pré-construídas **não pode** ser modificado. Salvo indicação em contrário, as entidades pré-construídas estão disponíveis em todos os locais de aplicação LUIS (culturas). A tabela que se segue mostra as entidades pré-construídas que são apoiadas por cada cultura.

|Cultura|Subculturas|Notas|
|--|--|--|
|Chinês|[zh-CN](#chinese-entity-support)||
|Neerlandês|[nl-NL](#dutch-entity-support)||
|Inglês|[en-EUA (Americano)](#english-american-entity-support)||
|Francês|[fr-CA (Canadá)](#french-canadian-entity-support), [fr-FR (França)](#french-france-entity-support), ||
|Alemão|[de-DE](#german-entity-support)||
|Italiano|[it-IT](#italian-entity-support)||
|Japonês|[ja-JP](#japanese-entity-support)||
|Coreano|[ko-KR](#korean-entity-support)||
|Português|[pt-BR (Brasil)](#portuguese-brazil-entity-support)||
|Espanhol|[es-ES (Espanha)](#spanish-spain-entity-support), [es-MX (México)](#spanish-mexico-entity-support)||
|Turco|[Turco](#turkish-entity-support)|Nenhuma entidade pré-construída apoiada na Turquia|

## <a name="prediction-endpoint-runtime"></a>Tempo de execução do ponto final da previsão

A disponibilidade de uma entidade pré-construída numa língua específica é determinada pela versão de prazo de execução do ponto final de previsão.

## <a name="chinese-entity-support"></a>Apoio à entidade chinesa

São apoiadas as seguintes entidades:

|Entidade pré-construída|```zh-CN``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Apoio à entidade holandesa

São apoiadas as seguintes entidades:

|Entidade pré-construída|```nl-NL``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Data de data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Apoio à entidade inglesa (americana)

São apoiadas as seguintes entidades:

|Entidade pré-construída|```en-US``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Apoio da entidade francesa (França)

São apoiadas as seguintes entidades:

|Entidade pré-construída|```fr-FR``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |   -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Apoio à entidade francesa (canadiana)

São apoiadas as seguintes entidades:

|Entidade pré-construída|```fr-CA``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Apoio da entidade alemã

São apoiadas as seguintes entidades:

|Entidade pré-construída|```de-DE``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Apoio da entidade italiana

Idade pré-construída italiana, moeda, dimensão, número, _resolução_ percentual alterada da pré-visualização V2 e V3.

São apoiadas as seguintes entidades:

|Entidade pré-construída|```it-IT``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Data de data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Apoio à entidade japonesa

São apoiadas as seguintes entidades:

|Entidade pré-construída|```ja-JP``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, -   |
[Data de data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Apoio à entidade coreana

São apoiadas as seguintes entidades:

|Entidade pré-construída|```ko-KR``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    -   |
[Data de data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Apoio da entidade portuguesa (Brasil)

São apoiadas as seguintes entidades:

|Entidade pré-construída|```pt-BR``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Apoio à entidade espanhola (Espanha)

São apoiadas as seguintes entidades:

|Entidade pré-construída|```es-ES``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Apoio à entidade espanhola (México)

São apoiadas as seguintes entidades:

|Entidade pré-construída|```es-MX``` |
------|:------:|
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    -   |
[DataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>datarange<br>hora<br>intervalo de tempo   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informações (ex: bit/byte)<br>comprimento (ex: contador)<br>velocidade (ex: milha por hora)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   |
[Nome de pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Ver notas sobre [entidades pré-construídas deprecatadas](luis-reference-prebuilt-deprecated.md)

KeyPhrase não está disponível em todas as ```pt-BR```subculturas do português (Brasil) - .

## <a name="turkish-entity-support"></a>Apoio da entidade turca

**Não há entidades pré-construídas apoiadas na Turquia.**

<!--

|Prebuilt entity|```tr-tr``` |
------|:------:|
[Age](luis-reference-prebuilt-age.md):<br>year<br>month<br>week<br>day   |    -   |
[Currency (money)](luis-reference-prebuilt-currency.md):<br>dollar<br>fractional unit (ex: penny)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>time<br>timerange   |    -   |
[Dimension](luis-reference-prebuilt-dimension.md):<br>volume<br>area<br>weight<br>information (ex: bit/byte)<br>length (ex: meter)<br>speed (ex: mile per hour)  |    -   |
[Email](luis-reference-prebuilt-email.md)   |    -   |
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[Percentage](luis-reference-prebuilt-percentage.md)   |    -   |
[PersonName](luis-reference-prebuilt-person.md)   |    -   |
[Phonenumber](luis-reference-prebuilt-phonenumber.md)   |    -   |
[Temperature](luis-reference-prebuilt-temperature.md):<br>fahrenheit<br>kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    -   |

See notes on [Deprecated prebuilt entities](luis-reference-prebuilt-deprecated.md)


KeyPhrase is not available.
-->

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuir para culturas de entidades pré-construídas
As entidades pré-construídas são desenvolvidas no projeto Recognisers-Text open-source. [Contribuir](https://github.com/Microsoft/Recognizers-Text) para o projeto. Este projeto inclui exemplos de moeda por cultura.

GeografiaV2 e PersonName não estão incluídos no projeto Recognisers-Text. Para problemas com estas entidades pré-construídas, por favor abra um pedido de [apoio.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Passos seguintes

Conheça o [número](luis-reference-prebuilt-number.md), [dataV2](luis-reference-prebuilt-datetimev2.md), e entidades [cambiais.](luis-reference-prebuilt-currency.md)
