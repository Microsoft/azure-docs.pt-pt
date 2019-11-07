---
title: Gerenciar os dispositivos em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como um operador, saiba como gerenciar dispositivos em seu aplicativo de IoT Central do Azure.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: adcaa29ed894f2d61871f467369bcdd05f8cc593
ms.sourcegitcommit: c62a68ed80289d0daada860b837c31625b0fa0f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/05/2019
ms.locfileid: "73601605"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerenciar dispositivos em seu aplicativo de IoT Central do Azure

[!INCLUDE [iot-central-original-pnp](../../../includes/iot-central-original-pnp-note.md)]

Este artigo descreve como, como um operador, gerenciar dispositivos em seu aplicativo IoT Central do Azure. Como um operador, você pode:

- Use a página **dispositivos** para exibir, adicionar e excluir dispositivos conectados ao seu aplicativo IOT central do Azure.
- Mantenha um inventário atualizado dos seus dispositivos.
- Mantenha os metadados do dispositivo atualizados alterando os valores armazenados nas propriedades do dispositivo.
- Controle o comportamento de seus dispositivos atualizando uma configuração em um dispositivo específico na página **configurações** .

## <a name="view-your-devices"></a>Ver os seus dispositivos

Para exibir um dispositivo individual:

1. Escolha **dispositivos** no painel esquerdo. Aqui você vê uma lista de seus [modelos de dispositivo](howto-set-up-template.md).

1. Escolha um modelo de dispositivo na lista **modelos** .

1. No painel à direita da página **dispositivos** , você verá uma lista de dispositivos criados a partir desse modelo de dispositivo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para esse dispositivo:

    ![Página de detalhes do dispositivo](./media/howto-manage-devices/devicelist.png)

## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo ao aplicativo de IoT Central do Azure:

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do qual você deseja criar um dispositivo.

1. Escolha + **novo**.

1. Escolha **real** ou **simulado**. Um dispositivo real é para um dispositivo físico que você conecta ao seu aplicativo de IoT Central do Azure. Um dispositivo simulado tem dados de exemplo gerados para você pelo Azure IoT Central.

## <a name="import-devices"></a>Importar dispositivos

Para conectar um grande número de dispositivos ao seu aplicativo, você pode importar dispositivos em massa de um arquivo CSV. O arquivo CSV deve ter as seguintes colunas e cabeçalhos:

* **IOTC_DeviceID** -a ID do dispositivo deve estar em minúsculas.
* **IOTC_DeviceName** -essa coluna é opcional.

Para registrar dispositivos em massa em seu aplicativo:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual você deseja criar os dispositivos em massa.

    > [!NOTE]
    > Se você ainda não tiver um modelo de dispositivo, poderá importar dispositivos em **dispositivos não associados** e registrá-los sem um modelo. Depois que os dispositivos tiverem sido importados, você poderá associá-los a um modelo.

1. Selecione **importar**.

    ![Importar ação](./media/howto-manage-devices/bulkimport1a.png)

1. Selecione o arquivo CSV que tem a lista de IDs de dispositivo a ser importada.

1. A importação do dispositivo é iniciada depois que o arquivo é carregado. Você pode acompanhar o status de importação na parte superior da grade do dispositivo.

1. Quando a importação for concluída, uma mensagem de êxito será mostrada na grade do dispositivo.

    ![Êxito na importação](./media/howto-manage-devices/bulkimport3a.png)

Se a operação de importação de dispositivo falhar, você verá uma mensagem de erro na grade do dispositivo. Um arquivo de log capturando todos os erros é gerado e você pode baixá-lo.

**Associando dispositivos a um modelo**

Se você registrar dispositivos iniciando a importação em **dispositivos não associados**, os dispositivos serão criados sem nenhuma associação de modelo de dispositivo. Os dispositivos devem ser associados a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estas etapas para associar dispositivos a um modelo:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha **dispositivos não associados**:

    ![Dispositivos não associados](./media/howto-manage-devices/unassociateddevices1a.png)

1. Selecione os dispositivos que você deseja associar a um modelo:

1. Selecione **associar**:

    ![Associar dispositivos](./media/howto-manage-devices/unassociateddevices2a.png)

1. Escolha o modelo na lista de modelos disponíveis e selecione **associar**.

1. Os dispositivos selecionados estão associados ao modelo de dispositivo escolhido.

> [!NOTE]
> Depois que um dispositivo tiver sido associado a um modelo, ele não poderá ser não associado nem associado a um modelo diferente.

## <a name="export-devices"></a>Exportar dispositivos

Para conectar um dispositivo real ao IoT Central, você precisa de sua cadeia de conexão. Você pode exportar detalhes do dispositivo em massa para obter as informações necessárias para criar cadeias de conexão de dispositivo. O processo de exportação cria um arquivo CSV com a identidade do dispositivo, o nome do dispositivo e as chaves para todos os dispositivos selecionados.

Para exportar em massa dispositivos do seu aplicativo:

1. Escolha **dispositivos** no painel esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo do qual você deseja exportar os dispositivos.

1. Selecione os dispositivos que você deseja exportar e, em seguida, selecione a ação **Exportar** .

    ![Exportar](./media/howto-manage-devices/export1a.png)

1. O processo de exportação é iniciado. Você pode acompanhar o status na parte superior da grade.

1. Quando a exportação for concluída, uma mensagem de êxito será mostrada junto com um link para baixar o arquivo gerado.

1. Selecione a **mensagem de êxito** para baixar o arquivo em uma pasta local no disco.

    ![Êxito na exportação](./media/howto-manage-devices/export2a.png)

1. O arquivo CSV exportado contém as seguintes colunas: ID do dispositivo, nome do dispositivo, chaves do dispositivo e impressões digitais do certificado X509:

    * IOTC_DEVICEID
    * IOTC_DEVICENAME
    * IOTC_SASKEY_PRIMARY
    * IOTC_SASKEY_SECONDARY
    * IOTC_X509THUMBPRINT_PRIMARY
    * IOTC_X509THUMBPRINT_SECONDARY

Consulte [conectividade do dispositivo no Azure IOT central](concepts-connectivity.md), para obter mais informações sobre cadeias de conexão e conectar dispositivos reais ao seu aplicativo IOT central.

## <a name="delete-a-device"></a>Excluir um dispositivo

Para excluir um dispositivo real ou simulado do seu aplicativo de IoT Central do Azure:

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo que você deseja excluir.

1. Marque a caixa ao lado do dispositivo a ser excluído.

1. Escolha **excluir**.

## <a name="change-a-device-setting"></a>Alterar uma configuração de dispositivo

As configurações controlam o comportamento de um dispositivo. Em outras palavras, eles permitem que você forneça entradas para seu dispositivo. Você pode exibir e atualizar as configurações do dispositivo na página de **detalhes do dispositivo** .

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo cujas configurações você deseja alterar.

1. Escolha a guia **configurações** . Aqui você vê todas as configurações que seu dispositivo tem e seus valores atuais. Para cada configuração, você pode ver se o dispositivo ainda está sincronizando.

1. Modifique as configurações para os valores necessários. Você pode modificar várias configurações por vez e atualizá-las ao mesmo tempo.

1. Escolha **Atualizar**. Os valores são enviados ao seu dispositivo. Quando o dispositivo confirma a alteração da configuração, o status da configuração retorna para **sincronizado**.

## <a name="change-a-property"></a>Alterar uma propriedade

As propriedades são os metadados de dispositivo associados ao dispositivo, como cidade e número de série. Você pode exibir e atualizar as propriedades na página de **detalhes do dispositivo** .

1. Escolha **dispositivos** no painel esquerdo.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades você deseja alterar.

1. Escolha a guia **Propriedades** , onde você verá todas as propriedades.

1. Modifique as propriedades do aplicativo para os valores necessários. Você pode modificar várias propriedades de uma vez e atualizá-las ao mesmo tempo. Escolha **Atualizar**.

> [!NOTE]
> Você não pode alterar o valor das _Propriedades do dispositivo_. As propriedades do dispositivo são definidas pelo dispositivo e são somente leitura no aplicativo IoT Central do Azure.

## <a name="next-steps"></a>Passos seguintes

Agora que você aprendeu como gerenciar dispositivos em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como usar conjuntos de dispositivos](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
