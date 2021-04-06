---
title: Use o modelo ARM para publicar Azure IoT Hub, conta de armazenamento, mensagens de rota
description: Use o modelo ARM para publicar Azure IoT Hub, conta de armazenamento, mensagens de rota
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: quickstart
ms.date: 08/24/2020
ms.author: robinsh
ms.custom: mvc, subject-armqs
ms.openlocfilehash: fc8ddba2ec9b7bc9f1c2db8673ab805810afe17e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "99981294"
---
# <a name="quickstart-deploy-an-azure-iot-hub-and-a-storage-account-using-an-arm-template"></a>Quickstart: Implementar um Hub Azure IoT e uma conta de armazenamento usando um modelo ARM

Neste arranque rápido, você usa um modelo de Gestor de Recursos Azure (modelo ARM) para criar um Hub IoT que irá encaminhar mensagens para O Azure Storage, e uma conta de armazenamento para segurar as mensagens. Depois de adicionar manualmente um dispositivo IoT virtual ao centro para enviar as mensagens, configura essa informação de ligação numa aplicação chamada  *"leitura de braço-escrita"* para enviar mensagens do dispositivo para o centro. O hub está configurado para que as mensagens enviadas para o hub sejam automaticamente encaminhadas para a conta de armazenamento. No final deste arranque rápido, pode abrir a conta de armazenamento e ver as mensagens enviadas.

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

Se o seu ambiente cumpre os pré-requisitos e se está familiarizado com a utilização de modelos ARM, selecione o botão **Implementar no Azure**. O modelo será aberto no portal do Azure.

[![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

## <a name="prerequisites"></a>Pré-requisitos

Se não tiver uma subscrição do Azure, crie uma [conta do Azure gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="review-the-template"></a>Rever o modelo

O modelo utilizado neste quickstart é chamado `101-iothub-auto-route-messages` de [Azure Quickstart Templates](https://azure.microsoft.com/resources/templates/101-iothub-auto-route-messages).

:::code language="json" source="~/quickstart-templates/101-iothub-auto-route-messages/azuredeploy.json":::

Dois recursos Azure são definidos no modelo:

- [Microsoft.Storage/storageAcontas](/azure/templates/microsoft.storage/storageaccounts)
- [Microsoft.Devices/IotHubs](/azure/templates/microsoft.devices/iothubs)

## <a name="deploy-the-template-and-run-the-sample-app"></a>Implemente o modelo e execute a aplicação da amostra

Esta secção fornece os passos para implantar o modelo, criar um dispositivo virtual e executar a aplicação de escrita de leitura de braço para enviar as mensagens.

1. Crie os recursos implantando o modelo ARM.

    > [!TIP]
    > Selecione o botão abaixo para iniciar a implementação do modelo. Enquanto está a funcionar, prepara a aplicação de leitura de braço para executar.

    [![Implementar no Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-iothub-auto-route-messages%2Fazuredeploy.json)

1. Faça o download e desaperte as [amostras IoT C#](/samples/azure-samples/azure-iot-samples-csharp/azure-iot-samples-for-csharp-net/).

1. Abra uma janela de comando e vá para a pasta onde desapertou as amostras IoT C#. Encontre a pasta com o ficheiro arm-read-write.csproj. Cria-se as variáveis ambientais nesta janela de comando. Entre no [portal Azure](https://portal.azure.com) para obter as chaves. Selecione **grupos de recursos** e, em seguida, selecione o grupo de recursos utilizado para este arranque rápido.

   ![Selecione o grupo de recursos](./media/horizontal-arm-route-messages/01-select-resource-group.png)

1. Você vê o Hub IoT e a conta de armazenamento que foram criados quando implementou o modelo ARM. Aguarde até que o modelo esteja totalmente implantado antes de continuar. Em seguida, selecione o seu grupo de recursos para ver os seus recursos.

   ![Ver recursos no grupo de recursos](./media/horizontal-arm-route-messages/02-view-resources-in-group.png)

1. Precisa do **nome do centro.** Selecione o hub na lista de recursos. Copie o nome do hub da parte superior da secção IoT Hub para a área de transferência do Windows.

   ![Copiar o nome do hub](./media/horizontal-arm-route-messages/03-copy-hub-name.png)

    Substitua o nome do hub neste comando quando for notado, e execute este comando na janela de comando:

    ```cmd
    SET IOT_HUB_URI=<hub name goes here>.azure-devices-net;
    ```

   que olhará para este exemplo:

   ```cmd
   SET IOT_HUB_URI=ContosoTestHubdlxlud5h.azure-devices-net;
   ```

1. A próxima variável ambiental é a chave de dispositivo ioT. Adicione um novo dispositivo ao hub selecionando **dispositivos IOT** do menu IoT Hub para o hub.

   ![Selecione dispositivos IoT](./media/horizontal-arm-route-messages/04-select-iot-devices.png)

1. No lado direito do ecrã, selecione **+ NOVO** para adicionar um novo dispositivo.

   Preencha o nome do novo dispositivo. Este quickstart usa um nome a começar pelo **Contoso-Test-Device**. Guarde o dispositivo e, em seguida, abra novamente o ecrã para recuperar a chave do dispositivo. (A chave é gerada para si quando fecha o painel.) Selecione a tecla primária ou secundária e copie-a para a área de transferência do Windows. Na janela de comando, desapedaça o comando para executar e, em seguida, prima **Enter**. O comando deve parecer-se com este, mas com a chave do dispositivo colada em:

   ```cmd
   SET IOT_DEVICE_KEY=<device-key-goes-here>
   ```

1. A última variável ambiente é o **ID do dispositivo.** Na janela de comando, instale o comando e execute-o.

   ```cmd
   SET IOT_DEVICE_ID=<device-id-goes-here>
   ```

   que será semelhante a este exemplo:

   ```cmd
   SET IOT_DEVICE_ID=Contoso-Test-Device
   ```

1. Para ver as variáveis ambientais que definiu, escreva SET na linha de comando e prima **Enter,** em seguida, procure as que começam com **IoT**.

   ![Ver variáveis ambientais](./media/horizontal-arm-route-messages/06-environment-variables.png)

    Agora as variáveis ambientais estão definidas, executar a aplicação a partir da mesma janela de comando. Como está a usar a mesma janela, as variáveis estarão acessíveis na memória quando executar a aplicação.

1. Para executar a aplicação, digite o seguinte comando na janela de comando e prima **Enter**.

    `dotnet run arm-read-write`

   A aplicação gera e exibe mensagens na consola à medida que envia cada mensagem para o hub IoT. O hub foi configurado no modelo ARM para ter encaminhamento automatizado. As mensagens que contêm o texto `level = storage` são automaticamente encaminhadas para a conta de armazenamento. Deixe a aplicação funcionar durante 10 a 15 minutos e, em seguida, prima **Enter** uma ou duas vezes até parar de funcionar.

## <a name="review-deployed-resources"></a>Revisão dos recursos implantados

1. Faça login no [portal Azure](https://portal.azure.com) e selecione o Grupo de Recursos e, em seguida, selecione a conta de armazenamento.

1. Desateia na conta de armazenamento até encontrar ficheiros.

   ![Veja os ficheiros da conta de armazenamento](./media/horizontal-arm-route-messages/07-see-storage.png)

1. Selecione um dos ficheiros e selecione **Baixar** e transferir o ficheiro para um local que pode encontrar mais tarde. Terá um nome numérico, como 47. Adicione _.txt_ ao fim e, em seguida, clique duas vezes no ficheiro para abri-lo.

1. Quando abre o ficheiro, cada linha é para uma mensagem diferente; o corpo de cada mensagem também é encriptado. Deve ser para que faça perguntas contra o corpo da mensagem.

   ![Ver as mensagens enviadas](./media/horizontal-arm-route-messages/08-messages.png)

   > [!NOTE]
   > Estas mensagens estão codificadas na UTF-32 e na base64. Se ler a mensagem de volta, tem de descodificá-la da base64 e utf-32 para lê-la como ASCII. Se estiver interessado, pode utilizar o método ReadOneRowFromFile no Tutorial de Encaminhamento para ler um para um destes ficheiros de mensagens e descodificá-lo no ASCII. ReadOneRowFromFile está no repositório IoT C# Samples que abriu para este arranque rápido. Aqui está o caminho a partir do topo da pasta: *./iot-hub/Tutorials/Encaminhamento/SimulatedDevice/Program.cs.* Descole o `readTheFile` booleano para o caso verdadeiro, e código o caminho para o ficheiro no disco, e abrirá e traduzirá a primeira linha do ficheiro.

Implementou um modelo ARM para criar um Hub IoT e uma conta de armazenamento, e executou um programa para enviar mensagens para o centro. As mensagens são então automaticamente armazenadas na conta de armazenamento onde podem ser visualizadas.

## <a name="clean-up-resources"></a>Limpar os recursos

Para remover os recursos adicionados durante este arranque rápido, inicie sessão no [portal Azure](https://portal.azure.com). Selecione **Grupos de Recursos** e, em seguida, encontre o grupo de recursos utilizado para este arranque rápido. Selecione o grupo de recursos e, em seguida, *selecione Delete*. Eliminará todos os recursos do grupo.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Crie e implemente o seu primeiro modelo ARM](../azure-resource-manager/templates/template-tutorial-create-first-template.md)
