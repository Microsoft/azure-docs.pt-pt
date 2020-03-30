---
title: Gestão remota do Windows sobre HTTPS para Azure Mercado Azure
description: Explica como configurar um VM baseado no Windows, baseado no Windows, para que possa ser gerido remotamente com o PowerShell.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: dsindona
ms.openlocfilehash: 7c799c4a56ee5fc2074e4d644bdbcbc6d2b1ca5a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288755"
---
# <a name="windows-remote-management-over-https"></a>Gestão Remota do Windows através de HTTPS

Esta secção explica como configurar um VM baseado no Windows, baseado no Windows, para que possa ser gerido e implantado remotamente com o PowerShell.  Para permitir a remoção powerShell, o VM alvo deve expor um ponto final HTTPS de Gestão Remota do Windows (WinRM).  Para obter mais informações sobre o remo powerShell, consulte [Comandos Remotos em Execução](https://docs.microsoft.com/powershell/scripting/learn/remoting/running-remote-commands).  Para mais informações sobre o WinRM, consulte [a Windows Remote Management](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Se criou um VM utilizando uma das abordagens "clássicas" do Azure — ou o Portal do Gestor de Serviços Azure ou a Depreciada API de Gestão de [Serviços Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))— então é automaticamente configurado com um ponto final WinRM.  No entanto, se criar um VM utilizando qualquer uma das seguintes abordagens "modernas" do Azure, então o seu VM *não* será configurado para WinRM em HTTPS.

- Utilizando o [portal Azure](https://portal.azure.com/), tipicamente a partir de uma base aprovada, como descrito na secção [Criar um VHD compatível com Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Usando os modelos de Gestor de Recursos Azure](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Utilizando a concha de comando Azure PowerShell ou Azure CLI.  Por exemplo, consulte [Quickstart: Crie uma máquina virtual Windows em Azure com PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e [Quickstart: Crie uma máquina virtual Linux com o Azure CLI](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Este ponto final WinRM também é necessário para executar o kit de ferramentas de certificação para embarcar no VM, conforme descrito em [Certify a sua imagem VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Em contraste, normalmente os VMs Linux são geridos remotamente usando comandos [Azure CLI](https://docs.microsoft.com/cli/azure) ou Linux a partir de uma consola SSH.  O Azure também fornece vários métodos alternativos para [executar scripts no seu Linux VM](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Para cenários mais complexos, existem várias soluções de automação e integração disponíveis para VMs baseados no Windows ou Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configure e implemente com winRM

O ponto final winRM para um VM baseado em janelas pode ser configurado em duas fases diferentes do seu desenvolvimento:

- Durante a criação - durante a implantação de um VM para um VHD existente.  Esta é a abordagem preferida para novas ofertas.  Esta abordagem requer a criação de um certificado Azure, utilizando modelos fornecidos do Gestor de Recursos Azure, e executando scripts PowerShell personalizados.
- Após a implantação - num VM existente hospedado no Azure.  Utilize esta abordagem se já tiver uma solução VM implantada no Azure e precisa de ativar a Gestão remota da janela para o mesmo.  Esta abordagem requer alterações manuais no portal Azure e a execução de um script no VM alvo.


## <a name="next-steps"></a>Passos seguintes
Se estiver a criar um novo VM, pode ativar o WinRM durante a [implementação do seu VM a partir dos seus VHDs](./cpp-deploy-vm-vhd.md).  Caso contrário, o WinRM pode ser ativado num VM existente
