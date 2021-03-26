---
title: Arquitetura de implantação - Azure Dedicado HSM | Microsoft Docs
description: Considerações básicas de design ao usar o HSM dedicado Azure como parte de uma arquitetura de aplicações
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: c454b2e4df7a9ce5fadd33386e5bb413b503c6e4
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105608430"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Arquitetura de implementação do Azure Dedicated HSM

A Azure Dedicado HSM fornece armazenamento de chaves criptográficas em Azure. Cumpre os rigorosos requisitos de segurança. Os clientes beneficiarão da utilização do HSM dedicado Azure se:

* Deve cumprir certificação [FIPS 140-2 Nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final)
* Exijam que tenham acesso exclusivo ao HSM
* devem ter controlo total dos seus dispositivos

Os HSMs são distribuídos pelos centros de dados da Microsoft e podem ser facilmente a provisionados como um par de dispositivos como base de uma solução altamente disponível. Podem também ser implantados em todas as regiões para uma solução resiliente para catástrofes. As regiões com HSM dedicado disponível atualmente podem ser verificadas através da [página Produtos por Região.](https://azure.microsoft.com/global-infrastructure/services/?products=azure-dedicated-hsm) 

* E.U.A. Leste
* E.U.A. Leste 2
* E.U.A. Oeste
* E.U.A. Oeste 2
* E.U.A. Centro-Sul
* Sudeste Asiático
* Ásia Leste
* Índia Central
* Índia do Sul
* Leste do Japão
* Oeste do Japão
* Europa do Norte
* Europa Ocidental
* Sul do Reino Unido
* Oeste do Reino Unido
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Austrália Sudeste
* Suíça Norte
* Suíça Oeste
* US Gov - Virginia
* US Gov - Texas

Cada uma destas regiões tem racks HSM implantados em dois centros de dados independentes ou pelo menos duas zonas de disponibilidade independentes. O Sudeste Asiático tem três zonas de disponibilidade e o Leste dos EUA 2 tem duas. Há um total de vinte e três regiões em toda a Europa, Ásia e América do Norte que oferecem o serviço dedicado ao HSM. Para obter mais informações sobre as regiões de Azure, consulte as informações oficiais [sobre as regiões de Azure.](https://azure.microsoft.com/global-infrastructure/regions/)
Alguns fatores de design para qualquer solução dedicada baseada em HSM são localização/latência, alta disponibilidade e suporte para outras aplicações distribuídas.

## <a name="device-location"></a>Localização do dispositivo

A localização ideal do dispositivo HSM está mais próxima das aplicações que realizam operações criptográficas. Espera-se que a latência na região seja de milissegundos de um dígito. A latência entre regiões pode ser 5 a 10 vezes maior do que esta.

## <a name="high-availability"></a>Elevada disponibilidade

Para obter uma elevada disponibilidade, um cliente deve usar dois dispositivos HSM numa região configurada usando o software Thales como um par de alta disponibilidade. Este tipo de implementação garante a disponibilidade de chaves se um único dispositivo tiver um problema que o impeça de processar operações-chave. Também reduz significativamente o risco ao efetuar uma manutenção de break/fix, como a substituição da alimentação. É importante que um desenho responsável por qualquer tipo de falha a nível regional. Falhas de nível regional podem ocorrer quando há desastres naturais, como furacões, inundações ou terramotos. Este tipo de eventos deve ser atenuado através do fornecimento de dispositivos HSM noutra região. Os dispositivos implantados noutra região podem ser emparelhados através da configuração do software Thales. Isto significa que a implantação mínima para uma solução altamente disponível e resiliente a catástrofes são quatro dispositivos HSM em duas regiões. O despedimento local e o despedimento em todas as regiões podem ser usados como base para adicionar quaisquer outras implementações de dispositivos HSM para apoiar a latência, capacidade ou para satisfazer outros requisitos específicos da aplicação.

## <a name="distributed-application-support"></a>Suporte de aplicação distribuído

Os dispositivos dedicados do HSM são normalmente implantados em suporte a aplicações que precisam de realizar operações de armazenamento de chaves e de recuperação de chaves. Os dispositivos HSM dedicados têm 10 divisórias para suporte independente de aplicações. A localização do dispositivo deve basear-se numa visão holística de todas as aplicações que necessitem de utilizar o serviço.

## <a name="next-steps"></a>Passos seguintes

Uma vez determinada a arquitetura de implantação, a maioria das atividades de configuração para implementar essa arquitetura será fornecida pelo Thales. Isto inclui a configuração do dispositivo, bem como cenários de integração de aplicações. Para mais informações, utilize o portal [de suporte ao cliente Thales](https://supportportal.thalesgroup.com/csm) e descarregue guias de administração e configuração. O site parceiro da Microsoft tem uma variedade de guias de integração.
Recomenda-se que todos os conceitos-chave do serviço, como a elevada disponibilidade e segurança, por exemplo, sejam bem compreendidos antes do fornecimento de dispositivos ou design e implementação de aplicações.
Outros tópicos de nível de conceito:

* [Alta Disponibilidade](high-availability.md)
* [Segurança Física](physical-security.md)
* [Rede](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
