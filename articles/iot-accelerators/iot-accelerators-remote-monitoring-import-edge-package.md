---
title: Pacote Edge de solução de monitorização remota - Azure [ Microsoft Docs
description: Este artigo descreve como importar um pacote IoT Edge para o acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "61443016"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importe um pacote IoT Edge para o seu acelerador de solução de monitorização remota

Um manifesto de implantação define os módulos para implantar num dispositivo IoT Edge. Este artigo assume que um desenvolvedor na sua organização já criou um manifesto de implantação. Para saber como um desenvolvedor cria um manifesto, consulte um dos seguintes artigos ioT Edge how-to:

- [Implante módulos Azure IoT Edge do portal Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Implementar módulos Azure IoT Edge com Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Implementar módulos Azure IoT Edge do Visual Studio Code](../iot-edge/how-to-deploy-modules-vscode.md)

Um desenvolvedor cria e testa um manifesto de implantação num ambiente de desenvolvimento. Quando estiver pronto, pode importar o manifesto para o seu acelerador de solução de monitorização remota.

## <a name="export-a-manifest"></a>Exportar um manifesto

Utilize o portal Azure para exportar o manifesto de implantação do seu ambiente de desenvolvimento:

1. No portal Azure, navegue para o hub IoT que está a usar para desenvolver e testar os seus dispositivos IoT Edge. Clique em **IoT Edge** e, em ![ **seguida, ioT Edge implementações**: IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Clique na implementação que tem a configuração de implementação que pretende utilizar. A página de ![Detalhes de **Implementação** exibe: Detalhes de implementação de IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Clique no **manifesto de download IoT Edge**: ![Descarregamento manifesto de implementação](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Guarde o ficheiro JSON como um ficheiro local chamado **deploymentmanifest.json**.

Agora tem um ficheiro que contém o manifesto de implantação. Na secção seguinte, importa este manifesto como um pacote para a solução de Monitorização Remota.

## <a name="import-a-package"></a>Importar um pacote

Siga os passos abaixo para importar um manifesto de implantação edge como um pacote para a sua solução:

1. Navegue para a página **pacotes** na página ![web de monitorização remota UI: Página de pacotes](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Clique **+ Novo Pacote,** escolha **Edge Manifest** como o tipo de pacote e clique **em Navegar** para selecionar o ficheiro **deploymentmanifest.json** que guardou na secção anterior: ![Selecione manifesto](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Clique em **Carregar** para adicionar o pacote ![à sua solução de monitorização remota: pacote carregado](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Já carregaste um manifesto de implantação da IoT Edge como um pacote. Na página **de Implementações,** pode implantar este pacote nos seus dispositivos IoT Edge ligados.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implementar módulos para um dispositivo IoT Edge a partir da solução de Monitorização Remota, o próximo passo é saber mais sobre [ioT Edge](../iot-edge/about-iot-edge.md).
