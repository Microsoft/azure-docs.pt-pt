---
title: Gerenciamento Remoto do Windows sobre HTTPS para o Azure | Azure Marketplace
description: Explica como configurar uma VM do Windows hospedada no Azure para que ela possa ser gerenciada remotamente com o PowerShell.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/26/2018
ms.author: pabutler
ms.openlocfilehash: 6e159bd9b57b26c99afd590d6a9f2153dba2a205
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73808429"
---
# <a name="windows-remote-management-over-https"></a>Gerenciamento Remoto do Windows sobre HTTPS

Esta seção explica como configurar uma VM do Windows hospedada no Azure para que ela possa ser gerenciada e implantada remotamente com o PowerShell.  Para habilitar a comunicação remota do PowerShell, a VM de destino deve expor um ponto de extremidade HTTPS de Gerenciamento Remoto do Windows (WinRM).  Para obter mais informações sobre a comunicação remota do PowerShell, consulte [executando comandos remotos](https://docs.microsoft.com/powershell/scripting/core-powershell/running-remote-commands?view=powershell-6).  Para obter mais informações sobre o WinRM, consulte [gerenciamento remoto do Windows](https://docs.microsoft.com/windows/desktop/WinRM/portal).

Se você criou uma VM usando uma das abordagens "clássicas" do Azure — o portal de Service Manager do Azure ou o [API de gerenciamento de serviços do Azure](https://docs.microsoft.com/previous-versions/azure/ee460799(v=azure.100))preterido, ele será configurado automaticamente com um ponto de extremidade do WinRM.  No entanto, se você criar uma VM usando qualquer uma das seguintes abordagens "modernas" do Azure, sua VM *não* será configurada para WinRM por HTTPS.  

- Usando o [portal do Azure](https://portal.azure.com/), normalmente de uma base aprovada, conforme descrito na seção [criar um VHD compatível com o Azure](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-create-vhd)
- [Usando os modelos de Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)
- Usando o Azure PowerShell ou o Shell de comando CLI do Azure.  Para obter exemplos, consulte [início rápido: criar uma máquina virtual do Windows no Azure com o PowerShell](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-powershell) e [o início rápido: criar uma máquina virtual Linux com o CLI do Azure](https://docs.microsoft.com/azure/virtual-machines/linux/quick-create-cli).

Esse ponto de extremidade do WinRM também é necessário para executar o kit de ferramentas de certificação para integração da VM, conforme descrito em [certificar sua imagem de VM](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/virtual-machine/cpp-certify-vm).

Por outro lado, normalmente as VMs do Linux são gerenciadas remotamente usando comandos [CLI do Azure](https://docs.microsoft.com/cli/azure) ou Linux de um console do SSH.  O Azure também fornece vários métodos alternativos para [executar scripts em sua VM Linux](https://docs.microsoft.com/azure/virtual-machines/linux/run-scripts-in-vm).  Para cenários mais complexos, há uma série de soluções de automação e integração disponíveis para VMs baseadas em Windows ou Linux.


## <a name="configure-and-deploy-with-winrm"></a>Configurar e implantar com o WinRM

O ponto de extremidade do WinRM para uma VM baseada no Windows pode ser configurado durante dois estágios diferentes de seu desenvolvimento:

- Durante a criação-durante a implantação de uma VM em um VHD existente.  Essa é a abordagem preferida para novas ofertas.  Essa abordagem requer a criação de um certificado do Azure, o uso de modelos de Azure Resource Manager fornecidos e a execução de scripts personalizados do PowerShell. 
- Após a implantação-em uma VM existente hospedada no Azure.  Use essa abordagem se você já tiver uma solução de VM implantada no Azure e precisar habilitar o gerenciamento remoto de janelas para ela.  Essa abordagem requer alterações manuais no portal do Azure e a execução de um script na VM de destino. 


## <a name="next-steps"></a>Passos seguintes
Se você estiver criando uma nova VM, poderá habilitar o WinRM durante a [implantação da VM a partir de seus VHDs](./cpp-deploy-vm-vhd.md).  Caso contrário, o WinRM poderá ser habilitado em uma VM existente  
