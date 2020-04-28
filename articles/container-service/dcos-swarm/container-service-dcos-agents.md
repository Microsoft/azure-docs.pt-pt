---
title: (DEPRECIADO) Piscinas de agente DC/OS para o Serviço de Contentores Azure
description: Como as piscinas de agentes públicos e privados funcionam com um cluster Azure Container Service DC/OS
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "76278386"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>(DEPRECIADO) Piscinas de agente DC/OS para o Serviço de Contentores Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Os clusters DC/OS do Serviço de Contentores Azure contêm nós de agente em duas piscinas, uma piscina pública e uma piscina privada. Uma aplicação pode ser implementada em qualquer piscina, afetando a acessibilidade entre máquinas no seu serviço de contentores. As máquinas podem ser expostas à internet (pública) ou mantidas internas (privadas). Este artigo dá uma breve visão geral do porquê de existirem piscinas públicas e privadas.


* **Agentes privados**: Os nós de agente privado passam por uma rede não-enedaível. Esta rede só é acessível a partir da zona de administração ou através do router de borda da zona pública. Por padrão, a DC/OS lança aplicações em nós de agente privado. 

* **Agentes públicos**: Os nós de agente público executam aplicações e serviços DC/OS através de uma rede acessível ao público. 

Para obter mais informações sobre a segurança da rede DC/OS, consulte a [documentação DC/OS](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Implementar piscinas de agentes

As piscinas de agentes DC/OS No Serviço de Contentores Azure são criadas da seguinte forma:

* O **pool privado** contém o número de nós de agente que especifica quando [implanta o cluster DC/OS](container-service-deployment.md). 

* A **piscina pública** contém inicialmente um número pré-determinado de nós de agente. Este pool é adicionado automaticamente quando o cluster DC/OS é aprovisionado.

A piscina privada e a piscina pública são conjuntos de escala de máquinas virtuais Azure. Você pode redimensionar estas piscinas após a implantação.

## <a name="use-agent-pools"></a>Use piscinas de agentes
Por padrão, **a Marathon** implementa qualquer nova aplicação aos nós de agente *privado.* Tem de implementar explicitamente a aplicação aos nomes *públicos* durante a criação da aplicação. Selecione o separador **Opcional** e introduza **slave_public** para o valor das Funções de **Recurso Aceites.** Este processo está documentado [aqui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e na documentação [DC/OS.](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/)

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre a gestão dos [seus contentores DC/OS](container-service-mesos-marathon-ui.md).

* Saiba como [abrir a firewall](container-service-enable-public-access.md) fornecida pelo Azure para permitir o acesso do público aos seus contentores DC/OS.

