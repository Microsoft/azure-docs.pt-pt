---
title: Conectar-se às suas máquinas virtuais por meio de um navegador – Azure | Microsoft Docs
description: Saiba como se conectar às suas máquinas virtuais por meio de um navegador.
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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 1bb5d979ff7c9fe6e2afec4c7ebd81649b4488e3
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73581238"
---
# <a name="connect-to-your-virtual-machines-through-a-browser"></a>Conectar-se às suas máquinas virtuais por meio de um navegador 

O DevTest Labs integra-se à [bastiões do Azure](https://docs.microsoft.com/azure/bastion/), que permite que você se conecte às suas máquinas virtuais por meio de um navegador. Para obter informações sobre como habilitar esse recurso no DevTest Labs, consulte [habilitar conexão do navegador em máquinas virtuais do laboratório](enable-browser-connection-lab-virtual-machines.md).

Quando o *navegador conectar* estiver habilitado, os usuários do laboratório poderão acessar as máquinas virtuais por meio de um navegador.  


## <a name="create-a-lab-virtual-machine"></a>Criar uma máquina virtual do laboratório

Primeiro, você precisa criar a máquina virtual do laboratório em uma VNet que tenha a bastiões configurada nela. Você pode selecionar uma VNet durante a criação da máquina virtual acessando a guia **Configurações avançadas** .

![Criar a máquina virtual](./media/connect-virtual-machine-through-browser/create-virtual-machine.png)

## <a name="launch-virtual-machine-in-a-browser"></a>Iniciar máquina virtual em um navegador

Depois que a máquina virtual for criada, você poderá iniciá-la em um navegador clicando no botão *conectar do navegador* e inserindo seu nome de usuário e senha para o computador.  

![Iniciar em um navegador](./media/connect-virtual-machine-through-browser/browser-connect.png)

## <a name="next-steps"></a>Passos Seguintes

[Adicionar uma VM a um laboratório no Azure DevTest Labs](devtest-lab-add-vm.md)
