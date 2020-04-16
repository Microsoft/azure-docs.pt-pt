---
title: Orientações de nomeação de palavras-chave - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A criação de uma palavra-chave eficaz é vital para garantir que o seu dispositivo irá responder de forma consistente e precisa.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: c00d27e5e7b7a8d614ce94ea4024b6093669757c
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81399834"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Diretrizes para a criação de uma palavra-chave eficaz

A criação de uma palavra-chave eficaz é vital para garantir que o seu dispositivo irá responder de forma consistente e precisa. Personalizar a sua palavra-chave é uma forma eficaz de diferenciar o seu dispositivo e fortalecer a sua marca. Neste artigo, aprende-se alguns princípios orientadores para criar uma palavra-chave eficaz.

## <a name="choose-an-effective-keyword"></a>Escolha uma palavra-chave eficaz

Considere as seguintes orientações quando escolher uma palavra-chave:

> [!div class="checklist"]
> * A tua palavra-chave deve ser uma palavra ou frase em inglês.
> * Não deve levar mais do que dois segundos para dizer.
> * Palavras de 4 a 7 silabáveis funcionam melhor. Por exemplo, "Hey, Computador" é uma boa palavra-chave. Só "Hey" é pobre.
> * As palavras-chave devem seguir as regras comuns de pronúncia inglesa.
> * Uma palavra única ou mesmo inventada que siga as regras comuns de pronúncia inglesa pode reduzir falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.
> * Não escolha uma palavra comum. Por exemplo, "comer" e "ir" são palavras que as pessoas dizem frequentemente numa conversa comum. Podem ser falsos gatilhos para o seu dispositivo.
> * Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os utilizadores teriam de saber a pronúncia "certa" para que o seu dispositivo respondesse. Por exemplo, "509" pode ser pronunciado "cinco zero nove", "cinco oh nove", ou "quinhentos e nove". "R.E.I." pode ser pronunciado "r-e-i" ou "ray". "Live" pode ser pronunciado "/līv/" ou "/liv/".
> * Não utilize caracteres, símbolos ou dígitos especiais. Por exemplo, "Go#" e "20 + gatos" podem ser palavras-chave problemáticas. No entanto, "vai afiado" ou "vinte mais gatos" pode funcionar. Ainda pode utilizar os símbolos na sua marca e utilizar marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se escolher uma palavra marcada como palavra-chave, certifique-se de que possui essa marca ou que tem permissão do proprietário da marca para usar a palavra. A Microsoft não se responsabiliza por quaisquer problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="next-steps"></a>Passos seguintes

Aprenda a [criar uma palavra-chave personalizada usando](speech-devices-sdk-create-kws.md)o Speech Studio .
