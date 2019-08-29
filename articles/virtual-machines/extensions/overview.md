---
title: Recursos e extensões de máquina virtual do Azure | Microsoft Docs
description: Saiba o que são extensões de VM do Azure e como usá-las com máquinas virtuais do Azure
services: virtual-machines-linux
documentationcenter: ''
author: roiyz-msft
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 03/30/2018
ms.author: roiyz
ms.openlocfilehash: 8c63df2d9a7e398fb9b67edd3b57a3ba06cbe7a1
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/28/2019
ms.locfileid: "70084333"
---
# <a name="azure-virtual-machine-extensions-and-features"></a>Recursos e extensões de máquina virtual do Azure
As extensões de VM (máquina virtual) do Azure são aplicativos pequenos que fornecem tarefas de configuração e automação de pós-implantação em VMs do Azure, você pode usar imagens existentes e, em seguida, personalizá-las como parte de suas implantações, o que o torna fora dos negócios personalizados criação de imagem.

A plataforma Azure hospeda muitas extensões que variam de aplicativos de configuração, monitoramento, segurança e utilitário da VM. Os editores pegam um aplicativo, o encapsulam em uma extensão e simplificam a instalação, portanto, tudo o que você precisa fazer é fornecer parâmetros obrigatórios. 

 Há uma grande opção de extensões do primeiro e de terceiros, se o aplicativo no repositório de extensões não existir, você poderá usar a extensão de script personalizado e configurar sua VM com seus próprios scripts e comandos.

Exemplos de cenários principais para os quais extensões são usadas:
* Configuração da VM, você pode usar a DSC do PowerShell (configuração de estado desejado), o chefe, o Puppet e as extensões de script personalizado para instalar os agentes de configuração da VM e configurar sua VM. 
* Produtos AV, como Symantec, ESET.
* Ferramenta de vulnerabilidade de VM, como Qualys, Rapid7, HPE.
* Ferramentas de monitoramento de VM e aplicativo, como DynaTrace, observador de rede do Azure, Site24x7 e Stackify.

As extensões podem ser agrupadas com uma nova implantação de VM. Por exemplo, eles podem fazer parte de uma implantação maior, configurar aplicativos em provisionamento de VM ou executar em qualquer sistema operado com extensão com suporte após a implantação.

## <a name="how-can-i-find-what-extensions-are-available"></a>Como posso encontrar quais extensões estão disponíveis?
Você pode exibir as extensões disponíveis na folha da VM no portal, em extensões, isso representa apenas uma pequena quantidade, para a lista completa, você pode usar as ferramentas da CLI, consulte [descobrindo extensões de VM para Linux](features-linux.md) e [descobrindo extensões de VM para Windows](features-windows.md).

## <a name="how-can-i-install-an-extension"></a>Como posso instalar uma extensão?
As extensões de VM do Azure podem ser gerenciadas usando os modelos CLI do Azure, Azure PowerShell, Azure Resource Manager e portal do Azure. Para experimentar uma extensão, você pode ir para a portal do Azure, selecionar a extensão de script personalizado e, em seguida, passar um comando/script e executar as extensões.

Se você quiser ter a mesma extensão adicionada no portal por CLI ou modelo do Resource Manager, consulte a documentação de extensão diferente, como [extensão de script personalizado do Windows](custom-script-windows.md) e extensão de [script personalizado do Linux](custom-script-linux.md).

## <a name="how-do-i-manage-extension-application-lifecycle"></a>Como fazer gerenciar o ciclo de vida do aplicativo de extensão?
Você não precisa se conectar a uma VM diretamente para instalar ou excluir a extensão. Como o ciclo de vida do aplicativo de extensão do Azure é gerenciado fora da VM e integrado à plataforma Azure, você também obtém o status integrado da extensão.

## <a name="anything-else-i-should-be-thinking-about-for-extensions"></a>Qualquer outra coisa que eu deva pensar para extensões?
As extensões instalam aplicativos, como qualquer aplicativo, há alguns requisitos, para extensões, há uma lista de sistemas operacionais Windows e Linux com suporte e você precisa ter os agentes de VM do Azure instalados. Alguns aplicativos de extensão de VM individuais podem ter seus próprios pré-requisitos ambientais, como o acesso a um ponto de extremidade.

## <a name="next-steps"></a>Passos seguintes
* Para obter mais informações sobre como o agente e as extensões do Linux funcionam, consulte [recursos e extensões de VM do Azure para Linux](features-linux.md).
* Para obter mais informações sobre como o agente convidado do Windows e as extensões funcionam, consulte [extensões e recursos de VM do Azure para Windows](features-windows.md).  
* Para instalar o agente convidado do Windows, consulte [visão geral do agente de máquina virtual do Windows do Azure](agent-windows.md).  
* Para instalar o agente do Linux, consulte [visão geral do agente de máquina virtual Linux do Azure](agent-linux.md).  

