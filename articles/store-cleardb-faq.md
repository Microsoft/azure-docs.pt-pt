---
title: FAQ sobre as bases de dados do MySql ClearDB com Serviço de Aplicações do Azure
description: Respostas a perguntas comuns sobre a utilização de bases de dados ClearDB MySQL com serviço de aplicações do Azure.
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
ms.openlocfilehash: 1838dbb627a6dec46f817101fc1d0cca0961ef77
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64574199"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>FAQ sobre as bases de dados do MySql ClearDB com Serviço de Aplicações do Azure
Encontre respostas para perguntas comuns sobre o uso e a compra de bases de dados ClearDB MySQL para aplicações Web do Azure.

> [!IMPORTANT]
> A partir do dia 13 de Junho de 2018, ClearDB transferido os clientes baseados no Azure atualmente cobrados pela Microsoft para um modelo de faturação direto com a ClearDB. As informações neste artigo agora estão Desatualizadas. Já não será possível criar ou atualizar uma base de dados do ClearDB que foi criada no Azure.
>
> Para obter mais detalhes e os passos seguintes, veja [é alterado para planos de serviço do ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Que opções tenho para o MySQL no Azure?
Ver [ClearDB](https://w2.cleardb.net/) para as últimas informações sobre esse serviço. ClearDB é um serviço de alojamento de MySQL e gere a infraestrutura do MySQL para. 

Tem várias outras opções para alojar o MySQL no Azure:
* [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql/)
* [Cluster MySQL em execução numa VM do Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Instância única do MySQL em execução numa VM do Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>É necessário um cartão de crédito para a aplicação Web + MySQL modelo no Azure Marketplace?
Isso depende do tipo de subscrição que está a utilizar. Seguem-se alguns tipos de subscrição frequentemente utilizadas:

* [Pay as you Go](https://azure.microsoft.com/offers/ms-azr-0003p/): Requer um cartão de crédito e na compra de uma base de dados do MySQL paga que seu cartão de crédito é cobrado.
* [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/): Inclui os créditos para utilizar com os serviços do Microsoft Azure, mas não permite a compra de recursos de terceiros. Para comprar serviços de terceiros ou uma base de dados do MySQL paga que tem de utilizar uma subscrição de cartão de crédito ativada. Para aplicações Web, pode criar uma base de dados MySQL do ClearDB gratuito.
* [Assinatura do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) e **MSDN Dev Test Pay as you go**: Semelhante à versão de avaliação gratuita, uma assinatura do MSDN requer que tenha um cartão de crédito para comprar uma solução paga do MySQL da ClearDB.
* [Contrato Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/): Os clientes com EA são faturados relativamente a seu EA da cada trimestre para todas as suas compras de (de terceiros) no Azure Marketplace numa fatura separada, consolidada. É-lhe cobrada fora o compromisso monetário para qualquer compras do marketplace. Tenha em atenção que, neste momento, Store do Azure não está disponível para os clientes inscritos na Azerbaijão, Croácia, Noruega e Porto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Por que motivo fui cobrado us $3,50 para uma aplicação Web + MySQL no Azure Marketplace?
A opção de base de dados predefinida é Titan, o que é de US $3,50. Não foi possível exibir o custo durante a criação da base de dados e, por engano, pode comprar uma base de dados que não pretende. Estamos a tentar encontrar uma forma de melhorar a experiência, mas até essa data tem de verificar todos os seus selecionados escalões de preço para a aplicação web e a base de dados antes de clicar em **criar** e iniciar a implantação dos recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Eu estou executando o MySQL na minha própria máquina virtual do Azure. Pode ligar a minha aplicação web do Azure para o meu banco de dados?
Sim. Pode ligar a sua aplicação web à sua base de dados, desde que a VM do Azure tenha dado acesso remoto à sua aplicação web. Para obter mais informações, consulte [instalar o MySQL numa máquina virtual](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Em que países/regiões são suportadas de clusters do MySQL do ClearDB Premium?
Os clusters de MySQL do ClearDB Premium estão disponíveis em todas as regiões do Azure em todo o mundo com a exceção Índia, Austrália, sul do Brasil e China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Pode criar um novo cluster antes de criar uma base de dados com a solução de cluster do ClearDB premium?
Não, a criação de clusters de ClearDB vazios não é suportada. O portal do Azure permite-lhe criar bases de dados num cluster, que pode criar um novo cluster ao mesmo tempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Receberei ser se tentar eliminar uma base de dados ClearDB MySQL está a ser utilizado por uma das minhas aplicações?
Não, Azure não o irá avisar se eliminar uma compra do marketplace que seu aplicativo depende.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>As regiões que pode criar bases de dados do ClearDB no?
O Azure Marketplace não está disponível para clientes inscritos na Azerbaijão, Croácia, Noruega ou Porto Rico. O ClearDB não está disponível nestas regiões.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>O escalão de preço devo escolher para uma aplicação web de produção e a base de dados?
Utilize o básico ou de um escalão de preço mais elevado para aplicações Web. Para ClearDB, recomendamos que plano um Saturn ou Jupiter. Reveja as funcionalidades e limitações de cada escalão de preço para os dois [aplicações Web](https://azure.microsoft.com/pricing/details/app-service/) e [bases de dados MySQL do ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) escolher o que se adeque às suas necessidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Como posso atualizar meu banco de dados do ClearDB num plano para outra?
Na [portal do Azure](https://portal.azure.com), pode aumentar verticalmente um ClearDB alojamento base de dados partilhada. Leia este [artigo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para saber mais. Atualmente não suportamos a atualização para os clusters Premium de ClearDB no portal do Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Não consigo ver meu banco de dados do ClearDB no portal do Azure?
Se tiver criado uma base de dados do ClearDB no modelo clássico, não será capaz de ver a sua base de dados do [portal do Azure](https://portal.azure.com). Não existe nenhuma solução alternativa para este cenário.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Quem devo contactar para obter suporte quando meu banco de dados está inativa?
Contacte [suporte do ClearDB](https://www.cleardb.com/developers/help/support) para qualquer base de dados problemas relacionados. Esteja preparado para fornecê-los com as suas informações de subscrição do Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Pode criar utilizadores adicionais para a minha solução de cluster de base de dados ClearDB MySQL?
Não. Não é possível criar utilizadores adicionais, mas pode criar bases de dados adicionais no seu cluster de base de dados do ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Pode ser de bases de dados de série de Basic/Pro atualizados no local semelhante aos planos de planetários hoje mesmo no portal do ClearDB?
Sim, série básica bases de dados podem ser atualizados no local (60 básicos por meio de 500 básico). Série Pro pode ser atualizados no local (125 Pro através de Pro 1000), exceto para o Pro de 60. Não suportamos atualmente a atualizar a base de dados de Pro 60. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando migrar os meus recursos de uma subscrição para outro, meu banco de dados ClearDB MySQL é migrado também?
Quando efetua a migração de recursos entre subscrições, algumas [limitações](azure-resource-manager/resource-group-move-resources.md#app-service-limitations) aplicam-se. Uma base de dados ClearDB MySQL é um serviço de terceiros e, por conseguinte, não é migrado durante a migração de subscrição do Azure. Se não gerencia a migração da base de dados MySQL antes da migração de recursos do Azure, as bases de dados ClearDB MySQL podem ser desativados. Migrar manualmente as bases de dados pela primeira vez e, em seguida, efetuar a migração de subscrição do Azure para a sua aplicação web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Eu atingir o limite de gastos na minha subscrição. Removi o limite e meu serviço de aplicações está online, no entanto, a base de dados não está acessível. Como posso ativar novamente a base de dados do ClearDB?
Contacte [suporte do ClearDB](https://www.cleardb.com/developers/help/support) para voltar a ativar a base de dados. Disponibilize o seu nome de base de dados e de informações de subscrição do Azure.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Posso transferir uma base de dados do ClearDB de uma subscrição de cartão de crédito para uma subscrição de EA?
Bases de dados existentes do ClearDB, utilize o cartão de crédito associado às subscrições existentes. Para utilizar uma subscrição do EA terá de migrar os dados para uma nova base de dados:

* Compre uma nova base de dados do ClearDB com a sua subscrição EA.
* Migre os dados para a nova base de dados.
* Atualize a sua aplicação para utilizar a nova base de dados.
* Elimine a base de dados do ClearDB antigo.

Quando cria uma nova aplicação web com MySQL (ClearDB) ou criar uma base de dados do MySQL (ClearDB), a subscrição que escolher determina como irá pagar para o serviço. Com uma assinatura de EA, não podemos irá bloquear o aprovisionamento dos serviços de terceiros como o ClearDB no portal do Azure. As subscrições do EA são cobradas fora da alocação monetária e são cobradas são feitas trimestralmente após o consumo Efetivo. O cliente EA teria de configurar um método de pagamento, como um cartão de crédito para pagar quaisquer serviços do marketplace de terceiros.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Onde posso ver as cobranças do ClearDB recursos numa subscrição EA?
Para clientes com EA direto, os encargos do Azure Marketplace são visíveis no Enterprise Portal. Tenha em atenção que todas as compras do marketplace e consumo são faturados fora da alocação monetária e são cobradas são feitas trimestralmente após o consumo Efetivo. Os clientes com EA terá de pagar diretamente para os fornecedores de serviços de terceiros e podem fazer isso, permitindo que um método de pagamento, como um cartão de crédito com suas contas EA.

Os clientes com EA indiretos podem encontrar suas subscrições do Azure Marketplace sobre o **gerir subscrições** página do Portal da empresa, mas preços está oculto. Os clientes devem contactar o respetivo LSP para obter informações sobre os encargos do marketplace.

Acesso ao Azure Marketplace para serviços de terceiros pode ser gerido pelos seus administradores de inscrição do EA Azure. Eles podem desativar ou reativar o acesso para compras a terceiros 3º do Store em Gerir contas e subscrições na secção de contas no Portal da empresa.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Quem devo contactar para perguntas sobre minha fatura para serviços do ClearDB em minha assinatura do EA?
Contacte [atendimento ao cliente Enterprise](https://aka.ms/AzureEntSupport) com respeito a faturação na sua inscrição EA. A equipa de suporte do Portal de EA irá responder à sua pergunta ou ajudar a resolver seu problema.

## <a name="more-information"></a>Mais informações
[FAQ do Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)

