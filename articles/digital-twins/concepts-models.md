---
title: Modelos DTDL
titleSuffix: Azure Digital Twins
description: Entenda como a Azure Digital Twins usa modelos personalizados para descrever entidades no seu ambiente.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ms.openlocfilehash: b3f0dd599f982e19fee7febc3b85d46f91a55b35
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/17/2021
ms.locfileid: "107589300"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Noções básicas de modelos de um duplo digital do Azure Digital Twins

Uma característica chave da Azure Digital Twins é a capacidade de definir o seu próprio vocabulário e construir o seu gráfico gémeo nos termos auto-definidos do seu negócio. Esta capacidade é fornecida através de **modelos** fornecidos pelo utilizador. Podes pensar nas modelos como os substantivos numa descrição do teu mundo. 

Um modelo é semelhante a uma **classe** numa linguagem de programação orientada a objetos, definindo uma forma de dados para um conceito particular no seu ambiente de trabalho real. Os modelos têm nomes (como *O Ambiente* ou *O Medidor de Temperatura),* e contêm elementos como propriedades, telemetria/eventos e comandos que descrevem o que este tipo de entidade no seu ambiente pode fazer. Mais tarde, utilizará estes modelos para criar [**gémeos digitais**](concepts-twins-graph.md) que representam entidades específicas que cumprem esta descrição do tipo.

Os modelos Azure Digital Twins estão representados na **Linguagem de Definição Digital DeFins (DTDL)** baseada em JSON-LD.  

## <a name="digital-twin-definition-language-dtdl-for-models"></a>Linguagem de definição de gémeos digital (DTDL) para modelos

Os modelos para Gémeos Digitais Azure são definidos usando a Linguagem de Definição de Gémeos Digitais (DTDL). 

Pode ver as especificações linguísticas completas para DTDL no GitHub: [**Digital Twins Definition Language (DTDL) - Versão 2**](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

A DTDL tem como base JSON-LD e é independente de linguagens de programação. O DTDL não é exclusivo da Azure Digital Twins, mas também é usado para representar dados de dispositivos em outros serviços IoT, como [ioT Plug e Play.](../iot-pnp/overview-iot-plug-and-play.md) Azure Digital Twins usa a versão DTDL **2** (a utilização da versão DTDL 1 com a Azure Digital Twins foi agora depreciada). 

O resto deste artigo resume como a linguagem é usada em Azure Digital Twins.

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Especificidades de implementação do Azure Digital Twins DTDL

Nem todos os serviços que utilizam o DTDL implementam exatamente as mesmas funcionalidades do DTDL. Por exemplo, o IoT Plug and Play não utiliza as funcionalidades DTDL que são para gráficos, enquanto a Azure Digital Twins não implementa atualmente comandos DTDL. 

Para que um modelo DTDL seja compatível com a Azure Digital Twins, deve satisfazer estes requisitos:

* Todos os elementos DTDL de nível superior num modelo devem ser de *interface* tipo . Isto porque o modelo Azure Digital Twins APIs pode receber objetos JSON que representam uma interface ou um conjunto de interfaces. Como resultado, nenhum outro tipo de elemento DTDL é permitido no nível superior.
* O DTDL para Azure Digital Twins não deve definir quaisquer *comandos*.
* O Azure Digital Twins só permite um único nível de nidificação de componentes. Isto significa que uma interface que está a ser usada como um componente não pode ter nenhum componente em si. 
* As interfaces não podem ser definidas em linha dentro de outras interfaces DTDL; devem ser definidas como entidades de alto nível separadas com as suas próprias identificações. Então, quando outra interface quiser incluir essa interface como componente ou através de herança, pode referenciar o seu ID.

A Azure Digital Twins também não observa o `writable` atributo sobre propriedades ou relacionamentos. Embora isto possa ser definido de acordo com as especificações de DTDL, o valor não é usado pela Azure Digital Twins. Em vez disso, estes são sempre tratados como writable por clientes externos que têm permissões de escrita geral para o serviço Azure Digital Twins.

## <a name="elements-of-a-model"></a>Elementos de um modelo

Dentro de uma definição de modelo, o item de código de nível superior é uma **interface**. Esta encapsula a totalidade do modelo, enquanto o resto deste é definido na própria interface. 

Uma interface de modelo DTDL pode conter zero, um ou muitos dos seguintes campos:
* **Propriedade** - Propriedades são campos de dados que representam o estado de uma entidade (como as propriedades em muitas linguagens de programação orientadas a objetos). As propriedades têm armazenamento de suporte e podem ser lidas a qualquer momento.
* **Telemetria** - Os campos de telemetria representam medições ou eventos, e são frequentemente utilizados para descrever leituras de sensores de dispositivos. Ao contrário das propriedades, a telemetria não é armazenada num gémeo digital; é uma série de eventos de dados ligados ao tempo que precisam de ser tratados à medida que ocorrem. Para obter mais informações sobre as diferenças entre propriedade e telemetria, consulte a secção [*Propriedades vs. telemetria*](#properties-vs-telemetry) abaixo.
* **Componente** - Os componentes permitem-lhe construir a interface do seu modelo como conjunto de outras interfaces, se quiser. Um exemplo de um componente é uma interface *frontCamera* (e outra interface de componente *backCamera)* que são usadas na definição de um modelo para um *telefone*. Primeiro deve definir uma interface para *a FrontCamera* como se fosse o seu próprio modelo, e depois pode remundo-a ao definir *o Telefone*.

    Use um componente para descrever algo que é parte integrante da sua solução, mas que não precisa de uma identidade separada, e não precisa de ser criado, eliminado ou reorganizado no gráfico gémeo de forma independente. Se quiser que as entidades tenham existências independentes no gráfico gémeo, represente-as como gémeas digitais separadas de diferentes modelos, ligadas por *relacionamentos* (ver próxima bala).
    
    >[!TIP] 
    >Os componentes também podem ser usados para organização, para agrupar conjuntos de propriedades relacionadas dentro de uma interface de modelo. Nesta situação, pode pensar em cada componente como um espaço de nome ou "pasta" dentro da interface.
* **Relacionamento** - Relacionamentos permitem-lhe representar como um gémeo digital pode estar envolvido com outros gémeos digitais. As relações podem representar diferentes significados semânticos, tais como *contém* ("chão contém espaço"), *frescos* ("hvac cools room"), *isBilledTo* ("compressor é faturado para o utilizador"), etc. As relações permitem a solução fornecer um gráfico de entidades interrelacionadas. As relações também podem ter [propriedades](#properties-of-relationships) próprias.

> [!NOTE]
> A [especificação para DTDL](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md) também define Comandos , que são métodos que podem ser **executados** num gémeo digital (como um comando de reset, ou um comando para ligar ou desligar uma ventoinha). No entanto, *os comandos não são atualmente suportados em Azure Digital Twins.*

### <a name="properties-vs-telemetry"></a>Propriedades vs. telemetria

Aqui está uma orientação adicional sobre a distinção entre **a propriedade** DTDL e os campos **de telemetria** em Azure Digital Twins.

A diferença entre propriedades e telemetria para os modelos Azure Digital Twins é a seguinte:
* Espera-se que as **propriedades** tenham armazenamento de suporte. Isto significa que você pode ler uma propriedade a qualquer momento e recuperar o seu valor. Se a propriedade for escrita, também pode armazenar um valor na propriedade.  
* **A telemetria** é mais como uma corrente de eventos; é um conjunto de mensagens de dados que têm uma vida útil curta. Se não se prepara para ouvir o evento e as ações a tomar quando isso acontecer, não há vestígios do evento mais tarde. Não pode voltar a lê-lo mais tarde. 
  - Em termos C#, a telemetria é como um evento C#. 
  - Em termos IoT, a telemetria é normalmente uma única medição enviada por um dispositivo.

**A telemetria** é frequentemente usada com dispositivos IoT, porque muitos dispositivos não são capazes, ou interessados em armazenar os valores de medição que geram. Enviam-nos como uma corrente de eventos de "telemetria". Neste caso, não pode perguntar no dispositivo em momento algum sobre o valor mais recente do campo de telemetria. Em vez disso, terá de ouvir as mensagens do dispositivo e tomar medidas à medida que as mensagens chegam. 

Como resultado, ao desenhar um modelo em Azure Digital Twins, provavelmente usará **propriedades** na maioria dos casos para modelar os seus gémeos. Isto permite-lhe ter o armazenamento de suporte e a capacidade de ler e consultar os campos de dados.

A telemetria e as propriedades muitas vezes trabalham em conjunto para lidar com a entrada de dados dos dispositivos. Como todas as entradas para Azure Digital Twins são via [APIs,](how-to-use-apis-sdks.md)você normalmente usará a sua função de entrada para ler eventos de telemetria ou propriedade de dispositivos, e definir uma propriedade em Azure Digital Twins em resposta. 

Também pode publicar um evento de telemetria da Azure Digital Twins API. Tal como acontece com outras telemetrias, este é um evento de curta duração que requer um ouvinte a manusear.

#### <a name="properties-of-relationships"></a>Propriedades de relacionamentos

O DTDL também permite que **as relações** tenham propriedades próprias. Ao definir uma relação dentro de um modelo DTDL, a relação pode ter o seu próprio `properties` campo onde você pode definir propriedades personalizadas para descrever estado específico de relacionamento.

## <a name="model-inheritance"></a>Herança modelo

Às vezes, talvez queiras especializar um modelo. Por exemplo, pode ser útil ter um modelo genérico *Room,* e variantes especializadas *ConferenceRoom* e *Gym.* Para expressar a especialização, o DTDL suporta a herança: as interfaces podem herdar de uma ou mais outras interfaces. 

O exemplo a seguir reimagina o modelo *Planet* do exemplo DTDL anterior como um subtipo de um modelo *CelestialBody* maior. O modelo "pai" é definido primeiro, e depois o modelo "criança" baseia-se nele utilizando o campo `extends` .

:::code language="json" source="~/digital-twins-docs-samples/models/CelestialBody-Planet-Crater.json":::

Neste exemplo, *a CelestialBody* contribui com um nome, uma massa e uma temperatura para o *Planeta.* A `extends` secção é um nome de interface, ou um conjunto de nomes de interface (permitindo que a interface de extensão herde de vários modelos-mãe, se desejar).

Uma vez aplicada a herança, a interface de extensão expõe todas as propriedades de toda a cadeia de heranças.

A interface de extensão não pode alterar nenhuma das definições das interfaces-mãe; só pode adicioná-los. Também não pode redefinir uma capacidade já definida em nenhuma das suas interfaces-mãe (mesmo que as capacidades sejam definidas como as mesmas). Por exemplo, se uma interface-mãe define uma `double` *massa* de propriedade, a interface de extensão não pode conter uma declaração de *massa*, mesmo que seja também uma `double` .

## <a name="model-code"></a>Código modelo

Modelos de tipo gémeo podem ser escritos em qualquer editor de texto. A língua DTDL segue a sintaxe JSON, pelo que deve armazenar modelos com a extensão *.json*. A utilização da extensão JSON permitirá que muitos editores de texto de programação forneçam a verificação e a realce básicas de sintaxe para os seus documentos DTDL. Há também uma [extensão DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponível para [Código de Estúdio Visual](https://code.visualstudio.com/).

### <a name="possible-schemas"></a>Possíveis esquemas

De acordo com o DTDL, o esquema para atributos *de propriedade* e *telemetria* pode ser de tipos `integer` primitivos padrão, e `double` `string` `Boolean` outros tipos como `DateTime` e `Duration` . 

Além de tipos primitivos, os campos *de propriedade* e *telemetria* podem ter estes tipos complexos:
* `Object`
* `Map`
* `Enum`

Os campos *de telemetria* também suportam. `Array`

### <a name="example-model"></a>Modelo de exemplo

Esta secção contém um exemplo de um modelo típico, escrito como uma interface DTDL. O modelo descreve **planetas,** cada um com um nome, uma massa e uma temperatura.
 
Considere que os planetas também podem interagir com **as luas** que são seus satélites, e podem conter **crateras.** No exemplo abaixo, o `Planet` modelo expressa ligações a estas outras entidades, referindo dois modelos externos `Moon` e `Crater` . Estes modelos também são definidos no código de exemplo abaixo, mas são mantidos muito simples para não desviar do `Planet` exemplo principal.

:::code language="json" source="~/digital-twins-docs-samples/models/Planet-Crater-Moon.json":::

Os campos do modelo são:

| Campo | Descrição |
| --- | --- |
| `@id` | Um identificador para o modelo. Deve estar no `dtmi:<domain>:<unique model identifier>;<model version number>` formato. |
| `@type` | Identifica o tipo de informação que está a ser descrita. Para uma interface, o tipo é *Interface.* |
| `@context` | Define o [contexto](https://niem.github.io/json/reference/json-ld/context/) para o documento JSON. Os modelos devem ser `dtmi:dtdl:context;2` utilizados. |
| `displayName` | [opcional] Permite-lhe dar ao modelo um nome amigável, se desejar. |
| `contents` | Todos os dados restantes da interface são colocados aqui, como uma variedade de definições de atributos. Cada atributo deve fornecer a `@type` (*Propriedade*, *Telemetria,* *Comando,* *Relacionamento,* *Ou Componente*) para identificar o tipo de informação de interface que descreve, e, em seguida, um conjunto de propriedades que definem o atributo real (por exemplo, e para definir `name` um `schema` *Imóvel).* |

> [!NOTE]
> Note que a interface do componente *(Cratera* neste exemplo) é definida na mesma matriz que a interface que a utiliza (*Planeta).* Os componentes devem ser definidos desta forma nas chamadas API para que a interface seja encontrada.

## <a name="best-practices-for-designing-models"></a>Melhores práticas para desenhar modelos

Ao desenhar modelos para refletir as entidades no seu ambiente, pode ser útil olhar para o futuro e considerar as implicações de [consulta](concepts-query-language.md) do seu design. Pode querer projetar propriedades de uma forma que evite grandes conjuntos de resultados de graf traversal. Você também pode querer modelar relações que serão respondidas numa única consulta como relacionamentos de nível único.

### <a name="validating-models"></a>Modelos de validação

[!INCLUDE [Azure Digital Twins: validate models info](../../includes/digital-twins-validate.md)]

## <a name="tools-for-models"></a>Ferramentas para modelos 

Existem várias amostras disponíveis para tornar ainda mais fácil lidar com modelos e ontotologias. Estão localizados neste repositório: [Ferramentas para linguagem de definição de gémeos digitais (DTDL)](https://github.com/Azure/opendigitaltwins-tools).

Esta secção descreve o atual conjunto de amostras com mais detalhes.

### <a name="model-uploader"></a>Carregador de modelos 

_**Para o upload de modelos para Azure Digital Twins**_

Uma vez terminada a criação, ampliação ou seleção dos seus modelos, pode enviá-los para a sua instância Azure Digital Twins para os disponibilizar para utilização na sua solução. Isto é feito usando as [APIs de Gémeos Digitais Azure,](how-to-use-apis-sdks.md)como descrito em [*Como-a: Gerir os modelos DTDL*](how-to-manage-model.md#upload-models).

No entanto, se tiver muitos modelos para carregar — ou se tiverem muitas interdependências que complicariam as encomendas individuais — pode utilizar esta amostra para carregar muitos modelos ao mesmo tempo: [**Azure Digital Twins Model Uploader**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/ModelUploader). Siga as instruções fornecidas com a amostra para configurar e utilize este projeto para carregar os modelos em seu próprio caso.

### <a name="model-visualizer"></a>Visualizador de modelo 

_**Para visualizar modelos**_

Depois de ter carregado os modelos na sua instância Azure Digital Twins, pode ver os modelos na sua instância Azure Digital Twins, incluindo qualquer herança e relacionamentos de modelos, utilizando o [**Visualizer Modelo Azure Digital Twins.**](https://github.com/Azure/opendigitaltwins-building-tools/tree/master/AdtModelVisualizer) Esta amostra está atualmente num estado de projeto. Encorajamos a comunidade de desenvolvimento de gémeos digitais a estender e contribuir para a amostra. 

## <a name="next-steps"></a>Passos seguintes

* Saiba a criação de modelos baseados em ontologias padrão da indústria: [ *Conceitos: O que é uma ontologia?*](concepts-ontologies.md)

* Mergulhar mais profundamente na gestão de modelos com operações API: [ *Como fazer: Gerir modelos DTDL*](how-to-manage-model.md)

* Saiba como os modelos são usados para criar gémeos digitais: [ *Conceitos: Gémeos Digitais e o gráfico gémeo*](concepts-twins-graph.md)

