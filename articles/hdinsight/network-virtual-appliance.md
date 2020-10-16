---
title: Configurar aparelho virtual de rede em Azure HDInsight
description: Saiba como configurar uma série de funcionalidades adicionais para o seu aparelho virtual de rede em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: 407160a5c315844003db4c5e371a03e6e25d2694
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630938"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurar aparelho virtual de rede em Azure HDInsight

> [!Important]
> As seguintes informações **só** são necessárias se desejar configurar um aparelho virtual de rede (NVA) que não seja o Azure Firewall.

O Azure Firewall está automaticamente configurado para permitir o tráfego de muitos dos cenários importantes comuns. A utilização de outro aparelho virtual de rede exigirá que configurá uma série de funcionalidades adicionais. Tenha em mente os seguintes fatores ao configurar o seu aparelho virtual de rede:

* Os serviços de endpoint de serviço podem ser configurados com pontos finais de serviço que resultam em contornar o NVA, geralmente para considerações de custos ou desempenho.
* As dependências do endereço IP destinam-se ao tráfego não-HTTP/S (tráfego TCP e UDP).
* Os pontos finais FQDN HTTP/HTTPS podem ser aprovados no seu dispositivo NVA.
* Atribua a tabela de rotas que cria para a sua sub-rede HDInsight.

## <a name="service-endpoint-capable-dependencies"></a>Dependências capazes de endpoint de serviço

Pode opcionalmente ativar um ou mais dos seguintes pontos finais de serviço que resultarão em contornar o NVA. Esta opção pode ser útil para grandes quantidades de transferências de dados para economizar no custo e também para otimizações de desempenho. 

| **Ponto final** |
|---|
| SQL do Azure |
| Storage do Azure |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Dependências de endereços IP

| **Ponto final** | **Detalhes** |
|---|---|
| IPs publicado [aqui](hdinsight-management-ip-addresses.md) | Estes IPs são para o local de controlo HDInsight e devem ser incluídos na UDR para evitar o encaminhamento assimétrico |
| IPs privados AAD-DS | Apenas necessário para clusters ESP|


### <a name="fqdn-httphttps-dependencies"></a>DEPENDÊNCIAS FQDN HTTP/HTTPS

> [!Important]
> A lista abaixo dá apenas alguns FQDNs que podem ser necessários para a correção de so e segurança ou validações de certificados após a criação do cluster e durante o período de vida das operações de cluster. Pode obter a lista de dependências de FQDNs (principalmente Azure Storage e Azure Service Bus) para configurar o seu NVA [neste ficheiro.](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json) Estas dependências são utilizadas pelo fornecedor de recursos HDInsight (RP) para criar e monitorizar/gerir os clusters com sucesso. Estes incluem registos de telemetria/diagnóstico, fornecimento de metadados, configurações relacionadas com o cluster, scripts, modelos ARM, etc. A lista de dependência da FQDN pode mudar com a divulgação de futuras atualizações hdingisht.

| **Ponto final**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |

## <a name="next-steps"></a>Passos seguintes

* [Utilizar a firewall para restringir o tráfego de saída](./hdinsight-restrict-outbound-traffic.md)
* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
