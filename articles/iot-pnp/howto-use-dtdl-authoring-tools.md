---
title: Utilize uma ferramenta para autor e validar modelos DTDL | Microsoft Docs
description: Instale o editor DTDL para Visual Studio Code ou Visual Studio 2019 e use-o para os modelos IoT Plug e Play.
author: dominicbetts
ms.author: dobett
ms.date: 09/14/2020
ms.topic: how-to
ms.service: iot-pnp
services: iot-pnp
ms.openlocfilehash: 40d1ae4da07e159c24970c065d1c39e22b89a29a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "93280206"
---
# <a name="install-and-use-the-dtdl-authoring-tools"></a>Instale e utilize as ferramentas de autoria DTDL

[Os modelos Digital Twins Definition Language (DTDL)](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) são ficheiros JSON. Pode utilizar uma extensão para código Visual Studio ou Visual Studio 2019 para autor e validar estes ficheiros de modelos.

## <a name="install-and-use-the-vs-code-extension"></a>Instale e utilize a extensão do Código VS

A extensão DTDL para o Código VS adiciona as seguintes características de autoria DTDL:

- Validação de sintaxe DTDL v2.
- Intellisense, incluindo autocomplete, para ajudá-lo com a sintaxe linguística.
- A capacidade de criar interfaces a partir da paleta de comando.

Para instalar a extensão DTDL, vá ao [editor DTDL para Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl). Também pode pesquisar **por DTDL** na vista Extensões no Código VS.

Quando tiver instalado a extensão, use-a para o ajudar a autoria de ficheiros de modelos DTDL no código VS:

- A extensão fornece validação de sintaxe em ficheiros de modelo DTDL, realçando os erros como mostrado na imagem seguinte:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation.png" alt-text="Validação do modelo no Código VS":::

- Use intellisense e preenchê-lo automaticamente quando estiver a editar modelos DTDL:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense.png" alt-text="Utilize intellisense para modelos DTDL em Código VS":::

- Criar uma nova interface DTDL. O **comando DTDL: Criar Interface** cria um ficheiro JSON com uma nova interface. A interface inclui definições de telemetria, propriedade e comando.

## <a name="install-and-use-the-visual-studio-extension"></a>Instale e utilize a extensão do Estúdio Visual

A extensão DTDL para Visual Studio 2019 adiciona as seguintes funcionalidades de autoria DTDL:

- Validação de sintaxe DTDL v2.
- Intellisense, incluindo autocomplete, para ajudá-lo com a sintaxe linguística.

Para instalar a extensão DTDL, aceda ao [Suporte Linguístico DTDL para VS 2019](https://marketplace.visualstudio.com/items?itemName=vsc-iot.vs16dtdllanguagesupport). Também pode pesquisar **por DTDL** em **Extensões de Gestão** em Estúdio Visual.

Quando tiver instalado a extensão, use-a para o ajudar a autorize ficheiros de modelos DTDL no Estúdio Visual:

- A extensão fornece validação de sintaxe em ficheiros de modelo DTDL, realçando os erros como mostrado na imagem seguinte:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-validation-2.png" alt-text="Validação de modelos no Estúdio Visual":::

- Use intellisense e preenchê-lo automaticamente quando estiver a editar modelos DTDL:

    :::image type="content" source="media/howto-use-dtdl-authoring-tools/model-intellisense-2.png" alt-text="Use intellisense para modelos DTDL em Estúdio Visual":::

## <a name="next-steps"></a>Passos seguintes

Neste artigo de como fazer, aprendeu a usar as extensões DTDL para Visual Studio Code e Visual Studio 2019 para autor e validar ficheiros de modelos DTDL. Um próximo passo sugerido é aprender a usar o [explorador Azure IoT com os seus modelos e dispositivos.](./howto-use-iot-explorer.md)
