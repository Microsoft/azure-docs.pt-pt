---
title: Devolver N-Best Traduções - Tradutor
titleSuffix: Azure Cognitive Services
description: Devolver as melhores traduções do N-Best utilizando o Tradutor.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ROBOTS: NOINDEX
ms.openlocfilehash: 1fe370070aa97befb418d27def32725bf0a148b3
ms.sourcegitcommit: bb0afd0df5563cc53f76a642fd8fc709e366568b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83592683"
---
# <a name="how-to-return-n-best-translations"></a>Como devolver as melhores traduções da N-Best

> [!NOTE]
> Este método está premeditado. Não está disponível em V3.0 do Tradutor.

Os métodos GetTranslations() e GetTranslationsArray do Tradutor incluem uma bandeira booleana opcional "Inclua MultipleMTAlternatives".
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
