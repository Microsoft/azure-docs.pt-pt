---
title: Funcionalidades e extensões de máquina virtual do Azure | Documentos da Microsoft
description: Saiba quais são as extensões de VM do Azure e como usá-los com máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: a35cba0ab7df80596ba1403765980809635c0249
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56881860"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Extensões de máquina virtual do Azure e funcionalidades
As extensões de máquina virtual do Azure (VM) são aplicativos pequenos que fornecem as tarefas de automatização e configuração de pós-implementação em VMs do Azure, pode utilizar imagens existentes e, em seguida, personalize-os como parte das suas implementações, saindo do a empresa de personalizado criação de imagem.

A plataforma do Azure aloja várias extensões que vão de configuração da VM, monitorização, segurança e aplicativos de utilitário. Os publicadores Pegue um aplicativo, em seguida, empacotá-lo numa extensão e simplificam a instalação, para que tudo o que precisa fazer é fornecer os parâmetros obrigatórios. 

 Há uma grande opção de primeiro e das extensões de terceiros, se a aplicação no repositório de extensão não existe, em seguida, pode utilizar a extensão de Script personalizado e configurar a VM com seus próprios scripts e comandos.

Exemplos de cenários-chave que extensões são utilizadas para:
* Configuração da VM, pode utilizar Powershell DSC (Desired State Configuration do), Chef, Puppet e extensões de Script personalizado para instalar agentes de configuração de VM e configurar a sua VM. 
* Produtos de AV, por exemplo, Symantec, ESET.
* Ferramenta de vulnerabilidade VM, como o Qualys, Rapid7, HPE.
* VM e as ferramentas, como a DynaTrace, observador de rede do Azure, Site24x7 e Stackify de monitorização de aplicações.

As extensões podem ser agrupadas com uma nova implementação de VM. Por exemplo, pode fazer parte de uma implantação maior, configurando aplicativos fornecimento de VM, ou executado em qualquer implementação de postagem de sistemas de extensão com suporte operado.

## <a name="how-can-i-find-what-extensions-are-available"></a>Como posso encontrar que extensões estão disponíveis?
Pode ver as extensões disponíveis no painel da VM no Portal, em extensões, isso representa apenas uma pequena quantidade, para a lista completa, pode utilizar as ferramentas da CLI, veja [descoberta de extensões de VM para Linux](features-linux.md) e [ Descoberta de extensões de VM para Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Como posso instalar uma extensão?
Extensões VM do Azure podem ser geridas com a CLI do Azure, Azure PowerShell, modelos Azure Resource Manager e o portal do Azure. Para experimentar uma extensão, pode ir para o portal do Azure, selecione a extensão de Script personalizado, em seguida, passar um comando / script e executar as extensões.

Se pretender que a mesma extensão que adicionou no portal pelo modelo CLI ou do Resource Manager, consulte a documentação de extensão diferentes, tal como [extensão de Script do Windows personalizado](custom-script-windows.md) e [extensão de Script personalizado de Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como posso gerir o ciclo de vida de aplicativos de extensão?
Não é necessário ligar a uma VM diretamente para instalar ou eliminar a extensão. Como o ciclo de vida do aplicativo de extensão do Azure é gerido fora da VM e integrado na plataforma do Azure, obtém também integrado estado da extensão.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Qualquer outra coisa que deve estar pensando sobre extensões de?
Extensões de instalar aplicações, como todas as aplicações existem alguns requisitos, para lá de extensões é uma lista de sistemas operacionais de Linux e Windows com suporte e terá de instalar os agentes de VM do Azure. Alguns aplicativos de extensão VM individuais podem ter seus próprios pré-requisitos ambientais, como o acesso a um ponto final.

## <a name="next-steps"></a>Passos Seguintes
* Para obter mais informações sobre como funcionam as extensões e o agente do Linux, consulte [extensões de VM do Azure e funcionalidades para Linux](features-linux.md).
* Para obter mais informações sobre como funcionam o agente convidado do Windows e as extensões, consulte [extensões de VM do Azure e funcionalidades para o Windows](features-windows.md).  
* Para instalar o agente convidado do Windows, consulte [descrição geral do agente de Máquina Virtual de Windows do Azure](agent-windows.md).  
* Para instalar o agente do Linux, veja [descrição geral do agente de Máquina Virtual de Linux do Azure](agent-linux.md).  

