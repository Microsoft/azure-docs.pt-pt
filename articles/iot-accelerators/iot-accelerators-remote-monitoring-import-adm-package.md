---
title: Solução de monitorização remota importar pacote - Azure | Documentos da Microsoft
description: Este artigo descreve como importar um pacote de gestão do dispositivo automática para o acelerador de solução de monitorização remota
author: dominicbetts
manager: philmea
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/29/2018
ms.topic: conceptual
ms.openlocfilehash: 8100914e9a1d1489cb80de55a689e17f6d28a941
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "61443450"
---
# <a name="import-an-automatic-device-management-package-into-your-remote-monitoring-solution-accelerator"></a>Importar um pacote de gestão do dispositivo automática para o seu acelerador de solução de monitorização remota

Uma configuração de gestão de dispositivos automático define as alterações de configuração para implementar um grupo de dispositivos. Este artigo pressupõe que um desenvolvedor na sua organização já tiver criado uma configuração de gestão de dispositivos automática. Para saber mais sobre como um desenvolvedor cria uma configuração, consulte um dos seguintes artigos de procedimentos de IoT Hub:

- [Configurar e monitorizar dispositivos de IoT em escala no portal do Azure](../iot-hub/iot-hub-auto-device-config.md)
- [Configurar e monitorizar dispositivos de IoT em escala com a CLI do Azure](../iot-hub/iot-hub-auto-device-config-cli.md)

Um desenvolvedor cria e testa uma configuração de gestão de dispositivos automática num ambiente de desenvolvimento. Quando estiver pronto, pode importar a configuração para o seu acelerador de solução de monitorização remota.

## <a name="export-a-configuration"></a>Exportar uma configuração

Utilize o portal do Azure para exportar a configuração de gestão de dispositivos automática do seu ambiente de desenvolvimento:

1. No portal do Azure, navegue para o hub IoT que está a utilizar para desenvolver e testar seus dispositivos IoT. Clique em **configuração do dispositivo IoT**:

    [![Configuração do dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/deviceconfiguration-expanded.png#lightbox)

1. Clique em configuração que pretende utilizar. O **detalhes de configuração do dispositivo** for exibido:

    [![Detalhes da configuração do dispositivo IoT](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/configuration-details-expanded.png#lightbox)
1. Clique em **o ficheiro de configuração de Download**:

    [![Transferir o ficheiro de configuração](./media/iot-accelerators-remote-monitoring-import-adm-package/download-inline.png)](./media/iot-accelerators-remote-monitoring-import-adm-package/download-expanded.png#lightbox)

1. Guarde o ficheiro JSON como um ficheiro local chamado **configuration.json**.

Agora tem um ficheiro que contém a configuração de gestão de dispositivos automática. Na próxima seção, importar esta configuração como um pacote para a solução de monitorização remota.

## <a name="import-a-package"></a>Importar um pacote

Siga os passos abaixo para importar uma configuração de gestão de dispositivos automático como um pacote para sua solução:

1. Navegue para o **pacotes** página na monitorização remota da IU da web:  ![Página de pacotes](media/iot-accelerators-remote-monitoring-import-adm-package/packagepage.png)

1. Clique em **+ novo pacote**, escolha **configuração** como o tipo de pacote e clique em **procurar** para selecionar o **configuration.json** ficheiro guardou na secção anterior:

    ![Selecione a configuração](media/iot-accelerators-remote-monitoring-import-adm-package/uploadpackage.png)

1. Clique em **carregar** para adicionar o pacote à sua solução de monitorização remota:

    ![Pacote carregado](media/iot-accelerators-remote-monitoring-import-adm-package/uploadedpackage.png)

Agora que já carregou uma configuração de gestão de dispositivos automático como um pacote. Sobre o **implementações** página, pode implementar este pacote em seus dispositivos ligados.

## <a name="next-steps"></a>Passos Seguintes

Agora que aprendeu como criar um pacote de configuração e importar para da solução de monitorização remota, a próxima etapa é saber como [gerir dispositivos ligados a monitorização remota em massa](iot-accelerators-remote-monitoring-bulk-configuration-update.md).
