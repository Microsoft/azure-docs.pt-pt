---
title: Todas as entidades predefinidas-LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas das entidades predefinidas que estão incluídas no Reconhecimento vocal (LUIS).
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/03/2019
ms.author: diberry
ms.openlocfilehash: a5fcccc18a453101bd1166f2c85b0c3cdc747c3a
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73464960"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por cultura em seu modelo LUIS

Reconhecimento vocal (LUIS) fornece entidades predefinidas. Quando uma entidade predefinida é incluída em seu aplicativo, o LUIS inclui a previsão de entidade correspondente na resposta do ponto de extremidade. Todos os exemplos de declarações também são rotulados com a entidade. O comportamento de entidades predefinidas **não pode** ser modificado. Salvo indicação em contrário, as entidades predefinidas estão disponíveis em todas as localidades do aplicativo LUIS (culturas). A tabela a seguir mostra as entidades predefinidas que têm suporte para cada cultura.

|Cultura|Subculturas|Notas|
|--|--|--|
|Chinês|[ZH-CN](#chinese-entity-support)||
|Holandês|[NL-NL](#dutch-entity-support)||
|Português|[en-US (American)](#english-american-entity-support)||
|Francês|[fr-CA (Canadá)](#french-canadian-entity-support), [fr-fr (França)](#french-france-entity-support), ||
|Alemão|[de-DE](#german-entity-support)||
|Italiano|[ti-ti](#italian-entity-support)||
|Japonês|[ja-JP](#japanese-entity-support)||
|Coreano|[ko-KR](#korean-entity-support)||
|Português|[pt-BR (Brasil)](#portuguese-brazil-entity-support)||
|Espanhol|[es-es (Espanha)](#spanish-spain-entity-support), [es-MX (México)](#spanish-mexico-entity-support)||
|Turco|[Turco](#turkish-entity-support)|Não há suporte para entidades predefinidas em Turco|

## <a name="prediction-endpoint-runtime"></a>Tempo de execução de ponto de extremidade de previsão

A disponibilidade de uma entidade predefinida em um idioma específico é determinada pela versão de tempo de execução de ponto de extremidade de previsão. 

## <a name="chinese-entity-support"></a>Suporte a entidades do chinês

Há suporte para as seguintes entidades:

|Entidade predefinida|```zh-CN``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    -   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Suporte a entidades holandeses

Há suporte para as seguintes entidades:

|Entidade predefinida|```nl-NL``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Suporte a entidades do inglês (American)

Há suporte para as seguintes entidades:

|Entidade predefinida|```en-US``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    V2, V3   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Suporte a entidades francesas (França)

Há suporte para as seguintes entidades:

|Entidade predefinida|```fr-FR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |   -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Suporte a entidades francesas (canadense)

Há suporte para as seguintes entidades:

|Entidade predefinida|```fr-CA``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Suporte a entidades alemãs

Há suporte para as seguintes entidades:

|Entidade predefinida|```de-DE``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |  
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Suporte à entidade italiana

Idade predefinida italiano, moeda, dimensão, número, _resolução_ de porcentagem alterada da versão de visualização V2 e v3.

Há suporte para as seguintes entidades:

|Entidade predefinida|```it-IT``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Suporte a entidades japonesas

Há suporte para as seguintes entidades:

|Entidade predefinida|```ja-JP``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2,-   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2,-   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2,-   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2,-   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2,-   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2,-   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2,-   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Suporte a entidades coreanas

Há suporte para as seguintes entidades:

|Entidade predefinida|```ko-KR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    -   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    -   |
[Horário](luis-reference-prebuilt-deprecated.md)   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    -   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Suporte a entidades do Português (Brasil)

Há suporte para as seguintes entidades:

|Entidade predefinida|```pt-BR``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Suporte a entidades do espanhol (Espanha)

Há suporte para as seguintes entidades:

|Entidade predefinida|```es-ES``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    V2, V3   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    V2, V3   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    V2, V3   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    V2, V3   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    V2, V3   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Suporte a entidades espanhol (México)

Há suporte para as seguintes entidades:

|Entidade predefinida|```es-MX``` |
------|:------:|
[Idade](luis-reference-prebuilt-age.md):<br>ano<br>mês<br>Emana<br>diário   |    -   |
[Moeda (Money)](luis-reference-prebuilt-currency.md):<br>mina<br>unidade fracionária (por exemplo: centavos)  |    -   |
[DatetimeV2](luis-reference-prebuilt-datetimev2.md):<br>date<br>daterange<br>hora<br>timerange   |    -   | 
[Dimensão](luis-reference-prebuilt-dimension.md):<br>volume<br>Rede<br>largura<br>informações (por exemplo: bit/byte)<br>comprimento (por exemplo: medidor)<br>velocidade (por exemplo: milhar por hora)  |    -   | 
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   | 
[GeographyV2](luis-reference-prebuilt-geographyV2.md)   |    -   | 
[KeyPhrase](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   | 
[Automática](luis-reference-prebuilt-number.md)   |    V2, V3   |  
[Numera](luis-reference-prebuilt-ordinal.md)   |    -   |  
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Porcentagem](luis-reference-prebuilt-percentage.md)   |    -   | 
[PersonName](luis-reference-prebuilt-person.md)   |    -   | 
[PhoneNumber](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   | 
[Temperatura](luis-reference-prebuilt-temperature.md):<br>Fahrenheit<br>Kelvin<br>rankine<br>delisle<br>Celsius   |    -   | 
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Veja observações sobre [entidades predefinidas preteridas](luis-reference-prebuilt-deprecated.md)

KeyPhrase não está disponível em todas as subculturas de Português (Brasil)-```pt-BR```.

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

## <a name="contribute-to-prebuilt-entity-cultures"></a>Contribuir para culturas de entidade predefinidas
As entidades predefinidas são desenvolvidas no projeto de código-fonte aberto de reconhecedores-texto. [Contribuir](https://github.com/Microsoft/Recognizers-Text) para o projeto. Este projeto inclui exemplos de moeda por cultura. 

GeographyV2 e PersonName não estão incluídos no projeto de texto Recognizers. Para problemas com essas entidades predefinidas, abra uma [solicitação de suporte](../../azure-supportability/how-to-create-azure-support-request.md). 

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre as entidades [número](luis-reference-prebuilt-number.md), [datetimeV2](luis-reference-prebuilt-datetimev2.md)e [moeda](luis-reference-prebuilt-currency.md) . 
