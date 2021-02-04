---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: 44188608edfdff861cc7e8f611694dec79b9ac7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/04/2021
ms.locfileid: "99538781"
---
Criar um Hub IoT com o portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir da página inicial do Azure, **selecione Criar um recurso** e, em seguida, insira o *IoT Hub* em Busca do **Mercado.**

1. Selecione **IoT Hub** a partir dos resultados da pesquisa e, em seguida, selecione **Criar**.

1. No separador **Básicos,** complete os campos da seguinte forma:

   - **Subscrição**: Selecione a subscrição para usar para o seu hub.

   - **Grupo de Recursos**: Selecione um grupo de recursos ou crie um novo. Para criar um novo, **selecione Criar novo** e preencha o nome que pretende utilizar. Para utilizar um grupo de recursos existente, selecione esse grupo de recursos. Para obter mais informações, consulte os grupos de [recursos Do Gestor de Recursos Azure.](../articles/azure-resource-manager/management/manage-resource-groups-portal.md) Este tutorial utiliza o nome **tutoriais-iot-hub-rg**.

   - **Região**: Selecione a região em que deseja que o seu centro esteja localizado. Selecione o local mais próximo de si. Algumas funcionalidades, como [os fluxos de dispositivos IoT Hub,](../articles/iot-hub/iot-hub-device-streams-overview.md)só estão disponíveis em regiões específicas. Para estas características limitadas, deve selecionar uma das regiões apoiadas. Este tutorial usa a região **oeste dos EUA.**

   - **IoT Hub Name**: Insira um nome para o seu hub. Este nome tem de ser globalmente exclusivo. Este tutorial usa **tutoriais-iot-hub.** Tem de escolher o seu próprio nome exclusivo quando criar o hub.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Criar um hub no portal Azure":::

1. Selecione **Seguinte: Networking** para continuar a criar o seu hub.

   Escolha os pontos finais que podem ligar-se ao seu Hub IoT. Pode selecionar o ponto final público por defeito **(todas as redes)** ou escolher **o ponto final público (intervalos IP selecionados)** ou ponto final **privado**. Aceite a definição padrão para este tutorial.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Escolha os pontos finais que podem ligar.":::

1. Selecione **Seguinte: Gestão** para continuar a criar o seu hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Desconfiem do tamanho e da escala para um novo hub utilizando o portal Azure.":::

   Pode aceitar as definições predefinidos aqui. Se desejar, pode modificar qualquer um dos seguintes campos:

   - **Preço e nível de escala**: O seu nível selecionado. Escolha o nível livre. O nível livre destina-se a testes e avaliação. Permite que 500 dispositivos sejam ligados ao hub e até 8.000 mensagens por dia. Cada subscrição do Azure pode criar um hub IoT no nível livre.

   - **Unidades IoT Hub**: O número de mensagens permitidas por unidade por dia depende do nível de preços do seu hub. Por exemplo, se quiser que o hub suporte uma entrada de 700.000 mensagens, escolha duas unidades de nível S1.
   Cada subscrição do Azure pode criar um hub IoT no nível livre. Para mais detalhes sobre as outras opções de nível, consulte [escolher o nível IoT Hub certo](../articles/iot-hub/iot-hub-scaling.md).

   - **Defender para IoT**: Ligue isto para adicionar uma camada extra de proteção contra ameaças ao IoT e aos seus dispositivos. Esta opção não está disponível para centros no nível livre. Para mais informações sobre esta funcionalidade, consulte [o Azure Security Center for IoT](/azure/asc-for-iot/).

   - **Definições Avançadas**  >  **Partições dispositivo-nuvem**: Esta propriedade relaciona as mensagens dispositivo-nuvem com o número de leitores simultâneos das mensagens. A maioria dos centros só precisa de quatro divisórias. Um hub de nível livre é limitado a duas divisórias.

1. Selecione **Seguinte: Tags** para continuar para o próximo ecrã.

   As etiquetas são pares de nome/valor. Pode atribuir a mesma etiqueta a múltiplos recursos e grupos de recursos para categorizar recursos e consolidar a faturação. Para mais informações, consulte [utilize etiquetas para organizar os seus recursos Azure.](../articles/azure-resource-manager/management/tag-resources.md)

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Atribua etiquetas para o hub utilizando o portal Azure.":::

1. Selecione **Seguinte: Review + create** para rever as suas escolhas. Vê algo semelhante a este ecrã, mas com os valores que selecionou ao criar o hub.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Reveja informações para criar o novo hub.":::

1. Anote o nome do hub IoT que escolheu. Vai utilizar este valor mais tarde no tutorial.
