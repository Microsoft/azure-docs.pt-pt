---
title: Neste artigo, irá aprender a atualizar os planos e ofertas do Azure Stack | Documentos da Microsoft
description: Este artigo descreve como ver e modificar as ofertas do Azure Stack e planos existentes.
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
ms.topic: conceptual
ms.custom: mvc
ms.date: 03/07/2019
ms.author: sethm
ms.reviewer: efemmano
ms.lastreviewed: 03/07/2019
ms.openlocfilehash: 00bb17eadfee32e9b0d006ac76bb8e1cd614f13e
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/12/2019
ms.locfileid: "57780393"
---
# <a name="azure-stack-add-on-plans"></a>Planos de suplementos do Azure Stack

Como um operador do Azure Stack, criar planos de suplementos para modificar uma [plano base](azure-stack-create-plan.md) quando pretender oferecer serviços adicionais ou expandir *computador*, *armazenamento*, ou *rede* quotas para além da oferta inicial do plano base. Planos de suplementos modificar o plano de base e são as extensões opcionais que os utilizadores podem optar por subscrever.

Existem ocasiões combinar tudo num único plano é ideal. Outras vezes poderá ter uma base de planos e ofertas, em seguida, os serviços adicionais ao utilizar planos de suplementos. Por exemplo, poderia optar por oferecer serviços IaaS como parte de um plano base, com todos os serviços de PaaS tratados como planos de suplementos.

É outro motivo para usar os planos de suplementos para o ajudar a monitorizar a utilização de recursos. Para fazer isso, pode começar com um plano base que inclui quotas relativamente pequenas (consoante os serviços necessários). Em seguida, como utilizadores atingem a capacidade, eles seriam ser alertados de que eles consumiu a alocação de recursos com base no respetivo plano atribuído. A partir daí, os usuários possam selecionar um plano de suplemento que fornece os recursos adicionais.

> [!NOTE]
> Quando não pretender utilizar um plano de suplemento para expandir uma quota, também pode optar por [editar a configuração original da quota de](azure-stack-quota-types.md#edit-a-quota).

Quando adiciona um plano de suplemento a uma subscrição de oferta existente, os recursos adicionais podem demorar até uma hora são apresentados.

Planos de suplementos são criados ao modificar uma oferta existente.

## <a name="create-an-add-on-plan-1902-and-later"></a>Criar um plano de suplemento (1902 e posterior)

1. Inicie sessão no portal de administrador do Azure Stack como um administrador da nuvem.
2. Siga os passos utilizados para [criar um novo plano base](azure-stack-create-plan.md) para criar um novo plano de oferta de serviços que não foram oferecidos anteriormente.
3. No portal do administrador, clique em **oferece** e, em seguida, selecione a oferta para ser atualizado com um plano de suplemento.

   ![Criar plano de suplemento](media/create-add-on-plan/add-on1.png)

4. Desloque para baixo das propriedades da oferta e selecione **planos de suplementos**. Clique em **Adicionar**.

    ![Criar plano de suplemento](media/create-add-on-plan/add-on2.png)

5. Selecione o plano para adicionar. Neste exemplo, o plano é designado **20 storageaccounts**. Depois de selecionar o plano, clique em **selecione** para adicionar o plano à oferta. Deverá receber uma notificação que o plano foi adicionado com êxito para a oferta.

    ![Criar plano de suplemento](media/create-add-on-plan/add-on3.png)

6. Reveja a lista de planos de suplementos incluídos com a oferta para verificar se o novo plano de suplemento está listado.

    [![Criar plano de suplemento](media/create-add-on-plan/add-on4.png "criar plano de suplemento")](media/create-add-on-plan/add-on4lg.png#lightbox)

## <a name="create-an-add-on-plan-1901-and-earlier"></a>Criar um plano de suplemento (1901 e anterior)

1. Inicie sessão no portal de administrador do Azure Stack como um administrador da nuvem.
2. Siga os passos utilizados para [criar um novo plano base](azure-stack-create-plan.md) para criar um novo plano de oferta de serviços que não foram oferecidos anteriormente. Neste exemplo, o Cofre de chaves (**Microsoft. keyvault**) serviços serão incluídos no novo plano.
3. No portal do administrador, clique em **oferece** e, em seguida, selecione a oferta para ser atualizado com um plano de suplemento.

   ![Criar plano de suplemento](media/create-add-on-plan/1.PNG)

4. Desloque para baixo das propriedades da oferta e selecione **planos de suplementos**. Clique em **Adicionar**.

    ![Criar plano de suplemento](media/create-add-on-plan/2.PNG)

5. Selecione o plano para adicionar. Neste exemplo, o plano é designado **plano do Cofre de chave**. Depois de selecionar o plano, clique em **selecione** para adicionar o plano à oferta. Deverá receber uma notificação que o plano foi adicionado com êxito para a oferta.

    ![Criar plano de suplemento](media/create-add-on-plan/3.PNG)

6. Reveja a lista de planos de suplementos incluídos com a oferta para verificar se o novo plano de suplemento está listado.

    ![Criar plano de suplemento](media/create-add-on-plan/4.PNG)

## <a name="next-steps"></a>Passos Seguintes

* [Criar uma oferta](azure-stack-create-offer.md)