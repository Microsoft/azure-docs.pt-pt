---
title: Aplicações de renderização
description: É possível usar quaisquer aplicações de renderização com o Lote Azure. No entanto, as imagens VM do Azure Marketplace estão disponíveis com aplicações comuns pré-instaladas.
ms.date: 09/19/2019
ms.topic: conceptual
ms.openlocfilehash: f04fff3df2deb714f0432e5ad77d921a2d7ac205
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "82115793"
---
# <a name="pre-installed-applications-on-rendering-vm-images"></a>Aplicações pré-instaladas na renderização de imagens VM

É possível usar quaisquer aplicações de renderização com o Lote Azure. No entanto, as imagens VM do Azure Marketplace estão disponíveis com aplicações comuns pré-instaladas.

Se for caso disso, o licenciamento pay-per-use está disponível para as aplicações de renderização pré-instaladas. Quando um pool de lote é criado, as aplicações necessárias podem ser especificadas e tanto o custo de VM como as aplicações serão faturadas por minuto. Os preços da aplicação estão listados na página de preços do [Lote Azure](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Algumas aplicações suportam apenas o Windows, mas a maioria é suportada tanto no Windows como no Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplicações em Imagens de renderização do CentOS 7

A lista seguinte aplica-se ao CentOS 7.6, versão 1.1.6 imagens de renderização.

* Autodesk Maya I/O 2017 Atualização 5 (versão 201708032230)
* Autodesk Maya I/O 2018 Update 2 (corte 201711281015)
* Autodesk Maya I/O 2019 Update 1
* Autodesk Arnold para Maya 2017 (versão Arnold 5.3.1.1) MtoA-3.2.1-2017
* Autodesk Arnold para Maya 2018 (versão Arnold 5.3.1.1) MtoA-3.2.1-2018
* Autodesk Arnold para Maya 2019 (versão Arnold 5.3.1.1) MtoA-3.2.1-1-2019
* Chaos Group V-Ray for Maya 2017 (versão 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versão 3.60.04)
* Blender (2.68)
* Liquidificador (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplicações sobre imagens mais recentes do Windows Server 2016

A lista seguinte aplica-se ao Windows Server 2016, versão 1.3.8 imagens de renderização.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Update 6 (versão 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Update 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Update 2
* Autodesk Arnold para Maya 2017 (versão Arnold 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold para Maya 2018 (versão Arnold 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold para Maya 2019 (versão Arnold 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold para 3ds Max 2018 (versão Arnold 5.3.0.2)(versão 1.2.926)
* Autodesk Arnold para 3ds Max 2019 (versão Arnold 5.3.0.2)(versão 1.2.926)
* Autodesk Arnold para 3ds Max 2020 (versão Arnold 5.3.0.2)(versão 1.2.926)
* Grupo Caos V-Ray para Maya 2017 (versão 4.12.01)
* Grupo Caos V-Ray para Maya 2018 (versão 4.12.01)
* Grupo Caos V-Ray para Maya 2019 (versão 4.04.03)
* Grupo Caos V-Ray para 3ds Max 2018 (versão 4.20.01)
* Grupo Caos V-Ray para 3ds Max 2019 (versão 4.20.01)
* Grupo Caos V-Ray para 3ds Max 2020 (versão 4.20.01)
* Blender (2.79)
* Liquidificador (2.80)
* AZ 10

> [!IMPORTANT]
> Para executar o V-Ray com maya fora dos `vrayses.exe` modelos de [extensão do Lote Azure,](https://github.com/Azure/batch-extension-templates)comece antes de executar o render. Para iniciar as vrayses.exe fora dos modelos `%MAYA_2017%\vray\bin\vrayses.exe"`pode utilizar o seguinte comando .
>
> Por exemplo, consulte a tarefa inicial do [modelo Maya e V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) no GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplicações em imagens anteriores do Windows Server 2016

A lista seguinte aplica-se ao Windows Server 2016, versão 1.3.7 imagens de renderização.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Update 4 (versão 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Update 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold para Maya 2017 (versão Arnold 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold para Maya 2018 (versão Arnold 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold para 3ds Max 2018 (versão Arnold 5.0.2.4)(versão 1.2.926)
* Autodesk Arnold para 3ds Max 2019 (versão Arnold 5.0.2.4)(versão 1.2.926)
* Grupo Caos V-Ray para Maya 2018 (versão 3.52.03)
* Grupo Caos V-Ray para 3ds Max 2018 (versão 3.60.02)
* Grupo Caos V-Ray para Maya 2019 (versão 3.52.03)
* Grupo Caos V-Ray para 3ds Max 2019 (versão 4.10.01)
* Blender (2.79)

> [!NOTE]
> O Grupo Caótico V-Ray para 3ds Max 2019 (versão 4.10.01) introduz alterações de rutura no raio-V. Para utilizar a versão anterior (versão 3.60.02), utilize os nódosos de renderização do Windows Server 2016, versão 1.3.2.

## <a name="next-steps"></a>Passos seguintes

Para utilizar as imagens VM de renderização, elas precisam de ser especificadas na configuração da piscina quando uma piscina é criada; ver as capacidades da [piscina do Lote para renderização](https://docs.microsoft.com/azure/batch/batch-rendering-functionality#batch-pools).
