---
title: Terminologia de & de análise de mercado comercial - Partner Center
description: Obtenha respostas para perguntas comumente sobre análise de marketplace comercial no Partner Center. Este artigo inclui um dicionário de dados para terminologia analítica.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
author: shganesh-dev
ms.author: shganesh
ms.openlocfilehash: aff26d3b491896aa9a1b1fee93d39a5ec93d730c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91665146"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Terminologia de análise de mercado comercial e questões comuns

Este artigo aborda perguntas comumente sobre mensagens de análise no Partner Center e também fornece um dicionário de terminologia analítica.

## <a name="common-questions"></a>Perguntas comuns

**Não consigo ver os meus dados de análise no Partner Center. Quando acesso a estas páginas, vejo a mensagem abaixo. Por que é isto?**

![Ainda não há dados para as suas ofertas](./media/analytics-faq-no-data.png)

Porque é que pode estar a receber esta mensagem:

- Atualmente não existem aquisições para as suas ofertas publicadas no mercado. Isto pode significar que as suas ofertas estão ao vivo no mercado e a obter visualizações dos clientes nas páginas de exibição do produto, mas os clientes ainda não tomaram medidas para as comprar e implementar.
- A publicação da sua oferta pode estar em processo e ainda não está ao vivo. Apenas as ofertas ao vivo podem ser adquiridas pelos clientes. Para verificar o estado das suas ofertas, consulte a visão geral no [painel de instrumentos 'Analisar'.](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary) Para obter mais informações, consulte [o Painel De Resumo em análise de mercado comercial.](./summary-dashboard.md)
- As suas ofertas podem ser listadas como **Contato Me,** que são ofertas apenas de lista e não podem ser adquiridas por clientes no mercado. Embora estas ofertas gerem leads e são partilhadas consigo, as encomendas não são criadas para estas ofertas, uma vez que não podem ser compradas. Para verificar o tipo de listagem de oferta, vá para a página de configuração.

**Sei que tenho dados analíticos, mas a mensagem abaixo está a aparecer:**

![Não há dados para o intervalo de datas dado](./media/analytics-faq-data-range.png)

Se estiver a receber esta mensagem, significa que tem dados de análise, mas não existem dados para o intervalo de datas selecionados. Selecione uma gama de datas ou uma gama de datas personalizadas diferente para ver quaisquer dados desde 2010. Para obter mais informações, consulte a secção de intervalo de datas do [Painel de Resumo em análise de mercado comercial.](./summary-dashboard.md)

## <a name="dictionary-of-data-terms"></a>Dicionário de termos de dados

| Nome do atributo | Relatórios | Definição|
|---|---|---|
| Tipo de Licença Azure | Cliente, Encomenda | O tipo de contrato de licenciamento usado pelos clientes para comprar a Azure. Também conhecido como Canal |
| Tipo de licença Azure: Fornecedor de solução de nuvem | Cliente, Encomenda | O cliente final adquire a Azure e a sua oferta marketplace através do seu Cloud Solution Provider, que atua como o seu revendedor.|
| Tipo de licença Azure: Empresa | Cliente, Encomenda | O cliente final adquire a Azure e a sua oferta marketplace através de um Acordo Empresarial, assinado diretamente com a Microsoft.|
| Tipo de licença Azure: Empresa através de Revendedor  | Cliente, Encomenda | O cliente final adquire a Azure e a sua oferta marketplace através de um revendedor que facilita o seu Acordo de Empresa com a Microsoft.|
| Tipo de licença Azure: Pague à medida que vai| Cliente, Encomenda | O cliente final adquire a Azure e a sua oferta marketplace através de um acordo "Pay as You Go", assinado diretamente com a Microsoft.|
| ID de conta de faturação| Cliente, Encomenda, Utilização| O identificador da conta em que a faturação é gerada. **Mapear iD de conta** de faturação ao **clienteID** para ligar o seu Relatório de Transações de Pagamento com os Relatórios de Cliente, Ordem e Utilização.|
| Nome da instância da nuvem| Encomenda| A Cloud Microsoft em que ocorreu uma implementação em VM.|
| Nome da instância da nuvem: Azure Global| Encomenda| A nuvem pública global da Microsoft.|
| Nome da instância da nuvem: Governo Azure | Encomenda| Nuvens específicas do governo da Microsoft para um dos seguintes governos: China, Alemanha ou Estados Unidos da América.| |
| Cidade do Cliente| Cliente| O nome da cidade fornecido pelo cliente. A cidade pode ser diferente da cidade na assinatura Azure de um cliente.|
| Linguagem de comunicação do cliente  | Cliente| O idioma preferido pelo cliente para comunicação.|
| Nome da empresa do cliente | Cliente, Encomenda | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na Subscrição Azure de um cliente.|
| País/Região do Cliente | Cliente, Encomenda | O nome país/região fornecido pelo cliente. O país/região poderia ser diferente do país/região na Subscrição Azure de um cliente.|
| E-mail do cliente| Cliente| O endereço de e-mail fornecido pelo cliente final. O e-mail pode ser diferente do endereço de e-mail na Subscrição Azure de um cliente.|
| Primeiro nome do cliente| Cliente| O nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na Subscrição Azure de um cliente.|
| ID de Cliente | Cliente, Encomenda | O identificador único atribuído a um cliente. Um cliente pode ter zero ou mais Subscrições do Azure Marketplace.|
| Código Postal do Cliente  | Cliente| O código postal fornecido pelo cliente. O código pode ser diferente do código postal fornecido na Subscrição Azure de um cliente.|
| Estado do Cliente| Cliente| O estado (endereço) fornecido pelo cliente. O Estado pode ser diferente do estado fornecido na Subscrição Azure de um cliente.|
| Data Adquirida| Cliente| Na primeira data em que o cliente adquiriu qualquer oferta publicada por si.|
| Data Perdida| Cliente| Na última data em que o cliente cancelou a última de todas as ofertas previamente adquiridas.|
| É novo cliente  | Encomenda| O valor identificará um novo cliente adquirindo uma ou mais das suas ofertas pela primeira vez (ou não). O valor será "Sim" se no mesmo mês de calendário para "Data Adquirida". O valor será "Não" se o cliente tiver adquirido alguma das suas ofertas antes do mês civil reportado. |
| É pré-visualização SKU| Encomenda| O valor irá informá-lo se marcou o SKU como "pré-visualização". O valor será "Sim" se o SKU tiver sido marcado em conformidade, e apenas as subscrições Azure autorizadas por você pode implementar e usar esta imagem. O valor será "Não" se o SKU não tiver sido identificado como "pré-visualização". |
| É Contacto Promocional Opt In| Cliente| O valor irá informá-lo se o cliente optou proativamente pelo contacto promocional das editoras. Neste momento, não estamos a apresentar a opção aos clientes, por isso indicámos "Não" em todo o tabuleiro. Assim que esta funcionalidade for implementada, começaremos a atualizar em conformidade.|
| Tipo de Licença de Mercado| Encomenda| O método de faturação da oferta do Marketplace.|
| Tipo de licença de mercado: faturado através do Azure| Encomenda| A Microsoft é o seu agente para esta oferta do Marketplace e fatura clientes em seu nome. (Cartão de Crédito PAYG ou Fatura Empresarial)|
| Tipo de licença de mercado: Traga a sua própria licença | Encomenda| O VM requer uma chave de licença fornecida pelo cliente para implementar. A Microsoft não cobra aos clientes a listagem das suas ofertas desta forma através do mercado.|
| Tipo de licença de mercado: grátis| Encomenda| A oferta está configurada para ser gratuita para todos os utilizadores. A Microsoft não cobra aos clientes pelo uso desta oferta.|
| Tipo de licença de mercado: Microsoft como Revendedor  | Encomenda| A Microsoft é o seu revendedor para esta oferta do Marketplace.|
| ID de assinatura de mercado | Cliente, Encomenda | O identificador único associado à Subscrição Azure que o cliente usou para adquirir a sua oferta marketplace. ID foi anteriormente a Azure Subscription GUID.|
| Nome da Oferta  | Encomenda| O nome da oferta do Marketplace.|
| Tipo de Oferta  | Encomenda| O tipo de oferta do Microsoft Marketplace.|
| Tipo de Oferta: Aplicação gerida  | Encomenda | Utilize a aplicação Azure: tipo de oferta de aplicações geridas quando são necessárias as seguintes condições: Implementa uma solução baseada em subscrição para o seu cliente utilizando uma Solução VM ou toda uma solução baseada em IaaS. Você ou o seu cliente exigem que a solução seja gerida por um parceiro. |
| Tipo de Oferta: Aplicação Azure| Encomenda | Utilize o tipo de oferta de solução Azure Application quando a sua solução requer uma automatização adicional de implementação e configuração para além de um simples VM.|
| Tipo de Oferta: Serviço de Consultoria| Encomenda| Os Serviços de Consultoria no Azure Marketplace ajudam a conectar os clientes com serviços para apoiar e alargar o seu uso do Azure.|
| Tipo de Oferta: Recipiente | Encomenda| Utilize o tipo de oferta de recipiente quando a sua solução for uma imagem de contentor Docker aerovada como um serviço de contentores Azure baseado em Kubernetes.|
| Tipo de Oferta: Dinâmica 365 Business Central| Encomenda| Utilize este tipo de oferta quando a sua solução estiver integrada com a Dynamics 365 para Finanças e Operações|
| Tipo de Oferta: Dinâmica 365 para Envolvimento do Cliente | Encomenda| Utilize este tipo de oferta quando a sua solução estiver integrada com a Dynamics 365 para o Envolvimento do Cliente.|
| Tipo de oferta: Módulo IoT Edge | Encomenda| Os módulos Azure IoT Edge são as unidades de computação mais pequenas geridas pelo IoT Edge e podem conter serviços microsoft (como a Azure Stream Analytics), serviços de terceiros ou o seu próprio código específico para a solução. |
| Tipo de Oferta: Aplicação Power BI | Encomenda| Utilize o tipo de oferta de aplicação Power BI quando implementar uma aplicação integrada com o Power BI.|
| Tipo de Oferta: Aplicação SaaS| Encomenda| Utilize o tipo de oferta de aplicação SaaS para permitir ao seu cliente comprar a sua solução técnica baseada no SaaS como subscrição.||
| Tipo de Oferta: Máquina Virtual | Encomenda | Utilize o tipo de oferta de máquina virtual quando colocar um aparelho virtual na subscrição associada ao seu cliente.|
| Tipo de oferta: Extensão do mercado do estúdio visual  | Encomenda| Tipo de oferta anteriormente disponível para desenvolvedores de extensão Azure DevOps. Para a frente, os desenvolvedores de extensão Azure DevOps podem vender a sua extensão diretamente aos clientes. As ofertas de extensão podem ser configuradas como pagas ou incluindo um julgamento. |
| Data de Cancelamento de Encomenda| Encomenda| A data em que a encomenda do Marketplace foi cancelada.|
| ID da Encomenda| Encomenda| O identificador único da encomenda do cliente para o seu serviço Marketplace. As ofertas baseadas em máquinas virtuais não estão associadas a uma encomenda.|
| Data de Compra da Encomenda| Encomenda| A data em que a Ordem do Mercado foi criada.|
| Estado da Encomenda| Encomenda| O estado de uma encomenda do Marketplace na altura em que os dados foram atualizados pela última vez.|
| Estado da encomenda: Ativo  | Encomenda| O cliente comprou uma encomenda e não cancelou a sua encomenda.|
| Estado da Encomenda: cancelado | Encomenda| O cliente previamente adquiriu uma encomenda e posteriormente cancelou a sua encomenda.|
| E-mail do fornecedor| Cliente| O endereço de e-mail do fornecedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma Enterprise através do Revendedor, este será o revendedor. Se estiver envolvido um Fornecedor de Solução Cloud (CSP), este será o CSP.|
| Nome do provedor| Cliente| O nome do fornecedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma Enterprise através do Revendedor, este será o revendedor. Se estiver envolvido um Fornecedor de Solução Cloud (CSP), este será o CSP.|
| SKU| Encomenda| Nome SKU (agora chamado Plano) como definido durante a publicação. Uma oferta pode ter muitos SKUs (planos), mas cada um só pode ser associado a uma única oferta.|
| Data de Fim do Julgamento| Encomenda| A data do período experimental para esta ordem terminará ou terminou.|
|||

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumem a atividade do mercado para a sua oferta, consulte [o Painel De Resumo em análise de mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e transferível, consulte [o Painel de Encomendas em análise de marketplace comercial.](./orders-dashboard.md)
- Para Máquina Virtual (VM) oferece métricas de utilização e faturação medido, consulte [o Painel de Utilização em análise de mercado comercial.](./usage-dashboard.md)
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte [o Customer Dashboard em análise de mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento nos últimos 30 dias, consulte [Downloads Dashboard em análise de mercado comercial.](./downloads-dashboard.md)
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e comentários no painel de análise de mercado comercial.](./ratings-reviews.md)
