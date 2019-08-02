---
title: Segurança de contêiner abstrato no Microsoft Azure
description: Resumo para a segurança do contêiner em Microsoft Azure white paper.
author: TomShinder
ms.author: TomSh
ms.date: 09/05/2018
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.openlocfilehash: b70744f403c483448a844d3f3decf6ce26f48f06
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727787"
---
# <a name="container-security-in-microsoft-azure"></a>Segurança de contêiner no Microsoft Azure
## <a name="abstract"></a>Abstrato

A tecnologia de contêiner está causando uma alteração estrutural no mundo da computação em nuvem. Os contêineres possibilitam a execução de várias instâncias de um aplicativo em uma única instância de um sistema operacional, usando os recursos com mais eficiência. Os contêineres proporcionam consistência e flexibilidade às organizações. Eles habilitam a implantação contínua porque o aplicativo pode ser desenvolvido em um desktop, testado em uma máquina virtual e, em seguida, implantado para produção na nuvem. Os contêineres fornecem agilidade, operações simplificadas, escalabilidade e custos reduzidos devido à otimização de recursos.

Como a tecnologia de contêiner é relativamente nova, muitos profissionais de ti têm preocupações de segurança sobre a falta de visibilidade e uso em um ambiente de produção. As equipes de desenvolvimento geralmente não sabem das práticas recomendadas de segurança. Este white paper pode ajudar equipes de operações de segurança e desenvolvedores a selecionar abordagens para proteger o desenvolvimento de contêineres e implantações na plataforma Microsoft Azure.

Este documento descreve contêineres, implantação de contêiner e gerenciamento e serviços de plataforma nativa. Ele também descreve problemas de segurança de tempo de execução que surgem com o uso de contêineres na plataforma Azure. Em figuras e exemplos, este documento enfoca o Docker como o modelo de contêiner e o kubernetes como o orquestrador de contêiner. A maioria das recomendações de segurança também se aplica a outros modelos de contêiner de parceiros da Microsoft na plataforma do Azure.

[Baixar o white paper](https://azure.microsoft.com/mediahandler/files/resourcefiles/container-security-in-microsoft-azure/Open%20Container%20Security%20in%20Microsoft%20Azure.pdf)