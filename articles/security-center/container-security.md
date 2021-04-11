---
title: Segurança de contentores com O Centro de Segurança Azure e Azure Defender
description: Saiba mais sobre as funcionalidades de segurança do Azure Security Center
author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: overview
ms.date: 04/06/2021
ms.author: memildin
ms.openlocfilehash: 9fddb27ee6a1139fa8b07c6c19dd4fdf1a20096e
ms.sourcegitcommit: d40ffda6ef9463bb75835754cabe84e3da24aab5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107029148"
---
# <a name="container-security-in-security-center"></a>Segurança do contentor no Centro de Segurança

O Azure Security Center é a solução nativa do Azure para proteger os seus contentores.

O Centro de Segurança pode proteger os seguintes tipos de recursos de contentores:

| Tipo de recurso | Proteções oferecidas pelo Centro de Segurança |
|:--------------------:|-----------|
| ![Serviço Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)<br>**Clusters do Kubernetes** | Avaliação contínua dos seus clusters para fornecer visibilidade em configurações erradas e diretrizes para ajudá-lo a mitigar ameaças identificadas. Saiba mais sobre [o endurecimento do ambiente através de recomendações de segurança.](#environment-hardening)<br><br>Proteção contra ameaças para aglomerados e nós Linux. Os alertas para atividades suspeitas são fornecidos pelo [Azure Defender para Kubernetes.](defender-for-kubernetes-introduction.md) Este plano Azure Defender defende os seus clusters Kubernetes quer estejam hospedados no Serviço Azure Kubernetes (AKS), no local ou em outros fornecedores de nuvem. aglomerados. <br>Saiba mais sobre [a proteção do tempo de execução para nós e agrupamentos Kubernetes](#run-time-protection-for-kubernetes-nodes-and-clusters).|
| ![Hospedeiro de contentores](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)<br>**Anfitriões de contentores**<br>(VMs em execução Docker) | Avaliação contínua dos seus ambientes Docker para fornecer visibilidade em configurações erradas e diretrizes para ajudá-lo a mitigar ameaças identificadas pelo [Azure Defender](defender-for-servers-introduction.md)opcional para servidores .<br>Saiba mais sobre [o endurecimento do ambiente através de recomendações de segurança.](#environment-hardening)|
| ![Registo de contentores](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)<br>**Registos do Registo de Contentores de Azure (ACR)** | Ferramentas de avaliação e gestão de vulnerabilidades para as imagens nos registos ACR baseados em Recursos Azure com o [Opcional Azure Defender para registos de contentores](defender-for-container-registries-introduction.md).<br>Saiba mais sobre [a digitalização das suas imagens de contentores para obter vulnerabilidades.](#vulnerability-management---scanning-container-images) |
|||

Este artigo descreve como pode utilizar o Security Center, juntamente com os planos opcionais do Azure Defender para registos de contentores, cortes e Kubernetes, para melhorar, monitorizar e manter a segurança dos seus contentores e das suas aplicações.

Você vai aprender como o Centro de Segurança ajuda com estes aspetos fundamentais da segurança do contentor:

- [Gestão de vulnerabilidades - digitalização de imagens de contentores](#vulnerability-management---scanning-container-images)
- [Endurecimento do ambiente](#environment-hardening)
- [Proteção do tempo de execução para os nódoas e aglomerados kubernetes](#run-time-protection-for-kubernetes-nodes-and-clusters)

A imagem que se segue mostra a página de inventário do ativo e os vários tipos de recursos de contentores protegidos pelo Security Center.

:::image type="content" source="./media/container-security/inventory-container-resources.png" alt-text="Recursos relacionados com contentores na página de inventário de ativos do Security Center" lightbox="./media/container-security/inventory-container-resources.png":::

## <a name="vulnerability-management---scanning-container-images"></a>Gestão de vulnerabilidades - digitalização de imagens de contentores

Para monitorizar as imagens nos registos de contentores Azure baseados em Recursos Azure, ative [o Azure Defender para registos de contentores](defender-for-container-registries-introduction.md). O Centro de Segurança verifica quaisquer imagens retiradas nos últimos 30 dias, empurradas para o seu registo, ou importadas. O scanner integrado é fornecido pelo fornecedor líder de pesquisa de vulnerabilidades da indústria, Qualys.

Quando forem encontrados problemas – pelo Qualys ou pelo Security Center – será notificado no [painel Azure Defender](azure-defender-dashboard.md). Para cada vulnerabilidade, o Security Center fornece recomendações acccáveis, juntamente com uma classificação de gravidade, e orientações para como remediar o problema. Para obter mais informações sobre as recomendações do Centro de Segurança para os contentores, consulte a [lista de referência das recomendações.](recommendations-reference.md#recs-compute)

O Centro de Segurança filtra e classifica as descobertas do scanner. Quando uma imagem é saudável, o Centro de Segurança marca-a como tal. O Centro de Segurança gera recomendações de segurança apenas para imagens que têm problemas a resolver. Ao notificar apenas quando há problemas, o Centro de Segurança reduz o potencial de alertas informativos indesejados.

## <a name="environment-hardening"></a>Endurecimento do ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitorização contínua da sua configuração Docker

O Azure Security Center identifica contentores não geridos alojados em IaaS Linux VMs, ou outras máquinas Linux que executam contentores Docker. O Centro de Segurança avalia continuamente as configurações destes contentores. Em seguida, compara-os com o [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

O Centro de Segurança inclui todo o conjunto de regras do CIS Docker Benchmark e alerta-o se os seus contentores não satisfizerem nenhum dos controlos. Quando encontra configurações erradas, o Centro de Segurança gera recomendações de segurança. Use a página de **recomendações** do Centro de Segurança para ver recomendações e remediar problemas. Os controlos de referência do CIS não são executados em instâncias geridas pela AKS ou VMs geridos por Databricks.

Para obter mais detalhes sobre as recomendações relevantes do Centro de Segurança que possam aparecer para esta funcionalidade, consulte a secção de [cálculo](recommendations-reference.md#recs-compute) da tabela de referência de recomendações.

Quando está a explorar os problemas de segurança de um VM, o Security Center fornece informações adicionais sobre os contentores na máquina. Essas informações incluem a versão Docker e o número de imagens em execução no anfitrião. 

Para monitorizar os recipientes não geridos alojados nos VMs IaaS Linux, ative o [Azure Defender](defender-for-servers-introduction.md)opcional para servidores .


### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitorização contínua dos seus clusters Kubernetes
O Security Center trabalha em conjunto com o Azure Kubernetes Service (AKS), o serviço de orquestração de contentores gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas.

A AKS fornece controlos de segurança e visibilidade na postura de segurança dos seus clusters. O Security Center utiliza estas funcionalidades para monitorizar constantemente a configuração dos seus clusters AKS e gerar recomendações de segurança alinhadas com os padrões da indústria.

Este é um diagrama de alto nível da interação entre o Azure Security Center, o Azure Kubernetes Service e a Azure Policy:

:::image type="content" source="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png" alt-text="Arquitetura de alto nível da interação entre o Azure Security Center, o Azure Kubernetes Service e a Azure Policy" lightbox="./media/defender-for-kubernetes-intro/kubernetes-service-security-center-integration-detailed.png":::

Pode ver que os itens recebidos e analisados pelo Security Center incluem:

- registos de auditoria do servidor API
- eventos de segurança bruto do agente Log Analytics

    > [!NOTE]
    > Atualmente, não suportamos a instalação do agente Log Analytics nos clusters de serviço Azure Kubernetes que estão a funcionar em conjuntos de escala de máquina virtual.

- informação de configuração do cluster AKS
- configuração de carga de trabalho da Azure Policy (através do **addon Azure Policy para Kubernetes)**

Para obter mais detalhes sobre as recomendações relevantes do Centro de Segurança que possam aparecer para esta funcionalidade, consulte a secção de [cálculo](recommendations-reference.md#recs-compute) da tabela de referência de recomendações.


###  <a name="workload-protection-best-practices-using-kubernetes-admission-control"></a>Melhores práticas de proteção da carga de trabalho utilizando o controlo de admissão de Kubernetes

Para obter um conjunto de recomendações para proteger as cargas de trabalho dos seus recipientes Kubernetes, instale o  **addon Azure Policy para Kubernetes**. Também pode implementar automaticamente este addon como explicado no [Enable auto provisioning do agente Log Analytics e extensões](security-center-enable-data-collection.md#auto-provision-mma). Quando o fornecimento automático para o addon é definido como "on", a extensão é ativada por padrão em todos os clusters existentes e futuros (que satisfazem os requisitos de instalação adicionais).

Tal como explicado nesta [página da Política Azure para kubernetes,](../governance/policy/concepts/policy-for-kubernetes.md)o add-on estende o webhook do controlador de admissão [gatekeeper v3](https://github.com/open-policy-agent/gatekeeper)de código aberto   para o Agente de Política [Aberta](https://www.openpolicyagent.org/). Os controladores de admissão kubernetes são plugins que impõem a forma como os seus clusters são usados. O addon regista-se como um gancho web para o controlo de admissão de Kubernetes e permite aplicar aplicações e salvaguardas em escala nos seus clusters de forma centralizada e consistente. 

Com o addon no seu cluster AKS, todos os pedidos para o servidor API de Kubernetes serão monitorizados contra o conjunto pré-definido de boas práticas antes de serem persistidos no cluster. Em seguida, pode configurar para **impor** as melhores práticas e mandatá-las para futuras cargas de trabalho. 

Por exemplo, pode ordenar que os contentores privilegiados não sejam criados, e quaisquer pedidos futuros para o fazer serão bloqueados.

Saiba mais em [Proteger as cargas de trabalho dos Seus Kubernetes.](kubernetes-workload-protections.md)


## <a name="run-time-protection-for-kubernetes-nodes-and-clusters"></a>Proteção do tempo de execução para os nódoas e aglomerados kubernetes

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]



## <a name="next-steps"></a>Passos seguintes

Nesta visão geral, você aprendeu sobre os elementos fundamentais da segurança do contentor no Azure Security Center. Para materiais relacionados ver:

- [Introdução ao Azure Defender para Kubernetes](defender-for-kubernetes-introduction.md)
- [Introdução ao Azure Defender dos registos de contentores](defender-for-container-registries-introduction.md)