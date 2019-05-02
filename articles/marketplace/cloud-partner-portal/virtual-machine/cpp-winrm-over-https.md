---
title: Windows gestão remota através de HTTPS para o Azure | O Azure Marketplace
description: Explica como configurar uma VM baseada no Windows, alojado no Azure, para que possam ser gerido remotamente com o PowerShell.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: fb661a2705d437d1f40ceebcad7e759c2a78540f
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64938233"
---
# <a name="windows-remote-management-over-https"></a>Gestão remota do Windows através de HTTPS

Esta secção explica como configurar uma VM baseada no Windows, alojado no Azure, para que possa ser gerido e implementado remotamente com o PowerShell.  Para ativar a comunicação remota do PowerShell, a VM de destino deve expor um ponto de final de HTTPS de gestão remota do Windows (WinRM).  Para obter mais informações sobre a comunicação remota do PowerShell, consulte [executar comandos remotos](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6).  Para obter mais informações sobre o WinRM, consulte [gestão remota do Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Se tiver criado uma VM com uma das abordagens "clássicas" do Azure – Portal do Azure Service Manager ou o preterido [API de gestão de serviço do Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))—, em seguida, ele é automaticamente configurado com um ponto de extremidade do WinRM.  No entanto, se criar uma VM com qualquer uma das seguintes abordagens de Azure "moderno", em seguida, a VM será *não* ser configurado para o WinRM por HTTPS.  

- Utilizar o [portal do Azure](https://portal.azure.com/), normalmente a partir de uma base aprovada, conforme descrito na secção [criar um VHD compatível com o Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Com os modelos Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Usando o shell de comandos para o Azure PowerShell ou a CLI do Azure.  Para obter exemplos, consulte [início rápido: Criar uma máquina virtual do Windows no Azure com o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e [início rápido: Criar uma máquina virtual Linux com a CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Este ponto final de WinRM também é necessário para executar o kit de ferramenta de certificação para a integração da VM, conforme descrito em [certificar a sua imagem VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Por outro lado, normalmente, as VMs do Linux são geridas remotamente através de um [CLI do Azure](https://docs.microsoft.com/cli/azure) ou comandos de Linux a partir de uma consola SSH.  O Azure também fornece vários métodos alternativos para [executem scripts na VM do Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Para cenários mais complexos, existem diversas soluções de integração e automatização disponível para VMs baseados em Windows ou Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurar e implementar com o WinRM

O ponto de final de WinRM para uma VM baseada no windows pode ser configurado durante dois diferentes fases de seu desenvolvimento:

- Durante a criação - durante a implementação de uma VM para um VHD existente.  Essa é a abordagem preferencial para novas ofertas.  Essa abordagem exige a criação de um certificado do Azure, utilizando modelos Azure Resource Manager fornecidos, e em execução personalizados, scripts do PowerShell. 
- Após a implementação - numa VM existente alojada no Azure.  Utilize esta abordagem se já tiver uma solução VM implementada no Azure e tem de ativar a gestão remota de janela para o mesmo.  Essa abordagem exige alterações manuais no portal do Azure e a execução de um script na VM de destino. 


## <a name="next-steps"></a>Passos Seguintes
Se estiver a criar uma nova VM, pode ativar o WinRM durante [implementação da sua VM a partir de seu VHD](./cpp-deploy-vm-vhd.md).  Caso contrário, o WinRM pode ser habilitado numa VM existente  
