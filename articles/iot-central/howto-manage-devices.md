---
title: Gerir os dispositivos na sua aplicação do Azure IoT Central | Documentos da Microsoft
description: Como um operador, saiba como gerir dispositivos na sua aplicação do Azure IoT Central.
author: ellenfosborne
ms.author: elfarber
ms.date: 11/02/2018
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 22158d6a5a8c2768ee343f86160f79fc0dbd241b
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/06/2019
ms.locfileid: "57456266"
---
# <a name="manage-devices-in-your-azure-iot-central-application"></a>Gerir dispositivos na sua aplicação do Azure IoT Central

Este artigo descreve como, como um operador, para gerir dispositivos na sua aplicação do Azure IoT Central. Como um operador, pode:

- Utilize o **Device Explorer** página para visualizar, adicionar e eliminar dispositivos ligados à sua aplicação do Azure IoT Central.
- Manter um inventário atualizado dos seus dispositivos.
- Mantenha-se os metadados do dispositivo atualizado, alterando os valores armazenados nas propriedades do dispositivo.
- Controlar o comportamento dos seus dispositivos ao atualizar uma definição num dispositivo específico a partir da **definições** página.

[!INCLUDE [iot-central-experimental-note](../../includes/iot-central-experimental-note.md)]

## <a name="view-your-devices"></a>Ver os seus dispositivos

Para ver um dispositivo individual:

1. Escolher **Device Explorer** no menu de navegação esquerdo. Aqui verá uma lista de sua [modelos de dispositivos](howto-set-up-template.md).

1. Escolha um modelo de dispositivo em modelos no painel esquerdo.

1. No painel da direita da página do Device Explorer, verá uma lista de dispositivos criada a partir desse modelo de dispositivo, conforme mostrado abaixo. Escolha um dispositivo individual para ver a página de detalhes do dispositivo para que o dispositivo:

    [![Página de detalhes do dispositivo](./media/howto-manage-devices/image1.png)](./media/howto-manage-devices/image1.png#lightbox)

## <a name="add-a-device"></a>Adicionar um dispositivo

Para adicionar um dispositivo para a sua aplicação do Azure IoT Central:

1. Escolher **Device Explorer** no menu de navegação esquerdo.

1. Escolha o modelo de dispositivo a partir do qual pretende criar um dispositivo.

1. Escolher + **novo**.

1. Escolher **reais** ou **Simulated**. Um dispositivo real é para um dispositivo físico que se liga a sua aplicação do Azure IoT Central. Um dispositivo simulado tem dados de exemplo gerados para pelo Azure IoT Central. Este exemplo utiliza um dispositivo real. Escolher **reais** para navegar para o **detalhes do dispositivo** página para o seu dispositivo novo.


## <a name="import-devices"></a>Importar dispositivos

Para ligar o grande número de dispositivos à sua aplicação, o Azure IoT Central ofertas em massa importando dispositivos através de um ficheiro CSV. O ficheiro CSV deve ter as seguintes colunas (e cabeçalhos)
1.  IOTC_DeviceID  **<span style="color:Red">(deve ser em minúsculas)</span>**
1.  IOTC_DeviceName (opcional)


Ao registar de em massa dispositivos na sua aplicação:

1. Escolher **Device Explorer** no menu de navegação esquerdo.

1. No painel esquerdo, selecione o modelo de dispositivo para o qual pretende em massa criar os dispositivos.

 >   [!NOTE] 
    Se não tiver um modelo de dispositivo, mas, em seguida, pode importar dispositivos sob **não associados dispositivos** e registá-los sem qualquer modelo. Assim que os dispositivos forem importados, pode, em seguida, associá-las com um modelo como um passo subsequente.

1. Selecione **importação**.

    [![Ação de importação](./media/howto-manage-devices/BulkImport1.png)](./media/howto-manage-devices/BulkImport1.png#lightbox)

1. Selecione o ficheiro CSV que tem a lista de identificações de dispositivo para ser importado.

1. Importação de dispositivo é iniciado depois do ficheiro foi carregado. Pode controlar o estado de importação na parte superior da grade de dispositivo.

1. Assim que a importação estiver concluída, é apresentada uma mensagem de êxito na grade de dispositivo.

    [![Importar com êxito](./media/howto-manage-devices/BulkImport3.png)](./media/howto-manage-devices/BulkImport3.png#lightbox)

Se o dispositivo importar falha da operação, verá uma mensagem de erro na grade de dispositivo. Um ficheiro de registo capturar todos os erros é gerado e pode ser baixado ao selecionar a mensagem de erro.


**Associação de dispositivos com um modelo**

Se registar dispositivos ao iniciar a importação sob **não associados dispositivos**, em seguida, os dispositivos são criados sem qualquer associação de modelo do dispositivo. Dispositivo tem de estar associado a um modelo para explorar os dados e outros detalhes sobre o dispositivo. Siga estes passos para associar os dispositivos com um modelo:
1. Escolher **Device Explorer** no menu de navegação esquerdo.
1. No painel esquerdo, escolha **não associados dispositivos**.
    [![Dispositivos não associados](./media/howto-manage-devices/UnassociatedDevices1.png)](./media/howto-manage-devices/UnassociatedDevices1.png#lightbox)
1. Selecione os dispositivos que pretende associar com um modelo.
1. Selecione **associar** opção.
    [![Associar dispositivos](./media/howto-manage-devices/UnassociatedDevices2.png)](./media/howto-manage-devices/UnassociatedDevices2.png#lightbox)
1. Escolha o modelo da lista de modelos disponíveis e selecione **associar**.
1. Os dispositivos selecionados serão movidos sob o modelo do respectivo dispositivo.

 >   [!NOTE] 
    Depois de um dispositivo tiver sido associado um modelo não pode ser não associado nem associado a um modelo diferente.

## <a name="export-devices"></a>Exportar dispositivos

Aprovisionar dispositivos para estabelecer ligação ao IoT Central, terá da cadeia de ligação do dispositivo que é gerado pelo centro de IoT. Pode utilizar a funcionalidade de exportação para obter as cadeias de ligação e outras propriedades dos dispositivos em massa a partir da sua aplicação. Exportar cria um ficheiro CSV com a identidade de dispositivo, o nome do dispositivo e a cadeia de ligação primária para todos os dispositivos selecionados.

Em massa dispositivos de exportação da sua aplicação:
1. Escolher **Device Explorer** no menu de navegação esquerdo.

1. No painel esquerdo, escolha o modelo de dispositivo para o qual pretende exportar os dispositivos.

1. Selecione os dispositivos que pretende exportar e, em seguida, selecione o **exportar** ação.

    [![Exportar](./media/howto-manage-devices/Export1.png)](./media/howto-manage-devices/Export1.png#lightbox)

1. Será iniciado o processo de exportação e pode controlar o estado na parte superior da grade. 

1. Quando a exportação for concluída, é apresentada uma mensagem de êxito, juntamente com uma ligação para transferir o ficheiro gerado.

1. Selecione o **mensagem de êxito** para transferir o ficheiro numa pasta local no disco.

    [![Sucesso de exportação](./media/howto-manage-devices/Export2.png)](./media/howto-manage-devices/Export2.png#lightbox)

1. O ficheiro CSV exportado terá as seguintes informações de colunas: **Id do dispositivo, nome do dispositivo, as chaves de Priamry/secundária de dispositivo e primária/secundária de thumbprints de certificado**
    *   IOTC_DEVICEID
    *   IOTC_DEVICENAME
    *   IOTC_SASKEY_PRIMARY
    *   IOTC_SASKEY_SECONDARY
    *   IOTC_X509THUMBPRINT_PRIMARY 
    *   IOTC_X509THUMBPRINT_SECONDARY

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

1. Modifique as definições para os valores pretendidos. Pode modificar várias definições ao mesmo tempo e atualizá-los todos ao mesmo tempo.

1. Escolher **atualização**. Os valores são enviados para o seu dispositivo. Quando o dispositivo reconhece a alteração da definição, o estado da definição de retorna ao **sincronizado**.

## <a name="change-a-property"></a>Alterar uma propriedade

Propriedades são os metadados do dispositivo associado ao dispositivo, como cidade e o número de série. Pode ver e atualizar as propriedades no **detalhes do dispositivo** página.

1. Escolher **Device Explorer** no menu de navegação.

1. Escolha o modelo de dispositivo do dispositivo cujas propriedades pretende alterar.

1. Escolha o **propriedades** separador, onde se veem todas as propriedades.

1. Modificar as propriedades da aplicação para os valores pretendidos. Pode modificar várias propriedades de uma só vez e atualizá-los todos ao mesmo tempo. Escolher **atualização**.

> [!NOTE]
> Não é possível alterar o valor de _propriedades do dispositivo_. Propriedades do dispositivo são definidas pelo dispositivo e são só de leitura na aplicação do Azure IoT Central.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como gerir dispositivos na sua aplicação do Azure IoT Central, este é o passo seguinte sugerido:

> [!div class="nextstepaction"]
> [Como utilizar conjuntos de dispositivo](howto-use-device-sets.md)

<!-- Next how-tos in the sequence -->
