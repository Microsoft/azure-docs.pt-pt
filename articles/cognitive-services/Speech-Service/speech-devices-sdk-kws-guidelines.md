---
title: Orientações de nomeação de palavras-chave - Serviço de fala
titleSuffix: Azure Cognitive Services
description: A criação de uma palavra-chave eficaz é vital para garantir que o seu dispositivo irá responder de forma consistente e precisa.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: dapine
ms.openlocfilehash: 1d363f9f6f9cb5b1ea28e6ae15a9cef857304cab
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/14/2020
ms.locfileid: "79370683"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Diretrizes para a criação de uma palavra-chave eficaz

A criação de uma palavra-chave eficaz é vital para garantir que o seu dispositivo irá responder de forma consistente e precisa. Personalizar a sua palavra-chave é uma forma eficaz de diferenciar o seu dispositivo e fortalecer a sua marca. Neste artigo, aprende-se alguns princípios orientadores para criar uma palavra-chave eficaz.

## <a name="choose-an-effective-keyword"></a>Escolha uma palavra-chave eficaz

Considere as seguintes orientações quando escolher uma palavra-chave:

> [!div class="checklist"]
> * A tua palavra-chave deve ser uma palavra ou frase em inglês.
> * Deve demorar mais do que dois segundos para dizer.
> * Palavras de 4 a 7 sílabas funcionam melhor. Por exemplo, "Hey, Computador" é uma boa palavra-chave. Apenas "Olá" é ruim.
> * As palavras-chave devem seguir as regras comuns de pronúncia inglesa.
> * Um único ou até mesmo uma palavra produzida que segue as regras de pronúncia inglesa comum pode reduzir os falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.
> * Não escolha uma palavra em comum. Por exemplo, "" ilimitado"e"Ir"são palavras que as pessoas dizem com frequência na conversação comum. Elas podem ser acionadores falso para o seu dispositivo.
> * Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os utilizadores teria de saber a pronúncia "right" para que o seu dispositivo para responder. Por exemplo, "509" pode ser pronunciado "cinco zero nine," "cinco AH nove," ou "cinco centenas e nove." "R.E.I." pode ser pronunciado "r-e-i" ou "ray." "Ao vivo" pode ser pronunciado "/līv/" ou "/liv/".
> * Não utilize carateres especiais, símbolos ou dígitos. Por exemplo, "Go#" e "20 + gatos" podem ser palavras-chave problemáticas. No entanto, "aceda pequenos" ou "vinte plus gatos" poderá funcionar. Ainda pode usar os símbolos em sua imagem corporativa e usar o marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se escolher uma palavra marcada como palavra-chave, certifique-se de que possui essa marca ou que tem permissão do proprietário da marca para usar a palavra. A Microsoft não se responsabiliza por quaisquer problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="next-steps"></a>Passos seguintes

Aprenda a [criar uma palavra-chave personalizada usando](speech-devices-sdk-create-kws.md)o Speech Studio .
