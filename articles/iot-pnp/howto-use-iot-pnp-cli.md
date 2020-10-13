---
title: Utilize a extensão Azure IoT para o Azure CLI interagir com dispositivos IoT Plug e Play / Microsoft Docs
description: Instale a extensão Azure IoT para Azure CLI e utilize-a para interagir com os dispositivos IoT Plug and Play ligados ao meu hub IoT.
author: dominicbetts
ms.author: dobett
ms.date: 07/20/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 680cd4ef4f73c63850a2137b344fd0af6b27c673
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91577463"
---
# <a name="install-and-use-the-azure-iot-extension-for-the-azure-cli"></a>Instale e utilize a extensão Azure IoT para o Azure CLI

[O Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest&preserve-view=true) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como o IoT Hub. O Azure CLI está disponível em Windows, Linux e macOS. O Azure CLI permite-lhe gerir os recursos do Azure IoT Hub, instâncias do Serviço de Provisionamento de Dispositivos e centros ligados sem instalar quaisquer extensões.

A extensão Azure IoT para o Azure CLI é uma ferramenta de linha de comando para interagir e testar dispositivos IoT Plug e Play. Pode utilizar a extensão para:

- Ligue-se a um dispositivo.
- Veja a telemetria que o dispositivo envia.
- Trabalhe com as propriedades do dispositivo.
- Os comandos do dispositivo de chamada.

Este artigo mostra-lhe como:

- Instale e configuure a extensão Azure IoT para o Azure CLI.
- Utilize a extensão para interagir e testar os seus dispositivos.

## <a name="install-azure-iot-extension-for-the-azure-cli"></a>Instale a extensão Azure IoT para o Azure CLI

### <a name="step-1---install-the-azure-cli"></a>Passo 1 - Instalar o Azure CLI

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest&preserve-view=true) para configurar o Azure CLI no seu ambiente. Para uma melhor experiência, a sua versão Azure CLI deve ser a versão 2.9.1 ou superior. Utilize `az -–version` para validar.

### <a name="step-2---install-iot-extension"></a>Passo 2 - Instalar extensão IoT

[O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias formas de instalar a extensão. A forma mais simples consiste em executar `az extension add --name azure-iot`. Após a instalação, pode utilizar `az extension list` para validar as extensões atualmente instaladas ou `az extension show --name azure-iot` para ver detalhes sobre a extensão de IoT. No momento da escrita, o número da versão de extensão é `0.10.0` .

Para remover a extensão, pode utilizar `az extension remove --name azure-iot`.

## <a name="use-azure-iot-extension-for-the-azure-cli"></a>Use a extensão Azure IoT para o Azure CLI

### <a name="prerequisites"></a>Pré-requisitos

Para iniciar súm na sua subscrição Azure, execute o seguinte comando:

```azurecli
az login
```

Para utilizar a extensão Azure IoT para o CLI Azure, você precisa:

- Um centro de IoT Azure. Existem muitas formas de adicionar um hub IoT à sua subscrição Azure, como [criar um hub IoT usando o Azure CLI](../iot-hub/iot-hub-create-using-cli.md). Você precisa da cadeia de ligação do hub IoT para executar os comandos de extensão Azure IoT. Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

- Um dispositivo registado no seu centro de IoT. Pode utilizar o seguinte comando Azure CLI para registar um dispositivo, certifique-se de substituir os `{YourIoTHubName}` espaços reservados e `{YourDeviceID}` os seus valores:

    ```azurecli
    az iot hub device-identity create --hub-name {YourIoTHubName} --device-id {YourDeviceID}
    ```

### <a name="interact-with-a-device"></a>Interaja com um dispositivo

Pode utilizar a extensão para visualizar e interagir com dispositivos IoT Plug e Play que estão ligados a um hub IoT. A extensão funciona com o gémeo digital que representa o dispositivo IoT Plug and Play.

#### <a name="list-devices"></a>Listar dispositivos

Listar todos os dispositivos num Hub IoT:

```azurecli
az iot hub device-identity list --hub-name {YourIoTHubName}
```

#### <a name="view-digital-twin"></a>Ver twin digital

Veja o gémeo digital de um dispositivo:

```azurecli
az iot pnp twin show -n {iothub_name} -d {device_id}
```

#### <a name="properties"></a>Propriedades

Descreva o valor de uma propriedade de leitura-escrita:

```azurecli
az iot pnp twin update --hub-name {iothub_name} --device-id {device_id} --json-patch '{"op":"add", "path":"/thermostat1/targetTemperature", "value": 54}'
```

#### <a name="commands"></a>Comandos

Invocar um comando:

```azurecli
az iot pnp twin invoke-command --cn getMaxMinReport -n {iothub_name} -d {device_id} --component-path thermostat1
```

#### <a name="digital-twin-events"></a>Eventos digitais gémeos

Monitorize todos os eventos IoT Plug e Play digital twin de um dispositivo e interface específico que vão para o **grupo de** consumidores $Default de eventos:

```azurecli
az iot hub monitor-events -n {iothub_name} -d {device_id} -i {interface_id}
```

### <a name="manage-models-in-the-model-repository"></a>Gerir modelos no repositório de modelos

Pode utilizar os comandos de repositório do modelo Azure CLI para gerir os modelos no repositório.

#### <a name="create-model-repository"></a>Criar repositório de modelos

Crie um novo repositório de empresa IoT Plug and Play para o seu inquilino se for o primeiro utilizador no seu inquilino:

```azurecli
az iot pnp repo create
```

#### <a name="manage-model-repository-tenant-roles"></a>Gerir funções de inquilino de repositório de modelos

Crie uma atribuição de funções para um utilizador ou principal de serviço a um recurso específico.

Por exemplo, dar user@consoso.com o papel de **ModelCreator** para o inquilino:

```azurecli
az iot pnp role-assignment create --resource-id {tenant_id} --resource-type Tenant --subject-id {user@contoso.com} --subject-type User --role ModelsCreator
```

Ou dar user@consoso.com o papel de **ModeloAdministrador** para um modelo específico:

```azurecli
az iot pnp role-assignment create --resource-id {model_id} --resource-type Model --subject-id {user@contoso.com} --subject-type User --role ModelAdministrator
```

#### <a name="create-a-model"></a>Criar um modelo

Criar um novo modelo no repositório da empresa:

```azurecli
az iot pnp model create --model {model_json or path_to_file}
```

#### <a name="search-a-model"></a>Pesquisar um modelo

Modelos de lista que correspondem a uma palavra-chave específica:

```azurecli
az iot pnp model list -q {search_keyword}
```

#### <a name="publish-a-model"></a>Publicar um modelo

Publique um modelo de dispositivo localizado no repositório da empresa para o repositório público.

Por exemplo, tornar público o modelo com `dtmi:com:example:ClimateSensor;1` ID:

```azurecli
az iot pnp model publish --dtmi "dtmi:com:example:ClimateSensor;1"
```

Para publicar um modelo, os seguintes requisitos têm de ser cumpridos:

- A empresa ou inquilino da organização deve ser um Parceiro Microsoft. 
- O utilizador ou o principal de serviço devem ser membros da função **de Editor** do repositório.

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a instalar e utilizar a extensão Azure IoT para que o CLI Azure interaja com os seus dispositivos IoT Plug e Play. Um próximo passo sugerido é aprender a usar o [explorador Azure IoT com os seus dispositivos](./howto-use-iot-explorer.md).
