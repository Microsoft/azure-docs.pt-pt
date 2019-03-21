---
title: Gerir os dispositivos na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como um operador, saiba como gerir dispositivos na sua aplicação do Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 01/30/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 1e67c22a55068ce42cb1eb6d9c0c9bdf8215f44e
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58167988"
---
# <a name="manage-devices-in-your-azure-iot-central-application-new-ui-design"></a>Gerir dispositivos na sua aplicação do Azure IoT Central (design de nova interface do Usuário)

Este artigo descreve como, como um operador, para gerir dispositivos na sua aplicação do Azure IoT Central. Como um operador, pode:

- Utilize o **Device Explorer** página para visualizar, adicionar e eliminar dispositivos ligados à sua aplicação do Azure IoT Central.
- Manter um inventário atualizado dos seus dispositivos.
- Mantenha-se os metadados do dispositivo atualizado, alterando os valores armazenados nas propriedades do dispositivo.
- Controlar o comportamento dos seus dispositivos ao atualizar uma definição num dispositivo específico a partir da **definições** página.

## <a name="view-your-devices"></a>Ver os seus dispositivos

Para ver um dispositivo individual:

1. Escolher **Device Explorer** no menu de navegação esquerdo. Aqui verá uma lista de sua [modelos de dispositivos](howto-set-up-template.md).

1. Escolha um modelo de dispositivo na **modelos** lista.

1. No painel da direita do **Device Explorer** página, verá uma lista de dispositivos criada a partir desse modelo de dispositivo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para que o dispositivo:

    ![Página de detalhes do dispositivo](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo para a sua aplicação do Azure IoT Central:

1. Escolher **Device Explorer** no menu de navegação esquerdo.

1. Escolha o modelo de dispositivo a partir do qual pretende criar um dispositivo.

1. Escolher + **novo**.

1. Escolher **reais** ou **Simulated**. Um dispositivo real é para um dispositivo físico que se liga a sua aplicação do Azure IoT Central. Um dispositivo simulado tem dados de exemplo gerados para pelo Azure IoT Central.

## <a name="import-devices"></a>Importar dispositivos

Para ligar o grande número de dispositivos para a sua aplicação, pode efetuar em massa dispositivos de importação de um ficheiro CSV. O ficheiro CSV deve ter as seguintes colunas e cabeçalhos:

* **IOTC_DeviceID** -o ID do dispositivo deve estar em minúsculo.
* **IOTC_DeviceName** -nesta coluna é opcional.

Ao registar de em massa dispositivos na sua aplicação:

1. Escolher **Device Explorer** no menu de navegação esquerdo.

1. No painel esquerdo, selecione o modelo de dispositivo para o qual pretende em massa criar os dispositivos.

    > [!NOTE]
    > Se não tiver um modelo de dispositivo, mas, em seguida, pode importar dispositivos sob **não associados dispositivos** e registá-los sem um modelo. Depois de tem sido importados dispositivos, em seguida, pode associá-las com um modelo.

1. Selecione **importação**.

    ![Ação de importação](./media/howto-manage-devices/BulkImport1.png)

1. Selecione o ficheiro CSV que tem a lista de identificações de dispositivo para ser importado.

1. Importação de dispositivo é iniciado depois do ficheiro foi carregado. Pode controlar o estado de importação na parte superior da grade de dispositivo.

1. Assim que a importação estiver concluída, é apresentada uma mensagem de êxito na grade de dispositivo.

    ![Importar com êxito](./media/howto-manage-devices/BulkImport3.png)

Se o dispositivo importar operação falhar, verá uma mensagem de erro a grade de dispositivo. Um ficheiro de registo capturar todos os erros é gerado para que pode baixar.

**Associação de dispositivos com um modelo**

Se registar dispositivos ao iniciar a importação sob **não associados dispositivos**, em seguida, os dispositivos são criados sem qualquer associação de modelo do dispositivo. Dispositivos têm de estar associados a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estes passos para associar os dispositivos com um modelo:

1. Escolher **Device Explorer** no menu de navegação esquerdo.

1. No painel esquerdo, escolha **não associados dispositivos**:

    ![Dispositivos não associados](./media/howto-manage-devices/UnassociatedDevices1.png)

1. Selecione os dispositivos que pretende associar com um modelo:

1. Selecione **associar**:

    ![Associar Dispositivos](./media/howto-manage-devices/UnassociatedDevices2.png)

1. Escolha o modelo da lista de modelos disponíveis e selecione **associar**.

1. Os dispositivos selecionados estão associados com o modelo de dispositivo que escolheu.

> [!NOTE]
> Depois de um dispositivo tiver sido associado um modelo não pode ser não associado nem associado a um modelo diferente.

## <a name="export-devices"></a>Exportar dispositivos

Para ligar um dispositivo real ao IoT Central, terá de sua cadeia de ligação. Pode exportar detalhes do dispositivo em massa para obter as informações necessárias criar cadeias de ligação do dispositivo. O processo de exportação cria um ficheiro CSV com a identidade de dispositivo, nome do dispositivo e as chaves para todos os dispositivos selecionados.

Em massa dispositivos de exportação da sua aplicação:

1. Escolher **Device Explorer** no menu de navegação esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo a partir do qual pretende exportar os dispositivos.

1. Selecione os dispositivos que pretende exportar e, em seguida, selecione o **exportar** ação.

    ![Exportar](./media/howto-manage-devices/Export1.png)

1. Inicia o processo de exportação. Pode controlar o estado na parte superior da grade.

1. Quando a exportação for concluída, é apresentada uma mensagem de êxito, juntamente com uma ligação para transferir o ficheiro gerado.

1. Selecione o **mensagem de êxito** para transferir o ficheiro numa pasta local no disco.

    ![Sucesso de exportação](./media/howto-manage-devices/Export2.png)

1. O ficheiro CSV exportado contém as seguintes colunas: ID de dispositivo, nome do dispositivo, as chaves de dispositivo e X509 thumbprints de certificado:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Ver [conectividade do dispositivo no Azure IoT Central](concepts-connectivity.md), para obter mais informações sobre cadeias de ligação e de dispositivos reais ao ligar à sua aplicação IoT Central.

## <a name="delete-a-device"></a>Eliminar um dispositivo

Para eliminar a um dispositivo simulado ou real da sua aplicação do Azure IoT Central:

1. Escolher **Device Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo que pretende eliminar.

1. Selecione a caixa junto ao dispositivo para eliminar.

1. Escolher **eliminar**.

## <a name="change-a-device-setting"></a>Alterar uma configuração de dispositivo

Definições de controlam o comportamento de um dispositivo. Em outras palavras, eles permitem que forneça entradas para o seu dispositivo. Pode ver e atualizar as definições do dispositivo na **detalhes do dispositivo** página.

1. Escolher **Device Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo cujas definições pretende alterar.

1. Escolha o **definições** separador. Aqui pode ver todas as definições de que tem o seu dispositivo e seus valores atuais. Para cada definição, pode ver se o dispositivo ainda está a sincronizar.

1. Modifique as definições para os valores que necessários. Pode modificar várias definições de cada vez e atualizá-los todos ao mesmo tempo.

1. Escolher **atualização**. Os valores são enviados para o seu dispositivo. Quando o dispositivo confirma a alteração da definição, o estado da definição de retorna ao **sincronizado**.

## <a name="change-a-property"></a>Alterar uma propriedade

Propriedades são os metadados do dispositivo associado ao dispositivo, como cidade e o número de série. Pode ver e atualizar as propriedades no **detalhes do dispositivo** página.

1. Escolher **Device Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades pretende alterar.

1. Escolha o **propriedades** separador, onde se veem todas as propriedades.

1. Modificar as propriedades da aplicação para os valores que necessários. Pode modificar várias propriedades de uma só vez e atualizá-los todos ao mesmo tempo. Escolher **atualização**.

> [!NOTE]
> Não é possível alterar o valor de _propriedades do dispositivo_. Propriedades do dispositivo são definidas pelo dispositivo e são só de leitura na aplicação do Azure IoT Central.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como gerir dispositivos na sua aplicação do Azure IoT Central, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Como utilizar conjuntos de dispositivo](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
