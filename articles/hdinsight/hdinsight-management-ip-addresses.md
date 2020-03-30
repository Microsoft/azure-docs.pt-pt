---
title: Endereços IP de gestão Azure HDInsight
description: Saiba quais os endereços IP a partir de onde deve permitir o tráfego de entrada, de forma a configurar adequadamente os grupos de segurança da rede e as rotas definidas pelo utilizador para a rede virtual com o Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 03/03/2020
ms.openlocfilehash: 5e8f39b58f258742108fe323d9395efd87bc288f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78271825"
---
# <a name="hdinsight-management-ip-addresses"></a>Endereços IP de gestão HDInsight

> [!Important]
> Na maioria dos casos, pode agora utilizar a funcionalidade de etiqueta de [serviço](hdinsight-service-tags.md) para grupos de segurança de rede, em vez de adicionar manualmente endereços IP. Novas regiões só serão adicionadas para etiquetas de serviço e os endereços IP estáticos acabarão por ser depreciados.

Se utilizar grupos de segurança de rede (NSGs) ou rotas definidas pelo utilizador (UDRs) para controlar o tráfego de entrada no seu cluster HDInsight, deve certificar-se de que o seu cluster pode comunicar com serviços críticos de saúde e gestão azure.  Alguns dos endereços IP destes serviços são específicos da região, e alguns deles aplicam-se a todas as regiões do Azure. Poderá também ter de permitir o tráfego do serviço DNS do Azure se não utilizar um DNS personalizado.

As seguintes secções discutem os endereços IP específicos que devem ser permitidos.

## <a name="azure-dns-service"></a>Serviço Azure DNS

Se estiver a utilizar o serviço DNS fornecido pelo Azure, deixe acesso a partir de __168.63.129.16__ na porta 53. Para mais informações, consulte a resolução de [nomes para VMs e documento de exemplos](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md) de papel. Se estiver a usar DNS personalizados, ignore este passo.

## <a name="health-and-management-services-all-regions"></a>Serviços de saúde e gestão: Todas as regiões

Permitir o tráfego a partir dos seguintes endereços IP para os serviços de saúde e gestão Azure HDInsight, que se aplicam a todas as regiões do Azure:

| Endereço IP de origem | Destino  | Direção |
| ---- | ----- | ----- |
| 168.61.49.99 | \*:443 | Entrada |
| 23.99.5.239 | \*:443 | Entrada |
| 168.61.48.131 | \*:443 | Entrada |
| 138.91.141.162 | \*:443 | Entrada |

## <a name="health-and-management-services-specific-regions"></a>Serviços de saúde e gestão: Regiões específicas

Permitir o tráfego a partir dos endereços IP listados para os serviços de saúde e gestão Azure HDInsight na região específica de Azure onde os seus recursos estão localizados:

> [!IMPORTANT]  
> Se a região azure que está a utilizar não estiver listada, utilize a função de etiqueta de [serviço](hdinsight-service-tags.md) para grupos de segurança de rede.

| País | Região | Endereços IP de origem permitida | Destino Permitido | Direção |
| ---- | ---- | ---- | ---- | ----- |
| Ásia | Ásia Leste | 23.102.235.122</br>52.175.38.134 | \*:443 | Entrada |
| &nbsp; | Ásia Sudeste | 13.76.245.160</br>13.76.136.249 | \*:443 | Entrada |
| Austrália | Leste da Austrália | 104.210.84.115</br>13.75.152.195 | \*:443 | Entrada |
| &nbsp; | Austrália Sudeste | 13.77.2.56</br>13.77.2.94 | \*:443 | Entrada |
| Brasil | Sul do Brasil | 191.235.84.104</br>191.235.87.113 | \*:443 | Entrada |
| Canadá | Leste do Canadá | 52.229.127.96</br>52.229.123.172 | \*:443 | Entrada |
| &nbsp; | Canadá Central | 52.228.37.66</br>52.228.45.222 |\*: 443 | Entrada |
| China | Norte da China | 42.159.96.170</br>139.217.2.219</br></br>42.159.198.178</br>42.159.234.157 | \*:443 | Entrada |
| &nbsp; | Leste da China | 42.159.198.178</br>42.159.234.157</br></br>42.159.96.170</br>139.217.2.219 | \*:443 | Entrada |
| &nbsp; | China Norte 2 | 40.73.37.141</br>40.73.38.172 | \*:443 | Entrada |
| &nbsp; | China Leste 2 | 139.217.227.106</br>139.217.228.187 | \*:443 | Entrada |
| Europa | Europa do Norte | 52.164.210.96</br>13.74.153.132 | \*:443 | Entrada |
| &nbsp; | Europa ocidental| 52.166.243.90</br>52.174.36.244 | \*:443 | Entrada |
| França | França Central| 20.188.39.64</br>40.89.157.135 | \*:443 | Entrada |
| Alemanha | Alemanha Central | 51.4.146.68</br>51.4.146.80 | \*:443 | Entrada |
| &nbsp; | Nordeste da Alemanha | 51.5.150.132</br>51.5.144.101 | \*:443 | Entrada |
| Índia | Índia Central | 52.172.153.209</br>52.172.152.49 | \*:443 | Entrada |
| &nbsp; | Sul da Índia | 104.211.223.67<br/>104.211.216.210 | \*:443 | Entrada |
| Japão | Leste do Japão | 13.78.125.90</br>13.78.89.60 | \*:443 | Entrada |
| &nbsp; | Oeste do Japão | 40.74.125.69</br>138.91.29.150 | \*:443 | Entrada |
| Coreia | Coreia do Sul Central | 52.231.39.142</br>52.231.36.209 | \*:443 | Entrada |
| &nbsp; | Sul da Coreia do Sul | 52.231.203.16</br>52.231.205.214 | \*:443 | Entrada
| Reino Unido | Oeste do Reino Unido | 51.141.13.110</br>51.141.7.20 | \*:443 | Entrada |
| &nbsp; | Sul do Reino Unido | 51.140.47.39</br>51.140.52.16 | \*:443 | Entrada |
| Estados Unidos | E.U.A. Central | 13.89.171.122</br>13.89.171.124 | \*:443 | Entrada |
| &nbsp; | E.U.A. Leste | 13.82.225.233</br>40.71.175.99 | \*:443 | Entrada |
| &nbsp; | E.U.A. Centro-Norte | 157.56.8.38</br>157.55.213.99 | \*:443 | Entrada |
| &nbsp; | E.U.A. Centro-Oeste | 52.161.23.15</br>52.161.10.167 | \*:443 | Entrada |
| &nbsp; | E.U.A. Oeste | 13.64.254.98</br>23.101.196.19 | \*:443 | Entrada |
| &nbsp; | E.U.A.Oeste 2 | 52.175.211.210</br>52.175.222.222 | \*:443 | Entrada |
| &nbsp; | Emirados Unidos norte | 65.52.252.96</br>65.52.252.97 | \*:443 | Entrada |

Para obter informações sobre os endereços IP a utilizar para o Governo Azure, consulte o documento [Azure Government Intelligence + Analytics.](https://docs.microsoft.com/azure/azure-government/documentation-government-services-intelligenceandanalytics)

Para mais informações, consulte a secção de tráfego da [rede Controle.](hdinsight-plan-virtual-network-deployment.md#networktraffic)

Se estiver a utilizar rotas definidas pelo utilizador (UDRs), deve especificar uma rota e permitir o tráfego de saída da rede virtual para os IPs acima com o próximo salto definido para "Internet".

## <a name="next-steps"></a>Passos seguintes

* [Criar redes virtuais para clusters Azure HDInsight](hdinsight-create-virtual-network.md)
* [Etiquetas de serviço do grupo de segurança de rede (NSG) para Azure HDInsight](hdinsight-service-tags.md)
