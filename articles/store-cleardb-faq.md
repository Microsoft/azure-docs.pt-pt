---
title: FAQ para bases de dados ClearDB MySql com Serviço de Aplicações Azure
description: Respostas a perguntas comuns sobre a utilização de bases de dados Do MySQL da ClearDB com o Azure App Service.
documentationcenter: php
services: mysql
author: sunbuild
manager: yochayk
tags: mysql
ms.service: multiple
ms.topic: article
ms.date: 10/27/2016
ms.author: sumuth
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: references_regions
ms.openlocfilehash: ced4e920936ad94c3d4140d51d5e7d4e867ee848
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "84196073"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>FAQ para bases de dados ClearDB MySql com Serviço de Aplicações Azure
Esta FAQ responde a perguntas comuns sobre a utilização e compra de bases de dados ClearDB MySQL para aplicações web Azure.

> [!IMPORTANT]
> A 13 de junho de 2018, a ClearDB transferiu clientes baseados no Azure atualmente faturados pela Microsoft para um modelo de faturação direta com a ClearDB. A informação neste artigo está agora desatualizada. Deixará de ser capaz de criar ou atualizar uma base de dados ClearDB criada em Azure.
>
> Para mais detalhes e próximos passos, consulte [alterações aos planos de serviço ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Que opções tenho para o MySQL em Azure?
Consulte [a ClearDB](https://w2.cleardb.net/) para obter as informações mais recentes sobre este serviço. A ClearDB é um serviço de hospedagem MySQL e gere a infraestrutura MySQL para si. 

Você tem várias outras opções para hospedar MySQL em Azure:
* [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql/)
* [Cluster MySQL em execução em um VM Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Instância única do MySQL a correr num Azure VM](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Preciso de um cartão de crédito para a aplicação Web + modelo MySQL no Azure Marketplace?
Isto depende do tipo de subscrição que está a utilizar. Aqui estão alguns tipos de subscrição comumente usados:

* [Pague como você vai](https://azure.microsoft.com/offers/ms-azr-0003p/): Requer um cartão de crédito, e quando você compra uma base de dados MySQL paga o seu cartão de crédito é cobrado.
* [Teste gratuito](https://azure.microsoft.com/pricing/free-trial/): Inclui créditos para uso com os serviços microsoft Azure, mas não permite a compra de recursos de terceiros. Para adquirir serviços de terceiros ou uma base de dados MySQL paga, é necessário utilizar uma subscrição ativada por um cartão de crédito. Para aplicações web, pode criar uma base de dados MySQL Free ClearDB.
* [Subscrição da MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) e **MSDN Dev Test Pay**à medida que vai: Semelhante ao teste gratuito, uma subscrição da MSDN requer que você tenha um cartão de crédito para comprar uma solução MySQL paga à ClearDB.
* [Acordo de Empresa (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): Os clientes da EA são faturados contra os seus EA a cada trimestre para todas as suas compras Azure Marketplace (terceiros) numa fatura separada e consolidada. Você está fora do compromisso monetário para qualquer compra de mercado. Por favor, note que, neste momento, a Azure Store não está disponível para clientes matriculados no Azerbaijão, Croácia, Noruega e Porto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Porque me cobraram $3,50 por uma aplicação Web + MySQL do Azure Marketplace?
A opção de base de dados padrão é Titan, que é $3,50. Não mostramos o custo durante a criação da base de dados, e pode por engano comprar uma base de dados que não pretendia. Estamos a tentar encontrar uma maneira de melhorar a experiência, mas até lá tem de verificar todos os seus níveis de preços selecionados para aplicações web e base de dados antes de clicar em **Criar** e iniciar a implementação dos recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Estou a executar o MySQL na minha própria máquina virtual Azure. Posso ligar o meu aplicativo Azure à minha base de dados?
Sim. Pode ligar a sua aplicação web à sua base de dados desde que o seu VM Azure tenha dado acesso remoto à sua aplicação web. Para obter mais informações, consulte [instalar o MySQL numa máquina virtual.](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Em que países/regiões são apoiados os clusters MySQL premium Da ClearDB?
Os clusters MySQL Premium Da ClearDB estão disponíveis em todas as regiões do Azure em todo o mundo, com exceção da Índia, Austrália, Brasil Sul e China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Posso criar um novo cluster antes de criar uma base de dados com solução de cluster premium ClearDB?
Não, a criação de aglomerados ClearDB vazios não é suportada. O portal Azure permite criar bases de dados num cluster, o que pode criar um novo cluster ao mesmo tempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Serei avisado se tentar apagar uma base de dados ClearDB MySQL que está a ser utilizada por uma das minhas aplicações?
Não, o Azure não o avisará se apagar uma compra de mercado da que a sua aplicação depende.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Em que regiões posso criar bases de dados ClearDB?
O Azure Marketplace não está disponível para clientes matriculados no Azerbaijão, Croácia, Noruega ou Porto Rico. A ClearDB não está disponível nestas regiões.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Que nível de preços devo escolher para uma aplicação web de produção e base de dados?
Use Basic ou um nível de preços mais elevado para aplicações web. Para a ClearDB, recomendamos um plano de Saturno ou Júpiter. Reveja as funcionalidades & limitações de cada nível de preços tanto para [as web apps](https://azure.microsoft.com/pricing/details/app-service/) como para as bases de [dados Do MySQL da ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) para escolher a que se adequa às suas necessidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Como atualizo a minha base de dados ClearDB de um plano para outro?
No [portal Azure,](https://portal.azure.com)pode escalar uma base de dados de hospedagem partilhada da ClearDB. Leia este [artigo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para saber mais. Atualmente, não apoiamos upgrade para clusters ClearDB Premium no portal Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Não consigo ver a minha base de dados ClearDB no portal Azure?
Se criou uma base de dados ClearDB em clássico, não poderá ver a sua base de dados no [portal Azure](https://portal.azure.com). Não há trabalho para este cenário.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Quem contacto para apoio quando a minha base de dados está em baixo?
Contacte [o suporte da ClearDB](https://www.cleardb.com/developers/help/support) para quaisquer questões relacionadas com a base de dados. Esteja preparado para fornecer-lhes as informações de subscrição do Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Posso criar utilizadores adicionais para a minha solução de cluster de base de dados ClearDB MySQL?
N.º Não é possível criar utilizadores adicionais, mas pode criar bases de dados adicionais no seu cluster de base de dados ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>As bases de dados básicas/pro podem ser atualizadas no local semelhantes aos planos planetários de hoje no portal ClearDB?
Sim, as bases de dados de séries básicas podem ser atualizadas no local (Basic 60 a Basic 500). A série Pro pode ser atualizada no local (Pro 125 através pro 1000) exceto no Pro 60. Não apoiamos atualmente a atualização da base de dados Pro 60. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando migrar os meus recursos de uma subscrição para outra, a minha base de dados ClearDB MySQL também é migrada?
Quando realiza a migração de recursos através de subscrições, [algumas limitações](azure-resource-manager/management/move-support-resources.md) se aplicam. Uma base de dados ClearDB MySQL é um serviço de terceiros e, portanto, não é migrado durante a migração de subscrição do Azure. Se não gerir a migração da sua base de dados MySQL antes de migrar recursos Azure, as suas bases de dados ClearDB MySQL podem ser desativadas. Migrar manualmente as suas bases de dados primeiro e, em seguida, realizar a migração de subscrição Azure para a sua aplicação web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Atingi o limite de gastos na minha assinatura. Removi o limite e o meu Serviço de Aplicações está online, no entanto a base de dados não está acessível. Como posso reequipá-la na base de dados ClearDB?
Contacte [o suporte clearDB](https://www.cleardb.com/developers/help/support) para voltar a ativar a base de dados. Forneça-lhes as informações de subscrição do Azure e o nome da base de dados.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Posso transferir uma base de dados ClearDB de uma subscrição de cartão de crédito para uma subscrição da EA?
As bases de dados ClearDB existentes utilizam o cartão de crédito associado às subscrições existentes. Para utilizar uma subscrição da EA é necessário migrar os seus dados para uma nova base de dados:

* Compre uma nova base de dados ClearDB com a sua assinatura EA.
* Migrar os seus dados para a sua nova base de dados.
* Atualize a sua aplicação para utilizar a nova base de dados.
* Elimine a sua antiga base de dados ClearDB.

Quando cria uma nova aplicação web com o MySQL (ClearDB) ou cria uma base de dados MySQL (ClearDB), a subscrição que escolhe determina como vai pagar pelo serviço. Com uma subscrição da EA, não bloquearemos a aquisição de serviços de terceiros, como o ClearDB no portal Azure. As assinaturas da EA são faturadas fora do Compromisso Monetário e são faturadas trimestralmente e em atraso. O cliente da EA teria de criar um método de pagamento, como um cartão de crédito, para pagar quaisquer serviços de mercado de terceiros.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Onde posso ver os encargos com os recursos da ClearDB numa subscrição da EA?
Para os clientes diretos da EA, os encargos do Azure Marketplace são visíveis no Portal da Empresa. Note que todas as compras e consumos do mercado são faturados fora do Compromisso Monetário e são cobrados trimestralmente e em atraso. Os clientes da EA têm de pagar diretamente aos prestadores de serviços de terceiros e podem fazê-lo, permitindo um método de pagamento, como um cartão de crédito com a sua conta EA.

Os clientes indiretos da EA podem encontrar as suas subscrições Azure Marketplace na página de Subscrições de **Gestão** do Portal da Empresa, mas os preços estão escondidos. Os clientes devem entrar em contacto com o seu LSP para obter informações sobre os custos do Marketplace.

O acesso ao Azure Marketplace para serviços de terceiros pode ser gerido pelos seus administradores de inscrição da EA Azure. Podem desativar ou reativar o acesso a compras de terceiros na Loja em Contas e Assinaturas geridas ao abrigo da secção Contas no Portal da Empresa.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Quem é que eu contacto para perguntas sobre a minha conta para os serviços ClearDB na minha assinatura EA?
Contacte o Apoio ao [Cliente da Empresa](https://aka.ms/AzureEntSupport) no que diz respeito à faturação ao abrigo da sua inscrição na EA. A Equipa de Suporte do Portal da EA responderá à sua pergunta ou ajudará a resolver o seu problema.

## <a name="more-information"></a>Mais informações
[FAQ do Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)

