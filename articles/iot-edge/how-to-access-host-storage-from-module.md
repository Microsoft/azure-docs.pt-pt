---
title: Utilize o armazenamento local do dispositivo IoT Edge a partir de um módulo - Azure IoT Edge [ Microsoft Docs
description: Utilize variáveis ambientais e crie opções para permitir o acesso do módulo ao armazenamento local do dispositivo IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/18/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 079d5845917e63fadcf0466e5a744ed637d704ca
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "75434530"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Fornecer o acesso a módulos ao armazenamento local de um dispositivo

Além de armazenar dados utilizando serviços de armazenamento Azure ou no armazenamento de contentores do seu dispositivo, também pode dedicar armazenamento no próprio dispositivo IoT Edge para melhorar a fiabilidade, especialmente quando opera offline.

## <a name="link-module-storage-to-device-storage"></a>Link armazenamento de módulos para armazenamento de dispositivos

Para ativar uma ligação desde o armazenamento do módulo até ao armazenamento no sistema de hospedagem, crie uma variável ambiental para o seu módulo que aponta para uma pasta de armazenamento no recipiente. Em seguida, utilize as opções de criação para ligar essa pasta de armazenamento a uma pasta na máquina de acolhimento.

Por exemplo, se quiser ativar o hub IoT Edge para armazenar mensagens no armazenamento local do seu dispositivo e recuperá-las mais tarde, pode configurar as variáveis ambientais e criar opções no portal Azure na secção Definições de Tempo de **Execução.**

1. Tanto para o hub IoT Edge como para o agente IoT Edge, adicione uma variável ambiental chamada **StorageFolder** que aponta para um diretório no módulo.
1. Tanto para o hub IoT Edge como para o agente IoT Edge, adicione ligações para ligar um diretório local na máquina hospedeira a um diretório no módulo. Por exemplo:

   ![Adicione criar opções e variáveis ambientais para armazenamento local](./media/how-to-access-host-storage-from-module/offline-storage.png)

Ou, pode configurar o armazenamento local diretamente no manifesto de implantação. Por exemplo:

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

`<HostStoragePath>` Substitua `<ModuleStoragePath>` e com o seu caminho de armazenamento de hospedeiro e módulo; ambos os valores devem ser um caminho absoluto.

Por exemplo, num sistema `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` Linux, significa que o diretório **/etc/iotedge/armazenamento** no seu sistema de acolhimento está mapeado para o diretório **/iotedge/armazenamento/** no recipiente. Num sistema Windows, como `"Binds":["C:\\temp:C:\\contemp"]` outro exemplo, significa o diretório **C:\\** a temperatura no seu sistema de hospedagem está mapeada para o diretório **C:\\contemp** no recipiente.

Além disso, nos dispositivos Linux, certifique-se de que o perfil do utilizador para o seu módulo tem as permissões necessárias para ler, escrever e executar permissões para o diretório do sistema de anfitriões. Voltando ao exemplo anterior de permitir que o hub IoT Edge guarde mensagens no armazenamento local do seu dispositivo, precisa de conceder permissões ao seu perfil de utilizador, UID 1000. (O agente IoT Edge funciona como raiz, por isso não precisa de permissões adicionais.) Existem várias formas de gerir permissões `chown` de diretório nos sistemas Linux, incluindo a utilização para alterar o proprietário do diretório e, em seguida, `chmod` alterar as permissões, tais como:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Pode encontrar mais detalhes sobre a criação de opções a partir de [docs de estivador.](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate)

## <a name="next-steps"></a>Passos seguintes

Para um exemplo adicional de acesso ao armazenamento do hospedeiro a partir de um módulo, consulte os dados da [Loja na borda com o Armazenamento De Blob Azure no IoT Edge](how-to-store-data-blob.md).
