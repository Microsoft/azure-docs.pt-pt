---
title: Composição de aplicações - Azure Batch
description: Aplicações pré-instaladas de processamento de Batch
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 03/26/2018
ms.topic: conceptual
ms.openlocfilehash: 84b2ca30f1ccd49e18e2f9d42133f8672d3f8ad6
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2019
ms.locfileid: "58496025"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Aplicações pré-instaladas nas imagens VM de composição

É possível usar qualquer aplicativos de composição com o Azure Batch. No entanto, as imagens de VM do Azure Marketplace estão disponíveis com aplicativos comuns, previamente instalados.

Quando aplicável, licenciamento pay-per-use está disponível para as aplicações de processamento pré-instaladas. Quando é criado um conjunto do Batch, podem especificar as aplicações necessárias e os dois o custo de VM e aplicações é cobrado por minuto. Os preços de aplicativo são listados os [página de preços do Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Algumas aplicações suportam apenas o Windows, mas a maioria, é suportada no Windows e Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplicações nas imagens de renderização do CentOS 7

* Autodesk Maya I/O 2017 Atualização 5 (versão 201708032230)
* Autodesk Maya e/s 2018 atualização 2 (cortar 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold versão 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold versão 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (versão 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versão 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplicações nas imagens de processamento mais recente do Windows Server 2016

A lista seguinte aplica-se para o Windows Server 2016, imagens de composição de versão 1.3.4.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya e/s 2018 atualização 4 (versão 18.4.0.7622)
* Autodesk 3ds Max e/s de 2019 atualização 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold para Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold para Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold para 3ds Max (Arnold versão 5.0.2.4)(version 1.2.926)
* Chaos grupo V-Ray para Maya (versão 3.52.03) de 2018
* Chaos grupo V-Ray para 3ds Max 2018 (versão 3.60.02)
* Chaos grupo V-Ray para Maya (versão 3.52.03) de 2019
* Chaos grupo V-Ray para 3ds Max 2019 (versão 4.10.01)
* Blender (2.79)

> [!NOTE]
> Chaos grupo V-Ray para 3ds Max 2019 (versão 4.10.01) introduz alterações significativas para V-ray. Para utilizar a versão anterior (versão 3.60.02), utilize o Windows Server 2016, nós de composição de versão 1.3.2.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplicações nas imagens de processamento anteriores do Windows Server 2016

A lista seguinte aplica-se para o Windows Server 2016, imagens de composição de versão 1.3.2.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya e/s 2018 atualização 4 (versão 18.4.0.7622)  
* Autodesk 3ds Max e/s de 2019 atualização 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold para Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold para Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold para 3ds Max (Arnold versão 5.0.2.4)(version 1.2.926)
* Chaos grupo V-Ray para Maya (versão 3.52.03) de 2019
* Chaos grupo V-Ray para 3ds Max 2018 (versão 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Passos Seguintes

Para utilizar as imagens de VM de composição, precisam de ser especificado na configuração do conjunto, quando é criado um conjunto; consulte a [capacidades do agrupamento para a composição do Batch](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).