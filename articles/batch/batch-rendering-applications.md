---
title: Aplicativos de renderização – lote do Azure
description: Aplicativos de renderização em lotes pré-instalados
services: batch
ms.service: batch
author: laurenhughes
ms.author: lahugh
ms.date: 07/29/2019
ms.topic: conceptual
ms.openlocfilehash: 133742bf45bddf866d2dfcae97a331ee2a0f84fc
ms.sourcegitcommit: 3877b77e7daae26a5b367a5097b19934eb136350
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2019
ms.locfileid: "68639372"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Aplicativos pré-instalados ao renderizar imagens de VM

É possível usar qualquer aplicativo de renderização com o lote do Azure. No entanto, as imagens de VM do Azure Marketplace estão disponíveis com aplicativos comuns pré-instalados.

Quando aplicável, o licenciamento de pagamento por uso está disponível para os aplicativos de renderização pré-instalados. Quando um pool do lote é criado, os aplicativos necessários podem ser especificados e o custo da VM e dos aplicativos será cobrado por minuto. Os preços de aplicativo são listados na [página de preços do lote do Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Alguns aplicativos dão suporte apenas ao Windows, mas há suporte para a maioria no Windows e no Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplicativos em imagens de renderização CentOS 7

A lista a seguir se aplica a imagens de renderização CentOS 7,6, 1.1.5 de versão.

* Autodesk Maya I/O 2017 Atualização 5 (versão 201708032230)
* Autodesk Maya de e/s 2018 atualização 2 (recortar 201711281015)
* Autodesk Arnold for Maya 2017 (Arnold versão 5.0.1.1) MtoA-2.0.1.1-2017
* Autodesk Arnold for Maya 2018 (Arnold versão 5.0.1.4) MtoA-2.1.0.3-2018
* Chaos Group V-Ray for Maya 2017 (versão 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versão 3.60.04)
* Blender (2.68)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplicativos nas imagens mais recentes de renderização do Windows Server 2016

A lista a seguir se aplica às imagens de renderização do Windows Server 2016, versão 1.3.4.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya e/s 2018 atualização 4 (versão 18.4.0.7622)
* Autodesk 3ds Max e/s 2019 atualização 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold para Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold para Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold para 3ds Max (Arnold Version 5.0.2.4) (versão 1.2.926)
* Grupo de caos V-Ray para Maya 2018 (versão 3.52.03)
* Grupo de caos V-Ray para 3ds Max 2018 (versão 3.60.02)
* Grupo de caos V-Ray para Maya 2019 (versão 3.52.03)
* Grupo de caos V-Ray para 3ds Max 2019 (versão 4.10.01)
* Blender (2.79)

> [!NOTE]
> O grupo de caos V-Ray para 3ds Max 2019 (versão 4.10.01) apresenta alterações significativas no V-Ray. Para usar a versão anterior (versão 3.60.02), use os nós de renderização do Windows Server 2016, versão 1.3.2.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplicativos nas imagens de renderização anteriores do Windows Server 2016

A lista a seguir se aplica às imagens de renderização do Windows Server 2016, versão 1.3.2.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya e/s 2018 atualização 4 (versão 18.4.0.7622)  
* Autodesk 3ds Max e/s 2019 atualização 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold para Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold para Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold para 3ds Max (Arnold Version 5.0.2.4) (versão 1.2.926)
* Grupo de caos V-Ray para Maya 2019 (versão 3.52.03)
* Grupo de caos V-Ray para 3ds Max 2018 (versão 3.60.02)
* Blender (2.79)

## <a name="next-steps"></a>Passos Seguintes

Para usar as imagens de VM de renderização, elas precisam ser especificadas na configuração do pool quando um pool é criado; consulte os [recursos do pool do lote para renderização](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
