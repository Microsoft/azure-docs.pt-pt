---
title: O que é a delegação da sub-rede na rede virtual Azure?
description: Conheça a delegação da sub-rede na rede virtual Azure
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
ms.openlocfilehash: 1fbb683754aed5b2a2e6e9c022713b7e87ad9ba9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91329212"
---
# <a name="what-is-subnet-delegation"></a>O que é a delegação da sub-rede?

A delegação da sub-rede permite-lhe designar uma sub-rede específica para um serviço Azure PaaS à sua escolha que precisa de ser injetado na sua rede virtual. A delegação da Subnet fornece total controlo ao cliente sobre a gestão da integração dos serviços Azure nas suas redes virtuais.

Quando delega uma sub-rede para um serviço Azure, permite que esse serviço estabeleça algumas regras básicas de configuração da rede para essa sub-rede, que ajudam o serviço Azure a operar as suas instâncias de forma estável. Como resultado, o serviço Azure pode estabelecer algumas condições de implantação pré ou pós-post, tais como:
- implementar o serviço numa sub-rede partilhada versus dedicada.
- adicionar ao serviço um conjunto de Políticas de Intenções de Rede postar implementação que é necessário para que o serviço funcione corretamente.

##  <a name="advantages-of-subnet-delegation"></a>Vantagens da delegação da sub-rede

Delegar uma sub-rede a serviços específicos fornece as seguintes vantagens:

- ajuda a designar uma sub-rede para um ou mais serviços Azure e gerir as instâncias da sub-rede de acordo com os requisitos. Por exemplo, o proprietário da rede virtual pode definir o seguinte para uma sub-rede delegada para gerir melhor os recursos e o acesso da seguinte forma:
    - políticas de filtragem de rede com grupos de segurança de rede.
    - políticas de encaminhamento com rotas definidas pelo utilizador.
    - integração de serviços com configurações de pontos finais de serviço.
- ajuda os serviços injetados a integrarem-se melhor com a rede virtual, definindo as suas pré-condições de implantação sob a forma de Políticas de Intenções de Rede. Isto garante que quaisquer ações que possam afetar o funcionamento do serviço injetado podem ser bloqueadas no PUT.


## <a name="who-can-delegate"></a>Quem pode delegar?
A delegação da sub-rede é um exercício que os proprietários da rede virtual precisam de realizar para designar uma das sub-redes para um serviço Azure específico. O Serviço Azure, por sua vez, implementa as instâncias nesta sub-rede para consumo pelas cargas de trabalho do cliente.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Impacto da delegação da sub-rede na sua sub-rede
Cada serviço Azure define o seu próprio modelo de implantação, onde podem definir que propriedades fazem ou não suportam numa sub-rede delegada para efeitos de injeção, tais como:
- sub-rede partilhada com outros Serviços Azure ou escala de máquina vm/virtual definida na mesma sub-rede, ou apenas suporta uma sub-rede dedicada com apenas instâncias deste serviço.
- apoia a associação NSG com a sub-rede delegada.
- suporta o NSG associado à sub-rede delegada também pode ser associado a qualquer outra sub-rede.
- permite a associação de tabelas de rota com a sub-rede delegada.
- permite que a tabela de rotas associada à sub-rede delegada seja associada a qualquer outra sub-rede.
- dita o número mínimo de endereços IP na sub-rede delegada.
- dita que o espaço ip address na sub-rede delegada seja do espaço private IP Address (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- dita que a configuração dNS personalizada tem uma entrada Azure DNS.
- exige que a delegação seja removida antes de a sub-rede ou a rede virtual poderem ser eliminadas.

Os serviços injetados também podem adicionar as suas próprias políticas da seguinte forma:
- **Políticas de segurança**: Recolha de regras de segurança necessárias para que um determinado serviço funcione.
- **Políticas de rota**: Recolha de rotas necessárias para um determinado serviço funcionar.

## <a name="what-subnet-delegation-does-not-do"></a>O que a delegação da sub-rede não faz

Os serviços Azure que estão a ser injetados numa sub-rede delegada ainda possuem o conjunto básico de propriedades disponíveis para sub-redes não delegadas, tais como:
-  Os serviços Azure podem injetar casos em sub-redes de clientes, mas não podem afetar as cargas de trabalho existentes.
-  As políticas ou rotas que estes serviços aplicam são flexíveis e podem ser ultrapassadas pelo cliente.

## <a name="next-steps"></a>Passos seguintes

- [Delegar uma sub-rede](manage-subnet-delegation.md)
