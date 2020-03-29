---
title: Introdução ao Consultor Azure
description: Utilize o Azure Advisor para otimizar as suas implementações Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "75443083"
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Consultor Azure

Conheça as principais capacidades do Azure Advisor e obtenha respostas para perguntas frequentes.

## <a name="what-is-advisor"></a>O que é conselheiro?
O Advisor é um consultor personalizado de nuvem que o ajuda a seguir as melhores práticas para otimizar as suas implementações azure. Ele analisa a sua telemetria de configuração e utilização de recursos e, em seguida, recomenda soluções que o podem ajudar a melhorar a rentabilidade, o desempenho, a elevada disponibilidade e a segurança dos seus recursos do Azure.

Com o Advisor, pode:
* Obter recomendações proativas, acionáveis e personalizadas de melhores práticas. 
* Melhore o desempenho, segurança e elevada disponibilidade dos seus recursos, pois identifica oportunidades para reduzir os gastos globais do Azure.
* Obter recomendações inline com propostas de ações.

Pode aceder ao Advisor através do [portal Azure.](https://aka.ms/azureadvisordashboard) Inscreva-se no [portal,](https://portal.azure.com)localize o **Advisor** no menu de navegação ou procure-o no menu **Todos os serviços.**

O dashboard Advisor apresenta recomendações personalizadas para todas as suas subscrições.  Pode aplicar filtros para apresentar recomendações para subscrições específicas e tipos de recursos.  As recomendações estão divididas em quatro categorias: 

* **Alta Disponibilidade**: Para garantir e melhorar a continuidade das suas aplicações críticas ao negócio. Para mais informações, consulte [as recomendações](advisor-high-availability-recommendations.md)de Alta Disponibilidade do Advisor.
* **Segurança**: Para detetar ameaças e vulnerabilidades que possam levar a falhas de segurança. Para mais informações, consulte [as recomendações](advisor-security-recommendations.md)do Advisor Security .
* **Desempenho**: Para melhorar a velocidade das suas aplicações. Para mais informações, consulte [as recomendações](advisor-performance-recommendations.md)do Advisor Performance .
* **Custo**: Para otimizar e reduzir os gastos globais do Azure. Para mais informações, consulte as recomendações do [Advisor Cost.](advisor-cost-recommendations.md)
* **Excelência Operacional**: Para ajudá-lo a alcançar a eficiência do processo e do fluxo de trabalho, a gestão de recursos e as melhores práticas de implantação. . Para mais informações, consulte recomendações de [Excelência Operacional Consultiva.](advisor-operational-excellence-recommendations.md)

  ![Tipos de recomendação de aconselhamento](./media/advisor-overview/advisor-dashboard.png)

Pode clicar numa categoria para apresentar a lista de recomendações dentro dessa categoria e selecionar uma recomendação para saber mais sobre o mesmo.  Também pode aprender sobre ações que pode realizar para aproveitar uma oportunidade ou resolver um problema.

![Categoria de recomendação de conselheiro](./media/advisor-overview/advisor-ha-category-example.png) 

Selecione a ação recomendada para uma recomendação para implementar a recomendação.  Uma interface simples será aberta que lhe permite implementar a recomendação ou encaminhá-lo para documentação que o ajude na implementação.  Uma vez implementada uma recomendação, pode levar até um dia para o Advisor reconhecer isso.

Se não pretender tomar medidas imediatas por recomendação, pode adiá-la por um período de tempo determinado ou descartá-la.  Se não quiser receber recomendações para uma subscrição ou grupo de recursos específicos, pode configurar o Advisor apenas para gerar recomendações para subscrições e grupos de recursos especificados.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-access-advisor"></a>Como posso aceder ao Advisor?
Pode aceder ao Advisor através do [portal Azure.](https://aka.ms/azureadvisordashboard) Inscreva-se no [portal,](https://portal.azure.com)localize o **Advisor** no menu de navegação ou procure-o no menu **Todos os serviços.**

Também pode ver recomendações do Advisor através da interface de recursos de máquina virtual. Escolha uma máquina virtual e, em seguida, percorra as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Que permissões preciso para aceder ao Conselheiro?
 
Pode aceder às recomendações do Advisor como *Proprietário,* *Colaborador*ou *Leitor* de uma subscrição.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para que recursos o Advisor fornece recomendações?

Advisor fornece recomendações para Application Gateway, App Services, conjuntos de disponibilidade, Azure Cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, Azure public Endereços IP, Armazém de Dados SQL, servidores SQL, contas de armazenamento, perfis do Gestor de Tráfego e máquinas virtuais.

O Azure Advisor também inclui as suas recomendações do [Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations) que podem incluir recomendações para tipos de recursos adicionais.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Posso adiar ou rejeitar uma recomendação?

Para adiar ou rejeitar uma recomendação, clique no link **Adiar.** Pode especificar um período de adiamento ou selecionar **Nunca** rejeitar a recomendação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre recomendações do Advisor, consulte:

* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de Alta Disponibilidade Do Conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do conselheiro](advisor-security-recommendations.md)
* [Recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Custos Consultivos](advisor-cost-recommendations.md)
