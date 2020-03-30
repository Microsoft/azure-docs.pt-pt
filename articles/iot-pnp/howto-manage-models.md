---
title: Gerencie os modelos IoT Plug e Play Preview no repositório. Microsoft Docs'
description: Como gerir os modelos de capacidade do dispositivo no repositório utilizando o Portal Azure Certified para ioT, o Azure CLI e o código Visual Studio.
author: Philmea
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: how-to
ms.date: 12/26/2019
ms.author: philmea
ms.openlocfilehash: 78406175090521af70381f61f4e33dfb6c35ac8c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80159154"
---
# <a name="manage-models-in-the-repository"></a>Gerir modelos no repositório

O modelo ioT Plug e Play Preview repositório de reprodução armazena modelos e interfaces de capacidade de dispositivo. O repositório torna os modelos e interfaces detetáveis e consumíveis por desenvolvedores de soluções.

Existem três ferramentas que pode usar para gerir o repositório:

- O Portal Azure Certified for IoT
- A CLI do Azure
- Visual Studio Code

## <a name="model-repositories"></a>Repositórios-modelo

Existem dois tipos de repositório de modelos para armazenar modelos e interfaces de capacidade do dispositivo:

- Existe um único _repositório público_ que armazena os modelos e interfaces de capacidade do dispositivo para dispositivos no [catálogo de dispositivos Azure Certified for IoT](https://aka.ms/iotdevcat). Este repositório também armazena [interfaces comuns](./concepts-common-interfaces.md) e [DCMs e interfaces publicadas pela Microsoft Partners.](./howto-onboard-portal.md) Para aprender a certificar um dispositivo e adicionar o seu modelo de capacidade do dispositivo ao repositório público, consulte o tutorial [Certify your IoT Plug and Play device](./tutorial-certification-test.md).
- Existem vários _repositórios da Empresa._ Um repositório da empresa é automaticamente criado para a sua organização quando você [está a bordo do portal Azure Certified for IoT](./howto-onboard-portal.md). Pode utilizar o repositório da sua empresa para armazenar os modelos e interfaces da sua capacidade de dispositivo durante o desenvolvimento e teste.

## <a name="azure-certified-for-iot-portal"></a>Azure Certified para portal IoT

No [portal Azure Certified for IoT,](https://preview.catalog.azureiotsolutions.com)pode completar as seguintes tarefas:

- [Complete o processo de certificação para o seu dispositivo IoT](./tutorial-certification-test.md).
- Encontre os modelos de capacidade do dispositivo IoT Plug e Play. Pode utilizar estes modelos para [construir rapidamente dispositivos prontos ioT e integrá-los com soluções.](./quickstart-connect-pnp-device-solution-node.md)

## <a name="azure-cli"></a>CLI do Azure

O Azure CLI fornece comandos para gerir modelos e interfaces de capacidade de dispositivo nos repositórios públicos e modelos ioT Plug e Play e modelo selecionais. Para mais informações, consulte a [instalação e utilize a extensão Azure IoT para guia Azure CLI.](./howto-install-pnp-cli.md)

## <a name="visual-studio-code"></a>Visual Studio Code

Para abrir a visão do **Modelo repositório** no Código do Estúdio Visual.

1. Código de estúdio visual aberto, utilize **Ctrl+Shift+P,** escreva e selecione **IoT Plug and Play: Open Model Repositório**.

1. Pode optar por abrir o **Repositório de Modelos Públicos** ou **o Repositório de Modelos Organizacionais Abertos.** Para o repositório modelo da empresa, você precisa introduzir a sua cadeia de ligação repositório modelo. Pode encontrar esta cadeia de ligação no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com) no separador de **cordas De ligação** para o seu **repositório da Empresa**.

1. Um novo separador abre a vista do **Repositório Modelo.**

    Utilize esta vista para adicionar, descarregar e eliminar modelos e interfaces de capacidade do dispositivo. Pode utilizar um filtro para encontrar itens específicos na lista.

1. Para alternar entre o repositório do modelo da empresa e o repositório de modelopúblico, utilize **Ctrl+Shift+P,** escreva e selecione **IoT Plug and Play: Assine o Repositório modelo**. Em seguida, utilize o **IoT Plug and Play: Open Model Repository** Command again.

> [!NOTE]
> No Código VS, o repositório de modelos públicos é apenas para leitura. A Microsoft Partners pode atualizar o repositório público no [portal Azure Certified for IoT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Passos seguintes

O próximo passo sugerido é aprender a [submeter um dispositivo IoT Plug and Play para certificação](tutorial-certification-test.md).
