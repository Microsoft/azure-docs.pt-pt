---
title: Entender os modelos de objeto gêmeos do Azure e o grafo de inteligência espacial | Microsoft Docs
description: Utilize Azure Digital Twins para modelar relações entre pessoas, lugares e dispositivos
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 11/05/2019
ms.openlocfilehash: da345c0cf039e3cc2f7f8828a0769b6e65048571
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73930405"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Entender os modelos de objeto gêmeos digital e o grafo de inteligência espacial

O Azure digital gêmeos é um serviço de IoT do Azure que capacita representações virtuais abrangentes de ambientes físicos e dispositivos associados, sensores e pessoas. Ele melhora o desenvolvimento organizando conceitos específicos de domínio em modelos úteis. Os modelos são então situados em um grafo de inteligência espacial. Esses conceitos modelam de forma fiel as relações e interações entre pessoas, espaços e dispositivos.

Os modelos de objeto gêmeos digital descrevem conceitos, categorias e propriedades específicos do domínio. Os modelos são predefinidos por usuários que desejam personalizar a solução para suas necessidades específicas. Juntos, esses modelos de objeto de gêmeos digital predefinidos compõem um _ontologia_. Um ontologia de construção inteligente descreve regiões, locais, andares, escritórios, zonas, salas de conferência e salas de foco. Um ontologia de grade de energia descreve várias estações de energia, subestações, recursos de energia e clientes. Com os modelos de objeto gêmeos e ontologies, vários cenários e necessidades podem ser personalizados.

Com os modelos de objetos do digital gêmeos e um ontologia no lugar, você pode preencher um _grafo espacial_. Grafos espaciais são representações virtuais das muitas relações entre espaços, dispositivos e pessoas que são relevantes para uma solução de IoT. Este diagrama mostra um exemplo de um grafo espacial que usa um ontologia de construção inteligente.

[![criação de grafo espacial digital gêmeos](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

O grafo espacial reúne espaços, dispositivos, sensores e usuários. Cada uma é vinculada de uma forma que modela o mundo real. Neste exemplo, o local 43 tem quatro andares, cada um com muitas áreas diferentes. Os usuários estão associados às estações de trabalho e recebem acesso a partes do grafo. Um administrador tem os direitos de fazer alterações no grafo espacial, enquanto um visitante tem direitos para exibir apenas determinados dados de compilação.

## <a name="digital-twins-object-models"></a>Modelos de objeto gêmeos digital

Os modelos de objetos do digital gêmeos dão suporte a essas principais categorias de objetos:

- Os **espaços** são locais virtuais ou físicos, por exemplo, `Tenant`, `Customer`, `Region`e `Venue`.
- Os **dispositivos** são partes físicas ou virtuais de equipamentos, por exemplo, `AwesomeCompany Device` e `Raspberry Pi 3`.
- **Sensores** são objetos que detectam eventos, por exemplo, `AwesomeCompany Temperature Sensor` e `AwesomeCompany Presence Sensor`.
- **Os usuários** identificam o occupants e suas características.

Outras categorias de objetos são:

- Os **recursos** são anexados a um espaço e normalmente representam os recursos do Azure a serem usados por objetos no grafo espacial, por exemplo, `IoTHub`.
- Os **BLOBs** são anexados a objetos (como espaços, dispositivos, sensores e usuários). Eles são usados como arquivos com tipo MIME e metadados, por exemplo, `maps`, `pictures`e `manuals`.
- Os **tipos estendidos** são enumerações extensíveis que aumentam as entidades com características específicas, por exemplo `SpaceType` e `SpaceSubtype`.
- **Ontologies** representa um conjunto de tipos estendidos, por exemplo, `Default`, `Building`, `BACnet`e `EnergyGrid`.
- **As chaves de propriedade e os valores** são características personalizadas de espaços, dispositivos, sensores e usuários. Eles podem ser usados junto com características internas, por exemplo, `DeltaProcessingRefreshTime` como chave e `10` como valor.
- As **funções** são conjuntos de permissões atribuídas a usuários e dispositivos no grafo espacial, por exemplo, `Space Administrator`, `User Administrator`e `Device Administrator`.
- As **atribuições de função** são a associação entre uma função e um objeto no grafo espacial. Por exemplo, um usuário ou uma entidade de serviço pode receber permissão para gerenciar um espaço no grafo espacial.
- Os **repositórios de chaves de segurança** fornecem as chaves de segurança para todos os dispositivos na hierarquia em um determinado objeto de espaço para permitir que o dispositivo se comunique com segurança com o gêmeos digital.
- As UDFs ( **funções definidas pelo usuário** ) permitem o processamento de telemetria de sensor personalizável dentro do grafo espacial. Por exemplo, um UDF pode:
  - Defina um valor de sensor.
  - Execute a lógica personalizada com base nas leituras do sensor e defina a saída como um espaço.
  - Anexe metadados a um espaço.
  - Enviar notificações quando condições predefinidas forem atendidas. Atualmente, as UDFs podem ser escritas em JavaScript.
- Os **correspondências** são objetos que determinam quais UDFs são executados para uma determinada mensagem de telemetria.
- Os **pontos de extremidade** são os locais onde as mensagens de telemetria e os eventos de gêmeos digital podem ser roteados, por exemplo, `Event Hub`, `Service Bus`e `Event Grid`.

## <a name="spatial-intelligence-graph"></a>Gráfico de inteligência espacial

O grafo espacial é o grafo hierárquico de espaços, dispositivos e pessoas definidos no modelo de objeto digital gêmeos. O grafo espacial dá suporte à herança, filtragem, passagem, escalabilidade e extensibilidade. Você pode gerenciar e interagir com o grafo espacial com uma coleção de APIs REST.

Se você implantar um serviço de gêmeos digital em sua assinatura, você se tornará o administrador global do nó raiz. Em seguida, você recebe automaticamente acesso completo a toda a estrutura. Provisione espaços no grafo usando a API de espaço. Provisione serviços usando a API do dispositivo e os sensores usando a API do sensor. As [ferramentas de código-fonte aberto](https://github.com/Azure-Samples/digital-twins-samples-csharp) também estão disponíveis para provisionar o grafo em massa.

**Herança de grafo**. A herança se aplica às permissões e propriedades que descendem de um nó pai para todos os nós abaixo dela. Por exemplo, quando uma função é atribuída a um usuário em um determinado nó, o usuário tem as permissões dessa função para o nó fornecido e para cada nó abaixo dele. Cada chave de propriedade e tipo estendido definido para um determinado nó é herdado por todos os nós abaixo desse nó.

**Filtragem de gráfico**. A filtragem é usada para restringir os resultados da solicitação. Você pode filtrar por IDs, nome, tipos, subtipos, espaço pai e espaços associados. Você também pode filtrar por tipos de dados de sensor, chaves de propriedade e valores, *atravessar*, *minLevel*, *maxLevel*e outros parâmetros de filtro OData.

**Atravessamento de grafo**. Você pode percorrer o gráfico espacial por meio de sua profundidade e amplitude. Para profundidade, percorra o gráfico de cima para baixo ou de baixo para cima usando os parâmetros *Traversal*, *minLevel*e *maxLevel*. Percorra o grafo para obter nós irmãos diretamente anexados a um espaço pai ou a um de seus descendentes para amplitude. Ao consultar um objeto, você pode obter todos os objetos relacionados que têm relações com esse objeto usando o parâmetro *includes* das APIs Get.

**Escalabilidade do grafo**. O digital gêmeos garante a escalabilidade do grafo, para que possa lidar com suas cargas de trabalho do mundo real. O digital gêmeos pode ser usado para representar grandes portfólios de imóveis, infraestrutura, dispositivos, sensores, telemetria e muito mais.

**Extensibilidade do grafo**. Use a extensibilidade para personalizar os modelos de objeto de gêmeos digital subjacentes com novos tipos e ontologies. Os dados do digital gêmeos também podem ser aprimorados com propriedades e valores extensíveis.

### <a name="spatial-intelligence-graph-management-apis"></a>APIs de gerenciamento de grafo de inteligência espacial

Depois de implantar o gêmeos digital do [portal do Azure](https://portal.azure.com), a URL do [Swagger](https://swagger.io/tools/swagger-ui/) das APIs de gerenciamento é gerada automaticamente. Ele é exibido no portal do Azure na seção **visão geral** com o formato a seguir.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nome | Substituir |
| --- | --- |
| YOUR_INSTANCE_NAME | O nome da sua instância de gêmeos digital |
| YOUR_LOCATION | Em qual região do servidor sua instância está hospedada |

 O formato da URL completa aparece nessa imagem.

[API de gerenciamento do ![digital gêmeos Portal](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Para obter mais detalhes sobre como usar grafos de inteligência espacial, visite a versão prévia de APIs de gerenciamento de gêmeos digital do Azure.

> [!TIP]
> Uma visualização de espiadas do Swagger é fornecida para demonstrar o conjunto de recursos da API.
> Ele é hospedado em [docs.westcentralus.azuresmartspaces.net/Management/Swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Saiba mais sobre [como usar o Swagger](how-to-use-swagger.md).

Todas as chamadas à API devem ser autenticadas usando [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). As APIs seguem as [convenções de diretrizes da API REST da Microsoft](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). A maioria das APIs que retornam coleções dão suporte a opções de consulta do sistema [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) .

## <a name="next-steps"></a>Passos seguintes

- Para saber mais sobre a conectividade do dispositivo e como enviar mensagens de telemetria para gêmeos digital, leia [entrada de conectividade e telemetria do dispositivo gêmeos do Azure digital](concepts-device-ingress.md).

- Para saber mais sobre limitações e restrições da API de gerenciamento, leia [Gerenciamento e limitações do Azure digital gêmeos API](concepts-service-limits.md).
