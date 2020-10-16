---
title: Gerir o IoT Central programáticamente Microsoft Docs
description: Este artigo descreve como criar e gerir o seu IoT Central programáticamente. Pode visualizar, modificar e remover a aplicação utilizando múltiplos SDKs de linguagem, tais como JavaScript, Python, C#, Ruby e Go.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 05/19/2020
ms.topic: how-to
ms.openlocfilehash: 773793c7681890098fea1a37cc5b9912c0ecb75c
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92122999"
---
# <a name="manage-iot-central-programmatically"></a>Gerir o IoT Central programáticamente

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Em vez de criar e gerir aplicações IoT Central no site do [gestor de aplicações Azure IoT Central,](https://aka.ms/iotcentral) pode gerir as suas aplicações programáticamente utilizando os Azure SDKs. Os idiomas suportados incluem JavaScript, Python, C#, Ruby e Go.

## <a name="install-the-sdk"></a>Instalar o SDK

A tabela que se segue lista os repositórios SDK e os comandos de instalação de pacotes:

| Repositório SDK | Instalação do pacote |
| -------------- | ------------ |
| [Azure IotCentralClient SDK para JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Microsoft Azure SDK para Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [SDK do Azure para .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Microsoft Azure SDK para Ruby - Gestão de Recursos (pré-visualização)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [SDK do Azure para Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Pacote Maven](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [SDK do Azure para Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Lançamentos de pacotes](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>Amostras

O repositório [de amostras Azure IoT Central ARM SDK](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) tem amostras de código para várias linguagens de programação que mostram como criar, atualizar, listar e eliminar aplicações Azure IoT Central.

## <a name="next-steps"></a>Passos seguintes

Agora que aprendeu a gerir programáticamente aplicações Azure IoT Central, um passo sugerido é saber mais sobre o serviço [Azure Resource Manager.](../../azure-resource-manager/management/overview.md)