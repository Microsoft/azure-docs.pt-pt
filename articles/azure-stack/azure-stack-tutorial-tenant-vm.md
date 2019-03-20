---
title: Tornar as máquinas virtuais disponíveis para os utilizadores do Azure Stack | Documentos da Microsoft
description: Saiba como disponibilizar as máquinas virtuais no Azure Stack
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 02/12/2019
ms.author: jeffgilb
ms.reviewer: unknown
ms.lastreviewed: 09/11/2018
ms.custom: mvc
ms.openlocfilehash: 34ef200ff96b098969c74937934d6178cfaf81ea
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58117137"
---
# <a name="tutorial-make-virtual-machines-available-to-your-azure-stack-users"></a>Tutorial: tornar as máquinas virtuais disponíveis para os utilizadores do Azure Stack

Como um administrador de nuvem do Azure Stack, pode criar ofertas que seus usuários (por vezes referidos como inquilinos) podem subscrever. Ao subscrever uma oferta, os utilizadores podem consumir os serviços do Azure Stack que fornece uma oferta.

Este tutorial mostra como criar uma oferta para uma máquina virtual e, em seguida, inicie sessão como um utilizador para testar a oferta.

O que irá aprender:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta

No Azure Stack, os serviços são entregues aos utilizadores com planos, ofertas e subscrições. Os utilizadores podem subscrever várias ofertas. Uma oferta pode ter um ou mais planos e um plano pode ter um ou mais serviços.

![Planos, ofertas e subscrições](media/azure-stack-key-features/image4.png)

Para obter mais informações, consulte [principais funcionalidades e conceitos no Azure Stack](azure-stack-key-features.md).

## <a name="create-an-offer"></a>Criar uma oferta

As ofertas são grupos de um ou mais planos que os fornecedores apresentam aos utilizadores para comprar ou subscrever. O processo de criação de uma oferta tem vários passos. Em primeiro lugar, lhe for pedido para criar a oferta, em seguida, um plano e, por fim, quotas.

1. [Iniciar sessão](azure-stack-connect-azure-stack.md) no portal como um administrador da nuvem e em seguida, selecione **+ criar um recurso** > **ofertas + planos** > **oferecem**.

   ![Nova oferta](media/azure-stack-tutorial-tenant-vm/image01.png)

1. Na **nova oferta**, introduza um **nome a apresentar** e **nome do recurso**e, em seguida, selecione uma nova ou existente **grupo de recursos**. O nome a apresentar é o nome amigável da oferta. O operador só de nuvem pode ver o nome do recurso que é o nome que o administrador utiliza para trabalhar com a oferta como um recurso do Azure Resource Manager.

   ![Nome a apresentar](media/azure-stack-tutorial-tenant-vm/image02.png)

1. Selecione **Planos Base**e, no **plano** secção, selecione **Add** para adicionar um novo plano à oferta.

   ![Adicionar um plano](media/azure-stack-tutorial-tenant-vm/image03.png)

1. Na **novo plano** secção, preencha **nome a apresentar** e **nome do recurso**. O nome a apresentar é o nome amigável do plano que os utilizadores veem. O operador só de nuvem pode ver o nome do recurso que é o nome que operadores da nuvem utilizam para trabalhar com o plano como um recurso do Azure Resource Manager.

   ![Nome a apresentar do plano](media/azure-stack-tutorial-tenant-vm/image04.png)

1. Selecione **serviços**. Na lista de serviços, escolha **Microsoft. Compute**, **Network**, e **Microsoft. Storage**. Escolher **selecione** para adicionar esses serviços para o plano.

   ![Plano de serviços](media/azure-stack-tutorial-tenant-vm/image05.png)

1. Selecione **Quotas**e, em seguida, selecione o primeiro serviço que pretende criar uma quota para. Para uma quota de IaaS, utilize o exemplo seguinte como guia para configurar quotas para os serviços de computação, rede e armazenamento.

   - Primeiro, crie uma quota para o serviço de computação. Na lista de espaço de nomes, selecione **Microsoft. Compute** e, em seguida, selecione **criar nova quota**.

     ![Criar nova quota](media/azure-stack-tutorial-tenant-vm/image06.png)

   - Na **Criar quota**, introduza um nome para a quota. Pode alterar ou aceitar qualquer um dos valores de quota que são exibidos para a quota que estiver a criar. Neste exemplo, vamos aceitar as predefinições e selecionar **OK**.

     ![Nome da quota](media/azure-stack-tutorial-tenant-vm/image07.png)

   - Escolher **Microsoft. Compute** na lista de espaço de nomes e, em seguida, selecione a quota que criou. Esta ação liga a quota para o serviço de computação.

     ![Selecione a quota](media/azure-stack-tutorial-tenant-vm/image08.png)

      Repita estes passos para os serviços de rede e armazenamento. Quando tiver terminado, selecione **OK** na **Quotas** para guardar todas as quotas.

1. Na **novo plano**, selecione **OK**.

1. Sob **plano**, selecione o novo plano e, em seguida **selecione**.

1. Na **nova oferta**, selecione **criar**. Verá uma notificação quando a oferta é criada.

1. No menu do dashboard, selecione **oferece** e, em seguida, escolha a oferta que criou.

1. Selecione **alterar estado**e, em seguida, selecione **público**.

    ![Estado público](media/azure-stack-tutorial-tenant-vm/image09.png)

## <a name="add-an-image"></a>Adicionar uma imagem

Pode aprovisionar máquinas virtuais, tem de adicionar uma imagem para o mercado do Azure Stack. É possível adicionar a imagem à sua escolha, incluindo imagens do Linux, do Azure Marketplace.

Se estiver a utilizar um cenário conectado e se tiver registado a sua instância do Azure Stack com o Azure, pode transferir a imagem de VM do Windows Server 2016 no Azure Marketplace utilizando os passos descritos no [marketplace de Download itens do Azure para o Azure Stack](azure-stack-download-azure-marketplace-item.md) tópico.

Para obter informações sobre como adicionar itens diferentes no Marketplace, consulte [o Azure Stack Marketplace](azure-stack-marketplace.md).

## <a name="test-the-offer"></a>Testar a oferta

Agora que criou uma oferta, pode testá-la. Vai iniciar sessão como um utilizador, subscreva a oferta e, em seguida, adicionar uma máquina virtual.

1. **Subscrever uma oferta**

   a. Inicie sessão portal de utilizador com uma conta de utilizador e selecione o **obter uma subscrição** mosaico.
   - Para um sistema integrado, o URL varia com base na região e o nome de domínio externo de sua operador e estará no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
   - Se estiver usando o Kit de desenvolvimento do Azure Stack, o endereço de portal é https://portal.local.azurestack.external.

   ![Obter uma subscrição](media/azure-stack-tutorial-tenant-vm/image10.png)

   b. Na **obter uma subscrição**, introduza um nome para a sua subscrição no **nome a apresentar** campo. Selecione **oferecem**e, em seguida, escolha uma das ofertas na **escolher uma oferta** lista. Selecione **Criar**.

   ![Criar uma oferta](media/azure-stack-tutorial-tenant-vm/image11.png)

   c. Para ver a subscrição, selecione **todos os serviços**e, em seguida, sob o **geral** selecionar categoria **subscrições**. Selecione a sua nova subscrição para ver quais os serviços que fazem parte da subscrição.

   >[!NOTE]
   >Depois de subscrever uma oferta, poderá ter de atualizar o portal para ver quais os serviços que fazem parte da nova subscrição.

1. **Aprovisionar uma máquina virtual**

   No portal de utilizador pode aprovisionar uma máquina virtual utilizando a nova subscrição.

   a. Inicie sessão no portal de utilizador com uma conta de utilizador.
   - Para um sistema integrado, o URL varia com base na região e o nome de domínio externo de sua operador e estará no formato https://portal.&lt; *região*&gt;.&lt; *FQDN*&gt;.
     - Se estiver usando o Kit de desenvolvimento do Azure Stack, o endereço de portal é https://portal.local.azurestack.external.

   b.  No dashboard, selecione **+ criar um recurso** > **computação** > **Windows Server 2016 Datacenter Eval**e, em seguida, selecione **Criar**.

   c. Na **Noções básicas**, forneça as seguintes informações:
      - Introduza um **nome**
      - Introduza um **nome de utilizador**
      - Introduza um **palavra-passe**
      - Escolha um **subscrição**
      - Criar uma **grupo de recursos** (ou selecione um existente.) 
      - Selecione **OK** para salvar essas informações.

   d. Na **escolher um tamanho**, selecione **A1 padrão**e, em seguida **selecionar**.  

   e. Na **configurações**, selecione **rede Virtual**.

   f. Na **escolher rede virtual**, selecione **criar nova**.

   g. Na **criar rede virtual**, aceite todas as predefinições e selecione **OK**.

   h. Selecione **OK** na **definições** para guardar a configuração de rede.

      i. Na **resumo**, selecione **OK** para criar a máquina virtual.  

   j. Para ver a nova máquina virtual, selecione **todos os recursos**. Procure a máquina virtual e selecione o respetivo nome resultados da pesquisa.

   
## <a name="next-steps"></a>Passos Seguintes

Neste tutorial, ficou a saber como:

> [!div class="checklist"]
> * Criar uma oferta
> * Adicionar uma imagem
> * Testar a oferta

Avance para o próximo tutorial para saber como:
> [!div class="nextstepaction"]
> [Disponibilizar bases de dados SQL para os utilizadores do Azure Stack](azure-stack-tutorial-sql-server.md)