---
title: FAQ sobre as bases de dados do MySql ClearDB com Serviço de Aplicações do Azure
description: Respostas para perguntas comuns sobre como usar bancos de dados MySQL ClearDB com Azure App serviço.
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
ms.openlocfilehash: ddbf1eb960a24cfaa8d09ab45b6febca07e6d504
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979917"
---
# <a name="faq-for-cleardb-mysql-databases-with-azure-app-service"></a>FAQ sobre as bases de dados do MySql ClearDB com Serviço de Aplicações do Azure
Estas perguntas frequentes respondem a perguntas comuns sobre como usar e comprar bancos de dados MySQL ClearDB para aplicativos Web do Azure.

> [!IMPORTANT]
> A partir de 13 de junho de 2018, os clientes baseados no Azure em transição são cobrados pela Microsoft em um modelo de cobrança direto com ClearDB. As informações neste artigo agora estão desatualizadas. Você não poderá mais criar ou atualizar um banco de dados ClearDB criado no Azure.
>
> Para obter mais detalhes e próximas etapas, consulte [alterações nos planos de serviço de ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/).

## <a name="what-options-do-i-have-for-mysql-on-azure"></a>Quais são as opções que tenho para MySQL no Azure?
Consulte [ClearDB](https://w2.cleardb.net/) para obter as informações mais recentes sobre esse serviço. ClearDB é um serviço de hospedagem do MySQL e gerencia a infraestrutura do MySQL para você. 

Você tem várias outras opções para hospedar o MySQL no Azure:
* [Base de Dados do Azure para MySQL](https://azure.microsoft.com/services/mysql/)
* [Cluster MySQL em execução em uma VM do Azure](https://github.com/azure/azure-quickstart-templates/tree/master/mysql-replication)
* [Instância única do MySQL em execução em uma VM do Azure](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json)


## <a name="do-i-need-a-credit-card-for-the-web-app--mysql-template-in-the-azure-marketplace"></a>Preciso de um cartão de crédito para o modelo de aplicativo Web + MySQL no Azure Marketplace?
Isso depende do tipo de assinatura que você está usando. Aqui estão alguns tipos de assinatura comumente usados:

* Pré- [pago](https://azure.microsoft.com/offers/ms-azr-0003p/): requer um cartão de crédito e, quando você adquire um banco de dados MySQL pago, seu cartão de crédito é cobrado.
* [Avaliação gratuita](https://azure.microsoft.com/pricing/free-trial/): inclui créditos para uso com Microsoft Azure Services, mas não permite a compra de recursos de terceiros. Para adquirir serviços de terceiros ou um banco de dados MySQL pago, você precisa usar uma assinatura habilitada para cartão de crédito. Para aplicativos Web, você pode criar um banco de dados MySQL de ClearDB gratuito.
* [Assinatura do MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits/) e **teste de desenvolvimento do MSDN pago conforme o uso**: semelhante à avaliação gratuita, uma assinatura do MSDN exige que você tenha um cartão de crédito para comprar uma solução paga do MySQL do ClearDB.
* [Enterprise Agreement (ea)](https://azure.microsoft.com/pricing/enterprise-agreement/): os clientes do ea são cobrados em relação ao seu ea a cada trimestre para todas as suas compras do Azure Marketplace (de terceiros) em uma fatura consolidada separada. Você é cobrado fora do compromisso monetário de qualquer compra do Marketplace. Observe que, neste momento, a Azure Store não está disponível para clientes registrados no Azerbaijão, na Croácia, na Noruega e em Porto Rico. 

## <a name="why-was-i-charged-350-for-a-web-app--mysql-from-the-azure-marketplace"></a>Por que fui cobrado $3.50 por um aplicativo Web + MySQL do Azure Marketplace?
A opção de banco de dados padrão é o Titan, que é $3.50. Não mostramos o custo durante a criação do banco de dados e você pode, por engano, comprar um banco de dados que não pretendia. Estamos tentando encontrar uma maneira de melhorar a experiência, mas até lá, você deve verificar todos os tipos de preço selecionados para o aplicativo Web e o banco de dados antes de clicar em **criar** e iniciar a implantação dos recursos.

## <a name="i-am-running-mysql-on-my-own-azure-virtual-machine-can-i-connect-my-azure-web-app-to-my-database"></a>Estou executando o MySQL em minha própria máquina virtual do Azure. Posso conectar meu aplicativo Web do Azure ao meu banco de dados?
Sim. Você pode conectar seu aplicativo Web ao seu banco de dados, desde que sua VM do Azure tenha acesso remoto ao seu aplicativo Web. Para obter mais informações, consulte [instalar o MySQL em uma máquina virtual](virtual-machines/windows/classic/mysql-2008r2.md?toc=%2fazure%2fvirtual-machines%2fwindows%2fclassic%2ftoc.json).

## <a name="in-which-countriesregions-are-cleardb-premium-mysql-clusters-supported"></a>Em quais países/regiões os clusters MySQL do ClearDB Premium têm suporte?
Os clusters MySQL do ClearDB Premium estão disponíveis em todas as regiões do Azure em todo o mundo com exceção da Índia, da Austrália, do Sul do Brasil e da China.

## <a name="can-i-create-a-new-cluster-prior-to-creating-a-database-with-cleardb-premium-cluster-solution"></a>Posso criar um novo cluster antes de criar um banco de dados com a solução de cluster de ClearDB Premium?
Não, não há suporte para a criação de clusters ClearDB vazios. O portal do Azure permite que você crie bancos de dados em um cluster, o que pode criar um novo cluster ao mesmo tempo.

## <a name="will-i-be-warned-if-i-try-to-delete-a-cleardb-mysql-database-that-is-in-use-by-one-of-my-applications"></a>Serei avisado se eu tentar excluir um banco de dados MySQL ClearDB que está sendo usado por um dos meus aplicativos?
Não, o Azure não avisará se você excluir uma compra do Marketplace da qual seu aplicativo depende.

## <a name="which-regions-can-i-create-cleardb-databases-in"></a>Em quais regiões posso criar bancos de dados ClearDB?
O Azure Marketplace não está disponível para clientes registrados no Azerbaijão, na Croácia, na Noruega ou em Porto Rico. O ClearDB não está disponível nessas regiões.

## <a name="what-pricing-tier-should-i-choose-for-a-production-web-app-and-database"></a>Qual tipo de preço devo escolher para um aplicativo Web de produção e um banco de dados?
Use um tipo de preço básico ou mais alto para aplicativos Web. Para ClearDB, recomendamos um plano Saturn ou Júpiter. Examine os recursos & limitações de cada tipo de preço para [aplicativos Web](https://azure.microsoft.com/pricing/details/app-service/) e [bancos de dados MySQL ClearDB](https://w2.cleardb.net/important-change-of-billing-notice-for-all-azure-cleardb-service-plans/) para escolher aquele que atenda às suas necessidades.

## <a name="how-do-i-upgrade-my-cleardb-database-from-one-plan-to-another"></a>Como fazer atualizar meu banco de dados ClearDB de um plano para outro?
No [portal do Azure](https://portal.azure.com), você pode escalar verticalmente um banco de dados de hospedagem compartilhado ClearDB. Leia este [artigo](https://blogs.msdn.microsoft.com/appserviceteam/2016/10/06/upgrade-your-cleardb-mysql-database-in-azure-portal/) para saber mais. Atualmente, não há suporte para atualização para clusters do ClearDB Premium no portal do Azure.

## <a name="i-cant-see-my-cleardb-database-in-azure-portal"></a>Não consigo ver meu banco de dados ClearDB no portal do Azure?
Se você criou um banco de dados ClearDB em clássico, não poderá ver seu banco de dados no [portal do Azure](https://portal.azure.com). Não há solução alternativa para esse cenário.

## <a name="who-do-i-contact-for-support-when-my-database-is-down"></a>Quem devo contatar para obter suporte quando meu banco de dados estiver inoperante?
Contate o [suporte do ClearDB](https://www.cleardb.com/developers/help/support) para qualquer problema relacionado ao banco de dados. Esteja preparado para fornecer as informações de sua assinatura do Azure.

## <a name="can-i-create-additional-users-for-my-cleardb-mysql-database-cluster-solution"></a>Posso criar usuários adicionais para minha solução de cluster de banco de dados MySQL ClearDB?
Não. Você não pode criar usuários adicionais, mas pode criar bancos de dados adicionais em seu cluster de banco de dados ClearDB.  

## <a name="can-basicpro-series-databases-be-upgraded-in-place-similar-to-planetary-plans-today-on-cleardb-portal"></a>Os bancos de dados da série Basic/pro podem ser atualizados in-loco de forma semelhante aos planos do planetários hoje no portal ClearDB?
Sim, os bancos de dados de série básica podem ser atualizados in-loco (Basic 60 até Basic 500). A série Pro pode ser atualizada in-loco (pro 125 até pro 1000), exceto para pro 60. Não há suporte para atualizar o banco de dados pro 60 no momento. 

## <a name="when-i-migrate-my-resources-from-one-subscription-to-another-does-my-cleardb-mysql-database-get-migrated-as-well"></a>Quando eu migrar meus recursos de uma assinatura para outra, meu banco de dados MySQL ClearDB também será migrado?
Quando você executa a migração de recursos entre assinaturas, algumas [limitações](azure-resource-manager/management/move-support-resources.md) se aplicam. Um banco de dados MySQL ClearDB é um serviço de terceiros e, portanto, não é migrado durante a migração de assinatura do Azure. Se você não gerenciar a migração do banco de dados MySQL antes de migrar os recursos do Azure, seus bancos de dados MySQL ClearDB poderão ser desabilitados. Migre manualmente seus bancos de dados primeiro e, em seguida, execute a migração de assinatura do Azure para seu aplicativo Web. 

## <a name="i-hit-the-spending-limit-on-my-subscription-i-removed-the-limit-and-my-app-service-is-online-however-the-database-is-not-accessible-how-do-i-re-enable-the-cleardb-database"></a>Eu pressiono o limite de gastos em minha assinatura. Removi o limite e meu serviço de aplicativo está online, no entanto, o banco de dados não está acessível. Como fazer reabilitar o banco de dados ClearDB?
Contate o [suporte do ClearDB](https://www.cleardb.com/developers/help/support) para reabilitar o banco de dados. Forneça suas informações de assinatura do Azure e o nome do banco de dados.

## <a name="can-i-transfer-a-cleardb-database-from-a-credit-card-subscription-to-an-ea-subscription"></a>Posso transferir um banco de dados ClearDB de uma assinatura de cartão de crédito para uma assinatura EA?
Os bancos de dados ClearDB existentes usam o cartão de crédito associado às assinaturas existentes. Para usar uma assinatura do EA, você precisa migrar seus dados para um novo banco de dado:

* Compre um novo banco de dados ClearDB com sua assinatura do EA.
* Migre seus dados para o novo banco de dado.
* Atualize seu aplicativo para usar o novo banco de dados.
* Exclua seu banco de dados ClearDB antigo.

Quando você cria um novo aplicativo Web com o MySQL (ClearDB) ou cria um banco de dados MySQL (ClearDB), a assinatura escolhida determina como você pagará pelo serviço. Com uma assinatura do EA, não bloquearemos a aquisição dos serviços de terceiros, como o ClearDB no portal do Azure. As assinaturas de EA são cobradas fora do compromisso monetário e são cobradas trimestralmente e em pelos débitos anteriores. O cliente EA teria que configurar um método de pagamento, como um cartão de crédito, para pagar por qualquer serviço de Marketplace de terceiros.

## <a name="where-can-i-see-the-charges-for-cleardb-resources-in-an-ea-subscription"></a>Onde posso ver os encargos de recursos ClearDB em uma assinatura EA?
Para clientes do EA direto, os encargos do Azure Marketplace são visíveis no Enterprise Portal. Observe que todas as compras e consumo do Marketplace são cobrados fora do compromisso monetário e são cobrados trimestralmente e em pelos débitos anteriores. Os clientes do EA precisam pagar diretamente para os provedores de serviço de terceiros e podem fazê-lo habilitando um método de pagamento como um cartão de crédito com sua conta EA.

Clientes de EA indiretos podem encontrar suas assinaturas do Azure Marketplace na página **gerenciar assinaturas** do Enterprise Portal, mas os preços ficam ocultos. Os clientes devem entrar em contacto com o seu LSP para obter informações sobre os custos do Marketplace.

O acesso ao Azure Marketplace para serviços de terceiros pode ser gerenciado por seus administradores de registro do Azure EA. Eles podem desabilitar ou reabilitar o acesso a compras de terceiros na loja em gerenciar contas e assinaturas na seção contas do Enterprise Portal.

## <a name="who-do-i-contact-for-questions-about-my-bill-for-cleardb-services-in-my-ea-subscription"></a>Quem devo contatar para perguntas sobre minha fatura para serviços de ClearDB em minha assinatura do EA?
Entre em contato com o [suporte ao cliente corporativo](https://aka.ms/AzureEntSupport) com relação à cobrança sob seu registro de ea. A equipe de suporte do portal de EA responderá à sua pergunta ou ajudará a resolver o problema.

## <a name="more-information"></a>Mais informações
[Perguntas frequentes do Azure Marketplace](https://azure.microsoft.com/marketplace/faq/)

