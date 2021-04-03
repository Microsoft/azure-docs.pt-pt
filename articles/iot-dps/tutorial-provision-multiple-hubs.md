---
title: Tutorial - Dispositivos de provisão através de centros equilibrados de carga utilizando o Serviço de Provisionamento de Dispositivos Azure IoT Hub
description: Este tutorial demonstra como o Serviço de Fornecimento de Dispositivos (DPS) permite o fornecimento automático de dispositivos através de hubs IoT equilibrados de carga no Portal Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 6cca34774038156cfb01e872e751338c1c96efaa
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91316005"
---
# <a name="tutorial-provision-devices-across-load-balanced-iot-hubs"></a>Tutorial: Dispositivos de provisão através de hubs IoT equilibrados em carga

Este tutorial mostra como aprovisionar dispositivos para vários hubs IoT com balanceamento de carga através do Serviço Aprovisionamento de Dispositivos. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para aprovisionar um segundo dispositivo para um segundo hub IoT 
> * Adicionar uma entrada da lista de inscrição ao segundo dispositivo
> * Definir a política de alocação do Serviço Aprovisionamento de Dispositivos para **distribuição uniforme**
> * Ligar o novo hub IoT ao Serviço Aprovisionamento de Dispositivos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Este tutorial é baseado no tutorial anterior [Aprovisionar dispositivo num hub](tutorial-provision-device-to-hub.md).

## <a name="use-the-azure-portal-to-provision-a-second-device-to-a-second-iot-hub"></a>Utilizar o portal do Azure para aprovisionar um segundo dispositivo para um segundo hub IoT

Siga os passos no tutorial [Aprovisionar dispositivo num hub](tutorial-provision-device-to-hub.md) para aprovisionar um segundo dispositivo noutro hub IoT.

## <a name="add-an-enrollment-list-entry-to-the-second-device"></a>Adicionar uma entrada da lista de inscrição ao segundo dispositivo

A lista de inscrição informa o Serviço Aprovisionamento de Dispositivos sobre o método de atestado (o método para confirmar a identidade do dispositivo) que está a utilizar no dispositivo. O passo seguinte consiste em adicionar uma entrada da lista de inscrição para o segundo dispositivo. 

1. Na página do seu Serviço Aprovisionamento de Dispositivos, clique em **Gerir inscrições**. A página **Adicionar entrada da lista de inscrição** é apresentada. 
2. No início da página, clique em **Adicionar**.
2. Preencha os campos e, em seguida, clique em **Guardar**.

## <a name="set-the-device-provisioning-service-allocation-policy"></a>Definir a política de alocação no Serviço Aprovisionamento de Dispositivos

A política de alocação é uma definição do Serviço Aprovisionamento de Dispositivos que determina a forma como os dispositivos são atribuídos a um hub IoT. Existem três políticas de alocação suportadas: 

1. **Latência mais baixa**: os dispositivos são aprovisionados para um hub IoT com base no hub com a latência mais baixa para o dispositivo.
2. **Distribuição ponderada uniformemente** (predefinição): os hubs IoT associados são igualmente suscetíveis de ter dispositivos aprovisionados para eles. Esta é a predefinição. Se estiver a aprovisionar dispositivos apenas para um hub IoT, pode manter esta definição. 
3. **Configuração estática através da lista de inscrição**: a especificação do hub IoT pretendido na lista de inscrição tem prioridade sobre a política de alocação ao nível do Serviço Aprovisionamento de Dispositivos.

Siga estes passos para definir a política de alocação:

1. Para definir a política de alocação, na página do Serviço Aprovisionamento de Dispositivos, clique em **Gerir política de alocação**.
2. Defina a política de alocação para **Distribuição ponderada uniformemente**.
3. Clique em **Guardar**.

## <a name="link-the-new-iot-hub-to-the-device-provisioning-service"></a>Ligar o novo hub IoT ao Serviço Aprovisionamento de Dispositivos

Ligar o Serviço Aprovisionamento de Dispositivos e o Hub IoT para que o Serviço Aprovisionamento de Dispositivos possa registar dispositivos nesse hub.

1. Na página **Todos os recursos**, clique no Serviço Aprovisionamento de Dispositivos que criou anteriormente.
2. Na página do Serviço Aprovisionamento de Dispositivos, clique em **Hubs IoT ligados**.
3. Clique em **Adicionar**.
4. Na página **Adicionar ligação ao hub IoT**, utilize os botões de opção para especificar se o hub IoT associado se encontra na subscrição atual ou numa subscrição diferente. Em seguida, escolha o nome do hub IoT na caixa **hub IoT**.
5. Clique em **Guardar**.

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para aprovisionar um segundo dispositivo para um segundo hub IoT 
> * Adicionar uma entrada da lista de inscrição ao segundo dispositivo
> * Definir a política de alocação do Serviço Aprovisionamento de Dispositivos para **distribuição uniforme**
> * Ligar o novo hub IoT ao Serviço Aprovisionamento de Dispositivos

## <a name="next-steps"></a>Passos seguintes

<!-- Advance to the next tutorial to learn how to 
 Replace this .md
> [!div class="nextstepaction"]
> [Bind an existing custom SSL certificate to Azure Web Apps]()
-->
