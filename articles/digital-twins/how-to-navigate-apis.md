---
title: Navegue pelas APIs - Azure Digital Twins [ Azure Digital Twins] Microsoft Docs
description: Aprenda a padrões comuns de consulta das APIs de gestão de Gémeos Digitais Azure.
ms.author: alinast
author: alinamstanciu
manager: bertvanhoof
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 02/24/2020
ms.openlocfilehash: e9cdfd40a9672d19ef32dede0baadcdd56266bab
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79265171"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Como usar as APIs de gestão de Gémeos Digitais Azure

As APIs de gestão de Gémeos Digitais Azure fornecem funcionalidades poderosas para as suas aplicações IoT. Este artigo mostra-lhe como navegar pela estrutura da API.  

## <a name="api-summary"></a>Resumo da API

A lista que se segue mostra os componentes das APIs das Gémeas Digitais.

* [/espaços](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Estas APIs interagem com os locais físicos da sua configuração. Estes ajudam a criar, apagar e gerir os mapeamentos digitais das suas localizações físicas sob a forma de um [gráfico espacial.](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph)

* [/dispositivos:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices)Estas APIs interagem com os dispositivos da sua configuração. Estes dispositivos podem gerir um ou mais sensores. Por exemplo, um dispositivo pode ser o seu telefone, ou uma cápsula de sensor Raspberry Pi, ou um gateway Lora, e assim por diante.

* [/sensores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Estas APIs ajudam-no a comunicar com os sensores associados aos seus dispositivos e às suas localizações físicas. Os sensores registam e enviam valores ambiente que podem ser usados para manipular o seu ambiente espacial.  

* [/recursos](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Estas APIs ajudam-no a criar recursos, como um hub IoT, para a sua instância de Gémeos Digitais.

* [/tipos](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Estas APIs permitem associar tipos estendidos com os seus objetos De Gémeos Digitais, para adicionar características específicas a esses objetos. Estes tipos permitem uma fácil filtragem e agrupamento de objetos na UI e as funções personalizadas que processam os seus dados de telemetria. Exemplos de tipos estendidos são *DispositivoType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType,* *SpaceBlobType,* *SpaceResourceType , SpaceResourceType*, e assim por diante.

* [/ontologias](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Estas APIs ajudam-no a gerir ontologias, que são coleções de tipos estendidos. As ontologias fornecem nomes para tipos de objetos de acordo com o espaço físico que representam. Por exemplo, a ontologia *BACnet* fornece nomes específicos para tipos de *sensores,* tipos de *dados,* tipos de *dados*e tipos de *dataunitais.* As ontologias são geridas e criadas pelo serviço. Os utilizadores podem carregar e descarregar ontologias. Quando uma ontologia é carregada, todos os seus nomes de tipo associados estão ativados e prontos para serem aprovisionados no seu gráfico espacial. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Pode utilizar estas APIs para criar propriedades personalizadas para os seus *espaços,* *dispositivos,* *utilizadores*e *sensores*. Estas propriedades são criadas como par de chaves/valor. Pode definir o tipo de dados para estas propriedades definindo o seu *PrimitivoDataType*. Por exemplo, pode definir uma propriedade denominada *BasicTemperatureDeltaProcessingRefreshTime* do tipo *uint* para os seus sensores e, em seguida, atribuir um valor para esta propriedade para cada um dos seus sensores. Também pode adicionar constrangimentos a estes valores ao mesmo tempo que cria a propriedade, como as gamas *Min* e *Max,* bem como valores permitidos como *ValidaçãoData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Estas APIs permitem especificar as condições que pretende avaliar a partir dos dados do seu dispositivo de entrada. Veja [este artigo](concepts-user-defined-functions.md#matchers) para obter mais informações. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Estas APIs permitem-lhe criar, excluir ou atualizar uma função personalizada que executará quando ocorrem as condições definidas pelos *matchers,* para processar os dados provenientes da sua configuração. Consulte [este artigo](concepts-user-defined-functions.md#user-defined-functions) para obter mais informações sobre estas funções personalizadas, também chamadas *funções definidas pelo utilizador*. 

* [/pontos finais](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Estas APIs permitem criar pontos finais para que a sua solução Digital Twins possa comunicar com outros serviços Azure para armazenamento de dados e análise. Leia [este artigo](concepts-events-routing.md) para mais informações. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Estas APIs permitem-lhe gerir as principais lojas de segurança para os seus espaços. Estas lojas podem conter uma coleção de chaves de segurança e permitir-lhe obter facilmente as chaves válidas mais recentes.

* [/utilizadores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Estas APIs permitem associar os utilizadores aos seus espaços, localizar estes indivíduos quando necessário. 

* [/sistema:](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System)Estas APIs permitem gerir as definições em todo o sistema, tais como os tipos padrão de espaços e sensores. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Estas APIs permitem-lhe associar funções a entidades como id do utilizador, ID de função definida pelo utilizador, etc. Cada atribuição de funções inclui a identificação da entidade para associado, o tipo de entidade, a identificação do papel a associado, o ID do arrendatário, e um caminho que define o limite superior do recurso a que a entidade pode aceder com essa associação. Leia [este artigo](security-role-based-access-control.md) para mais informações.


## <a name="api-navigation"></a>Navegação API

As APIs de suporte digital das Gémeas Digitais suportam a filtragem e navegação em todo o seu gráfico espacial utilizando os seguintes parâmetros:

- **spaceId**: A API filtrará os resultados pelo ID de espaço dado. Além disso, o uso booleano da **bandeiraParentSpace** é aplicável às [APIs/espaços,](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) o que indica que o ID de espaço dado se refere ao espaço principal em vez do espaço atual. 

- **minLevel** e **maxLevel**: Os espaços de raiz são considerados de nível 1. Os espaços com espaço para pais no n º *n* estão no nível *n+1*. Com estes valores definidos, pode filtrar os resultados a níveis específicos. Estes são valores inclusivos quando definidos. Dispositivos, sensores e outros objetos são considerados ao mesmo nível do seu espaço mais próximo. Para obter todos os objetos a um determinado nível, coloque tanto o **minLevel como** **o maxLevel** no mesmo valor.

- **minRelativo** e **maxRelative:** Quando estes filtros são administrados, o nível correspondente é relativo ao nível do ID de espaço dado:
   - O nível relativo *0* é o mesmo nível que o ID espacial dado.
   - O nível relativo *1* representa espaços ao mesmo nível que as crianças do id espacial dado. O nível relativo *n* representa espaços inferiores ao espaço especificado *por* n níveis.
   - O nível relativo *-1* representa espaços ao mesmo nível do espaço dos pais do espaço especificado.

- **traverse**: Permite-lhe atravessar em qualquer direção a partir de um determinado ID de espaço, conforme especificado pelos seguintes valores.
   - **Nenhum**: Este valor predefinido filtra o ID de espaço dado.
   - **Down**: Este filtro supõe-se pelo ID espacial dado e seus descendentes. 
   - **Up**: Este filtro saem pelo ID espacial dado e seus ancestrais. 
   - **Extensão**: Isto filtra uma parte horizontal do gráfico espacial, ao mesmo nível do ID de espaço dado. Isto precisa do **minRelative** ou do **maxRelative** para ser definido de verdade. 


### <a name="examples"></a>Exemplos

A lista que se segue mostra alguns exemplos de navegação através das APIs [/dispositivos.](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) Note que o `YOUR_MANAGEMENT_API_URL` espaço reservado se refere ao URI das `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`APIs das Gémeas Digitais no formato , onde `YOUR_INSTANCE_NAME` está o nome da sua instância Azure Digital Twins, e `YOUR_LOCATION` é a região onde a sua instância está hospedada.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1`devolve todos os dispositivos ligados aos espaços radiculares.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4`devolve todos os dispositivos ligados a espaços dos níveis 2, 3 ou 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId`devolve todos os dispositivos diretamente ligados ao mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down`devolve todos os dispositivos ligados ao mySpaceId ou a um dos seus descendentes.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true`devolve todos os dispositivos ligados aos descendentes do mySpaceId, excluindo o mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true`devolve todos os dispositivos ligados a crianças imediatas do mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true`devolve todos os dispositivos ligados a um dos ancestrais do mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5`devolve todos os dispositivos ligados aos descendentes do mySpaceId que estão no nível inferior ou igual a 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true`devolve todos os dispositivos ligados a espaços que estão ao mesmo nível do meuSpaceId.


## <a name="odata-support"></a>Suporte oData

A maioria das APIs que devolvem coleções, tais como uma chamada GET/espaços, suportam o seguinte subconjunto das opções genéricas de consulta do sistema [OData:](https://www.odata.org/getting-started/basic-tutorial/#queryData)  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** - Se pretender exibir toda a coleção, deverá solicitá-la como um conjunto inteiro numa única chamada e, em seguida, realizar a paging na sua aplicação. 

> [!NOTE]
> Algumas opções oData (tais como opções de consulta **$count**, **$expand**, e **$search**) não são suportadas atualmente.

### <a name="examples"></a>Exemplos

A seguinte lista retrata várias consultas com sintaxe OData válida:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,'space')`
- `YOUR_MANAGEMENT_API_URL/devices?$filter=TypeId eq 2`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=StatusId ne 1`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,'k')&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor',Name)`
 
## <a name="next-steps"></a>Passos seguintes

Para aprender alguns padrões comuns de consulta da API, leia Como consultar as APIs de [Gémeos Digitais Azure para tarefas comuns](./how-to-query-common-apis.md).

Para saber mais sobre os seus pontos finais da API, leia [Como usar Gémeos Digitais Swagger](./how-to-use-swagger.md).

Para rever a sintaxe OData e os operadores de comparação disponíveis, leia os operadores de [comparação OData em Pesquisa Cognitiva Azure](../search/search-query-odata-comparison-operators.md).
