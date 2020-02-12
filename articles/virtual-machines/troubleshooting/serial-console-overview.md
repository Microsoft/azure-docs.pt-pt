---
title: Consola em série Azure  Microsoft Docs
description: A Consola Em Série Azure permite-lhe ligar-se ao seu VM quando o SSH ou o RDP não estiverem disponíveis.
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
ms.date: 02/10/2020
ms.author: alsin
ms.openlocfilehash: 779bb88d15ea6c52f4399f17223b89916e22653d
ms.sourcegitcommit: 812bc3c318f513cefc5b767de8754a6da888befc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77153864"
---
# <a name="azure-serial-console"></a>Consola em série Azure

A Consola Em Série no portal Azure fornece acesso a uma consola baseada em texto para máquinas virtuais (VMs) e a instâncias de conjunto de máquinas virtuais que executam o Linux ou o Windows. Esta ligação em série liga-se à porta de série ttyS0 ou COM1 da instância de conjunto de métricas vm ou máquina virtual, proporcionando acesso independente mente da rede ou do estado do sistema operativo. A consola em série só pode ser acedida através do portal Azure e só é permitida para os utilizadores que tenham uma função de acesso de Colaborador ou superior ao conjunto de escala de máquinas VM ou virtual.

A Consola Série funciona da mesma forma para VMs e instâncias de conjunto de métricas de máquinas virtuais. Neste doc, todas as menções aos VMs incluirão implicitamente instâncias de conjunto de máquinas virtuais, salvo indicação em contrário.

> [!NOTE]
> A Consola Em Série está geralmente disponível nas regiões globais do Azure e em antevisão pública no Governo azure. Ainda não está disponível na nuvem azure china.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Pré-requisitos para aceder à Consola Em Série Azure
Para aceder à Consola Em Série na sua instância de conjunto de métricas vm ou máquina virtual, necessitará do seguinte:

- Os diagnósticos de arranque devem ser ativados para o VM
- Deve existir uma conta de utilizador que utilize a autenticação de senha dentro do VM. Pode criar um utilizador baseado em palavra-passe com a função de [palavra-passe redefinida](https://docs.microsoft.com/azure/virtual-machines/extensions/vmaccess#reset-password) da extensão de acesso VM. Selecione **Redefinir a palavra-passe** da secção Suporte + resolução de **problemas.**
- A conta Azure que acede à Consola Em Série deve ter [função de Colaborador](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) de Máquina Virtual tanto para a conta de armazenamento vm como para a conta de armazenamento de [diagnóstico de arranque](boot-diagnostics.md)

> [!NOTE]
> Implementações clássicas não são suportadas. A sua configuração de conjunto de métricas vm ou virtual deve utilizar o modelo de implementação do Gestor de Recursos Azure.

## <a name="get-started-with-the-serial-console"></a>Começar com a Consola em Série
A consola em série para VMs e conjunto de escala de máquina virtual só é acessível através do portal Azure:

### <a name="serial-console-for-virtual-machines"></a>Consola de série para máquinas virtuais
A Consola de Série para VMs é tão simples como clicar na **consola Serial** dentro da secção Suporte + resolução de **problemas** no portal Azure.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue para **todos os recursos** e selecione uma Máquina Virtual. A página geral do VM abre.

  1. Desloque-se até à secção de resolução de **problemas +** e selecione **consola série**. Um novo painel com a consola de série abre e começa a ligação.

     ![Janela da consola em série linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Consola de série para conjuntos de escala de máquina virtual
A Consola série está disponível para conjuntos de escala de máquinavirtual, acessíveis em cada instância dentro do conjunto de escala. Terá de navegar para a instância individual de uma escala de máquina virtual antes de ver o botão de **consola Serial.** Se o conjunto de escala de máquina virtual não tiver diagnósticos de arranque ativados, certifique-se de atualizar o seu modelo de conjunto de máquinas virtuais para ativar diagnósticos de arranque e, em seguida, atualizar todas as instâncias para o novo modelo para aceder à consola em série.
  1. Abra o [Portal do Azure](https://portal.azure.com).

  1. Navegue para **todos os recursos** e selecione um conjunto de escala de máquina virtual. Abre-se a página geral da escala virtual da máquina.

  1. Navegar para **Instâncias**

  1. Selecione uma instância de conjunto de escala de máquina virtual

  1. A partir da secção Suporte + resolução de **problemas,** selecione **consola série**. Um novo painel com a consola de série abre e começa a ligação.

     ![Conjunto de escala de máquina virtual Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)

## <a name="advanced-uses-for-serial-console"></a>Utilizações avançadas para consola em série
Além do acesso à consola ao seu VM, também pode utilizar a Consola Em Série Azure para o seguinte:
* Envio de um comando de pedido de [sistema para o seu VM](./serial-console-nmi-sysrq.md)
* Envio de uma [interrupção não-máscara para o seu VM](./serial-console-nmi-sysrq.md)
* [Reiniciando graciosamente ou pedalando com força o seu VM](./serial-console-power-options.md)


## <a name="next-steps"></a>Passos seguintes
A documentação adicional da Consola em Série está disponível na barra lateral.
- Mais informações estão disponíveis para [Consola em Série para VMs Linux](./serial-console-linux.md).
- Mais informações estão disponíveis para [Consola em Série para VMs do Windows.](./serial-console-windows.md)
