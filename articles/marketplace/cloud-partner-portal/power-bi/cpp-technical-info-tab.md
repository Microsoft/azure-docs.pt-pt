---
title: Informações técnicas para uma oferta de aplicativo Power BI | Azure Marketplace
description: Configure os campos de informações técnicas para uma oferta de aplicativo Power BI para o Microsoft AppSource Marketplace.
services: Azure, AppSource, Marketplace, Cloud Partner Portal, Power BI
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/24/2019
ms.author: pabutler
ms.openlocfilehash: 02c98e38f26fff73d02dc4c2b838ad3428cd2996
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819036"
---
# <a name="power-bi-apps-technical-info-tab"></a>Guia informações técnicas de aplicativos Power BI

Na página **nova oferta** , use a guia **informações técnicas** para fornecer o Power bi URL do pacote do instalador e outras informações necessárias para validar a nova oferta.  Para a versão inicial, todos os aplicativos Power BI são gratuitos e estão disponíveis para download em AppSource. Por isso, você não pode definir as unidades de manutenção de estoque (SKUs) para esse tipo de oferta.

![A guia informações técnicas](./media/technical-info-tab.png)


## <a name="technical-info-fields"></a>Campos de informações técnicas 

Na guia **informações técnicas** , preencha os campos descritos na tabela a seguir. Um asterisco (*) no final de um rótulo de campo significa que o campo é obrigatório.

|        Campo          |  Descrição                                                                 |
|    ---------------    |  ----------------------------------------------------------------------------|
| **URL do instalador\***     | Power BI gera essa URL quando você publica o aplicativo e o promove para produção.  Para obter mais informações, consulte [publicar aplicativos com dashboards e relatórios no Power bi](https://docs.microsoft.com/power-bi/service-create-distribute-apps).  |
|  **Instruções de validação**  |  Se desejar, adicione instruções (até 3.000 caracteres) para ajudar a equipe de validação da Microsoft a configurar, conectar e testar seu aplicativo. Inclua definições de configuração típicas, contas, parâmetros ou outras informações que podem ser usadas para testar a opção conectar dados. Essas informações são visíveis somente para a equipe de validação e são usadas somente para fins de validação.  |
| **Este aplicativo foi criado como um pacote de conteúdo Power BI?** | Atualmente, esse campo é usado somente internamente. Deixe a configuração padrão **não**. Se você alterar a configuração para **Sim**, poderá parar o processo de publicação.  |  
|  |  |


## <a name="next-steps"></a>Passos seguintes

Na guia [detalhes da vitrine](./cpp-storefront-details-tab.md) , forneça informações legais e de marketing para seu aplicativo.

