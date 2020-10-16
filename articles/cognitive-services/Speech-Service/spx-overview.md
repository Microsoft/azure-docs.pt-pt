---
title: O Azure Speech CLI
titleSuffix: Azure Cognitive Services
description: O CLI do Discurso é uma ferramenta de linha de comando para utilizar o serviço Desempreso sem escrever qualquer código. O CLI do Discurso requer uma configuração mínima, e é fácil começar imediatamente a experimentar as principais características do serviço de fala para ver se os seus casos de uso podem ser cumpridos.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: trbye
ms.custom: devx-track-azurecli
ms.openlocfilehash: 6b852186834fba858e8a049a8230b38f3d69164d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88067431"
---
# <a name="what-is-the-speech-cli"></a>O que é a CLI de Voz?

O CLI do Discurso é uma ferramenta de linha de comando para utilizar o serviço Desempreso sem escrever qualquer código. O CLI do Discurso requer uma configuração mínima, e é fácil começar imediatamente a experimentar as principais características do serviço Desempreso para ver se os seus casos de uso podem ser cumpridos. Em poucos minutos, pode executar fluxos de trabalho simples de teste como o reconhecimento da fala de um diretório de ficheiros, ou texto-a-voz numa coleção de cordas de um ficheiro. Para além de fluxos de trabalho simples, o CLI do Discurso está pronto para a produção e pode ser dimensionado para executar processos maiores usando `.bat` scripts automatizados ou de conchas.

A maioria das principais características do Discurso SDK estão disponíveis no CLI do Discurso, e algumas funcionalidades e personalizações avançadas são simplificadas no CLI do Discurso. Considere as seguintes orientações para decidir quando usar o CLI do Discurso ou o SDK do Discurso.

Utilize o CLI do Discurso quando:
* Você quer experimentar com funcionalidades de serviço de fala com configuração mínima e sem código
* Tem requisitos relativamente simples para uma aplicação de produção utilizando o serviço Speech

Utilize o SDK de discurso quando:
* Pretende integrar a funcionalidade do serviço de voz dentro de um idioma ou plataforma específico (por exemplo, C#, Python, C++)
* Você tem requisitos complexos que podem exigir pedidos de serviço avançados, ou desenvolver comportamento personalizado, incluindo streaming de resposta

## <a name="core-features"></a>Características do núcleo

* Reconhecimento de voz - Converta a fala-a-texto a partir de ficheiros áudio ou diretamente de um microfone, ou transcreva uma conversa gravada.

* Síntese de fala - Converta texto-a-fala utilizando a entrada de ficheiros de texto ou introduza diretamente a partir da linha de comando. Personalize as características de saída da fala utilizando [configurações SSML](speech-synthesis-markup.md), e [vozes padrão ou neurais](speech-synthesis-markup.md#standard-neural-and-custom-voices).

* Tradução da fala - Traduza o áudio numa língua de origem para texto ou áudio numa língua-alvo.

* Executar recursos compute Azure - Enviar comandos CLI de discurso para executar um recurso de computação remota Azure utilizando `spx webjob` .

## <a name="get-started"></a>Introdução

Para começar com o Discurso CLI, consulte o [artigo básico](spx-basics.md). Este artigo mostra-lhe como executar alguns comandos básicos, e também mostra comandos um pouco mais avançados para executar operações de lote para discurso-a-texto e texto-a-discurso. Depois de ler o artigo básico, você deve ter o suficiente de uma compreensão da sintaxe para começar a escrever alguns comandos personalizados, ou automatizar operações simples de serviço de fala.

## <a name="next-steps"></a>Passos seguintes

- [Básicos do CLI da fala](spx-basics.md)
- Se o seu caso de uso for mais complexo, [obtenha o SDK do discurso](speech-sdk.md)
