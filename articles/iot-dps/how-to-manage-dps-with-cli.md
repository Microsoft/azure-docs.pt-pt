---
title: Gerenciar o serviço de provisionamento de dispositivos no Hub IoT usando a extensão CLI do Azure & IoT
description: Saiba como usar CLI do Azure e a extensão de IoT para gerenciar o serviço de provisionamento de dispositivos no Hub IoT
author: chrissie926
ms.author: menchi
ms.date: 01/17/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 0e276ca32d7cd02f9668b33b3729757404b13b00
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2019
ms.locfileid: "74229748"
---
# <a name="how-to-use-azure-cli-and-the-iot-extension-to-manage-the-iot-hub-device-provisioning-service"></a>Como usar CLI do Azure e a extensão de IoT para gerenciar o serviço de provisionamento de dispositivos no Hub IoT

[CLI do Azure](https://docs.microsoft.com/cli/azure?view=azure-cli-latest) é uma ferramenta de linha de comando de plataforma cruzada de software livre para gerenciar recursos do Azure, como IOT Edge. O CLI do Azure está disponível no Windows, Linux e MacOS. CLI do Azure permite que você gerencie recursos do Hub IoT do Azure, instâncias do serviço de provisionamento de dispositivos e hubs vinculados prontos para uso.

A extensão de IoT enriquece CLI do Azure com recursos como o gerenciamento de dispositivos e o recurso de IoT Edge completo.

Neste tutorial, você primeiro conclui as etapas para configurar CLI do Azure e a extensão de IoT. Em seguida, você aprende a executar comandos da CLI para executar operações básicas do serviço de provisionamento de dispositivos. 

## <a name="installation"></a>Instalação 

### <a name="step-1---install-python"></a>Passo 1 – Instalar o Python

É necessário o [Python 2.7x ou Python 3.x](https://www.python.org/downloads/).

### <a name="step-2---install-the-azure-cli"></a>Etapa 2 – instalar o CLI do Azure

Siga a [instrução de instalação](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) para configurar CLI do Azure em seu ambiente. No mínimo, a versão da CLI do Azure tem de ser 2.0.24 ou superior. Utilize `az –version` para validar. Esta versão suporta comandos de extensão az e apresenta a arquitetura de comandos Knack. Uma forma simples de instalar no Windows é transferir e instalar o [MSI](https://aka.ms/InstallAzureCliWindows).

### <a name="step-3---install-iot-extension"></a>Passo 3 – Instalar a extensão de IoT

[O ficheiro Leia-me da extensão de IoT](https://github.com/Azure/azure-iot-cli-extension) descreve várias formas de instalar a extensão. A forma mais simples consiste em executar `az extension add --name azure-cli-iot-ext`. Após a instalação, pode utilizar `az extension list` para validar as extensões atualmente instaladas ou `az extension show --name azure-cli-iot-ext` para ver detalhes sobre a extensão de IoT. Para remover a extensão, pode utilizar `az extension remove --name azure-cli-iot-ext`.


## <a name="basic-device-provisioning-service-operations"></a>Operações básicas do serviço de provisionamento de dispositivos
O exemplo mostra como fazer logon em sua conta do Azure, criar um grupo de recursos do Azure (um contêiner que mantém recursos relacionados para uma solução do Azure), criar um hub IoT, criar um serviço de provisionamento de dispositivos, listar os serviços de provisionamento de dispositivos existentes e Crie um hub IoT vinculado com comandos da CLI. 

Conclua os passos de instalação descritos anteriormente antes de começar. Se ainda não tiver uma conta do Azure, pode [criar uma conta gratuita](https://azure.microsoft.com/free/?v=17.39a) hoje mesmo. 


### <a name="1-log-in-to-the-azure-account"></a>1. faça logon na conta do Azure
  
    az login

![início de sessão][1]

### <a name="2-create-a-resource-group-iothubblogdemo-in-eastus"></a>2. criar um grupo de recursos IoTHubBlogDemo em lesteus

    az group create -l eastus -n IoTHubBlogDemo

![Criar grupo de recursos][2]


### <a name="3-create-two-device-provisioning-services"></a>3. criar dois serviços de provisionamento de dispositivos

    az iot dps create --resource-group IoTHubBlogDemo --name demodps

![Criar serviço de provisionamento de dispositivos][3]

    az iot dps create --resource-group IoTHubBlogDemo --name demodps2

### <a name="4-list-all-the-existing-device-provisioning-services-under-this-resource-group"></a>4. listar todos os serviços de provisionamento de dispositivos existentes neste grupo de recursos

    az iot dps list --resource-group IoTHubBlogDemo

![Listar serviços de provisionamento de dispositivos][4]


### <a name="5-create-an-iot-hub-blogdemohub-under-the-newly-created-resource-group"></a>5. criar um blogDemoHub do Hub IoT no grupo de recursos recém-criado

    az iot hub create --name blogDemoHub --resource-group IoTHubBlogDemo

![Criar o Hub IoT][5]

### <a name="6-link-one-existing-iot-hub-to-a-device-provisioning-service"></a>6. vincular um hub IoT existente a um serviço de provisionamento de dispositivos

    az iot dps linked-hub create --resource-group IoTHubBlogDemo --dps-name demodps --connection-string <connection string> -l westus

![Ligar o Hub][5]

<!-- Images -->
[1]: ./media/how-to-manage-dps-with-cli/login.jpg
[2]: ./media/how-to-manage-dps-with-cli/create-resource-group.jpg
[3]: ./media/how-to-manage-dps-with-cli/create-dps.jpg
[4]: ./media/how-to-manage-dps-with-cli/list-dps.jpg
[5]: ./media/how-to-manage-dps-with-cli/create-hub.jpg
[6]: ./media/how-to-manage-dps-with-cli/link-hub.jpg


## <a name="next-steps"></a>Passos seguintes
Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Inscrever o dispositivo
> * Iniciar o dispositivo
> * Verificar se o dispositivo está registado

Avance para o próximo tutorial para saber como aprovisionar vários dispositivos em hubs com balanceamento de carga. 

> [!div class="nextstepaction"]
> [Aprovisionar dispositivos em vários hubs IoT com balanceamento de carga](./tutorial-provision-multiple-hubs.md)
