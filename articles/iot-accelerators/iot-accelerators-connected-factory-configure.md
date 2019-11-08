---
title: Configurar a topologia de fábrica conectada – Azure | Microsoft Docs
description: Este artigo descreve como configurar o acelerador de solução de fábrica conectada, incluindo sua topologia.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/08/2019
ms.locfileid: "73820122"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Configurar o acelerador de solução de fábrica conectada

O Solution Accelerator da fábrica conectada mostra um painel simulado para uma empresa fictícia Contoso. Essa empresa tem fábricas em vários locais globais globalmente.

Este artigo usa a Contoso como um exemplo para descrever como configurar a topologia de uma solução de fábrica conectada.

## <a name="simulated-factories-configuration"></a>Configuração de fábricas simuladas

Cada fábrica da Contoso tem linhas de produção que consistem em três estações cada. Cada estação é um servidor OPC UA real com uma função específica:

* Estação de assembly
* Estação de teste
* Estação de empacotamento

Esses servidores OPC UA têm nós OPC UA e o [Publicador OPC](overview-opc-publisher.md) envia os valores desses nós para a fábrica conectada. Isto inclui:

* Status operacional atual, como o consumo de energia atual.
* Informações de produção, como o número de produtos produzidos.

Você pode usar o painel para analisar a topologia de fábrica da Contoso de uma exibição global até uma exibição de nível de estação. O painel da fábrica conectada permite:

* A visualização de OEE e os valores de KPI para cada camada na topologia.
* A visualização dos valores atuais dos nós OPC UA nas estações.
* A agregação dos números OEE e KPI do nível de estação para o nível global.
* A visualização de alertas e ações a serem executadas se os valores atingirem limites específicos.

## <a name="connected-factory-topology"></a>Topologia de fábrica conectada

A topologia de fábricas, linhas de produção e estações é hierárquica:

* O nível global tem nós de fábrica como filhos.
* As fábricas têm nós de linha de produção como filhos.
* As linhas de produção têm nós de estação como filhos.
* As estações (servidores OPC UA) têm nós OPC UA como filhos.

Cada nó na topologia tem um conjunto comum de propriedades que definem:

* Um identificador exclusivo para o nó de topologia.
* Um nome.
* Uma descrição.
* Uma imagem.
* Os filhos do nó de topologia.
* Os valores mínimo, de destino e máximo para os números de OEE e KPI e as ações de alerta a serem executadas.

## <a name="topology-configuration-file"></a>Arquivo de configuração de topologia

Para configurar as propriedades listadas na seção anterior, a solução de fábrica conectada usa um arquivo de configuração chamado [ContosoTopologyDescription. JSON](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Você pode encontrar esse arquivo no código-fonte da solução na pasta `WebApp/Contoso/Topology`.

O trecho a seguir mostra uma estrutura de tópicos do arquivo de configuração `ContosoTopologyDescription.json`:

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

As propriedades comuns de `<global_configuration>`, `<factory_configuration>`, `<production_line_configuration>`e `<station_configuration>` são:

* **Nome** (tipo cadeia de caracteres)

  Define um nome descritivo, que deve ser apenas uma palavra para o nó de topologia Mostrar no painel.

* **Descrição** (tipo cadeia de caracteres)

  Descreve o nó de topologia em mais detalhes.

* **Imagem** (tipo cadeia de caracteres)

  O caminho para uma imagem na solução WebApp para mostrar quando informações sobre o nó de topologia são mostradas no painel.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality**, **Kpi1**, **Kpi2** (tipo `<performance_definition>`)

  Essas propriedades definem os valores mínimo, de destino e máximo da figura operacional usada para gerar alertas. Essas propriedades também definem as ações a serem executadas se um alerta for detectado.

Os itens `<factory_configuration>` e `<production_line_configuration>` têm uma propriedade:

* **GUID** (tipo cadeia de caracteres)

  Identifica exclusivamente o nó de topologia.

`<factory_configuration>` tem uma propriedade:

* **Local** (tipo `<location_definition>`)

  Especifica onde a fábrica está localizada.

`<station_configuration>` tem propriedades:

* **OpcUri** (tipo cadeia de caracteres)

  Essa propriedade deve ser definida como o URI do aplicativo OPC UA do servidor OPC UA.
  Como ele deve ser globalmente exclusivo pela especificação OPC UA, essa propriedade é usada para identificar o nó de topologia de estação.

* **OpcNodes**, que são uma matriz de nós OPC UA (tipo `<opc_node_description>`)

`<location_definition>` tem propriedades:

* **City** (tipo cadeia de caracteres)

  Nome da cidade mais próxima do local

* **Country** (tipo cadeia de caracteres)

  País do local

* **Latitude** (tipo Double)

  Latitude do local

* **Longitude** (tipo duplo)

  Longitude do local

`<performance_definition>` tem propriedades:

* **Mínimo** (tipo duplo)

  Limite inferior que o valor pode alcançar. Se o valor atual estiver abaixo desse limite, um alerta será gerado.

* **Target** (tipo Double)

  Valor de destino ideal.

* **Máximo** (tipo duplo)

  Limite superior que o valor pode alcançar. Se o valor atual estiver acima desse limite, um alerta será gerado.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que pode ser usado como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que pode ser usado como resposta a um alerta máximo.

`<alert_action`> tem propriedades:

* **Type** (tipo cadeia de caracteres)

  Tipo da ação de alerta. Os seguintes tipos são conhecidos:

  * **AcknowledgeAlert**: o status do alerta deve ser alterado para confirmado.
  * **CloseAlert**: todos os alertas mais antigos do mesmo tipo não devem mais ser mostrados no painel.
  * **CallOpcMethod**: um método OPC UA deve ser chamado.
  * **OpenWebPage**: uma janela do navegador deve ser aberta mostrando informações contextuais adicionais.

* **Descrição** (tipo cadeia de caracteres)

  Descrição da ação mostrada no painel.

* **Parameter** (tipo cadeia de caracteres)

  Parâmetros necessários para executar a ação. O valor depende do tipo de ação.

  * **AcknowledgeAlert**: nenhum parâmetro necessário.
  * **CloseAlert**: nenhum parâmetro necessário.
  * **CallOpcMethod**: as informações de nó e os parâmetros do método OPC UA para chamar no formato "NodeId do nó pai, NodeId do método a ser chamado, URI do servidor OPC UA".
  * **OpenWebPage**: a URL a ser mostrada na janela do navegador.

`<opc_node_description>` contém informações sobre nós do OPC UA em uma estação (servidor OPC UA). Nós que não representam nós OPC UA existentes, mas são usados como armazenamento na lógica de computação da fábrica conectada também são válidos. Ele tem as seguintes propriedades:

* **NodeId** (tipo cadeia de caracteres)

  Endereço do nó OPC UA no espaço de endereço da estação (servidor OPC UA). A sintaxe deve ser conforme especificado na especificação OPC UA para um NodeId.

* **Simbóliconame** (tipo cadeia de caracteres)

  Nome a ser mostrado no painel quando o valor desse nó OPC UA for mostrado.

* **Relevância** (matriz do tipo cadeia de caracteres)

  Indica para qual computação de OEE ou KPI o valor do nó OPC UA é relevante. Cada elemento de matriz pode ser um dos seguintes valores:

  * **OeeAvailability_Running**: o valor é relevante para o cálculo da disponibilidade do OEE.
  * **OeeAvailability_Fault**: o valor é relevante para o cálculo da disponibilidade do OEE.
  * **OeePerformance_Ideal**: o valor é relevante para o cálculo do desempenho do OEE e normalmente é um valor constante.
  * **OeePerformance_Actual**: o valor é relevante para o cálculo do desempenho do OEE.
  * **OeeQuality_Good**: o valor é relevante para o cálculo da qualidade de OEE.
  * **OeeQuality_Bad**: o valor é relevante para o cálculo da qualidade de OEE.
  * **Kpi1**: o valor é relevante para o cálculo de Kpi1.
  * **Kpi2**: o valor é relevante para o cálculo de Kpi2.

* **Opcode** (tipo cadeia de caracteres)

  Indica como o valor do nó OPC UA é manipulado nas consultas do time Series Insight e nos cálculos de OEE/KPI. Cada consulta Time Series Insight tem como alvo um período específico, que é um parâmetro da consulta e fornece um resultado. O OpCode controla como o resultado é computado e pode ser um dos seguintes valores:

  * **Diff**: diferença entre o último e o primeiro valor no TimeSpan.
  * **AVG**: a média de todos os valores no TimeSpan.
  * **Sum**: a soma de todos os valores no TimeSpan.
  * **Último**: não usado atualmente.
  * **Contagem**: o número de valores no período de tempo.
  * **Max**: o valor máximo no TimeSpan.
  * **Min**: o valor mínimo no TimeSpan.
  * **Const**: o resultado é o valor especificado pela propriedade constValue.
  * **SubMaxMin**: a diferença entre o valor máximo e o mínimo.
  * **TimeSpan**: o TimeSpan.

* **Unidades** (tipo cadeia de caracteres)

  Define uma unidade do valor para exibição no painel.

* **Visible** (tipo booliano)

  Controla se o valor deve ser mostrado no painel.

* **ConstValue** (tipo duplo)

  Se o **opcode** for **const**, essa propriedade será o valor do nó.

* **Mínimo** (tipo duplo)

  Se o valor atual cair abaixo desse valor, um alerta mínimo será gerado.

* **Máximo** (tipo duplo)

  Se o valor atual for levantado acima desse valor, um alerta máximo será gerado.

* **MinimumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que pode ser usado como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo `<alert_action>`)

  Define o conjunto de ações, que pode ser usado como resposta a um alerta máximo.

No nível de estação, você também vê objetos de **simulação** . Esses objetos são usados apenas para configurar a simulação de fábrica conectada e não devem ser usados para configurar uma topologia real.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Como os dados de configuração são usados em tempo de execução

Todas as propriedades usadas no arquivo de configuração podem ser agrupadas em categorias diferentes, dependendo de como elas são usadas. Essas categorias são:

### <a name="visual-appearance"></a>Aparência visual

As propriedades nessa categoria definem a aparência visual do painel da fábrica conectada. Os exemplos incluem:

* Nome
* Descrição
* Imagem
* Localização
* Unidades
* Visível

### <a name="internal-topology-tree-addressing"></a>Endereçamento da árvore de topologia interna

O WebApp mantém um dicionário de dados interno que contém informações de todos os nós de topologia. As propriedades **GUID** e **OpcUri** são usadas como chaves para acessar esse dicionário e precisam ser exclusivas.

### <a name="oeekpi-computation"></a>Computação de OEE/KPI

Os valores de OEE/KPI para a simulação de fábrica conectada são parametrizados por:

* Os valores de nó do OPC UA a serem incluídos no cálculo.
* Como a figura é computada dos valores de telemetria.

A fábrica conectada usa as fórmulas OEE como publicadas pelo [http://www.oeefoundation.org](http://www.oeefoundation.org).

Os objetos de nó do OPC UA nas estações permitem a marcação para uso no cálculo de OEE/KPI. A propriedade **relevância** indica para qual figura OEE/KPI o valor do nó OPC UA deve ser usado. A propriedade **opcode** define como o valor é incluído na computação.

### <a name="alert-handling"></a>Tratamento de alertas

A fábrica conectada dá suporte a um mecanismo simples de geração de alerta baseado em limite mínimo/máximo. Há várias ações predefinidas que você pode configurar em resposta a esses alertas. As propriedades a seguir controlam esse mecanismo:

* Máximo
* Mínimo
* MaximumAlertActions
* MinimumAlertActions

## <a name="correlating-to-telemetry-data"></a>Correlacionando a dados de telemetria

Para determinadas operações, como visualizar o último valor ou criar consultas de insight de série temporal, o WebApp precisa de um esquema de endereçamento para os dados de telemetria ingeridos. A telemetria enviada para a fábrica conectada também precisa ser armazenada em estruturas de dados internas. As duas propriedades que habilitam essas operações estão na estação (servidor OPC UA) e no nível do nó OPC UA:

* **OpcUri**

  Identifica (globalmente exclusivo) o servidor OPC UA do qual provém a telemetria. Nas mensagens ingeridas, essa propriedade é enviada como **ApplicationUri**.

* **NodeId**

  Identifica o valor do nó no servidor OPC UA. O formato da propriedade deve ser conforme especificado na especificação OPC UA. Nas mensagens ingeridas, essa propriedade é enviada como **NodeId**.

Consulte [o que é o editor do OPC](overview-opc-publisher.md) para obter mais informações sobre como os dados de telemetria são ingeridos na fábrica conectada.

## <a name="example-how-kpi1-is-calculated"></a>Exemplo: como o KPI1 é calculado

A configuração no arquivo de `ContosoTopologyDescription.json` controla como os valores de OEE/KPI são calculados. O exemplo a seguir mostra como as propriedades nesse arquivo controlam o cálculo de KPI1.

Na fábrica conectada, o KPI1 é usado para medir o número de produtos fabricados com êxito na última hora. Cada estação (servidor OPC UA) na simulação de fábrica conectada fornece um nó do OPC UA (`NodeId: "ns=2;i=385"`), que fornece a telemetria para computar esse KPI.

A configuração para este nó OPC UA é semelhante ao seguinte trecho:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Essa configuração permite consultar os valores de telemetria deste nó usando Time Series Insights. A consulta Time Series Insights recupera:

* O número de valores.
* O valor mínimo.
* O valor máximo.
* A média de todos os valores.
* A soma de todos os valores para todos **os pares** **OpcUri** (**ApplicationUri**) exclusivos em um determinado TimeSpan.

Uma característica do valor do nó **NumberOfManufactureredProducts** é que ele só aumenta. Para calcular o número de produtos fabricados no período de tempo, a fábrica conectada usa o **opcode** **SubMaxMin**. O cálculo recupera o valor mínimo no início do período e o valor máximo no final do período de tempo.

O **opcode** na configuração configura a lógica de computação para calcular o resultado da diferença de valor máximo e mínimo. Esses resultados são então acumulados de baixo para o nível de raiz (global) e mostrados no painel.

## <a name="next-steps"></a>Passos seguintes

Uma próxima etapa sugerida é aprender a [Personalizar a solução de fábrica conectada](iot-accelerators-connected-factory-customize.md).
