---
title: Configure aparelho virtual de rede em Azure HDInsight
description: Saiba como configurar uma série de funcionalidades adicionais para o seu aparelho virtual de rede no Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864711"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configure aparelho virtual de rede em Azure HDInsight

> [!Important]
> As seguintes informações **só** são necessárias se pretender configurar um aparelho virtual de rede (NVA) que não seja o Firewall Azure.

O Azure Firewall é configurado automaticamente para permitir o tráfego para muitos dos cenários importantes comuns. A utilização de outro aparelho virtual de rede exigirá que configure uma série de funcionalidades adicionais. Tenha em mente os seguintes fatores à medida que configura o seu aparelho virtual de rede:

* Os serviços de endpoint de serviço devem ser configurados com pontos finais de serviço.
* As dependências de endereçoip são para tráfego não HTTP/S (tanto o tráfego TCP como uDP).
* Os pontos finais FQDN HTTP/HTTPS podem ser colocados no seu dispositivo NVA.
* Os pontos finais wildcard HTTP/HTTPS são dependências que podem variar com base numa série de qualificações.
* Atribua a tabela de rotas que cria à sua sub-rede HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dependências de ponto final de serviço

| **Ponto Final** |
|---|
| SQL do Azure |
| Storage do Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dependências de endereços IP

| **Ponto Final** | **Detalhes** |
|---|---|
| \*:123 | Verificação do relógio NTP. O tráfego é verificado em vários pontos finais no porto 123 |
| IPs [publicados aqui](hdinsight-management-ip-addresses.md) | Estes IPs são serviço HDInsight |
| IPs privados AAD-DS para clusters ESP |
| \*:16800 para ativação do Windows KMS |
| \*12000 para Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>Dependências fQDN HTTP/HTTPS

> [!Important]
> A lista abaixo dá apenas alguns dos FQDNs mais importantes. Pode obter FQDNs adicionais (principalmente Azure Storage e Azure Service Bus) para configurar o seu NVA [neste ficheiro](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Ponto Final**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Passos seguintes

* [Utilizar a firewall para restringir o tráfego de saída](./hdinsight-restrict-outbound-traffic.md)
* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
