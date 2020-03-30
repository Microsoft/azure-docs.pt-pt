---
title: Configure a topologia da fábrica conectada - Azure [ Microsoft Docs
description: Este artigo descreve como configurar o acelerador de soluções Connected Factory, incluindo a sua topologia.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 12/12/2017
ms.author: dobett
ms.openlocfilehash: 5fa3d4d4fdfa0dd81cd8ab8772ffb3903dda289f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820122"
---
# <a name="configure-the-connected-factory-solution-accelerator"></a>Configure o acelerador de solução de fábrica conectada

O acelerador de solução Connected Factory mostra um painel simulado para uma empresa fictícia Contoso. Esta empresa tem fábricas em inúmeros locais globais a nível global.

Este artigo usa Contoso como um exemplo para descrever como configurar a topologia de uma solução de Fábrica Conectada.

## <a name="simulated-factories-configuration"></a>Configuração de fábricas simuladas

Cada fábrica de Contoso tem linhas de produção que consistem em três estações cada. Cada estação é um verdadeiro servidor OPC UA com uma função específica:

* Estação de montagem
* Estação de teste
* Estação de embalagem

Estes servidores OPC UA têm nós OPC UA e [a OPC Publisher](overview-opc-publisher.md) envia os valores destes nós para a Fábrica Conectada. Isto inclui:

* Estado operacional atual, como o consumo de energia atual.
* Informações de produção, como o número de produtos produzidos.

Você pode usar o tablier para perfurar a topologia da fábrica de Contoso de uma visão global até uma vista de nível de estação. O painel de instrumentos da Fábrica Conectada permite:

* A visualização das figuras de OEE e KPI para cada camada na topologia.
* A visualização dos valores atuais dos nódos da OPC UA nas estações.
* A agregação dos números do OEE e do KPI, desde o nível da estação até ao nível global.
* A visualização de alertas e ações para executar se os valores atingirem limiares específicos.

## <a name="connected-factory-topology"></a>Topologia de fábrica conectada

A topologia das fábricas, linhas de produção e estações é hierárquica:

* O nível global tem nódosos de fábrica quando crianças.
* As fábricas têm nódosos de linha de produção quando crianças.
* As linhas de produção têm nódosos de estação quando crianças.
* As estações (servidores OPC UA) têm nós oPC UA quando crianças.

Cada nó da topologia tem um conjunto comum de propriedades que definem:

* Um identificador único para o nó de topologia.
* Um nome.
* Uma descrição.
* Uma imagem.
* As crianças do nó de topologia.
* Valores mínimos, visados e máximos para os valores da OEE e do KPI e as ações de alerta a executar.

## <a name="topology-configuration-file"></a>Arquivo de configuração topologia

Para configurar as propriedades listadas na secção anterior, a solução Connected Factory utiliza um ficheiro de configuração chamado [ContosoTopologyDescription.json](https://github.com/Azure/azure-iot-connected-factory/blob/master/WebApp/Contoso/Topology/ContosoTopologyDescription.json).

Pode encontrar este ficheiro no código `WebApp/Contoso/Topology` fonte da solução na pasta.

O seguinte corte mostra um `ContosoTopologyDescription.json` esboço do ficheiro de configuração:

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

As propriedades `<global_configuration>`comuns de, `<factory_configuration>`, `<production_line_configuration>`, e `<station_configuration>` são:

* **Nome** (cadeia de tipo)

  Define um nome descritivo, que deve ser apenas uma palavra para o nó de topologia mostrar no painel de instrumentos.

* **Descrição** (cadeia de tipo)

  Descreve o nó de topologia com mais detalhes.

* **Imagem** (cadeia de tipo)

  O caminho para uma imagem na solução WebApp para mostrar quando a informação sobre o nó de topologia é mostrada no painel de instrumentos.

* **OeeOverall**, **OeePerformance,** **OeeAvailability,** **OeeQuality,** **Kpi1,** **Kpi2** (tipo) `<performance_definition>`

  Estas propriedades definem valores mínimos, alvo e máximos da figura operacional utilizada para gerar alertas. Estas propriedades também definem as ações a executar se um alerta for detetado.

Os `<factory_configuration>` `<production_line_configuration>` e itens têm uma propriedade:

* **Guia** (cadeia de tipo)

  Identifica exclusivamente o nó de topologia.

`<factory_configuration>`tem uma propriedade:

* **Localização** (tipo) `<location_definition>`

  Especifica onde está localizada a fábrica.

`<station_configuration>`tem propriedades:

* **OpcUri** (cadeia de tipo)

  Esta propriedade deve ser definida para a aplicação do OPC UA APLICAÇÃO URI do servidor OPC UA.
  Como deve ser globalmente único pela especificação da OPC UA, esta propriedade é usada para identificar o nó de topologia da estação.

* **OpcNodes,** que são uma variedade de nós oPC UA (tipo) `<opc_node_description>`

`<location_definition>`tem propriedades:

* **Cidade** (cadeia de tipo)

  Nome da cidade mais próximo do local

* **País** (cadeia de tipo)

  País da localização

* **Latitude** (tipo duplo)

  Latitude da localização

* **Longitude** (tipo duplo)

  Longitude da localização

`<performance_definition>`tem propriedades:

* **Mínimo** (tipo duplo)

  Limiar mais baixo que o valor pode alcançar. Se o valor atual estiver abaixo deste limiar, gera-se um alerta.

* **Alvo** (tipo duplo)

  Valor de destino ideal.

* **Máximo** (tipo duplo)

  Limiar superior o valor pode chegar. Se o valor atual estiver acima deste limiar, gera-se um alerta.

* **Ações Mínimas** `<alert_action>`de Alerta (tipo)

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo) `<alert_action>`

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta máximo.

`<alert_action`> tem propriedades:

* **Tipo** (cadeia de tipo)

  Tipo de ação de alerta. São conhecidos os seguintes tipos:

  * **Aviso:** o estado do alerta deve mudar para reconhecido.
  * **CloseAlert**: todos os alertas mais antigos do mesmo tipo já não devem ser mostrados no painel de instrumentos.
  * **CallOpcMethod**: deve ser chamado um método OPC UA.
  * **OpenWebPage**: deve ser aberta uma janela do navegador que mostre informações contextuais adicionais.

* **Descrição** (cadeia de tipo)

  Descrição da ação mostrada no painel de instrumentos.

* **Parâmetro** (cadeia de tipo)

  Parâmetros necessários para executar a ação. O valor depende do tipo de ação.

  * **Aviso:** não é necessário parâmetro.
  * **CloseAlert:** não é necessário um parâmetro.
  * **CallOpcMethod**: a informação do nó e os parâmetros do método OPC UA para chamar no formato "NodeId do nó dos pais, nodeId de método para ligar, URI do servidor OPC UA."
  * **OpenWebPage**: o URL para mostrar na janela do navegador.

`<opc_node_description>`contém informações sobre nós da OPC UA numa estação (servidor OPC UA). Os nós que não representam os nós existentes da OPC UA, mas são usados como armazenamento na lógica de cálculo da Fábrica Conectada também são válidos. Tem as seguintes propriedades:

* **NodeId** (cadeia de tipo)

  Endereço do nó UA opc no espaço de endereço da estação (opc ua server' da estação. A sintaxe deve ser especificada na especificação da OPC UA para um NodeId.

* **Nome simbólico** (cadeia de tipo)

  Nome a mostrar no painel de instrumentos quando o valor deste nó UA opc é mostrado.

* **Relevância** (matriz de cadeia de tipo)

  Indica para que computação de OEE ou KPI o valor do nó opc ua é relevante. Cada elemento matriz pode ser um dos seguintes valores:

  * **OeeAvailability_Running:** o valor é relevante para o cálculo da Disponibilidade do OEE.
  * **OeeAvailability_Fault:** o valor é relevante para o cálculo da Disponibilidade do OEE.
  * **OeePerformance_Ideal:** o valor é relevante para o cálculo do OEE Performance e é tipicamente um valor constante.
  * **OeePerformance_Atual:** o valor é relevante para o cálculo do OEE Performance.
  * **OeeQuality_Good:** o valor é relevante para o cálculo da Qualidade OEE.
  * **OeeQuality_Bad:** o valor é relevante para o cálculo da Qualidade OEE.
  * **Kpi1:** o valor é relevante para o cálculo do KPI1.
  * **Kpi2:** o valor é relevante para o cálculo do KPI2.

* **Código opcode** (cadeia de tipo)

  Indica como o valor do nó UA da OPC é tratado em consultas de Insight da Série Tempo e cálculos OEE/KPI. Cada consulta de Insight da Série Time visa uma espálhete específica, que é um parâmetro da consulta e fornece um resultado. O Código Op controla a forma como o resultado é calculado e pode ser um dos seguintes valores:

  * **Diff**: diferença entre o último e o primeiro valor no tempo.
  * **Avg:** a média de todos os valores no tempo.
  * **Soma:** a soma de todos os valores no tempo.
  * **Por último:** atualmente não utilizado.
  * **Contagem**: o número de valores na hora tímino.
  * **Max:** o valor máximo na hora.
  * **Min:** o valor mínimo no tempo.
  * **Const:** o resultado é o valor especificado pela propriedade ConstValue.
  * **SubMaxMin:** a diferença entre o valor máximo e o valor mínimo.
  * **Timepan:** o tempo.

* **Unidades** (cadeia de tipo)

  Define uma unidade do valor para visualização no painel de instrumentos.

* **Visível** (tipo boolean)

  Controle se o valor deve ser mostrado no painel de instrumentos.

* **ConstValue** (tipo duplo)

  Se o **Código Op** é **Const,** então esta propriedade é o valor do nó.

* **Mínimo** (tipo duplo)

  Se o valor atual ficar abaixo deste valor, então gera-se um alerta mínimo.

* **Máximo** (tipo duplo)

  Se o valor atual aumentar acima deste valor, então é gerado um alerta máximo.

* **Ações Mínimas** `<alert_action>`de Alerta (tipo)

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta mínimo.

* **MaximumAlertActions** (tipo) `<alert_action>`

  Define o conjunto de ações, que podem ser tomadas como resposta a um alerta máximo.

Ao nível da estação, também vê objetos **de simulação.** Estes objetos são utilizados apenas para configurar a simulação da Fábrica Conectada e não devem ser utilizados para configurar uma topologia real.

## <a name="how-the-configuration-data-is-used-at-runtime"></a>Como os dados de configuração são usados no tempo de execução

Todas as propriedades utilizadas no ficheiro de configuração podem ser agrunadas em diferentes categorias, dependendo da forma como são utilizadas. Estas categorias são:

### <a name="visual-appearance"></a>Aparência visual

As propriedades nesta categoria definem a aparência visual do painel de instrumentos connected Factory. Os exemplos incluem:

* Nome
* Descrição
* Imagem
* Localização
* Unidades
* Visible

### <a name="internal-topology-tree-addressing"></a>Abordação interna da árvore de topologia

O WebApp mantém um dicionário interno de dados contendo informações de todos os nós de topologia. As propriedades **Guid** e **OpcUri** são usadas como chaves para aceder a este dicionário e precisam ser únicas.

### <a name="oeekpi-computation"></a>Computação OEE/KPI

Os valores da OEE/KPI para a simulação da fábrica conectada são parametrizados por:

* Os valores do nó da UA oPC a incluir no cálculo.
* Como a figura é calculada a partir dos valores da telemetria.

A Connected Factory utiliza as fórmulas [http://www.oeefoundation.org](http://www.oeefoundation.org)OEE publicadas pelo .

Os objetos de nó da OPC UA nas estações permitem a marcação para utilização no cálculo OEE/KPI. A propriedade **De relevância** indica para o qual o valor do nó OPC UA deve ser utilizado. A propriedade **OpCode** define como o valor está incluído no cálculo.

### <a name="alert-handling"></a>Tratamento de alerta

A Fábrica Conectada suporta um mecanismo simples de produção de alerta mínimo/máximo baseado no limiar. Há uma série de ações predefinidas que pode configurar em resposta a esses alertas. As seguintes propriedades controlam este mecanismo:

* Máximo
* Mínimo
* Ações máximas de alerta
* Ações mínimas de alerta

## <a name="correlating-to-telemetry-data"></a>Correlacionado com dados de telemetria

Para determinadas operações, tais como visualizar o último valor ou criar consultas de Insight da Série Tempo, o WebApp precisa de um esquema de endereçamento para os dados de telemetria ingeridos. A telemetria enviada para a Fábrica Conectada também precisa de ser armazenada em estruturas internas de dados. As duas propriedades que permitem estas operações estão na estação (servidor OPC UA) e nível de nó ua OPC:

* **OpcUri**

  Identifica (globalmente único) o servidor OPC UA de onde vem a telemetria. Nas mensagens ingeridas, este imóvel é enviado como **ApplicationUri**.

* **NodeId**

  Identifica o valor do nó no servidor OPC UA. O formato da propriedade deve ser especificado na especificação da OPC UA. Nas mensagens ingeridas, esta propriedade é enviada como **NodeId**.

Veja [o que é o OPC Publisher](overview-opc-publisher.md) para obter mais informações sobre como os dados de telemetria são ingeridos para a Fábrica Conectada.

## <a name="example-how-kpi1-is-calculated"></a>Exemplo: Como o KPI1 é calculado

A configuração `ContosoTopologyDescription.json` do ficheiro controla a forma como os valores do OEE/KPI são calculados. O exemplo que se segue mostra como as propriedades neste ficheiro controlam a computação do KPI1.

Na Fábrica Conectada, o KPI1 é utilizado para medir o número de produtos fabricados com sucesso na última hora. Cada estação (servidor OPC UA) na simulação connected Factory`NodeId: "ns=2;i=385"`fornece um nó OPC UA ( ), que fornece a telemetria para calcular este KPI.

A configuração para este nó OPC UA parece ser o seguinte corte:

```json
{
  "NodeId": "ns=2;i=385",
  "SymbolicName": "NumberOfManufacturedProducts",
  "Relevance": [ "Kpi1", "OeeQuality_Good" ],
  "OpCode": "SubMaxMin"
},
```

Esta configuração permite a consulta dos valores de telemetria deste nó utilizando insights da Série do Tempo. A consulta time series Insights recupera:

* O número de valores.
* O valor mínimo.
* O valor máximo.
* A média de todos os valores.
* A soma de todos os valores para todos os pares **OpcUri** (**ApplicationUri),** **NodeId** numa determinada espástio.

Uma característica do valor do nó **numberOfManufactureredProducts** é que só aumenta. Para calcular o número de produtos fabricados na hora, a Fábrica Conectada utiliza o **SubMaxMin** **OpCode** . O cálculo recupera o valor mínimo no início da hora e o valor máximo no final do tempo.

O **Código Op** na configuração configura a lógica de cálculo para calcular o resultado da diferença de valor máximo e mínimo. Estes resultados são então acumulados de baixo até ao nível raiz (global) e mostrados no painel de instrumentos.

## <a name="next-steps"></a>Passos seguintes

Um próximo passo sugerido é aprender a [personalizar a solução Connected Factory](iot-accelerators-connected-factory-customize.md).
