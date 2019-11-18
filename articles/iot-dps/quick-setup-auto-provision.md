---
title: 'Início rápido: configurar o provisionamento de dispositivos no portal do Azure'
description: Início rápido do Azure-configurar o serviço de provisionamento de dispositivos no Hub IoT do Azure no portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
manager: timlt
ms.custom: mvc
ms.openlocfilehash: 498ec758fe8ad5d596a62d3541b39d860336e726
ms.sourcegitcommit: 5cfe977783f02cd045023a1645ac42b8d82223bd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/17/2019
ms.locfileid: "74151281"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Início rápido: configurar o serviço de provisionamento de dispositivos no Hub IoT com o portal do Azure

Estes passos mostram como configurar os recursos da cloud do Azure no portal para aprovisionar os seus dispositivos. Este artigo inclui passos para: criar o hub IoT, criar um novo Serviço de Aprovisionamento de Dispositivos no Hub IoT e ligar os dois serviços. 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-instance-for-the-iot-hub-device-provisioning-service"></a>Criar uma instância nova do Serviço Aprovisionamento de Dispositivos no Hub IoT

1. Selecione o botão **+ criar um recurso** novamente.

2. *Pesquise no Marketplace* o **serviço de provisionamento de dispositivos**. Selecione o **serviço de provisionamento de dispositivos no Hub IOT** e clique no botão **criar** . 

3. Forneça as seguintes informações para a nova instância do serviço de provisionamento de dispositivos e clique em **criar**.

    * **Nome**: forneça um nome exclusivo para a nova instância do Serviço de Aprovisionamento de Dispositivos. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.
    * **Subscrição**: escolha a subscrição que quer utilizar para criar a instância do Serviço de Aprovisionamento de Dispositivos.
    * **Grupo de recursos**: este campo permite criar um novo grupo de recursos ou selecionar um já existente para conter a nova instância. Selecione o mesmo grupo de recursos que contém o hub Iot que criou acima, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados num grupo, pode geri-los em conjunto. Por exemplo, eliminar o grupo de recursos elimina todos os recursos que se encontram nesse grupo. Para obter mais informações, consulte [Manage Azure Resource Manager Resource groups](../azure-resource-manager/manage-resource-groups-portal.md).
    * **Localização**: selecione a localização mais próxima dos seus dispositivos.

      ![Insira informações básicas sobre a instância do serviço de provisionamento de dispositivos na folha do portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Selecione o botão de notificação para monitorar a criação da instância de recurso. Depois que o serviço for implantado com êxito, selecione **fixar no painel**e **vá para recurso**.

    ![Notificação Monitorizar a implementação](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Vincular o Hub IoT e o serviço de provisionamento de dispositivos

Nesta seção, você adicionará uma configuração à instância do serviço de provisionamento de dispositivos. Esta configuração define o hub IoT para o qual os dispositivos serão aprovisionados.

1. Selecione o botão **todos os recursos** no menu à esquerda da portal do Azure. Selecione a instância do Serviço Aprovisionamento de Dispositivos que criou na secção anterior.  

2. No menu do serviço de provisionamento de dispositivos, selecione **hubs IOT vinculados**. Pressione o botão **+ Adicionar** visto na parte superior. 

3. Na página **Adicionar ligação ao hub IoT**, forneça as seguintes informações para ligar a nova instância do Serviço de Aprovisionamento de Dispositivos a um hub IoT. Em seguida, clique em **salvar**. 

    * **Subscrição**: selecione a subscrição que contém o hub IoT que quer ligar à nova instância do Serviço de Aprovisionamento de Dispositivos.
    * **Hub IoT**: selecione o hub IoT a ligar à nova instância do Serviço de Aprovisionamento de Dispositivos.
    * **Política de Acesso**: selecione **iothubowner** como as credenciais para estabelecer a ligação ao hub IoT.  

      ![Vincular o nome do Hub ao link para a instância do serviço de provisionamento de dispositivos na folha do portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Agora, deverá ver o hub selecionado no painel **Hubs IoT ligados**. Talvez seja necessário clicar em **Atualizar** para que ele apareça.


## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o serviço de provisionamento de dispositivos. Na parte superior do painel de detalhes do dispositivo, selecione **excluir**.  
2. No menu à esquerda na portal do Azure, selecione **todos os recursos** e, em seguida, selecione o Hub IOT. Na parte superior do painel de detalhes do Hub, selecione **excluir**.  

## <a name="next-steps"></a>Passos seguintes

Neste guia de início rápido, você implantou um hub IoT e uma instância do serviço de provisionamento de dispositivos e vinculou os dois recursos. Para saber como usar essa configuração para provisionar um dispositivo simulado, continue no início rápido para criar um dispositivo simulado.

> [!div class="nextstepaction"]
> [Início rápido para criar um dispositivo simulado](./quick-create-simulated-device.md)
