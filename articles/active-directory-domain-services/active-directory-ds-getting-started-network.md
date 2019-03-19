---
title: 'Azure Active Directory Domain Services: Introdução | Documentos da Microsoft'
description: Ativar o Azure Active Directory Domain Services no portal do Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/23/2018
ms.author: ergreenl
ms.openlocfilehash: 3020d7b29f19ec2ab578acbebac8db8ea320a844
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58098030"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Ativar o Azure Active Directory Domain Services no portal do Azure


## <a name="before-you-begin"></a>Antes de começar
Consulte [Networking considerations for Azure Active Directory Domain Services (Considerações sobre o funcionamento em rede do Azure Active Directory Domain Services)](active-directory-ds-networking.md).


## <a name="task-2-configure-network-settings"></a>Tarefa 2: Configurar definições de rede
A tarefa de configuração seguinte é criar uma rede virtual do Azure e uma sub-rede dedicada na mesma. O Azure Active Directory Domain Services é ativado nesta sub-rede dentro da sua rede virtual. Também pode escolher uma rede virtual existente e criar a sub-rede dedicada na mesma.

1. Clique em **rede Virtual** para selecionar uma rede virtual.
    > [!NOTE]
    > **Redes virtuais clássicas não são suportadas para novas implementações.** Redes virtuais clássicas não são suportadas para novas implementações. Os domínios geridos existentes implementados em redes virtuais clássicas continuam a ser suportados. Microsoft permitirá que migrar um domínio gerido existente de uma rede virtual clássica a uma rede virtual do Gestor de recursos num futuro próximo.
    >

2. Sobre o **escolher rede virtual** página, verá todas as redes virtuais existentes. Verá apenas as redes virtuais que pertencem ao grupo de recursos e localização do Azure que selecionou no **Noções básicas** página do assistente.
3. Escolha a rede virtual em que o Azure AD Domain Services deve ser ativado. Pode escolher uma rede virtual existente ou crie um novo.

   > [!TIP]
   > **Não é possível mover o seu domínio gerido para uma rede virtual diferente depois de ativar o Azure AD Domain Services.** Escolha a rede virtual correta para ativar o seu domínio gerido. Depois de criar um domínio gerido, é possível movê-lo para uma rede virtual diferente, sem eliminar o domínio gerido. Recomendamos que reveja os [considerações para o Azure Active Directory Domain Services de redes](active-directory-ds-networking.md) antes de continuar.  
   >

4. **Crie rede virtual:** Clique em **criar novo** para criar uma nova rede virtual. Utilize uma sub-rede dedicada para o Azure AD Domain Services. Por exemplo, crie uma sub-rede com o nome "DomainServices', tornando mais fácil para os outros administradores compreender o que é implementado na sub-rede. Clique em **OK** quando terminar.

    ![Escolha a rede virtual](./media/getting-started/domain-services-blade-network-pick-vnet.png)

   > [!WARNING]
   > Certifique-se de escolher um espaço de endereços que esteja dentro do espaço de endereços IP privados. Endereços IP que não é o proprietário que estão no espaço de endereços públicos causar erros nos serviços de domínio do Azure AD.

5. **Rede virtual existente:** Se pretender escolher uma rede virtual existente, [criar uma sub-rede dedicada com a extensão de redes virtuais](../virtual-network/virtual-network-manage-subnet.md#add-a-subnet)e, em seguida, escolha essa sub-rede. Clique em **rede Virtual** para selecionar a rede virtual existente. Clique em **sub-rede** para selecionar a sub-rede dedicada na sua rede virtual existente, no qual pretende ativar o seu novo domínio gerido. Clique em **OK** quando terminar.

    ![Escolher sub-rede dentro da rede virtual](./media/getting-started/domain-services-blade-network-pick-subnet.png)

   > [!NOTE]
   > **Diretrizes para selecionar uma sub-rede**
   > 1. Utilize uma sub-rede dedicada para o Azure AD Domain Services. Não implemente quaisquer outras máquinas virtuais para esta sub-rede. Esta configuração permite-lhe configurar grupos de segurança de rede (NSGs) para as suas máquinas virtuais/cargas de trabalho sem interromper o seu domínio gerido. Para obter detalhes, consulte [considerações para o Azure Active Directory Domain Services de redes](active-directory-ds-networking.md).
   > 2. Não selecione a sub-rede do Gateway para implementar os serviços de domínio do Azure AD, porque não é uma configuração suportada.
   > 3. A sub-rede que selecionou tem de ter, pelo menos, 3 a 5 endereços IP disponíveis no seu espaço de endereços.

6. Quando tiver terminado, clique em **OK** para avançar para o **grupo Administrador** página do assistente.


## <a name="next-step"></a>Passo seguinte
[Tarefa 3: configurar o grupo administrativo e ativar os serviços de domínio do Azure AD](active-directory-ds-getting-started-admingroup.md)
