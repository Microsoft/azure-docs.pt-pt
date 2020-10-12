---
title: Alta disponibilidade - Azure Dedicado HSM / Microsoft Docs
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
ms.date: 03/27/2019
ms.author: mbaldwin
ms.openlocfilehash: 8f8fa2f12825fe88218fe7033a1721cb49fc7335
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88189856"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Azure Dedicado HSM alta disponibilidade

O Azure Dedicated HSM é apoiado pelos centros de dados altamente disponíveis da Microsoft. No entanto, qualquer datacenter altamente disponível é vulnerável a falhas localizadas e em circunstâncias extremas, falhas a nível regional. A Microsoft implementa dispositivos HSM em diferentes datacenters dentro de uma região para garantir que o fornecimento de vários dispositivos não leva a que esses dispositivos partilhem uma única cremalheira. Um novo nível de alta disponibilidade pode ser alcançado emparelhando estes HSMs através dos datacenters de uma região usando a funcionalidade Do Grupo Gemalto HA. É também possível emparelhar dispositivos em todas as regiões para fazer face ao fracasso regional numa situação de recuperação de catástrofes. Com esta configuração de alta disponibilidade em várias camadas, qualquer falha no dispositivo será automaticamente endereçada para manter as aplicações a funcionar. Todos os datacenters também têm dispositivos e componentes sobressalentes no local para que qualquer dispositivo falhado possa ser substituído em tempo útil.

## <a name="high-availability-example"></a>Exemplo de alta disponibilidade

As informações sobre como configurar dispositivos HSM para uma elevada disponibilidade a nível de software estão no 'Guia de Administração HSM da Rede Gemalto Luna'. Este documento está disponível na  [Página Gemalto HSM](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/).

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

Para obter detalhes específicos sobre a configuração de dispositivos HSM para uma elevada disponibilidade, consulte o Portal de Apoio ao Cliente gemalto para os Guias do Administrador e consulte a secção 6.
