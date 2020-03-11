---
title: Implementar anfitriões dedicados azure usando o portal Azure
description: Implemente VMs para anfitriões dedicados usando o portal Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: article
ms.date: 01/09/2020
ms.author: cynthn
ms.openlocfilehash: 5af09cf7ef6c811a239a64c5c6349c3625316177
ms.sourcegitcommit: 5f39f60c4ae33b20156529a765b8f8c04f181143
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2020
ms.locfileid: "78970741"
---
# <a name="deploy-vms-to-dedicated-hosts-using-the-portal"></a>Implementar VMs para anfitriões dedicados usando o portal

Este artigo guia-o através de como criar um [anfitrião dedicado](dedicated-hosts.md) azure para hospedar as suas máquinas virtuais (VMs). 

[!INCLUDE [virtual-machines-common-dedicated-hosts-portal](../../../includes/virtual-machines-common-dedicated-hosts-portal.md)]

## <a name="create-a-vm"></a>Criar uma VM

1. Selecione **Criar um recurso** no canto superior esquerdo do portal do Azure.
1. Na caixa de pesquisa acima da lista de recursos do Azure Marketplace, procure e selecione **Ubuntu Server 16.04 LTS** da Canonical e, em seguida, selecione **Criar**.
1. No separador **Basics,** sob os detalhes do **Projeto,** certifique-se de que a subscrição correta é selecionada e, em seguida, selecione *myDedicatedHostsRG* como o **grupo Recursos**. 
1. Em **Detalhes da instância**, escreva *aminhaVM* para o **Nome da máquina virtual** e selecione *E.U.A. Leste* para a **Localização**.
1. Nas **opções de disponibilidade** selecione Zona de **disponibilidade**, selecione *1* a partir do drop-down.
1. Para o tamanho, selecione **Tamanho de mudança**. Na lista de tamanhos disponíveis, escolha um da série Esv3, como **o Standard E2s v3**. Pode ser necessário limpar o filtro para ver todos os tamanhos disponíveis.
1. Em **Conta de administrador**, selecione **Chave pública SSH**, escreva o seu nome de utilizador e, em seguida, cole a chave pública na caixa de texto. Remova quaisquer espaços em branco à esquerda ou à direita na chave pública.

    ![Conta de administrador](./media/quick-create-portal/administrator-account.png)

1. De acordo com **as regras** portuárias de entrada > portas de **entrada públicas,** escolha permitir portas **selecionadas** e, em seguida, selecione **SSH (22)** a partir da queda. 
1. No topo da página, selecione o separador **Avançado** e na secção **Anfitrião,** selecione *myHostGroup* para **host group** e *myHost* for the **Host**. 
    ![Selecione grupo anfitrião e acolhe](./media/dedicated-hosts-portal/advanced.png)
1. Mantenha as restantes predefinições e, em seguida, selecione o botão **Rever + criar** na parte inferior da página.
1. Quando vir a mensagem que a validação passou, selecione **Criar**.

Irá demorar alguns minutos até a VM ser implementada.

## <a name="next-steps"></a>Passos seguintes

- Para mais informações, consulte a visão geral dos [anfitriões dedicados.](dedicated-hosts.md)

- Há um modelo de amostra, encontrado [aqui,](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vm-dedicated-hosts/README.md)que usa tanto zonas como domínios de falha para a máxima resiliência numa região.

- Também pode implantar um hospedeiro dedicado utilizando o [Azure CLI](dedicated-hosts-cli.md).



