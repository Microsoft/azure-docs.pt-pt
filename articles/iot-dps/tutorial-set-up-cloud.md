---
title: Tutorial - Configurar nuvem para serviço de fornecimento de dispositivos Azure IoT Hub no portal
description: Este tutorial mostra como pode configurar os recursos em nuvem para o fornecimento de dispositivos no [portal Azure](https://portal.azure.com) utilizando o Serviço de Provisionamento de Dispositivos IoT Hub (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: f45c3def84c548ba12221efa59e9ebbd4699df71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91316074"
---
# <a name="tutorial-configure-cloud-resources-for-device-provisioning-with-the-iot-hub-device-provisioning-service"></a>Tutorial: Configurar recursos em nuvem para o fornecimento de dispositivos com o Serviço de Provisionamento de Dispositivos IoT Hub

Este tutorial mostra como configurar a cloud para o aprovisionamento de dispositivos com o Serviço Aprovisionamento de Dispositivos no Hub IoT. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para criar um Serviço Aprovisionamento de Dispositivos no Hub IoT e obter o âmbito do ID
> * Criar um hub IoT
> * Ligar o hub IoT ao Serviço Aprovisionamento de Dispositivos
> * Definir a política de alocação no Serviço Aprovisionamento de Dispositivos

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-a-device-provisioning-service-instance-and-get-the-id-scope"></a>Criar uma instância do Serviço Aprovisionamento de Dispositivos e obter o âmbito do ID

Siga estes passos para criar uma nova instância do Serviço Aprovisionamento de Dispositivos.

1. No canto superior esquerdo do portal do Azure, clique em **Criar um recurso**.

2. Na Caixa de pesquisa, escreva **aprovisionamento de dispositivos**. 

3. Clique em **Serviço Aprovisionamento de Dispositivos no Hub IoT**.

4. Preencha o formulário **Serviço Aprovisionamento de Dispositivos no Hub IoT** com as seguintes informações:
    
   | Definição       | Valor sugerido | Descrição | 
   | ------------ | ------------------ | ------------------------------------------------- | 
   | **Nome** | Qualquer nome exclusivo | -- | 
   | **Subscrição** | A sua subscrição  | Para obter detalhes sobre as suas subscrições, veja [Subscriptions](https://account.windowsazure.com/Subscriptions) (Subscrições). |
   | **Grupo de recursos** | myResourceGroup | Para nomes de grupo de recursos válidos, veja [Naming rules and restrictions](/azure/architecture/best-practices/resource-naming) (Atribuição de nomes de regras e restrições). |
   | **Localização** | Nenhuma localização válida | Para obter mais informações sobre regiões, veja [Azure Regions](https://azure.microsoft.com/regions/) (Regiões do Azure). |   

   ![Introduza as informações básicas do Serviço Aprovisionamento de Dispositivos no portal](./media/tutorial-set-up-cloud/create-iot-dps-portal.png)

5. Clique em **Criar**. Após alguns instantes, a instância do Serviço de Aprovisionamento de Dispositivos é criada e é apresentada a página **Descrição Geral**.

6. Na página **Descrição Geral** da nova instância de serviço, copie o valor do **Âmbito do ID** para utilização posterior. O valor é utilizado para identificar os IDs de registo e oferece uma garantia de que o ID do registo é exclusivo.

7. Além disso, copie o valor **Ponto final de serviço** para utilização posterior. 

## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]

### <a name="retrieve-connection-string-for-iot-hub"></a>Obter corda de ligação para o hub IoT

[!INCLUDE [iot-hub-include-find-connection-string](../../includes/iot-hub-include-find-connection-string.md)]

Criou o seu hub IoT e tem agora o nome de anfitrião e a cadeia de ligação do Hub IoT de que precisa para concluir este tutorial.

## <a name="link-the-device-provisioning-service-to-an-iot-hub"></a>Ligar o Serviço Aprovisionamento de Dispositivos ao hub IoT

O passo seguinte é ligar o Serviço Aprovisionamento de Dispositivos e de hub IoT, para que o Serviço Aprovisionamento de Dispositivos no Hub IoT possa registar os dispositivos nesse hub. O serviço apenas pode aprovisionar dispositivos para os hubs IoT que foram associados ao Serviço Aprovisionamento de Dispositivos. Siga estes passos.

1. Na página **Todos os recursos**, clique na instância de Serviço Aprovisionamento de Dispositivos que criou anteriormente.

2. Na página do Serviço Aprovisionamento de Dispositivos, clique em **Hubs IoT ligados**.

3. Clique em **Adicionar**.

4. Na página **Adicionar ligação ao hub IoT**, forneça as seguintes informações e clique em **Guardar**:

    * **Subscrição:** certifique-se de que está selecionada a subscrição que contém o hub IoT. Pode ligar ao hub IoT que reside numa subscrição diferente.

    * **Hub IoT:** escolha o nome do hub IoT que quer associar a esta instância do Serviço de Aprovisionamento de Dispositivos.

    * **Política de Acesso**: selecione **iothubowner** como as credenciais a utilizar para estabelecer a ligação ao hub IoT.

   ![Ligar o nome do hub para ligar ao Serviço Aprovisionamento de Dispositivos no portal](./media/tutorial-set-up-cloud/link-iot-hub-to-dps-portal.png)

## <a name="set-the-allocation-policy-on-the-device-provisioning-service"></a>Definir a política de alocação no Serviço Aprovisionamento de Dispositivos

A política de atribuição é uma definição de Serviço de Provisionamento de Dispositivos IoT Hub que determina como os dispositivos são atribuídos a um hub IoT. Existem três políticas de alocação suportadas: 

1. **Latência mais baixa**: os dispositivos são aprovisionados para um hub IoT com base no hub com a latência mais baixa para o dispositivo.

2. **Distribuição ponderada uniformemente** (predefinição): os hubs IoT associados são igualmente suscetíveis de ter dispositivos aprovisionados para eles. Esta é a predefinição. Se estiver a aprovisionar dispositivos apenas para um hub IoT, pode manter esta definição. 

3. **Configuração estática através da lista de inscrição**: a especificação do hub IoT pretendido na lista de inscrição tem prioridade sobre a política de alocação ao nível do Serviço Aprovisionamento de Dispositivos.

Para definir a política de alocação, na página do Serviço Aprovisionamento de Dispositivos, clique em **Gerir política de alocação**. Certifique-se de que a política de alocação está definida como **Distribuição ponderada uniformemente** (predefinição). Se realizar alterações, clique em **Guardar** quando tiver terminado.

![Gerir política de alocação](./media/tutorial-set-up-cloud/iot-dps-manage-allocation.png)

## <a name="clean-up-resources"></a>Limpar os recursos

Outros tutoriais desta coleção têm por base este tutorial. Se planeia continuar a trabalhar com inícios rápidos subsequentes ou com os tutoriais, não limpe os recursos criados neste tutorial. Se não quiser continuar, utilize os passos seguintes para eliminar todos os recursos criados por este tutorial no portal do Azure.

1. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione a sua instância de Serviço Aprovisionamento de Dispositivos. Na parte superior da página **Todos os recursos**, clique em **Eliminar**.  

2. No menu do lado esquerdo do portal do Azure, clique em **Todos os recursos** e selecione o seu hub IoT. Na parte superior da página **Todos os recursos**, clique em **Eliminar**.
 
## <a name="next-steps"></a>Passos seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Utilizar o portal do Azure para criar um Serviço Aprovisionamento de Dispositivos no Hub IoT e obter o âmbito do ID
> * Criar um hub IoT
> * Ligar o hub IoT ao Serviço Aprovisionamento de Dispositivos
> * Definir a política de alocação no Serviço Aprovisionamento de Dispositivos

Avance para o próximo tutorial para saber como configurar o dispositivo para aprovisionamento.

> [!div class="nextstepaction"]
> [Configurar dispositivo para aprovisionamento](tutorial-set-up-device.md)
