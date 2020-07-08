---
title: Configurar aparelho virtual de rede em Azure HDInsight
description: Saiba como configurar uma série de funcionalidades adicionais para o seu aparelho virtual de rede em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 805be8d5c9ab4f6316251adbb9bce3e99f4fa01d
ms.sourcegitcommit: 124f7f699b6a43314e63af0101cd788db995d1cb
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/08/2020
ms.locfileid: "86086675"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurar aparelho virtual de rede em Azure HDInsight

> [!Important]
> As seguintes informações **só** são necessárias se desejar configurar um aparelho virtual de rede (NVA) que não seja o Azure Firewall.

O Azure Firewall está automaticamente configurado para permitir o tráfego de muitos dos cenários importantes comuns. A utilização de outro aparelho virtual de rede exigirá que configurá uma série de funcionalidades adicionais. Tenha em mente os seguintes fatores ao configurar o seu aparelho virtual de rede:

* Os serviços de endpoint de serviço podem ser configurados com pontos finais de serviço que resultam em contornar o NVA, geralmente para considerações de custos ou desempenho.
* As dependências do endereço IP destinam-se ao tráfego não-HTTP/S (tráfego TCP e UDP).
* Os pontos finais FQDN HTTP/HTTPS podem ser whitelisted no seu dispositivo NVA.
* Atribua a tabela de rotas que cria para a sua sub-rede HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dependências capazes de endpoint de serviço

Pode opcionalmente ativar um ou mais dos seguintes pontos finais de serviço que resultarão em contornar o NVA. Esta opção pode ser útil para grandes quantidades de transferências de dados para economizar no custo e também para otimizações de desempenho. 

| **Ponto Final** |
|---|
| SQL do Azure |
| Storage do Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dependências de endereços IP

| **Ponto Final** | **Detalhes** |
|---|---|
| IPs publicado [aqui](hdinsight-management-ip-addresses.md) | Estes IPs são para o local de controlo HDInsight e devem ser incluídos na UDR para evitar o encaminhamento assimétrico |
| IPs privados AAD-DS | Apenas necessário para clusters ESP|


### <a name="fqdn-httphttps-dependencies"></a>DEPENDÊNCIAS FQDN HTTP/HTTPS

> [!Important]
> A lista abaixo dá apenas alguns dos FQDNs mais importantes. Você pode obter uma lista completa de FQDNs (principalmente Azure Storage e Azure Service Bus) para configurar o seu NVA [neste ficheiro](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json). Estas dependências são usadas pelas operações de plano de controlo HDInsight para criar um cluster com sucesso.

| **Ponto Final**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Próximos passos

* [Utilizar a firewall para restringir o tráfego de saída](./hdinsight-restrict-outbound-traffic.md)
* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
