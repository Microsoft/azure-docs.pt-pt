---
title: Métricas personalizadas no Monitor Azure (Pré-visualização)
description: Saiba mais sobre as métricas personalizadas no Azure Monitor e como são modeladas.
author: anirudhcavale
ms.author: ancav
services: azure-monitor
ms.topic: conceptual
ms.date: 01/25/2021
ms.subservice: metrics
ms.openlocfilehash: ce081896292ec92c41dabc735df828ed167d86e7
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/26/2021
ms.locfileid: "98788507"
---
# <a name="custom-metrics-in-azure-monitor-preview"></a>Métricas personalizadas no Monitor Azure (Pré-visualização)

À medida que implementa recursos e aplicações em Azure, vai querer começar a colecionar telemetria para obter informações sobre o seu desempenho e saúde. O Azure disponibiliza-te algumas métricas da caixa. Estas métricas são chamadas [padrão ou plataforma.](./metrics-supported.md) No entanto, são limitados na natureza. 

É melhor recolher alguns indicadores de desempenho personalizados ou métricas específicas do negócio para fornecer informações mais profundas. Estas métricas **personalizadas** podem ser recolhidas através da telemetria da sua aplicação, um agente que funciona com os seus recursos Azure, ou mesmo um sistema de monitorização externo e submetido diretamente ao Azure Monitor. Depois de publicados no Azure Monitor, pode navegar, consultar e alertar sobre métricas personalizadas para os seus recursos E aplicações Azure lado a lado com as métricas padrão emitidas pela Azure.

As métricas personalizadas do Azure Monitor estão em vigor na pré-visualização pública. 

## <a name="methods-to-send-custom-metrics"></a>Métodos para enviar métricas personalizadas

As métricas personalizadas podem ser enviadas para o Azure Monitor através de vários métodos:
- Instrumente a sua aplicação utilizando o Azure Application Insights SDK e envie telemetria personalizada para o Azure Monitor. 
- Instale o Agente monitor Azure (Preview) no seu [Windows ou Linux Azure VM](azure-monitor-agent-overview.md) e utilize uma [regra de recolha de dados](data-collection-rule-azure-monitor-agent.md) para enviar contadores de desempenho para as métricas do Monitor Azure.
- Instale a extensão do Windows Azure Diagnostics (WAD) no seu [Azure VM,](collect-custom-metrics-guestos-resource-manager-vm.md) [conjunto de balanças de máquinas virtuais,](collect-custom-metrics-guestos-resource-manager-vmss.md) [VM clássico](collect-custom-metrics-guestos-vm-classic.md)ou [serviços clássicos da Cloud](collect-custom-metrics-guestos-vm-cloud-service-classic.md) e envie contadores de desempenho para o Azure Monitor. 
- Instale o [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) no seu Azure Linux VM e envie métricas utilizando o plug-in de saída do Azure Monitor.
- Envie métricas personalizadas [diretamente para a API do Monitor Azure,](./metrics-store-custom-rest-api.md) `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics` .

## <a name="pricing-model-and-retention"></a>Modelo de preços e retenção

Consulte a página de preços do [Azure Monitor](https://azure.microsoft.com/pricing/details/monitor/) para obter mais detalhes sobre quando a faturação será ativada para consultas de métricas e métricas personalizadas. Detalhes de preços específicos para todas as métricas, incluindo métricas personalizadas e consultas métricas estão disponíveis nesta página. Em resumo, não há custo para ingerir métricas padrão (métricas de plataforma) na loja de métricas do Azure Monitor, mas as métricas personalizadas incorrerão em custos quando entram na disponibilidade geral. Consultas métricas de API incorrem em custos.

As métricas personalizadas são mantidas pelo mesmo tempo que as [métricas da plataforma.](data-platform-metrics.md#retention-of-metrics) 

> [!NOTE]  
> As métricas enviadas ao Azure Monitor através do Application Insights SDK são faturadas como dados de registo ingeridos. Só incorrem em métricas adicionais se a funcionalidade Desinsuidor de Aplicações [Ativar o alerta sobre as dimensões métricas personalizadas.](../app/pre-aggregated-metrics-log-metrics.md#custom-metrics-dimensions-and-pre-aggregation) Esta caixa de verificação envia dados para a base de dados de métricas do Azure Monitor utilizando as métricas personalizadas API para permitir o alerta mais complexo.  Saiba mais sobre o [modelo de preços](../app/pricing.md#pricing-model) e preços da Application Insights na sua [região.](https://azure.microsoft.com/pricing/details/monitor/)


## <a name="how-to-send-custom-metrics"></a>Como enviar métricas personalizadas

Quando envia métricas personalizadas para o Azure Monitor, cada ponto de dados, ou valor, reportado deve incluir as seguintes informações.

### <a name="authentication"></a>Autenticação
Para submeter métricas personalizadas ao Azure Monitor, a entidade que submete a métrica necessita de um token válido do Azure Ative Directory (Azure AD) no cabeçalho do Pedido ao **Portador.** Existem algumas formas suportadas de adquirir um símbolo de portador válido:
1. [Identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/overview.md). Dá uma identidade a um recurso Azure em si, como um VM. A Identidade de Serviço Gerido (MSI) foi concebida para dar permissões de recursos para a realização de determinadas operações. Um exemplo é permitir que um recurso emita métricas sobre si mesmo. Um recurso, ou o seu MSI, pode ser concedido **permissões de Monitor Métricas Editor** em outro recurso. Com esta permissão, o MSI pode emitir métricas para outros recursos também.
2. [Azure Ad Service Principal](../../active-directory/develop/app-objects-and-service-principals.md). Neste cenário, uma aplicação AD Azure, ou serviço, pode ser atribuída permissões para emitir métricas sobre um recurso Azure.
Para autenticar o pedido, o Azure Monitor valida o token da aplicação utilizando as chaves públicas Azure AD. O papel existente **da Editora de Métricas de Monitorização** já tem esta permissão. Está disponível no portal Azure. O diretor de serviço, dependendo dos recursos para os quais emite métricas personalizadas, pode ser dado o papel de **Editor de Métricas de Monitorização** no âmbito exigido. Exemplos são uma subscrição, grupo de recursos ou recurso específico.

> [!TIP]  
> Quando solicitar um token AD Azure para emitir métricas personalizadas, certifique-se de que o público ou recurso que o token é solicitado é `https://monitoring.azure.com/` . Certifique-se de incluir o '/'.

### <a name="subject"></a>Assunto
Esta propriedade captura qual iD de recurso Azure a métrica personalizada é reportada. Esta informação será codificada no URL da chamada da API que está a ser feita. Cada API só pode apresentar valores métricos para um único recurso Azure.

> [!NOTE]  
> Não é possível emitir métricas personalizadas contra o ID de recursos de um grupo de recursos ou subscrição.


### <a name="region"></a>Region
Esta propriedade captura o que a região de Azure, o recurso para o qual está a emitir métricas, é implantado. As métricas devem ser emitidas para o mesmo ponto final regional do Azure Monitor em que o recurso é implantado. Por exemplo, as métricas personalizadas para um VM implantado nos EUA ocidentais devem ser enviadas para o ponto final regional do WestUS Azure Monitor. A informação da região também está codificada no URL da chamada API.

> [!NOTE]  
> Durante a pré-visualização pública, as métricas personalizadas só estão disponíveis num subconjunto de regiões de Azure. Uma lista de regiões apoiadas é documentada numa secção posterior deste artigo.
>
>

### <a name="timestamp"></a>CarimboDeDataEHora
Cada ponto de dados enviado ao Monitor Azure deve ser marcado com uma marca de tempo. Esta estampta de tempo captura o DateTime no qual o valor métrico é medido ou recolhido. O Azure Monitor aceita dados métricos com timetamps até 20 minutos no passado e 5 minutos no futuro. A estada de tempo deve estar no formato ISO 8601.

### <a name="namespace"></a>Espaço de Nomes
Os espaços de nome são uma forma de categorizar ou agrupar métricas semelhantes em conjunto. Ao utilizar espaços de nome, pode alcançar o isolamento entre grupos de métricas que podem recolher diferentes insights ou indicadores de desempenho. Por exemplo, você pode ter um espaço de nome chamado **contosomemorymetrics** que rastreia métricas de uso de memória que perfilam a sua aplicação. Outro espaço de nome chamado **contosoapptransaction** pode rastrear todas as métricas sobre transações de utilizadores na sua aplicação.

### <a name="name"></a>Name
**O** nome é o nome da métrica que está a ser reportada. Normalmente, o nome é descritivo o suficiente para ajudar a identificar o que é medido. Um exemplo é uma métrica que mede o número de bytes de memória usados num determinado VM. Pode ter um nome métrico como **Memory Bytes In Use**.

### <a name="dimension-keys"></a>Chaves de dimensão
Uma dimensão é um par chave ou valor que ajuda a descrever características adicionais sobre a métrica que está sendo recolhida. Utilizando as características adicionais, pode recolher mais informações sobre a métrica, o que permite obter informações mais profundas. Por exemplo, a métrica **memory Bytes In Use** pode ter uma chave de dimensão chamada **Processo** que captura quantos bytes de memória cada processo em um VM consome. Ao utilizar esta chave, pode filtrar a métrica para ver quantos processos específicos de memória utilizam ou para identificar os cinco principais processos por utilização da memória.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Uma métrica personalizada pode ter até 10 dimensões.

### <a name="dimension-values"></a>Valores de dimensão
Ao reportar um ponto de dados métrico, para cada tecla de dimensão na métrica que está sendo reportada, há um valor de dimensão correspondente. Por exemplo, é melhor relatar a memória utilizada pelo ContosoApp no seu VM:

* O nome métrico seria **Memory Bytes in Use**.
* A chave de dimensão seria **o Processo.**
* O valor da dimensão seria **ContosoApp.exe**.

Ao publicar um valor métrico, só é possível especificar um valor de dimensão única por tecla de dimensão. Se recolher a mesma utilização de memória para vários processos no VM, pode reportar vários valores métricos para esse selo de tempo. Cada valor métrico especificaria um valor de dimensão diferente para a chave de dimensão **do processo.**
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Se um poste métrico define teclas de dimensão, os valores de dimensão correspondentes são obrigatórios.

### <a name="metric-values"></a>Valores de métricas
O Azure Monitor armazena todas as métricas em intervalos de granularidade de um minuto. Entendemos que durante um determinado minuto, uma métrica pode precisar de ser amostrada várias vezes. A utilização da CPU é um exemplo. Ou pode ter de ser medido para muitos eventos discretos. Um exemplo são as latências de transação de inscrição. Para limitar o número de valores brutos que tem de emitir e pagar no Azure Monitor, pode-se localmente pré-agregar e emitir os valores:

* **Min**: O valor mínimo observado de todas as amostras e medições durante o minuto.
* **Máximo**: O valor máximo observado de todas as amostras e medições durante o minuto.
* **Soma**: A soma de todos os valores observados de todas as amostras e medições durante o minuto.
* **Contagem**: Número de amostras e medições colhidas durante o minuto.

Por exemplo, se houvesse quatro transações de entrada na sua app durante um minuto, as latências medida resultantes para cada uma poderia ser a seguinte:

|Transação 1|Transação 2|Transação 3|Transação 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|

Em seguida, a publicação métrica resultante ao Azure Monitor seria a seguinte:
* Min: 4
* Máx: 16
* Soma: 40
* Contagem: 4

Se a sua aplicação não for capaz de pré-agregar localmente e precisar de emitir cada amostra ou evento discreto imediatamente após a recolha, pode emitir os valores de medida bruta. Por exemplo, sempre que ocorre uma transação de entrada na sua aplicação, publica uma métrica para o Azure Monitor com apenas uma medição. Assim, para uma transação de inscrição que levou 12 ms, a publicação métrica seria a seguinte:
* Min: 12
* Máx: 12
* Soma: 12
* Contagem: 1

Com este processo, pode emitir vários valores para a mesma combinação de dimensão métrica mais durante um determinado minuto. O Azure Monitor leva então todos os valores brutos emitidos por um dado minuto e agrega-os.

### <a name="sample-custom-metric-publication"></a>Publicação métrica personalizada da amostra
No exemplo seguinte, cria uma métrica personalizada chamada **Memory Bytes in Use** sob o perfil de **memória** do espaço de nome métrico para uma máquina virtual. A métrica tem uma única dimensão chamada **Processo.** Para a determinada timetamp, emitemos valores métricos para dois processos diferentes:

```json
{
    "time": "2018-08-20T11:25:20-7:00",
    "data": {

      "baseData": {

        "metric": "Memory Bytes in Use",
        "namespace": "Memory Profile",
        "dimNames": [
          "Process"
        ],
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
> Os Insights de Aplicação, a extensão de diagnóstico e o agente InfluxData Telegraf já estão configurados para emitir valores métricos em relação ao ponto final regional correto e transportar todas as propriedades anteriores em cada emissão.
>
>

## <a name="custom-metric-definitions"></a>Definições métricas personalizadas
Não há necessidade de pré-finar uma métrica personalizada no Azure Monitor antes de ser emitida. Cada ponto de dados métricos publicado contém informações sobre o espaço de nome, nome e dimensão. Assim, a primeira vez que uma métrica personalizada é emitida ao Azure Monitor, uma definição métrica é criada automaticamente. Esta definição métrica é então detetável em qualquer recurso que a métrica seja emitida através das definições métricas.

> [!NOTE]  
> O Azure Monitor ainda não suporta a definição de **Unidades** para uma métrica personalizada.

## <a name="using-custom-metrics"></a>Usando métricas personalizadas
Depois de as métricas personalizadas serem submetidas ao Azure Monitor, pode navedá-las através do portal Azure e questioná-las através das APIs do Monitor Azure. Também pode criar alertas para o notificar quando determinadas condições estiverem reunidas.

> [!NOTE]
> Você precisa ser um leitor ou um papel contribuinte para ver métricas personalizadas. Consulte [o Leitor de Monitorização](../../role-based-access-control/built-in-roles.md#monitoring-reader). 

### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Navegue pelas suas métricas personalizadas através do portal Azure
1.    Aceda ao [Portal do Azure](https://portal.azure.com).
2.    Selecione o **painel monitor.**
3.    Selecione **Métricas**.
4.    Selecione um recurso contra o qual emitia métricas personalizadas.
5.    Selecione o espaço de nome das métricas para a sua métrica personalizada.
6.    Selecione a métrica personalizada.

> [!NOTE]
> Consulte [começar com o Azure Metrics Explorer](./metrics-getting-started.md) para obter mais informações sobre as métricas de visualização no portal Azure.

## <a name="supported-regions"></a>Regiões suportadas
Durante a pré-visualização pública, a capacidade de publicar métricas personalizadas está disponível apenas num subconjunto de regiões do Azure. Esta restrição significa que as métricas só podem ser publicadas para recursos numa das regiões apoiadas. Consulte [as geografias do Azure](https://azure.microsoft.com/global-infrastructure/geographies/) para obter mais informações sobre as regiões de Azure. O código da região de Azure utilizado nos pontos finais abaixo é apenas o nome da região com espaço branco removido A tabela seguinte lista o conjunto de regiões de Azure apoiadas para métricas personalizadas. Enumera igualmente os pontos finais correspondentes que devem ser publicados métricas de recursos nessas regiões:

|Região do Azure |Prefixo do ponto final regional|
|---|---|
| Todas as regiões de nuvem pública | https://<azure_region_code>.monitoring.azure.com |
| **Azure Government** | |
| US Gov - Arizona | https: \/ /usgovarizona.monitoring.azure.us |
| **China** | |
| China Leste 2 | https: \/ /chinaeast2.monitoring.azure.cn |

## <a name="latency-and-storage-retention"></a>Retenção de latência e armazenamento

Adicionar uma nova métrica ou uma nova dimensão a ser adicionada a uma métrica pode demorar até 2 a 3 minutos a aparecer. Uma vez no sistema, os dados devem aparecer em menos de 30 segundos 99% do tempo. 

Se eliminar uma métrica ou remover uma dimensão, a alteração pode demorar uma semana a um mês a ser eliminada do sistema.

## <a name="quotas-and-limits"></a>Quotas e limites
O Azure Monitor impõe os seguintes limites de utilização em métricas personalizadas:

|Categoria|Limite|
|---|---|
|Séries/subscrições/regiões de tempo ativo|50 000|
|Chaves de dimensão por métrica|10|
|Comprimento de corda para espaços de nome métrico, nomes métricos, teclas de dimensão e valores de dimensão|256 caracteres|

Uma série de tempo ativa é definida como qualquer combinação única de métrica, chave de dimensão ou valor de dimensão que teve valores métricos publicados nas últimas 12 horas.

## <a name="next-steps"></a>Próximos passos
Utilize métricas personalizadas de diferentes serviços: 
 - [Máquinas Virtuais](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Conjuntos de dimensionamento de máquinas virtuais](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Máquinas Virtuais Azure (clássico)](collect-custom-metrics-guestos-vm-classic.md)
 - [Máquina virtual Linux usando o agente Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [API REST](./metrics-store-custom-rest-api.md)
 - [Serviços clássicos da nuvem](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 

