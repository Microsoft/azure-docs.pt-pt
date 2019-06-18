---
title: Elevada disponibilidade - HSM dedicada do Azure | Documentos da Microsoft
description: Exemplo de elevada disponibilidade de HSM dedicados do Azure e considerações básicas
services: dedicated-hsm
author: barclayn
manager: barbkess
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: barclayn
ms.openlocfilehash: 784a6821502a0e02a74d619047604ffe47df7e97
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60914176"
---
# <a name="azure-dedicated-hsm-high-availability"></a>Disponibilidade elevada de HSM dedicados do Azure

HSM dedicada do Azure é underpinned por data Centers de elevada disponibilidade da Microsoft. No entanto, qualquer datacenter de elevada disponibilidade é vulnerável ao falhas localizadas e, em circunstâncias extremas, falhas regionais. A Microsoft implementa dispositivos HSM em datacenters diferentes dentro de uma região para garantir que o aprovisionamento de vários dispositivos não conduz a esses dispositivos partilha um bastidor único. Pode ser alcançado um nível adicional de elevada disponibilidade emparelhando estas HSMs centros de dados numa região a utilizar a funcionalidade de Gemalto HA grupo. Também é possível para dispositivos de par em várias regiões para ativação pós-falha numa situação de recuperação após desastre de endereços. Com esta configuração em várias camadas de elevada disponibilidade, qualquer falha de dispositivo será automaticamente resolvida para manter as aplicações a funcionar. Todos os data Centers também têm componentes de reserva dispositivos e componentes no local para que todos os dispositivos com falha podem ser substituído no momento oportuno.

## <a name="high-availability-example"></a>Exemplo de elevada disponibilidade

Obter informações sobre como configurar dispositivos HSM para elevada disponibilidade ao nível do software são o "Gemalto Luna rede HSM administração guia'. Este documento está disponível na [Portal de suporte ao cliente da Gemalto](https://supportportal.gemalto.com/csm/).

O diagrama seguinte mostra uma arquitetura de elevada disponibilidade. Ele usa vários dispositivos na região e vários dispositivos emparelhados numa região separado. Esta arquitetura utiliza um mínimo de quatro dispositivos HSM e componentes de rede virtual.

![Diagrama de elevada disponibilidade](media/high-availability/high-availability.png)

## <a name="next-steps"></a>Passos Seguintes

Recomenda-se que todos os principais conceitos do serviço, como elevada disponibilidade e segurança, são bem compreendidos antes de aprovisionamento de dispositivos e de design do aplicativo ou de implementação.
Mais tópicos de nível de conceito:

* [Arquitetura de implantação](deployment-architecture.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitorização](monitoring.md)

Para obter detalhes específicos sobre como configurar dispositivos HSM para elevada disponibilidade, consulte o Portal de suporte de cliente da Gemalto para os guias de administrador e consulte a secção 6.
