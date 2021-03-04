---
title: Começa com o Azure Percept Audio
description: Saiba como ligar o seu dispositivo Azure Percept Audio ao seu Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/18/2021
ms.custom: template-quickstart
ms.openlocfilehash: 588ebde85b6012ddbfb88ca8305fc735b7a0ba41
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2021
ms.locfileid: "102097997"
---
# <a name="azure-percept-audio-setup"></a>Configuração de áudio Azure Percept

Azure Percept Audio trabalha fora da caixa com Azure Percept DK. Não é necessária uma configuração única.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- Áudio Azure Percept
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu devkit a uma rede Wi-Fi, criou um Hub IoT e ligou o seu devkit ao IoT Hub
- Altifalantes ou auscultadores que podem ligar-se a tomada de áudio de 3,5 mm (opcional)

## <a name="connecting-your-devices"></a>Ligação dos seus dispositivos

1. Ligue o dispositivo Azure Percept Audio à placa de porta-aviões Azure Percept DK com o cabo Micro USB incluído ao cabo USB Type-A. Ligue a extremidade Micro USB do cabo à placa Interposer (desenvolvedor) e à extremidade Type-A à placa de porta-aviões Percept DK.
1. (Opcional) ligue o seu altifalante ou auscultadores ao seu Azure Percept Audio através da tomada de áudio, que está rotulada como "Line out". Isto permitir-lhe-á ouvir as respostas áudio do seu assistente de voz. Se não ligar um altifalante ou auscultadores, ainda poderá ver as respostas como texto na janela de demonstração. 

1. Energia no devkit. O LED L02 na placa Interposer mudará para branco intermitente para indicar que o dispositivo foi ligado e que o SoM áudio está a autenticar.

1. Aguarde que o processo de autenticação esteja concluído-- isto pode demorar até 3 minutos.

1. Está pronto para começar a prototipagem quando vir um dos seguintes:

    - O LED L02 mudará para branco sólido. Isto indica que a autenticação está completa e o devkit ainda não foi configurado com uma palavra-chave.
    - Os três LEDs ficam azuis. Isto indica que a autenticação está completa e o devkit é configurado com uma palavra-chave.

## <a name="next-steps"></a>Passos seguintes

Criar uma [solução de fala sem código](./tutorial-no-code-speech.md).
