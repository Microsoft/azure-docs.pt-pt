---
title: ficheiro de inclusão
description: ficheiro de inclusão
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 367a0b1d17f8d5ebe4f46835ace963b00e75354e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68229276"
---
Criar um Hub IoT com o portal do Azure:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).

1. Selecione **Criar um recurso**Internet of  >  **Things**  >  **IoT Hub**.

    ![Selecione para instalar o Hub IoT](media/iot-hub-tutorials-create-free-hub/selectiothub.png)

1. Para criar o hub IoT de escalão gratuito, utilize os valores da tabela seguinte:

    | Definição | Valor |
    | ------- | ----- |
    | Subscrição | Selecione a sua subscrição do Azure na lista pendente. |
    | Grupo de recursos | Crie um novo. Este tutorial utiliza o nome **tutoriais-iot-hub-rg**. |
    | Region | Este tutorial utiliza **E.U.A. Oeste**. Pode escolher a região mais próxima para si. |
    | Nome | A captura de ecrã seguinte utiliza o nome **tutoriais-iot-hub**. Tem de escolher o seu próprio nome exclusivo quando criar o hub. |

    ![Definições do hub 1](media/iot-hub-tutorials-create-free-hub/hubdefinition-1.png)

    | Definição | Valor |
    | ------- | ----- |
    | Escalão de preço e dimensionamento | F1 Gratuito. Só pode ter um hub de escalão gratuito numa subscrição. |
    | Unidades do Hub IoT | 1 |

    ![Definições do hub 2](media/iot-hub-tutorials-create-free-hub/hubdefinition-2.png)

1. Clique em **Criar**. A criação do hub pode demorar alguns minutos.

    ![Definições do hub 3](media/iot-hub-tutorials-create-free-hub/hubdefinition-3.png)

1. Anote o nome do hub IoT que escolheu. Vai utilizar este valor mais tarde no tutorial.
