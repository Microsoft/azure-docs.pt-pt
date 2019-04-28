---
title: Solução de monitorização remota, importar o pacote do Edge - Azure | Documentos da Microsoft
description: Este artigo descreve como importar um pacote do IoT Edge para o acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: v-yiso
ms.service: iot-accelerators
services: iot-accelerators
origin.date: 10/10/2018
ms.date: 12/17/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61443016"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importar um pacote do IoT Edge para o seu acelerador de solução de monitorização remota

Um manifesto de implantação define os módulos para implementar um dispositivo IoT Edge. Este artigo pressupõe que um desenvolvedor na sua organização já tiver criado o manifesto de implantação. Para saber mais sobre como um desenvolvedor cria um manifesto, consulte um dos seguintes artigos de procedimentos de IoT Edge:

- [Implementar módulos do Azure IoT Edge do portal do Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Implementar módulos do Azure IoT Edge com a CLI do Azure](../iot-edge/how-to-deploy-modules-cli.md)
- [Implementar módulos do Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Um desenvolvedor cria e testa um manifesto de implantação num ambiente de desenvolvimento. Quando estiver pronto, pode importar o manifesto para o seu acelerador de solução de monitorização remota.

## <a name="export-a-manifest"></a>Exportar um manifesto

Utilize o portal do Azure para exportar o manifesto de implantação do seu ambiente de desenvolvimento:

1. No portal do Azure, navegue para o hub IoT que está a utilizar para desenvolver e testar seus dispositivos IoT Edge. Clique em **IoT Edge** e, em seguida **implementações do IoT Edge**: ![IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Clique na implementação que tem a configuração de implementação que pretende utilizar. O **detalhes de implementação** for exibido: ![Detalhes de implementação do IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Clique em **manifesto de transferir o IoT Edge**:  ![Transferir o manifesto de implantação](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Guarde o ficheiro JSON como um ficheiro local chamado **deploymentmanifest.json**.

Agora tem um ficheiro que contém o manifesto de implantação. Na próxima seção, importe esse manifesto como um pacote para a solução de monitorização remota.

## <a name="import-a-package"></a>Importar um pacote

Siga os passos abaixo para importar um manifesto de implantação do Edge como um pacote para a sua solução:

1. Navegue para o **pacotes** página na monitorização remota da IU da web:  ![Página de pacotes](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Clique em **+ novo pacote**, escolha **Edge manifesto** como o tipo de pacote e clique em **procurar** para selecionar o **deploymentmanifest.json** ficheiro que guardou na secção anterior:  ![Selecione o manifesto](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Clique em **carregar** para adicionar o pacote à sua solução de monitorização remota:  ![Pacote carregado](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Agora que já carregou um manifesto de implantação do IoT Edge como um pacote. Sobre o **implementações** página, pode implementar este pacote em seus dispositivos IoT Edge ligados.

## <a name="next-steps"></a>Passos Seguintes

Agora que sabe como implementar módulos num dispositivo IoT Edge da solução de monitorização remota, a próxima etapa é saber mais sobre [IoT Edge](../iot-edge/about-iot-edge.md).
