---
title: Registe-se um dispositivo novo a partir do portal do Azure – Azure IoT Edge | Documentos da Microsoft
description: Utilizar o portal do Azure para registar um novo dispositivo IoT Edge e obter a cadeia de ligação
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 01/03/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 6414f694296ce1f5a8b65ccab30cceaf2172dee7
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62126327"
---
# <a name="register-a-new-azure-iot-edge-device-from-the-azure-portal"></a>Registar um novo dispositivo Azure IoT Edge do portal do Azure

Antes de poder utilizar os seus dispositivos IoT com o Azure IoT Edge, tem de registá-los com o seu hub IoT. Depois de se registar um dispositivo, receberá uma cadeia de ligação que pode ser utilizada para configurar o dispositivo para cargas de trabalho do Edge.

Este artigo mostra como registar um novo dispositivo IoT Edge com o portal do Azure.

## <a name="prerequisites"></a>Pré-requisitos

Uma [IoT hub](../iot-hub/iot-hub-create-through-portal.md) na sua subscrição do Azure.

## <a name="create-a-device"></a>Criar um dispositivo

No portal do Azure, os dispositivos do IoT Edge são criados e gerenciados separadamente a partir de dispositivos que ligar ao seu hub IoT, mas não são habilitados no edge.

1. Inicie sessão para o [portal do Azure](https://portal.azure.com) e navegue até ao seu hub IoT.
2. Selecione **IoT Edge** no menu.
3. Selecione **adicionar um dispositivo IoT Edge**.
4. Forneça um ID de dispositivo descritivo. Utilize as predefinições para a criação automática de chaves de autenticação e ligar o novo dispositivo ao seu hub.
5. Selecione **Guardar**.

## <a name="view-all-devices"></a>Ver todos os dispositivos

São listados todos os dispositivos habilitados para edge que se ligam ao seu hub IoT a **IoT Edge** página.

## <a name="retrieve-the-connection-string"></a>Obter a cadeia de ligação

Quando estiver pronto para configurar o dispositivo, terá da cadeia de ligação que liga o dispositivo físico com a respetiva identidade no IoT hub.

1. Partir do **IoT Edge** página no portal, clique no ID de dispositivo da lista de dispositivos periféricos.
2. Copie o valor de qualquer um **cadeia de ligação (chave primária)** ou **cadeia de ligação (chave secundária)**.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [implementar módulos num dispositivo com o portal do Azure](how-to-deploy-modules-portal.md)
