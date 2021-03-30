---
title: Upgrade Azure IoT Hub | Microsoft Docs
description: Mude o nível de preços e escala para o IoT Hub para obter mais capacidades de mensagens e gestão de dispositivos.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "61440238"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Como atualizar o seu Hub IoT?

À medida que a sua solução IoT cresce, o Azure IoT Hub está pronto para o ajudar a escalar. O Azure IoT Hub oferece dois níveis, básicos (B) e standard (S), para acomodar clientes que queiram utilizar diferentes funcionalidades. Dentro de cada nível estão três tamanhos (1, 2 e 3) que determinam o número de mensagens que podem ser enviadas todos os dias.

Quando tem mais dispositivos e precisa de mais capacidades, existem três formas de ajustar o seu hub IoT de acordo com as suas necessidades:

* Adicione unidades dentro do hub IoT. Por exemplo, cada unidade adicional num hub B1 IoT permite mais 400.000 mensagens por dia.

* Mude o tamanho do hub IoT. Por exemplo, migrar do nível B1 para o nível B2 para aumentar o número de mensagens que cada unidade pode suportar por dia.

* Upgrade para um nível mais alto. Por exemplo, o upgrade do nível B1 para o nível S1 para acesso a funcionalidades avançadas com a mesma capacidade de mensagens.

Estas alterações podem ocorrer sem interromper as operações existentes.

Se quiser desvalorizar o seu hub IoT, pode remover unidades e reduzir o tamanho do hub IoT, mas não pode descer para um nível mais baixo. Por exemplo, pode passar do nível S2 para o nível S1, mas não do nível S2 para o nível B1. Apenas um tipo de [edição Iot Hub](https://azure.microsoft.com/pricing/details/iot-hub/) dentro de um nível pode ser escolhido por IoT Hub. Por exemplo, pode criar um Hub IoT com múltiplas unidades de S1, mas não com uma mistura de unidades de diferentes edições, como S1 e B3, ou S1 e S2.

Estes exemplos destinam-se a ajudá-lo a entender como ajustar o seu hub IoT à medida que a sua solução muda. Para obter informações específicas sobre as capacidades de cada nível, deve sempre consultar os preços do [Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Atualize o seu hub IoT existente

1. Inscreva-se no [portal Azure](https://portal.azure.com/) e navegue até ao seu hub IoT.

2. **Selecione preços e escala**.

   ![Preços e dimensionamento](./media/iot-hub-upgrade/pricing-scale.png)

3. Para alterar o nível para o seu hub, **selecione preço e nível de escala**. Escolha o novo nível e, em seguida, clique **em seleção**.

   ![Escalão de preço e dimensionamento](./media/iot-hub-upgrade/select-tier.png)

4. Para alterar o número de unidades no seu hub, insira um novo valor em **unidades IoT Hub**.

5. Selecione **Guardar** para guardar as alterações.

O seu hub IoT está agora ajustado e as suas configurações são inalteradas.

O limite máximo de partição para o hub IoT de nível básico e o ioT hub de nível padrão é de 32. A maioria dos hubs IoT só precisa de 4 divisórias. O limite de partição é escolhido quando o IoT Hub é criado, e relaciona as mensagens dispositivo-a-nuvem com o número de leitores simultâneos destas mensagens. Este valor permanece inalterado quando migra do nível básico para o nível padrão.

## <a name="next-steps"></a>Passos seguintes

Obtenha mais detalhes sobre [como escolher o nível IoT Hub certo](iot-hub-scaling.md).