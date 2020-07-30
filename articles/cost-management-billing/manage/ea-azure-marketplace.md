---
title: Azure Marketplace
description: Descreve como os clientes EA podem utilizar o Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.topic: conceptual
ms.date: 07/13/2020
ms.author: banders
ms.openlocfilehash: 27851ab4ba9e73c0c3aaea036ec2f0afc0d4378c
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/23/2020
ms.locfileid: "87039697"
---
# <a name="azure-marketplace"></a>Azure Marketplace

Este artigo explica como os clientes e parceiros EA podem ver os custos do marketplace e permitir compras no Azure Marketplace.

## <a name="azure-marketplace-for-ea-customers"></a>Azure Marketplace para clientes EA

Para os clientes diretos, os custos do Azure Marketplace estão visíveis no portal Azure Enterprise. As compras e o consumo do Azure Marketplace são faturados fora do Pré-pagamento do Azure numa cadência trimestral ou mensal e após o consumo efetivo.

Os clientes indiretos podem encontrar as respetivas subscrições do Azure Marketplace na página **Gerir Subscrições** do portal Azure Enterprise, mas os preços ficam ocultos. Os clientes devem entrar em contacto com o respetivo Fornecedor de Soluções de Licenciamento (LSP) para obter mais informações acerca dos custos do Azure Marketplace.

As novas compras mensais ou anuais recorrentes do Azure Marketplace são faturadas pelo valor total durante o período em que os itens do Azure Marketplace foram comprados. Estes itens serão renovados automaticamente no período seguinte, no mesmo dia da compra original.

Os custos mensais recorrentes que existam continuam a ser renovados no primeiro dia de cada mês de calendário. Os custos anuais continuam a ser renovados no aniversário da data de compra.

Alguns serviços de revenda de terceiros disponíveis no Azure Marketplace consomem agora o seu saldo do Pré-pagamento do Azure do Contrato Enterprise (EA). Anteriormente, estes serviços eram cobrados à margem do Pré-pagamento do Azure do EA e faturados separadamente. O Pré-pagamento do Azure do EA para estes serviços no Azure Marketplace ajuda a simplificar a gestão das compras e dos pagamentos dos clientes. Para obter uma lista completa dos serviços que agora consomem o Pré-pagamento do Azure, veja a [atualização de 6 de março de 2018 no site do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Parceiros

Os LSPs podem transferir uma lista de preços do Azure Marketplace a partir da página da folha de preços no portal Azure Enterprise. Selecione a ligação da **Lista de preço do Marketplace** no canto superior direito. A lista de preços do Azure Marketplace mostra todos os serviços disponíveis e os respetivos preços.

Para transfira a lista de preços:

1. No portal Azure Enterprise, aceda a **Relatórios** > **Folha de Preços**.
1. No canto superior direito, localize a ligação para a lista de preços do Azure Marketplace por baixo do nome de utilizador.
1. Clique com o botão direito do rato na ligação e selecione **Guardar Destino Como**.
1. Na janela **Guardar**, altere o título do documento para`AzureMarketplacePricelist.zip`, o que vai alterar o ficheiro de um .xlsx para um ficheiro .zip.
1. Após a transferência estar concluída, terá um ficheiro zip com as listas de preços específicas do país.
1. Os LSPs devem consultar o ficheiro do país específico para verem os preços específicos do país. Os LSPs podem utilizar o separador **Notificações** para estarem a par dos SKUs que são novos na rede ou que foram descontinuados.
1. As alterações de preços ocorrem com pouca frequência. Os LSPs recebem notificações por e-mail dos aumentos de preços e das alterações de câmbio internacional (FX) com 30 dias de antecedência.
1. Os LSPs recebem uma fatura por inscrição, por ISV, por trimestre.

### <a name="enabling-azure-marketplace-purchases"></a>Ativar as compras no Azure Marketplace

Os administradores do Enterprise podem desativar ou ativar as compras do Azure Marketplace para todas as subscrições do Azure na respetiva inscrição. Se o administrador do Enterprise desativar as compras e existirem subscrições do Azure que já têm subscrições do Azure Marketplace, essas subscrições do Azure Marketplace não serão canceladas nem afetadas.

Embora os clientes possam converter as suas subscrições diretas do Azure para o Azure EA ao associá-las à inscrição no portal Azure Enterprise, esta ação não converte automaticamente as subscrições subordinadas.

Para ativar compras do Azure Marketplace:

1. Inicie sessão no portal Azure Enterprise como administrador do Enterprise.
1. Aceda a **Gerir**.
1. Em **Detalhes da Inscrição**, selecione o ícone do lápis ao lado do item de linha **Azure Marketplace**.
1. Alterne entre **Ativado/Desativado** ou **Apenas SKUs Gratuitos/BYOL**, conforme apropriado.
1. Selecione **Guardar**.

> [!NOTE]
> A opção Apenas Gratuitos e BYOL (traga a sua própria licença) limita a compra e aquisição de SKUs do Azure Marketplace para apenas SKUs Gratuitos e BYOL.

### <a name="services-billed-hourly-for-azure-ea"></a>Serviços faturados à hora para o Azure EA

Os seguintes serviços são faturados à hora ao abrigo de um Contrato Enterprise em vez da tarifa mensal do MOSP:

- Rede de Entrega de Aplicações
- Firewall de Aplicação Web

### <a name="azure-remoteapp"></a>Azure RemoteApp

Se tiver um Contrato Enterprise, pagará o Azure RemoteApp com base no nível de preço do respetivo Contrato Enterprise. Não existem custos adicionais. O preço padrão inclui 40 horas iniciais. O preço ilimitado cobre 80 horas iniciais. O RemoteApp deixa de emitir a utilização superior a 80 horas.

## <a name="azure-marketplace-faq"></a>FAQ do Azure Marketplace

Esta secção explica como se pode aplicar o Pré-pagamento do Azure a alguns serviços de revenda de terceiros no Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-azure-ea-prepayment"></a>O que mudou nos serviços do Azure Marketplace e no Pré-pagamento do Azure do EA?

Desde 1 de março de 2018 que alguns serviços de revenda de terceiros consomem o Pré-pagamento do Azure do EA. À exceção das instâncias de VM reservadas (RIs) do Azure, os serviços eram previamente faturados fora do Pré-pagamento do Azure do EA e possuíam uma faturação à parte.

Alargámos a utilização do Pré-pagamento do Azure para incluir alguns dos serviços do Azure Marketplace publicados por terceiros que são comprados com mais frequência. O Pré-pagamento do Azure do EA para estes serviços no Azure Marketplace ajuda a simplificar a gestão das compras e dos pagamentos.

### <a name="why-did-we-make-this-change"></a>Por que motivo fizemos esta alteração?

Os clientes estão continuamente à procura de outras formas de tirar partido do pagamento antecipado do Pré-pagamento do Azure. Esta alteração foi frequentemente pedida pelos clientes e teve impacto numa grande parte dos clientes do Azure Marketplace.

### <a name="how-do-you-benefit"></a>Como beneficia?

Obtém uma experiência de faturação mais simples e pode gastar o Pré-pagamento do Azure do EA de forma mais adequada. Como estes serviços estão incluídos no Pré-pagamento do Azure, o seu Pré-pagamento do Azure do EA torna-se mais útil.

### <a name="what-azure-marketplace-services-use-azure-ea-prepayment-and-how-do-i-know"></a>Que serviços do Azure Marketplace utilizam o Pré-pagamento do Azure do EA e como posso saber?

Ao comprar um serviço que utiliza o Pré-pagamento do Azure, o Azure Marketplace apresenta uma exclusão de responsabilidade. São suportados alguns serviços publicados pelo Red Hat, SUSE, Autodesk e Oracle. Atualmente, os serviços com nomes semelhantes publicados por outras entidades não são deduzidos no Pré-pagamento do Azure. No fim desta FAQ, está disponível uma lista completa.

### <a name="what-if-my-azure-ea-prepayment-runs-out"></a>E se o meu Pré-pagamento do Azure do EA acabar?

Se consumir a totalidade do Pré-pagamento do Azure e entrar em utilização excedida, os custos relacionados com estes serviços serão apresentados na próxima fatura de utilização excedida, juntamente com quaisquer outros serviços de consumo. Antes da alteração de 1 de março de 2018, estes custos eram faturados com outros serviços do Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-azure-ea-prepayment"></a>Por que razão nem todos os serviços do Azure Marketplace consomem o Pré-pagamento do Azure do EA?

Trabalhamos frequentemente para garantir a melhor experiência para o cliente no que respeita o Pré-pagamento do Azure do EA. Esta alteração aplicar-se a um grande número de clientes e a uma parte significativa do total de gastos no Azure Marketplace. No futuro, podem ser adicionados outros serviços.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>De que forma é que isto afeta os parceiros e clientes de inscrição indireta?

Não há qualquer impacto para os nossos parceiros ou clientes de inscrição indireta. Estes serviços estão sujeitos às mesmas margens de lucro de parceiros do que outros serviços de consumo. A única alteração é que os custos são apresentamos numa fatura diferente e o pagamento dos custos é debitado no Pré-pagamento do Azure do EA do cliente.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-azure-ea-prepayment"></a>Existe uma lista dos serviços do Azure Marketplace que consomem o Pré-pagamento do Azure do EA?

As ofertas específicas do Azure Marketplace podem utilizar fundos do Pré-pagamento do Azure. Veja os [serviços de terceiros que utilizam o Pré-pagamento do Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment) para obter uma lista completa dos produtos que participam neste programa.


## <a name="next-steps"></a>Passos seguintes

- Obtenha mais informações sobre [Preços](ea-pricing-overview.md).