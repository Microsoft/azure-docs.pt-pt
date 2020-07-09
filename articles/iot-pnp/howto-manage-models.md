---
title: Gerir os modelos IoT Plug e Play Preview no repositório Microsoft Docs'
description: Como gerir os modelos de capacidade do dispositivo no repositório utilizando o portal Azure Certified for IoT, o código Azure CLI e Visual Studio.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "80159154"
---
# <a name="manage-models-in-the-repository"></a>Gerir modelos no repositório

O repositório de modelo ioT Plug e Play Preview armazena modelos e interfaces de capacidade do dispositivo. O repositório torna os modelos e interfaces detetáveis e consumíveis pelos desenvolvedores de soluções.

Existem três ferramentas que pode usar para gerir o repositório:

- O Portal Azure Certificado para IoT
- A CLI do Azure
- Visual Studio Code

## <a name="model-repositories"></a>Repositórios de modelos

Existem dois tipos de repositório de modelos para armazenar modelos e interfaces de capacidade do dispositivo:

- Existe um único _repositório público_ que armazena os modelos e interfaces de capacidade do dispositivo para dispositivos no catálogo de [dispositivos Azure Certified for IoT.](https://aka.ms/iotdevcat) Este repositório também armazena [interfaces comuns](./concepts-common-interfaces.md) e [DCMs e interfaces publicadas pela Microsoft Partners.](./howto-onboard-portal.md) Para aprender a certificar um dispositivo e adicionar o seu modelo de capacidade de dispositivo ao repositório público, consulte o tutorial Certificando o [seu dispositivo IoT Plug and Play](./tutorial-certification-test.md).
- Existem _vários repositórios da Empresa._ Um repositório da empresa é automaticamente criado para a sua organização quando você está [a bordo do portal Azure Certified for IoT](./howto-onboard-portal.md). Pode utilizar o repositório da empresa para armazenar os modelos e interfaces de capacidade do seu dispositivo durante o desenvolvimento e teste.

## <a name="azure-certified-for-iot-portal"></a>Azure Certificado para portal IoT

No [portal Azure Certified for IoT,](https://preview.catalog.azureiotsolutions.com)pode completar as seguintes tarefas:

- [Complete o processo de certificação do seu dispositivo IoT.](./tutorial-certification-test.md)
- Encontre os modelos de capacidade do dispositivo IoT Plug e Play. Pode utilizar estes modelos para [construir rapidamente dispositivos prontos IoT e integrá-los com soluções.](./quickstart-connect-pnp-device-solution-node.md)

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI fornece comandos para gerir modelos e interfaces de capacidade de dispositivos nos repositórios de modelos ioT plug e Play e da empresa. Para obter mais informações, consulte a [Instalação e utilize a extensão Azure IoT para o guia Azure CLI.](./howto-install-pnp-cli.md)

## <a name="visual-studio-code"></a>Visual Studio Code

Para abrir a vista **do Repositório modelo** no Código do Estúdio Visual.

1. Código de estúdio visual aberto, use **Ctrl+Shift+P,** escreva e **selecione IoT Plug and Play: Open Model Repository**.

1. Pode optar por abrir o **Repositório de Modelos Públicos** ou **o Repositório de Modelos Abertos.** Para o repositório de modelos da empresa, precisa de introduzir a sua cadeia de ligação de repositório de modelo. Pode encontrar esta cadeia de ligação no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) no **separador de cordas De Ligação** para o **seu repositório da Empresa**.

1. Um novo separador abre a vista **do Repositório modelo.**

    Utilize esta vista para adicionar, transferir e eliminar modelos e interfaces de capacidade do dispositivo. Pode utilizar um filtro para encontrar itens específicos na lista.

1. Para alternar entre o repositório do modelo da empresa e o repositório de modelos públicos, utilize **ctrl+Shift+P,** escreva e selecione **IoT Plug and Play: Assine o Repositório do Modelo**. Em seguida, utilize o plug e play IoT: Abra novamente o comando **do Repositório de Modelos** Abertos.

> [!NOTE]
> No Código VS, o repositório de modelo público é apenas de leitura. A Microsoft Partners pode atualizar o repositório público no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é aprender a [enviar um dispositivo IoT Plug and Play para certificação](tutorial-certification-test.md).
