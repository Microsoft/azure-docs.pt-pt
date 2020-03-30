---
title: O que é a delegação de sub-rede na rede virtual Azure?
description: Conheça a delegação de subnet na rede virtual azure
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/20/2019
ms.author: kumud
ms.openlocfilehash: b33ff808b802b6848e2d5debaf515a73bf21a1bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74281340"
---
# <a name="what-is-subnet-delegation"></a>O que é a delegação da sub-rede?

A delegação subnet permite-lhe designar uma subnet específica para um serviço Azure PaaS à sua escolha que precisa de ser injetado na sua rede virtual. A delegação da Subnet fornece total controlo ao cliente sobre a gestão da integração dos serviços Azure nas suas redes virtuais.

Ao delegar uma subnet a um serviço Azure, permite que esse serviço estabeleça algumas regras básicas de configuração da rede para essa sub-rede, que ajudam o serviço Azure a operar as suas instâncias de forma estável. Como resultado, o serviço Azure pode estabelecer algumas condições de implantação pré ou pós, tais como:
- implementar o serviço numa subnet partilhada versus dedicada.
- adicione ao serviço um conjunto de Políticas de Intenções de Rede pós implementação que é necessário para que o serviço funcione corretamente.

##  <a name="advantages-of-subnet-delegation"></a>Vantagens da delegação da sub-rede

A delegação de uma sub-rede a serviços específicos proporciona as seguintes vantagens:

- ajuda a designar uma sub-rede para um ou mais serviços Azure e gerir as instâncias na subrede de acordo com os requisitos. Por exemplo, o proprietário da rede virtual pode definir o seguinte para uma subnet delegada para gerir melhor os recursos e aceder da seguinte forma:
    - rede filtrando políticas de tráfego com grupos de segurança da rede.
    - políticas de encaminhamento com rotas definidas pelo utilizador.
    - integração de serviços com configurações de pontos finais de serviço.
- ajuda os serviços injetados a integrarem-se melhor com a rede virtual, definindo as suas pré-condições de implantação sob a forma de Políticas de Intenção de Rede. Isto garante que quaisquer ações que possam afetar o funcionamento do serviço injetado podem ser bloqueadas no PUT.


## <a name="who-can-delegate"></a>Quem pode delegar?
A delegação da Subnet é um exercício que os proprietários de redes virtuais precisam de realizar para designar uma das subredes para um serviço Azure específico. O Serviço Azure, por sua vez, implanta as instâncias nesta subnet para consumo pelas cargas de trabalho dos clientes.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Impacto da delegação de sub-rede na sua subnet
Cada serviço Azure define o seu próprio modelo de implantação, onde podem definir quais as propriedades que fazem ou não suportam numa subnet delegada para fins de injeção, tais como:
- subnet compartilhado com outros Serviços Azure ou VM/ escala de máquina virtual definida na mesma subnet, ou apenas suporta uma subrede dedicada com apenas instâncias deste serviço nele.
- apoia a associação NSG com a subnet delegada.
- os suportes NSG associados à sub-rede delegada também podem ser associados a qualquer outra subrede.
- permite a associação de tabelas de rotas com a subnet delegada.
- permite que a tabela de rotas associada à sub-rede delegada seja associada a qualquer outra sub-rede.
- dita o número mínimo de endereços IP na sub-rede delegada.
- dita o espaço IP Address na subnet delegada a partir do espaço de endereçoip privado (10.0.0.0.0/8, 192.168.0.0/16, 172.16.0.0.0/12).
- dita que a configuração personalizada do DNS tem uma entrada DNS Azure.

Os serviços injetados também podem adicionar as suas próprias políticas da seguinte forma:
- **Políticas**de segurança : Recolha de regras de segurança necessárias para um determinado serviço para funcionar.
- **Políticas de rota**: Recolha de rotas necessárias para um determinado serviço para funcionar.

## <a name="what-subnet-delegation-does-not-do"></a>O que a delegação de subnetnão faz

Os serviços Azure que estão a ser injetados numa subnet delegada ainda têm o conjunto básico de imóveis disponíveis para subredes não delegadas, tais como:
-  Os serviços Azure podem injetar instâncias nas subredes dos clientes, mas não podem afetar as cargas de trabalho existentes.
-  As políticas ou rotas que estes serviços aplicam são flexíveis e podem ser ultrapassadas pelo cliente.

## <a name="next-steps"></a>Passos seguintes

- [Delegar uma sub-rede](manage-subnet-delegation.md)
