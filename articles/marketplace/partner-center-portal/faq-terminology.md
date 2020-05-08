---
title: Análise de mercado comercial FAQ & terminologia - Partner Center
description: Obtenha respostas a perguntas comumente feitas sobre análise de mercado comercial no Partner Center. Este artigo inclui um dicionário de dados para terminologia de análise.
author: dsindona
ms.author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 02/17/2020
ms.openlocfilehash: 42478f6e6d6cb28364fe39aafcb52b3a17aa3c23
ms.sourcegitcommit: e0330ef620103256d39ca1426f09dd5bb39cd075
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82791481"
---
# <a name="commercial-marketplace-analytics-terminology-and-common-questions"></a>Terminologia de análise de mercado comercial e questões comuns

Este artigo aborda perguntas comumente feitas sobre mensagens de análise no Partner Center e também fornece um dicionário de terminologia analítica.

## <a name="common-questions"></a>Perguntas comuns

**Não consigo ver os meus dados de análise no Partner Center. Quando acedo a estas páginas, vejo a mensagem abaixo. Por que é isto?**

![Ainda não há dados para as suas ofertas](./media/analytics-faq-no-data.png)

Porque pode estar a receber esta mensagem:

- Não existem atualmente aquisições para as suas ofertas publicadas no mercado. Isto pode significar que as suas ofertas estão ao vivo no mercado e a ganhar visualizações dos clientes nas páginas de exibição de produtos, mas os clientes ainda não tomaram medidas para as comprar e implementar.
- A publicação da sua oferta pode estar em processo e ainda não está ao vivo. Só as ofertas ao vivo podem ser adquiridas pelos clientes. Para verificar o estado das suas ofertas, consulte a visão geral no [painel de instrumentos da Analyze](https://partner.microsoft.com/dashboard/commercial-marketplace/analytics/summary). Para mais informações, consulte [Summary Dashboard na análise](./summary-dashboard.md)do mercado comercial.
- As suas ofertas podem ser listadas como **Contact Me**, que são ofertas apenas de lista e não podem ser compradas por clientes no mercado. Embora estas ofertas gerem pistas e sejam partilhadas consigo, as encomendas não são criadas para estas ofertas, uma vez que não podem ser compradas. Para verificar o seu tipo de oferta de listagem, vá à página de configuração.

**Sei que tenho dados de análise, mas a mensagem abaixo está a aparecer:**

![Não há dados para a data dada](./media/analytics-faq-data-range.png)

Se estiver a receber esta mensagem, significa que tem dados de análise, mas não existem dados para o intervalo de data seletiva selecionado. Selecione uma gama de datas diferente ou uma gama de datas personalizadas para visualizar quaisquer dados desde 2010. Para mais informações, consulte a secção Data Range do [Summary Dashboard na análise do mercado comercial](./summary-dashboard.md).

## <a name="dictionary-of-data-terms"></a>Dicionário de termos de dados

| Nome do atributo | Relatórios | Definição|
|---|---|---|
| Tipo de licença Azure | Cliente, Encomenda | O tipo de contrato de licenciamento utilizado pelos clientes para a compra do Azure. Também conhecido como Canal |
| Tipo de licença Azure: Fornecedor de soluções em nuvem | Cliente, Encomenda | O cliente final adquire a oferta do Azure e do seu Marketplace através do seu Fornecedor de Soluções Cloud, que atua como o seu revendedor.|
| Tipo de licença Azure: Enterprise | Cliente, Encomenda | O cliente final adquire a Oferta Azure e o seu Marketplace através de um Acordo de Empresa, assinado diretamente com a Microsoft.|
| Tipo de licença Azure: Empresa através de Revendedor  | Cliente, Encomenda | O cliente final adquire a oferta do Azure e do seu Marketplace através de um revendedor que facilita o seu Acordo de Empresa com a Microsoft.|  |
| Tipo de licença Azure: Pague à medida que vai| Cliente, Encomenda | O cliente final adquire a oferta do Azure e do seu Marketplace através de um acordo "Pay as You Go", assinado diretamente com a Microsoft.||
| Nome de instância de nuvem| Encomenda| O Microsoft Cloud em que ocorreu uma implementação vm.||
| Cloud Instance Name: Azure Global| Encomenda| A nuvem pública global da Microsoft.|| |
| Cloud Instance Name: Governo Azure | Encomenda| Nuvens específicas do governo para um dos seguintes governos: China, Alemanha ou Estados Unidos da América.| |
| Cidade do Cliente| Cliente| O nome da cidade fornecido pelo cliente. A cidade pode ser diferente da cidade na assinatura Azure de um cliente.||
| Linguagem de comunicação do cliente  | Cliente| A linguagem preferida pelo cliente para a comunicação.||
| Nome da empresa cliente | Cliente, Encomenda | O nome da empresa fornecido pelo cliente. O nome pode ser diferente da cidade na assinatura Azure de um cliente.|  |
| País cliente | Cliente, Encomenda | O nome do país fornecido pelo cliente. O país pode ser diferente do país na subscrição do Azure de um cliente.|  |
| E-mail do cliente| Cliente| O endereço de e-mail fornecido pelo cliente final. O e-mail pode ser diferente do endereço de e-mail na Subscrição Azure de um cliente.||
| Primeiro nome do cliente| Cliente| O nome fornecido pelo cliente. O nome pode ser diferente do nome fornecido na Subscrição Azure de um cliente.| |
| ID de Cliente | Cliente, Encomenda | O identificador único atribuído a um cliente. Um cliente pode ter zero ou mais subscrições do Azure Marketplace.|  |
| Código Postal do Cliente  | Cliente| O código postal fornecido pelo cliente. O código pode ser diferente do código postal fornecido na Assinatura Azure de um cliente.| |
| Estado cliente| Cliente| O estado (endereço) fornecido pelo cliente. O Estado pode ser diferente do estado fornecido na Subscrição Azure de um cliente.| |
| Data Adquirida| Cliente| A primeira data em que o cliente adquiriu qualquer oferta publicada por si.| |
| Data Perdida| Cliente| A última data em que o cliente cancelou a última de todas as ofertas anteriormente adquiridas.||
| É novo cliente  | Encomenda| O valor identificará um novo cliente adquirindo uma ou mais das suas ofertas pela primeira vez (ou não). O valor será "Sim" se dentro do mesmo mês civil para "Data Adquirida". O valor será "Não" se o cliente tiver adquirido alguma das suas ofertas antes do mês civil reportado. |
| É Pré-visualização SKU| Encomenda| O valor irá informá-lo se marcou o SKU como "pré-visualização". O valor será "Sim" se o SKU tiver sido marcado em conformidade, e apenas as subscrições do Azure autorizadas por si podem implementar e utilizar esta imagem. O valor será "Não" se o SKU não tiver sido identificado como "pré-visualização".  |
| É o Contacto Promocional Opt in| Cliente| O valor irá informá-lo se o cliente optou proativamente pelo contacto promocional da editora. Neste momento, não estamos a apresentar a opção aos clientes, pelo que indicámos "Não" em todo o tabuleiro. Assim que esta funcionalidade for implementada, começaremos a atualizar em conformidade.|
| Tipo de Licença de Mercado| Encomenda| O método de faturação da oferta do Marketplace.||
| Tipo de licença de mercado: Faturado através do Azure| Encomenda| A Microsoft é o seu agente para esta oferta do Marketplace e fatura clientes em seu nome. (Cartão de crédito PAYG ou Fatura da Empresa)||
| Tipo de licença de mercado: traga a sua própria licença | Encomenda| O VM requer uma chave de licença fornecida pelo cliente para implementar. A Microsoft não fatura clientes por listarem as suas ofertas desta forma através do mercado.||
| Tipo de licença de mercado: Grátis| Encomenda| A oferta está configurada para ser gratuita para todos os utilizadores. A Microsoft não fatura os clientes pelo uso desta oferta.||
| Tipo de licença de mercado: Microsoft como Revendedor  | Encomenda| A Microsoft é o seu revendedor para esta oferta do Marketplace.|  |
| ID de subscrição do mercado | Cliente, Encomenda | O identificador único associado à Subscrição Azure o cliente usou para comprar a sua oferta de Marketplace. Id foi anteriormente o GUIA de Subscrição Azure.||
| Nome da Oferta  | Encomenda| O nome da oferta do Marketplace.|| |
| Tipo de Oferta  | Encomenda| O tipo de oferta do Microsoft Marketplace.|||
| Tipo de Oferta: Aplicação gerida  | Ordem, | Utilize a aplicação Azure: tipo de oferta de aplicações gerida quando são necessárias as seguintes condições: Implementa uma solução baseada em subscrição para o seu cliente utilizando um VM ou uma solução inteira baseada em IaaS. Você ou o seu cliente exigem que a solução seja gerida por um parceiro. |
| Tipo de oferta: Aplicação Azure| Ordem, | Utilize o modelo de oferta de solução de aplicação Azure quando a sua solução requer uma implementação adicional e automatização de configuração para além de um VM simples.||
| Tipo de Oferta: Serviço de Consultoria| Encomenda| Os Serviços de Consultoria no Mercado Azure ajudam a conectar os clientes com serviços de apoio e alargar a sua utilização do Azure.| |
| Tipo de oferta: Recipiente | Encomenda| Utilize o tipo de oferta do Recipiente quando a sua solução for uma imagem de contentor Docker aprovisionada como um serviço de contentores Azure baseado em Kubernetes.||
| Tipo de oferta: Dynamics 365 Business Central| Encomenda| Utilize este tipo de oferta quando a sua solução estiver integrada com a Dynamics 365 para Finanças e Operações| |
| Tipo de oferta: Dinâmica 365 para envolvimento com o cliente | Encomenda| Utilize este tipo de oferta quando a sua solução estiver integrada com a Dynamics 365 para o Envolvimento do Cliente.||
| Tipo de oferta: Módulo IoT Edge | Encomenda| Os módulos Azure IoT Edge são as unidades de computação mais pequenas geridas pelo IoT Edge e podem conter serviços da Microsoft (como o Azure Stream Analytics), serviços de terceiros ou o seu próprio código específico de solução. |
| Tipo de oferta: Aplicação Power BI | Encomenda| Utilize o tipo de oferta de aplicação Power BI quando implementar uma aplicação integrada com o Power BI.|  |
| Tipo de oferta: Aplicação SaaS| Encomenda| Utilize o tipo de oferta de aplicações SaaS para permitir ao seu cliente comprar a sua solução técnica baseada no SaaS como subscrição.||
| Tipo de oferta: Máquina Virtual | Encomenda| Utilize o tipo de oferta de máquina virtual quando colocar um aparelho virtual na subscrição associada ao seu cliente.||
| Tipo de oferta: Extensão do Mercado do Estúdio Visual  | Encomenda| Tipo de oferta anteriormente disponível para desenvolvedores de extensão Azure DevOps. Os desenvolvedores de extensão Azure DevOps podem vender a sua extensão diretamente aos clientes. As ofertas de extensão podem ser configuradas como pagas ou incluindo um julgamento. |
| Data de cancelamento de encomenda| Encomenda| A data em que a encomenda do Marketplace foi cancelada.||
| ID da Encomenda| Encomenda| O identificador único da encomenda do cliente para o seu serviço Marketplace. As ofertas baseadas na utilização da Máquina Virtual não estão associadas a uma encomenda.| |
| Data de Compra de Encomendas| Encomenda| A data em que a Ordem do Mercado foi criada.|||
| Estado da Encomenda| Encomenda| O estado de uma encomenda do Marketplace na altura em que os dados foram atualizados pela última vez.|     |
| Estado da encomenda: Ativo  | Encomenda| O cliente comprou uma encomenda e não cancelou a encomenda.|         |
| Estado da Encomenda: cancelado | Encomenda| O cliente previamente adquiriu uma encomenda e posteriormente cancelou a sua encomenda.||
| Email do fornecedor| Cliente| O endereço de e-mail do fornecedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma Enterprise através do Revendedor, este será o revendedor. Se estiver envolvido um Fornecedor de Soluções cloud (CSP), este será o CSP.|
| Nome do fornecedor| Cliente| O nome do fornecedor envolvido na relação entre a Microsoft e o cliente final. Se o cliente for uma Enterprise através do Revendedor, este será o revendedor. Se estiver envolvido um Fornecedor de Soluções cloud (CSP), este será o CSP.|
| SKU| Encomenda| Nome SKU definido durante a publicação. Uma oferta pode ter muitos SKUs, mas um SKU só pode ser associado a uma única oferta.||
| Data final do julgamento| Encomenda| A data em que o período experimental desta ordem terminará ou terminou.||

## <a name="next-steps"></a>Passos seguintes

- Para uma visão geral dos relatórios de análise disponíveis no mercado comercial do Partner Center, consulte [o Analytics para o mercado comercial no Partner Center](./analytics.md).
- Para gráficos, tendências e valores de dados agregados que resumam a atividade do mercado para a sua oferta, consulte [Summary Dashboard na análise do mercado comercial.](./summary-dashboard.md)
- Para obter informações sobre as suas encomendas num formato gráfico e descarregável, consulte [o Painel de Encomendas na análise do mercado comercial](./orders-dashboard.md).
- Para máquina virtual (VM) oferece métricas de utilização e faturação medida, consulte [o Usage Dashboard na análise do mercado comercial](./usage-dashboard.md).
- Para obter informações detalhadas sobre os seus clientes, incluindo tendências de crescimento, consulte o [Customer Dashboard na análise do mercado comercial.](./customer-dashboard.md)
- Para obter uma lista dos seus pedidos de descarregamento ao longo dos últimos 30 dias, consulte [Downloads Dashboard na análise do mercado comercial](./downloads-dashboard.md).
- Para ver uma visão consolidada do feedback dos clientes para as ofertas no Azure Marketplace e appSource, consulte [ratings e avaliações de dashboard na análise do mercado comercial.](./ratings-reviews.md)
