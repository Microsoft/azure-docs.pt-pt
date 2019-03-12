---
title: Cloud Cruiser e integração de APIS de faturação do Microsoft Azure | Documentos da Microsoft
description: Fornece uma perspectiva exclusiva de faturação do Microsoft Azure parceiro Cloud Cruiser, em suas experiências integrar as APIs de faturação do Azure em seu produto.  Isso é especialmente útil para os clientes do Azure e o Cloud Cruiser que estejam interessados em utilizar/experimentar o Cloud Cruiser para o Microsoft Azure Pack.
services: ''
documentationcenter: ''
author: tonguyen
manager: mumami
editor: ''
tags: billing
ms.assetid: b65128cf-5d4d-4cbd-b81e-d3dceab44271
ms.service: billing
ms.devlang: na
ms.topic: reference
ms.tgt_pltfrm: na
ms.workload: billing
ms.date: 10/09/2017
ms.author: erikre
ms.openlocfilehash: bae35f9e153227ddcca6e0d20ce862317b968eb1
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57536016"
---
# <a name="cloud-cruiser-and-microsoft-azure-billing-api-integration"></a>Cloud Cruiser e integração de APIS de faturação do Microsoft Azure
Este artigo descreve como as informações recolhidas a partir do novo Microsoft Azure APIs de faturação podem ser usadas na Cloud Cruiser para análise e simulação de custo de fluxo de trabalho.

## <a name="azure-ratecard-api"></a>API de RateCard do Azure
A API de RateCard fornece informações de taxa do Azure. Depois de autenticar com as credenciais corretas, pode consultar a API para recolher os metadados sobre os serviços disponíveis no Azure, juntamente com as taxas associadas à sua oferta de ID.

A resposta de exemplo seguinte é a partir da API que mostra os preços para o A0 instância (Windows):

    {
        "MeterId": "0e59ad56-03e5-4c3d-90d4-6670874d7e29",
        "MeterName": "Compute Hours",
        "MeterCategory": "Virtual Machines",
        "MeterSubCategory": "A0 VM (Windows)",
        "Unit": "Hours",
        "MeterRates":
        {
            "0": 0.029
        },
        "EffectiveDate": "2014-08-01T00:00:00Z",
        "IncludedQuantity": 0.0,
        "MeterStatus": "Active"
    },

### <a name="cloud-cruisers-interface-to-azure-ratecard-api"></a>Cloud Interface da Cruiser para a API de RateCard do Azure
Cloud Cruiser pode utilizar as informações de API de RateCard de formas diferentes. Neste artigo, vamos mostrar como ele pode ser usado para tornar o IaaS simulação e análise de custos da carga de trabalho.

Para demonstrar este caso de utilização, imagine uma carga de trabalho de várias instâncias em execução no Microsoft Azure Pack (WAP). O objetivo é simular esta mesma carga de trabalho no Azure e estimar os custos de fazer essa migração. Para criar esta simulação, existem duas tarefas principais a serem executadas:

1. **Importar e processar as informações de serviço recolhidas a partir da API de RateCard.** Esta tarefa também é executada em pastas de trabalho, onde a extração da API de RateCard é transformada e publicada um novo plano de tarifa. Este novo plano de taxa é utilizado em simulações para estimar os preços do Azure.
2. **Normalizar os serviços WAP e serviços do Azure para o IaaS.** Por predefinição, os serviços WAP são baseados em recursos individuais (CPU, tamanho da memória, o tamanho do disco, etc.) enquanto o Azure services são baseados no tamanho de instância (A0, A1, A2, etc.). Esta primeira tarefa pode ser efetuada pelo motor ETL da Cloud Cruiser, chamado de pastas de trabalho, onde estes recursos podem ser agrupados em tamanhos de instância, análogos aos serviços de instância do Azure.

### <a name="import-data-from-the-ratecard-api"></a>Importar dados a partir da API de RateCard
Pastas de trabalho do cloud Cruiser fornecem uma forma de coletar e processar informações a partir da API de RateCard.  Pastas de trabalho do ETL (extrair-transformar-carregar) permitem-lhe configurar a coleção, transformação e publicação de dados no banco de dados de Cloud Cruiser.

Cada pasta de trabalho pode ter uma ou várias coleções, permitindo-lhe correlacionar as informações de diferentes origens, para complementar ou aumentar os dados de utilização. As duas capturas de ecrã seguintes mostram como criar uma nova *recolha* numa pasta de trabalho e importar informações para o *coleção* da API de RateCard:

![Figura 1 – criar uma nova coleção][1]

![Figura 2 – importar dados da nova coleção][2]

Depois de importar os dados para a pasta de trabalho, é possível criar várias etapas e os processos de transformação, modificar e modelar os dados. Neste exemplo, uma vez que estamos interessados apenas na infraestrutura-como-serviço (IaaS), que pode utilizar os passos de transformação para remover linhas desnecessárias ou registos, relacionados com serviços diferentes IaaS.

Captura de ecrã seguinte mostra os passos de transformação utilizados para processar os dados recolhidos a partir da API de RateCard:

![Figura 3 – passos de transformação para processar os dados recolhidos a partir da API RateCard][3]

### <a name="defining-new-services-and-rate-plans"></a>Definir a taxa de novos serviços e planos
Existem diferentes formas de definir os serviços em Cloud Cruiser. Uma das opções é importar os serviços de dados de utilização. Esse método é usado normalmente ao trabalhar com clouds públicas, onde os serviços já são definidos pelo provedor.

Um plano de taxa é um conjunto de tarifas ou os preços que podem ser aplicados a serviços diferentes, com base nas datas efetivas, ou um grupo de clientes, entre outras coisas. Planos de tarifas também podem ser utilizados no Cloud Cruiser para criar a simulação ou cenários de "What-if", para compreender como as alterações nos serviços podem afetar o custo total de uma carga de trabalho.

Neste exemplo, usamos as informações de serviço da API de RateCard para definir novos serviços em Cloud Cruiser. Da mesma forma, podemos usar as taxas associadas aos serviços para criar um novo plano de taxa em Cloud Cruiser.

No final do processo de transformação, é possível criar um novo passo e publicar os dados a partir da API de RateCard como novos serviços e taxas.

![Figura 4 – publicar os dados a partir da API de RateCard como novos serviços e as taxas de][4]

### <a name="verify-azure-services-and-rates"></a>Certifique-se de que os serviços do Azure e tarifas
Depois de publicar os serviços e as taxas, pode verificar a lista de serviços importados no Cloud Cruiser *serviços* separador:

![Figura 5 – verificar os novos serviços][5]

Sobre o *planos de taxa* separador, pode verificar o novo plano de tarifas, chamado "AzureSimulation" com as taxas de importados a partir da API de RateCard.

![Figura 6 – verificar o novo plano de taxa e as taxas associadas][6]

### <a name="normalize-wap-and-azure-services"></a>Normalizar WAP e serviços do Azure
Por predefinição, o WAP fornece informações de utilização com base na utilização de computação, memória e recursos de rede. No Cloud Cruiser, pode definir os seus serviços com base diretamente na alocação ou utilização limitada desses recursos. Por exemplo, pode definir uma taxa de básica para cada hora de utilização da CPU ou cobrar os GB de memória alocada a uma instância.

Neste exemplo, para comparar os custos entre WAP e o Azure, é necessário agregar a utilização de recursos no WAP em pacotes, que, em seguida, podem ser mapeados para os serviços do Azure. Essa transformação pode ser facilmente implementada nos livros:

![Figura 7 - transformar dados WAP normalizar serviços][7]

A última etapa na pasta de trabalho é publicar os dados da base de dados de Cloud Cruiser. Durante este passo, os dados de utilização é agora incluídos para os serviços (que são mapeiam para os serviços do Azure) e estão associados ao taxas padrão para criar os custos.

Depois de concluir a pasta de trabalho, pode automatizar o processamento dos dados, ao adicionar uma tarefa no scheduler e especificar a frequência e a hora para a pasta de trabalho ser executado.

![Figura 8 – agendamento de livro][8]

### <a name="create-reports-for-workload-cost-simulation-analysis"></a>Criar relatórios para análise de custo de simulação de carga de trabalho
Depois da utilização é recolhida e custos são carregados para a base de dados de Cloud Cruiser, podemos usar o módulo de Cloud Cruiser Insights para criar a carga de trabalho simulação que queremos de custos.

Para demonstrar este cenário, criamos o relatório seguinte:

![Comparação de custo][9]

O gráfico superior mostra uma comparação do custo por serviços, comparando o preço de executar a carga de trabalho para cada serviço específico entre WAP (azul escuro) e o Azure (azul claro).

O gráfico na parte inferior mostra os mesmos dados, mas discriminados por departamento. Os custos para cada departamento executar sua carga de trabalho no WAP e no Azure, juntamente com a diferença entre eles é apresentado na barra de economia (verde).

## <a name="azure-usage-api"></a>API de utilização do Azure
### <a name="introduction"></a>Introdução
Recentemente, a Microsoft introduziu a API de utilização do Azure, permitindo que os subscritores enviar programaticamente os dados de utilização para obter informações sobre o respetivo consumo. Os clientes do cloud Cruiser podem tirar partido do conjunto de dados mais avançado disponível através desta API.

Cloud Cruiser pode utilizar a integração com a API de utilização de várias formas. A granularidade (informações de utilização por hora) e informações de metadados de recursos disponíveis através da API fornecem o conjunto de dados necessário para oferecer suporte a modelos de análise de custos ou estorno flexíveis. 

Neste tutorial, apresentamos um exemplo de como o Cloud Cruiser pode beneficiar das informações de API de utilização. Mais especificamente, podemos irá criar um grupo de recursos no Azure, associe etiquetas para a estrutura de conta, em seguida, descrevem o processo de extrair e processar as informações de marca em Cloud Cruiser.

O objetivo final é ser capaz de criar relatórios como o seguinte e ser capaz de analisar o custo e o consumo com base na estrutura de conta preenchida pelas marcas.

![Figura 10 - relatório com divisões utilizando etiquetas][10]

### <a name="microsoft-azure-tags"></a>Microsoft Azure Tags
Os dados disponíveis por meio da API de utilização do Azure incluem não apenas informações de consumo, mas também metadados de recursos, incluindo todas as marcas associadas a ele. As etiquetas proporcionam uma forma fácil para organizar os recursos, mas para ser Efetivo, terá de se certificar de que:

* As etiquetas são aplicadas corretamente aos recursos na hora de aprovisionamento
* As etiquetas são corretamente utilizadas sobre o processo de análise de custos/estorno para se ligarem a utilização a estrutura de conta da organização.

Esses dois requisitos podem ser desafiantes, especialmente quando há um processo manual no aprovisionamento ou a cobrança de consumo de lado. As etiquetas de escrita incorretamente, incorretas ou até mesmo em falta são queixas comuns de clientes, quando utilizar etiquetas e estes erros pode tornar a vida no lado do charging difícil.

Com a nova API de utilização do Azure, Cloud Cruiser pode extrair informações de identificação de recurso e, por meio de uma ferramenta ETL sofisticada chamada pastas de trabalho, corrigir esses erros comuns de etiquetagem. Por meio da transformação usando expressões regulares e correlação de dados, o Cloud Cruiser pode identificar recursos incorretamente com etiquetas e aplicar as etiquetas corretas, garantindo a associação correta dos recursos com o consumidor.

No lado do charging, o Cloud Cruiser automatiza o processo de análise de custos/estorno e pode utilizar as informações de marca para se ligarem a utilização para o consumidor apropriado (departamento, divisão, projeto, etc.). Esta automatização fornece uma grande melhoria e pode certificar-se de um processo charging auditável e consistente.

### <a name="creating-a-resource-group-with-tags-on-microsoft-azure"></a>Criar um grupo de recursos com etiquetas no Microsoft Azure
Neste tutorial, a primeira etapa é criar um grupo de recursos no portal do Azure, em seguida, criar novas marcas para associar aos recursos. Neste exemplo, podemos criar as seguintes tags: Proprietário de departamento, ambiente, e o projeto.

Captura de ecrã seguinte mostra um exemplo de grupo de recursos com as etiquetas associadas.

![Figura 11 - grupo de recursos com etiquetas associadas no portal do Azure][11]

A próxima etapa é obter as informações a partir da API de utilização para o Cloud Cruiser. Atualmente, a API de utilização fornece respostas no formato JSON. Eis um exemplo dos dados recuperados:

    {
      "id": "/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXX/providers/Microsoft.Commerce/UsageAggregates/Daily_BRSDT_20150623_0000",
      "name": "Daily_BRSDT_20150623_0000",
      "type": "Microsoft.Commerce/UsageAggregate",
      "properties":
      {
        "subscriptionId": "bb678b04-0e48-4b44-XXXX-XXXXXXXXX",
        "usageStartTime": "2015-06-22T00:00:00+00:00",
        "usageEndTime": "2015-06-23T00:00:00+00:00",
        "meterName": "Compute Hours",
        "meterRegion": "",
        "meterCategory": "Virtual Machines",
        "meterSubCategory": "Standard_D1 VM (Non-Windows)",
        "unit": "Hours",
        "instanceData": "{\"Microsoft.Resources\":{\"resourceUri\":\"/subscriptions/bb678b04-0e48-4b44-XXXX-XXXXXXXX/resourceGroups/DEMOUSAGEAPI/providers/Microsoft.Compute/virtualMachines/MyDockerVM\",\"location\":\"eastus\",\"tags\":{\"Department\":\"Sales\",\"Project\":\"Demo Usage API\",\"Environment\":\"Test\",\"Owner\":\"RSE\"},\"additionalInfo\":{\"ImageType\":\"Canonical\",\"ServiceType\":\"Standard_D1\"}}}",
        "meterId": "e60caf26-9ba0-413d-a422-6141f58081d6",
        "infoFields": {},
        "quantity": 8

      },
    },


### <a name="import-data-from-the-usage-api-into-cloud-cruiser"></a>Importar dados a partir da API de utilização para o Cloud Cruiser
Pastas de trabalho do cloud Cruiser fornecem uma forma de coletar e processar informações a partir da API de utilização. Uma pasta de trabalho do ETL (extrair-transformar-carregar) permite-lhe configurar a coleção, transformação e publicação de dados no banco de dados de Cloud Cruiser.

Cada pasta de trabalho pode ter uma ou várias coleções. Isto permite-lhe correlacionar as informações de diferentes origens, para complementar ou aumentar os dados de utilização. Neste exemplo, vamos criar uma nova folha de cálculo no livro de modelo do Azure (*UsageAPI)* e defina uma nova *coleção* para importar informações a partir da API de utilização.

![Figura 3 – os dados de API de utilização importados para a folha de UsageAPI][12]

Tenha em atenção que este livro já tem outras folhas para importar os serviços do Azure (*ImportServices*) e processar as informações de consumo da API de faturação (*PublishData*).

Em seguida, usamos a API de utilização para preencher os *UsageAPI* sheet e correlacionar as informações com os dados de consumo da API de faturação no *PublishData* folha.

### <a name="processing-the-tag-information-from-the-usage-api"></a>Processamento de informações da etiqueta da API de utilização
Depois de importar os dados para o livro, podemos criar passos de transformação no *UsageAPI* folha para processar as informações da API. Primeiro passo é usar um processador de "Dividir JSON" para extrair as etiquetas de um único campo e, em seguida, criar campos para cada um deles (departamento, projeto, proprietário e ambiente).

![Figura 4 - criar novos campos para obter as informações de etiqueta][13]

Observe que o serviço de "Redes" está em falta as informações de marca (caixa de amarela), mas podemos verificar de que ele faz parte do mesmo grupo de recursos ao observar a *ResourceGroupName* campo. Uma vez que temos as marcas para os outros recursos deste grupo de recursos, podemos usar essas informações para aplicar as etiquetas em falta para este recurso posteriormente no processo.

A próxima etapa é criar uma tabela de pesquisa, associando as informações das marcas de para o *ResourceGroupName*. Esta tabela de referência é utilizada no próximo passo para enriquecer os dados de consumo com informações da etiqueta.

### <a name="adding-the-tag-information-to-the-consumption-data"></a>Adicionar as informações de marca para os dados de consumo
Agora, podemos saltar para o *PublishData* folha, que processa as informações de consumo da API de faturação e adicione os campos extraídos de etiquetas. Este processo é realizado ao consultar a tabela de pesquisa criada no passo anterior, utilizando o *ResourceGroupName* como a chave para as pesquisas.

![Figura 5 - preencher a estrutura de conta com as informações das pesquisas][14]

Tenha em atenção que os campos de estrutura de conta apropriada para o serviço de "Redes" foram aplicados, corrigir o problema com as etiquetas em falta. Também preenchemos os campos de estrutura de conta para os recursos que não seja o nosso grupo de recursos de destino com "Other", para diferenciá-los nos relatórios.

Agora precisamos apenas adicionar um passo para publicar os dados de utilização. Durante este passo, as taxas de segurança para cada serviço definido no nosso plano de taxa aplicada a informações de utilização, com a cobrança resultante carregada no banco de dados.

A melhor parte é que precisa apenas passar por esse processo, uma vez. Quando o livro estiver concluído, basta adicioná-lo para o agendador e é executada de hora a hora ou diariamente à hora agendada. Em seguida, é apenas uma questão de criar novos relatórios ou personalizar os existentes, para analisar os dados para obter informações significativas da sua utilização na cloud.

### <a name="next-steps"></a>Próximos Passos
* Para obter instruções detalhadas sobre como criar pastas de trabalho de Cloud Cruiser e relatórios, consulte o Cloud Cruiser online [documentação](https://docs.cloudcruiser.com/) (início de sessão válido obrigado).  Para obter mais informações sobre o Cloud Cruiser, contacte [ info@cloudcruiser.com ](mailto:info@cloudcruiser.com).
* Ver [obter informações sobre o consumo de recursos do Microsoft Azure](billing-usage-rate-card-overview.md) para uma descrição geral da utilização de recursos do Azure e RateCard APIs.
* Veja a [referência de API de REST de faturação do Azure](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) para obter mais informações sobre as duas APIs, o que fazem parte do conjunto de APIs fornecidas pelo Azure Resource Manager.
* Se gostaria de mergulhar no código de exemplo, confira nossos exemplos código da API de faturação do Azure do Microsoft no [exemplos de código do Azure](https://azure.microsoft.com/documentation/samples/?term=billing).

### <a name="learn-more"></a>Saiba Mais
* Para saber mais sobre o Azure Resource Manager, consulte a [descrição geral do Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) artigo.

<!--Image references-->

[1]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Create-New-Workbook-Collection.png "Figura 1 – criar uma nova coleção"
[2]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Import-Data-From-RateCard.png "Figura 2 – importar dados da nova coleção"
[3]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transformation-Steps-Process-RateCard-Data.png "Figura 3 – passos de transformação para processar os dados recolhidos a partir da API RateCard"
[4]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Publish-RateCard-Data-New-Services-Rates.png "Figura 4 – publicar os dados a partir da API de RateCard como novos serviços e as taxas de"
[5]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing1.png "Figura 5 – verificar os novos serviços"
[6]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Verify-Azure-Services-And-Pricing2.png "Figura 6 – verificar o novo plano de taxa e as taxas associadas"
[7]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Transforming-WAP-Normalize-Services.png "Figura 7 - transformar dados WAP normalizar serviços"
[8]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workbook-Scheduling.png "Figura 8 – agendamento de livro"
[9]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/Workload-Cost-Simulation-Report.png "Figura 9 - relatório de exemplo para o cenário de comparação do custo de carga de trabalho"
[10]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/1_ReportWithTags.png "Figura 10 - relatório com divisões utilizando etiquetas"
[11]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/2_ResourceGroupsWithTags.png "Figura 11 - grupo de recursos com etiquetas associadas no portal do Azure"
[12]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/3_ImportIntoUsageAPISheet.png "Figura 12 - dados de API de utilização importados para a folha de UsageAPI"
[13]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/4_NewTagField.png "Figura 13 - crie novos campos para obter as informações de etiqueta"
[14]: ./media/billing-usage-rate-card-partner-solution-cloudcruiser/5_PopulateAccountStructure.png "Figura 14 - preencher a estrutura de conta com as informações das pesquisas"
