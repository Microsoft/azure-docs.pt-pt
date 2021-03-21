---
title: Instale a proteção do ponto final Symantec num Windows VM em Azure
description: Aprenda a instalar e configurar a extensão de segurança Symantec Endpoint Protection num VM Azure novo ou existente criado com o modelo de implementação Classic.
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.collection: windows
ms.date: 03/31/2017
ms.openlocfilehash: 9a25e9eb27111a450f787f4efb3e0d39456fc757
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102559652"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar a proteção de ponto final Symantec num VM do Windows
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

A Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Classic.](../../azure-resource-manager/management/deployment-models.md) Este artigo abrange utilizando o modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo mostra-lhe como instalar e configurar o cliente Symantec Endpoint Protection numa máquina virtual existente (VM) que executa o Windows Server. Este cliente completo inclui serviços como proteção de vírus e spyware, firewall e prevenção de intrusões. O cliente é instalado como uma extensão de segurança utilizando o Agente VM.

Se tiver uma subscrição existente da Symantec para uma solução no local, pode usá-la para proteger as suas máquinas virtuais Azure. Se ainda não é cliente, pode inscrever-se para uma subscrição de teste. Para obter mais informações sobre esta solução, consulte [a Symantec Endpoint Protection na plataforma Azure da Microsoft.][Symantec] Esta página também tem links para licenciamento de informações e instruções para instalar o cliente se já é um cliente Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instale a proteção do ponto final Symantec num VM existente
Antes de começar, precisa do seguinte:

* O módulo Azure PowerShell, versão 0.8.2 ou posterior, no seu computador de trabalho. Pode verificar a versão do Azure PowerShell que instalou com o **comando de versão | versão de tabela de formato Get-Module.** Para obter instruções e um link para a versão mais recente, consulte [Como Instalar e Configurar a Azure PowerShell][PS]. Inicie sessão na sua assinatura Azure utilizando `Add-AzureAccount` .
* O agente VM a funcionar na Máquina Virtual Azure.

Primeiro, verifique se o Agente VM já está instalado na máquina virtual. Preencha o nome do serviço de nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos num pedido de comando Azure PowerShell ao nível do administrador. Substitua tudo dentro das citações, incluindo os caracteres < e >.

> [!TIP]
> Se não conhecer o serviço de nuvem e os nomes de máquinas virtuais, corra **o Get-AzureVM** para listar os nomes de todas as máquinas virtuais na sua subscrição atual.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se o comando **do anfitrião de escrita** apresentar **True,** o Agente VM é instalado. Se apresentar **Falso**, consulte as instruções e um link para o download no blog Azure post [VM Agent and Extensions - Part 2][Agent].

Se o Agente VM estiver instalado, executar estes comandos para instalar o agente Symantec Endpoint Protection.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Para verificar se a extensão de segurança Symantec foi instalada e está atualizada:

1. Inicie sessão na máquina virtual. Para obter instruções, consulte [como iniciar sessão num Servidor windows de uma máquina virtual.][Logon]
2. Para Windows Server 2008 R2, clique em **Iniciar > Proteção de Ponto final Symantec**. Para o Windows Server 2012 ou Windows Server 2012 R2, a partir do ecrã inicial, **escreva Symantec** e, em seguida, clique em **Symantec Endpoint Protection**.
3. A partir do **separador Estado** da janela **Status-Symantec Endpoint Protection,** aplique atualizações ou reinicie se necessário.

## <a name="additional-resources"></a>Recursos adicionais
[Como iniciar sessão numa máquina virtual que executa o servidor do Windows][Logon]

[Extensões e funcionalidades Azure VM][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azure/

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: features-windows.md
