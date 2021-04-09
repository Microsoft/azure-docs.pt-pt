---
title: Estratégia de localização de âncora
description: Conheça as diferentes estratégias ao chamar a API de localização
author: pamistel
manager: MehranAzimi-msft
services: azure-spatial-anchors
ms.author: pamistel
ms.date: 02/11/2021
ms.topic: conceptual
ms.service: azure-spatial-anchors
ms.openlocfilehash: 13aa12be5a336363bbe3bcbf3e3fb354a8fa3074
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048481"
---
# <a name="understanding-the-anchorlocatecriteria-class"></a>Compreender a classe AnchorLocateCriteria
Neste artigo você vai aprender as diferentes opções que você pode usar ao consultar uma âncora. Vamos passar pela classe AnchorLocateCriteria, as suas opções e combinações de opções válidas.

## <a name="anchor-locate-criteria"></a>Critérios de localização de âncora
A [classe AnchorLocateCriteria](/dotnet/api/microsoft.azure.spatialanchors.anchorlocatecriteria) ajuda-o a consultar o serviço para âncoras previamente criadas. Um objeto AnchorLocateCriteria pode ser utilizado por observador a qualquer momento. Cada objeto AnchorLocateCriteria deve incluir **exatamente uma das** seguintes propriedades: [Identifiers](#identifiers), [NearAnchor](#nearanchor), ou [NearDevice](#neardevice). Propriedades adicionais como [Estratégia,](#strategy) [BypassCache](#bypasscache)e [Categões Solicitados](#requestedcategories) podem ser definidas se desejar. 

### <a name="properties"></a>Propriedades
Defina **exatamente uma das** seguintes propriedades no seu observador:
#### <a name="identifiers"></a>Identificadores
*Valor padrão: matriz de corda vazia*

Utilizando identificadores, pode definir uma lista de IDs de âncora para âncoras que gostaria de localizar. Os IDs de âncora são inicialmente devolvidos após a criação de âncora bem sucedida. Com os identificadores especificados, anchorLocateCriteria restringe o conjunto de âncoras solicitadas a âncoras com iDs de âncora correspondentes. Esta propriedade é especificada usando uma matriz de cordas. 

#### <a name="nearanchor"></a>NearAnchor
*Valor predefinido: não definido*

Utilizando nearAnchor, pode especificar que anchorLocateCriteria restringe o conjunto de âncoras solicitadas a âncoras a uma distância desejada de uma âncora escolhida. Deve fornecer esta âncora escolhida como âncora de origem. Pode também definir a distância desejada para longe da âncora de origem, e o número máximo de âncoras devolvidas, para limitar ainda mais a procura.
Esta propriedade é especificada usando um objeto NearAnchorCriteria.

#### <a name="neardevice"></a>NearDevice
*Valor predefinido: não definido*

Utilizando o NearDevice, pode especificar que o AnchorLocateCriteria restringe o conjunto de âncoras solicitadas às que estão perto da localização física do dispositivo. Quaisquer sensores ativados serão utilizados para ajudar a descobrir âncoras em torno do seu dispositivo. Para ter a melhor hipótese de encontrar âncoras, deve configurar sensorCapabilidades para dar acesso à sessão a todos os sensores apropriados. Para obter mais informações sobre a instalação e utilização desta propriedade, consulte [a Relocalização Coarse - Azure Spatial Anchors | Microsoft Docs](./coarse-reloc.md) e *Como criar e localizar âncoras utilizando uma relocalização grosseira* em [C.](../how-tos/set-up-coarse-reloc-unity.md), [Objective-C,](../how-tos/set-up-coarse-reloc-unity.md) [Swift,](../how-tos/set-up-coarse-reloc-swift.md) [Java,](../how-tos/set-up-coarse-reloc-java.md) [C++/NDK,](../how-tos/set-up-coarse-reloc-cpp-ndk.md) [C++/WinRT](../how-tos/set-up-coarse-reloc-cpp-winrt.md).
Esta propriedade é especificada usando um objeto NearDeviceCriteria.

### <a name="additional-properties"></a>Propriedades adicionais
#### <a name="bypasscache"></a>BypassCache
*Valor Predefinido: falso*

Quando uma âncora foi criada ou encontrada numa sessão, também é armazenada na cache.  Com esta propriedade definida como falsa, qualquer consulta subsequente na mesma sessão devolverá o valor em cache. Não é feito qualquer pedido ao serviço ASA.

#### <a name="requestedcategories"></a>Presas Solicitadas
*Valor Predefinido: Imóveis | Espacial*

Esta propriedade é usada para determinar que dados são devolvidos de uma consulta usando AnchorLocateCriteria. O valor predefinido devolve tanto as propriedades como os dados espaciais, o que não deve ser alterado se as propriedades e os dados espaciais forem ambos desejados. Esta propriedade pode ser especificada usando o AnchorDataCategoria enum.

Valor enum anchorDataCategoria | Dados Devolvidos
-----|------------
Nenhum | Nenhum dado é devolvido
Propriedades| As propriedades de âncora, incluindo AppProperties, são devolvidas.
Espacial| A informação espacial sobre uma âncora é devolvida.

#### <a name="strategy"></a>Estratégia
*Valor predefinido: AnyStrategy*

A estratégia define ainda como as âncoras devem ser localizadas. A propriedade Estratégia pode ser especificada usando um LocateStrategy enum.

Localizar O Valor Enum daStrategy | Description
---------------|------------
AnyStrategy | Esta estratégia permite ao sistema utilizar combinações de estratégias de Informação Visual e Relacionamento para encontrar âncoras. 
Informação Visual|Esta estratégia tenta encontrar âncoras combinando informações visuais do ambiente atual com as da pegada visual da âncora. A pegada visual de uma âncora refere-se à informação visual atualmente associada à âncora. Esta informação visual é tipicamente, mas não exclusivamente recolhida durante a criação de âncora. Atualmente, esta estratégia só é permitida em conjunto com as propriedades NearDevice ou Identifiers.
Relação|Esta estratégia tenta encontrar âncoras utilizando [âncoras existentes.](./anchor-relationships-way-finding.md#connect-anchors) Atualmente, esta estratégia só é permitida em conjunto com as propriedades NearAnchor ou Identifiers. Quando utilizado com a propriedade Identifiers, é necessário que, na mesma sessão, o utilizador tenha previamente localizado uma âncora com relações conjuntivas já estabelecidas com as âncoras cujos IDs estão especificados na matriz de Identificadores. 


### <a name="valid-combinations-of-locatestrategy-and-anchorlocatecriteria-properties"></a>Combinações válidas de propriedades LocateStrategy e AnchorLocateCriteria 

Nem todas as combinações de propriedades de Estratégia e AnchorLocateCriteria são atualmente permitidas pelo sistema. A tabela a seguir mostra as combinações permitidas:



Propriedade | AnyStrategy | Relação | Informação Visual
-------- | ------------|--------------|-------------------
Identificadores | &check;    | &check;     | &check;
NearAnchor  | &check;   (irá incumprimento da Relação) | &check;    | 
NearDevice  | &check;    |   | &check;




## <a name="next-steps"></a>Passos seguintes

Veja [como criar e localizar âncoras usando âncoras espaciais Azure](../create-locate-anchors-overview.md) para mais alguns exemplos usando a classe AnchorLocateCriteria.