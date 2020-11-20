---
title: Instale a Tendência Micro Deep Security num VM
description: Este artigo descreve como instalar e configurar a segurança Trend Micro num VM criado com o modelo de implementação Clássico em Azure.
author: axayjo
tags: azure-service-management
ms.service: virtual-machines-windows
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: article
ms.date: 04/20/2018
ms.author: akjosh
ms.openlocfilehash: 9d6d80287d05517933f066d5e49fa31e78a48943
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966384"
---
# <a name="how-to-install-and-configure-trend-micro-deep-security-as-a-service-on-a-windows-vm"></a>Como instalar e configurar a Trend Micro Deep Security como um serviço num VM do Windows

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

[!INCLUDE [virtual-machines-extensions-deprecation-statement](../../../includes/virtual-machines-extensions-deprecation-statement.md)]
Este artigo mostra-lhe como instalar e configurar a Trend Micro Deep Security como um serviço numa máquina virtual nova ou existente (VM) que executa o Windows Server. A Deep Security como serviço inclui proteção anti-malware, uma firewall, um sistema de prevenção de intrusões e monitorização da integridade.

O cliente é instalado como uma extensão de segurança através do Agente VM. Numa nova máquina virtual, instala-se o Agente de Segurança Profunda, uma vez que o Agente VM é criado automaticamente pelo portal Azure.

Um VM existente criado usando o portal Azure, o Azure CLI, ou PowerShell pode não ter um agente VM. Para uma máquina virtual existente que não tenha o Agente VM, você precisa descarregá-lo e instalá-lo primeiro. Este artigo abrange ambas as situações.

Se tiver uma subscrição atual da Trend Micro para uma solução no local, pode usá-la para ajudar a proteger as suas máquinas virtuais Azure. Se ainda não é cliente, pode inscrever-se para uma subscrição de teste. Para obter mais informações sobre esta solução, consulte o blog Trend Micro post [Microsoft Azure VM Agent Extension For Deep Security](https://go.microsoft.com/fwlink/p/?LinkId=403945).

## <a name="install-the-deep-security-agent-on-a-new-vm"></a>Instale o Agente de Segurança Profunda num novo VM

O [portal Azure](https://portal.azure.com) permite instalar a extensão de segurança Trend Micro quando utiliza uma imagem do **Marketplace** para criar a máquina virtual. Se estiver a criar uma única máquina virtual, usar o portal é uma forma fácil de adicionar proteção à Trend Micro.

A utilização de uma entrada no **Marketplace** abre um assistente que o ajuda a configurar a máquina virtual. Utilize a lâmina **Definições,** o terceiro painel do assistente, para instalar a extensão de segurança Trend Micro.  Para obter instruções gerais, consulte [Criar uma máquina virtual que executa o Windows no portal Azure](../windows/quick-create-portal.md).

Quando chegar à lâmina **de Definições** do assistente, faça os seguintes passos:

1. Clique **em Extensões** e clique em **Adicionar extensão** no painel seguinte.

   ![Comece a adicionar a extensão][1]

2. Selecione **o Agente de Segurança Profunda** no painel de novos **recursos.** No painel de agentes de segurança profunda, clique em **Criar**.

   ![Identificar Agente de Segurança Profunda][2]

3. Introduza a Senha de **Ativação** **do Inquilino** e do Inquilino para a extensão. Opcionalmente, pode introduzir um **identificador de política de segurança.** Em seguida, clique **em OK** para adicionar o cliente.

   ![Fornecer detalhes de extensão][3]

## <a name="install-the-deep-security-agent-on-an-existing-vm"></a>Instale o Agente de Segurança Profunda num VM existente
Para instalar o agente num VM existente, necessita dos seguintes itens:

* O módulo Azure PowerShell, versão 0.8.2 ou mais recente, instalado no seu computador local. Pode verificar a versão do Azure PowerShell que instalou utilizando o comando **get-Module azure / formato-table version.** Para obter instruções e um link para a versão mais recente, consulte [Como instalar e configurar a Azure PowerShell](/powershell/azure/). Inicie sessão na sua assinatura Azure utilizando `Add-AzureAccount` .
* O Agente VM instalado na máquina virtual alvo.

Primeiro, verifique se o Agente VM já está instalado. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos num pedido de comando Azure PowerShell ao nível do administrador. Substitua tudo dentro das citações, incluindo os caracteres < e >.

```azurepowershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se não conhecer o serviço de nuvem e o nome da máquina virtual, corra **o Get-AzureVM** para exibir essa informação para todas as máquinas virtuais na sua subscrição atual.

Se o comando **do anfitrião de escrita** voltar a ser **verdadeiro,** o Agente VM é instalado. Se retornar **Falso**, consulte as instruções e um link para o download no blog Azure post [VM Agent and Extensions - Part 2](https://go.microsoft.com/fwlink/p/?LinkId=403947).

Se o Agente VM estiver instalado, executar estes comandos.

```azurepowershell
$Agent = Get-AzureVMAvailableExtension TrendMicro.DeepSecurity -ExtensionName TrendMicroDSA

Set-AzureVMExtension -Publisher TrendMicro.DeepSecurity –Version $Agent.Version -ExtensionName TrendMicroDSA -VM $vm | Update-AzureVM
```

## <a name="next-steps"></a>Passos seguintes
Leva alguns minutos para o agente começar a funcionar quando está instalado. Depois disso, precisa de ativar a Deep Security na máquina virtual para que possa ser gerida por um Gestor de Segurança Profunda. Consulte os seguintes artigos para obter instruções adicionais:

* Artigo da Trend sobre esta solução, [Instant-On Cloud Security para o Microsoft Azure](https://go.microsoft.com/fwlink/?LinkId=404101)
* Uma [amostra do script do Windows PowerShell](https://go.microsoft.com/fwlink/?LinkId=404100) para configurar a máquina virtual
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
[Extensões e funcionalidades Azure VM]: features-windows.md
