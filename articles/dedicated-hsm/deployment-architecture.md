---
title: Arquitetura de implementação - HSM dedicada do Azure | Documentos da Microsoft
description: Considerações de design básico ao utilizar o HSM do Azure dedicada como parte de uma arquitetura de aplicativos
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
ms.openlocfilehash: f078df7677e771d131f15056ac4a54a58a3134bd
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60912295"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Arquitetura de implementação de HSM dedicados do Azure

HSM dedicada do Azure fornece armazenamento de chaves criptográfico no Azure. Ele atende aos requisitos de segurança rigorosas. Os clientes beneficiarão de utilizar o HSM do Azure dedicado se eles:

* Tem de cumprir FIPS 140-2 nível 3 certificação
* Exigir que têm acesso exclusivo para o HSM
* deve ter controle completo dos respetivos dispositivos

Os HSMs são distribuídos em datacenters da Microsoft e podem ser facilmente aprovisionados como um par de dispositivos como a base de uma solução de elevada disponibilidade. Eles também podem ser implementados em várias regiões para uma solução resiliente após desastre. As regiões com o HSM dedicados disponíveis atualmente são:

* EUA Leste
* EUA Leste 2
* EUA Oeste
* EUA Centro-Sul
* Sudeste Asiático
* Ásia Oriental
* Europa do Norte
* Europa Ocidental
* Reino Unido Sul
* Reino Unido Oeste
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Sudeste da Austrália

Cada uma dessas regiões tem racks HSM implementados em dois datacenters independentes ou, pelo menos, duas zonas de disponibilidade independentes. Sudeste asiático tem três zonas de disponibilidade e E.U.A. Leste 2 tem dois. Há um total de oito regiões na Europa, Ásia e EUA que oferecem o serviço de HSM dedicados. Para obter mais informações sobre regiões do Azure, consulte oficial [informações de regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Alguns fatores de design para qualquer solução baseada em HSM dedicados são localização/latência, elevada disponibilidade e suportam para outros aplicativos distribuídos.

## <a name="device-location"></a>Localização do dispositivo

Localização do dispositivo HSM ideal está na proximidade mais próxima para os aplicativos que executam operações criptográficas. Latência de na região e deve ser milissegundos de dígito. Latência em várias regiões pode ser 5 a 10 vezes maior do que isso.

## <a name="high-availability"></a>Elevada disponibilidade

Para assegurar elevada disponibilidade, um cliente tem de utilizar dois dispositivos HSM numa região utilizando a Gemalto software como um par de elevada disponibilidade. Este tipo de implementação garante a disponibilidade de chaves, se um dispositivo único sofrer um problema impedir que esta chave operações de processamento. Ela reduz consideravelmente também os riscos quando efetuar a manutenção de garantia de reparação/assistência como substituição de fonte de alimentação de energia. É importante para um design para levar em conta para qualquer tipo de falha de nível regional. Falhas de nível regionais podem acontecer quando existem desastres naturais, como furacões, inundações ou sismos. Esses tipos de eventos devem ser mitigados ao aprovisionamento de dispositivos HSM noutra região. Dispositivos implementados noutra região podem ser emparelhados através de uma configuração de software da Gemalto. Isso significa que a implementação mínima para uma elevada disponibilidade e a desastres solução resiliente é quatro dispositivos HSM em duas regiões. Redundância local e a redundância em várias regiões podem servir como uma linha de base para adicionar qualquer outra Implantações de dispositivos do HSM para latência, capacidade de suporte ou para cumprir outros requisitos específicos de aplicativo.

## <a name="distributed-application-support"></a>Distribuído de suporte técnico da aplicação

Dispositivos HSM dedicados são normalmente implantados para oferecer suporte a aplicativos que precisam para realizar operações de obtenção de chaves e de armazenamento de chaves. Dispositivos HSM dedicados tem 10 partições para o suporte de aplicativos independentes. Localização do dispositivo deve basear-se uma vista holística de todas as aplicações que precisam de utilizar o serviço.

## <a name="next-steps"></a>Passos Seguintes

Depois de arquitetura de implantação é determinada, a maioria das atividades de configuração para implementar essa arquitetura serão fornecidas pela Gemalto. Isto inclui a configuração do dispositivo, bem como a aplicação de cenários de integração. Para obter mais informações, utilize o [atendimento ao cliente da Gemalto](https://supportportal.gemalto.com/csm/) guias de administração e configuração do portal e a transferência. O site de parceiro da Microsoft tem uma variedade de guias de integração.
Recomenda-se que todos os principais conceitos do serviço, como elevada disponibilidade e segurança, por exemplo, são bem compreendidos antes do aprovisionamento de dispositivos ou de design do aplicativo e de implementação.
Mais tópicos de nível de conceito:

* [Elevada disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitorização](monitoring.md)
