---
title: Azure virtual machine extensions and features (Extensões e funcionalidades das máquinas virtuais do Azure)
description: Saiba o que são extensões Azure VM e como usá-las com máquinas virtuais Azure
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "74072968"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Azure virtual machine extensions and features (Extensões e funcionalidades das máquinas virtuais do Azure)
As extensões de máquina virtual Azure (VM) são pequenas aplicações que fornecem tarefas de configuração e automação pós-implantação em VMs Azure, pode usar imagens existentes e depois personalizá-las como parte das suas implementações, tirando-o do negócio de construção de imagens personalizadas.

A plataforma Azure acolhe muitas extensões que vão desde a configuração de VM, monitorização, segurança e aplicações de utilidade. Os editores tomam uma aplicação, embrulham-na numa extensão e simplificam a instalação, por isso tudo o que precisa de fazer é fornecer parâmetros obrigatórios. 

 Existe uma grande escolha de extensões de primeira e de terceiros, se a aplicação no repositório de extensão não existir, então pode usar a extensão de Script Personalizado e configurar o seu VM com os seus próprios scripts e comandos.

Exemplos de cenários-chave para os seguintes para:
* Configuração VM, pode utilizar Powershell DSC (Configuração de Estado Desejado), Chef, Extensões de Marionetas e Script Personalizado para instalar agentes de configuração VM e configurar o seu VM. 
* Produtos AV, como a Symantec, ESET.
* Ferramenta de vulnerabilidade VM, como Qualys, Rapid7, HPE.
* Ferramentas de monitorização de VM e App, tais como DynaTrace, Azure Network Watcher, Site24x7 e Stackify.

As extensões podem ser agregadas com uma nova implantação em VM. Por exemplo, podem fazer parte de uma implantação maior, configurando aplicações na provisão VM, ou correr contra qualquer sistema operado por extensão suportado após a implementação.

## <a name="how-can-i-find-what-extensions-are-available"></a>Como posso encontrar Que extensões estão disponíveis?
Pode ver as extensões disponíveis na lâmina VM no Portal, sob extensões, isto representa apenas uma pequena quantidade, para a lista completa, pode utilizar as ferramentas CLI, ver [Descoberta de Extensões VM para Linux](features-linux.md) e [Descobrir Extensões VM para Windows.](features-windows.md)

## <a name="how-can-i-install-an-extension"></a>Como posso instalar uma extensão?
As extensões Azure VM podem ser geridas usando os modelos Azure CLI, Azure PowerShell, Azure Resource Manager e o portal Azure. Para experimentar uma extensão, pode ir ao portal Azure, selecionar a Extensão de Script Personalizado, depois passar num comando/script e executar as extensões.

Se pretender a mesma extensão adicionada no portal pelo modelo CLI ou Gestor de Recursos, consulte diferente documentação de extensão, como [extensão de script personalizada do Windows](custom-script-windows.md) e [extensão de script personalizado Do Linux.](custom-script-linux.md)

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como posso gerir o ciclo de vida da aplicação de extensão?
Não é necessário ligar-se diretamente a um VM para instalar ou eliminar a extensão. Como o ciclo de vida da aplicação de extensão Azure é gerido fora do VM e integrado na plataforma Azure, você também obtém o estado integrado da extensão.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Mais alguma coisa em que eu deva pensar para prolongar?
As extensões instalam aplicações, como qualquer aplicação, existem alguns requisitos, para extensões existe uma lista de OSes suportados do Windows e Linux, e é necessário ter os agentes VM do Azure instalados. Algumas aplicações individuais de extensão de VM podem ter os seus próprios pré-requisitos ambientais, tais como o acesso a um ponto final.

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
| vmaccessforlinux.microsoft.ostcextensions | [Redefinir palavra-passe (VMAccess) para Linux](vmaccess.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Instantâneo para Linux](vmsnapshot-linux.md#troubleshoot-and-support) |
| microsoft.recoveryservices.vmsnapshot | [Instantâneo para Windows](vmsnapshot-windows.md#troubleshoot-and-support) |


## <a name="next-steps"></a>Próximos passos
* Para obter mais informações sobre como funciona o Agente e Extensões Linux, consulte [extensões e funcionalidades de Azure VM para Linux](features-linux.md).
* Para obter mais informações sobre o funcionamento do Windows Guest Agent and Extensions, consulte [as extensões e funcionalidades do Azure VM para windows](features-windows.md).  
* Para instalar o Windows Guest Agent, consulte [a visão geral do Agente de Máquinas Virtuais do Azure Windows](agent-windows.md).  
* Para instalar o Agente Linux, consulte [a visão geral do Agente de Máquinas Virtuais Azure Linux](agent-linux.md).  

