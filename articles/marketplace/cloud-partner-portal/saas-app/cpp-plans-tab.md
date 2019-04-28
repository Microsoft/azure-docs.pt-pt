---
title: Planos de oferta de aplicação SaaS do Azure | Documentos da Microsoft
description: Crie um plano para uma oferta de aplicação SaaS no Azure Marketplace.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/04/2018
ms.author: pbutlerm
ms.openlocfilehash: 0394fa759f4b6f6af59e075d9bc67668886d8075
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62120210"
---
# <a name="saas-application-plans-tab"></a>Separador de planos de aplicação SaaS

Utilize o separador de planos para criar um novo plano. Tem de ser adicionado, pelo menos, 1 plano se estiver a utilizar a origem de venda por meio da opção da Microsoft para a sua aplicação SaaS.

![Criar um novo plano](./media/saas-plans-new-plan.png)

## <a name="create-a-new-plan"></a>Criar um novo plano

Para criar um novo plano:

1. Sob **planos**, selecione **+ novo plano**
2. Na **novo plano** janela de pop-up, introduza um **ID de plano**. O comprimento máximo é de 50 carateres. Este ID deve consistir apenas em carateres em minúsculas, de alfanuméricos, travessões e carateres de sublinhado. Não é possível alterar este ID após a oferta de publicação do.
3. Selecione **OK** para guardar o plano de ID.

   ![ID do plano para o novo plano](./media/saas-plans-plan-ID.png)

### <a name="to-configure-the-plan"></a>Para configurar o plano:

1. Sob **detalhes do plano**, fornecer informações para os seguintes campos:

   - Título – introduza um título para o plano. O título está limitado a 50 carateres.
   - Descrição - forneça uma descrição. A descrição está limitada a até 500 carateres.
   - Este é um plano privado? – Se o plano apenas 's disponível para um grupo de clientes, selecione **Sim**.
   - Disponibilidade do país/região - o plano têm de estar disponível para, pelo menos, 1 país ou região. Clique em **selecionar regiões**. Escolher um país/região a partir da **disponibilidade de país/região, selecione** lista e, em seguida, selecione **OK**. 
   - Preços legado - fornecer o custo, em USD por mês.

2. Sob **preços de moeda simplificado**, forneça as seguintes informações:

   - Período de faturação - preços mensais está selecionada por predefinição. Também pode fornecer preços anual.
   - Preço mensal – forneça o preço mensal, o que tem de coincidir com os preços de legado.

   >[!NOTE] 
   >Se adicionar anual de preço para o período de faturação, obter-lhe-á pedido para o **preço anual** em USD por ano.

3. Selecione **guardar** para concluir a configuração do plano.

   >[!NOTE] 
   >Depois de guardar as alterações de preços que pode exportar/importar dados de preços.

![Configurar detalhes do plano](./media/saas-plans-plan-details.png)

## <a name="next-steps"></a>Passos Seguintes

[Separador Informações de Canal](./cpp-channel-info-tab.md)

