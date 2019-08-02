---
title: Implantar hosts dedicados do Azure usando o portal do Azure | Microsoft Docs
description: Implante VMs em hosts dedicados usando o portal do Azure.
services: virtual-machines-linux
author: cynthn
manager: gwallace
editor: tysonn
tags: azure-resource-manager
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure
ms.date: 07/25/2019
ms.author: cynthn
ms.openlocfilehash: 03d8b5353a2dc9840c8231978243e7f94a5bff56
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68700395"
---
# <a name="preview-deploy-vms-to-dedicated-hosts-using-the-portal"></a>Pré-visualização: Implantar VMs em hosts dedicados usando o portal

Este artigo orienta você sobre como criar um [host dedicado](dedicated-hosts.md) do Azure para hospedar suas máquinas virtuais (VMS). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Criar uma VM

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Ubuntu Server 16.04 LTS** da Canonical e, em seguida, selecione **Criar**.
1. Na guia **noções básicas** , em **detalhes do projeto**, verifique se a assinatura correta está selecionada e, em seguida, selecione *MyDedicatedHostsRG* como o **grupo de recursos**. 
1. Em **Detalhes da instância**, escreva *aminhaVM* para o **Nome da máquina virtual** e selecione *E.U.A. Leste* para a **Localização**.
1. Em **Opções de disponibilidade** selecionar **zona de disponibilidade**, selecione *1* na lista suspensa.
1. Para o tamanho, selecione **alterar tamanho**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **Standard E2 v3**. Talvez seja necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Em **Conta de administrador**, selecione **Chave pública SSH**, escreva o seu nome de utilizador e, em seguida, cole a chave pública na caixa de texto. Remova quaisquer espaços em branco à esquerda ou à direita na chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. Em **regras** > de porta de entrada**portas públicas de entrada**, escolha **permitir portas selecionadas** e, em seguida, selecione **SSH (22)** na lista suspensa. 
1. Na parte superior da página, selecione a guia **avançado** e, na seção **host** , selecione *myhost* Group para o **grupo** de hosts e myhost para o **host**. 
    ![Selecionar grupo de hosts e host](./media/dedicated-hosts-portal/advanced.png)
1. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.
1. Quando você vir a mensagem a validação foi aprovada, selecione **criar**.

Irá demorar alguns minutos até a VM ser implementada.

## <a name="next-steps"></a>Passos Seguintes

- Para obter mais informações, consulte a visão geral dos [hosts dedicados](dedicated-hosts.md) .

- Há um modelo de exemplo, encontrado [aqui](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md), que usa zonas e domínios de falha para obter máxima resiliência em uma região.

- Você também pode implantar um host dedicado usando o [CLI do Azure](dedicated-hosts-cli.md).



