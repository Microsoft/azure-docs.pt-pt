---
title: Alta disponibilidade - Azure Dedicado HSM [ Microsoft Docs
description: Azure Dedicado HSM exemplo de alta disponibilidade e considerações básicas
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 536ef62acad900090924598edfa45450b2a8c951
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70882243"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure dedicado HSM alta disponibilidade

O Azure Dedicated HSM é apoiado pelos centros de dados altamente disponíveis da Microsoft. No entanto, qualquer datacenter altamente disponível é vulnerável a falhas localizadas e, em circunstâncias extremas, falhas a nível regional. A Microsoft implementa dispositivos HSM em diferentes datacenters dentro de uma região para garantir que o fornecimento de vários dispositivos não leva a que esses dispositivos partilhem uma única prateleira. Um novo nível de alta disponibilidade pode ser alcançado emparelhando estes HSMs através dos centros de dados de uma região usando a funcionalidade gemalto HA Group. Também é possível emparelhar dispositivos entre regiões para abordar o fracasso regional numa situação de recuperação de desastres. Com esta configuração de alta disponibilidade em várias camadas, qualquer falha do dispositivo será automaticamente abordada para manter as aplicações em funcionamento. Todos os datacenters também têm dispositivos e componentes sobressalentes no local para que qualquer dispositivo falhado possa ser substituído em tempo útil.

## <a name="high-availability-example"></a>Exemplo de alta disponibilidade

A informação sobre como configurar dispositivos HSM para alta disponibilidade a nível de software está no 'Guia de Administração HSM da Rede Gemalto Luna'. Este documento está disponível na [Página HSM gemalto.](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/)

O diagrama seguinte mostra uma arquitetura altamente disponível. Utiliza vários dispositivos na região e vários dispositivos emparelhados numa região separada. Esta arquitetura utiliza um mínimo de quatro dispositivos HSM e componentes de rede virtuais.

![Diagrama de alta disponibilidade](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que todos os conceitos-chave do serviço, tais como alta disponibilidade e segurança, sejam bem compreendidos antes do fornecimento e design de aplicações do dispositivo ou implementação.
Outros tópicos de nível de conceito:

* [Arquitetura de Implantação](deployment-architecture.md)
* [Segurança Física](physical-security.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)

Para mais detalhes sobre a configuração de dispositivos HSM para alta disponibilidade, consulte o Portal de Apoio ao Cliente gemalto para os Guias de Administrador e consulte a secção 6.
