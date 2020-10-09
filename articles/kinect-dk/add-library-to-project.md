---
title: Adicione a biblioteca Azure Kinect ao seu projeto visual Studio
description: Saiba como adicionar o pacote Azure Kinect NuGet ao seu Visual Studio Project.
author: wes-b
ms.author: wesbarc
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: kinect, azure, sensor, sdk, estúdio visual 2017, estúdio visual 2019, nuget
ms.openlocfilehash: b0395118481cbaecd5ad0b6a3a6b3e89cc29dfaf
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "85277521"
---
# <a name="add-azure-kinect-library-to-your-visual-studio-project"></a>Adicione a biblioteca Azure Kinect ao seu projeto visual Studio

Este artigo acompanha-o através do processo de adição do pacote Azure Kinect NuGet ao seu Projeto Visual Studio.

## <a name="install-azure-kinect-nuget-package"></a>Instalar pacote Azure Kinect NuGet

Para instalar o pacote Azure Kinect NuGet:

1. Pode encontrar instruções detalhadas para instalar um pacote NuGet no Estúdio Visual no [Quickstart: Instale e utilize um pacote no Estúdio Visual](https://docs.microsoft.com/nuget/quickstart/install-and-use-a-package-in-visual-studio).
2. Para adicionar o pacote, pode utilizar o UI do Gestor de Pacotes clicando com o botão direito de referências e escolhendo Pacotes De Gestão de NuGet do Solution Explorer.
3. Escolha [nuget.org](https://www.nuget.org) como fonte do Pacote, selecione para procurar o separador Browse e procure `Microsoft.Azure.Kinect.Sensor` .
4. Selecione o pacote da lista e instale.

## <a name="use-azure-kinect-nuget-package"></a>Use o pacote Azure Kinect NuGet

Uma vez adicionado o pacote, adicione o ficheiro de cabeçalho inclui o código fonte, tais como:

- `#include <k4a/k4a.h>`
- `#include <k4arecord/record.h>`
- `#include <k4arecord/playback.h>`

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
>[Agora está pronto para construir a sua primeira aplicação](build-first-app.md)
