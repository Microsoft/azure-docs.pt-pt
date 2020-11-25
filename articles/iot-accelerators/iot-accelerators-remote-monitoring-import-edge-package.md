---
title: Pacote de importação de solução de monitorização remota Edge - Azure / Microsoft Docs
description: Este artigo descreve como importar um pacote IoT Edge para o acelerador de solução de monitorização remota
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/10/2018
ms.topic: conceptual
ms.openlocfilehash: 34222f396ed3c43932371aa9f64a459bb2a5dd0e
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96012293"
---
# <a name="import-an-iot-edge-package-into-your-remote-monitoring-solution-accelerator"></a>Importe um pacote IoT Edge no seu acelerador de solução de monitorização remota

Um manifesto de implantação define os módulos para implantar num dispositivo IoT Edge. Este artigo assume que um desenvolvedor na sua organização já criou um manifesto de implantação. Para saber como um desenvolvedor cria um manifesto, consulte um dos seguintes artigos ioT Edge como fazer:

- [Implementar módulos Azure IoT Edge a partir do portal Azure](../iot-edge/how-to-deploy-modules-portal.md)
- [Implementar módulos Azure IoT Edge com Azure CLI](../iot-edge/how-to-deploy-modules-cli.md)
- [Implementar módulos Azure IoT Edge a partir do Código do Estúdio Visual](../iot-edge/how-to-deploy-modules-vscode.md)

Um desenvolvedor cria e testa um manifesto de implantação num ambiente de desenvolvimento. Quando estiver pronto, pode importar o manifesto para o seu acelerador de solução de monitorização remota.

## <a name="export-a-manifest"></a>Exportar um manifesto

Utilize o portal Azure para exportar o manifesto de implantação do seu ambiente de desenvolvimento:

1. No portal Azure, navegue até ao hub IoT que está a usar para desenvolver e testar os seus dispositivos IoT Edge. Clique **em IoT Edge** e, em seguida, **ioT edge implementações**: ![ IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/iotedge.png)

1. Clique na implementação que tem a configuração de implementação que pretende utilizar. A página **Detalhes de Implementação** apresenta: ![ detalhes de implementação IoT Edge](media/iot-accelerators-remote-monitoring-import-edge-package/deploymentdetails.png)

1. Clique **em Download IoT Edge manifesto**:  ![ Descarregue o manifesto de implementação](media/iot-accelerators-remote-monitoring-import-edge-package/download.png)

1. Guarde o ficheiro JSON como um ficheiro local chamado **deploymentmanifest.jsligado**.

Agora tem um ficheiro que contém o manifesto de implantação. Na secção seguinte, importa este manifesto como um pacote para a solução de Monitorização Remota.

## <a name="import-a-package"></a>Importar um pacote

Siga os passos abaixo para importar um manifesto de implantação edge como um pacote para a sua solução:

1. Navegue para a página **pacotes** na web de monitorização remota UI:  ![ página de pacotes](media/iot-accelerators-remote-monitoring-import-edge-package/packagespage.png)

1. Clique **+ Pacote Novo,** escolha **o Edge Manifest** como o tipo de pacote e clique em **procurar** para selecionar o **deploymentmanifest.jsno** ficheiro guardado na secção anterior:  ![ Selecione manifesto](media/iot-accelerators-remote-monitoring-import-edge-package/selectmanifest.png)

1. Clique **em Upload** para adicionar o pacote à sua solução de Monitorização Remota: Pacote  ![ carregado](media/iot-accelerators-remote-monitoring-import-edge-package/uploadedpackage.png)

Agora enviaste um manifesto de implantação IoT Edge como um pacote. Na página **Implementações,** pode implantar este pacote nos seus dispositivos IoT Edge ligados.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a implantar módulos para um dispositivo IoT Edge a partir da solução de Monitorização Remota, o próximo passo é saber mais sobre [o IoT Edge](../iot-edge/about-iot-edge.md).
