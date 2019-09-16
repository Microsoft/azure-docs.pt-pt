---
title: Registe-se um dispositivo novo a partir do portal do Azure – Azure IoT Edge | Documentos da Microsoft
description: Utilizar o portal do Azure para registar um novo dispositivo IoT Edge e obter a cadeia de ligação
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 06/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 7f3d0037bcf0fd33ae23c298679e3157046247cb
ms.sourcegitcommit: 909ca340773b7b6db87d3fb60d1978136d2a96b0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/13/2019
ms.locfileid: "70983531"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registar um novo dispositivo Azure IoT Edge do portal do Azure

Antes de poder utilizar os seus dispositivos IoT com o Azure IoT Edge, tem de registá-los com o seu hub IoT. Depois de registrar um dispositivo, você recebe uma cadeia de conexão que pode ser usada para configurar seu dispositivo para cargas de trabalho de IoT Edge.

Este artigo mostra como registar um novo dispositivo IoT Edge com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Um [Hub IOT](../iot-hub/iot-hub-create-through-portal.md) gratuito ou Standard em sua assinatura do Azure.

## <a name="create-a-device"></a>Criar um dispositivo

No portal do Azure, os dispositivos do IoT Edge são criados e gerenciados separadamente a partir de dispositivos que ligar ao seu hub IoT, mas não são habilitados no edge.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
2. Selecione **IoT Edge** no menu.
3. Selecione **Adicionar um dispositivo IOT Edge**.
4. Forneça um ID de dispositivo descritivo. Use as configurações padrão para gerar automaticamente chaves de autenticação e conectar o novo dispositivo ao seu hub.
5. Selecione **Guardar**.

## <a name="view-all-devices"></a>Ver todos os dispositivos

São listados todos os dispositivos habilitados para edge que se ligam ao seu hub IoT a **IoT Edge** página.

## <a name="retrieve-the-connection-string"></a>Obter a cadeia de ligação

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub.

1. Na página **IOT Edge** no portal, clique na ID do dispositivo na lista de dispositivos IOT Edge.
2. Copie o valor de qualquer um **cadeia de ligação (chave primária)** ou **cadeia de ligação (chave secundária)** .

## <a name="next-steps"></a>Passos seguintes

Saiba como [implantar módulos em um dispositivo com o portal do Azure](how-to-deploy-modules-portal.md).
