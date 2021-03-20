---
title: Conecte-se às suas máquinas virtuais através de um browser - Azure | Microsoft Docs
description: Saiba como se conectar às suas máquinas virtuais através de um browser.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 352fc5fd3ff53a00d9f62966ecf21417ad898706
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "87288058"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Conecte-se às suas máquinas virtuais através de um browser 

A DevTest Labs integra-se com [o Azure Bastion,](../bastion/index.yml)que permite ligar-se às suas máquinas virtuais através de um browser. Para obter informações sobre como ativar esta funcionalidade em DevTest Labs, consulte [Ativar a ligação do navegador em máquinas virtuais de laboratório](enable-browser-connection-lab-virtual-machines.md).

Uma vez que o *browser connect* está ativado, os utilizadores de laboratório podem aceder a máquinas virtuais através de um navegador.  

## <a name="create-a-lab-virtual-machine"></a>Criar uma máquina virtual de laboratório

Primeiro tens de criar a máquina virtual do laboratório dentro de uma rede virtual que tenha o Bastion configurado nela. Selecione a segunda **sub-rede** que criou, não a AzureBastionSubnet. Pode selecionar uma rede virtual durante a criação de máquinas virtuais indo para o separador **Definições Avançadas.**

![Criar a máquina virtual](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Lançar máquina virtual num browser

Uma vez criada a máquina virtual, pode lançá-la num browser clicando no botão *de ligação do Navegador* e introduzindo o seu nome de utilizador e senha para a máquina.  

![Lançamento em um navegador](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Passos Seguintes

[Adicione um VM a um laboratório em Azure DevTest Labs](devtest-lab-add-vm.md)
