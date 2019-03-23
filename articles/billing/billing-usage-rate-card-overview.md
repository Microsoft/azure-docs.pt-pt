---
title: Obter a utilização do Azure com APIs de faturação do Azure | Documentos da Microsoft
description: Saiba mais sobre a utilização de faturação do Azure e RateCard APIs, que são utilizados para fornecer informações sobre o consumo de recursos do Azure e as tendências.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: 3e817b43-0696-400c-a02e-47b7817f9b77
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 5/10/2018
ms.author: erikre
ms.custom: seodec18
ms.openlocfilehash: cd1688cd9d3d19242800b04e7e29c8875879cffc
ms.sourcegitcommit: 87bd7bf35c469f84d6ca6599ac3f5ea5545159c9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58351580"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Utilizar APIs de faturação do Azure para obter informações sobre sua utilização do Azure programaticamente
Utilize APIs de faturação do Azure para extrair dados de utilização e de recursos em suas ferramentas de análise de dados preferencial. As APIs de Utilização de Recursos do Azure e de RateCard podem ajudá-lo a prever e gerir os seus custos com precisão. As APIs são implementadas como um fornecedor de recursos e a parte da família de APIs expostas pelo Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>API de transferência de faturas do Azure (pré-visualização)
Uma vez a [participar tiver sido concluída](billing-manage-access.md#opt-in), notas fiscais de download com a versão de pré-visualização [API de nota fiscal](/rest/api/billing). As funcionalidades incluem:

* **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [cmdlets do Azure PowerShell](/powershell/azure/overview) para especificar quais os utilizadores ou aplicações podem obter acesso à dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica.
* **Filtragem de datas** -utilize o `$filter` parâmetro para obter todas as notas fiscais em ordem cronológica reversa por data de fim do período da nota fiscal.

> [!NOTE]
> Esta funcionalidade é a primeira versão de pré-visualização e pode estar sujeitos a alterações com versões anteriores incompatíveis. Atualmente, ele não está disponível para determinados ofertas de subscrição (EA, CSP, AIO não suportado) e Azure Alemanha.

## <a name="azure-resource-usage-api-preview"></a>Utilização de recursos do Azure API (pré-visualização)
Utilizar o Azure [API de utilização de recursos](https://msdn.microsoft.com/library/azure/mt219003) para colocar os seus dados de consumo do Azure estimado. A API inclui:

* **Controlo de acesso baseado em funções do Azure** -configurar políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [cmdlets do Azure PowerShell](/powershell/azure/overview) para especificar quais os utilizadores ou aplicações podem obter acesso à dados de utilização da subscrição. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor de faturação, leitor, proprietário ou Contribuidor para obter acesso a dados de utilização de uma subscrição do Azure específica.
* **Hora a hora ou agregações diárias** – os autores de chamadas podem especificar se eles querem os dados de utilização do Azure hora a hora buckets ou buckets de diário. A predefinição é diário.
* **Metadados de instância (inclui as etiquetas de recursos)** – obter detalhes de nível de instância, como o uri de recurso completamente qualificado (/subscriptions/ {subscrição-id} /...), as informações do grupo de recursos e as etiquetas de recursos. Estes metadados ajudam-o a forma determinista e por meio de programação alocar utilização por etiquetas, para casos de utilização como a cobrança de consumo entre.
* **Metadados do recurso** -detalhes do recurso, como o nome do medidor, categoria do medidor, subcategoria do medidor, unidade e a região dar o autor da chamada uma melhor compreensão sobre o que foi consumido. Estamos também a trabalhar para alinhar a terminologia de metadados do recurso em todo o portal do Azure, utilização do Azure no. CSV, EA CSV e outras experiências de destinado ao público, para que possa correlacionar dados em experiências de faturação.
* **Utilização para tipos de oferta diferente** – os dados de utilização estão disponíveis para tipos de oferta, como o pay as you go, o MSDN, o compromisso monetário, o crédito monetário e o EA, exceto [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-invoice#retrieve-usage-data-for-a-specific-subscription).

## <a name="azure-resource-ratecard-api-preview"></a>Recursos do Azure RateCard API (pré-visualização)
Utilize o [API de RateCard de recursos do Azure](https://msdn.microsoft.com/library/azure/mt219005) para obter a lista de recursos do Azure disponíveis e informações de preços estimadas para cada um. A API inclui:

* **Controlo de acesso baseado em funções do Azure** -configurar as políticas de acesso no [portal do Azure](https://portal.azure.com) ou através de [cmdlets do Azure PowerShell](/powershell/azure/overview) para especificar quais os utilizadores ou aplicações podem obter acesso à Dados de RateCard. Os autores de chamadas tem de utilizar tokens padrão do Azure Active Directory para autenticação. Adicione o autor da chamada à função seja o leitor, proprietário ou Contribuidor para obter acesso a dados de utilização para uma subscrição específica do Azure.
* **Suporte para pay as you go, MSDN, alocação monetária e ofertas de crédito monetário (EA e [CSP](https://docs.microsoft.com/azure/cloud-solution-provider/billing/azure-csp-pricelist#get-prices-by-using-the-azure-rate-card) não suportado)** -esta API fornece as informações de taxas de nível de oferta do Azure.  O autor da chamada desta API têm de introduzir a informações sobre as ofertas para obter detalhes do recurso e taxas. Não é atualmente possível fornecer tarifas do EA, porque as ofertas EA personalizaram taxas por inscrição.

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários que são possíveis com a combinação da utilização e as APIs RateCard:

* **Gastos do Azure durante o mês** – utilize a combinação da utilização e APIs RateCard para obter melhores informações sobre a sua cloud gastar durante o mês. Pode analisar os registos por hora e diários das estimativas de utilização e custos.
* **Configure alertas** – utilizar a utilização e as APIs RateCard para obter o consumo na cloud estimado e os encargos e posso configurar alertas com base em recursos ou monetário com base em.
* **Prever a fatura** – Get seu consumo estimado e a cloud de gastos em aplicam algoritmos de machine learning para prever o que a cobrar seria no final do ciclo de faturação.
* **Pré-consumo de análise de custo** – utilizar a API de RateCard para prever quanto será para a sua utilização esperada ao mover as cargas de trabalho para o Azure. Se tiver cargas de trabalho existentes em outras clouds ou nuvens privadas, também pode mapear a utilização com o Azure gastar de taxas para obter uma melhor estimativa do Azure. Esta estimativa dá-lhe a capacidade para dinamizar na oferta e a comparação e contraste entre os tipos de oferta diferente, além de pay as you go, como um compromisso e o crédito monetário. A API também dá-lhe a capacidade de ver as diferenças de custo por região e permite-lhe efetuar uma análise de hipóteses custo para o ajudar a tomar decisões de implementação.
* **Análise de hipóteses** -

  * Pode determinar se é mais rentável para executar cargas de trabalho noutra região ou em outra configuração do recurso do Azure. Os custos de recursos do Azure podem divergir com base na região do Azure que está a utilizar.
  * Também pode determinar se o outro tipo de oferta do Azure fornece uma melhor taxa sobre um recurso do Azure.


## <a name="next-steps"></a>Passos Seguintes
* Veja os exemplos de código no GitHub:
  * [Exemplo de código da API de Faturas](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Exemplo de código da API de Utilização](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Exemplo de código da API de RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Para saber mais sobre o Azure Resource Manager, veja [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).
