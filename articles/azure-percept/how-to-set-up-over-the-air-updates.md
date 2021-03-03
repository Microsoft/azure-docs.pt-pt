---
title: Configurar o Azure IoT Hub para implementar sobre as atualizações aéreas
description: Saiba como configurar o Azure IoT Hub para implementar atualizações no ar para Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: 80f30ae818b9e74ff97d2bc26552ed7d7124b499
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662917"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Como configurar o Azure IoT Hub para implementar as atualizações aéreas para o seu Azure Percept DK
Mantenha o seu Azure Percept DK seguro e atualizado utilizando atualizações over-the-air. Em alguns passos simples, poderá configurar o seu ambiente Azure com a Atualização do Dispositivo para IoT Hub e implementar as mais recentes atualizações para o seu Azure Percept DK.

## <a name="create-a-device-update-account"></a>Criar uma conta de atualização de dispositivos

1. Vá ao [portal Azure](https://portal.azure.com) e inscreva-se na conta Azure que está a usar com a Azure Percept 

1. Na janela de pesquisa no topo da página, comece a escrever "Device Update for IoT Hub"

1. Selecione **atualização do dispositivo para IoT Hubs** tal como aparece na janela de pesquisa.

1. Clique no botão **+Adicionar** na parte superior esquerda da página.

1. Selecione o Grupo de Subscrição e Recursos Azure associado ao seu dispositivo Azure Percept (é aqui que está localizado o Hub IoT a partir de bordo).

1. Especifique um nome e localização para a sua conta de atualização de dispositivo

1. Reveja os detalhes e, em seguida, **selecione Review + Create**.
 
1. Uma vez concluída a implementação, clique **em Ir para o recurso**.
 
## <a name="create-a-device-update-instance"></a>Criar uma instância de atualização de dispositivo
Agora, crie uma instância dentro da sua atualização de dispositivos para conta IoT Hub.

1. Na atualização do dispositivo para recurso IoT Hub, clique em **Instâncias** em **Gestão de Exemplos**.
 
1. Clique + Especifique um nome de instância e selecione o Hub IoT associado ao seu dispositivo Azure Percept (isto é, criado durante a Experiência de Embarque).  Isto pode levar alguns minutos para ser concluído.
 
1. Clique em **Criar**

## <a name="configure-iot-hub"></a>Configure IoT Hub

1. Na página Instâncias de Gestão **de Casos,** aguarde que a sua Instância de Atualização do Dispositivo se mude para um estado **bem sucedido.** Clique no ícone **Refresh** ao lado **de Apagar** para atualizar o estado.
 
1. Selecione a Instância que foi criada para si e, em seguida, clique em **Configure IoT Hub**. No painel esquerdo, **selecione Concordo em fazer estas alterações** e clique em **Update**.
 
1. Aguarde que o processo esteja concluído com sucesso.
 
## <a name="configure-access-control-roles"></a>Configure funções de controlo de acesso
O passo final permitir-lhe-á conceder permissões aos utilizadores para publicar e implementar atualizações.

1. Na atualização do dispositivo para recurso IoT Hub, clique no **controlo de acesso (IAM)**
 
2. Clique **em +Adicionar** e, em seguida, selecione **Adicionar atribuição de função**
 
3. Para **função**, selecione **O Administrador de Atualização de Dispositivos**. Para **atribuir acesso ao** princípio de **Utilizador, grupo ou serviço** selecionado. Para **selecionar** a sua conta ou a conta da pessoa que irá implementar atualizações. Em seguida, clique em **Guardar**. 

    > [!TIP]
    > Se quiser dar acesso a mais pessoas na sua organização, pode repetir este passo e fazer de cada um destes utilizadores um Administrador de **Atualização de Dispositivos.**

## <a name="next-steps"></a>Passos seguintes

Está agora definido e pode [atualizar o seu kit dev Azure Percept over-the-air](./how-to-update-over-the-air.md) utilizando a Atualização do Dispositivo para ioT Hub. Navegue para o Hub Azure IoT que está a utilizar para o seu dispositivo Azure Percept.