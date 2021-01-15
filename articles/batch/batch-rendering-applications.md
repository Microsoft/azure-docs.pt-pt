---
title: Aplicações de renderização
description: É possível utilizar quaisquer aplicações de renderização com o Azure Batch. No entanto, as imagens VM do Azure Marketplace estão disponíveis com aplicações comuns pré-instaladas.
ms.date: 09/19/2019
ms.topic: how-to
ms.openlocfilehash: d4eefa287575d68acccb2697e1d262c7e48dab62
ms.sourcegitcommit: c7153bb48ce003a158e83a1174e1ee7e4b1a5461
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2021
ms.locfileid: "98234414"
---
# <a name="pre-installed-applications-on-batch-rendering-vm-images"></a>Aplicações pré-instaladas em imagens VM de renderização de lotes

É possível utilizar quaisquer aplicações de renderização com o Azure Batch. No entanto, as imagens VM do Azure Marketplace estão disponíveis com aplicações comuns pré-instaladas.

Se for caso disso, o licenciamento pay-per-use está disponível para os pedidos de renderização pré-instalados. Quando um pool de Lote é criado, as aplicações necessárias podem ser especificadas e tanto o custo de VM como as aplicações serão faturadas por minuto. Os preços da aplicação estão listados na página de preços do [Azure Batch](https://azure.microsoft.com/pricing/details/batch/#graphic-rendering).

Algumas aplicações apenas suportam o Windows, mas a maioria é suportada tanto no Windows como no Linux.

## <a name="applications-on-centos-7-rendering-images"></a>Aplicações em Imagens de renderização CentOS 7

A lista a seguir aplica-se ao CentOS 7.6, versão 1.1.6 imagens de renderização.

* Autodesk Maya I/O 2017 Atualização 5 (versão 201708032230)
* Autodesk Maya I/O 2018 Atualização 2 (corte 201711281015)
* Atualização 1 autodesk Maya I/O 2019
* Autodesk Arnold para Maya 2017 (Arnold versão 5.3.1.1) MtoA-3.2.1.1-2017
* Autodesk Arnold para Maya 2018 (Arnold versão 5.3.1.1) MtoA-3.2.1.1-2018
* Autodesk Arnold para Maya 2019 (Arnold versão 5.3.1.1) MtoA-3.2.1.1-2019
* Chaos Group V-Ray for Maya 2017 (versão 3.60.04)
* Chaos Group V-Ray for Maya 2018 (versão 3.60.04)
* Blender (2.68)
* Liquidificador (2.8)

## <a name="applications-on-latest-windows-server-2016-rendering-images"></a>Aplicações nas imagens mais recentes do Windows Server 2016

A lista a seguir aplica-se ao Windows Server 2016, versão 1.3.8 que renderiza imagens.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Atualização 6 (versão 18.4.0.7622)
* Autodesk Maya I/O 2019
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk 3ds Max I/O 2019 Atualização 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2020 Atualização 2
* Autodesk Arnold para Maya 2017 (Arnold versão 5.3.0.2) MtoA-3.2.0.2-2017
* Autodesk Arnold para Maya 2018 (Arnold versão 5.3.0.2) MtoA-3.2.0.2-2018
* Autodesk Arnold para Maya 2019 (Arnold versão 5.3.0.2) MtoA-3.2.0.2-2019
* Autodesk Arnold para 3ds Max 2018 (Arnold versão 5.3.0.2)(versão 1.2.926)
* Autodesk Arnold para 3ds Max 2019 (Arnold versão 5.3.0.2)(versão 1.2.926)
* Autodesk Arnold para 3ds Max 2020 (Arnold versão 5.3.0.2)(versão 1.2.926)
* Grupo Caos V-Ray para Maya 2017 (versão 4.12.01)
* Grupo Caos V-Ray para Maya 2018 (versão 4.12.01)
* Grupo Caos V-Ray para Maya 2019 (versão 4.04.03)
* Chaos Group V-Ray para 3ds Max 2018 (versão 4.20.01)
* Chaos Group V-Ray para 3ds Max 2019 (versão 4.20.01)
* Chaos Group V-Ray para 3ds Max 2020 (versão 4.20.01)
* Blender (2.79)
* Liquidificador (2.80)
* AZ 10

> [!IMPORTANT]
> Para executar O Raio-V com Maya fora dos modelos de extensão do [Azure Batch,](https://github.com/Azure/batch-extension-templates)comece `vrayses.exe` antes de executar o render. Para iniciar a vrayses.exe fora dos modelos pode utilizar o seguinte comando `%MAYA_2017%\vray\bin\vrayses.exe"` .
>
> Por exemplo, consulte a tarefa inicial do [modelo Maya e V-Ray](https://github.com/Azure/batch-extension-templates/blob/master/templates/maya/render-vray-windows/pool.template.json) no GitHub.

## <a name="applications-on-previous-windows-server-2016-rendering-images"></a>Aplicações em imagens anteriores do Windows Server 2016

A lista a seguir aplica-se ao Windows Server 2016, versão 1.3.7 que renderiza imagens.

* Autodesk Maya I/O 2017 Atualização 5 (versão 17.4.5459)
* Autodesk Maya I/O 2018 Atualização 4 (versão 18.4.0.7622)
* Autodesk 3ds Max I/O 2019 Atualização 1 (versão 21.2.0.2219)
* Autodesk 3ds Max I/O 2018 Atualização 4 (versão 20.4.0.4254)
* Autodesk Arnold para Maya 2017 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2017
* Autodesk Arnold para Maya 2018 (Arnold versão 5.2.0.1) MtoA-3.1.0.1-2018
* Autodesk Arnold para 3ds Max 2018 (Arnold versão 5.0.2.4)(versão 1.2.926)
* Autodesk Arnold para 3ds Max 2019 (Arnold versão 5.0.2.4)(versão 1.2.926)
* Grupo Caos V-Ray para Maya 2018 (versão 3.52.03)
* Chaos Group V-Ray para 3ds Max 2018 (versão 3.60.02)
* Grupo Caos V-Ray para Maya 2019 (versão 3.52.03)
* Chaos Group V-Ray para 3ds Max 2019 (versão 4.10.01)
* Blender (2.79)

> [!NOTE]
> O Grupo Caos V-Ray para 3ds Max 2019 (versão 4.10.01) introduz alterações no raio-V. Para utilizar a versão anterior (versão 3.60.02), utilize o Windows Server 2016, versão 1.3.2.

## <a name="next-steps"></a>Passos seguintes

Para utilizar as imagens VM de renderização, elas precisam de ser especificadas na configuração da piscina quando uma piscina é criada; ver as capacidades de [lote de piscina para renderização](./batch-rendering-functionality.md).
