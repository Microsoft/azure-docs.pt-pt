---
title: O Discurso Azure CLI
titleSuffix: Azure Cognitive Services
description: O Speech CLI é uma ferramenta de linha de comando para usar o serviço de Fala sem escrever qualquer código. O Talk CLI requer uma configuração mínima, e é fácil começar imediatamente a experimentar com as principais características do serviço de Fala para ver se os seus casos de uso podem ser cumpridos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.openlocfilehash: 3fb0b71cbb82b3b9acad1d1ce093baa86c700a51
ms.sourcegitcommit: cf7caaf1e42f1420e1491e3616cc989d504f0902
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/22/2020
ms.locfileid: "83800345"
---
# <a name="what-is-the-speech-cli"></a>O que é o Discurso CLI?

O Speech CLI é uma ferramenta de linha de comando para usar o serviço de Fala sem escrever qualquer código. O Talk CLI requer uma configuração mínima, e é fácil começar imediatamente a experimentar com as principais características do serviço de Fala para ver se os seus casos de uso podem ser cumpridos. Em poucos minutos, você pode executar fluxos de trabalho de teste simples como o reconhecimento da fala do lote a partir de um diretório de ficheiros, ou texto-a-fala em uma coleção de cordas de um arquivo. Além de fluxos de trabalho simples, o Speech CLI está pronto para a produção e pode ser dimensionado para executar processos maiores usando `.bat` scripts automatizados ou de concha.

A maioria das características primárias do SDK do Discurso estão disponíveis no Speech CLI, mas algumas características avançadas e personalizações são simplificadas no Discurso CLI. Considere as seguintes orientações para decidir quando usar o DISCURSO CLI ou o SDK da Fala.

Utilize o CLI da fala quando:
* Você quer experimentar com funcionalidades do serviço de fala com configuração mínima e sem código
* Você tem requisitos relativamente simples para uma aplicação de produção usando o serviço Speech

Use o SDK do discurso quando:
* Pretende integrar a funcionalidade do serviço da Fala dentro de uma linguagem ou plataforma específica (por exemplo, C#, Python, C++)
* Você tem requisitos complexos que podem exigir pedidos de serviço avançados, ou desenvolver comportamento personalizado, incluindo streaming de resposta

## <a name="core-features"></a>Características do núcleo

* Reconhecimento da fala - Converter o discurso a texto quer a partir de ficheiros áudio quer diretamente a partir de um microfone, ou transcrevo uma conversa gravada.

* Síntese da fala - Converta o texto-a-fala utilizando a entrada de ficheiros de texto ou a entrada diretamente da linha de comando. Personalize as características de saída da fala utilizando [configurações SSML,](speech-synthesis-markup.md)e [vozes standard ou neurais.](speech-synthesis-markup.md#standard-neural-and-custom-voices)

* Tradução da fala - Traduzir áudio numa linguagem de origem para texto numa linguagem-alvo.

* Executar os recursos da computação Azure - Envie comandos SPX para executar um recurso de computação remota Azure utilizando `spx webjob` .

## <a name="get-started"></a>Introdução

Para começar com o Discurso CLI, consulte o [artigo básico.](spx-basics.md) Este artigo mostra-lhe como executar alguns comandos básicos com SPX, e também mostra comandos um pouco mais avançados para executar operações de lote para o discurso-a-texto e texto-a-fala. Depois de ler o artigo básico, você deve ter o suficiente de uma compreensão da sintaxe SPX para começar a escrever alguns comandos personalizados, ou automatizar operações simples de Fala.

## <a name="next-steps"></a>Passos seguintes

- [Básicos cli da fala](spx-basics.md)
- Se o seu caso de uso for mais complexo, [obtenha o SDK da Fala](speech-sdk.md)
