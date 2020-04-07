---
title: Utilize remoting holográfico e renderização remota em unidade
description: Como a pré-visualização holográfica de remoting pode ser usada em combinação com a renderização remota azure
author: christophermanthei
ms.author: chmant
ms.date: 03/23/2020
ms.topic: how-to
ms.openlocfilehash: ac47a2922e92233f0acabf75817a712671306bc1
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/06/2020
ms.locfileid: "80681210"
---
# <a name="use-holographic-remoting-and-remote-rendering-in-unity"></a>Utilize remoting holográfico e renderização remota em unidade

[Remoting holográfico](https://docs.microsoft.com/windows/mixed-reality/holographic-remoting-player) e renderização remota Azure são mutuamente exclusivos dentro de uma aplicação. Como tal, o [modo de jogo de unidade](https://docs.microsoft.com/windows/mixed-reality/unity-play-mode) também não está disponível.

Para cada execução do editor da Unidade, apenas um dos dois pode ser usado. Para usar o outro, reinicie a Unidade primeiro.

## <a name="use-unity-play-mode-to-preview-on-hololens-2"></a>Use o modo de jogo da Unidade para pré-visualizar hololens 2

 O modo de jogo de unidade ainda pode ser usado, por exemplo, para testar a UI da aplicação. No entanto, é vital que a ARR nunca seja inicializada. Caso contrário, vai despenhar-se.

## <a name="use-a-wmr-vr-headset-to-preview-on-desktop"></a>Utilize um auricular WMR VR para pré-visualizar no ambiente de trabalho

Se estiver presente um auricular Vr de Realidade Mista do Windows, pode ser utilizado para pré-visualizar dentro da Unidade. Neste caso, é bom inicializar o ARR, no entanto não será possível ligar-se a uma sessão enquanto os auscultadores WMR forem utilizados.
