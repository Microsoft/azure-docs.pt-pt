---
title: Crie grupo de dispositivos na Atualização do Dispositivo para | do Hub IoT do Azure Microsoft Docs
description: Criar um grupo de dispositivos em Atualização de Dispositivos para Azure IoT Hub
author: vimeht
ms.author: vimeht
ms.date: 2/17/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: a0894047db1ed7687a1a0f5f87fc4020ddf7c694
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101680029"
---
# <a name="create-device-groups-in-device-update-for-iot-hub"></a>Criar grupos de dispositivos na Atualização do Dispositivo para IoT Hub
A atualização do dispositivo para o IoT Hub permite implementar uma atualização para um grupo de dispositivos IoT.

## <a name="prerequisites"></a>Pré-requisitos

* [Acesso a um Hub IoT com atualização do dispositivo para o IoT Hub ativado](create-device-update-account.md). Recomenda-se que utilize um nível S1 (Standard) ou superior para o seu Hub IoT. 
* Um dispositivo IoT (ou simulador) previsto para a Atualização do Dispositivo dentro do Hub IoT.
* [Pelo menos uma atualização foi importada com sucesso para o dispositivo provisionado.](import-update.md)

## <a name="add-a-tag-to-your-devices"></a>Adicione uma etiqueta aos seus dispositivos  

A atualização do dispositivo para o IoT Hub permite implementar uma atualização para um grupo de dispositivos IoT. Para criar um grupo, o primeiro passo é adicionar uma etiqueta ao conjunto de dispositivos alvo no IoT Hub. As etiquetas só podem ser adicionadas com sucesso ao seu dispositivo depois de ter sido ligada à Atualização do Dispositivo.

A documentação abaixo descreve como adicionar e atualizar uma etiqueta.

### <a name="programmatically-update-device-twin"></a>Atualização programática Do Dispositivo Twin

Pode atualizar o Dispositivo Twin com a etiqueta apropriada utilizando o RegistryManager depois de ter matriculado o dispositivo com a Atualização do Dispositivo. 
[Saiba como adicionar tags utilizando uma aplicação de amostra .NET.](../iot-hub/iot-hub-csharp-csharp-twin-getstarted.md)  
[Saiba mais sobre as propriedades da etiqueta.](../iot-hub/iot-hub-devguide-device-twins.md#tags-and-properties-format)

#### <a name="device-update-tag-format"></a>Formato de etiqueta de atualização de dispositivo

```markdown
     "tags": {
              "ADUGroup": "<CustomTagValue>"
             }
```

### <a name="using-jobs"></a>Utilização de Empregos

É possível agendar um Job em vários dispositivos para adicionar ou atualizar uma etiqueta de Atualização de Dispositivos seguindo [estes](../iot-hub/iot-hub-devguide-jobs.md) exemplos. [Saiba mais](../iot-hub/iot-hub-csharp-csharp-schedule-jobs.md).

  > [!NOTE] 
  > Esta ação vai contra a sua quota de mensagens IOT Hub atual e é aconselhável alterar apenas até 50.000 tags gémeas de dispositivos de cada vez, caso contrário poderá ter de comprar mais unidades IoT Hub se exceder a sua quota diária de mensagem IoT Hub. Os detalhes podem ser encontrados em [Quotas e estrangulamento.](../iot-hub/iot-hub-devguide-quotas-throttling.md#quotas-and-throttling)

### <a name="direct-twin-updates"></a>Atualizações diretas de twin

As etiquetas também podem ser adicionadas ou atualizadas no dispositivo twin diretamente.

1. Entre no [portal Azure](https://portal.azure.com) e navegue para o seu Hub IoT.

2. De 'Dispositivos IoT' ou 'IoT Edge' no painel de navegação esquerdo encontre o seu dispositivo IoT e navegue para o Dispositivo Twin.

3. No Dispositivo Twin, elimine qualquer valor de marcação de atualização do dispositivo existente, definindo-os para nulos.

4. Adicione um novo valor de etiqueta de atualização do dispositivo, como mostrado abaixo. [Exemplo dispositivo documento JSON duplo com etiquetas.](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

### <a name="limitations"></a>Limitações

* Pode adicionar qualquer valor à sua etiqueta, exceto "Não categorizado" que é um valor reservado.
* O valor da etiqueta não pode exceder 255 caracteres.
* O valor da etiqueta pode conter caracteres alfanuméricos e os seguintes caracteres especiais ".""-"_""."~".
* Os nomes da etiqueta e do grupo são sensíveis a casos.
* Um dispositivo só pode ter uma etiqueta com o nome ADUGroup, quaisquer adições subsequentes de uma etiqueta com esse nome irá sobrepor-se ao valor existente para o nome de etiqueta ADUGroup.
* Um dispositivo só pode pertencer a um grupo.

## <a name="create-a-device-group-by-selecting-an-existing-iot-hub-tag"></a>Crie um grupo de dispositivos selecionando uma tag IoT Hub existente

1. Aceda ao [Portal do Azure](https://portal.azure.com).

2. Selecione o Hub IoT que já ligou à sua instância de Atualização do Dispositivo.

3. Selecione a opção Atualizações de Dispositivos sob Gestão Automática de Dispositivos a partir da barra de navegação à esquerda.

4. Selecione o separador Grupos no topo da página. Poderá ver o número de dispositivos ligados à Atualização do Dispositivo que ainda não estão agrupados.
   :::image type="content" source="media/create-update-group/ungrouped-devices.png" alt-text="Screenshot de dispositivos não agrupados." lightbox="media/create-update-group/ungrouped-devices.png":::

5. Selecione o botão Adicionar para criar um novo grupo.
   :::image type="content" source="media/create-update-group/add-group.png" alt-text="Screenshot da adição do grupo do dispositivo." lightbox="media/create-update-group/add-group.png":::

6. Selecione uma etiqueta IoT Hub da lista e, em seguida, selecione Criar grupo de atualização.
   :::image type="content" source="media/create-update-group/select-tag.png" alt-text="Screenshot da seleção de tags." lightbox="media/create-update-group/select-tag.png":::

7. Assim que o grupo for criado, verá que o gráfico de conformidade da atualização e a lista de grupos são atualizados.  O gráfico de conformidade da atualização mostra a contagem de dispositivos em vários estados de conformidade: Na última atualização, novas atualizações disponíveis, Atualizações em Progresso e Dispositivos ainda não agrupados. [Saiba mais sobre a conformidade com a atualização.](device-update-compliance.md) 
    :::image type="content" source="media/create-update-group/updated-view.png" alt-text="Screenshot da vista de conformidade da atualização." lightbox="media/create-update-group/updated-view.png":::

8. Deverá ver o seu grupo recém-criado e quaisquer atualizações disponíveis para os dispositivos do novo grupo. Pode implementar a atualização para o novo grupo a partir desta vista clicando no nome da atualização. Ver Próximo Passo: Implementar atualização para mais detalhes.

## <a name="view-device-details-for-the-group-you-created"></a>Ver detalhes do Dispositivo para o grupo que criou

1. Navegue para o seu grupo recém-criado e clique no nome de grupo.

2. Será mostrada uma lista de dispositivos que fazem parte do grupo juntamente com as propriedades de atualização do dispositivo. Nesta visão, também é possível ver as informações de conformidade da atualização para todos os dispositivos que são membros do grupo. O gráfico de conformidade da atualização mostra a contagem de dispositivos em vários estados de conformidade: Na última atualização, novas atualizações disponíveis e Atualizações em Progresso.
   :::image type="content" source="media/create-update-group/group-details.png" alt-text="Screenshot da visualização de detalhes do grupo do dispositivo." lightbox="media/create-update-group/group-details.png":::

3. Também pode clicar em cada dispositivo individual dentro de um grupo para ser redirecionado para a página de detalhes do dispositivo no IoT Hub.
   :::image type="content" source="media/create-update-group/device-details.png" alt-text="Screenshot da visualização dos detalhes do dispositivo." lightbox="media/create-update-group/device-details.png":::

## <a name="next-steps"></a>Passos Seguintes 

[Implementar atualização](deploy-update.md)

[Saiba mais sobre grupos de dispositivos](device-update-groups.md)

[Saiba mais sobre a conformidade com a atualização.](device-update-compliance.md)
