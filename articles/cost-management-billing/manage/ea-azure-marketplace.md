---
title: Azure Marketplace
description: Descreve como os clientes EA podem utilizar o Azure Marketplace
author: bandersmsft
ms.reviewer: baolcsva
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: conceptual
ms.date: 09/03/2020
ms.author: banders
ms.openlocfilehash: ce9dff017a796e420586ad191a59c149bed07190
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/19/2021
ms.locfileid: "107726831"
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

## <a name="next-steps"></a>Passos seguintes

- Obtenha mais informações sobre [Preços](ea-pricing-overview.md).
- Leia a [Cost Management + Billing FAQ](../cost-management-billing-faq.yml) para ver uma lista de perguntas e respostas sobre os serviços Azure Marketplace e pré-pagamento Azure EA.