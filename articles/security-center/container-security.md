---
title: Segurança de contêiner na central de segurança do Azure | Microsoft Docs
description: Saiba mais sobre os recursos de segurança do contêiner da central de segurança do Azure.
services: security-center
documentationcenter: na
author: memildin
manager: rkarlin
ms.service: security-center
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/04/2019
ms.author: memildin
ms.openlocfilehash: 7bd097885d629a6c8541b57195f2a2996e45ffaf
ms.sourcegitcommit: ec2eacbe5d3ac7878515092290722c41143f151d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2019
ms.locfileid: "75552666"
---
# <a name="container-security-in-security-center"></a>Segurança do contêiner na central de segurança

A central de segurança do Azure é a solução Azure-Native para segurança de contêiner. A central de segurança também é o ideal painel único de experiência de vidro para a segurança de suas cargas de trabalho de nuvem, VMs, servidores e contêineres.

Este artigo descreve como você pode melhorar, monitorar e manter a segurança de seus contêineres e seus aplicativos. Você aprenderá como a central de segurança ajuda com esses aspectos principais da segurança do contêiner:

* Gerenciamento de vulnerabilidades
* Proteção do ambiente do contêiner
* Proteção de tempo de execução

[![guia Segurança do contêiner da central de segurança do Azure](media/container-security/container-security-tab.png)](media/container-security/container-security-tab.png#lightbox)

## <a name="vulnerability-management---scanning-container-images-preview"></a>Gerenciamento de vulnerabilidades-verificação de imagens de contêiner (visualização)
Para monitorar o registro de contêiner do Azure, verifique se você está na camada Standard da central de segurança (consulte [preços](/azure/security-center/security-center-pricing)). Em seguida, habilite o pacote opcional de registros de contêiner. Quando uma nova imagem é enviada por push, a central de segurança examina a imagem usando um scanner do fornecedor de verificação de vulnerabilidade líder do setor, Qualys.

Quando forem encontrados problemas – por Qualys ou pela central de segurança – você será notificado no painel da central de segurança. Para cada vulnerabilidade, a central de segurança fornece recomendações acionáveis, juntamente com uma classificação de gravidade, e orientações sobre como corrigir o problema. Para obter detalhes das recomendações da central de segurança, consulte a [lista de referências de recomendações](recommendations-reference.md).

## <a name="environment-hardening"></a>Proteção do ambiente

### <a name="continuous-monitoring-of-your-docker-configuration"></a>Monitoramento contínuo da configuração do Docker
A central de segurança do Azure identifica contêineres não gerenciados hospedados em VMs do Linux IaaS ou em outros computadores Linux que executam contêineres do Docker. A central de segurança avalia continuamente as configurações desses contêineres. Em seguida, ele os compara com o [benchmark do Docker do CIS (centro para Internet Security](https://www.cisecurity.org/benchmark/docker/))). 

A central de segurança inclui o conjunto de regras inteiro do parâmetro de comparação do Docker do CIS e o alerta se os contêineres não atenderem a nenhum dos controles. Quando ele encontra configurações incorretas, a central de segurança gera recomendações de segurança. Use a **página recomendações** para exibir as recomendações e corrigir os problemas. Você também verá as recomendações na guia **contêineres** que exibe todas as máquinas virtuais implantadas com o Docker. Quando você estiver explorando os problemas de segurança em uma máquina virtual, a central de segurança fornecerá informações adicionais sobre os contêineres no computador. Essas informações incluem a versão do Docker e o número de imagens em execução no host. Para obter detalhes sobre as recomendações, consulte [aqui](https://docs.microsoft.com/azure/security-center/security-center-virtual-machine-protection). 

>[!NOTE]
> Essas verificações de parâmetro de comparação de CIS não serão executadas em instâncias gerenciadas por AKS ou em VMs gerenciadas por databricks.

### <a name="continuous-monitoring-of-your-kubernetes-clusters-preview"></a>Monitoramento contínuo de seus clusters kubernetes (visualização)
A central de segurança funciona junto com o AKS (serviço de kubernetes do Azure), o serviço de orquestração de contêiner gerenciado da Microsoft para desenvolver, implantar e gerenciar aplicativos em contêineres.

O AKS fornece controles de segurança e visibilidade da postura de segurança de seus clusters. A central de segurança usa esses recursos para:
* Monitore constantemente a configuração de seus clusters AKS
* Gerar recomendações de segurança alinhadas com os padrões do setor

Para obter detalhes das recomendações da central de segurança, consulte [proteção da máquina virtual](security-center-virtual-machine-protection.md).

## <a name="run-time-protection---real-time-threat-detection"></a>Proteção de tempo de execução-detecção de ameaças em tempo real

A central de segurança fornece detecção de ameaças em tempo real para seus ambientes em contêineres e gera alertas para atividades suspeitas. Pode utilizar estas informações para remediar rapidamente problemas de segurança e aumentar a segurança dos contentores.

Detectamos ameaças no nível de cluster host e AKS. Para obter detalhes completos, consulte [detecção de ameaças para contêineres do Azure](https://docs.microsoft.com/azure/security-center/security-center-alerts-compute#azure-containers-).

## <a name="to-view-the-security-posture-of-your-container-related-resources"></a>Para exibir a postura de segurança de seus recursos relacionados ao contêiner
1.  Abra a página de **aplicativos & de computação** da central de segurança.
2.  Clique na guia **contêineres** . A postura de seus clusters AKS, registros de ACR e VMs que executam o Docker é exibida.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a segurança de contêiner na central de segurança do Azure, consulte:
* Detalhes da [integração com o serviço kubernetes do Azure](azure-kubernetes-service-integration.md)

* Detalhes da [integração com o registro de contêiner do Azure](azure-container-registry-integration.md)