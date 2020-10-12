---
title: Todas as entidades pré-construídas - LUIS
titleSuffix: Azure Cognitive Services
description: Este artigo contém listas das entidades pré-construídas que estão incluídas no Compreensão de Línguas (LUIS).
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 07/20/2020
ms.openlocfilehash: 7a4654b8f389ad18010bb4ad211103f2816a18ee
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91542007"
---
# <a name="entities-per-culture-in-your-luis-model"></a>Entidades por cultura no seu modelo LUIS

A Compreensão linguística (LUIS) fornece entidades pré-construídas.

## <a name="entity-resolution"></a>Resolução de entidades
Quando uma entidade pré-construída é incluída na sua aplicação, a LUIS inclui a resolução da entidade correspondente na resposta ao ponto final. Todas as expressões de exemplo também são rotuladas com a entidade.

O comportamento das entidades pré-construídas não pode ser modificado, mas pode melhorar a resolução [adicionando a entidade pré-construída como característica a uma entidade ou entidade de aprendizagem automática.](luis-concept-entity-types.md#effective-prebuilt-entities)

## <a name="availability"></a>Disponibilidade
Salvo indicação em contrário, as entidades pré-construídas estão disponíveis em todos os locais de aplicação LUIS (culturas). A tabela a seguir mostra as entidades pré-construídas que são apoiadas para cada cultura.

|Cultura|Subculturas|Notas|
|--|--|--|
|Chinês|[zh-CN](#chinese-entity-support)||
|Neerlandês|[nl-NL](#dutch-entity-support)||
|Inglês|[en-US (Americano)](#english-american-entity-support)||
|Francês|[fr-CA (Canadá)](#french-canadian-entity-support), [fr-FR (França)](#french-france-entity-support), ||
|Alemão|[de-DE](#german-entity-support)||
|Italiano|[it-IT](#italian-entity-support)||
|Japonês|[ja-JP](#japanese-entity-support)||
|Coreano|[ko-KR](#korean-entity-support)||
|Português|[pt-BR (Brasil)](#portuguese-brazil-entity-support)||
|Espanhol|[es-ES (Espanha)](#spanish-spain-entity-support), [es-MX (México)](#spanish-mexico-entity-support)||
|Turco|[Turco](#turkish-entity-support)|Nenhuma entidade pré-construída apoiada em turco|

## <a name="prediction-endpoint-runtime"></a>Tempo de execução do ponto final de previsão

A disponibilidade de uma entidade pré-incorporada numa língua específica é determinada pela versão de prazo de execução do ponto final de previsão.

## <a name="chinese-entity-support"></a>Apoio à entidade chinesa

São apoiadas as seguintes entidades:

| Entidade pré-construída | zh-CN |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    -   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="dutch-entity-support"></a>Apoio a entidades neerlandeses

São apoiadas as seguintes entidades:

| Entidade pré-construída | nl-NL |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="english-american-entity-support"></a>Suporte de entidade inglesa (americana)

São apoiadas as seguintes entidades:

| Entidade pré-construída | pt-PT |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    V2, V3   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    V2, V3   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    V2, V3   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-france-entity-support"></a>Apoio à entidade francesa (França)

São apoiadas as seguintes entidades:

| Entidade pré-construída | fr-FR |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |   -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="french-canadian-entity-support"></a>Apoio à entidade francesa (canadiana)

São apoiadas as seguintes entidades:

| Entidade pré-construída | fr-CA |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="german-entity-support"></a>Apoio a entidades alemãs

São apoiadas as seguintes entidades:

|Entidade pré-construída | de-DE |
| -------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="italian-entity-support"></a>Apoio à entidade italiana

Idade pré-construída italiana, moeda, dimensão, número, _resolução_ percentual alterada da pré-visualização V2 e V3.

São apoiadas as seguintes entidades:

| Entidade pré-construída | it-IT |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="japanese-entity-support"></a>Suporte de entidade japonesa

São apoiadas as seguintes entidades:

|Entidade pré-construída | ja-JP |
| -------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, -   |
[Hora da data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, -   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="korean-entity-support"></a>Apoio a entidades coreanas

São apoiadas as seguintes entidades:

| Entidade pré-construída | ko-KR |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    -   |
[Hora da data](luis-reference-prebuilt-deprecated.md)   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    -   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="portuguese-brazil-entity-support"></a>Apoio à entidade portuguesa (Brasil)

São apoiadas as seguintes entidades:

| Entidade pré-construída | pt-BR |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-spain-entity-support"></a>Apoio à entidade espanhola (Espanha)

São apoiadas as seguintes entidades:

| Entidade pré-construída | es-ES |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    V2, V3   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    V2, V3   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    V2, V3   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    V2, V3   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    V2, V3   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    V2, V3   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    V2, V3   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

## <a name="spanish-mexico-entity-support"></a>Apoio à entidade espanhola (México)

São apoiadas as seguintes entidades:

| Entidade pré-construída | es-MX |
| --------------- | :---: |
[Idade:](luis-reference-prebuilt-age.md)<br>ano<br>month<br>semana<br>day   |    -   |
[Moeda (dinheiro)](luis-reference-prebuilt-currency.md):<br>dólar<br>unidade fracionada (ex: penny)  |    -   |
[Hora da dataV2:](luis-reference-prebuilt-datetimev2.md)<br>date<br>encontro<br>hora<br>timerange   |    -   |
[Dimensão:](luis-reference-prebuilt-dimension.md)<br>volume<br>área<br>peso<br>informação (ex: bit/byte)<br>comprimento (ex: medidor)<br>velocidade (ex: milha por hora)  |    -   |
[E-mail](luis-reference-prebuilt-email.md)   |    V2, V3   |
[GeografiaV2](luis-reference-prebuilt-geographyV2.md)   |    -   |
[Frase-chave](luis-reference-prebuilt-keyphrase.md)   |    V2, V3   |
[Number](luis-reference-prebuilt-number.md)   |    V2, V3   |
[Ordinal](luis-reference-prebuilt-ordinal.md)   |    -   |
[OrdinalV2](luis-reference-prebuilt-ordinal-v2.md)   |    -   |
[Percentagem](luis-reference-prebuilt-percentage.md)   |    -   |
[Nome da pessoa](luis-reference-prebuilt-person.md)   |    -   |
[Número de telefone](luis-reference-prebuilt-phonenumber.md)   |    V2, V3   |
[Temperatura:](luis-reference-prebuilt-temperature.md)<br>fahrenheit<br>Kelvin<br>rankine<br>delisle<br>celsius   |    -   |
[URL](luis-reference-prebuilt-url.md)   |    V2, V3   |

Ver notas sobre [entidades pré-construídas precotados](luis-reference-prebuilt-deprecated.md)

KeyPhrase não está disponível em todas as subculturas do Português (Brasil) - ```pt-BR``` .

## <a name="turkish-entity-support"></a>Apoio à entidade turca

**Não existem entidades pré-construídas apoiadas na Turquia.**

<!--

| Prebuilt entity | tr-tr |
| --------------- | :---: |
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
As entidades pré-construídas são desenvolvidas no Recognizers-Text projeto de código aberto. [Contribuir](https://github.com/Microsoft/Recognizers-Text) para o projeto. Este projeto inclui exemplos de moeda por cultura.

GeografiaV2 e PersonName não estão incluídos no projeto Recognizers-Text. Para questões com estas entidades pré-construídas, por favor abra um pedido de [apoio.](../../azure-portal/supportability/how-to-create-azure-support-request.md)

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre o [número,](luis-reference-prebuilt-number.md) [datav2](luis-reference-prebuilt-datetimev2.md)e entidades [monetárias.](luis-reference-prebuilt-currency.md)
