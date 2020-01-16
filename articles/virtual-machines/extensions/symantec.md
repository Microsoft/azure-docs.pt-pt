---
title: Instalar o Endpoint Protection do Symantec em uma VM do Windows no Azure
description: Saiba como instalar e configurar a extensão de segurança do Symantec Endpoint Protection em uma VM do Azure nova ou existente criada com o modelo de implantação clássico.
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
ms.openlocfilehash: 63f6be105def083354abf8c546d1c334f4daa70e
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75975388"
---
# <a name="how-to-install-and-configure-symantec-endpoint-protection-on-a-windows-vm"></a>Como instalar e configurar o Symantec Endpoint Protection numa VM do Windows
> [!IMPORTANT] 
> O Azure tem dois modelos de implantação diferentes para criar e trabalhar com recursos: [Gerenciador de recursos e clássico](../../azure-resource-manager/management/deployment-models.md). Este artigo aborda o uso do modelo de implantação clássico. A Microsoft recomenda que as implementações mais novas utilizem o modelo Resource Manager.

Este artigo mostra como instalar e configurar o cliente Symantec Endpoint Protection em uma VM (máquina virtual) existente que executa o Windows Server. Esse cliente completo inclui serviços como proteção contra vírus e spyware, firewall e prevenção de intrusão. O cliente é instalado como uma extensão de segurança usando o agente de VM.

Se você tiver uma assinatura existente da Symantec para uma solução local, poderá usá-la para proteger suas máquinas virtuais do Azure. Se você ainda não é um cliente, você pode se inscrever para uma assinatura de avaliação. Para obter mais informações sobre essa solução, consulte [Symantec Endpoint Protection na plataforma do Azure da Microsoft][Symantec]. Esta página também tem links para informações de licenciamento e instruções para instalar o cliente se você já for um cliente da Symantec.

## <a name="install-symantec-endpoint-protection-on-an-existing-vm"></a>Instalar o Endpoint Protection da Symantec em uma VM existente
Antes de começar, você precisará do seguinte:

* O módulo Azure PowerShell, versão 0.8.2 ou posterior, em seu computador de trabalho. Você pode verificar a versão do Azure PowerShell que você instalou com o comando **Get-Module Azure | Format-Table Version** . Para obter instruções e um link para a versão mais recente, consulte [como instalar e configurar o Azure PowerShell][PS]. Faça logon em sua assinatura do Azure usando `Add-AzureAccount`.
* O agente de VM em execução na máquina virtual do Azure.

Primeiro, verifique se o agente de VM já está instalado na máquina virtual. Preencha o nome do serviço de nuvem e o nome da máquina virtual e execute os seguintes comandos em um prompt de comando Azure PowerShell de nível de administrador. Substitua tudo entre aspas, incluindo o < e > caracteres.

> [!TIP]
> Se você não souber o serviço de nuvem e os nomes de máquina virtual, execute **Get-AzureVM** para listar os nomes de todas as máquinas virtuais em sua assinatura atual.

```powershell
$CSName = "<cloud service name>"
$VMName = "<virtual machine name>"
$vm = Get-AzureVM -ServiceName $CSName -Name $VMName
write-host $vm.VM.ProvisionGuestAgent
```

Se o comando **write-host** exibir **true**, o agente de VM será instalado. Se ele exibir **falso**, consulte as instruções e um link para o download na postagem do blog do Azure [agente de VM e extensões-parte 2][Agent].

Se o agente de VM estiver instalado, execute estes comandos para instalar o agente de Endpoint Protection da Symantec.

```powershell
$Agent = Get-AzureVMAvailableExtension -Publisher Symantec -ExtensionName SymantecEndpointProtection

Set-AzureVMExtension -Publisher Symantec –Version $Agent.Version -ExtensionName SymantecEndpointProtection \
    -VM $vm | Update-AzureVM
```

Para verificar se a extensão de segurança da Symantec foi instalada e está atualizada:

1. Faça logon na máquina virtual. Para obter instruções, consulte [como fazer logon em uma máquina virtual que executa o Windows Server][Logon].
2. Para o Windows Server 2008 R2, clique em **iniciar > Symantec Endpoint Protection**. Para o Windows Server 2012 ou o Windows Server 2012 R2, na tela iniciar, digite **Symantec**e, em seguida, clique em **Symantec Endpoint Protection**.
3. Na guia **status** da janela **status-Symantec Endpoint Protection** , aplique as atualizações ou reinicie, se necessário.

## <a name="additional-resources"></a>Recursos adicionais
[Como fazer logon em uma máquina virtual que executa o Windows Server][Logon]

[Recursos e extensões de VM do Azure][Ext]

<!--Link references-->
[Symantec]: https://www.symantec.com/connect/blogs/symantec-endpoint-protection-now-microsoft-azure

[Create]:../windows/classic/tutorial.md

[PS]: /powershell/azureps-cmdlets-docs

[Agent]: https://go.microsoft.com/fwlink/p/?LinkId=403947

[Logon]:../windows/classic/connect-logon.md

[Ext]: https://go.microsoft.com/fwlink/p/?linkid=390493
