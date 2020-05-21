---
title: Perguntas comuns sobre a migração para partner center - mercado comercial da Microsoft
description: Respostas a perguntas comumente feitas sobre a migração de ofertas do Cloud Partner Portal para partner Center.
author: anbene
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: mingshen
ms.openlocfilehash: c4fdde94ea703d194e2de27a8df429f62ea374f4
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83727670"
---
# <a name="common-questions-about-migrating-from-the-cloud-partner-portal-to-partner-center"></a>Perguntas comuns sobre a migração do Portal do Parceiro cloud para o Partner Center

Este artigo aborda comumente perguntas sobre a migração de ofertas do Portal do Parceiro cloud para o Partner Center.

## <a name="what-does-offer-migration-mean"></a>O que significa migração?

Estamos a mover os seus dados de oferta do Portal do Parceiro cloud para o Partner Center com alterações na experiência de publicação e gestão da oferta.

| Área  | Alterações  |
|-------|----------|
| **Experiência de gestão de publicações e ofertas** | Terá uma experiência de utilizador melhorada com uma interface intuitiva no Partner Center. Para mais detalhes, veja [Quais são as diferenças entre partner center e o Portal de Parceiros cloud?](#what-are-the-differences-between-partner-center-and-the-cloud-partner-portal) |
| **Disponibilidade das suas ofertas no mercado** | Sem alterações. Se a sua oferta estiver ao vivo no mercado, continuará a estar ao vivo durante e após a migração estar concluída. |
| **Novas compras e implementações** | Sem alterações. Os seus clientes continuarão a poder adquirir e implementar as suas ofertas sem interrupções. |
| **Pagamentos** | Quaisquer compras e implementações que ocorram durante ou após a migração continuarão a ser-lhe pagas normalmente. |
|**Integrações da API com as [APIs](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-api-overview) existentes do Portal do Parceiro de Nuvem** | As APIs existentes do Portal do Parceiro cloud continuarão a ser apoiadas após a migração e as suas integrações existentes continuarão a funcionar. Para mais detalhes, consulte será apoiado o Portal de [Parceiros de Nuvem AS APIs após a migração?](#will-the-cloud-partner-portal-rest-apis-be-supported-post-migration) |
| | |

## <a name="can-i-still-access-the-cloud-partner-portal-and-manage-my-offers-during-migration"></a>Ainda posso aceder ao Portal do Parceiro cloud e gerir as minhas ofertas durante a migração?

Todas as ofertas foram migradas para o Partner Center. O período de migração para cada oferta deveria ter sido inferior a 24 horas. Depois de a sua oferta ter sido migrada, deveria ter recebido uma notificação por e-mail.

Depois de as suas ofertas serem migradas, estarão em modo de leitura **apenas por um período limitado de tempo** no Portal do Parceiro cloud. O seu estatuto mostrará "Moved to Partner Center" e incluirá um link para a sua oferta no Partner Center, como mostram as seguintes imagens. A partir deste ponto, irá gerir atualizações para todas as suas ofertas ou criar novas ofertas exclusivamente através do Partner Center,

:::image type="content" source="media/migration-faq/all-offers-2.png" alt-text="Ilustra a mensagem dada para ofertas que foram migradas para o Partner Center":::

:::image type="content" source="media/migration-faq/offer-has-moved.png" alt-text="Ilustra a página cloud partner portal para uma oferta migrada.":::

## <a name="how-will-i-create-new-offers"></a>Como vou criar novas ofertas?

A partir do Portal do Parceiro cloud, você será direcionado para criar novas ofertas no Partner Center

:::image type="content" source="media/migration-faq/create-new-offer-1.png" alt-text="Ilustra o menu para criar uma nova oferta no Cloud Partner Portal":::

Depois de selecionar uma nova oferta, verá uma mensagem, como a seguinte.

:::image type="content" source="media/migration-faq/create-new-offer-2.png" alt-text="Ilustra a mensagem recebida ao criar uma nova oferta no CPP":::

## <a name="do-i-need-to-create-a-new-account-to-manage-offers-in-partner-center"></a>Preciso de criar uma nova conta para gerir ofertas no Partner Center?

Não. A sua conta subjacente será preservada, e já deve geri-la no Partner Center. Isto significa que, se for um parceiro existente, pode utilizar as credenciais existentes do Portal cloud partner para entrar no Partner Center pós-migração. Apenas ofertas e a experiência de gestão associada estão passando do Portal do Parceiro cloud para partner Center. Pedimos que não crie novas contas, uma vez que as suas ofertas não serão associadas à nova conta.

## <a name="i-see-a-message-in-the-cloud-partner-portal-to-activate-my-account-what-does-this-mean"></a>Vejo uma mensagem no Portal do Parceiro cloud para ativar a minha conta, o que significa isto?

Precisamos de mais alguns detalhes para migrar corretamente a sua conta para partner center e permitir-lhe gerir as suas ofertas no Partner Center após a migração da oferta estar completa. Para mais detalhes sobre a ativação da conta, consulte a [migração da Conta do Portal do Parceiro cloud para o Partner Center](https://docs.microsoft.com/azure/marketplace/partner-center-portal/account-migration-from-cpp-to-pc).

Os passos necessários para completar a ativação da conta variam em função da sua conta.

| Papel de conta | Etapas de ativação |
|--------------|----------------|
|Proprietário | Vá à página de Perfil da [Editora](https://cloudpartner.azure.com/#profile) no Portal do Parceiro cloud e, em seguida, clique no link no banner para ativar. Será redirecionado para o Partner Center para completar a ativação da conta. |
| Contribuinte | Apenas um utilizador na conta com uma função de proprietário pode ativar a conta. Contacte os proprietários da sua conta para completar a ativação da conta. Os donos da sua conta devem estar listados na mensagem do banner. |
| | |

## <a name="im-having-trouble-logging-in-to-my-account-and-opening-a-support-ticket"></a>Estou com problemas para entrar na minha conta e abrir um bilhete de apoio.

Se não conseguir fazer login na sua conta, pode abrir um bilhete de [apoio.](https://partner.microsoft.com/support/v2/?stage=1)

## <a name="where-can-i-find-documentation-on-the-new-partner-center-publishing-experience"></a>Onde posso encontrar documentação sobre a nova experiência de publicação do Partner Center?

Vá à documentação do [mercado comercial.](https://docs.microsoft.com/azure/marketplace/) Em seguida, expandir **o Portal do Mercado Comercial no Partner Center**   >  **Crie uma nova oferta** para ver os tópicos de ajuda para criar cada tipo de oferta.

:::image type="content" source="media/migration-faq/marketplace-help-topics.png" alt-text="Ilustra os tópicos de ajuda para o Partner Center":::

### <a name="what-are-the-differences-between-partner-center-and-the-cloud-partner-portal"></a>Quais são as diferenças entre o Partner Center e o Cloud Partner Portal?

Pode notar as seguintes diferenças entre o Portal do Parceiro cloud e o Partner Center.

### <a name="modular-publishing-capabilities"></a>Capacidades de publicação modulares

O Partner Center oferece uma opção de publicação modular que permite selecionar as alterações que pretende publicar em vez de publicar sempre todas as atualizações ao mesmo tempo. Por exemplo, a imagem a seguir mostra que as únicas alterações selecionadas para serem publicadas são as alterações para **Imóveis** e **Listade Oferta**.

:::image type="content" source="media/migration-faq/review-and-publish-migration.png" alt-text="Ilustra a página pré-visualização e publicação":::

As atualizações que não publica são guardadas como rascunhos. Continue a utilizar a sua pré-visualização para verificar a sua oferta antes de a tornar em direto ao público.

### <a name="rich-text-format"></a>Formato de texto rico

Melhore a sua oferta e a descrição do plano usando o Rich Text Editor na página de Listagem de Ofertas e Planos.

:::image type="content" source="media/migration-faq/rich-text-editor-migration.png" alt-text="Ilustra o rico editor de texto":::

### <a name="enhanced-preview-options"></a>Opções de pré-visualização melhoradas

O Partner Center inclui uma [funcionalidade de comparação](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#compare-changes-to-marketplace-offers) com opções de filtragem melhoradas. Isto dá-lhe a capacidade de comparar com a pré-visualização e versões ao vivo da oferta.

:::image type="content" source="media/migration-faq/enhanced-preview.png" alt-text="Ilustra a característica de comparação":::

### <a name="branding-and-navigation-changes"></a>Mudanças de marca e navegação

Vai notar algumas mudanças de marca. Por exemplo, "SKUs" são marcados como "Planos" no Partner Center.

:::image type="content" source="media/migration-faq/plan-overview-migration.png" alt-text="Ilustra a visão geral do plano.":::

Além disso, as informações que usou para fornecer nas páginas **Marketplace** ou S**torefront Details** (Consulting Service, Power BI app) no Portal do Parceiro cloud são recolhidas na página **de listagem de oferta** no Partner Center.

:::image type="content" source="media/migration-faq/offer-listing-migration.png" alt-text="Ilustra a página de listagem de ofertas.":::

As informações que usou para fornecer sKUs numa única página no Portal do Parceiro cloud podem agora ser recolhidas ao longo de várias páginas no Partner Center:

* Página de configuração do plano
* Página de listagem de planos
* Página de disponibilidade do plano
* Planeie a página de configuração técnica, como mostrado nesta imagem.

:::image type="content" source="media/migration-faq/tech-config-migration.png" alt-text="Ilustra a página de configuração técnica do Plano.":::

A sua oferta de identificação é agora mostrada na barra de navegação à esquerda da oferta.

:::image type="content" source="media/migration-faq/offer-id-offer-overview.png" alt-text="Ilustra o menu de navegação à esquerda com o ID da oferta.":::

### <a name="stop-selling-an-offer"></a>Pare de vender uma oferta

Pode solicitar para [parar de vender uma oferta](https://docs.microsoft.com/azure/marketplace/partner-center-portal/update-existing-offer#stop-selling-an-offer-or-plan) no mercado diretamente do portal Partner Center. A opção está disponível na página **de visão geral** da Oferta para a sua oferta.

:::image type="content" source="media/migration-faq/stop-selling-migration.png" alt-text="Ilustra a página de visão geral da Oferta com a opção stop selling.":::

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Que páginas no Partner Center correspondem às páginas que usei no Portal do Parceiro cloud?

A tabela seguinte mostra as ligações correspondentes entre os dois portais.

| Página | Ligação portal do parceiro de nuvem | Ligação do Partner Center |
|------|---------------------------|---------------------|
| **Página com todas as ofertas** | https://cloudpartner.azure.com/#alloffers | https://partner.microsoft.com/dashboard/commercial-marketplace/overview |
| **Página com todos os editores** | https://cloudpartner.azure.com/#publishers | https://partner.microsoft.com/dashboard/account/v3/publishers/list |
| **Perfil da editora** | https://cloudpartner.azure.com/#profile | https://partner.microsoft.com/dashboard/account/management |
| **Página de utilizadores** | https://cloudpartner.azure.com/#users | https://partner.microsoft.com/dashboard/account/usermanagement |
| **Página de história** | https://cloudpartner.azure.com/#history | A funcionalidade História ainda não é suportada no Partner Center. |
| **Painel de insights** | https://cloudpartner.azure.com/#insights | https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary |
| **Relatório de pagamento** | https://cloudpartner.azure.com/#insights/payout | https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| | |

## <a name="will-the-cloud-partner-portal-rest-apis-be-supported-post-migration"></a>O Portal de Parceiros de Nuvem REST APIs será apoiado após a migração?

As APIs do Portal do Parceiro cloud estão integradas no Partner Center e continuarão a trabalhar após as suas ofertas serem migradas para o Partner Center. A integração introduz pequenas alterações. Reveja as alterações na tabela abaixo para garantir que o seu código continua a funcionar após a migração para o Partner Center.

| **API** | **Alteração de descrição** | **Impacto** |
| ------- | ---------------------- | ---------- |
| POST Publicar, GoLive, Cancelar | Para ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o estado de funcionamento. | Ao enviar qualquer um dos pedidos post correspondentes para uma oferta, o cabeçalho de localização terá um de dois formatos dependendo do estado de migração da oferta:<ul><li>Ofertas não migradas<br>`/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas<br>`/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li> |
| OPERAÇÃO GET | Para ofertas que anteriormente suportadas no campo de "notificação-e-mail" na resposta, este campo será depreciado e não mais devolvido para ofertas migradas. | Para ofertas migradas, deixaremos de enviar notificações para a lista de e-mails especificados nos pedidos. Em vez disso, o serviço API irá alinhar-se com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações serão enviadas para o endereço de e-mail definido na secção de informações de contacto do Vendedor das definições da sua Conta no Partner Center, para notificá-lo do progresso da operação.<br><br>Reveja o endereço de e-mail definido na secção de informações de contacto do Vendedor nas [definições de Conta](https://partner.microsoft.com/dashboard/account/management) no Partner Center para garantir que o e-mail correto é fornecido para notificações.  |
| | | |
