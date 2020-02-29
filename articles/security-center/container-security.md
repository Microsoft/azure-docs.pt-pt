---
title: Segurança de contentores no Centro de Segurança Azure Microsoft Docs
description: Conheça as funcionalidades de segurança de contentores do Azure Security Center.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/11/2020
ms.author: memildin
ms.openlocfilehash: cd9907d041562d23bc487de319a17d36b68db6f3
ms.sourcegitcommit: 3c925b84b5144f3be0a9cd3256d0886df9fa9dc0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2020
ms.locfileid: "77916122"
---
# <a name="container-security-in-security-center"></a>Segurança de contentores no Centro de Segurança

O Azure Security Center é a solução azure-nativa para a segurança dos contentores. O Security Center é também o painel único ideal de experiência de vidro para a segurança das suas cargas de trabalho em nuvem, VMs, servidores e contentores.

Este artigo descreve como o Security Center o ajuda a melhorar, monitorizar e manter a segurança dos seus contentores e das suas aplicações. Você vai aprender como o Centro de Segurança ajuda com estes aspetos fundamentais da segurança dos contentores:

* Gestão de vulnerabilidades
* Endurecimento do ambiente do contentor
* Proteção de tempo de corrido

[separador de segurança de contentores do ![Azure Security Center](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

Para obter instruções sobre como utilizar estas funcionalidades, consulte [monitorizar a segurança dos seus recipientes](monitor-container-security.md).

## <a name="vulnerability-management---scanning-container-images-preview"></a>Gestão de vulnerabilidades - digitalização de imagens de contentores (Pré-visualização)
Para monitorizar o registo de contentores Azure baseado no ARM, certifique-se de que está no nível padrão do Security Center (ver [preços).](/azure/security-center/security-center-pricing) Em seguida, ative o pacote opcional de registos de contentores. Quando uma nova imagem é empurrada, o Security Center digitaliza a imagem usando um scanner do fornecedor de vulnerabilidadelíder da indústria, qualys.

Quando os problemas forem encontrados – por Qualys ou Security Center – será notificado no painel do Centro de Segurança. Para cada vulnerabilidade, o Security Center fornece recomendações atol, juntamente com uma classificação de gravidade, e orientação para como remediar a questão. Para obter detalhes sobre as recomendações do Centro de Segurança para os contentores, consulte a [lista de recomendações de referência.](recommendations-reference.md#recs-containers)

## <a name="environment-hardening"></a>Endurecimento ambiental

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitorização contínua da sua configuração Docker
O Azure Security Center identifica contentores não geridos alojados em VMs IaaS Linux, ou outras máquinas Linux que executam contentores Docker. O Centro de Segurança avalia continuamente as configurações destes contentores. Em seguida, compara-os com o [Center for Internet Security (CIS) Docker Benchmark](https://www.cisecurity.org/benchmark/docker/).

O Centro de Segurança inclui todo o conjunto de regras do Referencial CIS Docker e alerta-o se os seus contentores não satisfizerem nenhum dos controlos. Quando encontra configurações erradas, o Centro de Segurança gera recomendações de segurança. Utilize a página de **recomendações** para ver recomendações e remediar questões. Você também verá as recomendações no separador **Contentores** que exibem todas as máquinas virtuais implantadas com O Docker. 

Para obter detalhes sobre as recomendações relevantes do Centro de Segurança que possam aparecer para esta funcionalidade, consulte a secção de [contentores](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

Ao explorar as questões de segurança de um VM, o Security Center fornece informações adicionais sobre os contentores na máquina. Tais informações incluem a versão Docker e o número de imagens em execução no hospedeiro. 

>[!NOTE]
> Estes controlos de referência do CIS não serão executados em instâncias geridas pela AKS ou em VMs geridos por Databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Monitorização contínua dos seus clusters Kubernetes (Pré-visualização)
O Security Center trabalha em conjunto com o Azure Kubernetes Service (AKS), o serviço de orquestração de contentores gerido pela Microsoft para o desenvolvimento, implantação e gestão de aplicações contentorizadas.

A AKS fornece controlos de segurança e visibilidade na postura de segurança dos seus clusters. O Centro de Segurança utiliza estas funcionalidades para:
* Monitorize constantemente a configuração dos seus clusters AKS
* Gerar recomendações de segurança alinhadas com os padrões do setor

Para obter detalhes sobre as recomendações relevantes do Centro de Segurança que possam aparecer para esta funcionalidade, consulte a secção de [contentores](recommendations-reference.md#recs-containers) da tabela de referência de recomendações.

## <a name="run-time-protection---real-time-threat-protection"></a>Proteção em tempo de execução - Proteção contra ameaças em tempo real

O Security Center fornece proteção contra ameaças em tempo real para os seus ambientes contentorizados e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

Detetamos ameaças ao nível do hospedeiro e do cluster AKS. Para mais detalhes, consulte [a proteção contra ameaças para os contentores Azure](threat-protection.md#azure-containers).


## <a name="container-security-faq"></a>FAQ de segurança de contentores

### <a name="what-types-of-images-can-azure-security-center-scan"></a>Que tipos de imagens podem digitalizar o Azure Security Center?
O Centro de Segurança digitaliza imagens baseadas em Linux OS que fornecem acesso à concha. 

O scanner Qualys não suporta imagens super minimalistas como imagens de [riscado Docker,](https://hub.docker.com/_/scratch/) ou imagens "Distroless" que contêm apenas a sua aplicação e as suas dependências de tempo de funcionamento (sem um gestor de pacotes, concha ou S).

### <a name="how-does-we-scan-azure-security-center-scan-an-image"></a>Como é que digitalizamos o Azure Security Center uma imagem?
A imagem é extraída do registo. É então executado numa caixa de areia isolada com o scanner Qualys que extrai uma lista de vulnerabilidades conhecidas.

### <a name="how-often-does-azure-security-center-scan-my-images"></a>Com que frequência o Centro de Segurança Azure digitaliza as minhas imagens?
Os exames de imagem são acionados em cada impulso.

### <a name="can-i-get-the-scan-results-via-rest-api"></a>Posso obter os resultados da varredura via REST API?
Sim. Os resultados estão em [Sub-Avaliações Rest API](/rest/api/securitycenter/subassessments/list/). Além disso, você pode usar o Azure Resource Graph (ARG), a API semelhante a Kusto para todos os seus recursos: uma consulta pode obter uma digitalização específica.
 

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre segurança de contentores no Centro de Segurança Azure, consulte estes artigos relacionados:

* Para ver a postura de segurança dos seus recursos relacionados com contentores, consulte a secção de contentores de [Proteger as suas máquinas e aplicações](security-center-virtual-machine-protection.md#containers).

* Detalhes da integração com o [Serviço Azure Kubernetes](azure-kubernetes-service-integration.md)

* Detalhes da integração com o Registo de [Contentores Azure](azure-container-registry-integration.md)