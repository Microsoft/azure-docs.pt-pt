---
title: Retornar traduções de melhor N - API de texto do tradutor
titlesuffix: Azure Cognitive Services
description: Retornar traduções de múltipla, com a API de texto do Microsoft Translator.
services: cognitive-services
author: rajdeep-in
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-pawal
ms.openlocfilehash: 27138fc82515983bb07df845e1204fe04dff915a
ms.sourcegitcommit: 3d4121badd265e99d1177a7c78edfa55ed7a9626
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/30/2019
ms.locfileid: "66389665"
---
# <a name="how-to-return-n-best-translations"></a>Como retornar traduções múltipla

> [!NOTE]
> Tato metoda se zamítá. Não está disponível no V3.0 da API de texto do Translator.

Os métodos GetTranslations() e GetTranslationsArray() de API do Microsoft Translator incluem um sinalizador booleano opcional "IncludeMultipleMTAlternatives".
O método deve retornar até maxTranslations alternativas em que o delta é fornecido a partir da lista de múltipla de mecanismo do Microsoft translator.

A assinatura é:

**Sintaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Translator.GetTranslations(appId, text, from, to, maxTranslations, options); |

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Necessário** se o cabeçalho de autorização é usado, deixe o campo de appid vazio ou especificar uma cadeia de caracteres que contém "Bearer" + "" + o token de acesso.|
| text | **Necessário** uma cadeia de caracteres que representa o texto a traduzir. O tamanho do texto não pode exceder os 10000 carateres.|
| from | **Necessário** uma cadeia de caracteres que representa o código de idioma do texto a traduzir. |
| para | **Necessário** representando o código de idioma para traduzir o texto numa cadeia de caracteres. |
| maxTranslations | **Necessário** um inteiro que representa o número máximo de traduções para retornar. |
| Opções | **Opcional** TranslateOptions de um objeto que contém os valores apresentados abaixo. Eles são todos opcionais e predefinido para as definições mais comuns.

* Categoria: O único suportado e a predefinição, a opção é "geral".
* ContentType: O único suportado e a predefinição, a opção é "text/plain".
* Estado: Estado do utilizador para o ajudar a correlacionar pedido e resposta. O mesmo conteúdo será devolvido na resposta.
* IncludeMultipleMTAlternatives: sinalizador para determinar se deve devolver mais do que um alternativas do mecanismo de MT. Padrão é false e inclui apenas 1 alternativa.

## <a name="ratings"></a>Classificações
As classificações são aplicadas da seguinte forma: A tradução automática melhor tem uma classificação de 5.
As alternativas de tradução (múltipla) geradas automaticamente tem uma classificação de 0 e tem um nível de correspondência de 100.

## <a name="number-of-alternatives"></a>Número de alternativas
O número de alternativas retornados é até maxTranslations, mas pode ser inferior.

## <a name="language-pairs"></a>Pares de idiomas
Esta funcionalidade não está disponível para conversões entre simplificado e chinês tradicional, ambas as direções. Está disponível para todos os outros pares de idiomas suportados do Microsoft Translator.
