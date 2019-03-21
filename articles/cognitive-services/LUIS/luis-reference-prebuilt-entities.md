---
title: Todas as entidades pré-concebidas
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas de entidades pré-criadas que estão incluídas na compreensão de idiomas (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 03/12/2019
ms.author: diberry
ms.openlocfilehash: fdb105fa5aa9baefc9e64b65c275f07db802daad
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58091703"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por cultura no seu modelo do LUIS

Compreensão de idiomas (LUIS) fornece entidades pré-concebidas. Quando uma entidade pré-criados está incluída na sua aplicação, o LUIS inclui a predição de entidade correspondente na resposta do ponto final. Todas as expressões de exemplo também são rotuladas com a entidade. O comportamento das entidades pré-concebidas **não é possível** ser modificado. Salvo indicação em contrário, entidades pré-concebidas estão disponíveis em todas as localidades de aplicação LUIS (culturas). A tabela seguinte mostra as entidades pré-concebidas que são suportadas para cada cultura.

|Cultura|Subcultures|
|--|--|
|Chinês|[zh-CN](#chinese-entity-support)|
|Neerlandês|[NL-NL](#dutch-entity-support)|
|Português|[INGLÊS (americano)](#english-american-entity-support)|
|Francês|[(Canadá) FR-AC](#french-canadian-entity-support), [fr-FR (França)](#french-france-entity-support), |
|Alemão|[de-DE](#german-entity-support)|
|Italiano|[it-IT](#italian-entity-support)|
|Japonês|[ja-JP](#japanese-entity-support)|
|Coreano|[ko-KR](#korean-entity-support)|
|Português|[pt-BR (Brasil)](#portuguese-brazil-entity-support)|
|Espanhol|[(Espanha) de es-ES](#spanish-spain-entity-support), [es-MX (México)](#spanish-mexico-entity-support)|
|Turco|[turkish](#turkish-entity-support)|

## <a name="chinese-entity-support"></a>Suporte de entidade chinês

As entidades a seguir são suportadas:

|Entidade pré-criados|```zh-CN``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="dutch-entity-support"></a>Suporte de entidade Holandês

As entidades a seguir são suportadas:

|Entidade pré-criados|```nl-NL``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="english-american-entity-support"></a>Suporte de entidade (americano) em inglês

As entidades a seguir são suportadas:

|Entidade pré-criados|```en-US``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-france-entity-support"></a>Suporte de entidade do francês (França)

As entidades a seguir são suportadas:

|Entidade pré-criados|```fr-FR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    ✔   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    ✔   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="french-canadian-entity-support"></a>Suporte de francês entidade (canadense)

As entidades a seguir são suportadas:

|Entidade pré-criados|```fr-CA``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="german-entity-support"></a>Suporte de entidade alemão

As entidades a seguir são suportadas:

|Entidade pré-criados|```de-DE``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="italian-entity-support"></a>Suporte de entidade italiano

As entidades a seguir são suportadas:

|Entidade pré-criados|```it-IT``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="japanese-entity-support"></a>Suporte de entidade japonês

As entidades a seguir são suportadas:

|Entidade pré-criados|```ja-JP``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="korean-entity-support"></a>Suporte de entidade coreano

As entidades a seguir são suportadas:

|Entidade pré-criados|```ko-KR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="portuguese-brazil-entity-support"></a>Suporte de entidade de Português (Brasil)

As entidades a seguir são suportadas:

|Entidade pré-criados|```pt-BR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-spain-entity-support"></a>Suporte de entidade de espanhol (Espanha)

As entidades a seguir são suportadas:

|Entidade pré-criados|```es-ES``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    ✔   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    ✔   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    ✔   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    ✔   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    ✔   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    ✔   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    ✔   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

## <a name="spanish-mexico-entity-support"></a>Suporte de entidade de espanhol (México)

As entidades a seguir são suportadas:

|Entidade pré-criados|```es-MX``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    ✔   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    ✔   | 
[Número](luis-reference-prebuilt-number.md)   |    ✔   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    ✔   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    ✔   |

Consulte as notas sobre [preterido entidades pré-concebidas](luis-reference-prebuilt-deprecated.md)

KeyPhrase não está disponível em todos os subcultures de Português (Brasil) - ```pt-BR```.

## <a name="turkish-entity-support"></a>Suporte de entidade turco

As entidades a seguir são suportadas:

|Entidade pré-criados|```tr-tr``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>semana<br>dia   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>Dólar<br>unidade fracionária (ex: mínima)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>área<br>peso<br>informações (ex: bits/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: lá por hora)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    -   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Número](luis-reference-prebuilt-number.md)   |    -   |  
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |  
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    -   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>Rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    -   |

Consulte as notas sobre [preterido entidades pré-concebidas](luis-reference-prebuilt-deprecated.md)

KeyPhrase não está disponível.


## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuir para culturas de entidade predefinidos
As entidades pré-concebidas são desenvolvidas no projeto de código-fonte aberto reconhecedores-texto. [Contribuir](https://github.com/Microsoft/Recognizers-Text) ao projeto. Esse projeto inclui exemplos de moeda por cultura. 

GeographyV2 e PersonName não estão incluídos no projeto reconhecedores-texto. Para problemas com estas entidades previamente concebidas, abra um [pedido de suporte](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre o [número](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md), e [moeda](luis-reference-prebuilt-currency.md) entidades. 
