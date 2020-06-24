---
title: Modelos personalizados
titleSuffix: Azure Digital Twins
description: Compreenda como a Azure Digital Twins utiliza modelos definidos pelo utilizador para descrever entidades no seu ambiente.
author: baanders
ms.author: baanders
ms.date: 3/12/2020
ms.topic: conceptual
ms.service: digital-twins
ROBOTS: NOINDEX, NOFOLLOW
ms.openlocfilehash: cacf4c21e92b434aeb73cd76e6dda26508f41d77
ms.sourcegitcommit: 635114a0f07a2de310b34720856dd074aaf4f9cd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/23/2020
ms.locfileid: "85261363"
---
# <a name="understand-twin-models-in-azure-digital-twins"></a>Compreender modelos gémeos em Azure Digital Twins

[!INCLUDE [Azure Digital Twins current preview status](../../includes/digital-twins-preview-status.md)]

Uma característica chave da Azure Digital Twins é a capacidade de definir o seu próprio vocabulário e construir o seu gráfico gémeo nos termos auto-definidos do seu negócio. Esta capacidade é fornecida através de **modelos definidos**pelo utilizador. Podes pensar nas modelos como os substantivos numa descrição do teu mundo. 

Um modelo é semelhante a uma **classe** numa linguagem de programação orientada a objetos, definindo uma forma de dados para um conceito particular no seu ambiente de trabalho real. Os modelos têm nomes (como *O Ambiente* ou *O Medidor de Temperatura),* e contêm elementos como propriedades, telemetria/eventos e comandos que descrevem o que este tipo de entidade no seu ambiente pode fazer. Mais tarde, utilizará estes modelos para criar [**gémeos digitais**](concepts-twins-graph.md) que representam entidades específicas que cumprem esta descrição do tipo.

Os modelos são escritos utilizando a Linguagem de **Definição Digital DeFins (DTDL)** baseada em JSON.  

## <a name="digital-twin-definition-language-dtdl-for-writing-models"></a>Linguagem de definição de gémeos digital (DTDL) para modelos de escrita

Os modelos para Gémeos Digitais Azure são definidos usando a linguagem de definição de gémeos digitais (DTDL). O DTDL baseia-se no JSON-LD e é independente da linguagem de programação. O DTDL não é exclusivo da Azure Digital Twins, mas também é usado para representar dados de dispositivos em outros serviços IoT, como [ioT Plug e Play.](../iot-pnp/overview-iot-plug-and-play.md) Azure Digital Twins usa a versão DTDL *2*.

> [!TIP] 
> Nem todos os serviços que utilizam o DTDL implementam exatamente as mesmas funcionalidades do DTDL. Por exemplo, o IoT Plug and Play não utiliza as funcionalidades DTDL que são para gráficos, enquanto a Azure Digital Twins não implementa atualmente comandos DTDL. Para obter mais informações sobre as funcionalidades DTDL específicas da Azure Digital Twins, consulte a secção mais tarde neste artigo sobre [as especificações de implementação do DTDL das Gémeas Digitais Azure.](#azure-digital-twins-dtdl-implementation-specifics)

Para obter mais informações sobre o DTDL em geral, consulte a sua documentação de especificação no GitHub: [Digital Twins Definition Language (DTDL) - versão 2](https://github.com/Azure/opendigitaltwins-dtdl/blob/master/DTDL/v2/dtdlv2.md).

## <a name="elements-of-a-model"></a>Elementos de um modelo

Dentro de uma definição de modelo, o item de código de nível superior é uma **interface**. Isto encapsula todo o modelo, e o resto do modelo é definido dentro da interface. 

Uma interface de modelo DTDL pode conter zero, um ou muitos dos seguintes campos:
* **Propriedade** - Propriedades são campos de dados que representam o estado de uma entidade (como as propriedades em muitas linguagens de programação orientadas a objetos). Ao contrário da telemetria, que é um evento de dados ligado ao tempo, as propriedades têm armazenamento de suporte e podem ser lidas a qualquer momento.
* **Telemetria** - Os campos de telemetria representam medições ou eventos, e são frequentemente utilizados para descrever leituras de sensores de dispositivos. A telemetria não é armazenada num gémeo digital; é mais como um fluxo de eventos de dados prontos para ser enviados para algum lugar. 
* **Componente** - Os componentes permitem-lhe construir a interface do seu modelo como conjunto de outras interfaces, se quiser. Um exemplo de um componente é uma interface *frontCamera* (e outra interface de componente *backCamera)* que são usadas na definição de um modelo para um *telefone*. Primeiro deve definir uma interface para *a FrontCamera* como se fosse o seu próprio modelo, e depois pode remundo-a ao definir *o Telefone*.

    Use um componente para descrever algo que é parte integrante da sua solução, mas que não precisa de uma identidade separada, e não precisa de ser criado, eliminado ou reorganizado no gráfico gémeo de forma independente. Se quiser que as entidades tenham existências independentes no gráfico gémeo, represente-as como gémeas digitais separadas de diferentes modelos, ligadas por *relacionamentos* (ver próxima bala).
    
    >[!TIP] 
    >Os componentes também podem ser usados para organização, para agrupar conjuntos de propriedades relacionadas dentro de uma interface de modelo. Nesta situação, pode pensar em cada componente como um espaço de nome ou "pasta" dentro da interface.
* **Relacionamento** - Relacionamentos permitem-lhe representar como um gémeo digital pode estar envolvido com outros gémeos digitais. As relações podem representar diferentes significados semânticos, tais como *contém* ("chão contém espaço"), *frescos* ("hvac cools room"), *isBilledTo* ("compressor é faturado para o utilizador"), etc. As relações permitem a solução fornecer um gráfico de entidades interrelacionadas.

> [!NOTE]
> A especificação para DTDL também define Comandos , que são métodos que podem ser **executados**num gémeo digital (como um comando de reset, ou um comando para ligar ou desligar uma ventoinha). No entanto, *os comandos não são atualmente suportados em Azure Digital Twins.*

### <a name="azure-digital-twins-dtdl-implementation-specifics"></a>Especificidades de implementação do Azure Digital Twins DTDL

Para que um modelo DTDL seja compatível com a Azure Digital Twins, deve satisfazer estes requisitos.

* Todos os elementos DTDL de nível superior num modelo devem ser de *interface*tipo . Isto porque o modelo Azure Digital Twins APIs pode receber objetos JSON que representam uma interface ou um conjunto de interfaces. Como resultado, nenhum outro tipo de elemento DTDL é permitido no nível superior.
* O DTDL para Azure Digital Twins não deve definir quaisquer *comandos*.
* O Azure Digital Twins só permite um único nível de nidificação de componentes. Isto significa que uma interface que está a ser usada como um componente não pode ter nenhum componente em si. 
* As interfaces não podem ser definidas em linha dentro de outras interfaces DTDL; devem ser definidas como entidades de alto nível separadas com as suas próprias identificações. Então, quando outra interface quiser incluir essa interface como componente ou através de herança, pode referenciar o seu ID.

## <a name="example-model-code"></a>Código modelo exemplo

Modelos de tipo gémeo podem ser escritos em qualquer editor de texto. A língua DTDL segue a sintaxe JSON, pelo que deve armazenar modelos com a extensão *.json*. A utilização da extensão JSON permitirá que muitos editores de texto de programação forneçam a verificação e a realce básicas de sintaxe para os seus documentos DTDL. Há também uma [extensão DTDL](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.vscode-dtdl) disponível para [Código de Estúdio Visual](https://code.visualstudio.com/).

Aqui está um exemplo de um modelo típico, escrito como uma interface DTDL. O modelo descreve planetas, cada um com um nome, uma massa e uma temperatura. O planeta pode ter luas como satélites, e pode conter crateras.

```json
[
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "Temperature",
        "schema": "double"
      },
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

Os campos do modelo são:

| Campo | Description |
| --- | --- |
| `@id` | Um identificador para o modelo. Deve estar no `dtmi:<domain>:<unique model identifier>;<model version number>` formato. |
| `@type` | Identifica o tipo de informação que está a ser descrita. Para uma interface, o tipo é *Interface.* |
| `@context` | Define o [contexto](https://niem.github.io/json/reference/json-ld/context/) para o documento JSON. Os modelos devem ser `dtmi:dtdl:context;2` utilizados. |
| `displayName` | [opcional] Permite-lhe dar ao modelo um nome amigável, se desejar. |
| `contents` | Todos os dados restantes da interface são colocados aqui, como uma variedade de definições de atributos. Cada atributo deve fornecer a `@type` (*Propriedade*, *Telemetria,* *Comando,* *Relacionamento,* *Ou Componente*) para identificar o tipo de informação de interface que descreve, e, em seguida, um conjunto de propriedades que definem o atributo real (por exemplo, e para definir `name` um `schema` *Imóvel).* |

> [!NOTE]
> Note que a interface do componente *(Cratera* neste exemplo) é definida na mesma matriz que a interface que a utiliza (*Planeta).* Os componentes devem ser definidos desta forma nas chamadas API para que a interface seja encontrada.

### <a name="possible-schemas"></a>Possíveis esquemas

De acordo com o DTDL, o esquema para atributos *de propriedade* e *telemetria* pode ser de tipos `integer` primitivos padrão, e `double` `string` `Boolean` outros tipos como `DateTime` e `Duration` . 

Além de tipos primitivos, os campos *de propriedade* e *telemetria* podem ter estes tipos complexos:
* `Object`
* `Map`
* `Enum`

Os campos *de telemetria* também suportam. `Array`

### <a name="model-inheritance"></a>Herança modelo

Às vezes, talvez queiras especializar um modelo. Por exemplo, pode ser útil ter um modelo genérico *Room,* e variantes especializadas *ConferenceRoom* e *Gym.* Para expressar a especialização, o DTDL suporta a herança: as interfaces podem herdar de uma ou mais outras interfaces. 

O exemplo a seguir reimagina o modelo *Planet* do exemplo DTDL anterior como um subtipo de um modelo *CelestialBody* maior. O modelo "pai" é definido primeiro, e depois o modelo "criança" baseia-se nele utilizando o campo `extends` .

```json
[
  {
    "@id": "dtmi:com:contoso:CelestialBody;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Celestial body",
    "contents": [
      {
        "@type": "Property",
        "name": "name",
        "schema": "string"
      },
      {
        "@type": "Property",
        "name": "mass",
        "schema": "double"
      },
      {
        "@type": "Telemetry",
        "name": "temperature",
        "schema": "double"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Planet;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2",
    "displayName": "Planet",
    "extends": "dtmi:com:contoso:CelestialBody;1",
    "contents": [
      {
        "@type": "Relationship",
        "name": "satellites",
        "target": "dtmi:com:contoso:Moon;1"
      },
      {
        "@type": "Component",
        "name": "deepestCrater",
        "schema": "dtmi:com:contoso:Crater;1"
      }
    ]
  },
  {
    "@id": "dtmi:com:contoso:Crater;1",
    "@type": "Interface",
    "@context": "dtmi:dtdl:context;2"
  }
]
```

Neste exemplo, *a CelestialBody* contribui com um nome, uma massa e uma temperatura para o *Planeta.* A `extends` secção é um nome de interface, ou um conjunto de nomes de interface (permitindo que a interface de extensão herde de vários modelos-mãe, se desejar).

Uma vez aplicada a herança, a interface de extensão expõe todas as propriedades de toda a cadeia de heranças.

A interface de extensão não pode alterar nenhuma das definições das interfaces-mãe; só pode adicioná-los. Também não pode redefinir uma capacidade já definida em nenhuma das suas interfaces-mãe (mesmo que as capacidades sejam definidas como as mesmas). Por exemplo, se uma interface-mãe define uma `double` *massa*de propriedade, a interface de extensão não pode conter uma declaração de *massa*, mesmo que seja também uma `double` .

## <a name="validating-models"></a>Modelos de validação

Existe uma amostra disponível para validar documentos de modelo para garantir que o DTDL é válido. É construído na biblioteca de parser DTDL e é agnóstico linguístico. Encontre-o aqui: [Amostra de Validador DTDL](https://docs.microsoft.com/samples/azure-samples/dtdl-validator/dtdl-validator).

Ou, para obter mais informações sobre a biblioteca de parser, incluindo um exemplo de como usá-la diretamente, consulte [Como-a-fazer: Parse e valide modelos](how-to-use-parser.md).

## <a name="next-steps"></a>Passos seguintes

Veja como gerir os modelos com as APIs digitalTwinsModels:
* [Como fazer: Gerir um modelo gémeo](how-to-manage-model.md)

Ou, saiba como os gémeos digitais são criados com base em modelos:
* [Conceitos: Gémeos digitais e o gráfico gémeo](concepts-twins-graph.md)

