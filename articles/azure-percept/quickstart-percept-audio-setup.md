---
title: Começa com o Azure Percept Audio
description: Saiba como ligar o seu dispositivo Azure Percept Audio ao seu Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 575107859f56df742ab41a299269c250511022b3
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101665626"
---
# <a name="azure-percept-audio-setup"></a>Configuração de áudio Azure Percept

Azure Percept Audio trabalha fora da caixa com Azure Percept DK. Não é necessária uma configuração única.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- Áudio Azure Percept
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub

## <a name="connecting-your-devices"></a>Ligação dos seus dispositivos

1. Ligue o dispositivo Azure Percept Audio à placa de porta-aviões Azure Percept DK com o cabo USB Micro Type-B a USB Type-A. Ligue a extremidade Micro Tipo-B do cabo ao SoM áudio e a extremidade tipo A à placa de porta-aviões Percept DK.

1. Energia no devkit.

    - O LED L01 no Audio SoM mudará para verde sólido para indicar que o dispositivo foi ligado.
    - O LED L02 mudará para verde intermitente para indicar que o SoM áudio está a autenticar..

1. Aguarde que o processo de autenticação esteja concluído-- isto pode demorar até 3 minutos.

1. Está pronto para começar a prototipagem quando vir um dos seguintes:

    - O LED L01 desliga-se e o L02 fica branco. Isto indica que a autenticação está completa e o devkit ainda não foi configurado com uma palavra-chave.
    - Os três LEDs ficam azuis. Isto indica que a autenticação está completa e o devkit é configurado com uma palavra-chave.

    > [!NOTE]
    > Contacte para suporte se o seu devkit não autenticar.

## <a name="next-steps"></a>Passos seguintes

Criar uma [solução de fala sem código](./tutorial-no-code-speech.md).