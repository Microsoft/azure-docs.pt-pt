---
title: Utilize o armazenamento local do dispositivo IoT Edge a partir de um módulo - Azure IoT Edge | Microsoft Docs
description: Use variáveis ambientais e crie opções para permitir o acesso do módulo ao armazenamento local do dispositivo IoT Edge.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 08/14/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: d88d35eece698c7d0079221ae3c76058d1877948
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200481"
---
# <a name="give-modules-access-to-a-devices-local-storage"></a>Fornecer o acesso a módulos ao armazenamento local de um dispositivo

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Além de armazenar dados utilizando serviços de armazenamento Azure ou no armazenamento de contentores do seu dispositivo, também pode dedicar armazenamento no próprio dispositivo IoT Edge do anfitrião para uma maior fiabilidade, especialmente quando opera offline.

## <a name="link-module-storage-to-device-storage"></a>Link armazenamento de módulos para armazenamento de dispositivos

Para ativar uma ligação desde o armazenamento do módulo ao armazenamento no sistema hospedeiro, crie uma variável ambiental para o seu módulo que aponta para uma pasta de armazenamento no recipiente. Em seguida, utilize as opções de criação para ligar essa pasta de armazenamento a uma pasta na máquina de anfitrião.

Por exemplo, se pretender ativar o hub IoT Edge para armazenar mensagens no armazenamento local do seu dispositivo e recuperá-las mais tarde, pode configurar as variáveis ambientais e criar opções no portal Azure na secção **Definições de Tempo** de Execução.

1. Para o hub IoT Edge e o agente IoT Edge, adicione uma variável ambiental chamada **StorageFolder** que aponta para um diretório no módulo.
1. Para o hub IoT Edge e para o agente IoT Edge, adicione ligações para ligar um diretório local na máquina de anfitrião a um diretório no módulo. Por exemplo:

   ![Adicione opções de criar e variáveis ambientais para armazenamento local](./media/how-to-access-host-storage-from-module/offline-storage.png)

Ou pode configurar o armazenamento local diretamente no manifesto de implantação. Por exemplo:

```json
"systemModules": {
    "edgeAgent": {
        "settings": {
            "image": "mcr.microsoft.com/azureiotedge-agent:1.1",
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
            "image": "mcr.microsoft.com/azureiotedge-hub:1.1",
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

Substitua `<HostStoragePath>` e pelo seu hospedeiro e pelo caminho de armazenamento do `<ModuleStoragePath>` módulo; ambos os valores devem ser um caminho absoluto.

Por exemplo, num sistema Linux, `"Binds":["/etc/iotedge/storage/:/iotedge/storage/"]` o diretório **/etc/iotedge/armazenamento** no seu sistema de anfitrião está mapeado para o diretório **/iotedge/armazenamento/** no recipiente. Num sistema Windows, como outro exemplo, `"Binds":["C:\\temp:C:\\contemp"]` significa o diretório **C: a \\ temperatura** no seu sistema de anfitrião está mapeada para o diretório **C: \\ contemp** no recipiente.

Além disso, nos dispositivos Linux, certifique-se de que o perfil do utilizador do seu módulo tem as permissões de leitura, escrita e execução necessárias para o diretório do sistema de anfitrião. Voltando ao exemplo anterior de permitir que o hub IoT Edge armazene mensagens no armazenamento local do seu dispositivo, precisa de conceder permissões ao seu perfil de utilizador, O UID 1000. (O agente IoT Edge funciona como raiz, por isso não precisa de permissões adicionais.) Existem várias formas de gerir permissões de diretório nos sistemas Linux, incluindo a utilização `chown` para mudar o proprietário do diretório e, em `chmod` seguida, alterar as permissões, tais como:

```bash
sudo chown 1000 <HostStoragePath>
sudo chmod 700 <HostStoragePath>
```

Pode encontrar mais detalhes sobre a criação de opções a partir de [docs dockers](https://docs.docker.com/engine/api/v1.32/#operation/ContainerCreate).

## <a name="encrypted-data-in-module-storage"></a>Dados encriptados no armazenamento de módulos

Quando os módulos invocam a API de carga de trabalho do Daemon IoT Edge para encriptar dados, a chave de encriptação é derivada usando o ID do módulo e o ID de geração do módulo. Um ID de geração é usado para proteger segredos se um módulo for removido da implementação e, em seguida, outro módulo com o mesmo módulo ID é posteriormente implantado para o mesmo dispositivo. Pode ver o id de geração de um módulo usando o comando Azure CLI [az iot hub módulo-identidade show](/cli/azure/ext/azure-iot/iot/hub/module-identity).

Se quiser partilhar ficheiros entre módulos entre gerações, eles não devem conter nenhum segredo ou não serão desencriptados.

## <a name="next-steps"></a>Passos seguintes

Para obter um exemplo adicional de acesso ao armazenamento de hospedeiros a partir de um módulo, consulte [os dados da Loja na borda com o Azure Blob Storage no IoT Edge](how-to-store-data-blob.md).
