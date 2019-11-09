---
title: Retornar N-melhores conversões-Tradução de Texto
titleSuffix: Azure Cognitive Services
description: Retornar N-melhores conversões usando o API de Tradução de Texto.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: eff25877165ac365e0af77651147fcdd1eebe294
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73837248"
---
# <a name="how-to-return-n-best-translations"></a>Como retornar N-melhores conversões

> [!NOTE]
> Esse método foi preterido. Ele não está disponível em V 3.0 do API de Tradução de Texto.

Os métodos gettranslas () e GetTranslationsArray () da API do Microsoft Translator incluem um sinalizador booliano opcional "IncludeMultipleMTAlternatives".
O método retornará até maxTranslations alternativas em que o Delta é fornecido da lista N melhores do mecanismo de tradutor.

A assinatura é:

**Sintaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft. Translator. gettranslates (appId, Text, from, to, maxTranslations, Options); |

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Necessário** Se o cabeçalho de autorização for usado, deixe o campo AppID vazio, caso contrário, especifique uma cadeia de caracteres contendo "portador" + "" + token de acesso.|
| texto | **Necessário** Uma cadeia de caracteres que representa o texto a ser traduzido. O tamanho do texto não deve exceder 10000 caracteres.|
| De | **Necessário** Uma cadeia de caracteres que representa o código de idioma do texto a ser traduzido. |
| para | **Necessário** Uma cadeia de caracteres que representa o código de idioma no qual converter o texto. |
| maxTranslations | **Necessário** Um int que representa o número máximo de conversões a serem retornadas. |
| Opções | **Opcional** Um objeto translateoptions que contém os valores listados abaixo. Eles são todos opcionais e padrão para as configurações mais comuns.

* Categoria: o único com suporte e a opção padrão é "geral".
* ContentType: o único com suporte e a opção default é "text/plain".
* Estado: estado do usuário para ajudar a correlacionar a solicitação e a resposta. O mesmo conteúdo será retornado na resposta.
* IncludeMultipleMTAlternatives: sinalizador para determinar se deve retornar mais de uma alternativa do mecanismo MT. O padrão é false e inclui apenas uma alternativa.

## <a name="ratings"></a>As
As classificações são aplicadas da seguinte maneira: a melhor tradução automática tem uma classificação de 5.
As alternativas de tradução (N-melhor) geradas automaticamente têm uma classificação de 0 e têm um grau de correspondência de 100.

## <a name="number-of-alternatives"></a>Número de alternativas
O número de alternativas retornadas é de até maxTranslations, mas pode ser menor.

## <a name="language-pairs"></a>Pares de idiomas
Essa funcionalidade não está disponível para traduções entre chinês simplificado e tradicional, ambas as direções. Ele está disponível para todos os outros pares de idiomas com suporte do Microsoft Translator.
