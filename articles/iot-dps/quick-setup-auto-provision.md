---
title: Configurar o serviço de provisionamento de dispositivos no Hub IoT no portal do Azure
description: Início rápido-configurar o DPS (serviço de provisionamento de dispositivos) do Hub IoT do Azure no portal do Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 765b7e5bb7341974a6a16d3f1ed0bcabf415f9f3
ms.sourcegitcommit: dbcc4569fde1bebb9df0a3ab6d4d3ff7f806d486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "76029173"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Início rápido: configurar o serviço de provisionamento de dispositivos no Hub IoT com o portal do Azure

O serviço de provisionamento de dispositivos no Hub IoT pode ser usado com o Hub IoT para habilitar o provisionamento sem toque, just-in-time para o Hub IoT desejado sem a necessidade de intervenção humana, permitindo que os clientes provisionem milhões de dispositivos IoT de maneira segura e escalonável. O serviço de provisionamento de dispositivos no Hub IoT do Azure dá suporte a dispositivos IoT com autenticações de certificado de TPM, chave simétrica e X. 509. Para obter mais informações, consulte [visão geral do serviço de provisionamento de dispositivos no Hub IOT](./about-iot-dps.md)

Neste guia de início rápido, você aprenderá a configurar o serviço de provisionamento de dispositivos do Hub IoT no portal do Azure para provisionar seus dispositivos com as seguintes etapas:
> [!div class="checklist"]
> * Usar o portal do Azure para criar um hub IoT
> * Utilizar o portal do Azure para criar um Serviço Aprovisionamento de Dispositivos no Hub IoT e obter o âmbito do ID
> * Ligar o hub IoT ao Serviço Aprovisionamento de Dispositivos


Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Criar um novo serviço de provisionamento de dispositivos no Hub IoT

1. Selecione o botão **+ criar um recurso** novamente.

2. *Pesquise no Marketplace* o **serviço de provisionamento de dispositivos**. Selecione o **serviço de provisionamento de dispositivos no Hub IOT** e clique no botão **criar** . 

3. Forneça as seguintes informações para a nova instância do serviço de provisionamento de dispositivos e clique em **criar**.

    * **Nome**: forneça um nome exclusivo para a nova instância do Serviço de Aprovisionamento de Dispositivos. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.
    * **Subscrição**: escolha a subscrição que quer utilizar para criar a instância do Serviço de Aprovisionamento de Dispositivos.
    * **Grupo de recursos**: este campo permite criar um novo grupo de recursos ou selecionar um já existente para conter a nova instância. Selecione o mesmo grupo de recursos que contém o hub Iot que criou acima, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados num grupo, pode geri-los em conjunto. Por exemplo, eliminar o grupo de recursos elimina todos os recursos que se encontram nesse grupo. Para obter mais informações, consulte [Manage Azure Resource Manager Resource groups](../azure-resource-manager/management/manage-resource-groups-portal.md).
    * **Localização**: selecione a localização mais próxima dos seus dispositivos.

      ![Insira informações básicas sobre a instância do serviço de provisionamento de dispositivos na folha do portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Selecione o botão de notificação para monitorar a criação da instância de recurso. Depois que o serviço for implantado com êxito, selecione **fixar no painel**e **vá para recurso**.

    ![Notificação Monitorizar a implementação](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Vincular o Hub IoT e o serviço de provisionamento de dispositivos

Nesta seção, você adicionará uma configuração à instância do serviço de provisionamento de dispositivos. Esta configuração define o hub IoT para o qual os dispositivos serão aprovisionados.

1. Selecione o botão **todos os recursos** no menu à esquerda da portal do Azure. Selecione a instância do Serviço Aprovisionamento de Dispositivos que criou na secção anterior. 

    Se o menu estiver configurado usando o **submenu** em vez do modo **encaixado** nas configurações do portal, você precisará clicar nas 3 linhas na parte superior esquerda para abrir o menu do portal à esquerda.  

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
> [Início rápido para criar um dispositivo simulado](./quick-create-simulated-device-symm-key.md)
