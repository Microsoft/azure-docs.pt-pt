---
title: Subscrever uma oferta no Azure Stack | Documentos da Microsoft
description: Criar subscrições para as ofertas no Azure Stack
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: 7f3f8683-ef09-4838-92ed-41f2fddbbbed
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: efemmano
ms.lastreviewed: 01/09/2019
ms.openlocfilehash: ba1232bc28d638d1e4293b5e6bf30b84b23e4951
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57761128"
---
# <a name="create-subscriptions-to-offers-in-azure-stack"></a>Criar subscrições de ofertas no Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Depois de [criar uma oferta](azure-stack-create-offer.md), os utilizadores precisam de uma subscrição para essa oferta antes de poderem utilizar. Existem duas formas a que os utilizadores podem obter subscrito para uma oferta:

- Como um operador de cloud, pode criar uma subscrição de um utilizador no portal do administrador. As subscrições que cria podem ser para as ofertas privadas e públicas.
- Como um utilizador de inquilino, pode subscrever uma oferta pública ao utilizar o portal de utilizador.  

## <a name="create-a-subscription-as-a-cloud-operator"></a>Criar uma subscrição como um operador de cloud

Operadores da nuvem podem utilizar o portal de administração para criar uma subscrição para uma oferta para um utilizador. Pode criar subscrições para os membros do seu próprio inquilino do diretório. Quando [multi-inquilinos](azure-stack-enable-multitenancy.md) é ativada, também pode criar subscrições para os utilizadores nos inquilinos do diretório adicional.

Se não quiser que seus inquilinos a criar seus próprios subscrições, tornar as suas ofertas privado e, em seguida, criar subscrições para os seus inquilinos. Essa abordagem é comum ao integrar o Azure Stack com faturação externa ou sistemas de catálogo de serviço.

Depois de criar uma subscrição para um utilizador, podem iniciar sessão no portal de utilizador e ver o que está subscrito para a oferta.  

### <a name="to-create-a-subscription-for-a-user"></a>Para criar uma subscrição para um utilizador

1. No portal de administração, aceda a **subscrições do utilizador.**
2. Selecione **Adicionar**. Sob **nova subscrição de utilizador**, introduza as seguintes informações:  

   - **Nome a apresentar** – um nome amigável para identificar a subscrição que aparece como o *nome da subscrição de utilizador*.
   - **Utilizador** – Especifique um utilizador de um inquilino do diretório disponível para esta subscrição. O nome de utilizador é apresentado como *proprietário*.  O formato do nome do utilizador depende da sua solução de identidade. Por exemplo:

     - **O Azure AD:** `<user1>@<contoso.onmicrosoft.com>`

     - **AD FS:** `<user1>@<azurestack.local>`

   - **Inquilino do diretório** – selecione o inquilino do diretório em que a conta de utilizador pertence. Se ainda não ativou a vários inquilinos, apenas o inquilino do diretório local está disponível.

3. Selecione **oferecem**. Sob **oferece**, escolha um **oferecer** para esta subscrição. Uma vez que está a criar a subscrição para um utilizador, selecione **privada** como o estado de acessibilidade.

4. Selecione **criar** para criar a subscrição. A nova subscrição é apresentado em **subscrição do utilizador**. Quando o utilizador inicia sessão portal de utilizador, podem ver os detalhes da subscrição.

### <a name="to-make-an-add-on-plan-available"></a>Para disponibilizar um plano de suplemento

Um operador da cloud pode adicionar um plano para uma subscrição criada anteriormente em qualquer altura:

1. No portal de administração, selecione **todos os serviços** e, em seguida, sob o **recursos administrativos** categoria, selecione **subscrições de utilizador**. Selecione a subscrição que pretende alterar.

2. Selecione **suplementos** e, em seguida, selecione **+ adicionar**.  

3. Sob **adicionar plano**, selecione o plano que pretende como um suplemento.

## <a name="create-a-subscription-as-a-user"></a>Criar uma subscrição como um utilizador

Como um utilizador, pode iniciar sessão portal de utilizador para localizar e subscrever de públicas ofertas e planos de suplementos para o seu inquilino do diretório (organização).

>[!NOTE]
>Se o seu ambiente do Azure Stack suporta [multi-inquilinos](azure-stack-enable-multitenancy.md), também pode subscrever as ofertas de um inquilino do diretório remoto.

### <a name="to-subscribe-to-an-offer"></a>Para subscrever uma oferta

1. [Iniciar sessão](azure-stack-connect-azure-stack.md) para o [portal de utilizador do Azure Stack](https://portal.local.azurestack.external) e selecione **obter uma subscrição**.

   ![Obter uma subscrição](media/azure-stack-subscribe-plan-provision-vm/image01.png)
  
2. Sob **obter uma subscrição**, introduza o nome amigável da subscrição no **nome a apresentar**. Selecione **oferecem** e, em **escolher uma oferta**, escolha uma oferta. Selecione **criar** para criar a subscrição.

   ![Criar uma oferta](media/azure-stack-subscribe-plan-provision-vm/image02.png)
  
3. Depois de subscrever uma oferta, atualize o portal para ver quais os serviços que fazem parte da nova subscrição.

4. Para ver a subscrição que criou, selecione **todos os serviços** e, em seguida, sob o **geral** selecionar categoria **subscrições**. Selecione a subscrição para ver os detalhes da subscrição.  

### <a name="to-subscribe-to-an-add-on-plan"></a>Para subscrever um plano de suplemento

Se uma oferta tem um plano de suplemento, pode adicionar esse plano à sua subscrição em qualquer altura.  

1. No portal de utilizador, selecione **todos os serviços**. Em seguida, sob o **gerais** categoria, selecione **subscrições**e, em seguida, selecione a subscrição que pretende que a alteração. Se existirem quaisquer planos de suplementos disponíveis, **+ adicionar plano** está ativa e existe um mosaico para **planos de suplementos**. 

   Se **+ adicionar plano** não está ativo, em seguida, existem não existem planos de suplementos para a oferta associada a essa subscrição.

1. Selecione **+ adicionar plano** ou o **planos de suplementos** mosaico. Sob **planos de suplementos**, selecione o plano que pretende adicionar.

## <a name="next-steps"></a>Passos Seguintes

- [Aprovisionar uma máquina virtual](azure-stack-provision-vm.md)
