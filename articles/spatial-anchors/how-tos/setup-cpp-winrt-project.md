---
title: Instale âncoras espaciais Azure para uma aplicação C++/WinRT HoloLens
description: Configure um projeto C++/WinRT HoloLens para usar âncoras espaciais Azure
author: msftradford
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: parkerra
ms.date: 11/20/2020
ms.topic: how-to
ms.service: azure-spatial-anchors
ms.openlocfilehash: 69134ef87d90fe69de2d9e4e9222e90f65edc785
ms.sourcegitcommit: b8eba4e733ace4eb6d33cc2c59456f550218b234
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/23/2020
ms.locfileid: "95507012"
---
# <a name="configuring-azure-spatial-anchors-in-a-cwinrt-hololens-project"></a>Configurar âncoras espaciais Azure num projeto C++/WinRT HoloLens

## <a name="requirements"></a>Requisitos

* [Visual Studio 2019](https://www.visualstudio.com/downloads/) instalado com a carga de **trabalho de desenvolvimento** da Plataforma Universal Windows e o componente Windows **10 SDK (10.0.18362.0 ou mais recente).**

## <a name="configuring-a-project"></a>Configurar um projeto

As âncoras espaciais Azure para HoloLens e C++/WinRT são distribuídas através do pacote [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet.

Siga as instruções [aqui](/nuget/consume-packages/install-use-packages-visual-studio) para utilizar o NuGet Package Manager do Visual Studio para instalar o pacote [Microsoft.Azure.SpatialAnchors.WinRT](https://www.nuget.org/packages/Microsoft.Azure.SpatialAnchors.WinRT/) NuGet no seu projeto.

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Como: Criar e localizar âncoras em C++/WinRT](./create-locate-anchors-cpp-winrt.md)