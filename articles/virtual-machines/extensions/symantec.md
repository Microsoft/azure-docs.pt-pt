---
title: Instale proteção de endpoint symantec num VM do Windows em Azure
description: Saiba como instalar e configurar a extensão de segurança symantec Endpoint Protection num novo Ou existente Azure VM criado com o modelo de implementação Classic.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: gwallace
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.topic: article
ms.date: 03/31/2017
ms.author: akjosh
ms.openlocfilehash: 63f9441d4df9551405c2ab2bf8c0c67d7de5753c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "77919911"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar a Proteção de Endpoint Symantec num VM do Windows
[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Gestor de Recursos e Clássico.](../../azure-resource-manager/management/deployment-models.md) Este artigo cobre a utilização do modelo de implantação Classic. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo mostra-lhe como instalar e configurar o cliente Symantec Endpoint Protection numa máquina virtual existente (VM) que executa o Windows Server. Este cliente completo inclui serviços como proteção de vírus e spyware, firewall e prevenção de intrusões. O cliente é instalado como uma extensão de segurança utilizando o Agente VM.

Se tiver uma subscrição existente da Symantec para uma solução no local, pode usá-la para proteger as suas máquinas virtuais Azure. Se ainda não é cliente, pode inscrever-se para uma subscrição de teste. Para obter mais informações sobre esta solução, consulte [a Symantec Endpoint Protection na plataforma Azure da Microsoft][Symantec]. Esta página também tem links para licenciamento de informações e instruções para instalar o cliente se já é cliente Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instale proteção de endpoint symantec num VM existente
Antes de começar, precisa do seguinte:

* O módulo Azure PowerShell, versão 0.8.2 ou posterior, no seu computador de trabalho. Pode verificar a versão do Azure PowerShell que instalou com o comando da **versão get-module azure [ formato-table** comando. Para obter instruções e um link para a versão mais recente, consulte Como Instalar e Configurar a [PowerShell do Azure][PS]. Inicie sessão na subscrição do Azure utilizando `Add-AzureAccount`.
* O Agente VM a funcionar na Máquina Virtual Azure.

Primeiro, verifique se o Agente VM já está instalado na máquina virtual. Preencha o nome do serviço na nuvem e o nome da máquina virtual e, em seguida, execute os seguintes comandos num pedido de comando Azure PowerShell de nível administrador. Substitua tudo dentro das citações, incluindo os caracteres < e >.

> [!TIP]
> Se não conhece o serviço de nuvem e os nomes de máquinas virtuais, faça o **Get-AzureVM** para listar os nomes de todas as máquinas virtuais na sua subscrição atual.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se o comando **de hospedeiro** de escrita apresentar **True,** o Agente VM está instalado. Se apresentar **Falso**, consulte as instruções e um link para o download no post de blog Azure [VM Agent and Extensions - Parte 2][Agent].

Se o Agente VM estiver instalado, execute estes comandos para instalar o agente de proteção de endpoint symantec.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Para verificar se a extensão de segurança Symantec foi instalada e está atualizada:

1. Aceda à máquina virtual. Para obter instruções, consulte [como iniciar sessão numa máquina virtual que executa][Logon]o servidor do Windows .
2. Para windows Server 2008 R2, clique **em Iniciar > Proteção de Endpoint Symantec**. Para windows Server 2012 ou Windows Server 2012 R2, a partir do ecrã inicial, tipo **Symantec,** e depois clique em **Symantec Endpoint Protection**.
3. A partir do separador **Status-Symantec Endpoint Protection,** aplique atualizações ou reinicie se necessário. **Status**

## <a name="additional-resources"></a>Recursos adicionais
[Como iniciar sessão numa máquina virtual que executa o servidor do Windows][Logon]

[Extensões e funcionalidades azure VM][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
