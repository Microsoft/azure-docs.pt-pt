---
title: Obter a utilização do Azure com as APIs de Faturação do Azure
description: Saiba mais sobre as APIs de Utilização do Azure e as APIs RateCard, que são utilizadas para fornecer informações sobre o consumo de recursos e as tendências do Azure.
author: tonguyen
ms.reviewer: mumami
tags: billing
ms.service: cost-management-billing
ms.subservice: common
ms.topic: reference
ms.date: 02/12/2020
ms.author: banders
ms.custom: seodec18
ms.openlocfilehash: 15e8a31c7ca3b87a8bf92a520bcf07de1e063efd
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98599143"
---
# <a name="use-azure-billing-apis-to-programmatically-get-insight-into-your-azure-usage"></a>Utilizar APIs de Faturação do Azure para obter informações sobre a sua utilização do Azure de forma programática
Utilize as APIs de Faturação do Azure para extrair dados de utilização e de recursos para as suas ferramentas preferidas de análise de dados. As APIs de Utilização de Recursos do Azure e de RateCard podem ajudá-lo a prever e gerir os seus custos com precisão. As APIs são implementadas como um Fornecedor de Recursos e fazem parte da família de APIs expostas pelo Azure Resource Manager.  

## <a name="azure-invoice-download-api-preview"></a>API de Transferência de Faturas do Azure (Pré-visualização)
Assim que a [opção ativa de participação estiver concluída](manage-billing-access.md#opt-in), transfira as faturas com a versão de pré-visualização da [API de Faturas](/rest/api/billing). As funcionalidades incluem:

* **Controlo de acesso baseado em funções do Azure (RBAC do Azure)** – configure políticas de acesso no [portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azure/) para especificar que utilizadores ou aplicações podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
* **Filtragem por Data** – utilize o parâmetro `$filter` para obter todas as faturas em ordem cronológica inversa por data de fim do período da fatura.

> [!NOTE]
> esta funcionalidade está na primeira versão da pré-visualização e pode estar sujeita a alterações das versões anteriores incompatíveis. Atualmente, não está disponível para determinadas ofertas de subscrição (EA, CSP, AIO não suportados) nem para o Azure Alemanha.

## <a name="azure-resource-usage-api-preview"></a>API de Utilização de Recursos do Azure (Pré-visualização)
Utilize a [API Utilização de Recursos](/previous-versions/azure/reference/mt219003(v=azure.100)) do Azure para obter os dados de consumo estimados do Azure. A API inclui:

* **Controlo de acesso baseado em funções do Azure (RBAC do Azure)** – configure políticas de acesso no [portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azure/) para especificar que utilizadores ou aplicações podem ter acesso aos dados de utilização da subscrição. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor de Faturação, Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
* **Agregações por hora ou dia** – os Chamadores podem especificar se querem os dados da utilização do Azure em registos por hora ou em registos por dia. A predefinição é por dia.
* **Metadados de instância (inclui etiquetas de recursos)** – obtenha detalhes ao nível da instância, como o URI do recurso totalmente qualificado (/subscriptions/{subscription-id}/..), as informações do grupo de recursos e as etiquetas de recursos. Estes metadados ajudam a alocar, de forma determinística e programática, a utilização por etiquetas, para os casos de utilização como a cobrança cruzada.
* **Metadados de recursos** – os detalhes dos recursos como o nome do medidor, a categoria do medidor, a subcategoria do medidor, a unidade e a região permitem que o chamador tenha uma melhor compreensão do que foi consumido. Também estamos a trabalhar para alinhar a terminologia de metadados de recursos entre o portal do Azure, o CSV de utilização do Azure, o CSV de faturação do EA e outras experiências destinadas ao público, de modo a permitir que correlacione os dados entre experiências.
* **Utilização para diferentes tipos de oferta** – Os dados de utilização estão disponíveis para tipos de oferta como Pay as you go, MSDN, Pré-pagamento do Azure (que antes se chamava alocação monetária), crédito do Pré-pagamento do Azure e EA, exceto [CSP](/partner-center).

## <a name="azure-resource-ratecard-api-preview"></a>API RateCard de Recursos do Azure (Pré-visualização)
Utilize a [API RateCard de Recursos do Azure](/previous-versions/azure/reference/mt219005(v=azure.100)) para obter a lista dos recursos do Azure disponíveis e as informações de preços estimados de cada um. A API inclui:

* **Controlo de acesso baseado em funções do Azure (RBAC do Azure)** – configure as políticas de acesso no [portal do Azure](https://portal.azure.com) ou através dos [cmdlets do Azure PowerShell](/powershell/azure/) para especificar que utilizadores ou aplicações podem ter acesso aos dados do RateCard. Os chamadores devem utilizar tokens do Azure Active Directory padrão para a autenticação. Adicione o chamador à função Leitor, Proprietário ou Contribuidor para obter acesso aos dados de utilização de uma subscrição específica do Azure.
* **Suporte para Pay as you go, MSDN, Pré-pagamento do Azure e ofertas de crédito do Pré-pagamento do Azure (EA e [CSP](/partner-center) não são suportados)** . Esta API fornece informações de tarifas ao nível de oferta do Azure.  O chamador desta API deve transmitir as informações da oferta para obter os detalhes dos recursos e as tarifas. Atualmente, não é possível fornecer as tarifas do EA porque as ofertas do EA têm tarifas personalizadas em cada inscrição.

## <a name="scenarios"></a>Cenários
Aqui estão alguns dos cenários possíveis com a combinação da API de Utilização e da API RateCard:

* **Gastos do Azure durante o mês** – utilize a combinação da API de Utilização e da API RateCard para obter informações mais detalhadas sobre os gastos com a cloud durante o mês. Pode analisar os registos por hora e por dia das estimativas de utilização e de encargos.
* **Configurar alertas** – utilize a API de Utilização e a API RateCard para obter o consumo estimado e os encargos da cloud, e configure alertas monetários ou baseados em recursos.
* **Predição da fatura** – obtenha o consumo estimado e os gastos com a cloud, e aplique os algoritmos de aprendizagem automática para prever qual será o montante da fatura no final do ciclo de faturação.
* **Análise dos custos antes do consumo**  – utilize a API RateCard para prever qual será o montante da fatura no que diz respeito à utilização esperada quando migra as cargas de trabalho para o Azure. Se tiver cargas de trabalho existentes noutras clouds ou em clouds privadas, também poderá mapear a utilização com as tarifas do Azure para obter uma melhor estimativa dos gastos do Azure. Esta estimativa permite-lhe dinamizar a oferta e comparar e ver as diferenças entre os diferentes tipos de oferta para além de Pay As You Go, como o Pré-pagamento do Azure e o crédito do Pré-pagamento do Azure. A API também lhe permite ver as diferenças de custos por região e fazer uma análise hipotética dos custos para o ajudar a tomar decisões em matéria de implementação.
* **Análise de hipóteses** -

  * Pode determinar se é mais económico executar cargas de trabalho noutra região ou noutra configuração de recurso do Azure. Os custos dos recursos do Azure podem ser diferentes com base na região do Azure que está a utilizar.
  * Também pode determinar se outro tipo de oferta do Azure oferece uma melhor tarifa num recurso do Azure.


## <a name="next-steps"></a>Passos seguintes
* Veja os exemplos de código no GitHub:
  * [Exemplo de código da API de Faturas](https://go.microsoft.com/fwlink/?linkid=845124)

  * [Exemplo de código da API de Utilização](https://github.com/Azure-Samples/billing-dotnet-usage-api)

  * [Exemplo de código da API de RateCard](https://github.com/Azure-Samples/billing-dotnet-ratecard-api)

* Para saber mais sobre o Azure Resource Manager, veja [Descrição geral do Azure Resource Manager](../../azure-resource-manager/management/overview.md).