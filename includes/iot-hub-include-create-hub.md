---
title: incluir ficheiro
description: incluir ficheiro
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: cf651a1a09662e3084a8a9bdb6365b69b6ea52b5
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99580357"
---
Esta secção descreve como criar um hub IoT utilizando o [portal Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. A partir da página inicial do Azure, selecione o **botão + Criar um** botão de recurso e, em seguida, introduza o *Hub IoT* no campo **'Pesquisar o Mercado'.**

1. Selecione **IoT Hub** a partir dos resultados da pesquisa e, em seguida, selecione **Criar**.

1. No separador **Básicos,** complete os campos da seguinte forma:

   - **Subscrição**: Selecione a subscrição para usar para o seu hub.

   - **Grupo de Recursos**: Selecione um grupo de recursos ou crie um novo. Para criar um novo, **selecione Criar novo** e preencha o nome que pretende utilizar. Para utilizar um grupo de recursos existente, selecione esse grupo de recursos. Para obter mais informações, consulte os grupos de [recursos Do Gestor de Recursos Azure.](../articles/azure-resource-manager/management/manage-resource-groups-portal.md)

   - **Região**: Selecione a região em que deseja que o seu centro esteja localizado. Selecione o local mais próximo de si. Algumas funcionalidades, como [os fluxos de dispositivos IoT Hub,](../articles/iot-hub/iot-hub-device-streams-overview.md)só estão disponíveis em regiões específicas. Para estas características limitadas, deve selecionar uma das regiões apoiadas.

   - **IoT Hub Name**: Insira um nome para o seu hub. Este nome tem de ser globalmente exclusivo.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png" alt-text="Crie um hub no portal Azure.":::

1. Selecione **Seguinte: Networking** para continuar a criar o seu hub.

   Escolha os pontos finais que podem ligar-se ao seu Hub IoT. Pode selecionar o ponto final público por defeito **(todas as redes)** ou escolher **o ponto final público (intervalos IP selecionados)** ou ponto final **privado**. Aceite a definição padrão para este exemplo.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-network-screen.png" alt-text="Escolha os pontos finais que podem ligar.":::

1. Selecione **Seguinte: Gestão** para continuar a criar o seu hub.

   :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-management.png" alt-text="Desconfiem do tamanho e da escala para um novo hub utilizando o portal Azure.":::

    Pode aceitar as definições predefinidos aqui. Se desejar, pode modificar qualquer um dos seguintes campos:

    - **Preço e nível de escala**: O seu nível selecionado. Pode escolher entre vários níveis, dependendo de quantas funcionalidades pretende e de quantas mensagens envia através da sua solução por dia. O nível livre destina-se a testes e avaliação. Permite que 500 dispositivos sejam ligados ao hub e até 8.000 mensagens por dia. Cada subscrição do Azure pode criar um hub IoT no nível livre.

      Se estiver a trabalhar através de um Quickstart para streams de dispositivos IoT Hub, selecione o nível livre.

    - **Unidades IoT Hub**: O número de mensagens permitidas por unidade por dia depende do nível de preços do seu hub. Por exemplo, se quiser que o hub suporte uma entrada de 700.000 mensagens, escolha duas unidades de nível S1.
    Para mais detalhes sobre as outras opções de nível, consulte [escolher o nível IoT Hub certo](../articles/iot-hub/iot-hub-scaling.md).

    - **Defender para IoT**: Ligue isto para adicionar uma camada extra de proteção contra ameaças ao IoT e aos seus dispositivos. Esta opção não está disponível para centros no nível livre. Para mais informações sobre esta funcionalidade, consulte [o Azure Security Center for IoT](/azure/asc-for-iot/).

    - **Definições Avançadas**  >  **Partições dispositivo-nuvem**: Esta propriedade relaciona as mensagens dispositivo-nuvem com o número de leitores simultâneos das mensagens. A maioria dos centros só precisa de quatro divisórias.

1. Selecione **Seguinte: Tags** para continuar para o próximo ecrã.

    As etiquetas são pares de nome/valor. Pode atribuir a mesma etiqueta a múltiplos recursos e grupos de recursos para categorizar recursos e consolidar a faturação. Para mais informações, consulte [utilize etiquetas para organizar os seus recursos Azure.](../articles/azure-resource-manager/management/tag-resources.md)

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-create-tags.png" alt-text="Atribua etiquetas para o hub utilizando o portal Azure.":::

1. Selecione **Seguinte: Review + create** para rever as suas escolhas. Vê algo semelhante a este ecrã, mas com os valores que selecionou ao criar o hub.

    :::image type="content" source="./media/iot-hub-include-create-hub/iot-hub-review-and-create.png" alt-text="Reveja informações para criar o novo hub.":::

1. Selecione **Criar** para criar o seu novo hub. Criar o centro leva alguns minutos.