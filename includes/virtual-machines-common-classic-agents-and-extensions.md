---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 9158e6bfe07fc5d06b0685d77eff26644b594a8b
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61485280"
---
As extensões de VM podem ajudá-lo a:

* Modificar as funcionalidades de segurança e identidade, como repor valores de conta e utilizar antimalware
* Iniciar, parar ou configurar a monitorização e o diagnóstico
* Repor ou instalar as funcionalidades de conectividade, como RDP e SSH
* Diagnosticar, monitorizar e gerir as suas VMs

Existem também muitas outras funcionalidades. Novas funcionalidades de extensão de VM são lançadas regularmente. Este artigo descreve os Agentes VM do Azure para o Windows e Linux e como podem suportam a funcionalidade de Extensão de VM. Para obter uma lista de extensões de VM por categoria de funcionalidades, veja [Extensões e Funcionalidades de VM do Azure](../articles/virtual-machines/extensions/features-windows.md).

## <a name="azure-vm-agents-for-windows-and-linux"></a>Agentes VM do Azure para Windows e Linux
O Agente de Máquinas Virtuais (Agente VM) do Azure é um processo protegido e leve que instala, configura e remove as extensões de VM de instâncias de máquinas virtuais do Azure. O Agente VM funciona como o serviço de controlo local seguro para a sua VM do Azure. As extensões que o agente carrega fornecem funcionalidades específicas para aumentar a produtividade ao utilizar a instância.

Existem dois Agentes VM do Azure, um para VMs do Windows e outro para VMs do Linux.

Se pretender que uma instância de máquina virtual utilize uma ou mais extensões de VM, a instância tem de ter um Agente VM instalado. Uma imagem de máquina virtual criada com o portal do Azure e uma imagem do **Marketplace** instalará automaticamente um Agente VM durante o processo de criação. Se uma instância da máquina virtual não tiver um Agente VM, pode instalar o Agente VM depois de a instância de máquina virtual ser criada. Em alternativa, pode instalar o agente numa imagem de VM personalizada que irá então carregar.

> [!IMPORTANT]
> Estes Agentes VM são serviços muito leves que permitem a administração protegida de instâncias de máquina virtual. Poderá haver casos em que não queira o Agente VM. Se assim for, certifique-se de que cria VMs em que o Agente VM não foi instalado com a CLI do Azure ou o PowerShell. Apesar de o Agente VM poder ser removido fisicamente, o comportamento das Extensões de VM na instância não está definido. Como resultado, a remoção de um Agente VM instalado não é suportada.
>

O Agente VM é ativado nas seguintes situações:

* Quando cria uma instância de uma VM através do portal do Azure e seleciona uma imagem do **Marketplace**,
* Quando cria uma instância de uma VM através do cmdlet [New-AzureVM](https://msdn.microsoft.com/library/azure/dn495254.aspx) ou [New-AzureQuickVM](https://msdn.microsoft.com/library/azure/dn495183.aspx). Pode criar uma VM sem um Agente VM, ao adicionar o parâmetro **–DisableGuestAgent** ao cmdlet [Add-AzureProvisioningConfig](https://msdn.microsoft.com/library/azure/dn495299.aspx),

* Ao transferir e instalar manualmente o Agente VM numa instância de VM existente e definir o valor **ProvisionGuestAgent** para **verdadeiro**. Pode utilizar esta técnica para agentes do Windows e Linux, com um comando do PowerShell ou uma chamada REST. (Se não definir o valor **ProvisionGuestAgent** depois de instalar manualmente o Agente VM, a adição do Agente VM não é detetada corretamente.) O exemplo de código seguinte mostra como efetuar este procedimento com o PowerShell, em que os argumentos `$svc` e `$name` já foram determinados:

      $vm = Get-AzureVM –ServiceName $svc –Name $name
      $vm.VM.ProvisionGuestAgent = $TRUE
      Update-AzureVM –Name $name –VM $vm.VM –ServiceName $svc

* Quando cria uma imagem de VM que inclui um agente VM instalado. Depois de a imagem com o agente VM existir, pode carregar essa imagem para o Azure. Para uma VM do Windows, transfira o [ficheiro .msi do Agente VM do Windows](https://go.microsoft.com/fwlink/?LinkID=394789) e instale o Agente VM. Para uma VM do Linux, instale o agente VM a partir do repositório GitHub localizado em <https://github.com/Azure/WALinuxAgent>. Para obter mais informações sobre como instalar o Agente VM no Linux, veja [Guia de Utilizador do Agente VM do Linux no Azure](../articles/virtual-machines/extensions/agent-linux.md).

> [!NOTE]
> No PaaS, o Agente VM chama-se **WindowsAzureGuestAgent** e está sempre disponível em VMs da Web e de Função de Trabalho. (Para obter mais informações, veja [Arquitetura de Funções do Azure](https://blogs.msdn.com/b/kwill/archive/2011/05/05/windows-azure-role-architecture.aspx).) O Agente VM para VMs de função pode agora adicionar extensões às VMs do serviço cloud, da mesma forma que o faz para Máquinas Virtuais persistentes. A maior diferença entre Extensões de VM em VMs de função e em VMs persistentes é quando as Extensões de VM são adicionadas. Nas VMs de função, as extensões são primeiro adicionadas ao serviço cloud e, em seguida, às implementações dentro desse serviço cloud.
>
> Utilize o cmdlet [Get-AzureServiceAvailableExtension](https://msdn.microsoft.com/library/azure/dn722498.aspx) para listar todas as extensões de VM de funções disponíveis.
>
>

## <a name="find-add-update-and-remove-vm-extensions"></a>Localizar, Adicionar, Atualizar e Remover Extensões de VM
Para obter detalhes sobre estas tarefas, veja [Adicionar, Localizar, Atualizar e Remover Extensões de VM do Azure](../articles/virtual-machines/windows/classic/manage-extensions.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).
