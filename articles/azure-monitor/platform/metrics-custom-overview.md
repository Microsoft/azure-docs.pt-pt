---
title: Métricas personalizadas no Monitor Azure (Pré-visualização)
description: Saiba mais sobre métricas personalizadas no Monitor Azure e como são modeladas.
author: ancav
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 04/23/2020
ms.subservice: metrics
ms.openlocfilehash: 4891d7272516caf4944219907d81ee4fb89e0189
ms.sourcegitcommit: 11572a869ef8dbec8e7c721bc7744e2859b79962
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/05/2020
ms.locfileid: "82837316"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Métricas personalizadas no Monitor Azure (Pré-visualização)

À medida que implementa recursos e aplicações em Azure, vai querer começar a recolher telemetria para obter informações sobre o seu desempenho e saúde. O Azure disponibiliza-te algumas métricas da caixa. Estas métricas são chamadas [de padrão ou plataforma.](https://docs.microsoft.com/azure/azure-monitor/platform/metrics-supported) No entanto, são de natureza limitada. 

Você pode querer recolher alguns indicadores de desempenho personalizados ou métricas específicas do negócio para fornecer insights mais profundos. Estas métricas **personalizadas** podem ser recolhidas através da telemetria da sua aplicação, um agente que funciona com os seus recursos Azure, ou mesmo um sistema de monitorização externo e submetido diretamente ao Monitor Azure. Depois de publicados no Azure Monitor, pode navegar, consultar e alertar sobre métricas personalizadas para os seus recursos e aplicações Azure lado a lado com as métricas padrão emitidas pelo Azure.

As métricas personalizadas do Azure Monitor estão em vigor na pré-visualização pública. 

## <a name="methods-to-send-custom-metrics"></a>Métodos para enviar métricas personalizadas

As métricas personalizadas podem ser enviadas para o Monitor Azure através de vários métodos:
- Instrumente a sua aplicação utilizando o Azure Application Insights SDK e envie telemetria personalizada para o Monitor Azure. 
- Instale a extensão de Diagnósticos Windows Azure (WAD) no seu [Azure VM,](collect-custom-metrics-guestos-resource-manager-vm.md)conjunto de [escala de máquinavirtual,](collect-custom-metrics-guestos-resource-manager-vmss.md) [VM clássico](collect-custom-metrics-guestos-vm-classic.md)ou [serviços de nuvem clássicos](collect-custom-metrics-guestos-vm-cloud-service-classic.md) e envie contadores de desempenho para o Monitor Azure. 
- Instale o [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) no seu VM Azure Linux e envie métricas utilizando o plug-in de saída do Monitor Azure.
- Envie métricas personalizadas [diretamente para a API REST Do Monitor Azure,](../../azure-monitor/platform/metrics-store-custom-rest-api.md) `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

## <a name="pricing-model-and-rentention"></a>Modelo de preços e rentenção

Verifique a página de preços do [Monitor Azure](https://azure.microsoft.com/pricing/details/monitor/) para obter detalhes sobre quando a faturação estará ativada para consultas personalizadas e métricas. Detalhes específicos do preço para todas as métricas, incluindo métricas personalizadas e consultas métricas estão disponíveis nesta página. Em resumo, não há qualquer custo para ingerir métricas padrão (métricas de plataforma) na loja de métricas Do Monitor De Azure, mas as métricas personalizadas incorrerão em custos quando entrarem na disponibilidade geral. Consultas métricas de API incurr custos.

As métricas personalizadas são mantidas pelo mesmo período de tempo que as [métricas da plataforma.](data-platform-metrics.md#retention-of-metrics) 

> [!NOTE]  
> As métricas enviadas para o Monitor Azure através do SDK de Insights de Aplicação são faturadas como dados de registo ingeridos. Só incorrem em cargas métricas adicionais se o recurso Informações de Aplicação [permitir alertar sobre dimensões métricas personalizadas.](https://docs.microsoft.com/azure/azure-monitor/app/pre-aggregated-metrics-log-metrics#custom-metrics-dimensions-and-pre-aggregation) Esta caixa de verificação envia dados para a base de dados de métricas do Monitor Azure utilizando as métricas personalizadas API para permitir o alerta mais complexo.  Saiba mais sobre o modelo de preços e preços da [Application Insights](https://docs.microsoft.com/azure/azure-monitor/app/pricing#pricing-model) na sua [região.](https://azure.microsoft.com/pricing/details/monitor/)


## <a name="how-to-send-custom-metrics"></a>Como enviar métricas personalizadas

Quando envia métricas personalizadas para o Monitor Azure, cada ponto de dados, ou valor, reportado deve incluir as seguintes informações.

### <a name="authentication"></a>Autenticação
Para submeter métricas personalizadas ao Azure Monitor, a entidade que submete a métrica precisa de um token Azure Ative Directory (Azure AD) válido no cabeçalho do **portador** do pedido. Existem algumas formas suportadas de adquirir um símbolo válido:
1. [Identidades geridas para os recursos Azure.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview) Dá uma identidade a um recurso Azure em si, como um VM. A Identidade de Serviço Gerida (MSI) destina-se a dar permissões de recursos para a realização de determinadas operações. Um exemplo é permitir que um recurso emita métricas sobre si mesmo. Um recurso, ou o seu MSI, pode ser concedido permissões da Editora de Métricas de **Monitorização** noutro recurso. Com esta permissão, o MSI também pode emitir métricas para outros recursos.
2. [Diretor de serviço da AD Azure.](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals) Neste cenário, uma aplicação ou serviço Azure AD pode ser atribuída permissões para emitir métricas sobre um recurso Azure.
Para autenticar o pedido, o Monitor Azure valida a aplicação através da utilização de chaves públicas Azure AD. O papel existente de **Monitoring Metrics Publisher** já tem esta permissão. Está disponível no portal Azure. O diretor de serviço, dependendo dos recursos para os quais emite métricas personalizadas, pode ser dado o papel de Editor de Métricas de **Monitorização** no âmbito necessário. Exemplos são uma subscrição, grupo de recursos ou recurso específico.

> [!TIP]  
> Quando solicitar um token Azure AD para emitir métricas personalizadas, certifique-se de `https://monitoring.azure.com/`que o público ou recurso que o token é solicitado é . Certifique-se de incluir o '/'.

### <a name="subject"></a>Assunto
Esta propriedade captura o qual o iD de recurso Azure a métrica personalizada é reportada. Estas informações serão codificadas no URL da chamada DaPI que está a ser feita. Cada API só pode submeter valores métricos para um único recurso Azure.

> [!NOTE]  
> Não pode emitir métricas personalizadas contra a identificação de recursos de um grupo de recursos ou subscrição.


### <a name="region"></a>Região
Esta propriedade captura a região de Azure o recurso para o qual está a emitir métricas é implantado. As métricas devem ser emitidas para o mesmo ponto final regional do Monitor Azure que a região em que o recurso é implantado. Por exemplo, as métricas personalizadas para um VM implantado nos EUA Ocidentais devem ser enviadas para o ponto final do WestUS Regional Azure Monitor. A informação da região também está codificada no URL da chamada DaPI.

> [!NOTE]  
> Durante a pré-visualização pública, as métricas personalizadas só estão disponíveis num subconjunto de regiões de Azure. Uma lista de regiões apoiadas é documentada numa secção posterior deste artigo.
>
>

### <a name="timestamp"></a>Carimbo de data/hora
Cada ponto de dados enviado ao Monitor Azure deve ser marcado com uma marca temporal. Este carimbo de tempo captura o Tempo de Data em que o valor métrico é medido ou recolhido. O Azure Monitor aceita dados métricos com selos temporais até 20 minutos no passado e 5 minutos no futuro. A marca temporizada deve estar no formato ISO 8601.

### <a name="namespace"></a>Espaço de nomes
Os espaços de nome são uma forma de categorizar ou agrupar métricas semelhantes. Ao utilizar espaços de nome, pode alcançar o isolamento entre grupos de métricas que podem recolher diferentes insights ou indicadores de desempenho. Por exemplo, você pode ter um espaço de nome chamado **contosomemorymetrics** que rastreia métricas de uso de memória que perfilam a sua aplicação. Outro espaço de nome chamado **contosoapptransaction** pode rastrear todas as métricas sobre transações de utilizadores na sua aplicação.

### <a name="name"></a>Name
**O nome** é o nome da métrica que está a ser reportada. Normalmente, o nome é descritivo o suficiente para ajudar a identificar o que é medido. Um exemplo é uma métrica que mede o número de bytes de memória usados num dado VM. Pode ter um nome métrico como **Memory Bytes In Use**.

### <a name="dimension-keys"></a>Teclas de dimensão
Uma dimensão é um par de chaves ou valor que ajuda a descrever características adicionais sobre a métrica que está a ser recolhida. Utilizando as características adicionais, pode recolher mais informações sobre a métrica, o que permite informações mais profundas. Por exemplo, a métrica Memory **Bytes In Use** pode ter uma chave de dimensão chamada **Processo** que captura quantos bytes de memória cada processo num VM consome. Ao utilizar esta tecla, pode filtrar a métrica para ver quanto os processos específicos de memória utilizam ou para identificar os cinco principais processos através do uso da memória.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Uma métrica personalizada pode ter até 10 dimensões.

### <a name="dimension-values"></a>Valores de dimensão
Ao reportar um ponto de dados métrico, para cada chave de dimensão na métrica que está a ser reportada, há um valor de dimensão correspondente. Por exemplo, é melhor relatar a memória utilizada pelo ContosoApp no seu VM:

* O nome métrico seria **Memory Bytes in Use**.
* A chave de dimensão seria **processo.**
* O valor de dimensão seria **ContosoApp.exe.**

Ao publicar um valor métrico, só pode especificar um valor de dimensão única por chave de dimensão. Se recolher a mesma utilização de memória para múltiplos processos no VM, pode reportar vários valores métricos para essa marca de tempo. Cada valor métrico especificaria um valor de dimensão diferente para a chave de dimensão do **Processo.**
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Se um post métrico define as teclas de dimensão, os valores de dimensão correspondentes são obrigatórios.

### <a name="metric-values"></a>Valores de métricas
O Azure Monitor armazena todas as métricas em intervalos de granularidade de um minuto. Entendemos que durante um dado minuto, uma métrica pode precisar de ser amostrada várias vezes. Um exemplo é a utilização do CPU. Ou pode ter de ser medido para muitos eventos discretos. Um exemplo são as tardios de transação de inscrição. Para limitar o número de valores brutos que tem de emitir e pagar no Monitor Azure, pode pré-agregar localmente e emitir os valores:

* **Min**: O valor mínimo observado de todas as amostras e medições durante o minuto.
* **Máx**: O valor máximo observado de todas as amostras e medições durante o minuto.
* **Resumo**: A soma de todos os valores observados de todas as amostras e medições durante o minuto.
* **Contagem**: O número de amostras e medições efetuadas durante a hora.

Por exemplo, se houvesse quatro transações de entrada na sua app durante um dado minuto, as tardios medidas resultantes para cada um poderia ser a seguinte:

|Transação 1|Transação 2|Transação 3|Transação 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Em seguida, a publicação métrica resultante ao Monitor Azure seria a seguinte:
* Min: 4
* Máx: 16
* Soma: 40
* Contagem: 4

Se a sua aplicação não puder pré-agregar localmente e necessitar de emitir cada amostra ou evento discreto imediatamente após a recolha, pode emitir os valores de medida bruta. Por exemplo, cada vez que ocorre uma transação de entrada na sua aplicação, publica uma métrica no Monitor Azure com apenas uma medição. Assim, para uma transação de inscrição que demorou 12 ms, a publicação métrica seria a seguinte:
* Min: 12
* Máx: 12
* Soma: 12
* Contagem: 1

Com este processo, pode emitir vários valores para a mesma combinação métrica mais dimensão durante um dado minuto. O Azure Monitor leva então todos os valores brutos emitidos por um dado minuto e agrega-os em conjunto.

### <a name="sample-custom-metric-publication"></a>Publicação métrica personalizada da amostra
No exemplo seguinte, cria-se uma métrica personalizada chamada **Memory Bytes in Use** sob o perfil de **memória** de espaço métrico para uma máquina virtual. A métrica tem uma única dimensão chamada **Processo**. Para o carimbo temporal dado, emitimos valores métricos para dois processos diferentes:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"        ],
        "series": [
          {
            "dimValues": [
              "ContosoApp.exe"
            ],
            "min": 10,
            "max": 89,
            "sum": 190,
            "count": 4
          },
          {
            "dimValues": [
              "SalesApp.exe"
            ],
            "min": 10,
            "max": 23,
            "sum": 86,
            "count": 4
          }
        ]
      }
    }
  }
```
> [!NOTE]  
> Application Insights, a extensão de diagnóstico, e o agente InfluxData Telegraf já estão configurados para emitir valores métricos contra o ponto final regional correto e transportar todas as propriedades anteriores em cada emissão.
>
>

## <a name="custom-metric-definitions"></a>Definições métricas personalizadas
Não há necessidade de pré-definir uma métrica personalizada no Monitor Azure antes de ser emitida. Cada ponto de dados métricos publicado contém informação sobre espaço de nome, nome e dimensão. Assim, a primeira vez que uma métrica personalizada é emitida para o Monitor Azure, uma definição métrica é automaticamente criada. Esta definição métrica é então detetável em qualquer recurso contra o qual a métrica é emitida através das definições métricas.

> [!NOTE]  
> O Monitor Azure ainda não suporta a definição de **Unidades** para uma métrica personalizada.

## <a name="using-custom-metrics"></a>Usando métricas personalizadas
Depois de submetidas métricas personalizadas ao Monitor Azure, pode navegá-las através do portal Azure e consulta-las através das APIs REST do Monitor Azure. Também pode criar alertas sobre eles para notificá-lo quando determinadas condições forem satisfeitas.

> [!NOTE]
> Tem de ser um leitor ou um papel de contribuinte para ver métricas personalizadas.

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Navegue nas suas métricas personalizadas através do portal Azure
1.    Vá ao [portal Azure.](https://portal.azure.com)
2.    Selecione o painel **monitor.**
3.    Selecione **Métricas**.
4.    Selecione um recurso contra o qual emitiste métricas personalizadas.
5.    Selecione o espaço de nome das métricas para a sua métrica personalizada.
6.    Selecione a métrica personalizada.

## <a name="supported-regions"></a>Regiões suportadas
Durante a pré-visualização pública, a capacidade de publicar métricas personalizadas está disponível apenas num subconjunto de regiões azure. Esta restrição significa que as métricas só podem ser publicadas para recursos numa das regiões apoiadas. A tabela seguinte lista o conjunto de regiões azure suportadas para métricas personalizadas. Enumera igualmente os pontos finais correspondentes para os quais as métricas dos recursos nessas regiões devem ser publicadas:

|Região do Azure |Prefixo de ponto final regional|
|---|---|
| **EUA e Canadá** | |
|E.U.A. Centro-Oeste | https:\//westcentralus.monitoring.azure.com/ |
|E.U.A.Oeste 2       | https:\//westus2.monitoring.azure.com/ |
|E.U.A. Centro-Norte | https:\//northcentralus.monitoring.azure.com
|E.U.A. Centro-Sul| https:\//southcentralus.monitoring.azure.com/ |
|E.U.A. Central      | https:\//centralus.monitoring.azure.com |
|Canadá Central | https:\//canadacentral.monitoring.azure.comc
|E.U.A. Leste| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Europa do Norte    | https:\//northeurope.monitoring.azure.com/ |
|Europa ocidental     | https:\//westeurope.monitoring.azure.com/ |
|Sul do Reino Unido | https:\//uksouth.monitoring.azure.com
|França Central | https:\//francecentral.monitoring.azure.com |
| **África** | |
|África do Sul Norte | https:\//southafricanorth.monitoring.azure.com
| **Ásia** | |
|Índia Central | https:\//centralindia.monitoring.azure.com
|Leste da Austrália | https:\//australiaeast.monitoring.azure.com
|Leste do Japão | https:\//japaneast.monitoring.azure.com
|Ásia Sudeste  | https:\//southeastasia.monitoring.azure.com |
|Ásia Leste | https:\//eastasia.monitoring.azure.com
|Coreia do Sul Central   | https:\//koreacentral.monitoring.azure.com

## <a name="latency-and-storage-retention"></a>Latência e retenção de armazenamento

Adicionar uma nova métrica ou uma nova dimensão adicionada a uma métrica pode levar até 2 a 3 minutos para aparecer. Uma vez no sistema, os dados devem ser lançados em menos de 30 segundos 99% do tempo. 

Se eliminar uma métrica ou remover uma dimensão, a alteração pode demorar uma semana a um mês a ser eliminada do sistema.

## <a name="quotas-and-limits"></a>Quotas e limites
O Monitor Azure impõe os seguintes limites de utilização em métricas personalizadas:

|Categoria|Limite|
|---|---|
|Séries/assinaturas/região ativas|50 000|
|Teclas de dimensão por métrica|10|
|Comprimento da corda para espaços de nomemétricos, nomes métricos, chaves de dimensão e valores de dimensão|256 caracteres|

Uma série de tempo ativa é definida como qualquer combinação única de métrica, chave de dimensão ou valor de dimensão que teve valores métricos publicados nas últimas 12 horas.

## <a name="next-steps"></a>Passos seguintes
Utilize métricas personalizadas de diferentes serviços: 
 - [Máquinas Virtuais](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Conjuntos de dimensionamento de máquinas virtuais](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Máquinas Virtuais Azure (clássica)](collect-custom-metrics-guestos-vm-classic.md)
 - [Máquina Virtual Linux usando o agente Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [API REST](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Serviços clássicos de nuvem](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
