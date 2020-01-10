---
title: Introdução ao Azure Advisor
description: Use o Azure Advisor para otimizar as implantações do Azure.
ms.topic: article
ms.date: 02/01/2019
ms.openlocfilehash: 600bda282d46f86979d0366719826c3a6c1323e0
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75443083"
---
# <a name="introduction-to-azure-advisor"></a>Introdução ao Azure Advisor

Saiba mais sobre os principais recursos do Azure Advisor e obtenha respostas para perguntas frequentes.

## <a name="what-is-advisor"></a>O que é o Advisor?
O Advisor é um consultor de nuvem personalizado que ajuda você a seguir as práticas recomendadas para otimizar suas implantações do Azure. Ele analisa a sua telemetria de configuração e utilização de recursos e, em seguida, recomenda soluções que o podem ajudar a melhorar a rentabilidade, o desempenho, a elevada disponibilidade e a segurança dos seus recursos do Azure.

Com o Advisor, você pode:
* Obter recomendações proativas, acionáveis e personalizadas de melhores práticas. 
* Melhore o desempenho, a segurança e a alta disponibilidade de seus recursos, conforme você identifica oportunidades para reduzir o gasto geral do Azure.
* Obter recomendações inline com propostas de ações.

Você pode acessar o Advisor por meio do [portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [portal](https://portal.azure.com), localize o **Advisor** no menu de navegação ou procure-o no menu **todos os serviços** .

O painel do Advisor exibe recomendações personalizadas para todas as suas assinaturas.  Você pode aplicar filtros para exibir recomendações para assinaturas e tipos de recursos específicos.  As recomendações estão divididas em quatro categorias: 

* **Alta disponibilidade**: para garantir e melhorar a continuidade dos seus aplicativos críticos para os negócios. Para obter mais informações, consulte [recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md).
* **Segurança**: para detectar ameaças e vulnerabilidades que podem levar a violações de segurança. Para obter mais informações, consulte [recomendações de segurança do Advisor](advisor-security-recommendations.md).
* **Desempenho**: para melhorar a velocidade de seus aplicativos. Para obter mais informações, consulte [recomendações de desempenho do Advisor](advisor-performance-recommendations.md).
* **Custo**: para otimizar e reduzir os gastos gerais do Azure. Para obter mais informações, consulte [recomendações de custo do Advisor](advisor-cost-recommendations.md).
* **Excelência operacional**: para ajudá-lo a obter eficiência de processo e fluxo de trabalho, gerenciamento de recursos e práticas recomendadas de implantação. . Para obter mais informações, consulte [recomendações de excelência operacional do Advisor](advisor-operational-excellence-recommendations.md).

  ![Tipos de recomendação do Advisor](./media/advisor-overview/advisor-dashboard.png)

Você pode clicar em uma categoria para exibir a lista de recomendações dentro dessa categoria e selecionar uma recomendação para saber mais sobre ela.  Você também pode aprender sobre as ações que podem ser executadas para aproveitar uma oportunidade ou resolver um problema.

![Categoria de recomendação do Advisor](./media/advisor-overview/advisor-ha-category-example.png) 

Selecione a ação recomendada para uma recomendação para implementar a recomendação.  Uma interface simples será aberta, permitindo que você implemente a recomendação ou consulte a documentação que auxilia na implementação.  Depois de implementar uma recomendação, pode levar até um dia para o Advisor reconhecê-la.

Se você não pretende executar uma ação imediata em uma recomendação, você pode adiar isso por um período de tempo especificado ou ignorá-lo.  Se não quiser receber recomendações para uma assinatura ou grupo de recursos específico, você poderá configurar o Advisor para gerar apenas recomendações para assinaturas e grupos de recursos especificados.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

### <a name="how-do-i-access-advisor"></a>Como posso aceder ao Assistente?
Você pode acessar o Advisor por meio do [portal do Azure](https://aka.ms/azureadvisordashboard). Entre no [portal](https://portal.azure.com), localize o **Advisor** no menu de navegação ou procure-o no menu **todos os serviços** .

Você também pode exibir as recomendações do Advisor por meio da interface de recurso da máquina virtual. Escolha uma máquina virtual e role até as recomendações do Advisor no menu. 

### <a name="what-permissions-do-i-need-to-access-advisor"></a>De que permissões preciso para aceder ao Assistente?
 
Você pode acessar as recomendações do Advisor como *proprietário*, *colaborador*ou *leitor* de uma assinatura.

### <a name="what-resources-does-advisor-provide-recommendations-for"></a>Para quais recursos o Advisor fornece recomendações?

O Advisor fornece recomendações para gateway de aplicativo, serviços de aplicativos, conjuntos de disponibilidade, cache do Azure, Azure Data Factory, banco de dados do Azure para MySQL, banco de dados do Azure para PostgreSQL, banco de dados do Azure para MariaDB, Azure ExpressRoute, Azure Cosmos DB, público do Azure Endereços IP, SQL Data Warehouse, SQL Servers, contas de armazenamento, perfis do Gerenciador de tráfego e máquinas virtuais.

O Azure Advisor também inclui suas recomendações da [central de segurança do Azure](https://docs.microsoft.com/azure/security-center/security-center-recommendations) , que podem incluir recomendações para tipos de recursos adicionais.

### <a name="can-i-postpone-or-dismiss-a-recommendation"></a>Posso adiar ou ignorar uma recomendação?

Para adiar ou descartar uma recomendação, clique no link **adiar** . Você pode especificar um período adiado ou selecionar **nunca** para ignorar a recomendação.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre as recomendações do Advisor, consulte:

* [Introdução ao Assistente](advisor-get-started.md)
* [Recomendações de alta disponibilidade do Advisor](advisor-high-availability-recommendations.md)
* [Recomendações de segurança do Advisor](advisor-security-recommendations.md)
* [Recomendações de desempenho do Advisor](advisor-performance-recommendations.md)
* [Recomendações de custo do Advisor](advisor-cost-recommendations.md)
