---
title: Ligue-se às suas máquinas virtuais através de um browser - Azure [ Microsoft Docs
description: Saiba como se conectar às suas máquinas virtuais através de um browser.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: 4549ee0f50c06040b70307c96b5ca96cd4162214
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74974304"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Conecte-se às suas máquinas virtuais através de um browser 

A DevTest Labs integra-se com o [Azure Bastion,](https://docs.microsoft.com/azure/bastion/)que lhe permite ligar-se às suas máquinas virtuais através de um browser. Para obter informações sobre como ativar esta funcionalidade em DevTest Labs, consulte [Enable a ligação do navegador em máquinas virtuais de laboratório](enable-browser-connection-lab-virtual-machines.md).

Uma vez que a *ligação* do Navegador esteja ativada, os utilizadores de laboratório podem aceder a máquinas virtuais através de um browser.  

## <a name="create-a-lab-virtual-machine"></a>Criar uma máquina virtual de laboratório

Primeiro é preciso criar a máquina virtual de laboratório dentro de uma rede virtual que tenha o Bastion configurado nela. Selecione a segunda **sub-rede** que criou, não a AzureBastionSubnet. Pode selecionar uma rede virtual durante a criação de máquinas virtuais, indo para o separador de **definições Avançada.**

![Criar a máquina virtual](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Lance máquina virtual em um navegador

Assim que a máquina virtual for criada, pode lançá-la num browser clicando no botão de *ligação* do Navegador e inserindo o seu nome de utilizador e palavra-passe para a máquina.  

![Lançamento em um navegador](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Passos Seguintes

[Adicione um VM a um laboratório em Azure DevTest Labs](devtest-lab-add-vm.md)
