---
title: Como utilizar a CLI do Azure e a extensão de IoT para gerir o serviço de aprovisionamento de dispositivos do IoT Hub | Documentos da Microsoft
description: Saiba como utilizar a CLI do Azure e a extensão de IoT para gerir o serviço de aprovisionamento de dispositivos do IoT Hub
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 59d2277bd99fac1e8357c1b0d7336ca7451bf8dc
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62122876"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Como utilizar a CLI do Azure e a extensão de IoT para gerir o serviço de aprovisionamento de dispositivos do IoT Hub

[CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma código-fonte aberto para várias plataforma ferramenta da linha de comandos para gerir recursos do Azure, como o IoT Edge. CLI do Azure está disponível no Windows, Linux e MacOS. CLI do Azure permite-lhe gerir recursos do IoT Hub do Azure e hubs ligados a caixa de instâncias do serviço de aprovisionamento de dispositivos.

A extensão de IoT otimiza a CLI do Azure com funcionalidades como a gestão de dispositivos e a capacidade total do IoT Edge.

Neste tutorial, primeiro de concluir os passos para configurar a CLI do Azure e a extensão de IoT. Em seguida, irá aprender a executar comandos da CLI para executar operações básicas do serviço aprovisionamento de dispositivos. 

## <a name="installation"></a>Instalação 

### <a name="step-1---install-python"></a>Passo 1 – Instalar o Python

É necessário o [Python 2.7x ou Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-the-azure-cli"></a>Passo 2: instalar a CLI do Azure

Siga os [instruções de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar a CLI do Azure no seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az –version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. Uma forma simples de instalar no Windows é transferir e instalar o [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Passo 3 – Instalar a extensão de IoT

[O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias formas de instalar a extensão. A forma mais simples consiste em executar `az extension add --name azure-cli-iot-ext`. Após a instalação, pode utilizar `az extension list` para validar as extensões atualmente instaladas ou `az extension show --name azure-cli-iot-ext` para ver detalhes sobre a extensão de IoT. Para remover a extensão, pode utilizar `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Operações básicas do serviço aprovisionamento de dispositivos
O exemplo mostra como iniciar sessão na sua conta do Azure, crie um grupo de recursos do Azure (um contentor que retém recursos relacionados para uma solução do Azure), criar um IoT Hub, criar um serviço de aprovisionamento de dispositivos, listar os serviços de aprovisionamento de dispositivos existentes e Crie um hub IoT ligado com comandos da CLI. 

Conclua os passos de instalação descritos anteriormente antes de começar. Se ainda não tiver uma conta do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje mesmo. 


### <a name="1-log-in-to-the-azure-account"></a>1. Iniciar sessão na conta do Azure
  
    az login

![início de sessão][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. Criar um grupo de recursos IoTHubBlogDemo em eastus

    az group create -l eastus -n IoTHubBlogDemo

![Criar grupo de recursos][2]


### <a name="3-create-two-device-provisioning-services"></a>3. Criar dois serviços de aprovisionamento de dispositivos

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Criar o serviço aprovisionamento de dispositivos][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. Listar todos os os existentes aprovisionamento de dispositivos serviços sob este grupo de recursos

    az iot dps list --resource-group IoTHubBlogDemo

![Serviços de aprovisionamento de dispositivos de lista][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. Criar um blogDemoHub do Hub IoT no grupo de recursos recém-criado

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Criar o Hub IoT][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. Ligar um IoT Hub existente a um serviço de aprovisionamento de dispositivos

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Ligar o Hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Passos Seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registado

Avance para o próximo tutorial para saber como aprovisionar vários dispositivos em hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Aprovisionar dispositivos em vários hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
