---
title: Configurar a Azure Percept Audio
description: Saiba como ligar o seu dispositivo Azure Percept Audio ao seu Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: quickstart
ms.date: 03/25/2021
ms.openlocfilehash: fa3dad8cdd38e6db621d8194cc9472430c7c5008
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105605795"
---
# <a name="azure-percept-audio-setup"></a>Configuração de áudio Azure Percept

Azure Percept Audio trabalha fora da caixa com Azure Percept DK. Não é necessária uma configuração única.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- Áudio Azure Percept
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub
- Altifalantes ou auscultadores que podem ligar-se a uma tomada de áudio de 3,5 mm (opcional)

## <a name="connecting-your-devices"></a>Ligação dos seus dispositivos

1. Ligue o dispositivo Azure Percept Audio à placa de porta-aviões Azure Percept DK com o cabo Micro USB incluído ao cabo USB Type-A. Ligue a extremidade Micro USB do cabo à placa audio-interposer (desenvolvedor) e à extremidade Type-A à placa de porta-aviões Percept DK.

1. (Opcional) ligue o seu altifalante ou auscultadores ao seu dispositivo Azure Percept Audio através da tomada de áudio, que está marcada como "Line out". Isto permitir-lhe-á ouvir respostas áudio.

1. Energia no devkit. O LED L02 na placa de interposição Áudio mudará para branco intermitente para indicar que o dispositivo foi ligado e que o Audio SoM está a autenticar.

1. Aguarde que o processo de autenticação esteja concluído-- isto pode demorar até 3 minutos.

1. Está pronto para começar a prototipagem quando vir um dos seguintes:

    - O LED L02 mudará para branco sólido: isto indica que a autenticação está completa e o devkit ainda não foi configurado com uma palavra-chave.
    - Os três LEDs ficam azuis: isto indica que a autenticação está completa e o devkit está configurado com uma palavra-chave.

## <a name="next-steps"></a>Passos seguintes

Crie uma [solução de fala sem código](./tutorial-no-code-speech.md) no [Azure Percept Studio](https://go.microsoft.com/fwlink/?linkid=2135819).
