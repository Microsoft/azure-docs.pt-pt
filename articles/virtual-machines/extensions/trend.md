---
title: Instalar o Trend Micro Deep Security numa VM | Documentos da Microsoft
description: Este artigo descreve como instalar e configurar a segurança do Trend Micro numa VM criada com o modelo de implementação clássico no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 04/20/2018
ms.author: roiyz
ms.openlocfilehash: 0bb49d3d75b88f197a42e02f03f89480563537ef
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58005472"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar o Trend Micro Deep Security como Serviço numa VM do Windows
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Este artigo mostra-lhe como instalar e configurar o Trend Micro Deep Security como serviço numa nova ou existente máquina virtual (VM) com o Windows Server. Deep Security como um serviço inclui proteção antimalware, uma firewall, um sistema de prevenção de intrusões e monitorização da integridade.

O cliente é instalado como uma extensão de segurança por meio do agente de VM. Numa nova máquina virtual, instale o agente de segurança avançada, à medida que o agente da VM é criado automaticamente pelo portal do Azure.

Uma VM existente criada com o portal do Azure, a CLI do Azure ou o PowerShell poderá não ter um agente de VM. Para uma máquina virtual existente que não tem o agente da VM, terá de transferir e instalá-lo primeiro. Este artigo aborda as duas situações.

Se tiver uma subscrição atual do Trend Micro, para uma solução no local, pode utilizá-lo para ajudar a proteger máquinas virtuais do Azure. Se ainda não estiver um cliente, pode inscrever-se para uma subscrição de avaliação. Para obter mais informações sobre esta solução, consulte a mensagem de blogue Trend Micro [Microsoft Azure VM Agent extensão para o Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalar o agente de segurança profunda numa nova VM

O [portal do Azure](https://portal.azure.com) permite-lhe instalar a extensão de segurança do Trend Micro, quando utiliza uma imagem a partir do **Marketplace** para criar a máquina virtual. Se estiver a criar uma única máquina virtual, com o portal é uma forma fácil de adicionar uma proteção da Trend Micro.

Através de uma entrada a partir da **Marketplace** abre um assistente que o ajuda a configurar a máquina virtual. Utilizar o **definições** painel, o painel de terceira do assistente, para instalar a extensão de segurança do Trend Micro.  Para instruções gerais, veja [criar uma máquina virtual com Windows no portal do Azure](../windows/classic/tutorial.md).

Quando vai para o **definições** painel do assistente, efetue os seguintes passos:

1. Clique em **extensões**, em seguida, clique em **Adicionar extensão** no painel seguinte.

   ![Começar a adicionar a extensão][1]

2. Selecione **agente de segurança avançado** no **novo recurso** painel. No painel de agente de Deep Security, clique em **criar**.

   ![Identificar o agente de segurança avançado][2]

3. Introduza o **identificador do inquilino** e **palavra-passe de ativação do inquilino** para a extensão. Opcionalmente, pode introduzir um **identificador de política de segurança**. Em seguida, clique em **OK** para adicionar o cliente.

   ![Fornecer detalhes de extensão][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalar o agente de segurança profunda numa VM existente
Para instalar o agente numa VM existente, terá dos seguintes itens:

* O módulo Azure PowerShell, versão 0.8.2 ou mais recente, instalado no seu computador local. Pode verificar a versão do PowerShell do Azure que tenha instalado utilizando o **azure Get-Module | versão do formato-table** comando. Para obter instruções e um link para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Inicie sessão na sua subscrição do Azure com `Add-AzureAccount`.
* O agente da VM instalado na máquina de virtual de destino.

Em primeiro lugar, certifique-se de que o agente da VM já está instalado. Preencha o nome do serviço cloud e o nome de máquina virtual e, em seguida, execute os seguintes comandos numa linha de comando do nível de administrador do Azure PowerShell. Substituir tudo dentro das aspas, incluindo os < e > carateres.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se não souber o serviço em nuvem e o nome de máquina virtual, execute **Get-AzureVM** exibir essas informações para todas as máquinas virtuais na sua subscrição atual.

Se o **write-host** comando **verdadeiro**, o agente da VM está instalado. Se ele retorna **False**, consulte as instruções e um link para download na mensagem de blogue do Azure [agente da VM e extensões – parte 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o agente da VM estiver instalado, execute estes comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Passos Seguintes
Demora alguns minutos para que o agente começar a executar quando é instalado. Depois disso, terá de ativar o Deep Security na máquina virtual para que possam ser gerido por um Gestor de segurança avançada. Veja os artigos seguintes para obter instruções adicionais:

* Artigo da tendência sobre esta solução, [Instant-On Cloud Security para o Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* R [script do Windows PowerShell de exemplo](https://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
* [Instruções](https://go.microsoft.com/fwlink/?LinkId=404099) para o exemplo

## <a name="additional-resources"></a>Recursos adicionais
[Como iniciar sessão a uma máquina virtual com o Windows Server]

[Extensões de VM do Azure e funcionalidades]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Como iniciar sessão a uma máquina virtual com o Windows Server]:../windows/classic/connect-logon.md
[Extensões de VM do Azure e funcionalidades]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
