---
title: O que é delegação de sub-rede na rede virtual do Azure?
description: Saiba mais sobre a delegação de sub-rede na rede virtual do Azure
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
ms.sourcegitcommit: 653e9f61b24940561061bd65b2486e232e41ead4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/21/2019
ms.locfileid: "74281340"
---
# <a name="what-is-subnet-delegation"></a>O que é delegação de sub-rede?

A delegação de sub-rede permite designar uma sub-rede específica para um serviço de PaaS do Azure de sua escolha que precisa ser injetada em sua rede virtual. A delegação de sub-rede fornece controle total ao cliente sobre como gerenciar a integração dos serviços do Azure em suas redes virtuais.

Ao delegar uma sub-rede a um serviço do Azure, você permite que esse serviço estabeleça algumas regras básicas de configuração de rede para essa sub-rede, o que ajuda o serviço do Azure a operar suas instâncias de maneira estável. Como resultado, o serviço do Azure pode estabelecer algumas condições de implantação anteriores ou posteriores, como:
- implante o serviço em uma sub-rede compartilhada versus dedicada.
- Adicionar ao serviço um conjunto de diretivas de intenção de rede após a implantação necessário para que o serviço funcione corretamente.

##  <a name="advantages-of-subnet-delegation"></a>Vantagens da delegação de sub-rede

A delegação de uma sub-rede a serviços específicos oferece as seguintes vantagens:

- ajuda a designar uma sub-rede para um ou mais serviços do Azure e gerenciar as instâncias na sub-rede de acordo com os requisitos. Por exemplo, o proprietário da rede virtual pode definir o seguinte para uma sub-rede delegada para gerenciar melhor os recursos e o acesso da seguinte maneira:
    - políticas de tráfego de filtragem de rede com grupos de segurança de rede.
    - políticas de roteamento com rotas definidas pelo usuário.
    - integração de serviços com as configurações de pontos de extremidade de serviço.
- ajuda os serviços injetados a se integrar melhor à rede virtual definindo suas pré-condições de implantações na forma de diretivas de intenção de rede. Isso garante que qualquer ação que possa afetar o funcionamento do serviço injetado possa ser bloqueada em PUT.


## <a name="who-can-delegate"></a>Quem pode delegar?
A delegação de sub-rede é um exercício que os proprietários da rede virtual precisam executar para designar uma das sub-redes para um serviço específico do Azure. O serviço do Azure, por sua vez, implanta as instâncias nessa sub-rede para consumo pelas cargas de trabalho do cliente.

## <a name="impact-of-subnet-delegation-on-your-subnet"></a>Impacto da delegação de sub-rede em sua sub-rede
Cada serviço do Azure define seu próprio modelo de implantação, no qual eles podem definir quais propriedades eles fazem ou não oferecem suporte em uma sub-rede delegada para fins de injeção, como a seguir:
- sub-rede compartilhada com outros serviços do Azure ou conjunto de dimensionamento de VM/máquina virtual na mesma sub-rede ou dá suporte apenas a uma sub-rede dedicada com apenas instâncias desse serviço.
- dá suporte à associação NSG com a sub-rede delegada.
- o oferece suporte a NSG associados à sub-rede delegada também pode ser associado a qualquer outra sub-rede.
- permite a associação da tabela de rotas com a sub-rede delegada.
- permite que a tabela de rotas associada à sub-rede delegada seja associada a qualquer outra sub-rede.
- determina o número mínimo de endereços IP na sub-rede delegada.
- determina o espaço de endereço IP na sub-rede delegada para ser do espaço de endereço IP privado (10.0.0.0/8, 192.168.0.0/16, 172.16.0.0/12).
- determina que a configuração de DNS personalizada tem uma entrada DNS do Azure.

Os serviços injetados também podem adicionar suas próprias políticas da seguinte maneira:
- **Políticas de segurança**: coleção de regras de segurança necessária para que um determinado serviço funcione.
- **Políticas de rota**: coleção de rotas necessárias para que um determinado serviço funcione.

## <a name="what-subnet-delegation-does-not-do"></a>O que a delegação de sub-rede não faz

Os serviços do Azure que estão sendo injetados em uma sub-rede delegada ainda têm o conjunto básico de propriedades que estão disponíveis para sub-redes não delegadas, como:
-  Os serviços do Azure podem injetar instâncias em sub-redes de clientes, mas não podem afetar as cargas de trabalho existentes.
-  As políticas ou rotas que esses serviços aplicam são flexíveis e podem ser substituídas pelo cliente.

## <a name="next-steps"></a>Passos seguintes

- [Delegar uma sub-rede](manage-subnet-delegation.md)
