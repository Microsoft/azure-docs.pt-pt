---
title: Consola em série Azure Microsoft Docs
description: A Consola em Série Azure permite-lhe ligar-se ao seu VM quando o SSH ou o RDP não estiverem disponíveis.
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
ms.openlocfilehash: 28c5a3085d84b25deb7c5ee09a9c9cc4d7a06819
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "87374070"
---
# <a name="azure-serial-console"></a>Consola em série Azure

A Consola em Série no portal Azure fornece acesso a uma consola baseada em texto para máquinas virtuais (VMs) e instâncias de escala de máquinas virtuais que executam o Linux ou o Windows. Esta ligação em série liga-se à porta de série ttyS0 ou COM1 da placa de conjunto de escala vm ou de máquina virtual, proporcionando acesso independente do estado da rede ou do sistema operativo. A consola em série só pode ser acedida através do portal Azure e só é permitida para os utilizadores que tenham uma função de acesso de Colaborador ou superior ao conjunto de escala de VM ou de máquina virtual.

A Consola em Série funciona da mesma forma para VMs e instâncias de escala de máquinas virtuais. Neste doc, todas as menções aos VMs incluirão implicitamente instâncias de conjunto de escala de máquina virtual, salvo indicação em contrário.

A Consola Em Série está geralmente disponível nas regiões globais do Azure e em pré-visualização pública no Governo de Azure. Ainda não está disponível na nuvem Azure China.

## <a name="prerequisites-to-access-the-azure-serial-console"></a>Pré-requisitos para aceder à Consola em Série Azure
Para aceder à Consola em Série na sua instância de definição de escala de VM ou de máquina virtual, necessitará do seguinte:

- Os diagnósticos de arranque devem ser ativados para o VM
- Uma conta de utilizador que utilize a autenticação de palavra-passe deve existir dentro do VM. Pode criar um utilizador baseado em palavra-passe com a função de [senha de reset](../extensions/vmaccess.md#reset-password) da extensão de acesso VM. Selecione Redefinir a **palavra-passe** a partir da secção **'Suporte + resolução de problemas'.**
- A conta Azure que acede à Consola em Série deve ter [papel de Contribuinte de Máquina Virtual](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) tanto para o VM como para a conta de armazenamento de diagnóstico de [arranque](boot-diagnostics.md)
- As implantações clássicas não são suportadas. A sua instância de definição de escala de VM ou de máquina virtual deve utilizar o modelo de implementação do Gestor de Recursos Azure.

> [!NOTE]
> A Consola em Série é atualmente incompatível com uma conta de armazenamento de diagnóstico de arranque gerida. Para utilizar a Consola em Série, certifique-se de que está a utilizar uma conta de armazenamento personalizada.

## <a name="get-started-with-the-serial-console"></a>Começar com a Consola em Série
A Consola em Série para VMs e conjunto de escala de máquina virtual só está acessível através do portal Azure:

### <a name="serial-console-for-virtual-machines"></a>Consola em série para máquinas virtuais
A consola em série para VMs é tão simples como clicar na **consola série** dentro da secção **De Resolução de Problemas De Suporte +** no portal Azure.
  1. Abra o [portal do Azure](https://portal.azure.com).

  1. Navegue para **todos os recursos** e selecione uma Máquina Virtual. A página geral do VM abre.

  1. Desloque-se para baixo para a secção **de resolução de problemas Do Suporte +** e selecione a consola **série**. Um novo painel com a consola em série abre e inicia a ligação.

     ![Janela da consola em série Linux](./media/virtual-machines-serial-console/virtual-machine-linux-serial-console-connect.gif)

### <a name="serial-console-for-virtual-machine-scale-sets"></a>Consola em série para conjuntos de escala de máquina virtual
A Consola em Série está disponível para conjuntos de escala de máquinas virtuais, acessíveis em cada instância dentro do conjunto de escala. Terá de navegar para a instância individual de uma balança de máquina virtual definida antes de ver o botão **de consola Serial.** Se o seu conjunto de escala de máquina virtual não tiver diagnósticos de arranque ativados, certifique-se de atualizar o seu modelo de conjunto de escala de máquina virtual para ativar diagnósticos de arranque e, em seguida, atualizar todas as instâncias para o novo modelo de forma a aceder à consola em série.
  1. Abra o [portal do Azure](https://portal.azure.com).

  1. Navegue para **todos os recursos** e selecione um Conjunto de Escala de Máquina Virtual. A página geral para o conjunto de escala de máquina virtual abre.

  1. Navegar para **instâncias**

  1. Selecione uma instância de conjunto de escala de máquina virtual

  1. A partir da secção **De Suporte + Resolução de Problemas,** selecione **consola série**. Um novo painel com a consola em série abre e inicia a ligação.

     ![Conjunto de escala de máquina virtual Linux](./media/virtual-machines-serial-console/vmss-start-console.gif)


### <a name="tls-12-in-serial-console"></a>TLS 1.2 em Consola em Série
A Consola em Série utiliza o TLS 1.2 de ponta a ponta para garantir toda a comunicação dentro do serviço. A Consola em Série tem uma dependência de uma conta de armazenamento de diagnóstico de arranque gerida pelo utilizador e o TLS 1.2 deve ser configurado separadamente para a conta de armazenamento. As instruções para o fazer estão localizadas [aqui.](../../storage/common/transport-layer-security-configure-minimum-version.md)

## <a name="advanced-uses-for-serial-console"></a>Usos avançados para consola em série
Além do acesso à consola ao seu VM, também pode utilizar a Consola em Série Azure para o seguinte:
* Envio de um [comando de pedido de sistema para o seu VM](./serial-console-nmi-sysrq.md)
* Envio de uma [interrupção não-mascarada para o seu VM](./serial-console-nmi-sysrq.md)
* Graciosamente [reiniciando ou com força o seu VM](./serial-console-power-options.md)


## <a name="next-steps"></a>Passos seguintes
A documentação adicional da Consola em Série está disponível na barra lateral.
- Mais informações estão disponíveis para [a Consola em Série para VMs Linux](./serial-console-linux.md).
- Mais informações estão disponíveis para [consola em série para VMs windows](./serial-console-windows.md).
