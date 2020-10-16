---
title: Configurar aparelho virtual de rede em Azure HDInsight
description: Saiba como configurar uma série de funcionalidades adicionais para o seu aparelho virtual de rede em Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: c0f5d8cdc7dda72f21fc1cf372e3796b26a3054a
ms.sourcegitcommit: 7dacbf3b9ae0652931762bd5c8192a1a3989e701
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92127425"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Configurar aparelho virtual de rede em Azure HDInsight

> [!Important]
> As seguintes informações **só** são necessárias se desejar configurar um aparelho virtual de rede (NVA) que não [seja o Azure Firewall](https://docs.microsoft.com/azure/hdinsight/hdinsight-restrict-outbound-traffic).

A marca FQDN de Firewall Azure Firewall é configurada automaticamente para permitir o tráfego de muitas das FQDNs importantes comuns. A utilização de outro aparelho virtual de rede exigirá que configurá uma série de funcionalidades adicionais. Tenha em mente os seguintes fatores ao configurar o seu aparelho virtual de rede:

* Os serviços de endpoint de serviço podem ser configurados com pontos finais de serviço que resultam em contornar o NVA, geralmente para considerações de custos ou desempenho.
* Se o ResourceProviderConnection estiver definido para *saída,* pode utilizar pontos finais privados para o armazenamento e servidores SQL para metastões e não há necessidade de adicioná-los ao NVA.
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
| IPs publicado [aqui](hdinsight-management-ip-addresses.md) | Estes IPs são para o fornecedor de recursos HDInsight e devem ser incluídos na UDR para evitar o encaminhamento assimétrico. Esta regra só é necessária se o ResourceProviderConnection estiver definido para *Entrada*. Se o ResourceProviderConnection estiver definido para *Outbound,* estes IPs não são necessários na UDR.  |
| IPs privados AAD-DS | Só é necessário para os agrupamentos de ESP, se os VNETs não forem espezinhados.|


### <a name="fqdn-httphttps-dependencies"></a>DEPENDÊNCIAS FQDN HTTP/HTTPS

Você pode obter a lista de dependências de FQDNs (principalmente Azure Storage e Azure Service Bus) para configurar o seu NVA [neste repo.](https://github.com/Azure-Samples/hdinsight-fqdn-lists/) Para a lista regional consulte [aqui.](https://github.com/Azure-Samples/hdinsight-fqdn-lists/tree/master/Regional) Estas dependências são utilizadas pelo fornecedor de recursos HDInsight (RP) para criar e monitorizar/gerir os clusters com sucesso. Estes incluem registos de telemetria/diagnóstico, fornecimento de metadados, configurações relacionadas com o cluster, scripts, etc. Esta lista de dependência de FQDN pode mudar com a divulgação de futuras atualizações HDInsight.

A lista abaixo apenas fornece alguns FQDNs que podem ser necessários para a correção de sistemas de segurança e validações de certificados *após* a criação do cluster e durante o período de vida das operações de cluster:

| **Dependências de tempo de execução FQDNs**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| microsoft.com:80                                                      |
|login.windows.net:443                                                  |
|login.microsoftonline.com:443                                          |

## <a name="next-steps"></a>Passos seguintes

* [Utilizar a firewall para restringir o tráfego de saída](./hdinsight-restrict-outbound-traffic.md)
* [Arquitetura de rede virtual Azure HDInsight](hdinsight-virtual-network-architecture.md)
