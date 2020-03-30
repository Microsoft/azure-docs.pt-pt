---
title: Configurar o Serviço de Provisionamento de Dispositivos IoT Hub no portal Azure
description: Quickstart - Instale o Serviço de Provisionamento de Dispositivos Hub Azure IoT (DPS) no portal Azure
author: wesmc7777
ms.author: wesmc
ms.date: 11/08/2019
ms.topic: quickstart
ms.service: iot-dps
services: iot-dps
ms.custom: mvc
ms.openlocfilehash: 765b7e5bb7341974a6a16d3f1ed0bcabf415f9f3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "76029173"
---
# <a name="quickstart-set-up-the-iot-hub-device-provisioning-service-with-the-azure-portal"></a>Quickstart: Instale o Serviço de Provisionamento de Dispositivos IoT Hub com o portal Azure

O Serviço de Provisionamento de Dispositivos IoT Hub pode ser utilizado com o IoT Hub para permitir o fornecimento de zero toques, just-in-time, ao centro IoT desejado sem necessitar de intervenção humana, permitindo aos clientes fornecer milhões de dispositivos IoT de forma segura e escalável. O Serviço de Provisionamento de Dispositivos Azure IoT Hub suporta dispositivos IoT com TPM, chave simétrica e autenticações de certificadoX.509. Para mais informações, consulte a visão geral do [Serviço de Provisionamento de Dispositivos IoT Hub](./about-iot-dps.md)

Neste arranque rápido, você aprenderá a configurar o Serviço de Provisionamento de Dispositivos IoT Hub no Portal Azure para fornecer os seus dispositivos com os seguintes passos:
> [!div class="checklist"]
> * Use o portal Azure para criar um Hub IoT
> * Utilizar o portal do Azure para criar um Serviço Aprovisionamento de Dispositivos no Hub IoT e obter o âmbito do ID
> * Ligar o hub IoT ao Serviço Aprovisionamento de Dispositivos


Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.


## <a name="create-an-iot-hub"></a>Criar um hub IoT

[!INCLUDE [iot-hub-include-create-hub](../../includes/iot-hub-include-create-hub.md)]


## <a name="create-a-new-iot-hub-device-provisioning-service"></a>Criar um novo serviço de provisionamento de dispositivos IoT Hub

1. Selecione o **+ Criar um** botão de recurso novamente.

2. *Pesquise no Mercado* o Serviço de Provisionamento de **Dispositivos**. Selecione **IoT Hub Device Provisioning Service** e acerte no botão **Criar.** 

3. Forneça as seguintes informações para a sua nova instância de Serviço de Provisionamento de Dispositivos e atinja **create**.

    * **Nome:** Forneça um nome único para a sua nova instância de Serviço de Provisionamento de Dispositivos. Se o nome introduzido estiver disponível, aparece uma marca de verificação verde.
    * **Subscrição:** Escolha a subscrição que pretende utilizar para criar esta instância de Serviço de Provisionamento de Dispositivos.
    * **Grupo de recursos**: este campo permite criar um novo grupo de recursos ou selecionar um já existente para conter a nova instância. Selecione o mesmo grupo de recursos que contém o hub Iot que criou acima, por exemplo, **TestResources**. Ao colocar todos os recursos relacionados num grupo, pode geri-los em conjunto. Por exemplo, eliminar o grupo de recursos elimina todos os recursos que se encontram nesse grupo. Para mais informações, consulte [Manage Azure Resource Manager grupos](../azure-resource-manager/management/manage-resource-groups-portal.md)de recursos .
    * **Localização:** Selecione a localização mais próxima dos seus dispositivos.

      ![Introduza informações básicas sobre a sua instância de Serviço de Provisionamento de Dispositivos na lâmina do portal](./media/quick-setup-auto-provision/create-iot-dps-portal.png)  

4. Selecione o botão de notificação para monitorizar a criação da instância de recursos. Uma vez que o serviço seja implementado com sucesso, selecione **Pin para dashboard**, e depois vá para o **recurso**.

    ![Notificação Monitorizar a implementação](./media/quick-setup-auto-provision/pin-to-dashboard.png)

## <a name="link-the-iot-hub-and-your-device-provisioning-service"></a>Ligue o hub IoT e o seu Serviço de Provisionamento de Dispositivos

Nesta secção, irá adicionar uma configuração à instância do Serviço de Provisionamento de Dispositivos. Esta configuração define o hub IoT para o qual os dispositivos serão aprovisionados.

1. Selecione o botão **Todos os recursos** do menu à esquerda do portal Azure. Selecione a instância do Serviço Aprovisionamento de Dispositivos que criou na secção anterior. 

    Se o seu menu estiver configurado utilizando o **Flyout** em vez do modo **Docked** nas definições do portal, terá de clicar nas 3 linhas na parte superior esquerda para abrir o menu do portal à esquerda.  

2. A partir do menu do Serviço de Provisionamento de Dispositivos, selecione **hubs Linked IoT**. Acerta no botão **+ Adicionar** visto na parte superior. 

3. No link Adicionar à página **do hub IoT,** forneça as seguintes informações para ligar a sua nova instância de Serviço de Provisionamento de Dispositivos a um hub IoT. Em seguida, bata **save**. 

    * **Subscrição:** Selecione a subscrição que contém o hub IoT que pretende ligar com a sua nova instância de Serviço de Provisionamento de Dispositivos.
    * **Centro de iot:** Selecione o hub IoT para ligar com a sua nova instância de Serviço de Provisionamento de Dispositivos.
    * **Política de Acesso**: selecione **iothubowner** como as credenciais para estabelecer a ligação ao hub IoT.  

      ![Ligue o nome do hub para ligar à instância do Serviço de Provisionamento de Dispositivos na lâmina do portal](./media/quick-setup-auto-provision/link-iot-hub-to-dps-portal.png)  

3. Agora, deverá ver o hub selecionado no painel **Hubs IoT ligados**. Talvez precises de ir ao **Refresh** para que apareça.


## <a name="clean-up-resources"></a>Limpar recursos

Outros guias de introdução desta coleção têm por base este guia de introdução. Se pretender continuar a trabalhar com guias de introdução subsequentes ou com os tutoriais, não limpe os recursos criados neste guia de introdução. Se não pretender continuar, utilize os passos seguintes para eliminar todos os recursos criados por este guia de introdução no portal do Azure.

1. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu Serviço de Provisionamento de Dispositivos. Na parte superior do painel de detalhes do dispositivo, **selecione Delete**.  
2. A partir do menu à esquerda no portal Azure, selecione **Todos os recursos** e, em seguida, selecione o seu hub IoT. Na parte superior do painel de detalhes do hub, **selecione Delete**.  

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido, implementou um hub IoT e uma instância de Serviço de Provisionamento de Dispositivos, e ligou os dois recursos. Para aprender a utilizar esta configuração para fornecer um dispositivo simulado, continue a ser rápido para criar um dispositivo simulado.

> [!div class="nextstepaction"]
> [Quickstart para criar um dispositivo simulado](./quick-create-simulated-device-symm-key.md)
