---
title: Neste tutorial, irá aprender a subscrever uma oferta do Azure Stack | Documentos da Microsoft
description: Este tutorial mostra-lhe como criar uma nova subscrição para serviços do Azure Stack e testar a oferta através da criação de uma máquina virtual de teste.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.custom: mvc
ms.date: 02/21/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 11/13/2018
ms.openlocfilehash: 431c31b39103e5ef21cb83b388167cab57e0c129
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56649089"
---
# <a name="tutorial-create-and-test-a-subscription"></a>Tutorial: criar e testar uma subscrição

Este tutorial mostra como criar uma subscrição que contém uma oferta e, em seguida, testá-lo. Para o teste, inicie sessão no portal de utilizador do Azure Stack como um administrador da nuvem, subscreva a oferta e, em seguida, crie uma máquina virtual.

> [!TIP]
> Para obter mais uma mais avançada experiência de avaliação, pode [criar uma subscrição para um usuário específico](../azure-stack-subscribe-plan-provision-vm.md#create-a-subscription-as-a-cloud-operator) e, em seguida, inicie sessão como utilizador no portal de utilizador.

Este tutorial mostra como subscrever uma oferta do Azure Stack.

O que irá aprender:

> [!div class="checklist"]
> * Subscrever uma oferta 
> * Testar a oferta

## <a name="subscribe-to-an-offer"></a>Subscrever uma oferta

Para subscrever uma oferta como um usuário, inicie sessão portal de utilizador do Azure Stack para descobrir os serviços que têm sido oferecidos pela operadora de rede do Azure Stack.

1. Inicie sessão para o usuário portal e selecione **obter uma subscrição**.

   ![Obter uma subscrição](media/azure-stack-subscribe-services/get-subscription.png)

2. No campo **Nome a Apresentar**, introduza um nome para a subscrição. Em seguida, selecione **oferecem** para escolher uma das ofertas disponíveis no **escolher uma oferta** secção. Em seguida, selecione **Criar**.

   ![Criar uma oferta](media/azure-stack-subscribe-services/create-subscription.png)

   > [!TIP]
   > Agora, tem de atualizar o portal de utilizador para começar a utilizar a sua subscrição.

3. Para ver a subscrição que criou, selecione **todos os serviços**. Em seguida, no **gerais** selecionar categoria **subscrições**e, em seguida, selecione a sua nova subscrição. Depois de subscrever uma oferta, atualize o portal para ver se os novos serviços tenham sido incluídos como parte da nova subscrição. Neste exemplo, **máquinas virtuais** foi adicionado.

   ![Vista de subscrição](media/azure-stack-subscribe-services/view-subscription.png)

## <a name="test-the-offer"></a>Testar a oferta

Enquanto tiver sessão iniciada portal de utilizador, pode testar a oferta ao aprovisionar uma máquina virtual utilizando os novos recursos de subscrição.

> [!NOTE]
> Este teste requer que primeiro foi adicionada uma VM do Windows Server 2016 Datacenter para o mercado do Azure Stack.

1. Inicie sessão no portal de utilizador.

2. No portal de utilizador, selecione **máquinas virtuais**, em seguida, **Add**, em seguida, **Windows Server 2016 Datacenter**e, em seguida, clique em **criar**.

3. Na **Noções básicas** secção, escreva um **nome**, **nome de utilizador**, e **palavra-passe**, escolha um **subscrição**, criar uma **grupo de recursos** (ou selecione um existente) e, em seguida, selecione **OK**.

4. Na **escolher um tamanho** secção, selecione **A1 padrão**e, em seguida, clique em **selecionar**.  

5. Na **configurações** painel, aceite as predefinições e selecione **OK**.

6. Na **resumo** secção, clique em **OK** para criar a máquina virtual.  

7. Para ver a sua nova máquina virtual, selecione **máquinas virtuais**, em seguida, procure a nova máquina virtual e clique no respetivo nome.

    ![Todos os recursos](media/azure-stack-subscribe-services/view-vm.png)

> [!NOTE]
> A implementação da máquina virtual demora alguns minutos a concluir.

## <a name="next-steps"></a>Passos Seguintes

O que aprendeu neste tutorial:

> [!div class="checklist"]
> * Subscrever uma oferta 
> * Testar a oferta

> [!div class="nextstepaction"]
> [Criar uma VM a partir de um modelo de Comunidade](azure-stack-create-vm-template.md)