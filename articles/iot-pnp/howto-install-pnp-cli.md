---
title: Utilize a extensão Azure IoT para o Azure CLI interagir com dispositivos IoT Plug e Play Preview / Microsoft Docs
description: Instale a extensão Azure IoT para Azure CLI e utilize-a para interagir com os dispositivos IoT Plug and Play ligados ao meu hub IoT.
author: Philmea
ms.author: philmea
ms.date: 12/26/2019
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.custom: mvc
ms.openlocfilehash: 1ccb32996cd8f15805a810dd5b5985aeb5f87c26
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "81770448"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instale e utilize a extensão Azure IoT para o Azure CLI

[O Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como o IoT Hub. O Azure CLI está disponível em Windows, Linux e MacOS. O Azure CLI também está pré-instalado na [Azure Cloud Shell](https://shell.azure.com). O Azure CLI permite-lhe gerir os recursos do Azure IoT Hub, instâncias do Serviço de Provisionamento de Dispositivos e centros ligados sem instalar quaisquer extensões.

A extensão Azure IoT para o Azure CLI é uma ferramenta de linha de comando para interagir e testar dispositivos IoT Plug e Reprodução. Pode utilizar a extensão para:

- Ligue-se a um dispositivo.
- Veja a telemetria que o dispositivo envia.
- Trabalhe com as propriedades do dispositivo.
- Os comandos do dispositivo de chamada.

Este artigo mostra-lhe como:

- Instale e configuure a extensão Azure IoT para o Azure CLI.
- Utilize a extensão para interagir e testar os seus dispositivos.
- Utilize a extensão para gerir interfaces no repositório de modelos.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instale a extensão Azure IoT para o Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Passo 1 - Instalar o Azure CLI

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar o Azure CLI no seu ambiente. Para utilizar todos os comandos abaixo, a sua versão Azure CLI deve ser a versão 2.0.73 ou superior. Utilize `az -–version` para validar.

### <a name="step-2---install-iot-extension"></a>Passo 2 - Instalar extensão IoT

[O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias formas de instalar a extensão. A forma mais simples consiste em executar `az extension add --name azure-iot`. Após a instalação, pode utilizar `az extension list` para validar as extensões atualmente instaladas ou `az extension show --name azure-iot` para ver detalhes sobre a extensão de IoT. Para remover a extensão, pode utilizar `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Use a extensão Azure IoT para o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos

Para iniciar súm na sua subscrição Azure, execute o seguinte comando:

```azurecli
az login
```

> [!NOTE]
> Se estiver a usar a casca de nuvem Azure, está automaticamente inscrito e não precisa de executar o comando anterior.

Para utilizar a extensão Azure IoT para o CLI Azure, você precisa:

- Um centro de IoT Azure. Existem muitas formas de adicionar um hub IoT à sua subscrição Azure, como [criar um hub IoT usando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de ligação do hub IoT para executar os comandos de extensão Azure IoT. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um dispositivo registado no seu centro de IoT. Pode utilizar o seguinte comando Azure CLI para registar um dispositivo, certifique-se de substituir os `{YourIoTHubName}` espaços reservados e `{YourDeviceID}` os seus valores:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

- Alguns comandos precisam da cadeia de ligação para um repositório de modelos da empresa. Um repositório de modelo para a sua empresa é criado quando a [bordo do portal Azure Certified for IoT](howto-onboard-portal.md). Um terceiro poderá partilhar a sua cadeia de ligação de repositório de modelos consigo para lhe dar acesso às suas interfaces e modelos.

### <a name="interact-with-a-device"></a>Interaja com um dispositivo

Pode utilizar a extensão para visualizar e interagir com dispositivos IoT Plug e Play que estão ligados a um hub IoT. A extensão funciona com o gémeo digital que representa o dispositivo IoT Plug and Play.

#### <a name="list-devices-and-interfaces"></a>Listar dispositivos e interfaces

Listar todos os dispositivos num Hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

Listar todas as interfaces registadas por um dispositivo IoT Plug and Play:

```azurecli
az iot dt list-interfaces --hub-name {YourIoTHubName} --device-id {YourDeviceID}
```

#### <a name="properties"></a>Propriedades

Listar todas as propriedades e valores de propriedade para uma interface num dispositivo:

```azurecli
az iot dt list-properties --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login "{YourCompanyModelRepoConnectionString}"
```

Descreva o valor de uma propriedade de leitura-escrita:

```azurecli
az iot dt update-property --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface-payload {JSONPayload or FilePath}
```

Um ficheiro de carga útil de exemplo para definir a propriedade do **nome** na interface do **sensor** de um dispositivo para **Contoso** parece o seguinte:

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

Listar todos os comandos para uma interface num dispositivo:

```azurecli
az iot dt list-commands --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --source private --repo-login {YourCompanyModelRepoConnectionString}
```

Sem o `--repo-login` parâmetro, este comando usa o repositório de modelo público.

Invocar um comando:

```azurecli
az iot dt invoke-command --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --cn {CommandName} --command-payload {CommandPayload or FilePath}
```

#### <a name="digital-twin-events"></a>Eventos digitais gémeos

Monitorize todos os eventos IoT Plug e Play digital twin de um dispositivo e interface específico que vão para o **grupo de** consumidores $Default de eventos:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID}
```

Monitorize todos os eventos IoT Plug e Play digital twin a partir de um dispositivo e interface específicos que vão a um grupo de consumidores específico:

```azurecli
az iot dt monitor-events --hub-name {YourIoTHubName} --device-id {YourDeviceID} --interface {YourInterfaceID} --consumer-group {YourConsumerGroup}
```

### <a name="manage-interfaces-in-a-model-repository"></a>Gerir interfaces num repositório de modelos

Os seguintes comandos utilizam o repositório de modelos IoT Plug e Play público. Para utilizar um repositório de modelos da empresa, adicione o argumento com a sua cadeia de `--login` ligação de repositório de modelo.

Liste interfaces no repositório de modelos IoT Plug e Play público:

```azurecli
az iot pnp interface list
```

Mostrar uma interface no repositório de modelos IoT Plug e Play público:

```azurecli
az iot pnp interface show --interface {YourInterfaceId}
```

Crie uma interface no repositório de modelos IoT Plug e Play:

```azurecli
az iot pnp interface create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível criar diretamente uma interface no repositório de modelos públicos.

Atualize uma interface no repositório de modelos IoT Plug e Play:

```azurecli
az iot pnp interface update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível atualizar diretamente uma interface no repositório de modelos públicos.

Publique uma interface do repositório de modelos IoT Plug e Play para o repositório de modelos públicos. Esta operação torna a interface imutável:

```azurecli
az iot pnp interface publish --interface {YourInterfaceID} --login {YourCompanyModelRepoConnectionString}
```

Apenas os parceiros da Microsoft podem publicar interfaces para o repositório de modelos públicos.

### <a name="manage-device-capability-models-in-a-model-repository"></a>Gerir os modelos de capacidade do dispositivo num repositório de modelos

Os seguintes comandos utilizam o repositório de modelos IoT Plug e Play público. Para utilizar um repositório de modelos da empresa, adicione o argumento com a sua cadeia de `--login` ligação de repositório de modelo.

Listar os modelos de capacidade do dispositivo no repositório de modelos IoT Plug e Play:

```azurecli
az iot pnp capability-model list
```

Mostrar um modelo de capacidade do dispositivo no repositório de modelos IoT Plug e Play:

```azurecli
az iot pnp capability-model show --model {YourModelID}
```

Crie um modelo de capacidade do dispositivo num repositório de modelos IoT Plug e Play:

```azurecli
az iot pnp capability-model create --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não se pode criar diretamente um modelo no repositório de modelos públicos.

Atualize um modelo de capacidade do dispositivo no repositório de modelos IoT Plug e Play:

```azurecli
az iot pnp capability-model update --definition {JSONPayload or FilePath} --login {YourCompanyModelRepoConnectionString}
```

Não é possível atualizar diretamente um modelo no repositório de modelos públicos.

Publique um modelo de capacidade do dispositivo a partir do repositório de modelos IoT Plug e Play para o repositório de modelos públicos. Esta operação torna o modelo imutável:

```azurecli
az iot pnp capability-model publish --model {YourModelID} --login {YourCompanyModelRepoConnectionString}
```

Apenas os parceiros da Microsoft podem publicar modelos para o repositório de modelos públicos.

## <a name="next-steps"></a>Próximos passos

Neste artigo de como fazer, aprendeu a instalar e utilizar a extensão Azure IoT para que o CLI Azure interaja com os seus dispositivos Plug and Play. Um próximo passo sugerido é aprender a [gerir modelos.](./howto-manage-models.md)
