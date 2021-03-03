---
title: Atualize o seu Azure Percept DK no ar
description: Saiba como receber as atualizações aéreas do seu Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 02/18/2021
ms.custom: template-how-to
ms.openlocfilehash: aa8f696b6a26e812256be4e93975844f2c721b6e
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101663010"
---
# <a name="update-your-azure-percept-dk-over-the-air"></a>Atualize o seu Azure Percept DK no ar

Siga este guia para saber como atualizar o conselho de transporte do seu Azure Percept DK over-the-air com a Atualização do Dispositivo para IoT Hub.

## <a name="import-your-update-file-and-manifest-file"></a>Importe o seu ficheiro de atualização e o ficheiro manifesto.

> [!NOTE]
> Se já importou a atualização, pode saltar diretamente para **criar um grupo atualizado com dispositivos.**

1. Navegue para o Hub Azure IoT que está a utilizar para o seu dispositivo Azure Percept. No painel de menus do lado esquerdo, selecione **Atualizações do Dispositivo** em **Gestão Automática de Dispositivos**.
 
1. Verá vários separadores na parte superior do ecrã. Selecione o separador **Atualizações.**
 
1. Selecione **+ Import New Update** abaixo do cabeçalho Pronto para **Implantar.**
 
1. Clique nas caixas em **Select Import Manifest File** e Select Update **Files** para selecionar o ficheiro manifesto apropriado (.json) e um ficheiro de atualização (.swu). Pode encontrar estes ficheiros de atualização para o seu dispositivo Azure Percept [aqui](https://go.microsoft.com/fwlink/?linkid=2155625).
 
1. Selecione o ícone de pasta ou caixa de texto sob **Selecionar um recipiente de armazenamento** e, em seguida, selecione a conta de armazenamento apropriada.
 
1. Se já criou um recipiente de armazenamento, pode reutilizá-lo. Caso contrário, selecione **+ Recipiente** para criar um novo recipiente de armazenamento para atualizações da OTA. Selecione o recipiente que pretende utilizar e clique em **Selecionar**.
 
    >[!Note]
    >Se não tiver um recipiente, pedir-lhe-á que crie um.
 
1. **Selecione Submeter-se** para iniciar o processo de importação. O processo de submissão levará cerca de 4 minutos.
 
    >[!Note]
    >Pode ser-lhe pedido que adicione uma regra de Pedido de Origem Cruzada (CORS) para aceder ao recipiente de armazenamento selecionado. **Selecione Adicionar a regra e tentar novamente** prosseguir.
 
    >[!Note]
    >Devido ao tamanho da imagem, poderá ver a página **submeter...** Até 5 min antes de ver o próximo passo.
    
1. O processo de importação começa e você é redirecionado para o **separador Deseudo histórico** de importação da página De Atualizações de **Dispositivos.** Clique **em Refresh** para monitorizar o progresso enquanto o processo de importação estiver concluído. Dependendo do tamanho da atualização, isto pode demorar alguns minutos ou mais (durante os horários de pico, por favor, espere que o serviço de importação desemque até 1hr).

1. Quando a coluna Status indicar que a importação foi bem sucedida, selecione o separador **Pronto para implementar** e clique em **Refresh**. Deve agora ver a sua atualização importada na lista.
 
## <a name="create-a-device-update-group"></a>Criar um grupo de atualização de dispositivos
A Atualização do Dispositivo para O Hub IoT permite-lhe direcionar uma atualização para grupos específicos de DKs Azure Percept. Para criar um grupo, deve adicionar uma etiqueta ao seu conjunto de dispositivos alvo no Azure IoT Hub.

> [!NOTE]
> Se já criou um grupo, pode saltar diretamente para o próximo passo.

Requisitos de etiqueta de grupo:
- Pode adicionar qualquer valor à sua etiqueta, exceto **para Uncatgorized,** que é um valor reservado.
- O valor da etiqueta não pode exceder 255 caracteres.
- O valor da etiqueta só pode conter estes caracteres especiais: "."""-"_""~".
- Etiquetas e nomes de grupo são sensíveis a casos.
- Um dispositivo só pode ter uma etiqueta. Qualquer etiqueta posterior adicionada ao dispositivo irá anular a etiqueta anterior.
- Um dispositivo só pode pertencer a um grupo.

1. Adicione uma Etiqueta ao(s) dispositivo(s).
    1. A partir do **IoT Edge** no painel de navegação esquerdo, encontre o seu Azure Percept DK e navegue até ao seu **Device Twin**.
    1. Adicione uma nova atualização do dispositivo para o valor da etiqueta **IoT Hub,** conforme mostrado abaixo (Alterar ```<CustomTagValue>``` para o seu valor, ou seja, AzurePerceptGroup1). Saiba mais sobre as etiquetas de [documentos JSON gémeas do](https://docs.microsoft.com/azure/iot-hub/iot-hub-devguide-device-twins#device-twins)dispositivo.

    ```
    "tags": {
    "ADUGroup": "<CustomTagValue>"
    },
    ```

 
1. Clique **em Guardar** e resolver quaisquer problemas de formatação.
 
1. Crie um grupo selecionando uma etiqueta Azure IoT Hub existente.
    1. Volte para a sua página Azure IoT Hub.
    1. Selecione **atualizações do dispositivo** sob **gestão automática** do dispositivo no painel do menu esquerdo.
    1. Selecione o **separador Grupos.** Esta página apresentará o número de dispositivos não agrupados ligados à Atualização do Dispositivo.
    1. **Selecione + Adicionar** para criar um novo grupo.
    1. Selecione uma etiqueta IoT Hub da lista e clique em **Enviar por exemplo**.
    1. Uma vez criado o grupo, o gráfico de conformidade de atualização e a lista de grupos serão atualizados. O gráfico mostra o número de dispositivos em vários estados de conformidade: **Na última atualização,** **novas atualizações disponíveis,** **Atualizações em curso**, e Ainda não **agrupadas**.
 

## <a name="deploy-an-update"></a>Implementar uma atualização
1. Deverá ver o seu grupo recém-criado com uma nova atualização listada nas **atualizações disponíveis** (poderá ter de atualizar uma vez). Selecione a atualização.
 
1. Confirme se o grupo de dispositivos correto está selecionado como grupo de dispositivos alvo. Selecione uma **data de início** e hora de **início** para a sua implementação e, em seguida, clique em **Criar implementação**. 

    >[!CAUTION]
    >Definir a hora de início o passado irá desencadear a implantação imediatamente.
 
1. Verifique a ficha de conformidade. Devia ver que a atualização está em andamento.
 
1. Depois de concluída a atualização, o seu gráfico de conformidade refletirá o seu novo estado de atualização.
 
1. Selecione o **separador Implementações** na página de atualizações do **dispositivo.**
 
1. Selecione a sua implementação para ver os detalhes da implementação. Pode ter de clicar em **Atualizar** até que o **Estado** mude para **Ser Bem Sucedido**.

## <a name="next-steps"></a>Passos seguintes

O seu kit dev está agora atualizado com sucesso. Pode continuar o desenvolvimento e a operação com o seu devkit.