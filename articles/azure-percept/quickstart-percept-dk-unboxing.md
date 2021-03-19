---
title: Desacompra e monte os seus componentes Azure Percept DK
description: Aprenda a desboxe, conecte-se e ligue-se no seu Azure Percept DK
author: elqu20
ms.author: v-elqu
ms.service: azure-percept
ms.topic: quickstart
ms.date: 02/16/2021
ms.custom: template-quickstart
ms.openlocfilehash: efa255ba38f7e00785335bf458ecc0ed91da646b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "104608185"
---
# <a name="quickstart-unbox-and-assemble-your-azure-percept-dk-components"></a>Quickstart: desboxe e monte os seus componentes Azure Percept DK

Depois de ter recebido o seu Azure Percept DK, consulte este guia para obter informações sobre a ligação dos componentes e a alimentação do dispositivo.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- Chave de fendas P7 (opcional, utilizada para fixar o conector do cabo de alimentação à placa transportadora)

## <a name="unbox-and-assemble-your-device"></a>Desacompra e monte o seu dispositivo

1. Descasque os componentes Azure Percept DK.

    O devkit contém uma placa transportadora, Azure Percept Vision SoM, caixa de acessórios contendo antenas e cabos, e um cartão de boas-vindas com uma chave hexaquisa.

1. Ligue os componentes de devkit.

    > [!NOTE]
    > A porta adaptador de energia está localizada no lado direito da placa do porta-aviões. As restantes portas (2x USB-A, 1x USB-C e 1x Ethernet) e o botão de alimentação estão localizados no lado esquerdo da placa de transporte.

    1. Aparafusar as duas antenas Wi-Fi na placa de transporte.

    1. Ligue o Vision SoM à porta USB-C da placa de porta-transporte com o cabo USB-C.

    1. Ligue o cabo de alimentação ao adaptador de alimentação.

    1. Retire as embalagens de plástico restantes dos dispositivos.

    1. Ligue o adaptador/cabo de alimentação à placa do porta-aviões e a uma tomada de parede. Para fixar completamente o conector do cabo de alimentação à placa de transporte, utilize uma chave de fendas P7 (não incluída no devkit) para apertar os parafusos do conector.

    1. Depois de ligar o cabo de alimentação a uma tomada de parede, o aparelho liga-se automaticamente. O botão de alimentação do lado esquerdo da placa do porta-aviões acende-se. Por favor, deixe algum tempo para o dispositivo arrancar.

        > [!NOTE]
        > O botão de alimentação destina-se a desligar ou reiniciar o dispositivo enquanto está ligado a uma tomada de corrente. Em caso de falha de energia, o dispositivo reiniciará automaticamente.

Para uma demonstração visual da montagem de devkit, consulte as 0:00 às 0:50 do seguinte vídeo:

</br>

> [!VIDEO https://www.youtube.com/embed/-dmcE2aQkDE]

## <a name="next-steps"></a>Passos seguintes

Agora que o seu devkit está ligado e ligado, consulte a experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md) para completar a configuração do dispositivo. A experiência de configuração permite-lhe ligar o seu devkit a uma rede Wi-Fi, configurar um login SSH, criar um Hub IoT e providenciar o seu devkit à sua conta Azure. Uma vez concluída a configuração do dispositivo, estará pronto para iniciar a prototipagem.