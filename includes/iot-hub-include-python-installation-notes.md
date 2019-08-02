---
title: incluir ficheiro
description: incluir ficheiro
services: iot-hub
author: robinsh
ms.service: iot-hub
ms.topic: include
ms.date: 07/24/2019
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: 103ad020b8d9f50ffe690f502b7cac08dab9237a
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68640441"
---
* Uma conta ativa do Azure. (Se você não tiver uma conta, poderá criar uma [conta gratuita](https://azure.microsoft.com/pricing/free-trial/) em apenas alguns minutos.)

* **Windows**

    * [Python 2. x ou 3. x](https://www.python.org/downloads/). Certifique-se de que utiliza a instalação de 32 ou 64 bits, conforme exigido pela sua configuração. Quando lhe for pedido durante a instalação, confirme que adiciona Python à variável de ambiente específica da sua plataforma. Se estiver a utilizar Python 2.x, poderá ter de [instalar ou atualizar o *pip*, o sistema de gestão de pacotes de Python](https://pip.pypa.io/en/stable/installing/).

    * Se você estiver usando o sistema operacional Windows, verifique se você tem a versão correta [do C++ pacote redistribuível Visual](https://support.microsoft.com/en-us/help/2977003/the-latest-supported-visual-c-downloads) para permitir o uso de DLLs nativas do Python. Recomendamos que você use a versão mais recente.

    * Se necessário, instale o pacote [Azure-iothub-Device-Client](https://pypi.org/project/azure-iothub-device-client/) , usando o comando`pip install azure-iothub-device-client`

    * Se necessário, instale o pacote [Azure-iothub-Service-Client](https://pypi.org/project/azure-iothub-service-client/) usando o comando`pip install azure-iothub-service-client`

* **Mac OS**

    Por Mac OS, você precisa do Python 3.7.0 (ou 2,7) + libboost-1,67 + ondulação 7.61.1 (todas instaladas via homebrew). Qualquer outra distribuição/sistema operacional provavelmente incorporará versões diferentes do Boost & dependências que não funcionarão e resultará em um ImportError no tempo de execução.

    Os pacotes *pip* para `azure-iothub-service-client` e `azure-iothub-device-client` estão atualmente disponíveis apenas para o SO Windows. Para o Linux/Mac OS, consulte as seções específicas do Linux e do Mac OS na postagem [preparar seu ambiente de desenvolvimento para o Python](https://github.com/Azure/azure-iot-sdk-python/blob/master/doc/python-devbox-setup.md) .

> [!NOTE]
> Houve vários relatórios de erros ao importar iothub_client em um exemplo. Para obter mais informações sobre como lidar com problemas de **ImportError** , consulte [lidando com problemas de ImportError](https://github.com/Azure/azure-iot-sdk-python#important-installation-notes---dealing-with-importerror-issues).
