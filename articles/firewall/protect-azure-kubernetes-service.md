---
title: Utilizar o Azure Firewall para proteger as Implementações do Azure Kubernetes Service (AKS)
description: Saiba como usar o Azure Firewall para proteger as implementações do Serviço Azure Kubernetes (AKS)
author: vhorne
ms.service: firewall
services: firewall
ms.topic: how-to
ms.date: 09/03/2020
ms.author: victorh
ms.openlocfilehash: 43755b312a64c429b38a07c8c4fad8c85b08342a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89437858"
---
# <a name="use-azure-firewall-to-protect-azure-kubernetes-service-aks-deployments"></a>Utilizar o Azure Firewall para proteger as Implementações do Azure Kubernetes Service (AKS)

O Azure Kubernetes Service (AKS) oferece um cluster kubernetes gerido em Azure. Reduz a complexidade e a sobrecarga operacional da gestão de Kubernetes, descarregando grande parte dessa responsabilidade para o Azure. A AKS lida com tarefas críticas, tais como monitorização e manutenção de saúde para si e fornece um cluster de qualidade empresarial e seguro com uma governação facilitada.

A Kubernetes orquestra aglomerados de máquinas virtuais e agenda contentores para funcionar nessas máquinas virtuais com base nos seus recursos de computação disponíveis e nos requisitos de recursos de cada recipiente. Os contentores são agrupados em cápsulas, a unidade operacional básica de Kubernetes, e aquelas cápsulas escalam para o estado que você deseja.

Para fins de gestão e operacional, os nódes num cluster AKS precisam de aceder a determinadas portas e nomes de domínio totalmente qualificados (FQDNs). Estas ações podem ser para comunicar com o servidor API, ou para descarregar e, em seguida, instalar componentes de cluster de kubernetes e atualizações de segurança de nó. O Azure Firewall pode ajudá-lo a bloquear o seu ambiente e a filtrar o tráfego de saída.

Siga as diretrizes deste artigo para fornecer proteção adicional para o seu cluster Azure Kubernetes utilizando a Azure Firewall.

## <a name="prerequisites"></a>Pré-requisitos

- Um cluster Azure Kubernetes implantado com aplicação de execução.

   Para obter mais informações, consulte Tutorial: Implemente um cluster e tutorial do [Serviço Azure Kubernetes (AKS):](../aks/tutorial-kubernetes-deploy-cluster.md) [Executar aplicações no Serviço Azure Kubernetes (AKS)](../aks/tutorial-kubernetes-deploy-application.md).


## <a name="securing-aks"></a>A fixação de AKS

O Azure Firewall fornece uma Etiqueta FQDN AKS para simplificar a configuração. Utilize os seguintes passos para permitir o tráfego da plataforma AKS de saída:

- Quando utilizar o Azure Firewall para restringir o tráfego de saída e criar uma rota definida pelo utilizador (UDR) para direcionar todo o tráfego de saída, certifique-se de que cria uma regra de ADN apropriada no Firewall para permitir corretamente o tráfego de entrada. 

   A utilização do Azure Firewall com um UDR quebra a configuração de entrada devido ao encaminhamento assimétrico. O problema ocorre se a sub-rede AKS tiver uma rota padrão que vai para o endereço IP privado da firewall, mas você está usando um equilibrador de carga pública. Por exemplo, serviço de entrada ou Kubernetes do tipo *LoadBalancer*.

   Neste caso, o tráfego do balançador de carga de entrada é recebido através do seu endereço IP público, mas a trajetória de retorno passa pelo endereço IP privado da firewall. Como a firewall é imponente, deixa cair o pacote de retorno porque a firewall não está ciente de uma sessão estabelecida. Para aprender a integrar o Azure Firewall com o seu equilibrador de carga de entrada ou serviço, consulte [Integrate Azure Firewall com o Azure Standard Load Balancer](integrate-lb.md).
- Crie uma coleção de regras de aplicação e adicione uma regra para ativar a etiqueta FQDN do *AzureKubernetesService.* A gama de endereços IP de origem é a rede virtual do pool anfitrião, o protocolo é https, e o destino é AzureKubernetesService.
- São necessárias as seguintes portas de saída/regras de rede para um cluster AKS:

   - Porta TCP 443
   - TCP [*IPAddrOfYourAPIServer*]:443 é necessário se tiver uma aplicação que precisa de falar com o servidor API. Esta alteração pode ser definida após a criação do cluster.
   - Porta TCP 9000, e porta UDP 1194 para a cápsula frontal do túnel comunicar com a extremidade do túnel no servidor API.

      Para ser mais específico, consulte o .*. <location> azmk8s.io* e endereços na tabela seguinte:

   | Ponto final de destino                                                             | Protocolo | Porta    | Utilização  |
   |----------------------------------------------------------------------------------|----------|---------|------|
   | **`*:1194`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:1194`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:1194`** <br/> *Ou* <br/> **`APIServerIP:1194`** `(only known after cluster creation)`  | UDP           | 1194      | Para uma comunicação segura entre os nós e o avião de controlo. |
   | **`*:9000`** <br/> *Ou* <br/> [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureCloud.<Region>:9000`** <br/> *Ou* <br/> [CIDRs regionais](../virtual-network/service-tags-overview.md#discover-service-tags-by-using-downloadable-json-files) - **`RegionCIDRs:9000`** <br/> *Ou* <br/> **`APIServerIP:9000`** `(only known after cluster creation)`  | TCP           | 9000      | Para uma comunicação segura entre os nós e o avião de controlo. |

   - Porta UDP 123 para sincronização de tempo do Protocolo de Tempo de Rede (NTP) (nóles Linux).
   - A porta UDP 53 para DNS também é necessária se tiver cápsulas de acesso direto ao servidor API.

   Para obter mais informações, consulte [o tráfego de saídas de controlo para os nós de cluster no Serviço Azure Kubernetes (AKS)](../aks/limit-egress-traffic.md).
- Configure as etiquetas de serviço AzureMonitor e Armazenamento. O Azure Monitor recebe dados de análise de registo.

   Também pode permitir o url do seu espaço de trabalho individualmente: `<worksapceguid>.ods.opinsights.azure.com` , e `<worksapceguid>.oms.opinsights.azure.com` . Pode abordar esta questão de uma das seguintes formas:

    - Permitir o acesso https a partir da sub-rede da piscina do anfitrião para `*. ods.opinsights.azure.com` , e `*.oms. opinsights.azure.com` . Estes FQDNs wildcard permitem o acesso necessário, mas são menos restritivos.
    - Utilize a seguinte consulta de análise de registo para listar as FQDNs exatas e, em seguida, permitir-lhes explicitamente nas suas regras de aplicação de firewall:
   ```
   AzureDiagnostics 
   | where Category == "AzureFirewallApplicationRule" 
   | search "Allow" 
   | search "*. ods.opinsights.azure.com" or "*.oms. opinsights.azure.com"
   | parse msg_s with Protocol " request from " SourceIP ":" SourcePort:int " to " FQDN ":" * 
   | project TimeGenerated,Protocol,FQDN 
   ```


## <a name="next-steps"></a>Passos seguintes

- Saiba mais sobre o Serviço Azure Kubernetes, consulte [os conceitos centrais da Kubernetes para o Serviço Azure Kubernetes (AKS)](../aks/concepts-clusters-workloads.md).
