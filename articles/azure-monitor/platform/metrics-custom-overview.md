---
title: Métricas personalizadas no Azure Monitor
description: Saiba mais sobre métricas personalizadas no Azure Monitor e como eles são modelados.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/24/2018
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 8602027431fdf2c1378834419977606bab5c6921
ms.sourcegitcommit: 8a59b051b283a72765e7d9ac9dd0586f37018d30
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58287269"
---
# <a name="custom-metrics-in-azure-monitor"></a>Métricas personalizadas no Azure Monitor

Como implementar recursos e aplicações no Azure, deseja iniciar a recolha de telemetria para obter informações sobre o desempenho e estado de funcionamento. Azure disponibiliza algumas métricas de imediato. Estas métricas são chamadas standard ou plataforma. No entanto, estão limitados por natureza. Pode querer recolher alguns indicadores de desempenho personalizados ou específicos do negócio métricas para fornecer informações mais detalhadas.
Estes **personalizado** métricas podem ser coletadas por meio da telemetria da sua aplicação, um agente que é executado em seus recursos do Azure, ou até mesmo um fora para dentro sistema de monitorização e enviado diretamente para o Azure Monitor. Depois, publicadas no Azure Monitor, pode procurar, consulta e alertar relativamente a métricas personalizadas para os seus recursos do Azure e aplicações lado a lado com as métricas padrão, emitidas pelo Azure.

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas
Métricas personalizadas podem ser enviadas para o Azure Monitor por vários métodos:
- Instrumentalize a sua aplicação ao utilizar o Azure Application Insights SDK e enviar telemetria personalizada para o Azure Monitor. 
- Instale a extensão do Windows Azure Diagnostics (WAD) no seu [VM do Azure](collect-custom-metrics-guestos-resource-manager-vm.md), [conjunto de dimensionamento de máquina virtual](collect-custom-metrics-guestos-resource-manager-vmss.md), [VM clássica](collect-custom-metrics-guestos-vm-classic.md), ou [clássico de serviços Cloud](collect-custom-metrics-guestos-vm-cloud-service-classic.md) e enviar os contadores de desempenho para o Azure Monitor. 
- Instalar o [InfluxData Telegraf agente](collect-custom-metrics-linux-telegraf.md) na sua VM Linux do Azure e as métricas de envio utilizando o Azure Monitor de saída do plug-in.
- Enviar métricas personalizadas [diretamente para a API de REST do Azure Monitor](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Quando enviar métricas personalizadas para o Azure Monitor, cada ponto de dados ou o valor, comunicado tem de incluir as seguintes informações.

### <a name="authentication"></a>Authentication
Para submeter métricas personalizadas para o Azure Monitor, a entidade que envia a métrica tem um token válido do Azure Active Directory (Azure AD) no **portador** cabeçalho do pedido. Existem algumas formas de suporte de adquirir um token de portador válido:
1. [Gerido identidades para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Fornece uma identidade para um recurso do Azure, tal como uma VM. Identidade de serviço gerida (MSI) foi projetado para dar permissões para executar determinadas operações de recursos. Um exemplo é permitir que um recurso emitir métricas sobre si próprio. Um recurso ou o MSI, que pode ser concedida **Editor de métricas de monitorização** permissões em outro recurso. Com esta permissão, o MSI pode emitir métricas para outros recursos também.
2. [Principal de serviço do Azure AD](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). Neste cenário, uma aplicação do Azure AD ou serviço, pode receber permissões para emitir métricas sobre um recurso do Azure.
Para autenticar o pedido, o Azure Monitor valida o token de aplicação através da utilização de chaves públicas do Azure AD. A atual **Editor de métricas de monitorização** função já tem esta permissão. Está disponível no portal do Azure. O principal de serviço, dependendo de quais recursos emite métricas personalizadas, pode ser atribuído a **Editor de métricas de monitorização** função no âmbito necessário. Os exemplos são uma subscrição, grupo de recursos ou recurso específico.

> [!NOTE]  
> Quando solicita um token do Azure AD para emitir métricas personalizadas, certifique-se de que é o público-alvo ou o recurso o token é solicitado para https://monitoring.azure.com/. Certifique-se de que incluem à direita '/'.

### <a name="subject"></a>Requerente
Esta propriedade captura o ID de recurso do Azure a métrica personalizada é comunicada para. Estas informações serão codificadas no URL da chamada à API feita. Cada API só pode enviar valores de métrica para um único recurso do Azure.

> [!NOTE]  
> Não é possível emitir métricas personalizadas com o ID de recurso de um grupo de recursos ou subscrição.
>
>

### <a name="region"></a>Região
Esta propriedade captura em que região do Azure, o recurso que está a emitir métricas para está implementado. Métricas tem de ser emitidas para o Azure Monitor mesmo ponto final regional que a região do recurso está implementado na. Por exemplo, métricas personalizadas para uma VM implementada na região E.U.A. oeste têm de ser enviadas para o WestUS regionais do Azure Monitor ponto final. As informações de região também estão codificadas no URL da chamada à API.

> [!NOTE]  
> Durante a pré-visualização pública, métricas personalizadas só estão disponíveis num subconjunto de regiões do Azure. Uma lista de regiões suportadas é documentada numa secção posterior deste artigo.
>
>

### <a name="timestamp"></a>Carimbo de data/hora
Cada ponto de dados enviado para o Azure Monitor deve ser marcado com um carimbo. Este timestamp captura a DateTime em que o valor da métrica é medido ou recolhido. O Azure Monitor aceita dados métricos carimbos de data máximo 20 minutos no passado e 5 minutos no futuro. O carimbo de hora tem de estar no formato ISO 8601.

### <a name="namespace"></a>Espaço de nomes
Espaços de nomes são uma forma de categorizar ou agrupar métricas semelhante. Ao utilizar espaços de nomes, pode atingir o isolamento entre grupos de métricas, que poderão recolher informações diferentes ou indicadores de desempenho. Por exemplo, pode ter um namespace chamado **ContosoMemoryMetrics** que controla as métricas de utilização de memória que a aplicação de perfil. Outro namespace chamado **ContosoAppTransaction** pode acompanhar todas as métricas sobre transações de utilizador na sua aplicação.

### <a name="name"></a>Name
**Nome** é o nome da métrica que está a ser comunicado. Normalmente, o nome é descritivo o bastante para ajudar a identificar o que é a medida. Um exemplo é uma métrica que mede o número de bytes de memória utilizada numa determinada VM. Poderá ter um nome de métrica, como **Bytes no uso da memória**.

### <a name="dimension-keys"></a>Chaves de dimensão
Uma dimensão é um par de chave ou valor que o ajuda a descrever as características adicionais sobre a métrica a ser recolhida. Ao utilizar as características adicionais, é possível recolher mais informações sobre a métrica, o que permite obter informações mais aprofundadas. Por exemplo, o **Bytes no uso da memória** métrica pode ter uma chave de dimensão chamada **processo** que captura consome de cada processo numa VM de quantos bytes de memória. Ao utilizar esta chave, pode filtrar a métrica para ver a quantidade de memória utilizam processos específicos ou para identificar os processos de cinco principais por utilização da memória.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Uma métrica personalizada pode ter até 10 dimensões.

### <a name="dimension-values"></a>Valores de dimensão
Quando um ponto de dados de métrica, para cada chave de dimensão em métrica está a ser comunicada, a geração de relatórios tem um valor de dimensão correspondente. Por exemplo, pode querer comunicar a memória utilizada pelas ContosoApp na sua VM:

* O nome da métrica seria **Bytes de memória em utilização**.
* A chave de dimensão seria **processo**.
* O valor de dimensão seria **ContosoApp.exe**.

Ao publicar um valor de métrica, só pode especificar um valor de dimensão único por chave de dimensão. Se a recolher a mesma utilização de memória para vários processos na VM, pode comunicar vários valores de métrica para esse timestamp. Cada valor de métrica de especificar um valor de dimensão diferente para o **processo** chave de dimensão.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Se uma métrica postagem define chaves de dimensão, valores de dimensão correspondentes são obrigatórios.

### <a name="metric-values"></a>Valores de métrica
Monitor do Azure armazena todas as métricas em intervalos de granularidade de um minuto. Estamos cientes de que, durante um determinado minuto, uma métrica poderá ter de amostragem várias vezes. Um exemplo é a utilização da CPU. Ou pode ter de ser medidos para muitos eventos discretos. Um exemplo é latências de transação de início de sessão. Para limitar o número de valores não processados, que terá de emitir e pague no Azure Monitor, pode pré-agregar localmente e emitir os valores:

* **Min**: O valor mínimo observado de todos os exemplos e medições durante o minuto.
* **Máx.**: O valor máximo observado de todos os exemplos e medições durante o minuto.
* **Soma**: A soma de todos os valores observados de todos os exemplos e medições durante o minuto.
* **Contagem de**: O número de amostras e medidas tomadas durante o minuto.

Por exemplo, se houver 4 transações início de sessão para a sua aplicação durante um dado um minuto, resultante medido latências para cada um seria da seguinte forma:

|Transação 1|Transação 2|Transação 3|Transação 4|
|---|---|---|---|
|7 ms|4 ms|13 ms|16 ms|
|

Em seguida, a publicação de métrica resultante para o Azure Monitor seria da seguinte forma:
* Mínimo: 4
* Máx.: 16
* Soma: 40
* Contagem de: 4

Se seu aplicativo não é possível de pré-agregar localmente e tem de emitir cada exemplo discreto ou evento imediatamente após a coleção, pode emitir os valores de medida não processados. Por exemplo, sempre que ocorre uma transação de início de sessão na sua aplicação, publicar uma métrica para o Azure Monitor com apenas uma única medida. Por conseguinte, para uma início de sessão transação qual 12 ms, a publicação de métrica seria da seguinte forma:
* Mínimo: 12
* Máx.: 12
* Soma: 12
* Contagem de: 1

Com esse processo, pode emitir vários valores para a mesma métrica mais a combinação de dimensão durante um determinado minuto. O Azure Monitor, em seguida, usa a todos os valores não processados emitidos para um determinado minuto e agrega-os em conjunto.

### <a name="sample-custom-metric-publication"></a>Publicação de métrica personalizada de exemplo
O exemplo seguinte, vai criar uma métrica personalizada denominada **Bytes de memória em uso** sob o espaço de nomes de métrica **perfil de memória** para uma máquina virtual. A métrica tem uma única dimensão chamada **processo**. Para o período de tempo determinado emitimos valores de métrica para dois processos diferentes:

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
> Application Insights, a extensão de diagnóstico e o agente de InfluxData Telegraf já estão configuradas para emitir os valores de métrica relativamente ao ponto de final regional correto e executar todas as propriedades anteriores em cada emissões.
>
>

## <a name="custom-metric-definitions"></a>Definições de métricas personalizadas
Não há necessidade de predefinir uma métrica personalizada no Azure Monitor, antes de ele é emitido. Cada ponto de dados métricos publicado contém o espaço de nomes, nome e informações de dimensão. Portanto, a primeira vez uma métrica personalizada é emitida para o Azure Monitor, uma definição de métrica é criada automaticamente. Esta definição de métrica, em seguida, é detetável em nenhum outro recurso que a métrica é emitida em relação a através de definições de métricas.

> [!NOTE]  
> Monitor do Azure ainda não suporta a definição **unidades** para uma métrica personalizada.

## <a name="using-custom-metrics"></a>Utilizar métricas personalizadas
Depois de métricas personalizadas são submetidas para o Azure Monitor, pode procurá-los através do portal do Azure e consultá-los por meio das APIs de REST do Azure Monitor. Também pode criar alertas nos mesmos para ser notificado quando forem cumpridas determinadas condições.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procurar as suas métricas personalizadas através do portal do Azure
1.  Aceda ao [Portal do Azure](https://portal.azure.com).
2.  Selecione o **Monitor** painel.
3.  Selecione **Métricas**.
4.  Selecione um recurso que já emitidos métricas personalizadas contra.
5.  Selecione o espaço de nomes de métricas para sua métrica personalizada.
6.  Selecione a métrica personalizada.

## <a name="supported-regions"></a>Regiões suportadas
Durante a pré-visualização pública, a capacidade de publicar métricas personalizadas está disponível apenas num subconjunto de regiões do Azure. Esta restrição significa que as métricas podem ser publicadas apenas pelos recursos de uma das regiões suportadas. A tabela seguinte lista o conjunto de regiões do Azure suportadas para métricas personalizadas. Ele também lista os pontos de extremidade correspondentes que devem ser publicadas métricas para recursos nessas regiões:

|Região do Azure|Prefixo de ponto de extremidade regional|
|---|---|
|EUA Leste| https:\//eastus.monitoring.azure.com/ |
|EUA Centro-Sul| https:\//southcentralus.monitoring.azure.com/ |
|EUA Centro-Oeste| https:\//westcentralus.monitoring.azure.com/ |
|EUA Oeste 2| https:\//westus2.monitoring.azure.com/ |
|Sudeste Asiático| https:\//southeastasia.monitoring.azure.com/ |
|Europa do Norte| https:\//northeurope.monitoring.azure.com/ |
|Europa Ocidental| https:\//westeurope.monitoring.azure.com/ |

## <a name="quotas-and-limits"></a>Quotas e limites
O Azure Monitor impõe os seguintes limites de utilização em métricas personalizadas:

|Categoria|Limite|
|---|---|
|Série de tempo ativo/subscrições/região|50,000|
|Chaves de dimensão por métrica|10|
|Comprimento de cadeia de caracteres para espaços de nomes de métrica, nomes de métricas, chaves de dimensão e valores de dimensão|256 carateres|

Uma série de tempo ativo é definida como qualquer combinação única de métrica, chave de dimensão ou valor de dimensão que tenha tido a valores de métrica publicadas nas últimas 12 horas.

## <a name="next-steps"></a>Passos Seguintes
Utilize métricas personalizadas dos serviços de diferentes: 
 - [Máquinas Virtuais](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Conjunto de dimensionamento de máquina virtual](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Máquinas virtuais do Azure (clássico)](collect-custom-metrics-guestos-vm-classic.md)
 - [Máquina Virtual Linux utilizando o agente de Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [API REST](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Serviços Cloud clássico](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
