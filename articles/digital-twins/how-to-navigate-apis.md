---
title: Navegue de APIs de duplos Digital do Azure | Documentos da Microsoft
description: Saiba como padrões comuns de consultar as APIs de gestão de duplos Digital do Azure.
author: dsk-2015
manager: philmea
ms.service: digital-twins
services: digital-twins
ms.topic: conceptual
ms.date: 1/7/2019
ms.author: dkshir
ms.openlocfilehash: 9cfcdc879e36b93e21bff6f91886536d799553bb
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60924499"
---
# <a name="how-to-use-azure-digital-twins-management-apis"></a>Como utilizar as APIs de gestão de duplos Digital do Azure

As APIs de gestão de duplos Digital do Azure fornecem funcionalidades poderosas para as suas aplicações de IoT. Este artigo mostra-lhe como navegar pela estrutura de API.  

## <a name="api-summary"></a>Resumo da API

A lista seguinte mostra os componentes das APIs de duplos Digital.

* [/spaces](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces): Essas APIs interagirem com os locais físicos em sua configuração. Isso o ajudará a criar, eliminar e gerir os mapeamentos digital de suas localizações físicas na forma de um [geográfico gráfico](concepts-objectmodel-spatialgraph.md#spatial-intelligence-graph).

* [/Devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices): Essas APIs interagirem com os dispositivos em sua configuração. Estes dispositivos podem gerir os sensores de uma ou mais. Por exemplo, um dispositivo pode ser o seu telemóvel, ou um pod de sensor Raspberry Pi ou um gateway Lora e assim por diante.

* [/Sensors](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Sensors): Estas APIs ajudam a comunicar com os sensores associados com seus dispositivos e suas localizações físicas. Os sensores de registos e enviar valores de ambiente que, em seguida, podem ser usados para manipular o seu ambiente espacial.  

* [/resources](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Resources): Estas APIs ajudam a configurar recursos, tais como um hub IoT, para a sua instância de duplos Digital.

* [/ tipos](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Types): Essas APIs permitem que associe tipos expandidos com seus objetos duplos digitais, para adicionar características específicas a esses objetos. Esses tipos permitem a fácil de filtragem e agrupamento de objetos na interface do Usuário e as funções personalizadas que processam os seus dados de telemetria. São exemplos dos tipos de expandida *DeviceType*, *SensorType*, *SensorDataType*, *SpaceType*, *SpaceSubType* , *SpaceBlobType*, *SpaceResourceType*e assim por diante.

* [/ontologies](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Ontologies): Estas APIs ajudam a gerir ontologies, que são coleções de tipos expandidas. Ontologies fornecer nomes para tipos de objeto de acordo com o espaço físico que elas representam. Por exemplo, o *BACnet* ontologia fornece nomes específicos para *tipos de sensores*, *tipos de dados*, *datasubtypes*e o *dataunittypes*. Ontologies são geridos e criados pelo serviço. Os utilizadores podem carregar e descarregar ontologies. Quando uma ontologia é carregada, todos seus nomes de tipo associado estão ativadas e pronto a ser aprovisionado no seu gráfico espacial. 

* [/propertyKeys](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/PropertyKeys): Pode usar essas APIs para criar propriedades personalizadas para seus *espaços*, *dispositivos*, *utilizadores*, e *sensores*. Estas propriedades são criadas como pares chave/valor. Pode definir o tipo de dados para estas propriedades definindo suas *PrimitiveDataType*. Por exemplo, pode definir uma propriedade chamada *BasicTemperatureDeltaProcessingRefreshTime* do tipo *uint* para sua sensores e, em seguida, atribuir um valor para esta propriedade para cada um dos seus sensores. Também pode adicionar restrições para esses valores ao criar a propriedade, tal como *Min* e *máx* intervalos, bem como os valores permitidos como *ValidationData*.

* [/matchers](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Matchers): Essas APIs permitem-lhe especificar as condições que pretende avaliar a partir dos seus dados de dispositivo de entrada. Ver [este artigo](concepts-user-defined-functions.md#matchers) para obter mais informações. 

* [/userDefinedFunctions](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/UserDefinedFunctions): Essas APIs permitem-lhe criar, delete ou update em condições de uma função personalizada que será executado quando definidos pelos *matchers* ocorrem, para processar dados provenientes da sua configuração. Ver [este artigo](concepts-user-defined-functions.md#user-defined-functions) para obter mais informações sobre estas funções personalizadas, também denominado o *funções definidas pelo utilizador*. 

* [/Endpoints](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/Endpoints): Essas APIs permitem-lhe criar pontos finais para que a sua solução de duplos Digital pode comunicar com outros serviços do Azure para armazenamento de dados e análise. Leia [este artigo](concepts-events-routing.md) para obter mais informações. 

* [/keyStores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#/KeyStores): Essas APIs permitem que gerencie arquivos de chave de segurança para seus espaços. Estes arquivos podem conter uma coleção de chaves de segurança e permitir que recupere facilmente as chaves mais recente válidas.

* [/utilizadores](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Users): Essas APIs permitem que associe utilizadores ao seu espaços, para localizar estes indivíduos quando necessário. 

* [. cmd /System](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/System): Essas APIs permitem-lhe gerir as definições globais do sistema, tais como os tipos de padrão de espaços e sensores. 

* [/roleAssignments](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/RoleAssignments): Essas APIs permitem-lhe associar funções de entidades, como ID de utilizador, ID de função definida pelo utilizador, etc. Cada atribuição de função inclui o ID da entidade para associar, o tipo de entidade, o ID da função para associar, o ID de inquilino e um caminho que define o limite superior do recurso que a entidade pode acessar com essa associação. Leia [este artigo](security-role-based-access-control.md) para obter mais informações.


## <a name="api-navigation"></a>Navegação de API

As APIs de duplos Digital suportam a filtragem e de navegação em todo o gráfico geográfico utilizando os seguintes parâmetros:

- **spaceId**: A API irá filtrar os resultados pelo ID de determinado espaço. Além disso, o sinalizador booleano **useParentSpace** aplica-se para o [/espaços](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Spaces) APIs, o que indica que o ID de determinado espaço refere-se ao espaço de principal em vez do atual espaço. 

- **minLevel** e **maxLevel**: Espaços de raiz são considerados como nível 1. Espaços com o principal ao nível do espaço *n* no nível *n + 1*. Com este conjunto de valores, pode filtrar os resultados em níveis específicos. Estes são os valores, inclusivos quando definido. Dispositivos, sensores e outros objetos são considerados para ser o mesmo nível, como o seu espaço de mais próximo. Para obter todos os objetos num determinado nível, definir ambos **minLevel** e **maxLevel** para o mesmo valor.

- **minRelative** e **maxRelative**: Quando estes filtros são dado, o nível correspondente é relativo ao nível do ID de determinado espaço:
   - Nível relativo *0* é como o mesmo nível que o ID de determinado espaço.
   - Nível relativo *1* representa espaços no mesmo nível, como os filhos do ID de determinado espaço. Nível relativo *n* representa espaços menores do que o espaço especificado pelo *n* níveis.
   - Nível relativo *-1* representa espaços no mesmo nível, como o espaço de principal do espaço especificado.

- **atravessar**: Pode percorrer em ambas as direções de um ID de determinado espaço, conforme especificado pelos seguintes valores.
   - **Nenhum**: Este valor predefinido filtros para o ID de determinado espaço.
   - **Baixo**: Este procedimento filtra o ID de determinado espaço e respetivos descendentes. 
   - **Cópia de segurança**: Este procedimento filtra o ID de determinado espaço e respetivos predecessores. 
   - **Marca span**: Este procedimento filtra uma parte horizontal do gráfico espacial, o mesmo nível, como o ID de determinado espaço. Isto tem um a **minRelative** ou o **maxRelative** para ser como true. 


### <a name="examples"></a>Exemplos

A lista seguinte mostra alguns exemplos de navegação por meio da [/devices](https://docs.westcentralus.azuresmartspaces.net/management/swagger/ui/index#!/Devices) APIs. Tenha em atenção que o marcador de posição `YOUR_MANAGEMENT_API_URL` refere-se para o URI das APIs de duplos Digital no formato `https://YOUR_INSTANCE_NAME.YOUR_LOCATION.azuresmartspaces.net/management/api/v1.0/`, onde `YOUR_INSTANCE_NAME` é o nome da sua instância de duplos Digital do Azure, e `YOUR_LOCATION` é a região onde está alojada a sua instância.

- `YOUR_MANAGEMENT_API_URL/devices?maxLevel=1` Devolve todos os dispositivos ligados aos espaços de raiz.
- `YOUR_MANAGEMENT_API_URL/devices?minLevel=2&maxLevel=4` Devolve todos os dispositivos ligados a espaços de níveis 2, 3 ou 4.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId` Devolve todos os dispositivos ligados diretamente a mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down` Devolve todos os dispositivos ligados a mySpaceId ou um dos respetivos descendentes.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true` Devolve todos os dispositivos ligados a descendentes de mySpaceId, excluindo mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&minLevel=1&minRelative=true&maxLevel=1&maxRelative=true` Devolve todos os dispositivos ligados a subordinados imediatos mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Up&maxLevel=-1&maxRelative=true` Devolve todos os dispositivos ligados a um dos predecessores de mySpaceId.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Down&maxLevel=5` Devolve todos os dispositivos ligados a descendentes de mySpaceId que estão no nível inferior ou igual a 5.
- `YOUR_MANAGEMENT_API_URL/devices?spaceId=mySpaceId&traverse=Span&minLevel=0&minRelative=true&maxLevel=0&maxRelative=true` Devolve todos os dispositivos ligados a espaços que estejam no mesmo nível como mySpaceId.


## <a name="odata-support"></a>Suporte de OData
A maioria das APIs que retornam coleções, por exemplo, uma chamada para GET /spaces, suporta o seguinte subconjunto de genérica [OData](https://www.odata.org/getting-started/basic-tutorial/#queryData) opções de consulta de sistema:  

* **$filter**
* **$orderby** 
* **$top**
* **$skip** -se de que pretende apresentar toda a coleção, deverá solicitá-la como um conjunto numa única chamada e, em seguida, realizar a paginação em seu aplicativo. 

Tenha em atenção que as outras opções de consulta, como $count, $expandir, $search, não são suportadas.

### <a name="examples"></a>Exemplos

A lista seguinte mostra alguns exemplos de consultas com opções de consulta de sistema do OData:

- `YOUR_MANAGEMENT_API_URL/devices?$top=3&$orderby=Name desc`
- `YOUR_MANAGEMENT_API_URL/keystores?$filter=endswith(Description,’space’)`
- `YOUR_MANAGEMENT_API_URL/propertykeys?$filter=Scope ne ‘Spaces’`
- `YOUR_MANAGEMENT_API_URL/resources?$filter=Size gt ‘M’`
- `YOUR_MANAGEMENT_API_URL/users?$top=4&$filter=endswith(LastName,’k’)&$orderby=LastName`
- `YOUR_MANAGEMENT_API_URL/spaces?$orderby=Name desc&$top=3&$filter=substringof('Floor’,Name)`
 

## <a name="next-steps"></a>Passos Seguintes

Para obter alguns padrões comuns de consulta de API, leia [como consultar APIs de duplos Digital do Azure para tarefas comuns](how-to-query-common-apis.md).


