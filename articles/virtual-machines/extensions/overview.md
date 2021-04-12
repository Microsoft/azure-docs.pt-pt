---
title: Azure virtual machine extensions and features (Extensões e funcionalidades das máquinas virtuais do Azure)
description: Saiba mais sobre as extensões Azure VM
ms.topic: article
ms.service: virtual-machines
ms.subservice: extensions
author: amjads1
ms.author: amjads
ms.date: 08/03/2020
ms.openlocfilehash: e1b96293db0389201fdab3340d8f0e74fefc4c52
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102559720"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure virtual machine extensions and features (Extensões e funcionalidades das máquinas virtuais do Azure)
As extensões são pequenas aplicações que fornecem configuração e automatização pós-implantação em VMs Azure. A plataforma Azure acolhe muitas extensões que cobrem a configuração, monitorização, segurança e aplicações de utilidade. Os editores pegam numa aplicação, envolvem-na numa extensão e simplificam a instalação. Tudo o que precisa fazer é fornecer parâmetros obrigatórios. 

## <a name="how-can-i-find-what-extensions-are-available"></a>Como posso descobrir que extensões estão disponíveis?
Pode visualizar as extensões disponíveis selecionando um VM, as **extensões** de seleção no menu esquerdo. Para obter uma lista completa de extensões, consulte a descoberta de [extensões VM para Linux](features-linux.md) e [descobrir extensões VM para windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Como posso instalar uma extensão?
As extensões Azure VM podem ser geridas utilizando os modelos Azure CLI, PowerShell, Resource Manager e o portal Azure. Para tentar uma extensão, vá ao portal Azure, selecione a Extensão de Script Personalizado e, em seguida, passe num comando ou script para executar a extensão.

Para obter mais informações, consulte [a extensão de script personalizada do Windows](custom-script-windows.md) e a [extensão de script personalizada do Linux.](custom-script-linux.md)

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como posso gerir o ciclo de vida da aplicação de extensão?
Não é necessário ligar-se diretamente a um VM para instalar ou eliminar uma extensão. O ciclo de vida de extensão Azure é gerido fora do VM e integrado na plataforma Azure.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Mais alguma coisa em que eu deva pensar para prolongar?
Algumas aplicações individuais de extensão de VM podem ter os seus próprios pré-requisitos ambientais, tais como o acesso a um ponto final. Cada extensão tem um artigo que explica quaisquer requisitos prévios, incluindo quais os sistemas operativos suportados.

## <a name="troubleshoot-extensions"></a>Resolver problemas relacionados com as extensões

As informações de resolução de problemas para cada extensão podem ser encontradas na secção **de resolução de problemas e suporte** na visão geral para a extensão. Aqui está uma lista das informações de resolução de problemas disponíveis:

| Espaço de Nomes | Resolução de problemas |
|-----------|-----------------|
| microsoft.azure.monitor.dependencyagent.dependencyagentlinux | [Dependência do Monitor Azure para Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitoring.dependencyagent.dependencyagentwindows | [Dependência do Monitor Azure para Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencrypationforlinux | [Encriptação do disco Azure para Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencrycryption | [Encriptação do disco Azure para Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Script personalizado para windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Configuração de estado desejada para Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Configuração de estado desejada para windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Extensão do condutor do GPU da NVIDIA para o Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Extensão do controlador gpu da NVIDIA para windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Extensão antimalware para windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Monitor Azure para Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Monitor Azure para Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace para Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Redefinir palavra-passe para Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Instantâneo para Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Instantâneo para Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como o Agente Linux e as extensões funcionam, consulte [extensões e funcionalidades do Azure VM para o Linux](features-linux.md).
* Para obter mais informações sobre o funcionamento do Windows Guest Agent e das extensões, consulte [as extensões e funcionalidades do Azure VM para windows](features-windows.md).  
* Para instalar o Windows Guest Agent, consulte [a visão geral do Agente de Máquinas Virtuais do Azure Windows](agent-windows.md).  
* Para instalar o Agente Linux, consulte [a visão geral do Agente de Máquinas Virtuais Azure Linux](agent-linux.md).  

