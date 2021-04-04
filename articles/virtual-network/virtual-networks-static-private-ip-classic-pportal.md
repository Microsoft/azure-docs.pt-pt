---
title: Configure endereços IP privados para VMs (Classic) - Portal Azure | Microsoft Docs
description: Saiba como configurar endereços IP privados para máquinas virtuais (Classic) utilizando o portal Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
tags: azure-service-management
ms.assetid: b8ef8367-58b2-42df-9f26-3269980950b8
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: genli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 6986f6f16cbd32d44223bba4f4be4577fa11258c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98222909"
---
# <a name="configure-private-ip-addresses-for-a-virtual-machine-classic-using-the-azure-portal"></a>Configure endereços IP privados para uma máquina virtual (Classic) usando o portal Azure

[!INCLUDE [virtual-networks-static-private-ip-selectors-classic-include](../../includes/virtual-networks-static-private-ip-selectors-classic-include.md)]

[!INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica. Também pode [gerir um endereço IP estático privado no modelo de implementação do Gestor de Recursos.](virtual-networks-static-private-ip-arm-pportal.md)

[!INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Os passos de amostra que se seguem esperam um ambiente simples já criado. Se pretender executar os passos tal como são apresentados neste documento, primeiro construa o ambiente de teste descrito na [criação de um vnet](/previous-versions/azure/virtual-network/virtual-networks-create-vnet-classic-pportal).

## <a name="how-to-specify-a-static-private-ip-address-when-creating-a-vm"></a>Como especificar um endereço IP privado estático ao criar um VM
Para criar um VM chamado *DNS01* na sub-rede *FrontEnd* de um VNet chamado *TestVNet* com um IP estático privado de *192.168.1.1.101,* complete os seguintes passos:

1. Num browser, navegue para https://portal.azure.com e, se necessário, inicie sessão com a sua conta do Azure.
2. Selecione **NEW**  >  **Compute**  >  **Windows Server 2012 R2 Datacenter**, note que a lista de **modelos de implementação Select** já mostra **Clássico** e, em seguida, selecione **Create**.
   
    ![Screenshot que mostra o portal Azure com o > de data do New > Compute > Windows Server 2012 R2.](./media/virtual-networks-static-ip-classic-pportal/figure01.png)
3. No **âmbito da Create VM,** insira o nome do VM a criar *(DNS01* no cenário), a conta de administrador local e a palavra-passe.
   
    ![Screenshot que mostra como criar um VM introduzindo o nome do VM, nome de utilizador do administrador local e senha.](./media/virtual-networks-static-ip-classic-pportal/figure02.png)
4. Selecione rede virtual de rede de **configuração opcional**  >    >  e, em seguida, selecione **TestVNet**. Se **o TestVNet** não estiver disponível, certifique-se de que está a utilizar a localização *central dos EUA* e criou o ambiente de teste descrito no início deste artigo.
   
    ![Screenshot que mostra a configuração opcional > rede > rede virtual > a opção TestVNet realçada.](./media/virtual-networks-static-ip-classic-pportal/figure03.png)
5. Em **Rede**, certifique-se de que a sub-rede atualmente selecionada é *FrontEnd*, em seguida, selecione **endereços IP**, em **designação de endereço IP** selecione **Estática**- e, em seguida, introduza *192.168.1.101* para **endereço IP** como visto abaixo.
   
    ![Screenshot que realça o campo IP Adresses onde se escreve o endereço IP estático.](./media/virtual-networks-static-ip-classic-pportal/figure04.png)    
6. Selecione **OK** em **endereços IP**, selecione **OK** em **Rede** e, em seguida, selecione **OK** em **configuração opcional**.
7. Em **Create VM,** selecione **Criar**. Note o azulejo abaixo exibido no seu painel de instrumentos:
   
    ![Screenshot que mostra o azulejo do Centro de Dados R2 do Windows Server 2012.](./media/virtual-networks-static-ip-classic-pportal/figure05.png)

## <a name="how-to-retrieve-static-private-ip-address-information-for-a-vm"></a>Como recuperar informações estáticas de endereço IP privadas para um VM
Para visualizar as informações estáticas do endereço IP privado para o VM criado com os passos acima, execute os passos abaixo.

1. A partir do portal Azure, **selecione BROWSE ALL**  >  **Virtual machines (clássico)**  >  **DNS01** Todas as  >  **definições**  >  **endereços IP** e note a atribuição de endereço IP e endereço IP como visto abaixo.
   
    ![Criar VM no portal Azure](./media/virtual-networks-static-ip-classic-pportal/figure06.png)

## <a name="how-to-remove-a-static-private-ip-address-from-a-vm"></a>Como remover um endereço IP privado estático de um VM

Nos **endereços IP**, selecione **Dynamic** para o direito de atribuição de **endereço IP**, selecione **Guardar**, e, em seguida, selecione **Sim**, como mostrado na seguinte imagem:
   
![Screenshot que mostra como remover o endereço IP privado estático de um VM selecionando Dynamic à direita da etiqueta de atribuição de endereço IP.](./media/virtual-networks-static-ip-classic-pportal/figure07.png)

## <a name="how-to-add-a-static-private-ip-address-to-an-existing-vm"></a>Como adicionar um endereço IP privado estático a um VM existente

1. Nos **endereços IP**, mostrados anteriormente, selecione **Estática** à direita da atribuição de **endereço IP**.
2. Tipo *192.168.1.101* para **endereço IP**, selecione **Guardar**, e, em seguida, selecione **Sim**.

## <a name="set-ip-addresses-within-the-operating-system"></a>Definir endereços IP dentro do sistema operativo

Recomenda-se que não atribua estáticamente o IP privado atribuído à máquina virtual Azure dentro do sistema operativo de um VM, a menos que seja necessário. Se definir manualmente o endereço IP privado dentro do sistema operativo, certifique-se de que é o mesmo endereço que o endereço IP privado atribuído ao Azure VM, ou pode perder conectividade com a máquina virtual. Nunca deve atribuir manualmente o endereço IP público atribuído a uma máquina virtual Azure dentro do sistema operativo da máquina virtual.

## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre os endereços [IP públicos reservados.](/previous-versions/azure/virtual-network/virtual-networks-reserved-public-ip)
* Saiba mais sobre [endereços IP públicos de nível de instância (ILPIP).](/previous-versions/azure/virtual-network/virtual-networks-instance-level-public-ip)
* Consulte as [APIs de REPOUSO IP Reservadas](/previous-versions/azure/reference/dn722420(v=azure.100)).