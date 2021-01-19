---
title: Alta disponibilidade - Azure Dedicado HSM | Microsoft Docs
description: Conheça as considerações básicas para a Azure Dedicada HSM alta disponibilidade. Este artigo inclui um exemplo.
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2021
ms.author: mbaldwin
ms.openlocfilehash: 85aa9d355b2440535ea13c8c15e95500bac22352
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567255"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedicado HSM alta disponibilidade

O Azure Dedicated HSM é apoiado pelos centros de dados altamente disponíveis da Microsoft. No entanto, qualquer datacenter altamente disponível é vulnerável a falhas localizadas e em circunstâncias extremas, falhas a nível regional. A Microsoft implementa dispositivos HSM em diferentes datacenters dentro de uma região para garantir que o fornecimento de vários dispositivos não leva a que esses dispositivos partilhem uma única cremalheira. Um novo nível de alta disponibilidade pode ser alcançado emparelhando estes HSMs através dos datacenters de uma região usando a funcionalidade do Grupo HA Thales. É também possível emparelhar dispositivos em todas as regiões para fazer face ao fracasso regional numa situação de recuperação de catástrofes. Com esta configuração de alta disponibilidade em várias camadas, qualquer falha no dispositivo será automaticamente endereçada para manter as aplicações a funcionar. Todos os datacenters também têm dispositivos e componentes sobressalentes no local para que qualquer dispositivo falhado possa ser substituído em tempo útil.

## <a name="high-availability-example"></a>Exemplo de alta disponibilidade

As informações sobre como configurar dispositivos HSM para uma elevada disponibilidade a nível de software estão no 'Guia de Administração Thales Luna 7 HSM'. Este documento está disponível na  [Página HSM do Thales](https://thalesdocs.com/gphsm/Content/luna/network/luna_network_releases.htm).

O diagrama que se segue mostra uma arquitetura altamente disponível. Utiliza vários dispositivos na região e vários dispositivos emparelhados numa região separada. Esta arquitetura utiliza um mínimo de quatro dispositivos HSM e componentes de rede virtual.

![Diagrama de alta disponibilidade](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Passos seguintes

Recomenda-se que todos os conceitos-chave do serviço, tais como alta disponibilidade e segurança, sejam bem compreendidos antes do fornecimento de dispositivos e design de aplicações ou implementação.
Outros tópicos de nível de conceito:

* [Arquitetura de Implantação](deployment-architecture.md)
* [Segurança Física](physical-security.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)

Para obter detalhes específicos sobre a configuração de dispositivos HSM para uma elevada disponibilidade, consulte o Portal de Apoio ao Cliente do Thales para os Guias do Administrador e consulte a secção 6.
