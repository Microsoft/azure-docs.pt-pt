---
title: Configurar a topologia da fábrica conectada - Azure | Microsoft Docs
description: Este artigo descreve como configurar o acelerador de solução Connected Factory, incluindo a sua topologia.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: e015c6761b920ef37af2bbfd67ced5fc3218d532
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2021
ms.locfileid: "105646214"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Configure o acelerador de solução de fábrica conectado

> [!IMPORTANT]
> Enquanto atualizamos este artigo, consulte [a Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) para obter o conteúdo mais atualizado.

O acelerador de soluções Connected Factory mostra um dashboard simulado para uma empresa fictícia Contoso. Esta empresa tem fábricas em inúmeras localizações globais a nível global.

Este artigo usa Contoso como um exemplo para descrever como configurar a topologia de uma solução de fábrica conectada.

## <a name="simulated-factories-configuration"></a>Configuração de fábricas simuladas

Cada fábrica da Contoso tem linhas de produção que consistem em três estações cada. Cada estação é um servidor OPC UA real com um papel específico:

* Estação de montagem
* Estação de teste
* Estação de embalagem

Estes servidores OPC UA têm nós OPC UA e [a OPC Publisher](overview-opc-publisher.md) envia os valores destes nós para a Fábrica Conectada. O que está incluído:

* Estado operacional atual, como o consumo atual de energia.
* Informações de produção, como o número de produtos produzidos.

Você pode usar o painel de instrumentos para perfurar a topologia da fábrica contoso de uma vista global para uma vista de nível de estação. O painel de instrumentos da Fábrica Conectada permite:

* A visualização de figuras de OEE e KPI para cada camada na topologia.
* A visualização dos valores atuais dos nós da UA OPC nas estações.
* A agregação dos números OEE e KPI do nível da estação para o nível global.
* A visualização de alertas e ações a realizar se os valores atingirem limiares específicos.

## <a name="connected-factory-topology"></a>Topologia de fábrica conectada

A topologia das fábricas, linhas de produção e estações é hierárquica:

* O nível global tem nós de fábrica como crianças.
* As fábricas têm nós de linha de produção quando crianças.
* As linhas de produção têm nós de estação quando crianças.
* As estações (servidores OPC UA) têm nós UA OPC quando crianças.

Cada nó na topologia tem um conjunto comum de propriedades que definem:

* Um identificador único para o nó de topologia.
* Um nome.
* Uma descrição.
* Uma imagem.
* As crianças do nó de topologia.
* Valores mínimos, alvo e máximos para os valores OEE e KPI e as ações de alerta a executar.

## <a name="topology-configuration-file"></a>Ficheiro de configuração de topologia

Para configurar as propriedades listadas na secção anterior, a solução De Fábrica Conectada utiliza um ficheiro de configuração chamado [ContosoTopologyDescription.jsem](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Pode encontrar este ficheiro no código fonte da solução na `WebApp/Contoso/Topology` pasta.

O seguinte corte mostra um esboço do ficheiro de `ContosoTopologyDescription.json` configuração:

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

As propriedades `<global_configuration>` `<factory_configuration>` `<production_line_configuration>` comuns de, e `<station_configuration>` são:

* **Nome** (tipo de cadeia)

  Define um nome descritivo, que deve ser apenas uma palavra para o nó de topologia mostrar no painel de instrumentos.

* **Descrição** (tipo de cadeia)

  Descreve o nó topologia com mais detalhes.

* **Imagem** (tipo de cadeia)

  O caminho para uma imagem na solução WebApp para mostrar quando a informação sobre o nó de topologia é mostrada no painel de instrumentos.

* **OeeOverall**, **OeePerformance**, **OeeAvailability**, **OeeQuality,** **Kpi1**, **Kpi2** (tipo) `<performance_definition>`

  Estas propriedades definem valores mínimos, alvos e máximos da figura operacional utilizada para gerar alertas. Estas propriedades também definem as ações a executar se for detetado um alerta.

Os `<factory_configuration>` `<production_line_configuration>` itens e itens têm uma propriedade:

* **Guia** (tipo de cadeia)

  Identifica exclusivamente o nó de topologia.

`<factory_configuration>` tem uma propriedade:

* **Localização** `<location_definition>` (tipo)

  Especifica onde se encontra a fábrica.

`<station_configuration>` possui propriedades:

* **OpcUri** (tipo de cadeia)

  Esta propriedade deve ser definida para o OPC UA Application URI do servidor OPC UA.
  Como deve ser globalmente único pela especificação da OPC UA, esta propriedade é usada para identificar o nó de topologia da estação.

* **OpcNodes,** que são uma matriz de nós OPC UA `<opc_node_description>` (tipo)

`<location_definition>` possui propriedades:

* **Cidade** (tipo de cadeia)

  Nome da cidade mais próxima do local

* **País** (tipo de cadeia)

  País do local

* **Latitude** (tipo duplo)

  Latitude da localização

* **Longitude** (tipo duplo)

  Longitude da localização

`<performance_definition>` possui propriedades:

* **Mínimo** (tipo duplo)

  Limiar inferior o valor pode alcançar. Se o valor atual estiver abaixo deste limiar, é gerado um alerta.

* **Alvo** (tipo duplo)

  Valor ideal para o alvo.

* **Máximo** (tipo duplo)

  Limiar superior que o valor pode alcançar. Se o valor atual estiver acima deste limiar, é gerado um alerta.

* **Mínimos de Ações** de Aert `<alert_action>` (tipo)

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta mínimo.

* **MáximasAlertactions** `<alert_action>` (tipo)

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta máximo.

`<alert_action`> tem propriedades:

* **Tipo** (tipo de cadeia)

  Tipo de ação de alerta. São conhecidos os seguintes tipos:

  * **ReconhecimentoAlert**: o estado do alerta deve mudar para reconhecido.
  * **CloseAlert**: todos os alertas mais antigos do mesmo tipo já não devem ser indicados no painel de instrumentos.
  * **CallOpcMethod:** um método OPC UA deve ser chamado.
  * **OpenWebPage**: deve ser aberta uma janela do navegador com informações contextuais adicionais.

* **Descrição** (tipo de cadeia)

  Descrição da ação mostrada no painel de instrumentos.

* **Parâmetro** (tipo de cadeia)

  Parâmetros necessários para executar a ação. O valor depende do tipo de ação.

  * **ReconhecimentoAlert:** não é necessário parâmetro.
  * **CloseAlert:** não é necessário nenhum parâmetro.
  * **CallOpcMethod**: as informações e parâmetros do nó do método OPC UA para ligar no formato "NodeId of parent node, NodeId of method to call, URI of the OPC UA server."
  * **OpenWebPage**: o URL para mostrar na janela do navegador.

`<opc_node_description>` contém informações sobre os nóndes UA OPC numa estação (servidor OPC UA). Os nós que não representam os nós UA OPC existentes, mas que são usados como armazenamento na lógica de computação da Connected Factory também são válidos. Possui as seguintes propriedades:

* **NodeId** (tipo de cadeia)

  Endereço do nó UA OPC no espaço de endereço da estação (OPC UA server's). A sintaxe deve ser especificada na especificação OPC UA para um NodeId.

* **Nome simbólico** (tipo de corda)

  Nome a ser mostrado no painel de instrumentos quando o valor deste nó UA OPC for mostrado.

* **Relevância** (matriz de cadeia de tipo)

  Indica para que cálculo de OEE ou KPI o valor do nó UA OPC é relevante. Cada elemento de matriz pode ser um dos seguintes valores:

  * **OeeAvailability_Running:** o valor é relevante para o cálculo da Disponibilidade de OEE.
  * **OeeAvailability_Fault:** o valor é relevante para o cálculo da Disponibilidade de OEE.
  * **OeePerformance_Ideal:** o valor é relevante para o cálculo do OEE Performance e é tipicamente um valor constante.
  * **OeePerformance_Atual:** o valor é relevante para o cálculo do Desempenho OEE.
  * **OeeQuality_Good:** o valor é relevante para o cálculo da Qualidade OEE.
  * **OeeQuality_Bad:** o valor é relevante para o cálculo da Qualidade do OEE.
  * **Kpi1**: o valor é relevante para o cálculo do KPI1.
  * **Kpi2**: o valor é relevante para o cálculo do KPI2.

* **OpCode** (tipo de cadeia)

  Indica como o valor do nó UA OPC é tratado em consultas de Insight de Séries temporais e cálculos OEE/KPI. Cada consulta de insight série de tempo visa um tempo de tempo específico, que é um parâmetro da consulta e fornece um resultado. O OpCode controla a forma como o resultado é calculado e pode ser um dos seguintes valores:

  * **Difusa:** diferença entre o último e o primeiro valor no tempo.
  * **Avg**: a média de todos os valores no tempo.
  * **Soma**: a soma de todos os valores no tempo.
  * **Por último:** atualmente não utilizado.
  * **Contagem:** o número de valores no tempo.
  * **Max**: o valor máximo no tempo.
  * **Min**: o valor mínimo no tempo.
  * **Const:** o resultado é o valor especificado pela Propriedade ConstValue.
  * **SubMaxMin:** a diferença entre o valor máximo e o valor mínimo.
  * **Timespan**: o tempo.

* **Unidades** (tipo de cadeia)

  Define uma unidade do valor para visualização no painel de instrumentos.

* **Visível** (tipo boolean)

  Controla se o valor deve ser indicado no painel de instrumentos.

* **ConstValue** (tipo duplo)

  Se o **OpCode** é **Const,** então esta propriedade é o valor do nó.

* **Mínimo** (tipo duplo)

  Se o valor atual ficar abaixo deste valor, então é gerado um alerta mínimo.

* **Máximo** (tipo duplo)

  Se o valor atual elevar acima deste valor, então é gerado um alerta máximo.

* **Mínimos de Ações** de Aert `<alert_action>` (tipo)

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta mínimo.

* **MáximasAlertactions** `<alert_action>` (tipo)

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta máximo.

Ao nível da estação, também se vêem objetos **de Simulação.** Estes objetos são utilizados apenas para configurar a simulação da Fábrica Conectada e não devem ser utilizados para configurar uma topologia real.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Como os dados de configuração são usados no tempo de execução

Todas as propriedades utilizadas no ficheiro de configuração podem ser agrupadas em diferentes categorias, dependendo da forma como são utilizadas. Estas categorias são:

### <a name="visual-appearance"></a>Aparência visual

As propriedades desta categoria definem a aparência visual do painel de instrumentos de fábrica conectada. Os exemplos incluem:

* Nome
* Descrição
* Imagem
* Localização
* Unidades
* Visible

### <a name="internal-topology-tree-addressing"></a>Endereçamento de árvores de topologia interna

O WebApp mantém um dicionário de dados interno contendo informações de todos os nós topologia. As propriedades **Guid** e **OpcUri** são usadas como chaves para aceder a este dicionário e precisam ser únicas.

### <a name="oeekpi-computation"></a>Computação OEE/KPI

Os valores OEE/KPI para a simulação de fábrica conectada são parametrizados por:

* Os valores do nó UA OPC devem ser incluídos no cálculo.
* Como o valor é calculado a partir dos valores da telemetria.

Connected Factory utiliza as fórmulas OEE publicadas pelo [http://www.oeefoundation.org](http://www.oeefoundation.org) .

Os objetos de nó OPC UA nas estações permitem a marcação para utilização no cálculo OEE/KPI. A propriedade **relevância** indica para o qual o OEE/KPI figura o valor do nó UA OPC deve ser usado. A propriedade **OpCode** define como o valor está incluído no cálculo.

### <a name="alert-handling"></a>Tratamento de alerta

A Connected Factory suporta um simples mecanismo de geração de alerta baseado em limiar mínimo/máximo. Há uma série de ações predefinidas que pode configurar em resposta a esses alertas. As seguintes propriedades controlam este mecanismo:

* Máximo
* Mínimo
* MáximasAlertactions
* Mínimos DeSerções

## <a name="correlating-to-telemetry-data"></a>Correlacionando com os dados da telemetria

Para determinadas operações, como visualizar o último valor ou criar consultas de Insight de Séries Temporais, o WebApp necessita de um esquema de endereçamento para os dados de telemetria ingeridos. A telemetria enviada para a Connected Factory também precisa de ser armazenada em estruturas de dados internas. As duas propriedades que permitem estas operações estão na estação (servidor OPC UA) e no nível do nó UA OPC:

* **OpcUri**

  Identifica (globalmente único) o servidor OPC UA de onde provém a telemetria. Nas mensagens ingeridas, esta propriedade é enviada como **ApplicationUri.**

* **NodeId**

  Identifica o valor do nó no servidor UA OPC. O formato da propriedade deve ser especificado na especificação OPC UA. Nas mensagens ingeridas, esta propriedade é enviada como **NodeId.**

Consulte [o Que é o OPC Publisher](overview-opc-publisher.md) para obter mais informações sobre como os dados da telemetria são ingeridos na Connected Factory.

## <a name="example-how-kpi1-is-calculated"></a>Exemplo: Como o KPI1 é calculado

A configuração no ficheiro controla a forma como os `ContosoTopologyDescription.json` valores OEE/KPI são calculados. O exemplo a seguir mostra como as propriedades neste ficheiro controlam o cálculo do KPI1.

Na Fábrica Conectada, o KPI1 é utilizado para medir o número de produtos fabricados com sucesso na última hora. Cada estação (servidor OPC UA) na simulação de Fábrica Conectada fornece um nó UA OPC ( `NodeId: "ns=2;i=385"` ), que fornece a telemetria para calcular este KPI.

A configuração deste nó UA OPC parece o seguinte corte:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Esta configuração permite a consulta dos valores de telemetria deste nó utilizando insights de séries de tempo. A consulta de Insights da Série De Tempo recupera:

* O número de valores.
* O valor mínimo.
* O valor máximo.
* A média de todos os valores.
* A soma de todos os valores para todos os **OpcUri** únicos **(ApplicationUri),** pares **NodeId** num determinado período de tempo.

Uma característica do valor do nó de números **DefactufactureredProducts** é que só aumenta. Para calcular o número de produtos fabricados no tempo, a Connected Factory utiliza o **OpCode** **SubMaxMin**. O cálculo recupera o valor mínimo no início do período e o valor máximo no final do período.

O **OpCode** na configuração configura a lógica de cálculo para calcular o resultado da diferença de valor máximo e mínimo. Estes resultados são então acumulados de baixo até ao nível raiz (global) e mostrados no painel de instrumentos.

## <a name="next-steps"></a>Passos seguintes

Um próximo passo sugerido é aprender a personalizar a [solução de Fábrica Conectada.](iot-accelerators-connected-factory-customize.md)
