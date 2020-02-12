---
title: Métricas personalizadas no Azure Monitor
description: Saiba mais sobre métricas personalizadas no Azure Monitor e como elas são modeladas.
author: ancav
services: azure-monitor
ms.service: azure-monitor
ms.topic: conceptual
ms.date: 09/09/2019
ms.author: ancav
ms.subservice: metrics
ms.openlocfilehash: 744958fc44a8d10bbc8ca5d44af8c473548ae5ca
ms.sourcegitcommit: 609d4bdb0467fd0af40e14a86eb40b9d03669ea1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/06/2019
ms.locfileid: "73669170"
---
# <a name="custom-metrics-in-azure-monitor"></a>Métricas personalizadas no Azure Monitor

Ao implantar recursos e aplicativos no Azure, você desejará começar a coletar a telemetria para obter informações sobre o desempenho e a integridade. O Azure disponibiliza algumas métricas para você. Essas métricas são chamadas de Standard ou Platform. No entanto, elas são limitadas por natureza. Talvez você queira coletar alguns indicadores de desempenho personalizados ou métricas específicas de negócios para fornecer informações mais aprofundadas.
Essas métricas **personalizadas** podem ser coletadas por meio de sua telemetria do aplicativo, um agente que é executado em seus recursos do Azure ou até mesmo um sistema de monitoramento externo e enviado diretamente para o Azure monitor. Depois que eles forem publicados no Azure Monitor, você poderá procurar, consultar e alertar sobre métricas personalizadas para seus recursos e aplicativos do Azure lado a lado com as métricas padrão emitidas pelo Azure.

## <a name="send-custom-metrics"></a>Enviar métricas personalizadas
Métricas personalizadas podem ser enviadas para Azure Monitor por meio de vários métodos:
- Instrumente seu aplicativo usando o SDK do Aplicativo Azure insights e envie telemetria personalizada para Azure Monitor. 
- Instale a extensão do Windows Diagnóstico do Azure (WAD) em sua [VM do Azure](collect-custom-metrics-guestos-resource-manager-vm.md), [conjunto de dimensionamento de máquinas virtuais](collect-custom-metrics-guestos-resource-manager-vmss.md), [VM clássica](collect-custom-metrics-guestos-vm-classic.md)ou [serviços de nuvem clássicos](collect-custom-metrics-guestos-vm-cloud-service-classic.md) e envie contadores de desempenho para Azure monitor. 
- Instale o [agente InfluxData Telegraf](collect-custom-metrics-linux-telegraf.md) na sua VM Linux do Azure e envie métricas usando o plug-in de saída Azure monitor.
- Envie métricas personalizadas [diretamente para a API REST do Azure monitor](../../azure-monitor/platform/metrics-store-custom-rest-api.md), `https://<azureregion>.monitoring.azure.com/<AzureResourceID>/metrics`.

Quando você envia métricas personalizadas para Azure Monitor, cada ponto de dados, ou valor, relatado deve incluir as informações a seguir.

### <a name="authentication"></a>Autenticação
Para enviar métricas personalizadas para Azure Monitor, a entidade que envia a métrica precisa de um token de Azure Active Directory (Azure AD) válido no cabeçalho do **portador** da solicitação. Há algumas maneiras com suporte para adquirir um token de portador válido:
1. [Identidades gerenciadas para recursos do Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview). Fornece uma identidade para um recurso do Azure em si, como uma VM. O Identidade de Serviço Gerenciada (MSI) foi projetado para fornecer permissões de recursos para realizar determinadas operações. Um exemplo é permitir que um recurso emita métricas sobre si mesmo. Um recurso, ou seu MSI, pode receber as permissões de **Editor de métricas de monitoramento** em outro recurso. Com essa permissão, o MSI também pode emitir métricas para outros recursos.
2. [Entidade de serviço do Azure ad](https://docs.microsoft.com/azure/active-directory/develop/app-objects-and-service-principals). Nesse cenário, um aplicativo ou serviço do Azure AD pode receber permissões para emitir métricas sobre um recurso do Azure.
Para autenticar a solicitação, Azure Monitor valida o token do aplicativo usando as chaves públicas do Azure AD. A função de **Publicador de métricas de monitoramento** existente já tem essa permissão. Ele está disponível na portal do Azure. A entidade de serviço, dependendo dos recursos para os quais ele emite métricas personalizadas, pode receber a função de **Editor de métricas de monitoramento** no escopo necessário. Os exemplos são uma assinatura, um grupo de recursos ou um recurso específico.

> [!NOTE]  
> Quando você solicita um token do Azure AD para emitir métricas personalizadas, certifique-se de que o público ou recurso para o qual o token é solicitado esteja https://monitoring.azure.com/. Certifique-se de incluir o '/' à direita.

### <a name="subject"></a>Assunto
Essa propriedade captura a qual ID de recurso do Azure a métrica personalizada é relatada. Essas informações serão codificadas na URL da chamada à API que está sendo feita. Cada API só pode enviar valores de métrica para um único recurso do Azure.

> [!NOTE]  
> Não é possível emitir métricas personalizadas em relação à ID de recurso de um grupo de recursos ou assinatura.
>
>

### <a name="region"></a>Região
Essa propriedade captura em qual região do Azure o recurso para o qual você está emitindo métricas está implantado. As métricas devem ser emitidas para o mesmo ponto de extremidade regional Azure Monitor como a região em que o recurso é implantado. Por exemplo, as métricas personalizadas para uma VM implantada no oeste dos EUA devem ser enviadas para o ponto de extremidade de Azure Monitor regional da Westus. As informações de região também são codificadas na URL da chamada à API.

> [!NOTE]  
> Durante a visualização pública, as métricas personalizadas só estão disponíveis em um subconjunto de regiões do Azure. Uma lista de regiões com suporte é documentada em uma seção posterior deste artigo.
>
>

### <a name="timestamp"></a>Carimbo de data/hora
Cada ponto de dados enviado para Azure Monitor deve ser marcado com um carimbo de data/hora. Esse carimbo de data/hora captura o DateTime no qual o valor da métrica é medido ou coletado. Azure Monitor aceita dados de métrica com carimbos de data/hora até 20 minutos no passado e cinco minutos no futuro. O carimbo de data/hora deve estar no formato ISO 8601.

### <a name="namespace"></a>Namespace
Os namespaces são uma maneira de categorizar ou agrupar métricas semelhantes juntas. Ao usar namespaces, você pode obter isolamento entre grupos de métricas que podem coletar diferentes informações ou indicadores de desempenho. Por exemplo, você pode ter um namespace chamado **contosomemorymetrics** que controla as métricas de uso de memória cujo perfil é seu aplicativo. Outro namespace chamado **contosoapptransaction** pode rastrear todas as métricas sobre transações de usuário em seu aplicativo.

### <a name="name"></a>Nome
**Nome** é o nome da métrica que está sendo relatada. Normalmente, o nome é descritivo o suficiente para ajudar a identificar o que é medido. Um exemplo é uma métrica que mede o número de bytes de memória usados em uma determinada VM. Ele pode ter um nome de métrica como **bytes de memória em uso**.

### <a name="dimension-keys"></a>Chaves de dimensão
Uma dimensão é um par chave ou valor que ajuda a descrever características adicionais sobre a métrica que está sendo coletada. Usando as características adicionais, você pode coletar mais informações sobre a métrica, que permite insights mais profundos. Por exemplo, os **bytes de memória na métrica de uso** podem ter uma chave de dimensão chamada **processo** que captura quantos bytes de memória cada processo em uma VM consome. Usando essa chave, você pode filtrar a métrica para ver a quantidade de processos específicos de memória que o usa ou para identificar os cinco principais processos por uso de memória.
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Uma métrica personalizada pode ter até 10 dimensões.

### <a name="dimension-values"></a>Valores de dimensão
Ao relatar um ponto de dados de métrica, para cada chave de dimensão na métrica que está sendo relatada, há um valor de dimensão correspondente. Por exemplo, talvez você queira relatar a memória usada pelo ContosoApp em sua VM:

* O nome da métrica seria **de bytes de memória em uso**.
* A chave de dimensão seria **processada**.
* O valor da dimensão seria **ContosoApp. exe**.

Ao publicar um valor de métrica, você só pode especificar um valor de dimensão único por chave de dimensão. Se você coletar a mesma utilização de memória para vários processos na VM, poderá relatar vários valores de métrica para esse carimbo de data/hora. Cada valor de métrica especificaria um valor de dimensão diferente para a chave de dimensão do **processo** .
As dimensões são opcionais, nem todas as métricas podem ter dimensões. Se uma postagem de métrica definir chaves de dimensão, os valores de dimensão correspondentes serão obrigatórios.

### <a name="metric-values"></a>Valores de métrica
Azure Monitor armazena todas as métricas em intervalos de granularidade de um minuto. Entendemos que, durante um determinado minuto, uma métrica pode precisar ser amostrada várias vezes. Um exemplo é a utilização da CPU. Ou pode precisar ser medido para muitos eventos discretos. Um exemplo é as latências de transação de entrada. Para limitar o número de valores brutos que você precisa emitir e pagar em Azure Monitor, você pode pré-configurar localmente e emitir os valores:

* **Min**: o valor mínimo observado de todos os exemplos e medidas durante o minuto.
* **Max**: o valor máximo observado de todos os exemplos e medidas durante o minuto.
* **Sum**: a soma de todos os valores observados de todos os exemplos e medidas durante o minuto.
* **Contagem**: o número de amostras e medições feitas durante o minuto.

Por exemplo, se houvesse quatro transações de entrada para seu aplicativo durante um determinado minuto, as latências medidas resultantes de cada uma delas podem ser as seguintes:

|Transação 1|Transação 2|Transação 3|Transação 4|
|---|---|---|---|
|7 MS|4 MS|13 MS|16 MS|
|

Em seguida, a publicação de métrica resultante para Azure Monitor seria a seguinte:
* Mín.: 4
* Máx.: 16
* Soma: 40
* Contagem: 4

Se seu aplicativo não puder agregar-se localmente e precisar emitir cada amostra ou evento discreto imediatamente na coleção, você poderá emitir os valores de medida brutos. Por exemplo, sempre que uma transação de entrada ocorre em seu aplicativo, você publica uma métrica para Azure Monitor com apenas uma única medida. Portanto, para uma transação de entrada que demorou 12 MS, a publicação de métrica seria a seguinte:
* Mín.: 12
* Máx.: 12
* Soma: 12
* Contagem: 1

Com esse processo, você pode emitir vários valores para a mesma métrica e combinação de dimensão durante um determinado minuto. Azure Monitor, em seguida, pega todos os valores brutos emitidos por um determinado minuto e os agrega em conjunto.

### <a name="sample-custom-metric-publication"></a>Publicação de métrica personalizada de exemplo
No exemplo a seguir, você cria uma métrica personalizada chamada **bytes de memória em uso** no perfil de **memória** do namespace de métrica para uma máquina virtual. A métrica tem uma única dimensão chamada **processo**. Para o carimbo de data/hora fornecido, emitimos valores de métrica para dois processos diferentes:

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
> Application Insights, a extensão de diagnóstico e o agente InfluxData Telegraf já estão configurados para emitir valores de métrica no ponto de extremidade regional correto e transportar todas as propriedades anteriores em cada emissão.
>
>

## <a name="custom-metric-definitions"></a>Definições de métrica personalizadas
Não é necessário predefinir uma métrica personalizada em Azure Monitor antes de ela ser emitida. Cada ponto de dados de métrica publicado contém informações de namespace, nome e dimensão. Portanto, na primeira vez que uma métrica personalizada é emitida para Azure Monitor, uma definição de métrica é criada automaticamente. Essa definição de métrica é detectável em qualquer recurso em que a métrica é emitida por meio das definições de métrica.

> [!NOTE]  
> Azure Monitor ainda não dá suporte à definição de **unidades** para uma métrica personalizada.

## <a name="using-custom-metrics"></a>Usando métricas personalizadas
Depois que as métricas personalizadas são enviadas para Azure Monitor, você pode procurá-las por meio do portal do Azure e consultá-las por meio das APIs REST do Azure Monitor. Você também pode criar alertas para notificá-lo quando determinadas condições forem atendidas.
### <a name="browse-your-custom-metrics-via-the-azure-portal"></a>Procurar suas métricas personalizadas por meio do portal do Azure
1.  Aceda ao [Portal do Azure](https://portal.azure.com).
2.  Selecione o painel **Monitor** .
3.  Selecione **Métricas**.
4.  Selecione um recurso para o qual você emitiu métricas personalizadas.
5.  Selecione o namespace de métricas para sua métrica personalizada.
6.  Selecione a métrica personalizada.

## <a name="supported-regions"></a>Regiões suportadas
Durante a visualização pública, a capacidade de publicar métricas personalizadas está disponível somente em um subconjunto de regiões do Azure. Essa restrição significa que as métricas podem ser publicadas somente para recursos em uma das regiões com suporte. A tabela a seguir lista o conjunto de regiões do Azure com suporte para métricas personalizadas. Ele também lista os pontos de extremidade correspondentes que as métricas para os recursos nessas regiões devem ser publicadas em:

|Região do Azure |Prefixo do ponto de extremidade regional|
|---|---|
| **EUA e Canadá** | |
|E.U.A. Centro-Oeste | https:\//westcentralus.monitoring.azure.com/ |
|E.U.A. Oeste 2       | https:\//westus2.monitoring.azure.com/ |
|E.U.A. Centro-Norte | https:\//northcentralus.monitoring.azure.com
|E.U.A. Centro-Sul| https:\//southcentralus.monitoring.azure.com/ |
|E.U.A. Central      | https:\//centralus.monitoring.azure.com |
|Canadá Central | https:\//canadacentral.Monitoring.Azure.comc
|E.U.A. Leste| https:\//eastus.monitoring.azure.com/ |
| **Europa** | |
|Europa do Norte    | https:\//northeurope.monitoring.azure.com/ |
|Europa Ocidental     | https:\//westeurope.monitoring.azure.com/ |
|Sul do Reino Unido | https:\//uksouth.monitoring.azure.com
|França Central | https:\//francecentral.monitoring.azure.com |
| **Oriental** | |
|Norte da África do Sul | https:\//southafricanorth.monitoring.azure.com
| **Ásia** | |
|Índia Central | https:\//centralindia.monitoring.azure.com
|Leste da Austrália | https:\//australiaeast.monitoring.azure.com
|Leste do Japão | https:\//japaneast.monitoring.azure.com
|Ásia Sudeste  | https:\//southeastasia.monitoring.azure.com |
|Ásia Leste | https:\//eastasia.monitoring.azure.com
|Coreia do Sul Central   | https:\//koreacentral.monitoring.azure.com


## <a name="quotas-and-limits"></a>Quotas e limites
Azure Monitor impõe os seguintes limites de uso em métricas personalizadas:

|Categoria|Limite|
|---|---|
|Série temporal/assinaturas/região ativas|50 000|
|Chaves de dimensão por métrica|10|
|Comprimento da cadeia de caracteres para namespaces de métrica, nomes de métrica, chaves de dimensão e valores de dimensão|256 caracteres|

Uma série temporal ativa é definida como qualquer combinação exclusiva de métrica, chave de dimensão ou valor de dimensão que tenha valores de métrica publicados nas últimas 12 horas.

## <a name="next-steps"></a>Passos seguintes
Use métricas personalizadas de serviços diferentes: 
 - [Máquinas Virtuais](collect-custom-metrics-guestos-resource-manager-vm.md)
 - [Conjunto de dimensionamento de máquinas virtuais](collect-custom-metrics-guestos-resource-manager-vmss.md)
 - [Máquinas virtuais do Azure (clássico)](collect-custom-metrics-guestos-vm-classic.md)
 - [Máquina virtual do Linux usando o agente Telegraf](collect-custom-metrics-linux-telegraf.md)
 - [API REST](../../azure-monitor/platform/metrics-store-custom-rest-api.md)
 - [Serviços de nuvem clássicos](collect-custom-metrics-guestos-vm-cloud-service-classic.md)
 
