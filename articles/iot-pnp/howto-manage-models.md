---
title: Gerenciar modelos de visualização de Plug and Play de IoT no repositório | Microsoft Docs '
description: Como gerenciar modelos de capacidade de dispositivo no repositório usando o portal do Azure Certified para IoT, o CLI do Azure e o Visual Studio Code.
author: YasinMSFT
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: conceptual
ms.date: 06/12/2019
ms.author: yahajiza
ms.openlocfilehash: ec36bef9c2b65a5c88a52621bbf3a1db708c235c
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69880504"
---
# <a name="manage-models-in-the-repository"></a>Gerenciar modelos no repositório

O repositório do modelo de visualização de IoT Plug and Play armazena modelos e interfaces de funcionalidade do dispositivo. O repositório torna os modelos e as interfaces detectáveis e consumíveis pelos desenvolvedores de soluções.

Há três ferramentas que você pode usar para gerenciar o repositório:

- O portal do Azure Certified para IoT
- A CLI do Azure
- Visual Studio Code

## <a name="model-repositories"></a>Repositórios de modelo

Há dois tipos de repositório de modelo para armazenar interfaces e modelos de funcionalidade de dispositivo:

- Há um único _repositório público_ que armazena os modelos e as interfaces de funcionalidade do dispositivo para dispositivos no [Catálogo de dispositivos certificado pelo Azure para IOT](https://aka.ms/iotdevcat). Esse repositório também armazena [interfaces comuns](./concepts-common-interfaces.md) e [DCMs e interfaces publicadas por parceiros da Microsoft](./howto-onboard-portal.md). Para saber como certificar um dispositivo e adicionar seu modelo de capacidade de dispositivo ao repositório público, consulte o tutorial [certificar seu dispositivo de plug and Play de IOT](./tutorial-certification-test.md).
- Há vários _repositórios da empresa_. Um repositório da empresa é criado automaticamente para sua organização quando você se integra [ao portal do Azure Certified para IOT](./howto-onboard-portal.md). Você pode usar o repositório de sua empresa para armazenar as interfaces e os modelos de funcionalidade do dispositivo durante o desenvolvimento e o teste.

## <a name="azure-certified-for-iot-portal"></a>Portal do Azure Certified para IoT

No [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com), você pode concluir as seguintes tarefas:

- [Conclua o processo de certificação para seu dispositivo IOT](./tutorial-certification-test.md).
- Encontre modelos de capacidade de dispositivo de Plug and Play de IoT. Você pode usar esses modelos para [criar rapidamente dispositivos preparados para IOT e integrá-los a soluções](./quickstart-connect-pnp-device-solution.md).

## <a name="azure-cli"></a>CLI do Azure

O CLI do Azure fornece comandos para gerenciar modelos de capacidade de dispositivo e interfaces nos repositórios de modelo público e da empresa de IoT Plug and Play.

## <a name="visual-studio-code"></a>Visual Studio Code

Para abrir a exibição do **repositório de modelos** no Visual Studio Code.

1. Abra Visual Studio Code, use **Ctrl + Shift + P**, digite e selecione **a plug and Play de IOT: Abra o repositório**de modelos.

1. Você pode optar por **abrir o repositório de modelos públicos** ou **abrir o repositório de modelos organizacionais**. Para o repositório de modelos da empresa, você precisa inserir a cadeia de conexão do repositório de modelos. Você pode encontrar essa cadeia de conexão no [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com) na guia cadeias de **conexão** do **repositório da sua empresa**.

1. Uma nova guia abre a exibição do **repositório de modelos** .

    Use esta exibição para adicionar, baixar e excluir modelos e interfaces de funcionalidade do dispositivo. Você pode usar um filtro para localizar itens específicos na lista.

1. Para alternar entre o repositório de modelos da empresa e o repositório de modelos públicos, use **Ctrl + Shift + P**, **digite e selecione plug and Play de IOT: Repositório**de modelos de saída. Em seguida, **use o plug and Play IOT: Abra o comando** de repositório de modelos novamente.

> [!NOTE]
> No VS Code, o repositório de modelo público é somente leitura. Os parceiros da Microsoft podem atualizar o repositório público no [portal do Azure Certified para IOT](https://preview.catalog.azureiotsolutions.com).

## <a name="next-steps"></a>Passos Seguintes

A próxima etapa sugerida é aprender a [enviar um dispositivo de plug and Play de IOT para certificação](tutorial-certification-test.md).
