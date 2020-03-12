---
title: Etiquetas de serviço do grupo de segurança de rede (NSG) para Azure HDInsight
description: Utilize etiquetas de serviço HDInsight para permitir o tráfego de entrada para o seu cluster a partir de nós de serviços de saúde e gestão HDInsight, sem adicionar explicitamente endereços IP aos seus grupos de segurança de rede.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/10/2020
ms.openlocfilehash: a72753d5553e79a8ed28c3afcc7e54af6c2d230c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/11/2020
ms.locfileid: "79117234"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Etiquetas de serviço do grupo de segurança de rede (NSG) para Azure HDInsight

As etiquetas de serviço HDInsight para grupos de segurança de rede (NSGs) são grupos de endereços IP para serviços de saúde e gestão. Estes grupos ajudam a minimizar a complexidade da criação de regras de segurança. [As etiquetas](../virtual-network/security-overview.md#service-tags) de serviço fornecem um método alternativo para permitir o tráfego de entrada a partir de endereços IP específicos sem introduzir cada um dos [endereços IP](hdinsight-management-ip-addresses.md) de gestão nos seus grupos de segurança de rede.

Estas etiquetas de serviço são criadas e geridas pelo serviço HDInsight. Não pode criar a sua própria etiqueta de serviço, ou modificar uma etiqueta existente. A Microsoft gere os prefixos de endereço que correspondem à etiqueta de serviço e atualiza automaticamente a etiqueta de serviço à medida que os endereços mudam.

## <a name="getting-started-with-service-tags"></a>Começar com etiquetas de serviço

Tem duas opções para utilizar etiquetas de serviço nos seus grupos de segurança de rede:

1. Utilize uma única etiqueta de serviço HDInsight - esta opção abrirá a sua rede virtual a todos os Endereços IP que o serviço HDInsight está a usar para monitorizar clusters em todas as regiões. Esta opção é o método mais simples, mas pode não ser apropriado se tiver requisitos de segurança restritivos.

1. Utilize várias etiquetas de serviço regionais - esta opção abrirá a sua rede virtual apenas para os Endereços IP que o HDInsight está a usar nessa região específica. No entanto, se estiver a utilizar várias regiões, terá de adicionar várias etiquetas de serviço à sua rede virtual.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Use uma única etiqueta de serviço HDInsight global

A maneira mais fácil de começar a usar etiquetas de serviço com o seu cluster HDInsight é adicionar a etiqueta global `HDInsight` a uma regra do grupo de segurança da rede.

1. A partir do [portal Azure,](https://portal.azure.com/)selecione o seu grupo de segurança de rede.

1. Em **Definições,** selecione **as regras de segurança de entrada**e, em seguida, selecione + **Adicionar**.

1. A partir da lista de drop-down **Source,** selecione **Etiqueta de Serviço**.

1. A partir da lista de drop-down da etiqueta de **serviço Fonte,** selecione **HDInsight**.

    ![Portal Azure adicionar etiqueta de serviço](./media/hdinsight-service-tags/azure-portal-add-service-tag.png)

Esta etiqueta contém os endereços IP dos serviços de saúde e gestão para todas as regiões onde o HDInsight está disponível, e garantirá que o seu cluster pode comunicar com os serviços de saúde e gestão necessários, independentemente do local onde seja criado.

## <a name="use-regional-hdinsight-service-tags"></a>Utilize etiquetas de serviço hDInsight regionais

Se a opção não funcionar porque precisa de permissões mais restritivas, então só pode permitir as etiquetas de serviço aplicáveis à sua região. As etiquetas de serviço aplicáveis podem ser uma, duas ou três etiquetas de serviço, dependendo da região onde o seu cluster é criado.

Para saber quais as etiquetas de serviço a adicionar para a sua região, leia as seguintes secções do documento.

### <a name="use-a-single-regional-service-tag"></a>Use uma única etiqueta de serviço regional

Se preferir a opção de etiqueta de serviço dois, e o seu cluster estiver localizado numa das regiões listadas nesta tabela, então apenas precisa adicionar uma única etiqueta de serviço regional ao seu grupo de segurança de rede.

| País | Região | Etiqueta de serviço |
| ---- | ---- | ---- |
| Austrália | Leste da Austrália | HDInsight.AustraliaEast |
| &nbsp; | Austrália Sudeste | HDInsight.AustraliaSoutheast |
| &nbsp; | Austrália Central | HDInsight.AustraliaCentral |
| China | China Leste 2 | HDInsight.chinaEast2 |
| &nbsp; | China Norte 2 | HDInsight.chinaNorth2 |
| Estados Unidos | E.U.A. Centro-Norte | HDInsight.NorthCentralUS |
| &nbsp; | E.U.A.Oeste 2 | HDInsight.WestUS2 |
| &nbsp; | E.U.A. Centro-Oeste | HDInsight.WestCentralUS |
| Canadá | Leste do Canadá | HDInsight.CanadaEast |
| Brasil | Sul do Brasil | HDInsight.BrasilSouth |
| Coreia | Coreia do Sul Central | HDInsight.KoreaCentral |
| &nbsp; | Sul da Coreia do Sul | HDInsight.KoreaSouth |
| Índia | Índia Central | HDInsight.CentralIndia |
| &nbsp; | Sul da Índia | HDInsight.SouthIndia |
| Japão | Oeste do Japão | HDInsight.JapanWest |
| França | França Central| HDInsight.FranceCentral |
| RU | Sul do Reino Unido | HDInsight.UKSouth |
| Azure Government | USDoD Central   | HDInsight.USDodCentral |
| &nbsp; | USGov Texas | HDInsight.USGovTexas |
| &nbsp; | UsDoD East | HDInsight.USDoDEast |
| &nbsp; | USGov Arizona | HDInsight.USGovArizona |

### <a name="use-multiple-regional-service-tags"></a>Utilize várias etiquetas de serviço regional

Se preferir a opção de etiqueta de serviço dois, e a região onde o seu cluster é criado não estava listada acima, então precisa permitir várias etiquetas de serviço regionais. A necessidade de utilizar mais do que uma deve-se às diferenças no regime dos fornecedores de recursos para as várias regiões.

As restantes regiões dividem-se em grupos com base nas etiquetas de serviço regionais que utilizam.

#### <a name="group-1"></a>Grupo 1

Se o seu cluster for criado numa das regiões da tabela abaixo, permita que as etiquetas de serviço `HDInsight.WestUS` e `HDInsight.EastUS` para além da etiqueta de serviço regional listada. As regiões desta secção requerem três etiquetas de serviço.

Por exemplo, se o seu cluster for criado na região `East US 2`, então terá de adicionar as seguintes etiquetas de serviço ao seu grupo de segurança de rede:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| País | Região | Etiqueta de serviço |
| ---- | ---- | ---- |
| Estados Unidos | E.U.A. Leste 2 | HDInsight.EastUS2 |
| &nbsp; | E.U.A. Central | HDInsight.CentralUS |
| &nbsp; | Norte-Central EUA | HDInsight. Norte-CentralUS |
| &nbsp; | E.U.A. Centro-Sul | HDInsight.SouthCentralUS |
| &nbsp; | E.U.A. Leste | HDInsight.EastUS |
| &nbsp; | E.U.A. Oeste | HDInsight.WestUS |
| Japão | Leste do Japão | HDInsight.JapanEast |
| Europa | Europa do Norte | HDInsight.NorthEurope |
| &nbsp; | Europa Ocidental| HDInsight.WestEurope |
| Ásia | Ásia Leste | HDInsight.EastAsia |
| &nbsp; | Ásia Sudeste | HDInsight.SoutheastAsia |
| Austrália | Leste da Austrália | HDInsight.AustraliaEast |

#### <a name="group-2"></a>Grupo 2

Os aglomerados nas regiões da **China Norte** e **China Leste**precisam de permitir duas etiquetas de serviço: `HDInsight.ChinaNorth` e `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Grupo 3

Os aglomerados nas regiões dos **EUA Gov Iowa** e **norte-americano Gov Virginia**precisam de permitir duas etiquetas de serviço: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Grupo 4

Os aglomerados nas regiões da **Alemanha Central** e **Alemanha Nordeste,** precisam de permitir duas etiquetas de serviço: `HDInsight.GermanyCentral` e `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Passos seguintes

- [Grupos de segurança da rede - etiquetas de serviço](../virtual-network/security-overview.md#security-rules)
- [Criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md)
