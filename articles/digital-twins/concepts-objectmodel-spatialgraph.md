---
title: Compreender os modelos de objeto de duplos Digital e gráfico de inteligência geográficos | Documentos da Microsoft
description: Utilize Azure Digital Twins para modelar relações entre pessoas, lugares e dispositivos
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/14/2018
ms.author: alinast
ms.openlocfilehash: e7efe1a8632643e2a299b6c9a1b1407414deee4b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542952"
---
# <a name="understand-digital-twins-object-models-and-spatial-intelligence-graph"></a>Compreender os modelos de objeto de duplos Digital e gráfico de inteligência espaciais

Os gémeos Digital do Azure é um serviço de IoT do Azure que alimenta representações virtuais abrangentes de ambientes físicos e associados dispositivos, sensores e as pessoas. Melhora a desenvolvimento ao organizar conceitos específicos ao domínio em modelos úteis. Os modelos são, em seguida, situados dentro de um gráfico de inteligência espaciais. Esses conceitos modelam fielmente as relações e interações entre pessoas, espaços e dispositivos.

Modelos de objetos duplos digital descrevem os conceitos de domínio específico, categorias e propriedades. Modelos estão predefinidos por utilizadores que pretendem personalizar a solução para suas necessidades específicas. Em conjunto, estas predefinidas duplos Digital, modelos de objeto constituem uma _ontologia_. Ontologia de um prédio inteligente descreve regiões, locais, pisos, escritórios, zonas, salas de conferência e salas de foco. Uma ontologia de grade de energia descreve várias estações de energia, subestações, recursos energéticos e clientes. Com modelos de objetos duplos Digital e ontologies, diversos cenários e as suas necessidades podem ser personalizadas.

Com modelos de objetos duplos Digital e uma ontologia no local, pode preencher um _geográfico gráfico_. Gráficos geográficos são representações virtuais das relações muitos entre espaços, dispositivos e pessoas que são relevantes para uma solução de IoT. Este diagrama mostra um exemplo de um gráfico geográfico que utiliza ontologia de um prédio inteligente.

![Construção de gráfico geográficos duplos digital][1]

<a id="model"></a>

O gráfico geográfico reúne espaços, dispositivos, sensores e os utilizadores. Cada um é vinculado de forma que modela o mundo real. Neste exemplo, na localização 43 tem quatro pisos, cada um com muitas áreas diferentes. Os utilizadores estão associados com suas estações de trabalho e dadas acesso a partes do gráfico. Um administrador tem os direitos para efetuar alterações ao gráfico espacial, enquanto que um visitante tem direitos para ver apenas a determinados dados de criação.

## <a name="digital-twins-object-models"></a>Modelos de objetos duplos digital

Modelos de objetos duplos digital suportam estas categorias principais de objetos:

- **Espaços** são localizações físicas ou virtuais, por exemplo, `Tenant`, `Customer`, `Region`, e `Venue`.
- **Dispositivos** são partes virtuais ou físicas dos equipamentos, por exemplo, `AwesomeCompany Device` e `Raspberry Pi 3`.
- **Sensores** são objetos que detete eventos de, por exemplo, `AwesomeCompany Temperature Sensor` e `AwesomeCompany Presence Sensor`.
- **Os utilizadores** identificar occupants e suas características.

Outras categorias de objetos são:

- **Recursos** anexados a um espaço e normalmente representam os recursos do Azure para serem utilizadas pelos objetos no gráfico espacial, por exemplo, `IoTHub`.
- **BLOBs** estão ligados a objetos (por exemplo, espaços, dispositivos, sensores e os utilizadores). Eles são usados como ficheiros com o tipo de mime e metadados, por exemplo, `maps`, `pictures`, e `manuals`.
- **Expandido tipos** são enumerações extensíveis que aumentam as entidades com características específicas, por exemplo `SpaceType` e `SpaceSubtype`.
- **Ontologies** representam um conjunto de tipos expandidos, por exemplo, `Default`, `Building`, `BACnet`, e `EnergyGrid`.
- **Propriedade chaves e valores** são características personalizadas de espaços, dispositivos, sensores e os utilizadores. Eles podem ser usados juntamente com características incorporadas, por exemplo, `DeltaProcessingRefreshTime` como chave e `10` como valor.
- **Funções** são conjuntos de permissões atribuídas a utilizadores e dispositivos no gráfico espacial, por exemplo, `Space Administrator`, `User Administrator`, e `Device Administrator`.
- **Atribuições de funções** são a associação entre uma função e um objeto no gráfico espacial. Por exemplo, um utilizador ou um principal de serviço pode ser concedido permissão para gerir um espaço no gráfico espacial.
- **Arquivos de chave de segurança** fornecer as chaves de segurança para todos os dispositivos da hierarquia num objeto de determinado espaço para permitir que o dispositivo de comunicar de forma segura duplos Digital.
- **Funções definidas pelo utilizador** (UDFs) permitem dentro do gráfico geográfico de processamento de telemetria de sensores personalizáveis. Por exemplo, uma UDF pode:
  - Defina um valor de sensor.
  - Executar a lógica personalizada com base nas leituras dos sensores e definir a saída para um espaço.
  - Anexe metadados a um espaço.
  - Envie notificações quando forem cumpridas condições predefinidas. Atualmente, os UDFs podem ser escritos em JavaScript.
- **Matchers** são objetos que determinam quais UDFs são executados de uma mensagem de telemetria de determinado.
- **Pontos finais** são os locais onde as mensagens de telemetria e eventos de duplos Digital podem ser encaminhados, por exemplo, `Event Hub`, `Service Bus`, e `Event Grid`.

<a id="graph"></a>

## <a name="spatial-intelligence-graph"></a>Gráfico de inteligência espacial

Gráfico espacial é o gráfico hierárquico de espaços, dispositivos e pessoas definidas no modelo de objeto duplos Digital. O gráfico geográfico oferece suporte a herança, filtragem, atravessando, escalabilidade e extensibilidade. Pode gerir e interagir com o gráfico espacial com uma coleção de REST APIs.

Se implementar um serviço de duplos Digital na sua subscrição, se tornar o administrador global do nó raiz. Em seguida, temos automaticamente acesso total para a estrutura inteira. Aprovisionar espaços no gráfico com a API de espaço. Aprovisione serviços com a API de dispositivos e sensores, utilizando a API do Sensor. [Ferramentas de código aberto](https://github.com/Azure-Samples/digital-twins-samples-csharp) também estão disponíveis para aprovisionar o gráfico em massa.

**Criar um gráfico de herança**. Herança aplica-se para as permissões e as propriedades que são originadas um nó principal para todos os nós abaixo dele. Por exemplo, quando uma função é atribuída a um usuário num determinado nó, o utilizador tem permissões dessa função para o nó fornecido e todos os nós abaixo do mesmo. Cada chave de propriedade e o tipo de extensão definidos para um determinado nó é herdado por todos os nós abaixo desse nó.

**Filtragem de gráfico**. Filtragem é utilizada para limitar os resultados do pedido. Pode filtrar por IDs, nome, tipos, subtipos, espaço de principal e espaços associados. Também pode filtrar por tipos de dados de sensor, chaves de propriedade e os valores, *atravessar*, *minLevel*, *maxLevel*e outros parâmetros de filtro de OData.

**Gráfico atravessando**. Pode percorrer o gráfico geográfico através do respetivo profundidade e amplitude. Para detalhes, percorrer o gráfico de cima para baixo ou de baixo para cima, utilizando os parâmetros *atravessar*, *minLevel*, e *maxLevel*. Percorra o gráfico para obter nós colateral diretamente ligados a um espaço de principal ou um dos respetivos descendentes para amplitude. Ao consultar um objeto, pode obter todos os objetos relacionados que tenham as relações para esse objeto ao utilizar o *inclui* parâmetro das APIs obter.

**Criar um gráfico de escalabilidade**. Duplos digital garante escalabilidade de gráfico, de modo que possa manipular suas cargas de trabalho do mundo real. Os gémeos digital podem ser utilizados para representar grandes portefólios de imóveis, infraestrutura, dispositivos, sensores, telemetria e muito mais.

**Extensibilidade de gráfico**. Utilize a extensibilidade para personalizar os modelos de objeto de duplos Digital subjacentes com novos tipos e ontologies. Os dados de duplos Digital também podem ser enriquecidos com propriedades extensíveis e valores.

### <a name="spatial-intelligence-graph-management-apis"></a>Gráfico de inteligência geográficos APIs de gestão

Depois de implementar duplos Digital do [portal do Azure](https://portal.azure.com), o [Swagger](https://swagger.io/tools/swagger-ui/) URL das APIs de gestão é gerado automaticamente. É apresentado no portal do Azure no **descrição geral** secção com o seguinte formato.

```plaintext
https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/swagger
```

| Name | Substituir |
| --- | --- |
| YOUR_INSTANCE_NAME | O nome da sua instância de duplos Digital |
| YOUR_LOCATION | Qual sua instância estiver alojada num servidor a região |

 O formato de URL completo é apresentado nesta imagem.

![Portal de duplos digital API de gestão][2]

Para obter mais detalhes sobre como utilizar gráficos de inteligência espaciais, visite a pré-estreia de APIs de gestão do Azure Digital duplos.

> [!TIP]
> Pré-estreia do Swagger é fornecido demonstrar o recurso de API definido.
> Ele é hospedado pelo [docs.westcentralus.azuresmartspaces.net/management/swagger](https://docs.westcentralus.azuresmartspaces.net/management/swagger).

Saiba mais sobre [como utilizar o Swagger](how-to-use-swagger.md).

Todas as chamadas de API tem de ser autenticadas utilizando [OAuth](https://docs.microsoft.com/azure/active-directory/develop/v1-protocols-oauth-code). Siga as APIs [convenções de diretrizes do Microsoft REST API](https://github.com/Microsoft/api-guidelines/blob/master/Guidelines.md). A maioria das APIs que retornam coleções suporta [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) opções de consulta do sistema.

## <a name="next-steps"></a>Passos Seguintes

- Para saber mais sobre a conectividade do dispositivo e como enviar mensagens de telemetria para o Digital duplos, leia [entrada de telemetria e de conectividade do dispositivo de duplos Digital do Azure](concepts-device-ingress.md).

- Para saber mais sobre as limitações de API de gestão e limitações, leia [gestão de API do Azure Digital duplos e limitações](concepts-service-limits.md).

<!-- Images -->
[1]: media/concepts/digital-twins-spatial-graph-building.png
[2]: media/concepts/digital-twins-spatial-graph-management-api-url.png
