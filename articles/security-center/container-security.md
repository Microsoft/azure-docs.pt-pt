---
title: Segurança de contentores no Centro de Segurança Azure Microsoft Docs
description: Saiba mais sobre as funcionalidades de segurança do Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2020
ms.author: memildin
ms.openlocfilehash: c01ed6dbbd6e1f7febfb99df11d2ee67cb1e5465
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "85800613"
---
# <a name="container-security-in-security-center"></a>Segurança de contentores no Centro de Segurança

O Azure Security Center é a solução nativa do Azure para proteger os seus contentores. O Centro de Segurança pode proteger os seguintes tipos de recursos de contentores:



|Recurso |Name  |Detalhes  |
|:---------:|---------|---------|
|![Hospedeiro de contentores](./media/security-center-virtual-machine-recommendations/icon-container-host-rec.png)|Anfitriões de contentores (máquinas virtuais que estão a executar Docker)|O Centro de Segurança analisa as suas configurações do Docker e fornece-lhe visibilidade sobre as configurações incorretas ao disponibilizar uma lista de todas as regras com falhas que foram analisadas. O Security Center fornece diretrizes para ajudá-lo a resolver estes problemas rapidamente e economizar tempo. O Centro de Segurança avalia continuamente as configurações do Docker e fornece o estado mais recente.|
|![Serviço Kubernetes](./media/security-center-virtual-machine-recommendations/icon-kubernetes-service-rec.png)|Aglomerados de Serviço Azure Kubernetes (AKS)|Obtenha uma visibilidade mais profunda para os seus nós AKS, tráfego em nuvem e controlos de segurança com [o pacote AKS opcional do Security Center](azure-kubernetes-service-integration.md) para utilizadores de nível padrão.|
|![Registo de contentor](./media/security-center-virtual-machine-recommendations/icon-container-registry-rec.png)|Registos do Registo de Contentores de Azure (ACR)|Obtenha uma visibilidade mais profunda sobre as vulnerabilidades das imagens nos registos ACR baseados no ARM com [o pacote ACR opcional do Security Center](azure-kubernetes-service-integration.md) para utilizadores de nível padrão.|
||||


Este artigo descreve como pode usar estes pacotes para melhorar, monitorizar e manter a segurança dos seus contentores e das suas apps. Você vai aprender como o Centro de Segurança ajuda com estes aspetos fundamentais da segurança do contentor:

- [Gestão de vulnerabilidades - digitalização de imagens de contentores](#vulnerability-management---scanning-container-images)
- [Endurecimento ambiental - monitorização contínua da sua configuração Docker e aglomerados de Kubernetes](#environment-hardening)
- [Proteção em tempo de execução - Deteção de ameaças em tempo real](#run-time-protection---real-time-threat-detection)

[![Separador de segurança do Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Para obter instruções sobre como utilizar estas funcionalidades, consulte [a segurança dos seus recipientes](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images"></a>Gestão de vulnerabilidades - digitalização de imagens de contentores
Para monitorizar o registo do contentor Azure baseado na ARM, certifique-se de que está no nível padrão do Centro de Segurança (ver [preços).](/azure/security-center/security-center-pricing) Em seguida, ative o pacote opcional de registos de contentores. Quando uma nova imagem é empurrada, o Security Center digitaliza a imagem usando um scanner do fornecedor líder de pesquisa de vulnerabilidades da indústria, Qualys.

Quando os problemas forem encontrados – pelo Qualys ou pelo Security Center – será notificado no painel do Centro de Segurança. Para cada vulnerabilidade, o Security Center fornece recomendações acccáveis, juntamente com uma classificação de gravidade, e orientações para como remediar o problema. Para obter mais informações sobre as recomendações do Centro de Segurança para os contentores, consulte a [lista de referência das recomendações.](recommendations-reference.md#recs-containers)

O Centro de Segurança filtra e classifica as descobertas do scanner. Quando uma imagem é saudável, o Centro de Segurança marca-a como tal. O Centro de Segurança gera recomendações de segurança apenas para imagens que têm problemas a resolver. Ao notificar apenas quando há problemas, o Centro de Segurança reduz o potencial de alertas informativos indesejados.

## <a name="environment-hardening"></a>Endurecimento do ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitorização contínua da sua configuração Docker
O Azure Security Center identifica contentores não geridos alojados em IaaS Linux VMs, ou outras máquinas Linux que executam contentores Docker. O Centro de Segurança avalia continuamente as configurações destes contentores. Em seguida, compara-os com o [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

O Centro de Segurança inclui todo o conjunto de regras do CIS Docker Benchmark e alerta-o se os seus contentores não satisfizerem nenhum dos controlos. Quando encontra configurações erradas, o Centro de Segurança gera recomendações de segurança. Utilize a página de **recomendações** para ver recomendações e remediar questões. Você também verá as recomendações no **separador Contentores** que exibe todas as máquinas virtuais implantadas com Docker. 

Para obter mais informações sobre as recomendações relevantes do Centro de Segurança que possam aparecer para esta funcionalidade, consulte a secção de [recipientes](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

Quando está a explorar os problemas de segurança de um VM, o Security Center fornece informações adicionais sobre os contentores na máquina. Essas informações incluem a versão Docker e o número de imagens em execução no anfitrião. 

>[!NOTE]
> Estes controlos de referência do CIS não serão executados em instâncias geridas pela AKS ou em VMs geridos por Databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters"></a>Monitorização contínua dos seus clusters Kubernetes
O Security Center trabalha em conjunto com o Azure Kubernetes Service (AKS), o serviço de orquestração de contentores gerido pela Microsoft para desenvolver, implantar e gerir aplicações contentorizadas.

A AKS fornece controlos de segurança e visibilidade na postura de segurança dos seus clusters. O Centro de Segurança utiliza estas funcionalidades para:
* Monitorize constantemente a configuração dos seus clusters AKS
* Gerar recomendações de segurança alinhadas com os padrões da indústria

Para obter mais informações sobre as recomendações relevantes do Centro de Segurança que possam aparecer para esta funcionalidade, consulte a secção de [recipientes](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

## <a name="run-time-protection---real-time-threat-detection"></a>Proteção em tempo de execução - Deteção de ameaças em tempo real

[!INCLUDE [AKS in ASC threat protection](../../includes/security-center-azure-kubernetes-threat-protection.md)]




## <a name="next-steps"></a>Próximos passos

Nesta visão geral, você aprendeu sobre os elementos fundamentais da segurança do contentor no Azure Security Center. Continue a [monitorizar a segurança dos seus contentores](monitor-container-security.md).
> [!div class="nextstepaction"]
> [Monitorização da segurança dos seus contentores](monitor-container-security.md)