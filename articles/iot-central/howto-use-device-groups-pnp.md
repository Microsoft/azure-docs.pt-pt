---
title: Usar grupos de dispositivos em seu aplicativo de IoT Central do Azure | Microsoft Docs
description: Como um operador, como usar grupos de dispositivos em seu aplicativo IoT Central do Azure.
author: ellenfosborne
ms.author: elfarber
ms.date: 06/09/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: peterpfr
ms.openlocfilehash: 07f1df101442e8ae43b26cebc60c8452d475d0f3
ms.sourcegitcommit: b3bad696c2b776d018d9f06b6e27bffaa3c0d9c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/21/2019
ms.locfileid: "69879750"
---
# <a name="use-device-groups-in-your-azure-iot-central-application-preview-features"></a>Usar grupos de dispositivos em seu aplicativo de IoT Central do Azure (recursos de visualização)

[!INCLUDE [iot-central-pnp-original](../../includes/iot-central-pnp-original-note.md)]

Este artigo descreve como, como um operador, usar grupos de dispositivos no aplicativo IoT Central do Azure.

Um grupo de dispositivos é uma lista de dispositivos agrupados, pois correspondem a alguns critérios especificados. Os grupos de dispositivos ajudam a gerenciar, Visualizar e analisar dispositivos em escala, agrupando dispositivos em grupos lógicos menores. Por exemplo, você pode criar um grupo de dispositivos para listar todos os dispositivos de ar-condicionado em Seattle para permitir que um técnico encontre os dispositivos para os quais eles são responsáveis. Este artigo mostra como criar e configurar grupos de dispositivos.

## <a name="create-a-device-group"></a>Criar um grupo de dispositivos

Para criar um grupo de dispositivos:

1. Escolha **grupos de dispositivos** no menu de navegação à esquerda.

1. Selecione **+ novo**.

    ![Novo grupo de dispositivos](media/howto-use-device-groups-pnp/image1.png)

1. Dê ao seu grupo de dispositivos um nome que seja exclusivo em todo o aplicativo. Também pode adicionar uma descrição. Um grupo de dispositivos só pode conter dispositivos de um único modelo de dispositivo. Escolha o modelo de dispositivo a ser usado para este grupo.

1. Crie a consulta para identificar os dispositivos para o grupo de dispositivos, selecionando uma propriedade, um operador de comparação e um valor. Você pode adicionar várias consultas e dispositivos que atendem a **todos** os critérios são colocados no grupo de dispositivos. O grupo de dispositivos que você cria pode ser acessado por qualquer pessoa que tenha acesso ao aplicativo, para que qualquer pessoa possa exibir, modificar ou excluir o grupo de dispositivos.

    ![Consulta do grupo de dispositivos](media/howto-use-device-groups-pnp/image2.png)

    > [!NOTE]
    > O grupo de dispositivos é uma consulta dinâmica. Toda vez que você exibir a lista de dispositivos, pode haver diferentes dispositivos na lista. A lista depende de quais dispositivos atendem atualmente aos critérios da consulta.

1. Escolha **Guardar**.

## <a name="analytics"></a>Análise

A análise em grupos de dispositivos é a mesma guia análise principal no menu de navegação à esquerda. Você pode aprender mais sobre a análise no artigo sobre [como criar análises](howto-use-device-groups-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="next-steps"></a>Passos Seguintes

Agora que você aprendeu a usar grupos de dispositivos em seu aplicativo IoT Central do Azure, aqui está a próxima etapa sugerida:

> [!div class="nextstepaction"]
> [Como criar regras de telemetria](howto-create-telemetry-rules-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json)
