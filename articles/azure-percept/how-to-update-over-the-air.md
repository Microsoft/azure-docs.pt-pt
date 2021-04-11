---
title: Atualize o seu Azure Percept DK over-the-air (OTA)
description: Saiba como receber atualizações over-the-air (OTA) para o seu Azure Percept DK
author: mimcco
ms.author: mimcco
ms.service: azure-percept
ms.topic: how-to
ms.date: 03/30/2021
ms.custom: template-how-to
ms.openlocfilehash: a3f586f853201534bbaa613e8538d55485ffe147
ms.sourcegitcommit: 73fb48074c4c91c3511d5bcdffd6e40854fb46e5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/31/2021
ms.locfileid: "106063122"
---
# <a name="update-your-azure-percept-dk-over-the-air-ota"></a>Atualize o seu Azure Percept DK over-the-air (OTA)

Siga este guia para saber como atualizar o SISTEMA e firmware do conselho de transporte do seu Azure Percept DK over-the-air (OTA) com a Atualização do Dispositivo para IoT Hub.

## <a name="prerequisites"></a>Pré-requisitos

- Azure Percept DK (devkit)
- [Subscrição do Azure](https://azure.microsoft.com/free/)
- Experiência de [configuração Azure Percept DK](./quickstart-percept-dk-set-up.md): ligou o seu kit dev a uma rede Wi-Fi, criou um Hub IoT e ligou o seu kit dev ao IoT Hub
- [A atualização do dispositivo para o IoT Hub foi configurada com sucesso](./how-to-set-up-over-the-air-updates.md)

## <a name="import-your-update-file-and-manifest-file"></a>Importe o seu ficheiro de atualização e manifesto

> [!NOTE]
> Se já importou a atualização, pode saltar diretamente para **criar um grupo de atualização de dispositivos.**

1. [Descarregue o ficheiro manifesto apropriado (.json) e informe o ficheiro de atualização (.swu) para o seu dispositivo Azure Percept](https://go.microsoft.com/fwlink/?linkid=2155625).

1. Navegue para o Hub Azure IoT que está a utilizar para o seu dispositivo Azure Percept. No painel de menus do lado esquerdo, selecione **Atualizações do Dispositivo** em **Gestão Automática de Dispositivos**.

1. Verá vários separadores na parte superior do ecrã. Selecione o separador **Atualizações.**

1. Selecione **+ Import New Update** abaixo do cabeçalho Pronto para **Implantar.**

1. Clique nas caixas em **Select Import Manifest File** e Select Update **Files** para selecionar o seu ficheiro manifesto (.json) e atualizar o ficheiro (.swu).

1. Selecione o ícone de pasta ou caixa de texto sob **Selecione um recipiente de armazenamento** e selecione a conta de armazenamento apropriada. Se já criou um recipiente de armazenamento, pode voltar a usá-lo. Caso contrário, selecione **+ Recipiente** para criar um novo recipiente de armazenamento para atualizações da OTA. Selecione o recipiente que pretende utilizar e clique em **Selecionar**.

1. **Selecione Submeter-se** para iniciar o processo de importação. Devido ao tamanho da imagem, o processo de submissão pode demorar até 5 minutos.

    > [!NOTE]
    > Pode ser-lhe pedido que adicione uma regra de Pedido de Origem Cruzada (CORS) para aceder ao recipiente de armazenamento selecionado. **Selecione Adicionar a regra e tentar novamente** prosseguir.

1. Quando o processo de importação começar, será redirecionado para o **separador 'Histórico de Importação'** da página **'Atualizações do Dispositivo'.** Clique **em Refresh** para monitorizar o progresso enquanto o processo de importação estiver concluído. Dependendo do tamanho da atualização, isto pode demorar alguns minutos ou mais (durante os horários de pico, o serviço de importação pode demorar até 1 hora).

1. Quando a coluna **Status** indicar que a importação foi bem sucedida, selecione o separador **Pronto para implementar** e clique em **Refresh**. Deve agora ver a sua atualização importada na lista.

## <a name="create-a-device-update-group"></a>Criar um grupo de atualização de dispositivos

A Atualização do Dispositivo para O Hub IoT permite-lhe direcionar uma atualização para grupos específicos de DKs Azure Percept. Para criar um grupo, deve adicionar uma etiqueta ao seu conjunto de dispositivos alvo no Azure IoT Hub.

> [!NOTE]
> Se já criou um grupo, pode saltar para a secção seguinte.

Requisitos de etiqueta de grupo:

- Pode adicionar qualquer valor à sua etiqueta, exceto "Uncategorized", que é um valor reservado.
- O valor da etiqueta não pode exceder 255 caracteres.
- O valor da etiqueta só pode conter estes caracteres especiais: "."""-"_""~".
- Etiquetas e nomes de grupo são sensíveis a casos.
- Um dispositivo só pode ter uma etiqueta. Qualquer etiqueta posterior adicionada ao dispositivo irá anular a etiqueta anterior.
- Um dispositivo só pode pertencer a um grupo.

1. Adicione uma Etiqueta ao seu(s) dispositivos):

    1. A partir do **IoT Edge** no painel de navegação esquerdo, encontre o seu Azure Percept DK e navegue até ao seu **Device Twin**.

    1. Adicione uma nova atualização do dispositivo para o valor da etiqueta **IoT Hub,** como mostrado abaixo ```<CustomTagValue>``` (refere-se ao seu valor/nome da etiqueta, por exemplo, AzurePerceptGroup1). Saiba mais sobre as etiquetas de [documentos JSON gémeas do](../iot-hub/iot-hub-devguide-device-twins.md#device-twins)dispositivo.

        ```
        "tags": {
        "ADUGroup": "<CustomTagValue>"
        },
        ```

1. Clique **em Guardar** e resolver quaisquer problemas de formatação.

1. Crie um grupo selecionando uma etiqueta Azure IoT Hub existente:

    1. Volte para a sua página Azure IoT Hub.

    1. Selecione **atualizações do dispositivo** sob **gestão automática** do dispositivo no painel do menu esquerdo.

    1. Selecione o **separador Grupos.** Esta página apresentará o número de dispositivos não agrupados ligados à Atualização do Dispositivo.

    1. **Selecione + Adicionar** para criar um novo grupo.

    1. Selecione uma etiqueta IoT Hub da lista e clique em **Enviar por exemplo**.

    1. Uma vez criado o grupo, o gráfico de conformidade de atualização e a lista de grupos serão atualizados. O gráfico mostra o número de dispositivos em vários estados de conformidade: **Na última atualização,** **novas atualizações disponíveis,** **Atualizações em curso**, e Ainda não **agrupadas**.

## <a name="deploy-an-update"></a>Implementar uma atualização

1. Deverá ver o seu grupo recém-criado com uma nova atualização listada nas **atualizações disponíveis** (poderá ter de atualizar uma vez). Selecione a atualização.

1. Confirme se o grupo de dispositivos correto está selecionado como grupo de dispositivos alvo. Selecione uma **data de início** e hora de **início** para a sua implementação e, em seguida, clique em **Criar implementação**.

    > [!CAUTION]
    > A definição da hora de início no passado irá ativar a implantação imediatamente.

1. Verifique a ficha de conformidade. Devia ver que a atualização está em andamento.

1. Depois de concluída a atualização, o seu gráfico de conformidade refletirá o seu novo estado de atualização.

1. Selecione o **separador Implementações** na página de atualizações do **dispositivo.**

1. Selecione a sua implementação para ver os detalhes da implementação. Pode ter de clicar em **Atualizar** até que o **Estado** mude para **Ser Bem Sucedido**.

## <a name="next-steps"></a>Passos seguintes

O seu kit dev está agora atualizado com sucesso. Pode continuar o desenvolvimento e operação com o seu kit dev.