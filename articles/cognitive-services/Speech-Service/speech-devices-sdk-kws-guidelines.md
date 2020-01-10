---
title: Diretrizes de nomenclatura de palavra-chave-serviço de fala
titleSuffix: Azure Cognitive Services
description: A criação de uma palavra-chave efetiva é vital para garantir que seu dispositivo responda de forma consistente e precisa.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/11/2019
ms.author: dapine
ms.openlocfilehash: 3c3943f7269fa2d0ed25acf2215549635b5f16ac
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456390"
---
# <a name="guidelines-for-creating-an-effective-keyword"></a>Diretrizes para criar uma palavra-chave efetiva

A criação de uma palavra-chave efetiva é vital para garantir que seu dispositivo responda de forma consistente e precisa. Personalizar sua palavra-chave é uma maneira eficaz de diferenciar seu dispositivo e fortalecer sua identidade visual. Neste artigo, você aprende alguns princípios de orientação para criar uma palavra-chave efetiva.

## <a name="choose-an-effective-keyword"></a>Escolher uma palavra-chave efetiva

Considere as seguintes diretrizes ao escolher uma palavra-chave:

* Sua palavra-chave deve ser uma palavra ou frase em inglês.

* Deve demorar mais do que dois segundos para dizer.

* Palavras de 4 a 7 sílabas funcionam melhor. Por exemplo, "Ei, computador" é uma boa palavra-chave. Apenas "Olá" é ruim.

* As palavras-chave devem seguir as regras de pronúncia em inglês comuns.

* Um único ou até mesmo uma palavra produzida que segue as regras de pronúncia inglesa comum pode reduzir os falsos positivos. Por exemplo, "computerama" pode ser uma boa palavra-chave.

* Não escolha uma palavra em comum. Por exemplo, "" ilimitado"e"Ir"são palavras que as pessoas dizem com frequência na conversação comum. Elas podem ser acionadores falso para o seu dispositivo.

* Evite usar uma palavra-chave que possa ter pronúncias alternativas. Os utilizadores teria de saber a pronúncia "right" para que o seu dispositivo para responder. Por exemplo, "509" pode ser pronunciado "cinco zero nine," "cinco AH nove," ou "cinco centenas e nove." "R.E.I." pode ser pronunciado "r-e-i" ou "ray." "Ao vivo" pode ser pronunciado "/līv/" ou "/liv/".

* Não utilize carateres especiais, símbolos ou dígitos. Por exemplo, "Go #" e "20 + gatos" podem ser palavras-chave problemáticas. No entanto, "aceda pequenos" ou "vinte plus gatos" poderá funcionar. Ainda pode usar os símbolos em sua imagem corporativa e usar o marketing e documentação para reforçar a pronúncia adequada.

> [!NOTE]
> Se você escolher uma palavra com marca registrada como sua palavra-chave, certifique-se de que você possui essa marca registrada ou que você tem permissão do proprietário da marca comercial para usar a palavra. A Microsoft não é responsável por problemas legais que possam surgir da sua escolha de palavra-chave.

## <a name="next-steps"></a>Passos seguintes

Saiba como [criar uma palavra-chave personalizada usando o Speech Studio](speech-devices-sdk-create-kws.md).
