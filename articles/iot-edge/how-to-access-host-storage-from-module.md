---
title: Usar IoT Edge armazenamento local do dispositivo de um módulo-Azure IoT Edge | Microsoft Docs
description: Use variáveis de ambiente e crie opções para habilitar o acesso de módulo para IoT Edge armazenamento local do dispositivo.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 10/12/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 2526f33f0053b5805394a4a898af88d86187066c
ms.sourcegitcommit: 8b44498b922f7d7d34e4de7189b3ad5a9ba1488b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/13/2019
ms.locfileid: "72301285"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Fornecer acesso de módulos ao armazenamento local de um dispositivo

Além de armazenar dados usando os serviços de armazenamento do Azure ou o armazenamento de contêiner do seu dispositivo, você também pode dedicar o armazenamento no próprio dispositivo IoT Edge host para maior confiabilidade, especialmente ao operar offline.

Para configurar o armazenamento no sistema host, crie uma variável de ambiente para o módulo que aponte para uma pasta de armazenamento no contêiner. Em seguida, use as opções de criação para associar essa pasta de armazenamento a uma pasta no computador host.

Por exemplo, se você quisesse habilitar o Hub de IoT Edge para armazenar mensagens no armazenamento local do dispositivo e recuperá-las mais tarde, você pode configurar as variáveis de ambiente e as opções de criação na portal do Azure nas **configurações de tempo de execução avançado do Edge** seção.

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

Por exemplo, em um sistema Linux, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` significa que o diretório **/etc/iotedge/Storage** no seu sistema host é mapeado para o diretório **/iotedge/Storage/** no contêiner. Em um sistema Windows, como outro exemplo, `"Binds":["C:\\temp:C:\\contemp"]` significa que o diretório **c: \\temp** no seu sistema host é mapeado para o diretório **c: \\contemp** no contêiner.

Além disso, em dispositivos Linux, certifique-se de que o perfil do usuário para o módulo tenha as permissões de leitura, gravação e execução necessárias para o diretório do sistema de host. Voltando ao exemplo anterior de como habilitar IoT Edge Hub para armazenar mensagens no armazenamento local do dispositivo, você precisa conceder permissões para seu perfil de usuário, UID 1000. (O agente de IoT Edge Opera como raiz, portanto, não precisa de permissões adicionais.) Há várias maneiras de gerenciar permissões de diretório em sistemas Linux, incluindo o uso de `chown` para alterar o proprietário do diretório e, em seguida, `chmod` para alterar as permissões, como:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Você pode encontrar mais detalhes sobre como criar opções de [documentos do Docker](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).
