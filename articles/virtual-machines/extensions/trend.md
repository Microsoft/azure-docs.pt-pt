---
title: Instalar o Trend Micro Deep Security em uma VM
description: Este artigo descreve como instalar e configurar o Trend Micro Security em uma VM criada com o modelo de implantação clássico no Azure.
services: virtual-machines-windows
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: e991b635-f1e2-483f-b7ca-9d53e7c22e2a
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: a9f99d418d572c14a7cdd7975483cf39f0707cba
ms.sourcegitcommit: a107430549622028fcd7730db84f61b0064bf52f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74072993"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar o Trend Micro Deep Security como Serviço numa VM do Windows
[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Este artigo mostra como instalar e configurar o Trend Micro Deep Security as a Service em uma VM (máquina virtual) nova ou existente que executa o Windows Server. A segurança profunda como um serviço inclui proteção Antimalware, um firewall, um sistema de prevenção de intrusão e monitoramento de integridade.

O cliente é instalado como uma extensão de segurança por meio do agente de VM. Em uma nova máquina virtual, você instala o agente de segurança profunda, pois o agente de VM é criado automaticamente pelo portal do Azure.

Uma VM existente criada usando o portal do Azure, o CLI do Azure ou o PowerShell pode não ter um agente de VM. Para uma máquina virtual existente que não tem o agente de VM, você precisa baixá-la e instalá-la primeiro. Este artigo aborda as duas situações.

Se você tiver uma assinatura atual da Trend Micro para uma solução local, poderá usá-la para ajudar a proteger suas máquinas virtuais do Azure. Se você ainda não é um cliente, você pode se inscrever para uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte a postagem do blog Trend Micro [Microsoft Azure extensão do agente de VM para maior segurança](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instalar o Deep Security Agent em uma nova VM

O [portal do Azure](https://portal.azure.com) permite que você instale a extensão de segurança Trend Micro ao usar uma imagem do **Marketplace** para criar a máquina virtual. Se você estiver criando uma única máquina virtual, usar o portal é uma maneira fácil de adicionar proteção da Trend Micro.

O uso de uma entrada do **Marketplace** abre um assistente que ajuda você a configurar a máquina virtual. Você usa a folha **configurações** , o terceiro painel do assistente, para instalar a extensão de segurança Trend Micro.  Para obter instruções gerais, consulte [criar uma máquina virtual executando o Windows no portal do Azure](../windows/classic/tutorial.md).

Quando chegar à folha **configurações** do assistente, execute as seguintes etapas:

1. Clique em **extensões**e, em seguida, clique em **adicionar extensão** no próximo painel.

   ![Iniciar a adição da extensão][1]

2. Selecione **agente de segurança profunda** no painel **novo recurso** . No painel do agente de segurança profunda, clique em **criar**.

   ![Identificar o Deep Security Agent][2]

3. Insira o **identificador do locatário** e a **senha de ativação do locatário** para a extensão. Opcionalmente, você pode inserir um **identificador de política de segurança**. Em seguida, clique em **OK** para adicionar o cliente.

   ![Fornecer detalhes da extensão][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instalar o agente de segurança profunda em uma VM existente
Para instalar o agente em uma VM existente, você precisará dos seguintes itens:

* O módulo Azure PowerShell, versão 0.8.2 ou mais recente, instalado no computador local. Você pode verificar a versão do Azure PowerShell que você instalou usando o comando **Get-Module Azure | Format-Table Version** . Para obter instruções e um link para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Faça logon em sua assinatura do Azure usando `Add-AzureAccount`.
* O agente de VM instalado na máquina virtual de destino.

Primeiro, verifique se o agente de VM já está instalado. Preencha o nome do serviço de nuvem e o nome da máquina virtual e execute os seguintes comandos em um prompt de comando Azure PowerShell de nível de administrador. Substitua tudo entre aspas, incluindo o < e > caracteres.

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se você não souber o serviço de nuvem e o nome da máquina virtual, execute **Get-AzureVM** para exibir essas informações para todas as máquinas virtuais em sua assinatura atual.

Se o comando **write-host** retornar **true**, o agente de VM será instalado. Se ele retornar **false**, consulte as instruções e um link para o download na postagem do blog do Azure [agente de VM e extensões-parte 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o agente de VM estiver instalado, execute estes comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Passos seguintes
Leva alguns minutos para que o agente comece a ser executado quando estiver instalado. Depois disso, você precisa ativar a segurança profunda na máquina virtual para que ela possa ser gerenciada por um amplo Gerenciador de segurança. Consulte os seguintes artigos para obter instruções adicionais:

* Artigo da tendência sobre esta solução, [segurança instantânea na nuvem para Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Um [script de exemplo do Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
* [Instruções](https://go.microsoft.com/fwlink/?LinkId=404099) para o exemplo

## <a name="additional-resources"></a>Recursos adicionais
[Como fazer logon em uma máquina virtual que executa o Windows Server]

[Recursos e extensões de VM do Azure]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Como fazer logon em uma máquina virtual que executa o Windows Server]:../windows/classic/connect-logon.md
[Recursos e extensões de VM do Azure]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
