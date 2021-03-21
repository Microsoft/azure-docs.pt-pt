---
title: Responsabilidades do cliente executando Azure Spring Cloud em vnet
description: Este artigo descreve as responsabilidades dos clientes com o Azure Spring Cloud em vnet.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: conceptual
ms.date: 12/02/2020
ms.custom: devx-track-java, devx-track-azurecli
ms.openlocfilehash: 0c73d0394486472c2c3c92450aab6a1a0d329cf7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101698217"
---
# <a name="customer-responsibilities-for-running-azure-spring-cloud-in-vnet"></a>Responsabilidades do cliente para executar Azure Spring Cloud em VNET
Este documento inclui especificações para a utilização da Azure Spring Cloud numa rede virtual.

Quando o Azure Spring Cloud é implantado na sua rede virtual, tem dependências de saída em serviços fora da rede virtual. Para fins de gestão e operacional, a Azure Spring Cloud deve aceder a determinadas portas e nomes de domínio totalmente qualificados (FQDNs). Estes pontos finais são necessários para comunicar com o plano de gestão Azure Spring Cloud e para descarregar e instalar componentes de cluster de kubernetes e atualizações de segurança.

Por padrão, a Azure Spring Cloud tem acesso ilimitado à internet de saída (saída). Este nível de acesso à rede permite que as aplicações que você execute para aceder a recursos externos conforme necessário. Se desejar restringir o tráfego de saídas, um número limitado de portos e endereços deve estar acessível para tarefas de manutenção. A solução mais simples para proteger endereços de saída é a utilização de um dispositivo de firewall que pode controlar o tráfego de saída com base em nomes de domínio. O Azure Firewall, por exemplo, pode restringir o tráfego http e HTTPS de saída com base na FQDN do destino. Também pode configurar as suas regras de firewall e segurança preferidas para permitir as portas e endereços necessários.

## <a name="azure-spring-cloud-resource-requirements"></a>Requisitos de recursos da Cloud Azure Spring 

Segue-se uma lista de requisitos de recursos para os serviços Azure Spring Cloud. Como requisito geral, não deve modificar os grupos de recursos criados pela Azure Spring Cloud e os recursos de rede subjacentes.
- Não modifique grupos de recursos criados e propriedade da Azure Spring Cloud.
  - Por padrão, estes grupos de recursos são nomeados como ap-svc-rt_ [SERVICE-INSTANCE-NAME]_[REGIÃO]* e ap_[SERVICE-INSTANCE-NAME]_[REGION]*.
- Não modifique as sub-redes utilizadas pela Azure Spring Cloud.
- Não crie mais do que uma instância de serviço Azure Spring Cloud na mesma sub-rede.
- Ao utilizar uma firewall para controlar o tráfego, *não bloqueie* o tráfego de saída a componentes da Azure Spring Cloud que operam, mantêm e suportam a instância de serviço.

## <a name="azure-spring-cloud-network-requirements"></a>Requisitos de rede Azure Spring Cloud

  | Ponto final de destino | Porta | Utilização | Nota |
  |------|------|------|
  | *:1194 *Ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:1194 | UDP:1194 | Gestão subjacente do Cluster Kubernetes. | |
  | *:443 *Ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:443 | TCP:443 | Gestão do Serviço de Nuvem de primavera Azure. | A informação sobre a instância de serviço "requeredTraffics" poderia ser conhecida na carga útil de recursos, na secção "networkProfile". |
  | *:9000 *Ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureCloud:9000 | TCP:9000 | Gestão subjacente do Cluster Kubernetes. |
  | *:123 *Ou* ntp.ubuntu.com:123 | UDP:123 | Sincronização do tempo NTP nos nós Linux. | |
  | *.azure.io:443 Ou  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - AzureContainerRegistry:443 | TCP:443 | Registo de Contentores Azure. | Pode ser substituído ativando o ponto final do serviço *de registo de contentores Azure* [na rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.core.windows.net:443 e *.core.windows.net:445 *Ou* [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - Armazenamento:443 e Armazenamento:445 | TCP:443, TCP:445 | Armazenamento de Ficheiros do Azure | Pode ser substituído ativando o ponto final do serviço *Azure Storage* [na rede virtual](../virtual-network/virtual-network-service-endpoints-overview.md). |
  | *.servicebus.windows.net:443 Ou  [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - EventHub:443 | TCP:443 | Centro de Eventos Azure. | Pode ser substituído ativando o ponto final de serviço *do Azure Event Hubs* [na rede virtual.](../virtual-network/virtual-network-service-endpoints-overview.md) |
  

## <a name="azure-spring-cloud-fqdn-requirements--application-rules"></a>Requisitos de FQDN da nuvem de primavera de Azure

A Azure Firewall fornece uma etiqueta de domínio totalmente qualificada (FQDN) **AzureKubernetesService** para simplificar as seguintes configurações.

  | Destino FQDN | Porta | Utilização |
  |------|------|------|
  | *.azmk8s.io | HTTPS:443 | Gestão subjacente do Cluster Kubernetes. |
  | <i>mcr.microsoft.com</i> | HTTPS:443 | Registo de Contentores da Microsoft (MCR). |
  | *.cdn.mscr.io | HTTPS:443 | Armazenamento MCR apoiado pelo Azure CDN. |
  | *.data.mcr.microsoft.com | HTTPS:443 | Armazenamento MCR apoiado pelo Azure CDN. |
  | <i>management.azure.com</i> | HTTPS:443 | Gestão subjacente do Cluster Kubernetes. |
  | <i>login.microsoftonline.com</i> | HTTPS:443 | Autenticação do Diretório Ativo Azure. |
  |<i>packages.microsoft.com</i>    | HTTPS:443 | Microsoft embala repositório. |
  | <i>acs-mirror.azureedge.net</i> | HTTPS:443 | Repositório necessário para instalar binários necessários como kubenet e Azure CNI. |
  | *mscrl.microsoft.com* | HTTPS:80 | Percursos em cadeia de certificados da Microsoft. |
  | *crl.microsoft.com* | HTTPS:80 | Percursos em cadeia de certificados da Microsoft. |
  | *crl3.digicert.com* | HTTPS:80 | 3º Partido SSL Certificate Chain Paths. |

## <a name="see-also"></a>Ver também
* [Aceda à sua aplicação numa rede privada](spring-cloud-access-app-virtual-network.md)
* [Expor aplicativos usando App Gateway e Azure Firewall](spring-cloud-expose-apps-gateway-azure-firewall.md)