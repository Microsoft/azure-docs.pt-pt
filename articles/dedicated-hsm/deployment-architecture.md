---
title: Arquitetura de implantação-HSM dedicado do Azure | Microsoft Docs
description: Considerações básicas de design ao usar o HSM dedicado do Azure como parte de uma arquitetura de aplicativo
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: mbaldwin
ms.openlocfilehash: ff86c25de006495e3536f2ff907e1cf40a216f8e
ms.sourcegitcommit: a10074461cf112a00fec7e14ba700435173cd3ef
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/12/2019
ms.locfileid: "73927851"
---
# <a name="azure-dedicated-hsm-deployment-architecture"></a>Arquitetura de implementação do Azure Dedicated HSM

O HSM dedicado do Azure fornece armazenamento de chave de criptografia no Azure. Ele atende aos rigorosos requisitos de segurança. Os clientes se beneficiarão do uso do HSM dedicado do Azure, se:

* Deve atender à certificação FIPS 140-2 nível 3
* Exigir que eles tenham acesso exclusivo ao HSM
* deve ter controle total de seus dispositivos

Os HSMs são distribuídos entre os data centers da Microsoft e podem ser facilmente provisionados como um par de dispositivos como a base de uma solução altamente disponível. Eles também podem ser implantados entre regiões para uma solução resiliente de desastres. As regiões com HSM dedicado disponíveis atualmente são:

* EUA Leste
* EUA Leste 2
* EUA Oeste
* EUA Oeste 2
* EUA Centro-Sul
* Sudeste asiático
* Ásia Oriental
* Europa do Norte
* Europa ocidental
* Reino Unido Sul
* Reino Unido Oeste
* Canadá Central
* Leste do Canadá
* Leste da Austrália
* Sudeste da Austrália

Cada uma dessas regiões tem racks HSM implantados em dois data centers independentes ou pelo menos duas zonas de disponibilidade independentes. O Ásia Oriental do Sul tem três zonas de disponibilidade e o leste dos EUA 2 tem dois. Há um total de oito regiões na Europa, na Ásia e nos EUA que oferecem o serviço HSM dedicado. Para obter mais informações sobre regiões do Azure, consulte as [informações oficiais das regiões do Azure](https://azure.microsoft.com/global-infrastructure/regions/).
Alguns fatores de design para qualquer solução dedicada baseada em HSM são localização/latência, alta disponibilidade e suporte para outros aplicativos distribuídos.

## <a name="device-location"></a>Localização do dispositivo

A localização ideal do dispositivo HSM está mais próxima à proximidade com os aplicativos que executam operações criptográficas. Espera-se que a latência na região seja de milissegundos de dígito único. A latência entre regiões poderia ser de 5 a 10 vezes maior do que isso.

## <a name="high-availability"></a>Elevada disponibilidade

Para obter alta disponibilidade, um cliente deve usar dois dispositivos HSM em uma região configurada usando o software Gemalto como um par de alta disponibilidade. Esse tipo de implantação garante a disponibilidade das chaves se um único dispositivo enfrentar um problema, impedindo-o de processar operações de chave. Ele também reduz significativamente o risco ao executar manutenção de conserto/conserto, como a substituição da fonte de energia. É importante que um design leve em consideração qualquer tipo de falha de nível regional. As falhas de nível regional podem ocorrer quando há desastres naturais, como furacões, inundações ou terremotos. Esses tipos de eventos devem ser mitigados pelo provisionamento de dispositivos HSM em outra região. Dispositivos implantados em outra região podem ser emparelhados por meio da configuração de software Gemalto. Isso significa que a implantação mínima para uma solução de alta disponibilidade e resistente a desastres é de quatro dispositivos HSM em duas regiões. Redundância local e redundância entre regiões podem ser usadas como uma linha de base para adicionar outras implantações de dispositivo HSM para dar suporte à latência, à capacidade ou atender a outros requisitos específicos do aplicativo.

## <a name="distributed-application-support"></a>Suporte a aplicativos distribuídos

Dispositivos HSM dedicados normalmente são implantados para dar suporte a aplicativos que precisam executar operações de armazenamento de chave e de recuperação de chave. Dispositivos HSM dedicados têm 10 partições para suporte independente de aplicativo. O local do dispositivo deve ser baseado em uma exibição holística de todos os aplicativos que precisam usar o serviço.

## <a name="next-steps"></a>Passos seguintes

Depois que a arquitetura de implantação for determinada, a maioria das atividades de configuração para implementar essa arquitetura será fornecida pelo Gemalto. Isso inclui a configuração do dispositivo, bem como os cenários de integração de aplicativos. Para obter mais informações, use o portal de [suporte ao cliente do Gemalto](https://supportportal.gemalto.com/csm/) e baixe os guias de administração e configuração. O site de parceiros da Microsoft tem uma variedade de guias de integração.
É recomendável que todos os principais conceitos do serviço, como alta disponibilidade e segurança, por exemplo, sejam bem compreendidos antes do provisionamento de dispositivos ou design e implantação de aplicativos.
Tópicos de nível de conceito adicionais:

* [Alta disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade](supportability.md)
* [Monitorização](monitoring.md)
