---
title: Introdução ao Assistente do Azure
description: Utilize o Azure Advisor para otimizar as suas implementações Azure.
ms.topic: article
ms.date: 09/27/2020
ms.openlocfilehash: 12e56bf44a29a32b2149bca14f7c99f319c9c4ea
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91405212"
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Assistente do Azure

Conheça as capacidades-chave do Azure Advisor e obtenha respostas para perguntas frequentes.

## <a name="what-is-advisor"></a>O que é Conselheiro?
Advisor é um consultor de nuvem personalizado que o ajuda a seguir as melhores práticas para otimizar as suas implementações do Azure. Analisa a configuração do recurso e a telemetria de utilização e recomenda soluções que o podem ajudar a melhorar a eficácia de custos, desempenho, fiabilidade (anteriormente chamada alta disponibilidade) e segurança dos seus recursos Azure.

Com o Advisor, pode:
* Obter recomendações proativas, acionáveis e personalizadas de melhores práticas. 
* Melhore o desempenho, segurança e fiabilidade dos seus recursos, pois identifica oportunidades para reduzir o seu gasto global do Azure.
* Obter recomendações inline com propostas de ações.

Pode aceder ao Advisor através do [portal Azure.](https://aka.ms/azureadvisordashboard) Inscreva-se no [portal,](https://portal.azure.com) **localize** o Advisor no menu de navegação ou procure-o no menu **Todos os serviços.**

O painel Advisor apresenta recomendações personalizadas para todas as suas subscrições.  Pode aplicar filtros para apresentar recomendações para subscrições específicas e tipos de recursos.  As recomendações estão divididas em cinco categorias: 

* **Fiabilidade (anteriormente denominada Alta Disponibilidade)**: Para garantir e melhorar a continuidade das suas aplicações críticas ao negócio. Para mais informações, consulte [recomendações de Fiabilidade do Conselheiro](advisor-high-availability-recommendations.md).
* **Segurança**: Detetar ameaças e vulnerabilidades que possam levar a falhas de segurança. Para mais informações, consulte [recomendações de Segurança Do Conselheiro](advisor-security-recommendations.md).
* **Desempenho**: Para melhorar a velocidade das suas aplicações. Para mais informações, consulte [recomendações de Desempenho Do Conselheiro](advisor-performance-recommendations.md).
* **Custo**: Para otimizar e reduzir os seus gastos globais do Azure. Para mais informações, consulte [recomendações do Advisor Cost](advisor-cost-recommendations.md).
* **Excelência Operacional**: Para ajudá-lo a alcançar a eficiência do processo e do fluxo de trabalho, a gestão de recursos e as melhores práticas de implantação. . Para mais informações, consulte [recomendações de Excelência Operacional Advisor.](advisor-operational-excellence-recommendations.md)

  ![Tipos de recomendação de aconselhamento](./media/advisor-overview/advisor-dashboard.png)

Pode clicar numa categoria para mostrar a lista de recomendações dentro dessa categoria e selecionar uma recomendação para saber mais sobre isso.  Você também pode aprender sobre ações que você pode executar para aproveitar uma oportunidade ou resolver um problema.

![Categoria de recomendação de aconselhamento](./media/advisor-overview/advisor-ha-category-example.png) 

Selecione a ação recomendada para uma recomendação para implementar a recomendação.  Abrirá uma interface simples que lhe permite implementar a recomendação ou encaminhá-lo para documentação que o ajude na implementação.  Uma vez que implemente uma recomendação, pode levar até um dia para o Conselheiro reconhecer isso.

Se não pretender tomar medidas imediatas por recomendação, pode adiá-la por um período de tempo especificado ou descartá-la.  Se não quiser receber recomendações para uma subscrição específica ou grupo de recursos, pode configurar o Advisor apenas para gerar recomendações para subscrições especificadas e grupos de recursos.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-access-advisor"></a>Como posso aceder ao Conselheiro?
Pode aceder ao Advisor através do [portal Azure.](https://aka.ms/azureadvisordashboard) Inscreva-se no [portal,](https://portal.azure.com) **localize** o Advisor no menu de navegação ou procure-o no menu **Todos os serviços.**

Também pode ver recomendações do Advisor através da interface de recursos de máquina virtual. Escolha uma máquina virtual e, em seguida, percorra as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>Que permissões preciso para aceder ao Advisor?
 
Pode aceder às recomendações do Advisor como *Proprietário,* *Contribuinte*ou *Leitor* de uma subscrição, Grupo de Recursos ou Recursos.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para que recursos o Advisor fornece recomendações?

O Advisor fornece recomendações para Gateway de Aplicações, Serviços de Aplicações, conjuntos de disponibilidade, Azure Cache, Azure Data Factory, Azure Database for MySQL, Azure Database for PostgreSQL, Azure Database for MariaDB, Azure ExpressRoute, Azure Cosmos DB, Azure Public IP addresss, Azure Synapse Analytics, SQL servers, storage accounts, Traffic Managers,

O Azure Advisor também inclui as suas recomendações do [Azure Security Center,](../security-center/security-center-recommendations.md) que podem incluir recomendações para tipos de recursos adicionais.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Posso adiar ou rejeitar uma recomendação?

Para adiar ou rejeitar uma recomendação, clique no link **Adia.** Pode especificar um período de adiamento ou selecionar **Nunca** rejeitar a recomendação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do Advisor, consulte:

* [Introdução ao Assistente](advisor-get-started.md)
* [Pontuação do conselheiro](azure-advisor-score.md)
* [Recomendações de fiabilidade do conselheiro](advisor-high-availability-recommendations.md)
* [Recomendações de Segurança do Conselheiro](advisor-security-recommendations.md)
* [Recomendações de Desempenho do Conselheiro](advisor-performance-recommendations.md)
* [Recomendações de Custos Do Consultor](advisor-cost-recommendations.md)
* [Recomendações de excelência operacional do consultor](advisor-operational-excellence-recommendations.md)
