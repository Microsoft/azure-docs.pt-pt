---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 01/07/2021
ms.author: alkohli
ms.openlocfilehash: c51577882e75facb1d8eb03c7cfab82467c5ec51
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "99524923"
---
Para configurar o cálculo no seu Azure Stack Edge Pro, irá criar um recurso IoT Hub através do portal Azure.

1. No portal Azure do seu recurso Azure Stack Edge, vá ao **Overview** e selecione **IoT Edge**.

   ![Começar com o computo](./media/azure-stack-edge-gateway-configure-compute/configure-compute-1.png)

2. No **serviço Enable IoT Edge**, selecione **Add**.

   ![Configure computação](./media/azure-stack-edge-gateway-configure-compute/configure-compute-2.png)

3. Na lâmina de **computação Configure Edge,** insira as seguintes informações:
   
    |Campo  |Valor  |
    |---------|---------|
    |Subscrição     |Selecione uma subscrição para o seu recurso IoT Hub. Pode utilizar a mesma subscrição que a utilizada pelo recurso Azure Stack Edge.         |
    |Grupo de recursos     |Selecione um grupo de recursos para o seu recurso IoT Hub. Pode utilizar o mesmo grupo de recursos que o utilizado pelo recurso Azure Stack Edge.         |
    |IoT Hub     | Escolha entre **Novo** ou **Já.** <br> Por predefinição, é utilizado um escalão Standard (S1) para criar um recurso IoT. Para utilizar um recurso IoT de escalão gratuito, crie um e, em seguida, selecione o recurso existente. <br> Em cada caso, o recurso do Hub IoT utiliza a mesma subscrição e o mesmo grupo de recursos utilizados pelo recurso do Azure Stack Edge.     |
    |Name     |Aceite o nome predefinido ou insira um nome para o seu recurso IoT Hub.         |

   ![Começar com o compute 2](./media/azure-stack-edge-gateway-configure-compute/configure-compute-3.png)

4. Quando terminar as definições, selecione **Review + Create**. Reveja as definições do seu recurso IoT Hub e selecione **Criar**.

   A criação de recursos para um recurso IoT Hub demora vários minutos. Após a criação do recurso, a **Visão Geral** indica que o serviço IoT Edge está agora em execução.

   ![Começar com o computo 3](./media/azure-stack-edge-gateway-configure-compute/configure-compute-4.png)

5. Para confirmar que a função de computação Edge foi configurada, vá à **IoT Edge > Properties**.

   ![Começar com o compute 4](./media/azure-stack-edge-gateway-configure-compute/configure-compute-5.png)

   Quando a função de computação Edge é configurada no dispositivo Edge, cria dois dispositivos: um dispositivo IoT e um dispositivo IoT Edge. Ambos os dispositivos podem ser vistos no recurso IoT Hub. Um IoT Edge Runtime também está a ser executado neste dispositivo IoT Edge. Neste momento, apenas a plataforma Linux está disponível para o seu dispositivo IoT Edge.

Pode levar 20 a 30 minutos para configurar o cálculo porque, nos bastidores, estão a ser criadas máquinas virtuais e um cluster Kubernetes.

Depois de configurar com sucesso o computação no portal Azure, existe um cluster Kubernetes e um utilizador padrão associado ao espaço de nome IoT (um espaço de nome do sistema controlado por Azure Stack Edge Pro).
