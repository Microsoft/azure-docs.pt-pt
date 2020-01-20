---
title: PRETERIDO Pools de agente do DC/so para o serviço de contêiner do Azure
description: Como os pools do agente público e privado funcionam com um cluster DC/OS do serviço de contêiner do Azure
author: iainfoulds
ms.service: container-service
ms.topic: conceptual
ms.date: 01/04/2017
ms.author: iainfou
ms.custom: mvc
ms.openlocfilehash: bb9b33bf537ebd5a563f8e8a8afd45cd2e5b292d
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76278386"
---
# <a name="deprecated-dcos-agent-pools-for-azure-container-service"></a>PRETERIDO Pools de agente do DC/so para o serviço de contêiner do Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

OS clusters DC/OS no serviço de contêiner do Azure contêm nós de agente em dois pools, um pool público e um pool privado. Um aplicativo pode ser implantado em qualquer pool, afetando a acessibilidade entre as máquinas no seu serviço de contêiner. Os computadores podem ser expostos à Internet (público) ou mantidos internos (privados). Este artigo fornece uma breve visão geral de por que há pools públicos e privados.


* **Agentes privados**: nós de agente privado são executados por meio de uma rede não roteável. Essa rede só pode ser acessada da zona de administração ou por meio do roteador de borda da zona pública. Por padrão, o DC/so inicia aplicativos em nós de agente privado. 

* **Agentes públicos**: nós de agente público executam aplicativos e serviços DC/os por meio de uma rede acessível publicamente. 

Para obter mais informações sobre a segurança de rede do DC/so, consulte a [documentação do DC/so](https://docs.mesosphere.com/).

## <a name="deploy-agent-pools"></a>Implantar pools de agente

Os pools de agente do DC/so no serviço de contêiner do Azure são criados da seguinte maneira:

* O **pool privado** contém o número de nós de agente que você especifica ao [implantar o cluster de DC/so](container-service-deployment.md). 

* O **pool público** inicialmente contém um número predeterminado de nós de agente. Esse pool é adicionado automaticamente quando o cluster DC/OS é provisionado.

O pool privado e o pool público são conjuntos de dimensionamento de máquinas virtuais do Azure. Você pode redimensionar esses pools após a implantação.

## <a name="use-agent-pools"></a>Usar pools de agente
Por padrão, o **Marathon** implanta qualquer novo aplicativo nos nós do agente *privado* . Você precisa implantar explicitamente o aplicativo nos nós *públicos* durante a criação do aplicativo. Selecione a guia **opcional** e insira **slave_public** para o valor **funções de recurso aceitas** . Esse processo está documentado [aqui](container-service-mesos-marathon-ui.md#deploy-a-docker-formatted-container) e na documentação do [DC/so](https://docs.mesosphere.com/1.7/administration/installing/oss/custom/create-public-agent/) .

## <a name="next-steps"></a>Passos seguintes
* Leia mais sobre como [gerenciar seus contêineres de DC/so](container-service-mesos-marathon-ui.md).

* Saiba como [abrir o firewall](container-service-enable-public-access.md) fornecido pelo Azure para permitir acesso público aos seus contêineres de DC/so.

