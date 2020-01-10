---
title: Perguntas frequentes e terminologia para a análise do Marketplace comercial no Partner Center
description: Saiba como abordar as perguntas mais frequentes sobre a análise do Marketplace comercial. Inclui um dicionário de dados para a terminologia de análise.
author: ChJenk
manager: evansma
ms.author: v-chjen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/11/2019
ms.openlocfilehash: 559c0e2a56bfb452f1faf10f68c19d4f406427e8
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75480557"
---
# <a name="frequently-asked-questions-and-terminology-for-commercial-marketplace-analytics"></a>Perguntas frequentes e terminologia para análise do Marketplace comercial

Este artigo aborda as perguntas mais frequentes sobre as mensagens de análise no Partner Center e também fornece um dicionário de terminologia de análise.

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

Esta seção fornece respostas para perguntas frequentes sobre **nenhuma análise disponível ainda** em mensagens no Partner Center.

**Não consigo exibir meus dados de análise no Partner Center. Quando acesso essas páginas, vejo a mensagem abaixo. Por que isso?**

![Não há dados para suas ofertas ainda](./media/analytics-faq-no-data.png)

Por que você pode estar recebendo esta mensagem:

- Suas ofertas listadas no Marketplace não são ofertas de SaaS. Atualmente, a análise de relatórios do Partner Center para SaaS oferece apenas. As ofertas do Dynamics 365 podem ser publicadas no Partner Center, mas a análise dessas ofertas ainda não está habilitada no Partner Center.
- No momento, não existem aquisições para suas ofertas publicadas no Marketplace. Isso pode significar que suas ofertas estão ativas no Marketplace e obtendo exibições de clientes nas páginas de exibição do produto, mas os clientes ainda não tomaram medidas para comprá-las e implantá-las.
- A publicação da sua oferta pode estar em processo e ainda não está dinâmica. Somente as ofertas dinâmicas podem ser adquiridas pelos clientes. Para verificar o status de suas ofertas, consulte Visão geral no [painel analisar](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Para obter mais informações, consulte [painel de resumo na análise do Marketplace comercial](./summary-dashboard.md).
- Suas ofertas podem estar listadas como **contato comigo**, que são ofertas somente de lista e não podem ser adquiridas por clientes no Marketplace. Embora essas ofertas gerem leads e sejam compartilhadas com você, as ordens não são criadas para essas ofertas, pois não podem ser adquiridas. Para verificar o tipo de listagem de oferta, vá para a página configuração.

**Sei que tenho dados de análise, mas a mensagem abaixo está aparecendo:**

![Não há dados para o intervalo de datas fornecido](./media/analytics-faq-data-range.png)

Se você estiver recebendo esta mensagem, isso significa que você tem dados de análise, mas não há dados para o intervalo de datas selecionado. Selecione um intervalo de datas diferente ou um intervalo de datas personalizado para exibir os dados desde 2010. Para obter mais informações, consulte a seção intervalo de datas do [painel Resumo na análise do Marketplace comercial](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Dicionário de termos de dados

| Nome de atributo | Relatórios | Definição|
|---|---|---|
| Tipo de licença do Azure | Cliente, pedido | O tipo de contrato de licenciamento usado pelos clientes para comprar o Azure. Também conhecido como canal |
| Tipo de licença do Azure: provedor de soluções na nuvem | Cliente, pedido | O cliente final adquiri o Azure e seu Marketplace oferece por meio de seu provedor de soluções na nuvem, que atua como seu revendedor.|
| Tipo de licença do Azure: Enterprise | Cliente, pedido | O cliente final compra o Azure e sua oferta do Marketplace por meio de um Enterprise Agreement, assinados diretamente com a Microsoft.|
| Tipo de licença do Azure: Enterprise por meio do revendedor  | Cliente, pedido | O cliente final compra o Azure e sua oferta do Marketplace por meio de um revendedor que facilita seus Enterprise Agreement com a Microsoft.|  |
| Tipo de licença do Azure: pague conforme o uso| Cliente, pedido | O cliente final compra o Azure e sua oferta do Marketplace por meio de um contrato "pré-pago", assinado diretamente com a Microsoft.||
| Nome da instância de nuvem| Encomenda| O Microsoft Cloud em que ocorreu uma implantação de VM.||
| Nome da instância de nuvem: Azure global| Encomenda| A nuvem global da Microsoft pública.|| |
| Nome da instância de nuvem: Azure governamental | Encomenda| Nuvens Microsoft específicas do governo para um dos seguintes governos: China, Alemanha ou a Estados Unidos da América.| |
| Cidade do cliente| Cliente| O nome da cidade fornecida pelo cliente. A cidade pode ser diferente da cidade na assinatura do Azure de um cliente.||
| Idioma de comunicação do cliente  | Cliente| A linguagem preferida pelo cliente para comunicação.||
| Nome da empresa do cliente | Cliente, pedido | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura do Azure de um cliente.|  |
| País do cliente | Cliente, pedido | O nome do país fornecido pelo cliente. O país pode ser diferente do país na assinatura do Azure de um cliente.|  |
| Email do cliente| Cliente| O endereço de email fornecido pelo cliente final. O email pode ser diferente do endereço de email na assinatura do Azure de um cliente.||
| Nome do cliente| Cliente| O nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na assinatura do Azure de um cliente.| |
| ID de Cliente | Cliente, pedido | O identificador exclusivo atribuído a um cliente. Um cliente pode ter zero ou mais assinaturas do Azure Marketplace.|  |
| CEP do cliente  | Cliente| O CEP fornecido pelo cliente. O código pode ser diferente do código postal fornecido na assinatura do Azure de um cliente.| |
| Estado do cliente| Cliente| O estado (endereço) fornecido pelo cliente. O estado pode ser diferente do estado fornecido na assinatura do Azure de um cliente.| |
| Data de aquisição| Cliente| A primeira data em que o cliente comprou qualquer oferta publicada por você.| |
| Data de perda| Cliente| A última data em que o cliente cancelou o último de todas as ofertas adquiridas anteriormente.||
| É novo cliente  | Encomenda| O valor identificará um novo cliente adquirindo uma ou mais das suas ofertas pela primeira vez (ou não). O valor será "Sim" se estiver no mesmo mês do calendário para "data de aquisição". O valor será "não" se o cliente tiver comprado uma de suas ofertas antes do relatório do mês do calendário. |
| É versão prévia do SKU| Encomenda| O valor informará se você marcou a SKU como "visualização". O valor será "Sim" se o SKU tiver sido marcado adequadamente e somente as assinaturas do Azure autorizadas por você puderem implantar e usar essa imagem. O valor será "no" se o SKU não tiver sido identificado como "Preview".  |
| É aceitar contato promocional| Cliente| O valor permitirá que você saiba se o cliente optou proativamente para contato promocional dos editores. Neste momento, não estamos apresentando a opção aos clientes, portanto, indicamos "não" no tabuleiro. Depois que esse recurso for implantado, começaremos a atualizar de acordo.|
| Tipo de licença do Marketplace| Encomenda| O método de cobrança da oferta do Marketplace.||
| Tipo de licença do Marketplace: cobrado por meio do Azure| Encomenda| A Microsoft é o seu agente para esta oferta do Marketplace e cobra os clientes em seu nome. (PAYG de cartão de crédito ou nota fiscal empresarial)||
| Tipo de licença do Marketplace: traga sua própria licença | Encomenda| A VM requer uma chave de licença fornecida pelo cliente para implantar. A Microsoft não fatura os clientes para listar suas ofertas dessa maneira por meio do Marketplace.||
| Tipo de licença do Marketplace: gratuito| Encomenda| A oferta está configurada para ser gratuita para todos os usuários. A Microsoft não cobra os clientes pelo uso dessa oferta.||
| Tipo de licença do Marketplace: Microsoft como revendedor  | Encomenda| A Microsoft é seu revendedor para esta oferta do Marketplace.|  |
| ID da assinatura do Marketplace | Cliente, pedido | O identificador exclusivo associado à assinatura do Azure que o cliente usou para comprar sua oferta do Marketplace. ID era anteriormente a GUID de assinatura do Azure.||
| Nome da Oferta  | Encomenda| O nome da oferta do Marketplace.|| |
| Tipo de Oferta  | Encomenda| O tipo de oferta de Microsoft Marketplace.|||
| Tipo de oferta: aplicativo gerenciado  | Ordene | Usar o tipo de oferta aplicativo do Azure: aplicativo gerenciado quando as seguintes condições são necessárias: você implanta uma solução baseada em assinatura para seu cliente usando uma VM ou uma solução inteira baseada em IaaS. Você ou seu cliente exigem que a solução seja gerenciada por um parceiro. |
| Tipo de oferta: Aplicativo Azure| Ordene | Use o tipo de oferta de modelo de solução Aplicativo Azure quando sua solução exigir implantação adicional e automação de configuração além de uma VM simples.||
| Tipo de oferta: serviço de consultoria| Encomenda| Serviços de consultoria no Azure Marketplace ajudam a conectar clientes com serviços para dar suporte e estender o uso do Azure.| |
| Tipo de oferta: contêiner | Encomenda| Use o tipo de oferta de contêiner quando sua solução for uma imagem de contêiner do Docker provisionado como um serviço de contêiner do Azure baseado em kubernetes.||
| Tipo de oferta: Dynamics 365 Business central| Encomenda| Use esse tipo de oferta quando sua solução estiver integrada com o Dynamics 365 para finanças e operações| |
| Tipo de oferta: Dynamics 365 para compromisso com o cliente | Encomenda| Use esse tipo de oferta quando sua solução estiver integrada com o Dynamics 365 for Customer Engagement.||
| Tipo de oferta: módulo de IoT Edge | Encomenda| Os módulos de Azure IoT Edge são as menores unidades de computação gerenciadas pelo IoT Edge e podem conter serviços da Microsoft (como Azure Stream Analytics), serviços de terceiros ou seu próprio código específico da solução. |
| Tipo de oferta: Power BI aplicativo | Encomenda| Use o tipo de oferta de aplicativo Power BI ao implantar um aplicativo integrado com Power BI.|  |
| Tipo de oferta: aplicativo SaaS| Encomenda| Use o tipo de oferta de aplicativo SaaS para permitir que seu cliente compre sua solução técnica baseada em SaaS como uma assinatura.||
| Tipo de oferta: máquina virtual | Encomenda| Use o tipo de oferta de máquina virtual ao implantar um dispositivo virtual na assinatura associada ao cliente.||
| Tipo de oferta: extensão de Visual Studio Marketplace  | Encomenda| Tipo de oferta disponível anteriormente para os desenvolvedores de extensão DevOps do Azure. Avançar os desenvolvedores da extensão DevOps do Azure podem vender sua extensão diretamente para os clientes. As ofertas de extensão podem ser configuradas como pagas ou incluindo uma avaliação. |
| Data de cancelamento do pedido| Encomenda| A data em que a ordem do Marketplace foi cancelada.||
| ID da Encomenda| Encomenda| O identificador exclusivo da ordem do cliente para o serviço do Marketplace. As ofertas baseadas em uso da máquina virtual não estão associadas a um pedido.| |
| Data de compra do pedido| Encomenda| A data em que a ordem do Marketplace foi criada.|||
| Estado da Encomenda| Encomenda| O status de uma ordem do Marketplace no momento em que os dados foram atualizados pela última vez.|     |
| Status do pedido: ativo  | Encomenda| O cliente comprou um pedido e não cancelou seu pedido.|         |
| Status do pedido: cancelado | Encomenda| O cliente comprou um pedido anteriormente e, subsequentemente, cancelou seu pedido.||
| Email do provedor| Cliente| O endereço de email do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP.|
| Nome do Fornecedor| Cliente| O nome do provedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma empresa por meio do revendedor, esse será o revendedor. Se um CSP (provedor de soluções de nuvem) estiver envolvido, esse será o CSP.|
| SKU| Encomenda| Nome do SKU, conforme definido durante a publicação. Uma oferta pode ter muitas SKUs, mas uma SKU só pode ser associada a uma única oferta.||
| Data de término da avaliação| Encomenda| A data em que o período de avaliação para esta ordem será encerrado ou finalizado.||

## <a name="next-steps"></a>Passos seguintes

- Para obter uma visão geral dos relatórios de análise disponíveis no Marketplace comercial do Partner Center, consulte [análise do Marketplace comercial no Partner Center](./analytics.md).
- Para grafos, tendências e valores de dados agregados que resumem a atividade do Marketplace para sua oferta, consulte [painel de resumo na análise do Marketplace comercial](./summary-dashboard.md).
- Para obter informações sobre seus pedidos em um formato gráfico e baixável, consulte [painel de pedidos na análise do Marketplace comercial](./orders-dashboard.md).
- Para VM (máquina virtual) oferece métricas de uso e cobrança limitada, consulte [painel de uso na análise do Marketplace comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre seus clientes, incluindo tendências de crescimento, consulte [painel do cliente na análise do Marketplace comercial](./customer-dashboard.md).
- Para obter uma lista de suas solicitações de download nos últimos 30 dias, consulte o [painel de downloads na análise do Marketplace comercial](./downloads-dashboard.md).
- Para ver uma exibição consolidada dos comentários dos clientes para ofertas no Azure Marketplace e no AppSource, consulte [painel classificações e revisões na análise do Marketplace comercial](./ratings-reviews.md).
