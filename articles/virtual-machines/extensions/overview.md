---
title: Azure virtual machine extensions and features (Extensões e funcionalidades das máquinas virtuais do Azure)
description: Saiba quais são as extensões Azure VM e como usá-las com máquinas virtuais Azure
services: virtual-machines-linux
documentationcenter: ''
author: axayjo
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/12/2019
ms.author: akjosh
ms.openlocfilehash: bf17f499c1e8339a1e9abb13cffd5e35c390f564
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74072968"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure virtual machine extensions and features (Extensões e funcionalidades das máquinas virtuais do Azure)
As extensões da máquina virtual Azure (VM) são pequenas aplicações que fornecem tarefas de configuração pós-implantação e automação em VMs Azure, pode utilizar as imagens existentes e depois personalizá-las como parte das suas implementações, tirando-o do negócio de construção de imagem personalizada.

A plataforma Azure acolhe muitas extensões que vão desde a configuração vM, monitorização, segurança e aplicações de utilidade. Os editores pegam numa aplicação, em seguida, embrulhá-la em uma extensão, e simplificar a instalação, por isso tudo o que você precisa fazer é fornecer parâmetros obrigatórios. 

 Existe uma grande escolha de extensões de primeiro e terceiro, se a aplicação no repositório de extensão não existir, então pode usar a extensão do Script Personalizado e configurar o seu VM com os seus próprios scripts e comandos.

Exemplos de cenários-chave para os casos em que as extensões são utilizadas:
* Configuração VM, pode utilizar Powershell DSC (Configuração de Estado Desejado), Chef, Marioneta e Extensões de Script Personalizados para instalar agentes de configuração VM e configurar o seu VM. 
* Produtos AV, como Symantec, ESET.
* Ferramenta de vulnerabilidade VM, tais como Qualys, Rapid7, HPE.
* Ferramentas de monitorização vM e App, tais como DynaTrace, Azure Network Watcher, Site24x7 e Stackify.

As extensões podem ser agregadas com uma nova implantação vm. Por exemplo, podem fazer parte de uma implementação maior, configurar aplicações na provisão VM ou correr contra qualquer implantação de sistemas de extensão suportados.

## <a name="how-can-i-find-what-extensions-are-available"></a>Como posso encontrar quais extensões estão disponíveis?
Pode visualizar extensões disponíveis na lâmina VM no Portal, sob extensões, isto representa apenas uma pequena quantidade, para a lista completa, pode utilizar as ferramentas CLI, ver [Extensões VM para Linux](features-linux.md) e [Descobrir extensões VM para Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Como posso instalar uma extensão?
As extensões Azure VM podem ser geridas utilizando os modelos Azure CLI, Azure PowerShell, Azure Resource Manager e o portal Azure. Para tentar uma extensão, pode ir ao portal Azure, selecionar a extensão do script personalizado, em seguida, passar num comando/script e executar as extensões.

Se pretender a mesma extensão adicionada no portal pelo modelo CLI ou Gestor de Recursos, consulte diferentes documentação de extensão, tais como [extensão](custom-script-windows.md) de script personalizado do Windows e [extensão de script personalizado do Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como posso gerir o ciclo de vida da aplicação de extensão?
Não é necessário ligar-se diretamente a um VM para instalar ou eliminar a extensão. Como o ciclo de vida da aplicação de extensão Azure é gerido fora do VM e integrado na plataforma Azure, você também obtém o estado integrado da extensão.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Mais alguma coisa em que eu devia estar a pensar em extensões?
As extensões instalam aplicações, como quaisquer aplicações existem alguns requisitos, para extensões existe uma lista de OSes Suportados windows e Linux, e é necessário instalar os agentes Azure VM. Algumas aplicações individuais de extensão vm podem ter os seus próprios pré-requisitos ambientais, tais como o acesso a um ponto final.

## <a name="troubleshoot-extensions"></a>Resolver problemas relacionados com as extensões

As informações de resolução de problemas para cada extensão podem ser encontradas na secção **De suposição de Problemas e suporte** na visão geral para a extensão. Aqui está uma lista das informações de resolução de problemas disponíveis:

| Espaço de nomes | Resolução de problemas |
|-----------|-----------------|
| microsoft.azure.monitor.dependencyagent.dependencyagentlinux | [Dependência do Monitor Azure para Linux](agent-dependency-linux.md#troubleshoot-and-support) |
| microsoft.azure.monitor.dependencyagent.dependencyagentwindows | [Dependência do Monitor Azure para Windows](agent-dependency-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskcryptonforlinux | [Encriptação de disco azure para Linux](azure-disk-enc-linux.md#troubleshoot-and-support) |
| microsoft.azure.security.azurediskencryption | [Encriptação de disco azure para Windows](azure-disk-enc-windows.md#troubleshoot-and-support) |
| microsoft.compute.customscriptextension | [Script personalizado para Windows](custom-script-windows.md#troubleshoot-and-support) |
| microsoft.ostcextensions.customscriptforlinux | [Configuração estatal desejada para Linux](dsc-linux.md#troubleshoot-and-support) |
| microsoft.powershell.dsc | [Configuração de estado desejada para windows](dsc-windows.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverlinux | [Extensão do condutor da GPU da NVIDIA para o Linux](hpccompute-gpu-linux.md#troubleshoot-and-support) |
| microsoft.hpccompute.nvidiagpudriverwindows | [Extensão do condutor da GPU da NVIDIA para Windows](hpccompute-gpu-windows.md#troubleshoot-and-support) |
| microsoft.azure.security.iaasantimalware | [Extensão antimalware para Windows](iaas-antimalware-windows.md#troubleshoot-and-support) |
| microsoft.enterprisecloud.monitoring.omsagentforlinux | [Monitor Azure para Linux](oms-linux.md#troubleshoot-and-support)
| microsoft.enterprisecloud.monitoring.microsoftmonitoringagent | [Monitor Azure para Windows](oms-windows.md#troubleshoot-and-support) |
| stackify.linuxagent.extension.stackifylinuxagentextension | [Stackify Retrace para Linux](stackify-retrace-linux.md#troubleshoot-and-support) |
| vmaccessforlinux.microsoft.ostcextensions | [Reset password (VMAccess) para Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Snapshot para Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Snapshot para Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre o funcionado do Agente Linux e extensões, consulte [extensões e funcionalidades do VM Azure para o Linux](features-linux.md).
* Para obter mais informações sobre o funcionao do Agente convidado do Windows e extensões, consulte [extensões e funcionalidades Do VM do Azure para windows](features-windows.md).  
* Para instalar o Agente Convidado do Windows, consulte a visão geral do [agente virtual do Windows](agent-windows.md).  
* Para instalar o Agente Linux, consulte a visão geral do [agente virtual da máquina Azure Linux](agent-linux.md).  

