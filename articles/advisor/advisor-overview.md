---
title: Introdução ao Assistente do Azure | Documentos da Microsoft
description: Utilize o Assistente do Azure para otimizar as suas implementações do Azure.
services: advisor
documentationcenter: NA
author: kasparks
ms.service: advisor
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/01/2019
ms.author: kasparks
ms.openlocfilehash: 2ccac3bf9a882dc021c6c969946ad9d439a7cf5d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "67069692"
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Assistente do Azure

Saiba mais sobre as principais funcionalidades do Assistente do Azure e obtenha respostas para perguntas freqüentes.

## <a name="what-is-advisor"></a>O que é o Advisor?
O assistente é um consultor da cloud personalizado que ajuda a seguir as melhores práticas para otimizar as suas implementações do Azure. Ele analisa a sua telemetria de configuração e utilização de recursos e, em seguida, recomenda soluções que o podem ajudar a melhorar a rentabilidade, o desempenho, a elevada disponibilidade e a segurança dos seus recursos do Azure.

Com o assistente, pode:
* Obtenha proativa e acionáveis e melhores práticas recomendadas. 
* Melhorar o desempenho, segurança e elevada disponibilidade dos seus recursos, à medida que identificar oportunidades para reduzir o seu Azure geral de gastos.
* Obtenha recomendações com ações proposto inline.

Pode acessar o Advisor através da [portal do Azure](https://aka.ms/azureadvisordashboard). Entrar para o [portal](https://portal.azure.com), localize **Advisor** no menu de navegação ou procure-na **todos os serviços** menu.

O dashboard do assistente apresenta recomendações personalizadas para todas as suas subscrições.  Pode aplicar filtros para apresentar as recomendações para as subscrições específicas e tipos de recursos.  As recomendações estão divididas em quatro categorias: 

* **Elevada disponibilidade**: Certifique-se e melhorar a continuidade das suas aplicações críticas para a empresa. Para obter mais informações, consulte [recomendações de elevada disponibilidade do Advisor](advisor-high-availability-recommendations.md).
* **Segurança**: para detetar ameaças e vulnerabilidades que podem dar origem a falhas de segurança. Para obter mais informações, consulte [recomendações de segurança do Advisor](advisor-security-recommendations.md).
* **Desempenho**: para melhorar a velocidade das suas aplicações. Para obter mais informações, consulte [recomendações de desempenho do Advisor](advisor-performance-recommendations.md).
* **Custo**: para otimizar e reduzir os gastos gerais do Azure. Para obter mais informações, consulte [recomendações de custos do Advisor](advisor-cost-recommendations.md).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-dashboard.png)

Pode clicar numa categoria para apresentar a lista de recomendações dentro dessa categoria e selecione uma recomendação para saber mais sobre ele.  Também pode aprender sobre as ações que podem ser executados para tirar partido de uma oportunidade ou resolver um problema.

![Categoria de recomendação do Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Selecione a ação recomendada para uma recomendação implementar a recomendação.  Uma interface simples será aberto que permite-lhe implementar a recomendação ou consulte a documentação que ajuda a implementação.  Depois de implementar uma recomendação, ele pode demorar até um dia para o assistente reconhecer que.

Se não pretender tomar medidas imediatas numa recomendação, pode adiá-lo por um período de tempo especificado ou descartá-lo.  Se não quiser receber recomendações para uma subscrição específica ou grupo de recursos, pode configurar o Assistente para gerar apenas recomendações para subscrições especificadas e grupos de recursos.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-access-advisor"></a>Como posso aceder Advisor?
Pode acessar o Advisor através da [portal do Azure](https://aka.ms/azureadvisordashboard). Entrar para o [portal](https://portal.azure.com), localize **Advisor** no menu de navegação ou procure-na **todos os serviços** menu.

Também pode ver as recomendações do assistente através da interface de recurso de máquina virtual. Escolha uma máquina virtual e, em seguida, desloque-se para as recomendações do assistente no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Que permissões preciso para aceder ao Advisor?
 
Pode acessar as recomendações do assistente como *proprietário*, *contribuinte*, ou *leitor* de uma subscrição.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Que recursos o assistente fornece recomendações para?

O assistente fornece recomendações para o Gateway de aplicação, serviços de aplicações, conjuntos de disponibilidade, a Cache do Azure, Azure Data Factory, base de dados do Azure para MySQL, base de dados do Azure para PostgreSQL, a base de dados do Azure para MariaDB, o Azure ExpressRoute, o Azure Cosmos DB, público do Azure Endereços IP, o SQL Data Warehouse, servidores SQL, contas de armazenamento, perfis do Gestor de tráfego e máquinas virtuais.

O Assistente do Azure também inclui as suas recomendações partir [Centro de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations) que pode incluir recomendações para tipos de recursos adicionais.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Pode adiar ou ignorar uma recomendação?

Para adiar ou ignorar uma recomendação, clique a **atrasar** ligação. Pode especificar um ' Adiar ' período ou selecione **Never** para dispensar a recomendação.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre as recomendações do assistente, consulte:

* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de elevada disponibilidade do Assistente](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Assistente](advisor-performance-recommendations.md)
* [Recomendações de custos do Assistente](advisor-cost-recommendations.md)
