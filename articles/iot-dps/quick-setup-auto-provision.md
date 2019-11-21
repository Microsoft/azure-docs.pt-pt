---
title: Set up IoT Hub Device Provisioning Service in the Azure portal
description: Quickstart - Set up the Azure IoT Hub Device Provisioning Service in the Azure portal
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: b983a9591743b1fda79e23aedc1aca88add2a3e2
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74228526"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Quickstart: Set up the IoT Hub Device Provisioning Service with the Azure portal

Estes passos mostram como configurar os recursos da cloud do Azure no portal para aprovisionar os seus dispositivos. Este artigo inclui passos para: criar o hub IoT, criar um novo Serviço de Aprovisionamento de Dispositivos no Hub IoT e ligar os dois serviços. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Criar uma instância nova do Serviço Aprovisionamento de Dispositivos no Hub IoT

1. Select the **+ Create a resource** button again.

2. *Search the Marketplace* for the **Device Provisioning Service**. Select **IoT Hub Device Provisioning Service** and hit the **Create** button. 

3. Provide the following information for your new Device Provisioning Service instance and hit **Create**.

    * **Nome**: forneça um nome exclusivo para a nova instância do Serviço de Aprovisionamento de Dispositivos. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.
    * **Subscrição**: escolha a subscrição que quer utilizar para criar a instância do Serviço de Aprovisionamento de Dispositivos.
    * **Grupo de recursos**: este campo permite criar um novo grupo de recursos ou selecionar um já existente para conter a nova instância. Selecione o mesmo grupo de recursos que contém o hub Iot que criou acima, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados num grupo, pode geri-los em conjunto. Por exemplo, eliminar o grupo de recursos elimina todos os recursos que se encontram nesse grupo. For more information, see [Manage Azure Resource Manager resource groups](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Localização**: selecione a localização mais próxima dos seus dispositivos.

      ![Enter basic information about your Device Provisioning Service instance in the portal blade](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Select the notification button to monitor the creation of the resource instance. Once the service is successfully deployed, select **Pin to dashboard**, and then **Go to resource**.

    ![Notificação Monitorizar a implementação](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Link the IoT hub and your Device Provisioning Service

In this section, you will add a configuration to the Device Provisioning Service instance. Esta configuração define o hub IoT para o qual os dispositivos serão aprovisionados.

1. Select the **All resources** button from the left-hand menu of the Azure portal. Selecione a instância do Serviço Aprovisionamento de Dispositivos que criou na secção anterior.  

2. From the Device Provisioning Service's menu, select **Linked IoT hubs**. Hit the **+ Add** button seen at the top. 

3. Na página **Adicionar ligação ao hub IoT**, forneça as seguintes informações para ligar a nova instância do Serviço de Aprovisionamento de Dispositivos a um hub IoT. Then hit **Save**. 

    * **Subscrição**: selecione a subscrição que contém o hub IoT que quer ligar à nova instância do Serviço de Aprovisionamento de Dispositivos.
    * **Hub IoT**: selecione o hub IoT a ligar à nova instância do Serviço de Aprovisionamento de Dispositivos.
    * **Política de Acesso**: selecione **iothubowner** como as credenciais para estabelecer a ligação ao hub IoT.  

      ![Link the hub name to link to the Device Provisioning Service instance in the portal blade](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Agora, deverá ver o hub selecionado no painel **Hubs IoT ligados**. You might need to hit **Refresh** for it to show up.


## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1. From the left-hand menu in the Azure portal, select **All resources** and then select your Device Provisioning Service. At the top of the device detail pane, select **Delete**.  
2. From the left-hand menu in the Azure portal, select **All resources** and then select your IoT hub. At the top of the hub detail pane, select **Delete**.  

## <a name="next-steps"></a>Passos seguintes

In this quickstart, you’ve deployed an IoT hub and a Device Provisioning Service instance, and linked the two resources. To learn how to use this setup to provision a simulated device, continue to the quickstart for creating a simulated device.

> [!div class="nextstepaction"]
> [Quickstart to create a simulated device](./quick-create-simulated-device.md)
