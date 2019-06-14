---
title: Segurança do contentor de abstrata no Microsoft Azure
description: Abstrato para a segurança do contentor no white paper do Microsoft Azure.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.openlocfilehash: 0f892767bb7ab8202a27f298b1cb708d9802aaa4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60590101"
---
# <a name="container-security-in-microsoft-azure"></a>Segurança do contentor no Microsoft Azure
## <a name="abstract"></a>Abstrato

Tecnologia de contentores está a causar uma alteração no mundo da computação em nuvem. Contentores que seja possível executar várias instâncias de um aplicativo numa única instância de um sistema operacional, usando recursos de forma mais eficiente. Contentores oferecem flexibilidade e consistência de organizações. Eles permitem a implementação contínua, porque o aplicativo pode ser desenvolvido num desktop, testado numa máquina virtual e, em seguida, implementado para produção na cloud. Os contentores oferecem agilidade, operações simplificadas, escalabilidade e redução de custos devido a otimização de recursos.

Como a tecnologia de contentores é relativamente nova, muitos profissionais de TI tem preocupações de segurança sobre a falta de visibilidade e a utilização num ambiente de produção. As equipes de desenvolvimento, muitas vezes, estão cientes dos melhores práticas de segurança. Este white paper pode ajudar as equipas de operações de segurança e os desenvolvedores a seleção de abordagens para proteger o desenvolvimento de contentores e implementações na plataforma Microsoft Azure.

Este documento descreve os contentores, a implementação do contentor e gestão e serviços de plataforma nativa. Também descreve problemas de segurança de tempo de execução que possam surgir com o uso de contentores na plataforma do Azure. As figuras e exemplos, este documento se concentra no Docker como o modelo de contentores e Kubernetes como o orquestrador de contentores. A maioria das recomendações de segurança também aplicam-se a outros modelos de contentor de parceiros da Microsoft na plataforma do Azure.

[Baixe o white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)