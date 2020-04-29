---
title: Devolver N-Best Traduções - Texto tradutor
titleSuffix: Azure Cognitive Services
description: Devolver as melhores traduções do N-Best utilizando a API de Texto tradutor.
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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "73837248"
---
# <a name="how-to-return-n-best-translations"></a>Como devolver as melhores traduções da N-Best

> [!NOTE]
> Este método está premeditado. Não está disponível em V3.0 da API de Texto tradutor.

Os métodos GetTranslations() e GetTranslationsArray() da Microsoft Tradutor API incluem uma bandeira booleanopcional opcional "Inclua MultipleMTAlternatives".
O método regressará às alternativas maxTranslations onde o delta é fornecido a partir da lista N-Best do motor tradutor.

A assinatura é:

**Sintaxe**

| C# |
|:---|
| GetTranslationsResponse Microsoft.Tradutor.GetTranslations (appId, text, from, to, maxTranslations, options); |

**Parâmetros**

| Parâmetro | Descrição |
|:---|:---|
| appId | **Obrigatório** Se o cabeçalho de autorização for utilizado, deixe o campo apástica vazio eespecifique uma cadeia contendo "Bearer" + " + ficha de acesso.|
| texto | **Obrigatório** Uma corda que representa o texto para traduzir. O tamanho do texto não deve exceder 10000 caracteres.|
| De | **Obrigatório** Uma cadeia que representa o código linguístico do texto para traduzir. |
| para | **Obrigatório** Uma cadeia que representa o código linguístico para traduzir o texto. |
| maxTraduções | **Obrigatório** Um int que representa o número máximo de traduções para devolver. |
| opções | **Opcional** Um objeto TranslateOptions que contém os valores listados abaixo. São todos opcionais e padrão para as configurações mais comuns.

* Categoria: A única opção suportada, e o padrão, é "geral".
* ConteúdoType: A única opção suportada, e a opção por defeito é "texto/planície".
* Estado: Estado do utilizador para ajudar a correlacionar o pedido e a resposta. O mesmo conteúdo será devolvido na resposta.
* Inclua MultipleMTAlternatives: bandeira para determinar se deve devolver mais do que uma alternativa do motor MT. O padrão é falso e inclui apenas 1 alternativa.

## <a name="ratings"></a>Classificações
As classificações são aplicadas da seguinte forma: A melhor tradução automática tem uma classificação de 5.
As alternativas de tradução geradas automaticamente (N-Best) têm uma classificação de 0, e têm um grau de correspondência de 100.

## <a name="number-of-alternatives"></a>Número de alternativas
O número de alternativas devolvidas está até ao maxTranslations, mas pode ser menor.

## <a name="language-pairs"></a>Pares de línguas
Esta funcionalidade não está disponível para traduções entre o chinês simplificado e o chinês tradicional, ambas as direções. Está disponível para todos os outros pares de idiomas suportados pelo Microsoft Tradutor.
