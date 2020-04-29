---
title: Utilize a extensão Azure IoT para o Azure CLI interagir com dispositivos de pré-visualização IoT Plug e Play Preview [ IoT Plug e Play Preview] Microsoft Docs
description: Instale a extensão Azure IoT para o Azure CLI e utilize-a para interagir com os dispositivos IoT Plug and Play ligados ao meu hub IoT.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81770448"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instale e utilize a extensão Azure IoT para o Azure CLI

[O Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como o IoT Hub. O Azure CLI está disponível no Windows, Linux e MacOS. O Azure CLI também está pré-instalado na [Casca de Nuvem Azure.](https://shell.azure.com) O Azure CLI permite-lhe gerir os recursos do Azure IoT Hub, instâncias do Serviço de Provisionamento de Dispositivos e centros ligados sem instalar quaisquer extensões.

A extensão Azure IoT para o Azure CLI é uma ferramenta de linha de comando para interagir com dispositivos ioT Plug e Play Preview. Pode utilizar a extensão para:

- Ligue-se a um dispositivo.
- Veja a telemetria que o dispositivo envia.
- Trabalhe com propriedades do dispositivo.
- Ligue para os comandos do dispositivo.

Este artigo mostra-lhe como:

- Instale e configure a extensão Azure IoT para o Azure CLI.
- Utilize a extensão para interagir e testar os seus dispositivos.
- Utilize a extensão para gerir as interfaces no repositório do modelo.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instale extensão Azure IoT para o Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Passo 1 - Instale o Azure CLI

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar o Azure CLI no seu ambiente. Para utilizar todos os comandos abaixo, a sua versão Azure CLI deve ser a versão 2.0.73 ou superior. Utilize `az -–version` para validar.

### <a name="step-2---install-iot-extension"></a>Passo 2 - Instalação de extensão IoT

[O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias formas de instalar a extensão. A forma mais simples consiste em executar `az extension add --name azure-iot`. Após a instalação, pode utilizar `az extension list` para validar as extensões atualmente instaladas ou `az extension show --name azure-iot` para ver detalhes sobre a extensão de IoT. Para remover a extensão, pode utilizar `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Utilize extensão Azure IoT para o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos

Para iniciar sessão na sua subscrição Azure, execute o seguinte comando:

```azurecli
az login
```

> [!NOTE]
> Se estiver a usar a casca de nuvem Azure, está automaticamente inscrito e não precisa de executar o comando anterior.

Para utilizar a extensão Azure IoT para o Azure CLI, precisa de:

- Um centro Azure IoT. Existem muitas formas de adicionar um hub IoT à sua subscrição Azure, como [criar um hub IoT usando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Precisa da cadeia de ligação do centro IoT para executar os comandos de extensão Azure IoT. Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um dispositivo registado no seu centro de IoT. Pode utilizar o seguinte comando Azure CLI para registar `{YourIoTHubName}` um `{YourDeviceID}` dispositivo, certifique-se de que substitui os espaços e os espaços reservados com os seus valores:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Alguns comandos precisam da corda de ligação para um repositório modelo da empresa. Um repositório modelo para a sua empresa é criado quando você primeiro [a bordo do Portal Azure Certified para IoT](howto-onboard-portal.md). Um terceiro pode partilhar consigo a sua cadeia de conexão repositória modelo para lhe dar acesso às suas interfaces e modelos.

### <a name="interact-with-a-device"></a>Interaja com um dispositivo

Pode utilizar a extensão para visualizar e interagir com dispositivos IoT Plug e Play que estão ligados a um hub IoT. A extensão funciona com o gémeo digital que representa o dispositivo IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Listar dispositivos e interfaces

Listar todos os dispositivos num Hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Enumerar todas as interfaces registadas por um dispositivo IoT Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Propriedades

Enumerar todas as propriedades e valores de propriedade para uma interface num dispositivo:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Definir o valor de um imóvel de leitura:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Um ficheiro de carga útil exemplo para definir a propriedade do **nome** na interface do **sensor** de um dispositivo para **Contoso** parece o seguinte:

```json
{
  "sensor": {
    "properties": {
      "name": {
        "desired": {
          "value": "Contoso"
        }
      }
    }
  }
}
```

#### <a name="commands"></a>Comandos

Enumerar todos os comandos para uma interface num dispositivo:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Sem `--repo-login` o parâmetro, este comando utiliza o repositório de modelo público.

Invocar um comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventos gémeos digitais

Monitorize todos os eventos ioT Plug e Play digital twin de um dispositivo específico e interface que vai para o grupo de consumidores do centro de eventos **$Default:**

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitorize todos os eventos de gémeos digitais IoT Plug e Play a partir de um dispositivo específico e interface que vai para um grupo de consumidores específico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gerir interfaces num repositório modelo

Os seguintes comandos utilizam o repositório do modelo IoT Plug e Play. Para utilizar um repositório modelo `--login` da empresa, adicione o argumento com a sua cadeia de ligação repositório modelo.

Lista interfaces no repositório do modelo IoT Plug e Play:

```azurecli
az iot pnp interface list
```

Mostre uma interface no repositório do modelo IoT Plug e Play:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Crie uma interface no seu repositório de modelos ioT Plug e Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não se pode criar diretamente uma interface no repositório de modelos públicos.

Atualize uma interface no seu repositório de modelos ioT Plug e Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível atualizar diretamente uma interface no repositório de modelos públicos.

Publique uma interface do seu repositório de modelos IoT Plug e Play para o repositório de modelos públicos. Esta operação torna a interface imutável:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Apenas os parceiros da Microsoft podem publicar interfaces para o repositório de modelos públicos.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gerir modelos de capacidade do dispositivo num repositório de modelos

Os seguintes comandos utilizam o repositório do modelo IoT Plug e Play. Para utilizar um repositório modelo `--login` da empresa, adicione o argumento com a sua cadeia de ligação repositório modelo.

Liste os modelos de capacidade do dispositivo no repositório de modelos públicos IoT Plug e Play:

```azurecli
az iot pnp capability-model list
```

Mostre um modelo de capacidade de dispositivo no repositório de modelos públicos IoT Plug e Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Crie um modelo de capacidade de dispositivo num repositório de modelos ioT Plug e Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não se pode criar diretamente um modelo no repositório de modelos públicos.

Atualize um modelo de capacidade do dispositivo no repositório do modelo ioT Plug and Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível atualizar diretamente um modelo no repositório de modelos públicos.

Publique um modelo de capacidade de dispositivo do seu repositório de modelos IoT Plug e Play para o repositório de modelos públicos. Esta operação torna o modelo imutável:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Apenas os parceiros da Microsoft podem publicar modelos no repositório de modelos públicos.

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a instalar e utilizar a extensão Azure IoT para o Azure CLI interagir com os seus dispositivos Plug and Play. Um próximo passo sugerido é aprender a [gerir modelos.](./howto-manage-models.md)
