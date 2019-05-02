---
title: Informações técnicas para uma oferta de aplicação do Power BI | O Azure Marketplace
description: Configure os campos de informações técnicas para uma oferta de aplicação do Power BI para o Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 15f4e2a76724a70c15411dea767cc9bc433e4d4a
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943222"
---
# <a name="power-bi-apps-technical-info-tab"></a>Separador de informações técnicas de aplicações de BI de energia

Sobre o **nova oferta** página, utilize o **informações técnicas** separador para fornecer o instalador do Power BI, o URL do pacote e outras informações que precisa validar a nova oferta.  Na versão inicial, todas as aplicações do Power BI são gratuitas e estão disponíveis para download do AppSource. Por este motivo, não é possível definir a unidades de manutenção de estoque (SKU) para este tipo de oferta.

![O separador de informações técnicas](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campos de informações técnicos 

Sobre o **informações técnicas** separador, preencha os campos descritos na tabela seguinte. Um asterisco (*) no final de uma etiqueta de campo significa que o campo é obrigatório.

|        Campo          |  Descrição                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL de instalador\***     | Power BI gera este URL quando publicar a aplicação e promovê-lo para produção.  Para obter mais informações, consulte [publicar aplicações com dashboards e relatórios no Power BI](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instruções de validação**  |  Se quiser, adicione instruções (até 3.000 carateres) para ajudar a equipe de validação da Microsoft, configurar, ligar e testar seu aplicativo. Inclua definições de configuração típica, contas, parâmetros ou outras informações que podem ser utilizadas para testar a opção de ligar a dados. Estas informações são visíveis apenas para a equipe de validação e é utilizado apenas para fins de validação.  |
| **Esta aplicação é criada como um pacote de conteúdos do Power BI?** | Atualmente, este campo é utilizado apenas internamente. Deixe a predefinição de **não**. Se alterar a definição para **Sim**, foi possível parar o processo de publicação.  |  
|  |  |


## <a name="next-steps"></a>Passos Seguintes

Sobre o [detalhes de loja](./cpp-storefront-details-tab.md) separador, forneça informações legais e de marketing para a sua aplicação.

