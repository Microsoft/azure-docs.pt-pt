---
title: Usar IoT Edge armazenamento local do dispositivo de um módulo-Azure IoT Edge | Microsoft Docs
description: Use variáveis de ambiente e crie opções para habilitar o acesso de módulo para IoT Edge armazenamento local do dispositivo.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75434530"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Fornecer o acesso a módulos ao armazenamento local de um dispositivo

Além de armazenar dados usando os serviços de armazenamento do Azure ou o armazenamento de contêiner do seu dispositivo, você também pode dedicar o armazenamento no próprio dispositivo IoT Edge host para maior confiabilidade, especialmente ao operar offline.

## <a name="link-module-storage-to-device-storage"></a>Vincular o armazenamento do módulo ao armazenamento do dispositivo

Para habilitar um link do armazenamento de módulo para o armazenamento no sistema host, crie uma variável de ambiente para o módulo que aponta para uma pasta de armazenamento no contêiner. Em seguida, utilize as opções de criar para vincular essa pasta de armazenamento para uma pasta no computador anfitrião.

Por exemplo, se você quisesse habilitar o IoT Edge Hub para armazenar mensagens no armazenamento local do dispositivo e recuperá-las mais tarde, poderá configurar as variáveis de ambiente e as opções de criação na portal do Azure na seção **configurações de tempo de execução** .

1. Para o Hub IoT Edge e o agente de IoT Edge, adicione uma variável de ambiente chamada **storageFolder** que aponta para um diretório no módulo.
1. Para o Hub IoT Edge e o agente de IoT Edge, adicione associações para conectar um diretório local no computador host a um diretório no módulo. Por exemplo:

   ![Adicionar opções de criação e variáveis de ambiente para armazenamento local](./media/how-to-access-host-storage-from-module/offline-storage.png)

Ou então, você pode configurar o armazenamento local diretamente no manifesto de implantação. Por exemplo:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"]
                }
            }
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        }
    },
    "edgeHub": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-hub:1.0",
            "createOptions": {
                "HostConfig": {
                    "Binds":["<HostStoragePath>:<ModuleStoragePath>"],
                    "PortBindings":{"5671/tcp":[{"HostPort":"5671"}],"8883/tcp":[{"HostPort":"8883"}],"443/tcp":[{"HostPort":"443"}]}}}
        },
        "type": "docker",
        "env": {
            "storageFolder": {
                "value": "<ModuleStoragePath>"
            }
        },
        "status": "running",
        "restartPolicy": "always"
    }
}
```

Substitua `<HostStoragePath>` e `<ModuleStoragePath>` pelo caminho de armazenamento do módulo e do host; ambos os valores devem ser um caminho absoluto.

Por exemplo, em um sistema Linux, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` significa que o diretório **/etc/iotedge/Storage** no seu sistema host é mapeado para o diretório **/iotedge/Storage/** no contêiner. Em um sistema Windows, como outro exemplo, `"Binds":["C:\\temp:C:\\contemp"]` significa que o diretório **c:\\Temp** no seu sistema host é mapeado para o diretório **c:\\contemp** no contêiner.

Além disso, em dispositivos Linux, certifique-se de que o perfil do usuário para o módulo tenha as permissões de leitura, gravação e execução necessárias para o diretório do sistema de host. Voltando ao exemplo anterior de como habilitar IoT Edge Hub para armazenar mensagens no armazenamento local do dispositivo, você precisa conceder permissões para seu perfil de usuário, UID 1000. (O agente de IoT Edge Opera como raiz, portanto, não precisa de permissões adicionais.) Há várias maneiras de gerenciar permissões de diretório em sistemas Linux, incluindo o uso de `chown` para alterar o proprietário do diretório e, em seguida, `chmod` alterar as permissões, como:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Você pode encontrar mais detalhes sobre como criar opções de [documentos do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="next-steps"></a>Passos seguintes

Para obter um exemplo adicional de acesso ao armazenamento de host de um módulo, consulte [armazenar dados na borda com o armazenamento de BLOBs do Azure em IOT Edge](how-to-store-data-blob.md).
