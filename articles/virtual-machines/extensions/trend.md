---
title: Instale a Trend Micro Deep Security num VM
description: Este artigo descreve como instalar e configurar a segurança trend micro num VM criado com o modelo de implementação Clássico em Azure.
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
ms.openlocfilehash: cffd2eab3a616b4d16d847d0f2e1a26655f40459
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77919928"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar a Trend Micro Deep Security como um serviço num VM windows

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Este artigo mostra-lhe como instalar e configurar a Trend Micro Deep Security como serviço numa máquina virtual nova ou existente (VM) que executa o Windows Server. A Deep Security como Serviço inclui proteção anti-malware, uma firewall, um sistema de prevenção de intrusões e monitorização da integridade.

O cliente é instalado como uma extensão de segurança através do Agente VM. Numa nova máquina virtual, instala-se o Agente de Segurança Profunda, uma vez que o Agente VM é criado automaticamente pelo portal Azure.

Um VM existente criado com o portal Azure, o Azure CLI ou powerShell pode não ter um agente VM. Para uma máquina virtual existente que não tem o Agente VM, precisa de descarregar e instalar primeiro. Este artigo aborda ambas as situações.

Se tiver uma subscrição atual da Trend Micro para uma solução no local, pode usá-la para ajudar a proteger as suas máquinas virtuais Azure. Se ainda não é cliente, pode inscrever-se para uma subscrição de teste. Para obter mais informações sobre esta solução, consulte a publicação de blog Trend Micro [Microsoft Azure VM Agent Extension for Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instale o Agente de Segurança Profunda num novo VM

O [portal Azure](https://portal.azure.com) permite instalar a extensão de segurança Trend Micro quando utiliza uma imagem do **Marketplace** para criar a máquina virtual. Se estiver a criar uma única máquina virtual, usar o portal é uma forma fácil de adicionar proteção contra a Trend Micro.

A utilização de uma entrada do **Marketplace** abre um assistente que o ajuda a configurar a máquina virtual. Utilize a lâmina **Definições,** o terceiro painel do assistente, para instalar a extensão de segurança Trend Micro.  Para obter instruções gerais, consulte [Criar uma máquina virtual que execute o Windows no portal Azure](../windows/classic/tutorial.md).

Quando chegar à lâmina **definições** do assistente, faça os seguintes passos:

1. Clique em **Extensões**e, em seguida, clique em **adicionar extensão** no próximo painel.

   ![Comece a adicionar a extensão][1]

2. Selecione **Deep Security Agent** no novo painel de **recursos.** No painel do Agente de Segurança Profunda, clique em **Criar**.

   ![Identificar agente de segurança profunda][2]

3. Introduza a **palavra-passe** de **identificação** de inquilino e de ativação do inquilino para a extensão. Opcionalmente, pode introduzir um identificador de política de **segurança.** Em seguida, clique em **OK** para adicionar o cliente.

   ![Fornecer detalhes de extensão][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instale o Agente de Segurança Profunda num VM existente
Para instalar o agente num VM existente, precisa dos seguintes itens:

* O módulo Azure PowerShell, versão 0.8.2 ou mais recente, instalado no seu computador local. Pode verificar a versão do Azure PowerShell que instalou utilizando o comando da **versão get-module azure  formato-table** comando. Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar o Azure PowerShell](/powershell/azure/overview). Inicie sessão na subscrição do Azure utilizando `Add-AzureAccount`.
* O Agente VM instalado na máquina virtual alvo.

Primeiro, verifique se o Agente VM já está instalado. Preencha o nome do serviço na nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos num pedido de comando Azure PowerShell de nível administrador. Substitua tudo dentro das cotações, incluindo os caracteres < e >

    $CSName = "<cloud service name>"
    $VMName = "<virtual machine name>"
    $vm = Get-AzureVM -ServiceName $CSName -Name $VMName
    write-host $vm.VM.ProvisionGuestAgent

Se não conhece o serviço de nuvem e o nome da máquina virtual, faça o **Get-AzureVM** para mostrar essa informação para todas as máquinas virtuais da sua subscrição atual.

Se o comando de **hospedeiro de escrita** devolver **True,** o Agente VM está instalado. Se devolver **Falso**, consulte as instruções e um link para o download no post de blog Azure [VM Agent and Extensions - Parte 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o Agente VM estiver instalado, execute estes comandos.

    $Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

    Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM

## <a name="next-steps"></a>Passos seguintes
Leva alguns minutos para o agente começar a funcionar quando está instalado. Depois disso, é necessário ativar a Segurança Profunda na máquina virtual para que possa ser gerida por um Gestor de Segurança Profunda. Consulte os seguintes artigos para obter instruções adicionais:

* Artigo da Trend sobre esta solução, [Instant-On Cloud Security para o Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Um [script windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) de amostra para configurar a máquina virtual
* [Instruções](https://go.microsoft.com/fwlink/?LinkId=404099) para a amostra

## <a name="additional-resources"></a>Recursos adicionais
[Como iniciar sessão numa máquina virtual que executa o Windows Server]

[Extensões e funcionalidades Azure VM]

<!-- Image references -->
[1]: ./media/trend/new_vm_Blade3.png
[2]: ./media/trend/find_SecurityAgent.png
[3]: ./media/trend/SecurityAgentDetails.png

<!-- Link references -->
[Como iniciar sessão numa máquina virtual que executa o Windows Server]:../windows/classic/connect-logon.md
[Extensões e funcionalidades Azure VM]: https://go.microsoft.com/fwlink/p/?linkid=390493&clcid=0x409
