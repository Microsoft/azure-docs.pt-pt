---
title: Compreender modelos de objetos e gráfico de inteligência espacial - Azure Digital Twins / Microsoft Docs
description: Use as Gémeas Digitais Azure para modelar relações entre pessoas, lugares e dispositivos
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/30/2019
ms.openlocfilehash: cafec321e7c40e27d8de731feda1103451271507
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265210"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Compreender modelos de objetos de gémeos digitais e gráfico de inteligência espacial

A Azure Digital Twins é um serviço Azure IoT que alimenta representações virtuais abrangentes de ambientes físicos e dispositivos associados, sensores e pessoas. Melhora o desenvolvimento organizando conceitos específicos de domínio em modelos úteis. Os modelos estão então situados dentro de um gráfico de inteligência espacial. Tais conceitos modelam fielmente as relações e interações entre pessoas, espaços e dispositivos.

Os modelos de objetos De Gémeos Digitais descrevem conceitos, categorias e propriedades específicos do domínio. Os modelos são predefinidos pelos utilizadores que pretendem adaptar a solução às suas necessidades específicas. Juntos, estes modelos de objetos digitais pré-definidos compõem uma _ontologia._ A ontologia de um edifício inteligente descreve regiões, locais, pisos, escritórios, zonas, salas de conferências e salas de foco. Uma ontologia da rede de energia descreve várias centrais elétricas, subestações, recursos energéticos e clientes. Com modelos de objetos De Gémeos Digitais e ontologias, diversos cenários e necessidades podem ser personalizados.

Com modelos de objetos De Gémeos Digitais e uma ontologia no lugar, você pode povoar um _gráfico espacial._ Gráficos espaciais são representações virtuais das muitas relações entre espaços, dispositivos e pessoas que são relevantes para uma solução IoT. Este diagrama mostra um exemplo de um gráfico espacial que usa a ontologia de um edifício inteligente.

[![Construção de gráficos espaciais de Gémeos Digitais](media/concepts/digital-twins-spatial-graph-building.png)](media/concepts/digital-twins-spatial-graph-building.png#lightbox)

O gráfico espacial reúne espaços, dispositivos, sensores e utilizadores. Cada um está ligado de uma forma que modela o mundo real. Nesta amostra, o local 43 tem quatro pisos, cada um com muitas áreas diferentes. Os utilizadores estão associados aos seus postos de trabalho e têm acesso a porções do gráfico. Um administrador tem o direito de fazer alterações no gráfico espacial, enquanto um visitante tem o direito de ver apenas determinados dados de construção.

## <a name="digital-twins-object-models"></a>Modelos de objetos de gémeos digitais

Os modelos de objetos Das Gémeas Digitais suportam estas categorias principais de objetos:

- **Os espaços** são locais virtuais `Tenant`ou `Customer` `Region`físicos, por exemplo, e `Venue`.
- **Os dispositivos** são peças de equipamento `AwesomeCompany Device` virtuais `Raspberry Pi 3`ou físicas, por exemplo, e .
- **Os sensores** são objetos que `AwesomeCompany Temperature Sensor` `AwesomeCompany Presence Sensor`detetam eventos, por exemplo, e .
- **Os utilizadores** identificam os ocupantes e as suas características.

Outras categorias de objetos são:

- **Os recursos** estão ligados a um espaço e normalmente representam recursos Azure para serem usados por objetos no gráfico espacial, por exemplo, `IoTHub`.
- **As bolhas** são anexadas a objetos (como espaços, dispositivos, sensores e utilizadores). São usados como ficheiros com tipo mímica `maps` `pictures`e `manuals`metadados, por exemplo, e .
- **Os tipos alargados** são enumerações extensíveis que aumentam `SpaceType` `SpaceSubtype`entidades com características específicas, por exemplo e .
- **As ontologias** representam um conjunto de `Default` `Building`tipos `BACnet`estendidos, por exemplo, e `EnergyGrid`.
- **As chaves e valores** de propriedade são características personalizadas de espaços, dispositivos, sensores e utilizadores. Podem ser utilizados juntamente com características `DeltaProcessingRefreshTime` incorporadas, por exemplo, como chave e `10` como valor.
- **As funções** são conjuntos de permissões atribuídas aos utilizadores `Space Administrator` `User Administrator`e `Device Administrator`dispositivos no gráfico espacial, por exemplo, , e .
- **As atribuições** de papéis são a associação entre um papel e um objeto no gráfico espacial. Por exemplo, um utilizador ou um diretor de serviço pode ser autorizado a gerir um espaço no gráfico espacial.
- **As lojas de chaves** de segurança fornecem as chaves de segurança para todos os dispositivos da hierarquia sob um determinado objeto espacial para permitir que o dispositivo se comunique com as Gémeas Digitais de forma segura.
- **As funções definidas pelo utilizador** (UDFs) permitem o processamento personalizável de telemetria do sensor dentro do gráfico espacial. Por exemplo, uma UDF pode:
  - Dete teo valor do sensor.
  - Execute a lógica personalizada com base nas leituras dos sensores e desempenhe a saída num espaço.
  - Anexar metadados a um espaço.
  - Envie notificações quando as condições predefinidas forem satisfeitas. Atualmente, os UDFs podem ser escritos no JavaScript.
- **Os matchers** são objetos que determinam quais os UDFs executados para uma determinada mensagem de telemetria.
- **Pontos finais** são os locais onde as mensagens de telemetria e os eventos de Gémeos Digitais podem ser encaminhados, por exemplo, `Event Hub`e `Service Bus`. `Event Grid`

## <a name="spatial-intelligence-graph"></a>Gráfico de inteligência espacial

O gráfico espacial é o gráfico hierárquico de espaços, dispositivos e pessoas definidos no modelo de objeto sinuoso das Gémeas Digitais. O gráfico espacial suporta a herança, filtragem, travessia, escalabilidade e extesensibilidade. Você pode gerir e interagir com o seu gráfico espacial com uma coleção de APIs REST.

Se implementar um serviço De Gémeos Digitais na sua subscrição, torna-se o administrador global do nó raiz. Em seguida, é-lhe automaticamente concedido acesso total a toda a estrutura. Disponibilize espaços no gráfico utilizando a API espacial. Serviços de prestação utilizando a API do dispositivo e os sensores utilizando a API do Sensor. [As ferramentas de código aberto](https://github.com/Azure-Samples/digital-twins-samples-csharp) também estão disponíveis para fornecer o gráfico a granel.

**Herança de gráfico.** A herança aplica-se às permissões e propriedades que descem de um nó dos pais a todos os nós por baixo. Por exemplo, quando uma função é atribuída a um utilizador num dado nó, o utilizador tem as permissões desse papel para o nó dado e cada nó abaixo. Cada chave de propriedade e tipo estendido definido para um dado nó é herdado por todos os nós abaixo desse nó.

**Filtragem de gráficos**. A filtragem é usada para reduzir os resultados dos pedidos. Pode filtrar por IDs, nome, tipos, subtipos, espaço dos pais e espaços associados. Também pode filtrar por tipos de dados de sensores, chaves de propriedade e valores, *travessia,* *minLevel,* *maxLevel*e outros parâmetros de filtro OData.

**Travessia de gráficos.** Pode atravessar o gráfico espacial através da sua profundidade e largura. Para profundidade, atravesse o gráfico em cima ou em baixo utilizando os parâmetros *que atravessam*, *minLevel*, e *maxLevel*. Atravesse o gráfico para obter nódosos de irmãos diretamente ligados a um espaço dos pais ou a um dos seus descendentes para a amplitude. Ao consultar um objeto, pode obter todos os objetos relacionados que tenham relações com esse objeto utilizando o parâmetro *de identificação* dos APIs get.

**Escalabilidade do gráfico.** As Gémeas Digitais garantem a escalabilidade do gráfico, para que possa lidar com as suas cargas de trabalho no mundo real. As Gémeas Digitais podem ser usadas para representar grandes portefólios de imóveis, infraestruturas, dispositivos, sensores, telemetria, entre outros.

**Extensibility do gráfico.** Use a extensibility para personalizar os modelos de objetos digitais gémeos subjacentes com novos tipos e ontologias. Os seus dados digitais também podem ser enriquecidos com propriedades e valores extensíveis.

### <a name="spatial-intelligence-graph-management-apis"></a>APIs de gestão de gráficos de inteligência espacial

Depois de implantar Gémeos Digitais a partir do [portal Azure,](https://portal.azure.com)o URL [Swagger](https://swagger.io/tools/swagger-ui/) das APIs de Gestão é gerado automaticamente. É exibido no portal Azure na secção **Visão Geral** com o seguinte formato.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Nome | Substituir |
| --- | --- |
| YOUR_INSTANCE_NAME | O nome da sua instância de Gémeos Digitais |
| YOUR_LOCATION | Qual região de servidor a sua instância está hospedada em |

 O formato URL completo aparece nesta imagem.

[![Digital Twins portal Management API](media/concepts/digital-twins-spatial-graph-management-api-url.png)](media/concepts/digital-twins-spatial-graph-management-api-url.png#lightbox)

Para mais detalhes sobre como usar gráficos de inteligência espacial, visite a pré-visualização de ApIs de Gestão de Gémeos Digitais Azure.

> [!TIP]
> Uma pré-visualização de sneak Swagger é fornecida para demonstrar o conjunto de funcionalidades API.
> Está hospedado no [docs.westcentralus.azuresmartspaces.net/management/swagger.](https://docs.westcentralus.azuresmartspaces.net/management/swagger)

Saiba mais sobre [como usar swagger.](how-to-use-swagger.md)

Todas as chamadas API devem ser autenticadas utilizando [o OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). As APIs seguem as convenções de [Diretrizes da Microsoft REST API](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). A maioria das APIs que devolvem as coleções suportam opções de consulta do sistema [OData.](https://www.odata.org/getting-started/basic-tutorial/#queryData)

## <a name="next-steps"></a>Passos seguintes

- Para saber sobre a conectividade do dispositivo e como enviar mensagens de telemetria para As Gémeas Digitais, leia a conectividade do dispositivo Azure Digital Twins e a entrada de [telemetria.](concepts-device-ingress.md)

- Para conhecer as limitações e acelerações da API de Gestão, leia a gestão e limitações da API das [Gémeas Digitais Azure.](concepts-service-limits.md)
