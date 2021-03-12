---
title: Perguntas frequentes sobre a transição do Portal de Parceiros cloud para Partner Center - Mercado comercial da Microsoft
description: Respostas a perguntas comumente sobre ofertas de transição do Cloud Partner Portal para Partner Center.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: mingshen-ms
ms.author: mingshen
ms.date: 07/14/2020
ms.openlocfilehash: e071692a1c6fe423b048dab884164d9b3c98f1fd
ms.sourcegitcommit: d135e9a267fe26fbb5be98d2b5fd4327d355fe97
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102613617"
---
# <a name="frequently-asked-questions-about-transitioning-from-the-cloud-partner-portal-to-partner-center"></a>Perguntas frequentes sobre a transição do Portal do Parceiro Cloud para o Centro de Parceiros

O Portal do Parceiro Cloud passou para o Partner Center. O Partner Center oferece uma experiência simplificada e integrada para a publicação de novas ofertas no [Microsoft AppSource](https://appsource.microsoft.com/) ou [no Azure Marketplace](https://azuremarketplace.microsoft.com/) e para gerir as ofertas existentes que foram migradas do Portal dos Parceiros cloud. Todas as funções do Portal do Parceiro cloud estão disponíveis no Partner Center. Este artigo aborda perguntas comumente sobre este assunto.

## <a name="what-does-the-transition-to-partner-center-mean-for-me"></a>O que significa a transição para o Centro de Parceiros para mim?

Pode continuar a fazer negócios no Partner Center:

| Área | Alterações |
| --- | --- |
| Conta | Não há necessidade de criar uma nova conta partner Center; pode utilizar as credenciais existentes do Cloud Partner Portal para iniciar sessão no Partner Center, onde irá agora gerir a sua conta, utilizadores, permissões e faturação. O contrato de publicação e as informações de perfil da empresa são migrados para a sua nova conta Partner Center, juntamente com qualquer informação de perfil de pagamento, contas e permissões de utilizador e ofertas ativas. Saiba mais na [Manage your commercial marketplace account in Partner Center](partner-center-portal/manage-account.md). |
| Oferecer experiência de publicação e gestão de ofertas | Mudamos os dados da sua oferta do Portal do Parceiro Cloud para o Partner Center. Agora terá acesso às suas ofertas no Partner Center, que oferece uma melhor experiência de utilizador e interface intuitiva. Saiba como [atualizar uma oferta existente no mercado comercial.](partner-center-portal/update-existing-offer.md) |
| Disponibilidade das suas ofertas no mercado comercial | Sem alterações. Se a sua oferta for ao vivo no mercado comercial, continuará a ser ao vivo. |
| Novas compras e implementações | Sem alterações. Os seus clientes podem continuar a comprar e implementar as suas ofertas sem interrupções. |
| Pagamentos | Quaisquer compras e implementações continuarão a ser pagos normalmente. Saiba mais sobre [ser pago no mercado comercial.](/partner-center/marketplace-get-paid?context=/azure/marketplace/context/context) |
| Integrações da API com apIs do [Portal do Parceiro cloud existentes](cloud-partner-portal-api-overview.md) | As APIs do Portal do Parceiro Cloud existentes ainda são suportadas e as suas integrações existentes ainda funcionam. Saiba mais no [Portal do Parceiro Cloud REST APIs será suportado?](#are-the-cloud-partner-portal-rest-apis-still-supported) |
| Análise | Pode continuar a monitorizar as vendas, avaliar o desempenho e otimizar as suas ofertas no mercado comercial, visualizando análises no Partner Center. Existem diferenças entre a forma como os relatórios de análise são exibidos no CPP e no Partner Center. Por exemplo, **o Seller Insights** em CPP tem um **separador De utilização & encomendas** que exibe dados para ofertas baseadas em uso e ofertas não baseadas em uso, enquanto no Centro parceiro a página **Encomendas** tem um separado separado para Ofertas SaaS. Saiba mais na [Access analítica reportagens para o mercado comercial no Partner Center.](partner-center-portal/analytics.md) |
|||

## <a name="do-i-need-to-create-a-new-account-to-manage-my-offers-in-partner-center"></a>Preciso de criar uma nova conta para gerir as minhas ofertas no Partner Center?

Não, a sua conta será preservada. Isto significa que, se for um parceiro existente, pode utilizar as credenciais de conta do Cloud Partner para iniciar singing no Partner Center. Não crie uma nova conta ou quaisquer ofertas criadas no Portal do Parceiro Cloud e transferidas para o Partner Center não serão associadas a ela.

## <a name="what-pages-in-partner-center-correspond-to-pages-i-used-in-the-cloud-partner-portal"></a>Que páginas no Partner Center correspondem às páginas que usei no Portal do Parceiro Cloud?

Seguem-se os links partner Center para páginas comumente utilizadas no Portal do Parceiro cloud. Se guardou as ligações do Portal do Parceiro Cloud como marcadores, irá querer atualizá-las.

| Página do Portal do Parceiro de Nuvem | Link de página do Portal do Parceiro de Nuvem | Link de página do Centro parceiro |
| --- | --- | --- |
| Página com todas as ofertas | [https://cloudpartner.azure.com/#alloffers](https://cloudpartner.azure.com/#alloffers) | [https://partner.microsoft.com/dashboard/commercial-marketplace/overview](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) |
| Página com todos os editores | [https://cloudpartner.azure.com/#publishers](https://cloudpartner.azure.com/#publishers) | [https://partner.microsoft.com/dashboard/account/v3/publishers/list](https://partner.microsoft.com/dashboard/account/v3/publishers/list) |
| Perfil de editor | [https://cloudpartner.azure.com/#profile](https://cloudpartner.azure.com/#profile) | [https://partner.microsoft.com/dashboard/account/management](https://partner.microsoft.com/dashboard/account/management) |
| Página de utilizadores | [https://cloudpartner.azure.com/#users](https://cloudpartner.azure.com/#users) | [https://partner.microsoft.com/dashboard/account/usermanagement](https://partner.microsoft.com/dashboard/account/usermanagement) |
| Página de história | [https://cloudpartner.azure.com/#history](https://cloudpartner.azure.com/#history) | A funcionalidade História ainda não é suportada no Partner Center. |
| Painel de insights | [https://cloudpartner.azure.com/#insights](https://cloudpartner.azure.com/#insights) | [https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) |
| Relatório de pagamento | [https://cloudpartner.azure.com/#insights/payout](https://cloudpartner.azure.com/#insights/payout) | [https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments](https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments) |
|||

## <a name="payout-report-differences"></a>Diferenças de relatório de pagamento

Estas são as diferenças no relatório de pagamento entre o Portal de Parceiros Cloud reformado e o atual Centro de Parceiros:

| Portal de Parceiros da Cloud | Partner Center |
| --- | --- |
| **Ligação:**https://cloudpartner.azure.com/ | **Ligação**: https://partner.microsoft.com/dashboard/payouts/reports/transactionhistory e https://partner.microsoft.com/dashboard/payouts/reports/incentivepayments |
| **Navegação**: Relatório de pagamento fornecido no Pagamento de Insights | **Navegação**: Relatório de pagamento fornecido no Partner Center – Ícone de pagamento |
| **Âmbito:**<ul><li>A transação por item de linha é visível, para recolha em curso, recolhida e paga.</li><li>Reporting – mostra todos os itens de linha uma vez que a encomenda de compra é criada, incluindo a cobrança em andamento e faturação em andamento, e o estado de cobrança e itens de linha que ainda não estão elegíveis para serem pagos.</li></ul> | **Âmbito:**<ul><li>Mostra os itens de linha depois de serem considerados ganhos elegíveis.</li><li>Os clientes pagam primeiro à Microsoft, e depois os ISVs podem ver o relatório de pagamento a começar.</li><li>O relatório do pagamento não mostrará a cobrança em andamento e a faturação em Progress.</li></ul> |
| **Transação não pronta para pagamento**: Faturação em Progresso | **Transação não pronta para pagamento**: Próximo pagamento estimado: O estado de pagamento está no estado não processado. |
| **Estado do pagamento**: n/a | **Estado do pagamento:**<ul><li>Não processado: O ganho é elegível para pagamento.</li><li>A seguir: O ganho será enviado à editora no próximo pagamento mensal.</li><li>Enviado: O pagamento foi enviado para o seu banco.</li></ul> |
|||

## <a name="what-about-offers-i-published-in-the-cloud-partner-portal"></a>E as ofertas que publiquei no Portal do Parceiro Cloud?

As ofertas foram transferidas para o Partner Center e estarão acessíveis depois de iniciar seduca no Partner Center, com exceção das ofertas da Dynamics Nav Managed Service e cortana Intelligence. Se a sua oferta foi ao vivo no mercado comercial, continuará a ser ao vivo e os seus clientes continuarão a poder comprá-la e implantá-la sem interrupções. Veja a próxima pergunta, **que ofertas foram transferidas para o Partner Center?**

## <a name="what-offers-were-moved-to-partner-center"></a>Que ofertas foram transferidas para o Partner Center?

Todos os tipos de oferta previamente suportados no Portal do Parceiro Cloud são suportados no Partner Center, com exceção das ofertas dynamics Nav Managed Service e Cortana Intelligence.

Para os tipos de oferta suportados no Partner Center, todas as ofertas foram movidas independentemente do seu estatuto; rascunho, des-listado, e ofertas apenas de pré-visualização também mudou.

| Tipo de oferta | Mudou-se para o Partner Center? | Passos seguintes |
| --- | --- | --- |
| SaaS | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais no [Plano uma oferta SaaS para o mercado comercial.](plan-saas-offer.md) |
| Máquina Virtual | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais no [Plano de uma oferta de máquina virtual.](marketplace-virtual-machines.md) |
| Aplicação Azure | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create a oferta de aplicações Azure.](create-new-azure-apps-offer.md) |
| Dynamics 365 Business Central | Sim | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create a Dynamics 365 Business Central.](partner-center-portal/create-new-business-central-offer.md) |
| Dinâmica 365 para Envolvimento de Clientes & PowerApps | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create a Dynamics 365 para o envolvimento do cliente & oferta de PowerApps.](partner-center-portal/create-new-customer-engagement-offer.md) |
| Dynamics 365 for Operations | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Oferta Create a Dynamics 365 for Operations.](partner-center-portal/create-new-operations-offer.md) |
| Aplicação Power BI | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create a Power BI app para AppSource.](partner-center-portal/create-power-bi-app-offer.md) |
| Módulo IoT Edge | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create, configurar e publicar uma oferta de módulo IoT Edge no Azure Marketplace.](partner-center-portal/azure-iot-edge-module-creation.md) |
| Contentor | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create a oferta de recipienteS Azure](./create-azure-container-offer.md). |
| Serviço de Consultoria | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create a consulting service offer.](./create-consulting-service-offer.md) |
| Serviço Gerido | Yes | Inscreva-se no Partner Center para criar novas ofertas e gerir ofertas que foram criadas no Cloud Partner Portal. Saiba mais na [Create a Managed Service offer](./plan-managed-service-offer.md). |
| Serviço gerido por Navegação Dinâmica | No | A Microsoft desenvolveu o Dynamics NAV Managed Service para [a Dynamics 365 Business Central](/dynamics365/business-central/), por isso desanumeroumos ofertas ao vivo do Serviço Gerido Dynamics NAV da AppSource. Estas ofertas já não são detetáveis pelos clientes e não foram transferidas para o Partner Center. Para disponibilizar as suas ofertas no AppSource, adapte-as às ofertas da Dynamics 365 Business Central e submeta-as no [Partner Center.](https://partner.microsoft.com/) Saiba mais na [Create a Dynamics 365 Business Central.](partner-center-portal/create-new-business-central-offer.md) |
| Cortana Intelligence | No | A Microsoft desenvolveu o roteiro do produto para a Cortana Intelligence, por isso desaubornamos as ofertas ao vivo da Cortana Intelligence da AppSource. Estas ofertas já não são detetáveis pelos clientes e não foram transferidas para o Partner Center. Para disponibilizar as suas ofertas no mercado comercial, adapte as suas ofertas ao Software como um Serviço (SaaS) oferece e submeta-as no [Partner Center.](https://partner.microsoft.com/) Saiba mais na [SaaS offer a lista de verificação de criação no Partner Center.](./plan-saas-offer.md) |

## <a name="i-cant-find-my-cloud-partner-portal-offers-in-partner-center"></a>Não encontro as ofertas do meu Portal de Parceiros Cloud no Partner Center

O que vê no Partner Center depende dos programas em que está inscrito, das contas a que pertence e das funções e permissões de utilizador que lhe foram atribuídas. Existem muitos programas do Partner Center disponíveis e você pode estar inscrito em vários programas. Pode também ter acesso a várias contas com as mesmas credenciais de utilizador.

As ofertas que criou no Cloud Partner Portal estão disponíveis no Partner Center no âmbito do programa **Mercado Comercial** e na conta utilizada para criar as ofertas. Para se certificar de que está a ver o programa certo e a conta certa, siga os passos abaixo. Para obter outras dicas de resolução de problemas, consulte [Gerir a sua conta Partner Center](/partner-center/partner-center-account-setup).

### <a name="access-the-right-program-in-partner-center"></a>Aceda ao programa certo no Partner Center

1. Inscreva-se no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) com as mesmas credenciais usadas para assinar no Portal do Parceiro Cloud. O painel de navegação à esquerda apresenta opções associadas aos programas em que está inscrito.

    Exemplo: assuma que tem acesso a três programas: o programa MPN, o programa de Referências e o programa Mercado Comercial. Quando assinar no Partner Center, verá estes três programas no painel de navegação.

2. Selecione **Commercial Marketplace**  >  **Overview** para aceder às suas ofertas.

    Se não vir o programa Mercado Comercial no painel de navegação à esquerda, pode estar na conta errada. Siga os passos na secção seguinte para aceder à conta certa.

    [![Screenshot que mostra o menu de visão geral do Centro de Parceiros](media/cpp-pc-faq/overview-menu.png "Mostra o menu de visão geral do Centro de Parceiros")](media/cpp-pc-faq/overview-menu.png#lightbox)

### <a name="access-the-right-account-in-partner-center"></a>Aceda à conta certa no Partner Center

Se fizer parte de várias contas, no centro Partner verá um botão de recolha de conta marcado por duas setas no menu de navegação à esquerda. Selecione o botão de recolha de conta para visualizar uma lista de todas as contas a que pertence. Selecione qualquer conta na lista para mudar para ela e ver todos os programas e informações relativos a essa conta. Se não vir um botão de recolha de conta no menu de navegação, é membro de uma única conta.

[![A screenshot mostra o botão de picker de conta Partner Center.](media/cpp-pc-faq/picker-button.png "Mostra o botão picker de conta Partner Center")](media/cpp-pc-faq/picker-button.png#lightbox)

## <a name="how-do-i-create-new-offers"></a>Como crio novas ofertas?

Aceda ao programa de marketplace comercial no [Partner Center](https://partner.microsoft.com/dashboard/commercial-marketplace/overview) para criar novas ofertas. Na página 'Vista Geral', selecione **+ Nova oferta.**

[![A screenshot mostra o menu de visão geral do Centro Parceiro.](media/cpp-pc-faq/new-offer.png "Mostra o menu de visão geral do Centro de Parceiros")](media/cpp-pc-faq/new-offer.png#lightbox)

## <a name="i-cant-sign-in-and-need-to-open-a-support-ticket"></a>Não posso entrar e preciso abrir um bilhete de apoio.

Se não puder entrar na sua conta, pode abrir um [bilhete de apoio](https://partner.microsoft.com/support/v2/?stage=1) aqui.

## <a name="where-are-instructions-for-using-partner-center"></a>Onde estão as instruções para a utilização do Partner Center?

Vá à documentação do [mercado comercial,](index.yml)em seguida, expanda o **Portal do Mercado Comercial no Partner Center.** Para ver artigos de ajuda para criar ofertas no Partner Center, expandir **Crie uma nova oferta.**

## <a name="what-are-the-publishing-and-offer-management-differences"></a>Quais são as diferenças de gestão de publicações e ofertas?

Aqui ficam algumas diferenças entre o Portal do Parceiro Cloud e o Partner Center.

### <a name="modular-publishing-capabilities"></a>Capacidades de publicação modulares

O Partner Center fornece uma opção de publicação modular que permite selecionar as alterações que pretende publicar em vez de publicar sempre todas as atualizações de uma só vez. Por exemplo, o seguinte ecrã mostra que as únicas alterações selecionadas a serem publicadas são as alterações à **Listagem de Propriedades** e **Ofertas.** As alterações que es fizer na página de Pré-visualização não serão publicadas.

[![A screenshot mostra a página de revisão e publicação do Partner Center.](media/cpp-pc-faq/review-page.png "Mostra a revisão do centro de parceiros e a página de publicação")](media/cpp-pc-faq/review-page.png#lightbox)

As atualizações que não publica são guardadas como rascunhos. Continue a usar a sua pré-visualização da oferta para verificar a sua oferta antes de a tornar ao vivo ao público.

### <a name="enhanced-preview-options"></a>Opções de pré-visualização melhoradas

O Partner Center inclui uma [funcionalidade de comparação](partner-center-portal/update-existing-offer.md#compare-changes-to-your-offer) com opções de filtragem melhoradas. Isto dá-lhe a capacidade de comparar com as versões de pré-visualização e ao vivo da oferta.

[![A screenshot mostra a função de comparação do Partner Center.](media/cpp-pc-faq/compare.png "Mostra a função de comparação do Centro parceiro")](media/cpp-pc-faq/compare.png#lightbox)

### <a name="branding-and-navigation-changes"></a>Alterações de marca e navegação

Vai notar algumas mudanças de marca. Por exemplo, *os SKUs* são marcados como Planos no Centro de *Parceiros:*

[![A screenshot mostra a página De Planos do Centro de Parceiros.](media/cpp-pc-faq/plans.png "Mostra a página de Planos do Centro de Parceiros")](media/cpp-pc-faq/plans.png#lightbox)

Além disso, as informações que forneceu anteriormente nas páginas **Marketplace** ou **Storefront Details**  (Serviço de Consultoria, Power BI) no Portal do Parceiro cloud são agora recolhidas na página **de listagem de Ofertas** no Partner Center:

[! [Screenshot mostra a página de listagem da Oferta do Centro parceiro.] (meios de comunicação/cpp-pc-faq/offer-listing.png](media/cpp-pc-faq/offer-listing.png#lightbox)

As informações que forneceu anteriormente para SKUs numa única página no Portal do Parceiro cloud podem agora ser recolhidas ao longo de várias páginas no Partner Center:

- Página de configuração do plano
- Página de listagem de plano
- Página de disponibilidade do plano
- Plane página de configuração técnica, como mostrado aqui:

[![Mostra a página de configuração técnica do Centro parceiro.](media/cpp-pc-faq/technical-configuration.png)](media/cpp-pc-faq/technical-configuration.png#lightbox)

O seu ID de oferta é agora mostrado na barra de navegação à esquerda da oferta:

![Mostra a localização do ID da oferta do centro de parceiros](media/cpp-pc-faq/offer-id.png)

### <a name="stop-selling-an-offer"></a>Pare de vender uma oferta

Pode solicitar para [parar de vender uma oferta](partner-center-portal/update-existing-offer.md#stop-selling-an-offer-or-plan) no mercado diretamente a partir do portal Partner Center. A opção está disponível na página geral da **Oferta** para a sua oferta.

[![A screenshot mostra a página partner Center para parar de vender uma oferta.](media/cpp-pc-faq/stop-sell.png "Mostra a página do Centro de Parceiros para parar de vender uma oferta")](media/cpp-pc-faq/stop-sell.png#lightbox)
<br><br>

## <a name="are-the-cloud-partner-portal-rest-apis-still-supported"></a>As APIs do Portal cloud Partner rest ainda estão suportadas?

As APIs do Portal do Parceiro cloud estão integradas com o Partner Center e continuarão a funcionar. A transição para o Partner Center introduz pequenas alterações. Reveja a tabela abaixo para garantir que o seu código continua a funcionar no Partner Center.

| API | Alterar descrição | Impacto |
| --- | --- | --- |
| POST Publish, GoLive, Cancele | Para as ofertas migradas, o cabeçalho de resposta terá um formato diferente, mas continuará a funcionar da mesma forma, denotando um caminho relativo para recuperar o estado de funcionamento. | Ao enviar qualquer um dos pedidos de envio de uma oferta, o cabeçalho Localização terá um de dois formatos dependendo do estado de migração da oferta: <ul><li>Ofertas não migradas: `/api/operations/{PublisherId}${offerId}$2$preview?api-version=2017-10-31`</li><li>Ofertas migradas: `/api/publishers/{PublisherId}/offers/{offereId}/operations/408a4835-0000-1000-0000-000000000000?api-version=2017-10-31`</li></ul>|
| Operação GET | Para ofertas que anteriormente suportassem um campo de 'notification-email' na resposta, este campo será depreciado e deixará de ser devolvido para ofertas migratórias. | Para ofertas migradas, não enviaremos mais notificações para a lista de e-mails especificados nos pedidos. Em vez disso, o serviço API irá alinhar-se com o processo de e-mail de notificação no Partner Center para enviar e-mails. Especificamente, as notificações de progresso da operação serão enviadas para o endereço de e-mail definido na secção de informações de conta do Vendedor das definições da sua conta no Partner Center.<br><br>Certifique-se de que o endereço de e-mail definido na secção de informações de contacto do Vendedor nas [definições de Conta](https://partner.microsoft.com/dashboard/account/management) no Centro de Parceiros está correto para receber notificações. |
|||