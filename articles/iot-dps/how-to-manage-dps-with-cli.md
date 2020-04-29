---
title: Gerir o Serviço de Provisionamento de Dispositivos IoT Hub utilizando a extensão Azure CLI & IoT
description: Saiba como utilizar o Azure CLI e a extensão IoT para gerir o Serviço de Provisionamento de Dispositivos IoT Hub (DPS)
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 03ec0b41ad910ff0d1dcdc17148e01ec94ea9fb0
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78674523"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Como utilizar o Azure CLI e a extensão IoT para gerir o Serviço de Provisionamento de Dispositivos IoT Hub

[O Azure CLI](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de código aberto para gerir recursos Azure, como o IoT Edge. O Azure CLI está disponível no Windows, Linux e MacOS. O Azure CLI permite-lhe gerir os recursos do Hub Azure IoT, as instâncias de serviço de provisionamento de dispositivos e os centros ligados fora da caixa.

A extensão IoT enriquece o Azure CLI com funcionalidades como a gestão do dispositivo e a capacidade completa do IoT Edge.

Neste tutorial, você primeiro completa os passos para configurar Azure CLI e a extensão IoT. Em seguida, aprende a executar comandos CLI para executar operações básicas do Serviço de Provisionamento de Dispositivos. 

[!INCLUDE [iot-hub-cli-version-info](../../includes/iot-hub-cli-version-info.md)]

## <a name="installation"></a>Instalação 

### <a name="install-python"></a>Instalar o Python

É necessário o [Python 2.7x ou Python 3.x](https://www.python.org/downloads/).

### <a name="install-the-azure-cli"></a>Instalar a CLI do Azure

Siga as [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar o Azure CLI no seu ambiente. No mínimo, a sua versão Azure CLI deve ser de 2.0.70 ou superior. Utilize `az –version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. Uma forma simples de instalar no Windows é transferir e instalar o [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="install-iot-extension"></a>Instalar extensão IoT

[O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias formas de instalar a extensão. A forma mais simples consiste em executar `az extension add --name azure-iot`. Após a instalação, pode utilizar `az extension list` para validar as extensões atualmente instaladas ou `az extension show --name azure-iot` para ver detalhes sobre a extensão de IoT. Para remover a extensão, pode utilizar `az extension remove --name azure-iot`.


## <a name="basic-device-provisioning-service-operations"></a>Operações básicas de serviço de provisionamento de dispositivos

O exemplo mostra-lhe como fazer login na sua conta Azure, criar um Grupo de Recursos Azure (um recipiente que detém recursos relacionados para uma solução Azure), criar um Hub IoT, criar um serviço de fornecimento de dispositivos, listar os serviços de provisionamento de dispositivos existentes e criar um hub IoT ligado com comandos CLI. 

Conclua os passos de instalação descritos anteriormente antes de começar. Se ainda não tiver uma conta do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje mesmo. 


### <a name="1-log-in-to-the-azure-account"></a>1. Iniciar sessão na conta Azure
  
    az login

![início de sessão](./media/how-to-manage-dps-with-cli/login.jpg)

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Criar um grupo de recursos IoTHubBlogDemo no leste

    az group create -l eastus -n IoTHubBlogDemo

![Criar grupo de recursos](./media/how-to-manage-dps-with-cli/create-resource-group.jpg)


### <a name="3-create-two-device-provisioning-services"></a>3. Criar dois serviços de fornecimento de dispositivos

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Criar serviço de provisionamento de dispositivos](./media/how-to-manage-dps-with-cli/create-dps.jpg)

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Enumerar todos os serviços existentes de fornecimento de dispositivos ao abrigo deste grupo de recursos

    az iot dps list --resource-group IoTHubBlogDemo

![Serviços de Fornecimento de Dispositivos de Lista](./media/how-to-manage-dps-with-cli/list-dps.jpg)


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Criar um blog IoT HubDemoHub sob o recém-criado grupo de recursos

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Criar o Hub IoT](./media/how-to-manage-dps-with-cli/create-hub.jpg)

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Ligue um hub IoT existente a um serviço de provisionamento de dispositivos

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Ligar o Hub](./media/how-to-manage-dps-with-cli/create-hub.jpg)

## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registado

Avance para o próximo tutorial para saber como aprovisionar vários dispositivos em hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Aprovisionar dispositivos em vários hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
