---
title: Console serial do Azure | Microsoft Docs
description: O console serial do Azure permite que você se conecte à sua VM quando SSH ou RDP não estiverem disponíveis.
services: virtual-machines
documentationcenter: ''
author: asinn826
manager: borisb
editor: ''
tags: azure-resource-manager
ms.service: virtual-machines
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm
ms.workload: infrastructure-services
ms.date: 8/30/2019
ms.author: alsin
ms.openlocfilehash: 20bc22661f9faad1b289dbbe7200f4f83c097f0e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75451233"
---
# <a name="azure-serial-console"></a>Consola de Série do Azure

O console serial no portal do Azure fornece acesso a um console baseado em texto para VMs (máquinas virtuais) e instâncias do conjunto de dimensionamento de máquinas virtuais que executam o Linux ou o Windows. Esta ligação série estabelece ligação com a porta série ttyS0 ou COM1 da VM ou da instância de conjunto de dimensionamento de máquinas virtuais, o que permite aceder independentemente do estado do sistema operativo ou da rede. O console serial só pode ser acessado usando o portal do Azure e é permitido somente para os usuários que têm uma função de acesso de colaborador ou superior ao conjunto de dimensionamento de máquinas virtuais ou VM.

O console serial funciona da mesma maneira para VMs e instâncias do conjunto de dimensionamento de máquinas virtuais. Neste documento, todas as menção a VMs irão incluir implicitamente as instâncias do conjunto de dimensionamento de máquinas virtuais, salvo indicação em contrário.

> [!NOTE]
> O console serial está geralmente disponível em regiões globais do Azure e em visualização pública no Azure governamental. Ele ainda não está disponível na nuvem do Azure na China.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Pré-requisitos para acessar o console serial do Azure
Para acessar o console serial em sua VM ou instância do conjunto de dimensionamento de máquinas virtuais, você precisará do seguinte:

- O diagnóstico de inicialização deve ser habilitado para a VM
- Uma conta de usuário que usa autenticação de senha deve existir na VM. Você pode criar um usuário baseado em senha com a função [Redefinir senha](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso à VM. Selecione **Repor palavra-passe** partir do **suporte + resolução de problemas** secção.
- A conta do Azure que acessa o console serial deve ter a [função de colaborador de máquina virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) para a VM e a conta de armazenamento de [diagnóstico de inicialização](boot-diagnostics.md)

> [!NOTE]
> - Implementações clássicas não são suportadas. A instância do conjunto de dimensionamento de máquinas virtuais ou VM deve usar o modelo de implantação Azure Resource Manager.

## <a name="get-started-with-the-serial-console"></a>Introdução ao console serial
O console serial para VMs e conjunto de dimensionamento de máquinas virtuais é acessível somente por meio do portal do Azure:

### <a name="serial-console-for-virtual-machines"></a>Console serial para máquinas virtuais
O console serial para VMs é tão simples quanto clicar em **console serial** na seção **suporte + solução de problemas** no portal do Azure.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue até **todos os recursos** e selecione uma máquina virtual. A página Visão geral da VM é aberta.

  1. Desloque para baixo para o **suporte + resolução de problemas** secção e selecione **consola de série**. Um novo painel com a consola de série abre e começa a ligação.

     ![Janela do console serial do Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Console serial para conjuntos de dimensionamento de máquinas virtuais
O console serial está disponível para conjuntos de dimensionamento de máquinas virtuais, acessível em cada instância dentro do conjunto de dimensionamento. Você precisará navegar até a instância individual de um conjunto de dimensionamento de máquinas virtuais antes de ver o botão de **console serial** . Se o conjunto de dimensionamento de máquinas virtuais não tiver o diagnóstico de inicialização habilitado, atualize o modelo do conjunto de dimensionamento de máquinas virtuais para habilitar o diagnóstico de inicialização e, em seguida, atualize todas as instâncias para o novo modelo a fim de acessar o console serial.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue até **todos os recursos** e selecione um conjunto de dimensionamento de máquinas virtuais. A página Visão geral do conjunto de dimensionamento de máquinas virtuais é aberta.

  1. Navegar até **instâncias**

  1. Selecionar uma instância do conjunto de dimensionamento de máquinas virtuais

  1. Na seção **suporte + solução de problemas** , selecione **console serial**. Um novo painel com a consola de série abre e começa a ligação.

     ![Console serial do conjunto de dimensionamento de máquinas virtuais do Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Usos avançados para o console serial
Além do acesso do console à sua VM, você também pode usar o console serial do Azure para o seguinte:
* Enviando um [comando de solicitação do sistema para sua VM](./serial-console-nmi-sysrq.md)
* Enviando uma [interrupção não mascarável para sua VM](./serial-console-nmi-sysrq.md)
* [Reinicializando ou forçando forçosamente a energia de sua VM](./serial-console-power-options.md)


## <a name="next-steps"></a>Passos seguintes
A documentação adicional do console serial está disponível na barra lateral.
- Mais informações estão disponíveis para o [console serial para VMs do Linux](./serial-console-linux.md).
- Mais informações estão disponíveis para o [console serial para VMs do Windows](./serial-console-windows.md).
