---
title: Atualização do dispositivo para a | do sistema operativo Azure Real-time Microsoft Docs
description: Começar com a atualização do dispositivo para o Azure Real-time-operating-system
author: valls
ms.author: valls
ms.date: 3/18/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: 66da860a5cdae1f5c7c18e4136b1f2d960492ca8
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629058"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-azure-real-time-operating-system-rtos"></a>Atualização do dispositivo para tutorial Azure IoT Hub usando O Sistema Operativo Azure Real Time (RTOS)

Este tutorial irá percorrer a forma de criar a Atualização de Dispositivos para IoT Hub Agent em Azure RTOS NetX Duo. Também fornece APIs simples para os desenvolvedores integrarem a capacidade de Atualização de Dispositivos na sua aplicação. Explore [amostras](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU) de placas de avaliação de semicondutores chave que incluem os guias de arranque para aprender a configurar, construir e implementar as atualizações over-the-air (OTA) para os dispositivos.

Neste tutorial, vai aprender a:
> [!div class="checklist"]
> * Introdução
> * Marque o seu dispositivo
> * Criar um grupo de dispositivos
> * Implementar uma atualização de imagem
> * Monitorize a implementação da atualização

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos
* Acesso a um hub IoT. Recomenda-se que utilize um nível S1 (Standard) ou superior.
* Uma instância e conta de atualização de dispositivos ligada ao seu Hub IoT. Siga o guia para [criar e ligar](http://create-device-update-account.md/) uma conta de atualização do dispositivo se não o tiver feito anteriormente.

## <a name="get-started"></a>Introdução

Cada projeto Azure RTOS específico da amostra contém código e documentação sobre como utilizar a Atualização do Dispositivo para o IoT Hub nele. 
1. Descarregue os ficheiros de amostras específicos do quadro a partir de [amostras de Azure RTOS e Device Update](https://github.com/azure-rtos/samples/tree/PublicPreview/ADU).
2. Encontre a pasta docs a partir da amostra descarregada.
3. A partir dos docs siga os passos para como preparar recursos Azure, Conta e registar dispositivos IoT para ele.
5. Em seguida, siga os docs para construir uma nova imagem de firmware e manifesto de importação para o seu conselho.
6. Em seguida, publique a imagem do firmware e manifeste-se para Device Update for IoT Hub.
7. Finalmente descarregue e execute o projeto no seu dispositivo.

Saiba mais sobre [o Azure RTOS](https://docs.microsoft.com/azure/rtos/).  

## <a name="tag-your-device"></a>Marque o seu dispositivo

1. Mantenha a aplicação do dispositivo a funcionar a partir do passo anterior.
2. Entre no [portal Azure](https://portal.azure.com) e navegue até ao Hub IoT.
3. A partir de 'Dispositivos IoT' no painel de navegação esquerdo, encontre o seu dispositivo IoT e navegue para o Dispositivo Twin.
4. No Dispositivo Twin, elimine qualquer valor de marcação de atualização do dispositivo existente, definindo-os para nulos.
5. Adicione um novo valor de etiqueta de atualização do dispositivo, como mostrado abaixo.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="create-update-group"></a>Criar grupo de atualização

1. Aceda ao Hub IoT que já ligou à sua instância de Atualização de Dispositivos.
2. Selecione a opção Atualizações de Dispositivos sob Gestão Automática de Dispositivos a partir da barra de navegação à esquerda.
3. Selecione o separador Grupos no topo da página. 
4. Selecione o botão Adicionar para criar um novo grupo.
5. Selecione a etiqueta IoT Hub que criou no passo anterior da lista. Selecione Criar grupo de atualização.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Screenshot mostrando a seleção da etiqueta." lightbox="media/create-update-group/select-tag.PNG":::

[Saiba mais](create-update-group.md) sobre a adição de tags e criar grupos de atualização

## <a name="deploy-new-firmware"></a>Implementar novo firmware

1. Uma vez criado o grupo, deverá ver uma nova atualização disponível para o seu grupo de dispositivos, com uma ligação à atualização em 'Atualizações Pendentes'. Pode precisar de refrescar uma vez. 
2. Clique na atualização disponível.
3. Confirme que o grupo correto está selecionado como grupo alvo. Agendar a sua implementação e, em seguida, selecione Implementar a atualização.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Selecione atualização" lightbox="media/deploy-update/select-update.png":::

4. Veja o gráfico de conformidade. Devia ver que a atualização está em andamento. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Atualização em curso" lightbox="media/deploy-update/update-in-progress.png":::

5. Depois de o seu dispositivo ter sido atualizado com sucesso, deverá ver a atualização do seu gráfico de conformidade e detalhes de implementação para refletir o mesmo. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Atualização conseguiu" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorize uma implementação de atualização

1. Selecione o separador Implementações no topo da página.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Separador de implementações" lightbox="media/deploy-update/deployments-tab.png":::

2. Selecione a implementação criada para ver os detalhes da implementação.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Detalhes da implementação" lightbox="media/deploy-update/deployment-details.png":::

3. Selecione Refresh para ver os mais recentes detalhes do estado. Continue este processo até que o estado mude para Sucesso.

Já concluiu uma atualização de imagem de ponta a ponta com sucesso utilizando a Atualização do Dispositivo para IoT Hub num dispositivo Raspberry Pi 3 B+. 

## <a name="cleanup-resources"></a>Recursos de limpeza

Quando já não era necessário limpar a conta de atualização do dispositivo, exemplo, IoT Hub e dispositivo IoT. 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a Azure RTOS e como funciona com a Azure IoT, veja https://azure.com/rtos .
