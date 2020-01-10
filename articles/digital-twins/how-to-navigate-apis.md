---
title: Navegar pelas APIs – Azure digital gêmeos | Microsoft Docs
description: Saiba como padrões comuns de consulta das APIs de gerenciamento de gêmeos digital do Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 12/27/2019
ms.openlocfilehash: 86ade45cd00e82e8787a117c23003d2a74750cf0
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552173"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Como usar as APIs de gerenciamento de gêmeos digital do Azure

As APIs de gerenciamento de gêmeos digital do Azure fornecem funcionalidades poderosas para seus aplicativos de IoT. Este artigo mostra como navegar pela estrutura de API.  

## <a name="api-summary"></a>Resumo da API

A lista a seguir mostra os componentes das APIs do digital gêmeos.

* [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): essas APIs interagem com os locais físicos na sua configuração. Eles ajudam a criar, excluir e gerenciar os mapeamentos digitais de seus locais físicos na forma de um [grafo espacial](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): essas APIs interagem com os dispositivos na sua configuração. Esses dispositivos podem gerenciar um ou mais sensores. Por exemplo, um dispositivo pode ser seu telefone ou um pod de sensor Raspberry Pi ou um gateway Lora,, e assim por diante.

* [/Sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): essas APIs ajudam você a se comunicar com os sensores associados aos seus dispositivos e seus locais físicos. Os sensores registram e enviam valores de ambiente que podem ser usados para manipular seu ambiente espacial.  

* [/Resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): essas APIs ajudam a configurar recursos, como um hub IOT, para sua instância de gêmeos digital.

* [/Types](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): essas APIs permitem que você associe tipos estendidos a seus objetos gêmeos digitais, para adicionar características específicas a esses objetos. Esses tipos permitem a filtragem fácil e o agrupamento de objetos na interface do usuário e as funções personalizadas que processam os dados de telemetria. Os exemplos de tipos estendidos são o *DeviceType*, o *SensorType*, o *SensorDataType*, o *spacetype*, o *SpaceSubType*, o *SpaceBlobType*, o *SpaceResourceType*e assim por diante.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): essas APIs ajudam a gerenciar ontologies, que são coleções de tipos estendidos. Ontologies fornece nomes para tipos de objeto de acordo com o espaço físico que eles representam. Por exemplo, o *BACnet* ontologia fornece nomes específicos para *tipos de sensor*, *tipo*de datatipos, *datasubtypes*e *dataunittypes*. Ontologies são gerenciados e criados pelo serviço. Os usuários podem carregar e descarregar o ontologies. Quando um ontologia é carregado, todos os seus nomes de tipo associados são habilitados e prontos para serem provisionados no grafo espacial. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): você pode usar essas APIs para criar propriedades personalizadas para seus *espaços*, *dispositivos*, *usuários*e *sensores*. Essas propriedades são criadas como pares de chave/valor. Você pode definir o tipo de dados para essas propriedades definindo seu *PrimitiveDataType*. Por exemplo, você pode definir uma propriedade chamada *BasicTemperatureDeltaProcessingRefreshTime* do tipo *uint* para seus sensores e, em seguida, atribuir um valor para essa propriedade para cada um dos sensores. Você também pode adicionar restrições para esses valores ao criar a propriedade, como intervalos *mínimos* e *máximos* , bem como os valores permitidos como *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): essas APIs permitem que você especifique as condições que deseja avaliar dos dados de seu dispositivo de entrada. Veja [este artigo](concepts-user-defined-functions.md#matchers) para obter mais informações. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): essas APIs permitem que você crie, exclua ou atualize uma função personalizada que será executada quando as condições definidas pelos *correspondentes* ocorrerem, para processar dados provenientes da sua instalação. Consulte [Este artigo](concepts-user-defined-functions.md#user-defined-functions) para obter mais informações sobre essas funções personalizadas, também chamadas de *funções definidas pelo usuário*. 

* [/Endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): essas APIs permitem que você crie pontos de extremidade para que sua solução de gêmeos digital possa se comunicar com outros serviços do Azure para o armazenamento e a análise de dados. Leia [Este artigo](concepts-events-routing.md) para obter mais informações. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): essas APIs permitem que você gerencie repositórios de chaves de segurança para seus espaços. Esses armazenamentos podem conter uma coleção de chaves de segurança e permitem que você recupere facilmente as chaves válidas mais recentes.

* [/Users](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): essas APIs permitem que você associe usuários a seus espaços, para localizar esses indivíduos quando necessário. 

* [/estado](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): essas APIs permitem que você gerencie configurações de todo o sistema, como os tipos padrão de espaços e sensores. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): essas APIs permitem que você associe funções a entidades, como ID de usuário, ID de função definida pelo usuário, etc. Cada atribuição de função inclui a ID da entidade a ser associada, o tipo de entidade, a ID da função a ser associada, a ID do locatário e um caminho que define o limite superior do recurso que a entidade pode acessar com essa associação. Leia [Este artigo](security-role-based-access-control.md) para obter mais informações.


## <a name="api-navigation"></a>Navegação de API

As APIs do digital gêmeos dão suporte à filtragem e navegação em todo o grafo espacial usando os seguintes parâmetros:

- **spaceid**: a API filtrará os resultados pela ID de espaço fornecida. Além disso, o sinalizador booliano **useParentSpace** é aplicável às APIs [/Spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) , que indica que a ID de espaço fornecida se refere ao espaço pai em vez do espaço atual. 

- **minLevel** e **maxLevel**: os espaços raiz são considerados no nível 1. Espaços com espaço pai no nível *n* estão no nível *n + 1*. Com esses valores definidos, você pode filtrar os resultados em níveis específicos. Esses são valores inclusivos quando definidos. Dispositivos, sensores e outros objetos são considerados no mesmo nível que o espaço mais próximo. Para obter todos os objetos em um determinado nível, defina **minLevel** e **maxLevel** com o mesmo valor.

- **minRelative** e **maxRelative**: quando esses filtros são fornecidos, o nível correspondente é relativo ao nível da ID de espaço fornecida:
   - O nível relativo *0* é igual ao mesmo nível da ID de espaço fornecida.
   - O nível relativo *1* representa espaços no mesmo nível que os filhos da ID de espaço fornecida. O nível relativo *n* representa os espaços inferiores ao espaço especificado em *n* níveis.
   - Nível relativo *-1* representa espaços no mesmo nível que o espaço pai do espaço especificado.

- **desviar**: permite que você percorra em ambas as direções de uma determinada ID de espaço, conforme especificado pelos valores a seguir.
   - **Nenhum**: esse valor padrão é filtrado para a ID de espaço fornecida.
   - **Para baixo**: isso filtra pela ID de espaço e seus descendentes fornecidos. 
   - **Para cima**: isso é filtrado pela ID de espaço e seus ancestrais. 
   - **Span**: isso filtra uma parte horizontal do grafo espacial, no mesmo nível da ID de espaço fornecida. Isso precisa que o **minRelative** ou o **maxRelative** seja definido como true. 


### <a name="examples"></a>Exemplos

A lista a seguir mostra alguns exemplos de navegação por meio das APIs [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) . Observe que o espaço reservado `YOUR_MANAGEMENT_API_URL` refere-se ao URI das APIs do digital gêmeos no formato `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, em que `YOUR_INSTANCE_NAME` é o nome da instância do gêmeos digital do Azure e `YOUR_LOCATION` é a região em que a instância está hospedada.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` retorna todos os dispositivos anexados a espaços raiz.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` retorna todos os dispositivos conectados aos espaços dos níveis 2, 3 ou 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` retorna todos os dispositivos diretamente anexados a myspaceid.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` retorna todos os dispositivos conectados a myspaceid ou um de seus descendentes.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` retorna todos os dispositivos anexados a descendentes de myspaceid, excluindo myspaceid.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` retorna todos os dispositivos anexados a filhos imediatos de myspaceid.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` retorna todos os dispositivos anexados a um dos ancestrais de myspaceid.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` retorna todos os dispositivos anexados a descendentes de myspaceid que estão no nível menor ou igual a 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` retorna todos os dispositivos anexados a espaços que estão no mesmo nível que myspaceid.


## <a name="odata-support"></a>Suporte a OData

A maioria das APIs que retornam coleções, como uma chamada GET em/Spaces, oferece suporte ao seguinte subconjunto das opções de consulta genérica do sistema [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) :  

* **$filter**
* **$orderby** 
* **$top**
* **$Skip** -se você pretende exibir a coleção inteira, solicite-a como um conjunto completo em uma única chamada e, em seguida, execute a paginação em seu aplicativo. 

> [!NOTE]
> Algumas opções de OData (como opções de consulta **$Count**, **$Expand**e **$Search**) não têm suporte atualmente.

### <a name="examples"></a>Exemplos

A lista a seguir descreve várias consultas com sintaxe de OData válida:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Passos seguintes

Para aprender alguns padrões comuns de consulta de API, leia [como consultar as APIs do Azure digital gêmeos para tarefas comuns](./how-to-query-common-apis.md).

Para saber mais sobre seus pontos de extremidade de API, leia [como usar o Swagger digital gêmeos](./how-to-use-swagger.md).

Para examinar a sintaxe do OData e os operadores de comparação disponíveis, leia [operadores de comparação OData no Azure pesquisa cognitiva](../search/search-query-odata-comparison-operators.md).
