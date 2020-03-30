---
title: Arquitetura de implantação - Azure Dedicado HSM [ Microsoft Docs
description: Considerações básicas de design ao utilizar o Azure Dedicado HSM como parte de uma arquitetura de aplicação
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 02/05/2020
ms.author: mbaldwin
ms.openlocfilehash: 89e3bf95a6b048e5e97cfb151ef9302b70eac1c9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77048559"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Arquitetura de implementação do Azure Dedicated HSM

O Azure Dedicado HSM fornece armazenamento de chaves criptográficas em Azure. Cumpre rigorosos requisitos de segurança. Os clientes beneficiarão da utilização de HSM dedicado ao Azure se:

* Deve cumprir a certificação FIPS 140-2 Nível 3
* Exigir que tenham acesso exclusivo ao HSM
* deve ter controlo total dos seus dispositivos

Os HSMs são distribuídos pelos centros de dados da Microsoft e podem ser facilmente provisionados como um par de dispositivos como base de uma solução altamente disponível. Podem também ser implantados em todas as regiões para uma solução resiliente a catástrofes. As regiões com HSM dedicado disponível atualmente são:

* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Oeste
* E.U.A. Centro-Sul
* Ásia Sudeste
* Ásia Leste
* Índia Central
* Índia do Sul
* Leste do Japão
* Oeste do Japão
* Europa do Norte
* Europa ocidental
* Sul do Reino Unido
* Oeste do Reino Unido
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Austrália Sudeste

Cada uma destas regiões tem racks HSM implantados em dois centros de dados independentes ou pelo menos duas zonas de disponibilidade independentes. O Sudeste Asiático tem três zonas de disponibilidade e o Leste dos EUA 2 tem duas. Há um total de oito regiões em toda a Europa, Ásia e EUA que oferecem o serviço Dedicado HSM. Para obter mais informações sobre as regiões de Azure, consulte as regiões oficiais de [Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
Alguns fatores de design para qualquer solução baseada em HSM dedicado são localização/latência, alta disponibilidade e suporte para outras aplicações distribuídas.

## <a name="device-location"></a>Localização do dispositivo

A localização ideal do dispositivo HSM está mais próxima das aplicações que realizam operações criptográficas. Espera-se que a latência na região seja milissegundos de um dígito. A latência transversal pode ser 5 a 10 vezes maior do que esta.

## <a name="high-availability"></a>Elevada disponibilidade

Para obter uma elevada disponibilidade, um cliente deve usar dois dispositivos HSM numa região que são configurados usando o software Gemalto como um par de alta disponibilidade. Este tipo de implementação garante a disponibilidade de chaves se um único dispositivo tiver um problema que o impeça de processar operações-chave. Também reduz significativamente o risco ao realizar a manutenção de rutura/correção, como a substituição da alimentação. É importante que um desenho seja responsável por qualquer tipo de falha a nível regional. Falhas a nível regional podem ocorrer quando há desastres naturais, como furacões, inundações ou terramotos. Este tipo de eventos deve ser atenuado através do fornecimento de dispositivos HSM noutra região. Os dispositivos implantados noutra região podem ser emparelhados através da configuração do software Gemalto. Isto significa que a implantação mínima para uma solução altamente disponível e resiliente a desastres é de quatro dispositivos HSM em duas regiões. O despedimento local e o despedimento em todas as regiões podem ser usados como base para adicionar mais implementações de dispositivos HSM para suportar a latência, capacidade ou para satisfazer outros requisitos específicos da aplicação.

## <a name="distributed-application-support"></a>Suporte de aplicação distribuído

Os dispositivos HSM dedicados são normalmente implantados em suporte a aplicações que precisam de realizar operações de armazenamento chave e de recuperação chave. Os dispositivos HSM dedicados têm 10 divisórias para suporte independente à aplicação. A localização do dispositivo deve basear-se numa visão holística de todas as aplicações que necessitem de utilizar o serviço.

## <a name="next-steps"></a>Passos seguintes

Uma vez determinada a arquitetura de implantação, a maioria das atividades de configuração para implementar essa arquitetura será fornecida pela Gemalto. Isto inclui a configuração do dispositivo, bem como cenários de integração de aplicações. Para mais informações, utilize o portal de apoio ao [cliente Gemalto](https://supportportal.gemalto.com/csm/) e descarregue guias de administração e configuração. O site parceiro da Microsoft tem uma variedade de guias de integração.
Recomenda-se que todos os conceitos-chave do serviço, como a elevada disponibilidade e segurança, por exemplo, sejam bem compreendidos antes do fornecimento de dispositivos ou da conceção e implementação de aplicações.
Outros tópicos de nível de conceito:

* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
