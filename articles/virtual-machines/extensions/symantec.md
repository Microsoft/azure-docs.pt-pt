---
title: Instalar o Symantec Endpoint Protection num Windows VM no Azure | Documentos da Microsoft
description: Saiba como instalar e configurar a extensão de segurança do Symantec Endpoint Protection numa VM do Azure nova ou existente criado com o modelo de implementação clássica.
services: virtual-machines-windows
documentationcenter: ''
author: roiyz
manager: jeconnoc
editor: ''
tags: azure-service-management
ms.assetid: 19dcebc7-da6b-4510-907b-d64088e81fa2
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-multiple
ms.devlang: na
ms.topic: article
ms.date: 03/31/2017
ms.author: roiyz
ms.openlocfilehash: 65b52c88741e618e8048451370918b06db73a651
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58014418"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows
> [!IMPORTANT] 
> O Azure tem dois modelos de implementação diferentes para criar e trabalhar com recursos: [Resource Manager e clássica](../../azure-resource-manager/resource-manager-deployment-model.md). Este artigo explica como utilizar o modelo de implementação clássica. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo mostra-lhe como instalar e configurar o cliente da Symantec Endpoint Protection numa máquina virtual existente (VM) com o Windows Server. Este cliente completo inclui os serviços, tais como vírus e spyware proteção, a firewall e prevenção contra intrusões. O cliente é instalado como uma extensão de segurança com o agente da VM.

Se tiver uma subscrição existente da Symantec para uma solução no local, pode utilizá-lo a proteger máquinas virtuais do Azure. Se ainda não estiver um cliente, pode inscrever-se para uma subscrição de avaliação. Para obter mais informações sobre esta solução, consulte [Symantec Endpoint Protection na plataforma Azure da Microsoft][Symantec]. Esta página também tem ligações para informações de licenciamento e instruções para instalar o cliente se já for um cliente da Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalar o Symantec Endpoint Protection numa VM existente
Antes de começar, precisa do seguinte:

* O módulo Azure PowerShell, versão 0.8.2 ou posterior, no computador de trabalho. Pode verificar a versão do PowerShell do Azure que tenha instalado com o **azure Get-Module | versão do formato-table** comando. Para obter instruções e um link para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell][PS]. Inicie sessão na sua subscrição do Azure com `Add-AzureAccount`.
* O agente de VM em execução na máquina de Virtual do Azure.

Em primeiro lugar, certifique-se de que o agente da VM já está instalado na máquina virtual. Preencha o nome do serviço cloud e o nome de máquina virtual e, em seguida, execute os seguintes comandos numa linha de comando do nível de administrador do Azure PowerShell. Substituir tudo dentro das aspas, incluindo os < e > carateres.

> [!TIP]
> Se não souber os nomes de máquina virtual e o serviço em nuvem, execute **Get-AzureVM** para listar os nomes de todas as máquinas virtuais na sua subscrição atual.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se o **write-host** comando apresenta **verdadeiro**, o agente da VM está instalado. Se for exibido **False**, consulte as instruções e um link para download na mensagem de blogue do Azure [agente da VM e extensões – parte 2][Agent].

Se o agente da VM estiver instalado, execute estes comandos para instalar o agente de proteção de ponto final da Symantec.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Para verificar se a extensão de segurança da Symantec foi instalada e que está atualizada:

1. Inicie sessão máquina virtual. Para obter instruções, consulte [como fazer logon numa máquina Virtual a executar o Windows Server][Logon].
2. Para o Windows Server 2008 R2, clique em **Iniciar > Symantec Endpoint Protection**. Para o Windows Server 2012 ou Windows Server 2012 R2, no ecrã Iniciar, digite **Symantec**e, em seguida, clique em **Symantec Endpoint Protection**.
3. Do **Status** separador da **Estado Symantec Endpoint Protection** janela, aplicar atualizações ou reiniciar se for necessário.

## <a name="additional-resources"></a>Recursos adicionais
[Como iniciar sessão numa máquina Virtual com o Windows Server][Logon]

[Extensões VM do Azure e funcionalidades][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
