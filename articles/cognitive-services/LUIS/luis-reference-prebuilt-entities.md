---
title: Todas as entidades predefinidas-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas de entidades pré-criadas que estão incluídas na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: 254fec23ef34b936405439e0334e24e594a24dc4
ms.sourcegitcommit: 8e9a6972196c5a752e9a0d021b715ca3b20a928f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/11/2020
ms.locfileid: "75890238"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por cultura em seu modelo LUIS

Compreensão de idiomas (LUIS) fornece entidades pré-concebidas. Quando uma entidade pré-criados está incluída na sua aplicação, o LUIS inclui a predição de entidade correspondente na resposta do ponto final. Todas as expressões de exemplo também são rotuladas com a entidade. O comportamento das entidades pré-concebidas **não é possível** ser modificado. Salvo indicação em contrário, entidades pré-concebidas estão disponíveis em todas as localidades de aplicação LUIS (culturas). A tabela seguinte mostra as entidades pré-concebidas que são suportadas para cada cultura.

|Cultura|Subcultures|Notas|
|--|--|--|
|Chinês|[zh-CN](#chinese-entity-support)||
|Neerlandês|[NL-NL](#dutch-entity-support)||
|Português|[INGLÊS (americano)](#english-american-entity-support)||
|Francês|[(Canadá) FR-AC](#french-canadian-entity-support), [fr-FR (França)](#french-france-entity-support), ||
|Alemão|[de-DE](#german-entity-support)||
|Italiano|[it-IT](#italian-entity-support)||
|Japonês|[ja-JP](#japanese-entity-support)||
|Coreano|[ko-KR](#korean-entity-support)||
|Português|[pt-BR (Brasil)](#portuguese-brazil-entity-support)||
|Espanhol|[(Espanha) de es-ES](#spanish-spain-entity-support), [es-MX (México)](#spanish-mexico-entity-support)||
|Turco|[turkish](#turkish-entity-support)|Não há suporte para entidades predefinidas em Turco|

## <a name="prediction-endpoint-runtime"></a>Tempo de execução de ponto de extremidade de previsão

A disponibilidade de uma entidade predefinida em um idioma específico é determinada pela versão de tempo de execução de ponto de extremidade de previsão. 

## <a name="chinese-entity-support"></a>Suporte de entidade chinês

As entidades a seguir são suportadas:

|Entidade pré-criados|```zh-CN``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Suporte de entidade Holandês

As entidades a seguir são suportadas:

|Entidade pré-criados|```nl-NL``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Suporte de entidade (americano) em inglês

As entidades a seguir são suportadas:

|Entidade pré-criados|```en-US``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Suporte de entidade do francês (França)

As entidades a seguir são suportadas:

|Entidade pré-criados|```fr-FR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Suporte de francês entidade (canadense)

As entidades a seguir são suportadas:

|Entidade pré-criados|```fr-CA``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Suporte de entidade alemão

As entidades a seguir são suportadas:

|Entidade pré-criados|```de-DE``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Suporte de entidade italiano

Idade predefinida italiano, moeda, dimensão, número, _resolução_ de porcentagem alterada da versão de visualização V2 e v3.

As entidades a seguir são suportadas:

|Entidade pré-criados|```it-IT``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Suporte de entidade japonês

As entidades a seguir são suportadas:

|Entidade pré-criados|```ja-JP``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2,-   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2,-   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2,-   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2,-   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2,-   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2,-   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2,-   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Suporte de entidade coreano

As entidades a seguir são suportadas:

|Entidade pré-criados|```ko-KR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    -   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Suporte de entidade de Português (Brasil)

As entidades a seguir são suportadas:

|Entidade pré-criados|```pt-BR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Suporte de entidade de espanhol (Espanha)

As entidades a seguir são suportadas:

|Entidade pré-criados|```es-ES``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Suporte de entidade de espanhol (México)

As entidades a seguir são suportadas:

|Entidade pré-criados|```es-MX``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Número](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Consulte as notas sobre [preterido entidades pré-concebidas](luis-reference-prebuilt-deprecated.md)

KeyPhrase não está disponível em todos os subcultures de Português (Brasil) - ```pt-BR```.

## <a name="turkish-entity-support"></a>Suporte à entidade Turco

**Não há entidades predefinidas com suporte em Turco.** 

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

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuir para culturas de entidade predefinidos
As entidades pré-concebidas são desenvolvidas no projeto de código-fonte aberto reconhecedores-texto. [Contribuir](https://github.com/Microsoft/Recognizers-Text) ao projeto. Esse projeto inclui exemplos de moeda por cultura. 

GeographyV2 e PersonName não estão incluídos no projeto reconhecedores-texto. Para problemas com estas entidades previamente concebidas, abra um [pedido de suporte](../../azure-portal/supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [número](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), e [moeda](luis-reference-prebuilt-currency.md) entidades. 
