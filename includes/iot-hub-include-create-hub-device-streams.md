---
title: incluir arquivo (visualização de fluxos de dispositivo)
description: incluir arquivo (visualização de fluxos de dispositivo)
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 08/20/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 46f8e59713896cd94b96de62d982072119c32513
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70050172"
---
Esta seção descreve como criar um hub IoT usando o [portal do Azure](https://portal.azure.com).

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Escolha **criar um recurso**e, em seguida, insira *Hub IOT* no campo **Pesquisar no Marketplace** .

1. Selecione **Hub IOT** nos resultados da pesquisa e, em seguida, selecione **criar**.

1. Na guia **noções básicas** , preencha os campos da seguinte maneira:

   - **Subscrição**: Selecione a assinatura a ser usada para o Hub.

   - **Grupo de recursos**: Selecione um grupo de recursos ou crie um novo. Para criar um novo, selecione **criar novo** e preencha o nome que você deseja usar. Para usar um grupo de recursos existente, selecione esse grupo de recursos. Para obter mais informações, consulte [Manage Azure Resource Manager Resource groups](../articles/azure-resource-manager/manage-resource-groups-portal.md).

   - **Região**: Escolha a região na qual você deseja que o Hub seja localizado. Selecione uma região que dê suporte à visualização de fluxos de dispositivo do Hub IoT, seja **EUA Central** ou **EUA Central EUAP**.

   - **Nome do Hub IOT**: Insira um nome para o Hub. Este nome tem de ser globalmente exclusivo. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.

   ![Criando um hub IoT no portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-device-streams.png)

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

1. Selecione **avançar: Tamanho e escala** para continuar criando o Hub.

   ![Definindo o tamanho e a escala de um novo hub IoT usando o portal do Azure](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-02.png)

   Em **tamanho e escala**, você pode aceitar as configurações padrão e selecionar **revisar + criar** na parte inferior. Considere as seguintes opções:

   - **Camada de preços e escala**: Sua camada selecionada. Selecione uma das camadas padrão (**S1**, **S2**ou **S3**) ou **F1: Camada**gratuita. Essa opção também pode ser guiada pelo tamanho de sua frota e pelas cargas de trabalho que não são de streaming que você espera em seu hub, por exemplo, mensagens de telemetria. Por exemplo, a camada gratuita destina-se a testes e avaliação. Ele permite que os dispositivos 500 sejam conectados ao Hub IoT e até 8.000 mensagens por dia. Cada assinatura do Azure pode criar um hub IoT na camada gratuita. 

   - **Número de unidades do Hub IOT**: O número de mensagens permitidas por unidade por dia depende do tipo de preço do seu hub. Essa opção depende da carga de trabalho que não é de streaming que você espera em seu hub. Você pode selecionar 1 por enquanto.

   - Configurações > avançadas**partições de dispositivo para nuvem**: Essa propriedade relaciona as mensagens do dispositivo para a nuvem com o número de leitores simultâneos das mensagens. A maioria dos hubs só precisa de quatro partições.

   Para obter mais informações sobre as opções de camada, consulte [escolher a camada certa do Hub IOT](../articles/iot-hub/iot-hub-scaling.md).

1. Para revisar suas escolhas, escolha revisar **+ criar**. Os resultados serão semelhantes ao seguinte:

   ![Informações para criar o novo hub IoT](./media/iot-hub-include-create-hub-device-streams/iot-hub-creation-03.png)

1. Para criar seu novo hub IoT, selecione **criar**. O processo leva alguns minutos.
