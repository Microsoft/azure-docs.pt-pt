---
title: Criar hub Azure IoT para implementar atualizações over-the-air
description: Saiba como configurar o Azure IoT Hub para implementar atualizações no ar para Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: 5890ca90b0ad0dcb3d5141e62e986475fd386959
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106064125"
---
# <a name="how-to-set-up-azure-iot-hub-to-deploy-over-the-air-updates-to-your-azure-percept-dk"></a>Como configurar o Azure IoT Hub para implementar as atualizações aéreas para o seu Azure Percept DK

Mantenha o seu Azure Percept DK seguro e atualizado utilizando atualizações over-the-air. Em alguns passos simples, poderá configurar o seu ambiente Azure com a Atualização do Dispositivo para IoT Hub e implementar as mais recentes atualizações para o seu Azure Percept DK.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu kit dev a uma rede Wi-Fi, criou um Hub IoT e ligou o seu kit dev ao IoT Hub

## <a name="create-a-device-update-account"></a>Criar uma conta de atualização de dispositivos

1. Vá ao [portal Azure](https://portal.azure.com) e inscreva-se na conta Azure que está a usar com a Azure Percept.

1. Na barra de pesquisa no topo da página, insira a Atualização do **Dispositivo para IoT Hub**.

1. Selecione **atualização do dispositivo para IoT Hub** quando aparecer na barra de pesquisa.

1. Clique no botão **+Adicionar** na parte superior esquerda da página.

1. Selecione o Grupo **de Subscrição** e Recursos **Azure** associado ao seu dispositivo Azure Percept e ao seu Hub IoT.

1. Especifique um **nome** e **localização** para a sua conta de atualização do dispositivo.

1. Reveja os detalhes e selecione **Review + Create**.

1. Uma vez concluída a implementação, clique **em Ir para o recurso**.

## <a name="create-a-device-update-instance"></a>Criar uma instância de atualização de dispositivo

1. Na atualização do dispositivo para recurso IoT Hub, clique em **Instâncias** em **Gestão de Exemplos**.

1. Clique + Especifique um nome de instância e selecione o Hub IoT associado ao seu dispositivo Azure Percept.  Isto pode levar alguns minutos para ser concluído.

1. Clique em **Criar**.

## <a name="configure-iot-hub"></a>Configure IoT Hub

1. Na página Instâncias de Gestão **de Casos,** aguarde que a sua Instância de Atualização do Dispositivo se mude para um estado **bem sucedido.** Clique no ícone **Refresh** para atualizar o estado.

1. Selecione a Instância que foi criada para si e clique em **Configurar IoT Hub**. No painel esquerdo, **selecione Concordo em fazer estas alterações** e clique em **Update**.

1. Aguarde que o processo esteja concluído com sucesso.

## <a name="configure-access-control-roles"></a>Configure funções de controlo de acesso

O passo final permitir-lhe-á conceder permissões aos utilizadores para publicar e implementar atualizações.

1. Na atualização do dispositivo para obter o recurso IoT Hub, clique em **Access control (IAM)**.

1. Clique **em +Adicionar** e, em seguida, selecione **Adicionar a atribuição de função**.

1. Para **função**, selecione **O Administrador de Atualização de Dispositivos**. Para **atribuir acesso ao** princípio de **Utilizador, grupo ou serviço** selecionado. Para **Selecionar**, selecione a sua conta ou a conta da pessoa que irá implementar atualizações. Clique em **Guardar**.

> [!TIP]
> Se quiser dar acesso a mais pessoas na sua organização, pode repetir este passo e fazer de cada um destes utilizadores um Administrador de **Atualização de Dispositivos.**

## <a name="next-steps"></a>Passos seguintes

Está agora pronto para [atualizar o seu kit dev Azure Percept over-the-air](./how-to-update-over-the-air.md) utilizando a Atualização do Dispositivo para ioT Hub.