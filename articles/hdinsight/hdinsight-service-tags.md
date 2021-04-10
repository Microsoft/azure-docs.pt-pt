---
title: Etiquetas de serviço do grupo de segurança de rede (NSG) para Azure HDInsight
description: Utilize tags de serviço HDInsight para permitir o tráfego de entrada para o seu cluster a partir de nós de serviços de saúde e gestão, sem adicionar endereços IP aos seus NSGs.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 08/11/2020
ms.openlocfilehash: 99a61d3e445bf6887db0c97e365e6e4489eb79e1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "104872018"
---
# <a name="nsg-service-tags-for-azure-hdinsight"></a>Etiquetas de serviço NSG para Azure HDInsight

As etiquetas de serviço Azure HDInsight para grupos de segurança de rede (NSGs) são grupos de endereços IP para serviços de saúde e gestão. Estes grupos ajudam a minimizar a complexidade da criação de regras de segurança. [As tags](../virtual-network/network-security-groups-overview.md#service-tags) de serviço permitem o tráfego de entrada a partir de IPs específicos sem introduzir cada um dos [endereços IP](hdinsight-management-ip-addresses.md) de gestão nos seus NSGs.

O serviço HDInsight gere estas tags de serviço. Não é possível criar a sua própria etiqueta de serviço ou modificar uma etiqueta existente. A Microsoft gere os prefixos de endereço que correspondem à etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

Se você gostaria de usar uma determinada região e a etiqueta de serviço ainda não está documentada nesta página, você pode usar a [API de Verificação de Marca de Serviço](../virtual-network/service-tags-overview.md#use-the-service-tag-discovery-api-public-preview) para encontrar a sua etiqueta de serviço. Também pode descarregar o [ficheiro JSON](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) da marca de serviço e procurar a região desejada.

## <a name="get-started-with-service-tags"></a>Começar com etiquetas de serviço

Tem duas opções para utilizar tags de serviço nos seus grupos de segurança de rede:

- **Utilize uma única etiqueta global de serviço HDInsight**: Esta opção abre a sua rede virtual a todos os endereços IP que o serviço HDInsight utiliza para monitorizar clusters em todas as regiões. Esta opção é o método mais simples, mas pode não ser apropriado se tiver requisitos de segurança restritivos.

- **Utilize várias tags de serviço regionais**: Esta opção abre a sua rede virtual apenas para os endereços IP que o HDInsight utiliza nessa região específica. No entanto, se estiver a utilizar várias regiões, terá de adicionar várias tags de serviço à sua rede virtual.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Use uma única etiqueta global de serviço HDInsight

A maneira mais fácil de começar a usar tags de serviço com o seu cluster HDInsight é adicionar a etiqueta global `HDInsight` a uma regra NSG.

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu grupo de segurança de rede.

1. Em **Definições**, selecione **as regras de segurança de entrada** e, em seguida, selecione + **Adicionar**.

1. A partir **da** lista de drop-down Source, selecione Tag de **Serviço**.

1. A partir da lista de drop-down da etiqueta de **serviço Source,** selecione **HDInsight**.

    :::image type="content" source="./media/hdinsight-service-tags/azure-portal-add-service-tag.png" alt-text="Adicione uma etiqueta de serviço do portal Azure":::

Esta etiqueta contém os endereços IP dos serviços de saúde e gestão para todas as regiões onde o HDInsight está disponível. A etiqueta garantirá que o seu cluster possa comunicar com os serviços de saúde e gestão necessários, independentemente do local onde for criado.

## <a name="use-regional-hdinsight-service-tags"></a>Use tags de serviço HDInsight regionais

Se a opção de marcação global não funcionar porque precisa de permissões mais restritivas, só pode permitir as etiquetas de serviço aplicáveis à sua região. Pode haver várias tags de serviço, dependendo da região onde o seu cluster é criado.

Para saber quais as etiquetas de serviço a adicionar para a sua região, leia as seguintes secções do artigo.

### <a name="use-a-single-regional-service-tag"></a>Use uma única etiqueta de serviço regional

Se o seu cluster estiver localizado numa região listada nesta tabela, só precisa de adicionar uma única etiqueta de serviço regional ao seu NSG.

| Country | Region | Etiqueta de serviço |
| ---- | ---- | ---- |
| Austrália | Leste da Austrália | HDInsight.AustraliaEast |
| &nbsp; | Austrália Sudeste | HDInsight.AustraliaSoutheast |
| &nbsp; | Austrália Central | HDInsight.AustraliaCentral |
| China | China Leste 2 | HDInsight.ChinaEast2 |
| &nbsp; | China Norte 2 | HDInsight.ChinaNorth2 |
| Estados Unidos da América | E.U.A. Centro-Norte | HDInsight.NorthCentralus |
| &nbsp; | E.U.A. Oeste 2 | HDInsight.WestUS2 |
| &nbsp; | E.U.A. Centro-Oeste | HDInsight.WestCentralus |
| Canadá | Leste do Canadá | HDInsight.CanadaEast |
| Brasil | Sul do Brasil | HDInsight.BrazilSouth |
| Coreia | Coreia do Sul Central | HDInsight.KoreaCentral |
| &nbsp; | Sul da Coreia do Sul | HDInsight.KoreaSouth |
| Índia | Índia Central | HDInsight.CentralIndia |
| &nbsp; | Sul da Índia | HDInsight.SouthIndia |
| Japão | Oeste do Japão | HDInsight.JapanWest |
| França | França Central| HDInsight.FranceCentral |
| REINO UNIDO | Sul do Reino Unido | HDInsight.UKSouth |
| Azure Government | USDoD Central | HDInsight.USDodCentral |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDod Leste | HDInsight.USDodEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Use várias etiquetas de serviço regionais

Se a região onde o seu cluster foi criado não está listada na tabela anterior, você precisa permitir várias etiquetas de serviço regional. A necessidade de utilizar mais do que uma deve-se a diferenças no regime dos prestadores de recursos para as diferentes regiões.

As restantes regiões dividem-se em grupos baseados nas etiquetas de serviço regionais que utilizam.

#### <a name="group-1"></a>Grupo 1

Se o seu cluster for criado numa das regiões da tabela seguinte, permita as etiquetas de serviço `HDInsight.WestUS` e `HDInsight.EastUS` . Além disso, a etiqueta de serviço regional listada. As regiões desta secção requerem três etiquetas de serviço.

Por exemplo, se o seu cluster for criado na `East US 2` região, terá de adicionar as seguintes etiquetas de serviço ao seu grupo de segurança de rede:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| Country | Region | Etiqueta de serviço |
| ---- | ---- | ---- |
| Estados Unidos da América | E.U.A. Leste 2 | HDInsight.EastUS2 |
| &nbsp; | E.U.A. Central | HDInsight.centralus |
| &nbsp; | NorteCentral EUA | HDInsight. NorthCentralus |
| &nbsp; | E.U.A. Centro-Sul | HDInsight.SouthCentralus |
| &nbsp; | E.U.A. Leste | HDInsight.EastUS |
| &nbsp; | E.U.A. Oeste | HDInsight.Westus |
| Japão | Leste do Japão | HDInsight.JapanEast |
| Europa | Europa do Norte | HDInsight.NorthEurope |
| &nbsp; | Europa Ocidental| HDInsight.WestEurope |
| Ásia | Ásia Leste | HDInsight.EastAsia |
| &nbsp; | Sudeste Asiático | HDInsight.SoutheastAsia |
| Austrália | Leste da Austrália | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Grupo 2

Os aglomerados nas regiões da *China Norte* e *China Oriental* precisam de permitir duas etiquetas de serviço: `HDInsight.ChinaNorth` e `HDInsight.ChinaEast` .

#### <a name="group-3"></a>Grupo 3

Os agrupamentos nas regiões do *Gov Iowa dos EUA* e do Governo dos *EUA, a Virgínia,* precisam de permitir duas etiquetas de serviço: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia` .

#### <a name="group-4"></a>Grupo 4

Os aglomerados nas regiões da *Alemanha Central* e Alemanha *O Nordeste* tem de permitir duas etiquetas de serviço: `HDInsight.GermanyCentral` e `HDInsight.GermanyNortheast` .

## <a name="next-steps"></a>Passos seguintes

- [Grupos de segurança da rede: tags de serviço](../virtual-network/network-security-groups-overview.md#security-rules)
- [Criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md)