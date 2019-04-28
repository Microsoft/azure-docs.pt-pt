---
title: Configurar a topologia de fábrica ligada - Azure | Documentos da Microsoft
description: Como configurar a topologia de um acelerador de solução de fábrica ligada.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 3fd160fbccfb5298cefed6a731797ca6962b997c
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "61450028"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Configurar o acelerador de solução de fábrica ligada

O acelerador de solução de fábrica ligada mostra um dashboard simulado para uma empresa fictícia Contoso. Esta empresa tem fábricas globalmente em várias localizações globais.

Este artigo utiliza Contoso como exemplo para descrever como configurar a topologia de uma solução de fábrica ligada.

## <a name="simulated-factories-configuration"></a>Configuração de fábricas simuladas

Cada fábrica de Contoso tem linhas de produção, que consistem em três estações. Cada estação é um servidor OPC UA real com uma função específica:

* Estação de assemblagem
* Estação de teste
* Estação de empacotamento

Estes servidores OPC UA tem nós de OPC UA e [o publicador OPC](https://github.com/Azure/iot-edge-opc-publisher) envia os valores de nós para a fábrica ligada. Isto inclui:

* Estado operacional atual, como o consumo de energia atual.
* Informações de produção, como o número de produtos produziram.

Pode utilizar o dashboard para explorar a topologia de fábrica da Contoso a partir de uma vista global para baixo para uma vista de nível de estação. O dashboard de fábrica ligada permite:

* A visualização dos valores OEE e KPI para cada camada na topologia.
* A visualização de valores atuais de nós de OPC UA nas estações.
* A agregação dos valores OEE e KPI do nível de estação para o nível global.
* A visualização de alertas e ações a efetuar se os valores de limiares específicos do alcance.

## <a name="connected-factory-topology"></a>Topologia de fábrica ligada

A topologia de fábricas, linhas de produção e estações é hierárquica:

* O nível global tem nós de fábrica como filhos.
* As fábricas tem nós de linha de produção como filhos.
* As linhas de produção ter nós de estação como filhos.
* As estações (servidores OPC UA) tem nós de OPC UA como filhos.

Cada nó na topologia tem um conjunto comum de propriedades que definem:

* Um identificador exclusivo para o nó de topologia.
* Um nome.
* Uma descrição.
* Uma imagem.
* Os filhos do nó de topologia.
* Mínimo, destino e valores máximos para valores OEE e KPI e as ações de alerta para executar.

## <a name="topology-configuration-file"></a>Ficheiro de configuração de topologia

Para configurar as propriedades listadas na secção anterior, a solução de fábrica ligada utiliza um ficheiro de configuração chamado [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Pode encontrar este ficheiro no código-fonte na solução o `WebApp/Contoso/Topology` pasta.

O fragmento seguinte mostra uma descrição do `ContosoTopologyDescription.json` ficheiro de configuração:

```json
{
  <global_configuration>,
  "Factories": [
    <factory_configuration>,
    "ProductionLines": [
      <production_line_configuration>,
      "Stations": [
        <station_configuration>,
        <more station_configurations>
      ],
      <more production_line_configurations>
    ]
    <more factory_configurations>
  ]
}
```

As propriedades comuns dos `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`, e `<station_configuration>` são:

* **Nome** (escreva a cadeia de caracteres)

  Define um nome descritivo, o que deve ser apenas uma palavra para o nó de topologia Mostrar no dashboard.

* **Descrição** (escreva a cadeia de caracteres)

  Descreve o nó de topologia mais detalhadamente.

* **Imagem** (escreva a cadeia de caracteres)

  O caminho para uma imagem na solução WebApp para mostrar a quando as informações acerca do nó de topologia são mostradas no dashboard.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (tipo `<performance_definition>`)

  Estas propriedades definir mínimo, de destino e os valores do máximo de operacional figura utilizado para gerar alertas. Essas propriedades também definem as ações a executar se for detetado um alerta.

O `<factory_configuration>` e `<production_line_configuration>` itens têm uma propriedade:

* **GUID** (escreva a cadeia de caracteres)

  Identifica exclusivamente o nó de topologia.

`<factory_configuration>` tem uma propriedade:

* **Localização** (tipo `<location_definition>`)

  Especifica onde está localizada a fábrica.

`<station_configuration>` tem propriedades:

* **OpcUri** (escreva a cadeia de caracteres)

  Esta propriedade tem de ser definida para o URI de aplicativo do OPC UA do servidor OPC UA.
  Uma vez que tem de ser globalmente exclusivo pela especificação OPC UA, esta propriedade é utilizada para identificar o nó de topologia de estação.

* **OpcNodes**, que são uma matriz de nós de OPC UA (tipo `<opc_node_description>`)

`<location_definition>` tem propriedades:

* **Cidade** (escreva a cadeia de caracteres)

  Nome da cidade mais próxima para a localização

* **País** (escreva a cadeia de caracteres)

  País da localização

* **Latitude** (tipo double)

  Latitude da localização

* **Longitude** (tipo double)

  Longitude da localização

`<performance_definition>` tem propriedades:

* **Mínimo** (tipo double)

  O valor de limiar mais baixo pode chegar. Se o valor atual é abaixo deste limiar, é gerado um alerta.

* **Destino** (tipo double)

  Valor de destino ideal.

* **Máximo** (tipo double)

  O valor de limiar superior pode chegar. Se o valor atual é acima deste limiar, é gerado um alerta.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que podem ser executadas como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que podem ser executadas como resposta a um alerta máximo.

`<alert_action`> tem propriedades:

* **Tipo de** (escreva a cadeia de caracteres)

  Tipo da ação do alerta. Os seguintes tipos são conhecidos:

  * **AcknowledgeAlert**: o estado do alerta deve ser alterado para reconhecido.
  * **CloseAlert**: todos os alertas mais antigos do mesmo tipo já não devem ser mostrados no dashboard.
  * **CallOpcMethod**: deve ser chamado um método OPC UA.
  * **OpenWebPage**: uma janela do browser deve ser aberta que mostra informações contextuais adicionais.

* **Descrição** (escreva a cadeia de caracteres)

  Descrição da ação apresentada no dashboard.

* **Parâmetro** (escreva a cadeia de caracteres)

  Parâmetros necessários para executar a ação. O valor depende do tipo de ação.

  * **AcknowledgeAlert**: nenhum parâmetro necessário.
  * **CloseAlert**: nenhum parâmetro necessário.
  * **CallOpcMethod**: as informações de nó e parâmetros do método OPC UA para chamar no formato "NodeId do nó principal, NodeId do método para chamar, o URI do servidor OPC UA."
  * **OpenWebPage**: o URL para mostrar na janela do browser.

`<opc_node_description>` contém informações sobre nós de OPC UA numa estação (servidor OPC UA). Nós que representam sem nós de OPC UA existentes, mas que são utilizadas como armazenamento na lógica de cálculo de fábrica ligada também são válidos. Ele tem as seguintes propriedades:

* **NodeId** (escreva a cadeia de caracteres)

  Endereço do espaço de endereço do nó (do servidor OPC UA) da estação de OPC UA. Sintaxe tem de estar conforme especificado na especificação OPC UA para um NodeId.

* **SymbolicName** (escreva a cadeia de caracteres)

  Nome a apresentar no dashboard quando é apresentado o valor deste nó OPC UA.

* **Relevância** (matriz de cadeia de caracteres de tipo)

  Indica para o qual o cálculo de OEE ou KPI o valor do nó OPC UA é relevante. Cada elemento de matriz pode ser um dos seguintes valores:

  * **OeeAvailability_Running**: o valor é relevante para o cálculo de disponibilidade de OEE.
  * **OeeAvailability_Fault**: o valor é relevante para o cálculo de disponibilidade de OEE.
  * **OeePerformance_Ideal**: o valor é relevante para o cálculo de desempenho de OEE e normalmente é um valor constante.
  * **OeePerformance_Actual**: o valor é relevante para o cálculo de desempenho de OEE.
  * **OeeQuality_Good**: o valor é relevante para o cálculo de qualidade de OEE.
  * **OeeQuality_Bad**: o valor é relevante para o cálculo de qualidade de OEE.
  * **Kpi1**: o valor é relevante para o cálculo de KPI1.
  * **Kpi2**: o valor é relevante para o cálculo de KPI2.

* **OpCode** (escreva a cadeia de caracteres)

  Indica como o valor do nó OPC UA é processado em consultas de Time Series Insight e cálculos de valores OEE/KPI. Cada consulta de Time Series Insights destina-se um intervalo de tempo específico, o que é um parâmetro da consulta e que oferece um resultado. O código operacional controla como o resultado é calculado e pode ser um dos seguintes valores:

  * **Diff**: diferença entre a última e o primeiro valor no período de tempo.
  * **Média**: a média de todos os valores do período de tempo.
  * **Soma**: a soma de todos os valores do período de tempo.
  * **Última**: atualmente não utilizadas.
  * **Contagem de**: o número de valores no período de tempo.
  * **Máx.**: o valor máximo no período de tempo.
  * **Min**: o valor mínimo no período de tempo.
  * **Const**: o resultado é o valor especificado pela propriedade ConstValue.
  * **SubMaxMin**: a diferença entre o máximo e o valor mínimo.
  * **Período de tempo**: o período de tempo.

* **Unidades** (escreva a cadeia de caracteres)

  Define uma unidade do valor de apresentação no dashboard.

* **Visível** (tipo Booleano)

  Controla se o valor deve ser mostrado no dashboard.

* **ConstValue** (tipo double)

  Se o **OpCode** é **Const**, em seguida, esta propriedade é o valor do nó.

* **Mínimo** (tipo double)

  Se o valor atual cai abaixo deste valor, em seguida, é gerado um alerta de mínimo.

* **Máximo** (tipo double)

  Se o valor atual gera acima este valor, em seguida, é gerado um alerta de máximo.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que podem ser executadas como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que podem ser executadas como resposta a um alerta máximo.

No nível de estação, também verá **simulação** objetos. Esses objetos só são utilizados para configurar a simulação de fábrica ligada e não devem ser utilizados para configurar uma topologia real.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Como os dados de configuração são utilizados no tempo de execução

Todas as propriedades utilizadas no ficheiro de configuração podem ser agrupadas em categorias diferentes dependendo de como elas são usadas. Essas categorias são:

### <a name="visual-appearance"></a>Aparência Visual

Propriedades nesta categoria definem a aparência visual do dashboard de fábrica ligada. Os exemplos incluem:

* Name
* Descrição
* Image
* Location
* Unidades
* Visível

### <a name="internal-topology-tree-addressing"></a>Endereçamento de árvore de topologia interna

A WebApp mantém um dicionário de dados interna que contém informações de todos os nós de topologia. As propriedades **Guid** e **OpcUri** são utilizadas como chaves para aceder a este dicionário e tem de ser exclusivo.

### <a name="oeekpi-computation"></a>Cálculo de valores OEE/KPI

Os valores OEE/KPI para a simulação de fábrica ligada são parametrizados por:

* Os valores de nó OPC UA a serem incluídos no cálculo.
* Como a figura é calculada entre os valores de telemetria.

Fábrica ligada utiliza as fórmulas OEE, tal como publicado pelo https://www.oeefoundation.org.

Objetos do nó OPC UA em estações de ativar a marcação para utilização no cálculo de valores OEE/KPI. O **relevância** propriedade indica para qual figura valores OEE/KPI deve ser utilizado o valor do nó OPC UA. O **OpCode** propriedade define como o valor está incluído na computação.

### <a name="alert-handling"></a>Manipulação de alerta

Fábrica ligada suporta um mecanismo simples mínimos/máximos baseados no limiar a geração do alerta. Há uma série de ações predefinidas que pode configurar em resposta a esses alertas. As seguintes propriedades controlam esse mecanismo:

* Máximo
* Mínimo
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Correlacionar dados de telemetria

Para determinadas operações, como visualizar o último valor ou a criação de consultas do Time Series Insight, a aplicação Web precisa de um esquema de endereçamento para os dados de telemetria ingeridos. A telemetria enviada para a fábrica ligada também tem de ser armazenados em estruturas de dados internas. As duas propriedades, permitindo que estas operações estão no nível de nó de OPC UA e estação (servidor OPC UA):

* **OpcUri**

  Identifica (globalmente exclusivo) que o servidor OPC UA a telemetria provém de. Nas mensagens ingeridas, esta propriedade é enviada como **ApplicationUri**.

* **NodeId**

  Identifica o valor do nó no servidor OPC UA. O formato da propriedade deve ser conforme especificado na especificação OPC UA. Nas mensagens ingeridas, esta propriedade é enviada como **NodeId**.

Verifique [isso](https://github.com/Azure/iot-edge-opc-publisher) página do GitHub para obter mais informações sobre como os dados de telemetria são ingeridos para utilizar o publicador de OPC de fábrica ligada.

## <a name="example-how-kpi1-is-calculated"></a>Exemplo: Como é calculado KPI1

A configuração no `ContosoTopologyDescription.json` arquivo controla como os valores OEE/KPI são calculadas. O exemplo seguinte mostra como propriedades neste ficheiro controlam a computação de KPI1.

KPI1 de fábrica ligada é utilizada para medir o número de produtos com êxito produzidos e na última hora. Cada estação (servidor OPC UA) na simulação de fábrica ligada fornece um nó OPC UA (`NodeId: "ns=2;i=385"`), que fornece a telemetria para este KPI de computação.

A configuração para este nó OPC UA semelhante ao seguinte fragmento:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Esta configuração permite consultar os valores de telemetria deste nó utilizando o Time Series Insights. Obtém a consulta do Time Series Insights:

* O número de valores.
* O valor mínimo.
* O valor máximo.
* A média de todos os valores.
* A soma de todos os valores para todos os exclusivo **OpcUri** (**ApplicationUri**), **NodeId** pares num determinado intervalo de tempo.

Uma característica da **NumberOfManufactureredProducts** valor do nó é que ele só aumenta. Para calcular o número de produtos fabricados no período de tempo, fábrica ligada utiliza a **OpCode** **SubMaxMin**. O cálculo obtém o valor mínimo no início do período de tempo e o valor máximo no final de timespan.

O **OpCode** na configuração configura a lógica de cálculo para calcular o resultado da diferença do valor mínimo e máximo. Esses resultados, em seguida, são acumulados na parte inferior, até o nível (global) de raiz e apresentados no dashboard.

## <a name="next-steps"></a>Passos Seguintes

Sugestões da próxima etapa é saber como [implementar um gateway no Windows ou Linux para o acelerador de solução de fábrica ligada](iot-accelerators-connected-factory-gateway-deployment.md).
