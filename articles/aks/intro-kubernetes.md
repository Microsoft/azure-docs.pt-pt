---
title: Introdução ao Azure Kubernetes Service
description: Conheça as funcionalidades e os benefícios do Azure Kubernetes Service para implementar e gerir aplicações baseadas em contentores no Azure.
services: container-service
ms.topic: overview
ms.date: 02/09/2021
ms.custom: mvc
ms.openlocfilehash: 1505366d9a91eac596b21804f93abb8245a84605
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100590013"
---
# <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

O Azure Kubernetes Service (AKS) simplifica a implementação de um cluster de Kubernetes gerido em Azure, descarregando grande parte da complexidade e sobrecarga operacional para Azure. Como um serviço kubernetes hospedado, a Azure lida com tarefas críticas para si, como monitorização de saúde e manutenção.  

Uma vez que os mestres Kubernetes são geridos pelo Azure, só consegues e manténs os nós dos agentes. Assim, como um serviço gerido kubernetes, AKS é gratuito; só pagas pelos nós dos agentes dentro dos teus agrupamentos, não pelos mestres.  

Pode criar um cluster AKS utilizando o portal Azure, o Azure CLI, Azure PowerShell ou utilizando opções de implementação orientadas por modelos, tais como modelos de Gestor de Recursos e Terraform. Ao implementar um cluster do AKS, o mestre do Kubernetes e todos os nós são implementados e configurados para si. As funcionalidades adicionais, como redes avançadas, integração do Azure Active Directory e monitorização também podem ser configuradas durante o processo de implementação. Os recipientes do Windows Server são suportados em AKS.

Para obter mais informações sobre o básico de Kubernetes, consulte [os conceitos fundamentais de Kubernetes para AKS][concepts-clusters-workloads].

Para começar, complete o Quickstart AKS [no portal Azure][aks-portal] ou [com o Azure CLI][aks-cli].

[!INCLUDE [azure-lighthouse-supported-service](../../includes/azure-lighthouse-supported-service.md)]

## <a name="access-security-and-monitoring"></a>Acesso, segurança e monitorização

Para uma melhor segurança e gestão, a AKS permite integrar-se com o Azure Ative Directory (Azure AD) e:
* Utilize o controlo de acesso baseado em funções da Kubernetes (Kubernetes RBAC). 
* Monitorize a saúde do seu cluster e recursos.

### <a name="identity-and-security-management"></a>Gestão de identidade e segurança

Para limitar o acesso aos recursos de cluster, a AKS suporta [o RBAC de Kubernetes.][kubernetes-rbac] O KUBernetes RBAC permite controlar o acesso e permissões aos recursos e espaços de nomes da Kubernetes.  

Também pode configurar um cluster AKS para integrar com a Azure AD. Com a integração AD AZure, você pode configurar o acesso de Kubernetes com base na identidade existente e na adesão ao grupo. Os seus utilizadores e grupos AZure AD existentes podem ser fornecidos com uma experiência integrada de sign-on e acesso aos recursos AKS.  

Para obter mais informações sobre identidade, consulte [opções de acesso e identidade para AKS.][concepts-identity]

Para proteger os seus clusters AKS, veja [Integrar o Azure Active Directory com o AKS][aks-aad].

### <a name="integrated-logging-and-monitoring"></a>Registo e monitorização integrados

O Azure Monitor for Container Health recolhe métricas de desempenho de memória e processador a partir de recipientes, nós e controladores dentro do seu cluster AKS e aplicações implementadas. Pode rever os registos dos contentores e [os registos principais de Kubernetes.][aks-master-logs] Estes dados de monitorização são armazenados num espaço de trabalho Azure Log Analytics e estão disponíveis através do portal Azure CLI ou de um ponto final REST.

Para obter mais informações, veja [Monitorizar o estado de funcionamento do contentor do Azure Kubernetes Service][container-health].

## <a name="clusters-and-nodes"></a>Aglomerados e nosdes

Os nosdes AKS funcionam em máquinas virtuais Azure (VMs). Com os nós AKS, pode ligar o armazenamento a nós e cápsulas, atualizar componentes de cluster e utilizar GPUs. A AKS suporta clusters Kubernetes que executam várias piscinas de nós para suportar sistemas operativos mistos e recipientes do Windows Server.  

Para obter mais informações sobre as capacidades de cluster, nó e piscina de nó de Kubernetes, consulte os [conceitos centrais de Kubernetes para AKS][concepts-clusters-workloads].

### <a name="cluster-node-and-pod-scaling"></a>Dimensionamento dos pods e dos nós do cluster

À medida que a procura dos recursos se altera, o número de pods ou nós do cluster que executam os serviços pode aumentar ou reduzir verticalmente de forma automática. Pode utilizar o dimensionamento automático horizontal do pod ou o dimensionamento automático do cluster. Esta abordagem do dimensionamento permite que o cluster do AKS se ajuste automaticamente às exigências e execute apenas os recursos necessários.

Para obter mais informações, veja [Dimensionar um cluster do Azure Kubernetes Service (AKS)][aks-scale].

### <a name="cluster-node-upgrades"></a>Atualizações do nó de cluster

A AKS oferece várias versões Kubernetes. À medida que novas versões ficam disponíveis no AKS, a versão do seu cluster pode ser atualizada através do portal do Azure ou da CLI do Azure. Durante o processo de atualização, os nós são cuidadosamente isolados e drenados para minimizar a interrupção das aplicações em execução.  

Para saber mais sobre as versões do ciclo de vida, veja [Versões suportadas do Kubernetes no AKS][aks-supported versions]. Para obter os passos de atualização, veja [Atualizar um cluster do Azure Kubernetes Service (AKS)][aks-upgrade].

### <a name="gpu-enabled-nodes"></a>GÚls de GPU

A AKS apoia a criação de piscinas de nó ativadas pela GPU. A Azure fornece atualmente VMs individuais ou múltiplos ativados por GPU. Os VMs ativados pela GPU são concebidos para cargas de trabalho de visualização intensivas de cálculo, gráficas e visualização.

Para obter mais informações, veja [Utilizar GPUs no AKS][aks-gpu].

### <a name="confidential-computing-nodes-public-preview"></a>Nós de computação confidencial (pré-visualização pública)

A AKS apoia a criação de piscinas de nó de computação confidenciais e baseadas em Intel SGX (DCSv2 VMs). Os nosdes de computação confidencial permitem que os contentores corram num ambiente de execução baseado em hardware e fidedigno (enclaves). O isolamento entre contentores, combinado com a integridade do código através do atestado, pode ajudar com a sua estratégia de segurança de contentores em profundidade. Os nós de computação confidencial suportam tanto os contentores confidenciais (aplicações existentes do Docker) como os contentores conscientes do enclave.

Para obter mais informações, consulte [os nós de computação confidencial na AKS][conf-com-node].

### <a name="storage-volume-support"></a>Suporte para volume de armazenamento

Para suportar cargas de trabalho da aplicação, pode montar volumes de armazenamento para dados persistentes. Pode utilizar volumes estáticos e dinâmicos. Dependendo do número de cápsulas conectadas que se espera que partilhem os volumes de armazenamento, pode utilizar o armazenamento apoiado por Discos Azure para acesso a uma única cápsula ou ficheiros Azure para acesso a vários pods simultâneos.

Para obter mais informações, consulte [as opções de Armazenamento para aplicações em AKS][concepts-storage].

Iniciou-se com volumes dinâmicos e persistentes utilizando [discos Azure][azure-disk] ou [Ficheiros Azure][azure-files].

## <a name="virtual-networks-and-ingress"></a>Redes virtuais e entrada

Um cluster do AKS pode ser implementado numa rede virtual existente. Nesta configuração, cada pod do cluster é atribuído um endereço IP na rede virtual, e pode comunicar diretamente com outros pods no cluster e outros nós na rede virtual. Os pods também podem ligar-se a outros serviços numa rede virtual espreitadas e a redes de VPN no local por via expressa ou por ligações VPN site-to-site (S2S).  

Para mais informações, consulte os [conceitos de Rede para aplicações em AKS][aks-networking].

### <a name="ingress-with-http-application-routing"></a>Entrar com encaminhamento de aplicações de HTTP

O suplemento de encaminhamento de aplicações de HTTP facilita o acesso às aplicações implementadas no cluster do AKS. Quando ativada, a solução de encaminhamento de aplicação de HTTP configura um controlador de entradas no seu cluster AKS.  

À medida que as aplicações são implementadas, os nomes DNS acessíveis ao público são configurados automaticamente. O encaminhamento de aplicações HTTP configura uma zona DE DNS e integra-a com o cluster AKS. Em seguida, pode implementar os recursos de entrada do Kubernetes como habitualmente.  

Para obter uma introdução ao tráfego de entrada, veja [Encaminhamento de aplicações de HTTP][aks-http-routing].

## <a name="development-tooling-integration"></a>Integração de ferramentas de desenvolvimento

A Kubernetes tem um rico ecossistema de ferramentas de desenvolvimento e gestão que funcionam perfeitamente com a AKS. Estas ferramentas incluem Helm e a extensão Kubernetes para Código de Estúdio Visual. Essas ferramentas funcionam perfeitamente com o AKS.  

Além disso, a Azure fornece várias ferramentas que ajudam a dinamizar Kubernetes, como Azure Dev Spaces e DevOps Starter.  

O Azure Dev Spaces oferece uma experiência de desenvolvimento do Kubernetes rápida e iterativa para equipas. Com a configuração mínima, pode executar e depurar contentores diretamente no AKS. Para obter uma introdução, veja[Azure Dev Spaces][azure-dev-spaces].

O DevOps Starter fornece uma solução simples para trazer o código existente e os repositórios de Git para o Azure. DevOps Starter automaticamente:
* Cria recursos Azure (como AKS); 
* Configura um oleoduto de libertação nos Serviços Azure DevOps que inclui um gasoduto de construção para CI; 
* Configurar um oleoduto de libertação para CD; e, 
* Gera um recurso Azure Application Insights para monitorização. 

Para obter mais informações, consulte [o DevOps Starter][azure-devops].

## <a name="docker-image-support-and-private-container-registry"></a>Suporte de imagem do Docker e registo de contentor privado

O AKS suporta o formato de imagem do Docker. Para o armazenamento privado das suas imagens do Docker, pode integrar AKS com o Azure Container Registry (ACR).

Para criar uma loja de imagens privada, consulte [o Registo do Contentor Azure.][acr-docs]

## <a name="kubernetes-certification"></a>Certificação Kubernetes

AKS foi certificada pela CNCF como conformante de Kubernetes.

## <a name="regulatory-compliance"></a>Conformidade regulamentar

AKS está em conformidade com SOC, ISO, PCI DSS e HIPAA. Para obter mais informações, consulte [a visão geral da conformidade do Microsoft Azure][compliance-doc].

## <a name="next-steps"></a>Passos seguintes

Saiba mais sobre a implementação e gestão da AKS com o Azure CLI Quickstart.

> [!div class="nextstepaction"]
> [Início Rápido do AKS][aks-cli]

<!-- LINKS - external -->
[aks-engine]: https://github.com/Azure/aks-engine
[kubectl-overview]: https://kubernetes.io/docs/user-guide/kubectl-overview/
[compliance-doc]: https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942

<!-- LINKS - internal -->
[acr-docs]: ../container-registry/container-registry-intro.md
[aks-aad]: ./azure-ad-integration-cli.md
[aks-cli]: ./kubernetes-walkthrough.md
[aks-gpu]: ./gpu-cluster.md
[aks-http-routing]: ./http-application-routing.md
[aks-networking]: ./concepts-network.md
[aks-portal]: ./kubernetes-walkthrough-portal.md
[aks-scale]: ./tutorial-kubernetes-scale.md
[aks-upgrade]: ./upgrade-cluster.md
[azure-dev-spaces]: ../dev-spaces/index.yml
[azure-devops]: ../devops-project/overview.md
[azure-disk]: ./azure-disks-dynamic-pv.md
[azure-files]: ./azure-files-dynamic-pv.md
[container-health]: ../azure-monitor/containers/container-insights-overview.md
[aks-master-logs]: view-master-logs.md
[aks-supported versions]: supported-kubernetes-versions.md
[concepts-clusters-workloads]: concepts-clusters-workloads.md
[kubernetes-rbac]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[concepts-identity]: concepts-identity.md
[concepts-storage]: concepts-storage.md
[conf-com-node]: ../confidential-computing/confidential-nodes-aks-overview.md
