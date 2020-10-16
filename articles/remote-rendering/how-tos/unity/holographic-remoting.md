---
title: Utilizar o Holographic Remoting e o Remote Rendering no Unity
description: Como a pré-visualização holográfica de remoting pode ser usada em combinação com a renderização remota Azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: 180af30f57a8123b6e90cc8b11848b92b3c86db1
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91802179"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Utilizar o Holographic Remoting e o Remote Rendering no Unity

[O Remoting Holográfico](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) e a Renderização Remota Azure são mutuamente exclusivos dentro de uma aplicação. Como tal, [o modo de reprodução Unity](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) também não está disponível.

Para cada execução do editor da Unidade, apenas um dos dois pode ser usado. Para usar o outro, reinicie a Unidade primeiro.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Use o modo de reprodução de Unidade para pré-visualizar hololens 2

 O modo de reprodução de unidade ainda pode ser usado, por exemplo, para testar a UI da aplicação. No entanto, é vital que o ARR nunca seja inicializado. Caso contrário, vai despenhar-se.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Use um auricular WMR VR para pré-visualizar no ambiente de trabalho

Se estiver presente um auricular Windows Mixed Reality VR, pode ser utilizado para pré-visualizar dentro da Unidade. Neste caso, é bom rubricar o ARR, no entanto não será possível ligar-se a uma sessão enquanto os auscultadores WMR são utilizados.
