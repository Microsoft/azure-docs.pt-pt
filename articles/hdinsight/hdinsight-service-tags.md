---
title: Marcas de serviço do NSG (grupo de segurança de rede) para o Azure HDInsight
description: Use as marcas de serviço do HDInsight para permitir o tráfego de entrada para o cluster dos nós de serviços de gerenciamento e integridade do HDInsight, sem adicionar explicitamente os endereços IP aos grupos de segurança de rede.
author: hrasheed-msft
ms.author: hrasheed
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/05/2019
ms.openlocfilehash: 24ecf90c2ffc88415afbf84f54af3efa7d5f4a39
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75435448"
---
# <a name="network-security-group-nsg-service-tags-for-azure-hdinsight"></a>Marcas de serviço do NSG (grupo de segurança de rede) para o Azure HDInsight

As marcas de serviço do HDInsight para NSGs (grupos de segurança de rede) são grupos de endereços IP para serviços de integridade e gerenciamento. Esses grupos ajudam a minimizar a complexidade para a criação de regras de segurança. As [marcas de serviço](../virtual-network/security-overview.md#service-tags) fornecem um método alternativo para permitir o tráfego de entrada de endereços IP específicos sem inserir cada um dos [endereços IP de gerenciamento](hdinsight-management-ip-addresses.md) em seus grupos de segurança de rede.

Essas marcas de serviço são criadas e gerenciadas pelo serviço HDInsight. Você não pode criar sua própria marca de serviço ou modificar uma marca existente. A Microsoft gerencia os prefixos de endereço que correspondem à marca de serviço e atualiza automaticamente a marca de serviço à medida que os endereços são alterados.

## <a name="getting-started-with-service-tags"></a>Introdução às marcas de serviço

Você tem duas opções para usar marcas de serviço em seus grupos de segurança de rede:

1. Usar uma única marca de serviço do HDInsight-essa opção abrirá sua rede virtual para todos os endereços IP que o serviço HDInsight está usando para monitorar clusters em todas as regiões. Essa opção é o método mais simples, mas pode não ser apropriada se você tiver requisitos de segurança restritivos.

1. Usar várias marcas de serviço regional – essa opção abrirá sua rede virtual somente para os endereços IP que o HDInsight estiver usando nessa região específica. No entanto, se você estiver usando várias regiões, precisará adicionar várias marcas de serviço à sua rede virtual.

## <a name="use-a-single-global-hdinsight-service-tag"></a>Usar uma única marca de serviço do HDInsight global

A maneira mais fácil de começar a usar marcas de serviço com o cluster HDInsight é adicionar a marca global `HDInsight` a uma regra de grupo de segurança de rede.

1. No [portal do Azure](https://portal.azure.com/), selecione o grupo de segurança de rede.

1. Em **configurações**, selecione **regras de segurança de entrada**e, em seguida, selecione **+ Adicionar**.

1. Na lista suspensa **origem** , selecione **marca de serviço**.

1. Na lista suspensa **marca de serviço de origem** , selecione **HDInsight**.

    ![portal do Azure adicionar marca de serviço](./media/hdinisght-service-tags/azure-portal-add-service-tag.png)

Essa marca contém os endereços IP dos serviços de integridade e gerenciamento para todas as regiões em que o HDInsight está disponível e garantirá que o cluster possa se comunicar com os serviços de integridade e gerenciamento necessários, independentemente de onde ele foi criado.

## <a name="use-regional-hdinsight-service-tags"></a>Usar marcas regionais de serviço do HDInsight

Se a opção um não funcionar porque você precisa de permissões mais restritivas, poderá permitir apenas as marcas de serviço aplicáveis à sua região. As marcas de serviço aplicáveis podem ser uma, duas ou três marcas de serviço, dependendo da região em que o cluster é criado.

Para descobrir quais marcas de serviço adicionar para sua região, leia as seções a seguir do documento.

### <a name="use-a-single-regional-service-tag"></a>Usar uma única marca de serviço regional

Se você preferir a opção de marca de serviço dois, e o cluster estiver localizado em uma das regiões listadas nesta tabela, você só precisará adicionar uma única marca de serviço regional ao seu grupo de segurança de rede.

| País | Região | Marca de serviço |
| ---- | ---- | ---- |
| Austrália | Leste da Austrália | HDInsight. AustraliaEast |
| &nbsp; | Sudeste da Austrália | HDInsight. AustraliaSoutheast |
| &nbsp; | Austrália Central | HDInsight. AustraliaCentral |
| China | Leste da China 2 | HDInsight. ChinaEast2 |
| &nbsp; | Norte da China 2 | HDInsight. ChinaNorth2 |
| Estados Unidos | E.U.A. Centro-Norte | HDInsight. NorthCentralUS |
| &nbsp; | E.U.A. Oeste 2 | HDInsight. WestUS2 |
| &nbsp; | E.U.A. Centro-Oeste | HDInsight. WestCentralUS |
| Canadá | Leste do Canadá | HDInsight. CanadaEast |
| Brasil | Sul do Brasil | HDInsight. BrazilSouth |
| Coreia | Coreia do Sul Central | HDInsight. KoreaCentral |
| &nbsp; | Sul da Coreia do Sul | HDInsight. KoreaSouth |
| Índia | Índia Central | HDInsight. CentralIndia |
| &nbsp; | Sul da Índia | HDInsight. SouthIndia |
| Japão | Oeste do Japão | HDInsight. JapanWest |
| França | França Central| HDInsight. FranceCentral |
| RU | Sul do Reino Unido | HDInsight. UKSouth |
| Azure Government | USDoD central   | HDInsight. USDoDCentral |
| &nbsp; | USGov Texas | HDInsight. USGovTexas |
| &nbsp; | Leste UsDoD | HDInsight. USDoDEast |

### <a name="use-multiple-regional-service-tags"></a>Usar várias marcas de serviço regional

Se você preferir a opção de marca de serviço dois, e a região em que o cluster é criado não estava listada acima, você precisará permitir várias marcas de serviço regional. A necessidade de usar mais de um é devido às diferenças na organização dos provedores de recursos para as várias regiões.

As regiões restantes são divididas em grupos com base nas marcas de serviço regional que usam.

#### <a name="group-1"></a>Grupo 1

Se o cluster for criado em uma das regiões na tabela abaixo, permita que as marcas de serviço `HDInsight.WestUS` e `HDInsight.EastUS` além da marca de serviço regional listada. As regiões nesta seção exigem três marcas de serviço.

Por exemplo, se o cluster for criado na região de `East US 2`, você precisará adicionar as seguintes marcas de serviço ao seu grupo de segurança de rede:

- `HDInsight.EastUS2`
- `HDInsight.WestUS`
- `HDInsight.EastUS`

| País | Região | Marca de serviço |
| ---- | ---- | ---- |
| Estados Unidos | E.U.A. Leste 2 | HDInsight. EastUS2 |
| &nbsp; | E.U.A. Central | HDInsight. Centralus |
| &nbsp; | NorthCentral nós | HDInsight. NorthCentralUS |
| &nbsp; | E.U.A. Centro-Sul | HDInsight. SouthCentralUS |
| &nbsp; | E.U.A. Leste | HDInsight. Eastus |
| &nbsp; | E.U.A. Oeste | HDInsight. Westus |
| Japão | Leste do Japão | HDInsight. JapanEast |
| Europa | Europa do Norte | HDInsight. NorthEurope |
| &nbsp; | Europa Ocidental| HDInsight. WestEurope |
| Ásia | Ásia Oriental | HDInsight. EastAsia |
| &nbsp; | Sudeste Asiático | HDInsight. SoutheastAsia |
| Austrália | Leste da Austrália | HDInsight. AustraliaEast |

#### <a name="group-2"></a>Grupo 2

Os clusters nas regiões de **norte da China** e **leste da China**precisam permitir duas marcas de serviço: `HDInsight.ChinaNorth` e `HDInsight.ChinaEast`.

#### <a name="group-3"></a>Grupo 3

Os clusters nas regiões de **US gov Iowa** e **US gov-Virgínia**precisam permitir duas marcas de serviço: `HDInsight.USGovIowa` e `HDInsight.USGovVirginia`.

#### <a name="group-4"></a>Grupo 4

Os clusters nas regiões da **Alemanha central** e da **Alemanha nordeste**precisam permitir duas marcas de serviço: `HDInsight.GermanyCentral` e `HDInsight.GermanyNorthEast`.

## <a name="next-steps"></a>Passos seguintes

- [Grupos de segurança de rede – marcas de serviço](../virtual-network/security-overview.md#security-rules)
- [Criar redes virtuais para clusters do Azure HDInsight](hdinsight-create-virtual-network.md)
