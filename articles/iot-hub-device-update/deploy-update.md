---
title: Implementar uma atualização utilizando a atualização do dispositivo para o Azure IoT Hub | Microsoft Docs
description: Implementar uma atualização utilizando a atualização do dispositivo para o Azure IoT Hub.
author: vimeht
ms.author: vimeht
ms.date: 2/11/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 0a11c8e8946229941c1fe60f7f2ce84d9fadb2ed
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680028"
---
# <a name="deploy-an-update-using-device-update-for-iot-hub"></a>Implementar uma atualização utilizando a atualização do dispositivo para o hub IoT

Saiba como implementar uma atualização num dispositivo IoT utilizando a Atualização do Dispositivo para o IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um Hub IoT com atualização do dispositivo para o IoT Hub ativado](create-device-update-account.md). Recomenda-se que utilize um nível S1 (Standard) ou superior para o seu Hub IoT. 
* [Pelo menos uma atualização foi importada com sucesso para o dispositivo provisionado.](import-update.md) 
* Um dispositivo IoT (ou simulador) previsto para a Atualização do Dispositivo dentro do Hub IoT.
* [Foi atribuída uma etiqueta ao dispositivo IoT que está a tentar atualizar. O dispositivo faz parte de pelo menos um grupo de atualização.](create-update-group.md)
* Navegadores suportados:
  * [Microsoft Edge](https://www.microsoft.com/edge)
  * Google Chrome

## <a name="deploy-an-update"></a>Implementar uma atualização

1. Ir para o [portal Azure](https://portal.azure.com)

2. Navegue para a lâmina de atualização do dispositivo do seu hub IoT.

  :::image type="content" source="media/deploy-update/device-update-iot-hub.png" alt-text="Hub IoT" lightbox="media/deploy-update/device-update-iot-hub.png":::

3. Selecione o separador Grupos no topo da página. [Saiba mais](device-update-groups.md) sobre grupos de dispositivos. 

  :::image type="content" source="media/deploy-update/updated-view.png" alt-text="Separador de grupos" lightbox="media/deploy-update/updated-view.png":::

4. Veja a tabela de conformidade da atualização e a lista de grupos. Deverá ver uma nova atualização disponível para o seu grupo de dispositivos, com um link para a atualização em Atualizações Pendentes (poderá necessitar de atualizar uma vez). [Saiba mais sobre a conformidade com a atualização.](device-update-compliance.md) 

5. Selecione a atualização disponível.

6. Confirme que o grupo correto está selecionado como grupo alvo. Agendar a sua implementação e, em seguida, selecione Implementar a atualização.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione atualização" lightbox="media/deploy-update/select-update.png":::

7. Veja o gráfico de conformidade. Devia ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em curso" lightbox="media/deploy-update/update-in-progress.png":::

8. Depois de o seu dispositivo ter sido atualizado com sucesso, deverá ver a atualização do seu gráfico de conformidade e detalhes de implementação para refletir o mesmo. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização conseguiu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorize uma implementação de atualização

1. Selecione o separador Implementações no topo da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Separador de implementações" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecione a implementação criada para ver os detalhes da implementação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes da implementação" lightbox="media/deploy-update/deployment-details.png":::

3. Selecione Refresh para ver os mais recentes detalhes do estado. Continue este processo até que o estado mude para Sucesso.


## <a name="retry-an-update-deployment"></a>Recandidutar uma implementação de atualização

Se a sua implementação falhar por alguma razão, pode voltar a tentar a implementação de dispositivos falhados. 

1. Vá ao separador Implementações e selecione a implementação que falhou. 

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes da implementação" lightbox="media/deploy-update/deployment-details.png":::

2. Clique no estado do dispositivo "Falhado" no painel de informações de implementação detalhado.

3. Clique em "Retry dispositivos falhados" e reconheça a notificação de confirmação. 

## <a name="next-steps"></a>Passos seguintes

[Resolver problemas comuns](troubleshoot-device-update.md)
