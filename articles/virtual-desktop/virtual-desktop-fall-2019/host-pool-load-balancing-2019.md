---
title: Windows Virtual Desktop host pool load-balance - Azure
description: Hospedar métodos de equilíbrio de carga da piscina para um ambiente de ambiente de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: e2bfbd580d0e114cf4a135879340745107183b6a
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82614814"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga do agrupamento de anfitriões

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtuais do Gestor de Recursos Azure. Se está a tentar gerir os objetos virtuais do Azure Resource Manager Windows, introduzidos na atualização da primavera de 2020, consulte [este artigo](../host-pool-load-balancing.md).

O Windows Virtual Desktop suporta dois métodos de equilíbrio de carga. Cada método determina qual o anfitrião da sessão que irá acolher uma sessão de utilizador quando se conecta a um recurso numa piscina hospedeira.

Os seguintes métodos de equilíbrio de carga estão disponíveis no Windows Virtual Desktop:

- O equilíbrio de primeira carga permite-lhe distribuir uniformemente as sessões de utilizador através dos anfitriões da sessão numa piscina de anfitriões.
- O equilíbrio de carga de profundidade permite-lhe saturar um anfitrião de sessão com sessões de utilizador numa piscina de anfitriões. Uma vez que a primeira sessão atinja o limiar limite de sessão, o balanceador de carga direciona quaisquer novas ligações do utilizador para o anfitrião da sessão seguinte na piscina de anfitrião até atingir o seu limite, e assim por diante.

Cada piscina hospedeira só pode configurar um tipo de carregamento específico. No entanto, ambos os métodos de equilíbrio de carga partilham os seguintes comportamentos, independentemente da piscina hospedeira em que estejam:

- Se um utilizador já tiver uma sessão na piscina de anfitrião e estiver a reconectar-se a essa sessão, o equilibrador de carga irá redirecioná-los com sucesso para o anfitrião da sessão com a sessão existente. Este comportamento aplica-se mesmo que a propriedade AllowNewConnections do anfitrião da sessão esteja definida como Falsa.
- Se um utilizador ainda não tiver uma sessão na piscina de anfitrião, então o balanceador de carga não considerará os anfitriões de sessão cuja propriedade AllowNewConnections está definida como Falsa durante o equilíbrio de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de equilíbrio de carga primeiro

O método de equilíbrio de carga primeiro permite-lhe distribuir ligações de utilizador para otimizar este cenário. Este método é ideal para organizações que queiram proporcionar a melhor experiência para os utilizadores conectarem-se ao seu ambiente de ambiente de trabalho virtual em conjunto.

O primeiro método de primeira consulta acolhe anfitriões que permitem novas ligações. Em seguida, o método seleciona o anfitrião da sessão com o menor número de sessões. Se houver um empate, o método seleciona o anfitrião da primeira sessão na consulta.

## <a name="depth-first-load-balancing-method"></a>Método de equilíbrio de carga de profundidade primeiro

O método de balanço de carga de profundidade primeiro permite-lhe saturar um anfitrião de cada vez para otimizar para este cenário. Este método é ideal para organizações conscientes do custo que querem mais controlo granular sobre o número de máquinas virtuais que atribuíram para uma piscina de anfitriões.

O método de primeira consulta de profundidade acolhe anfitriões que permitem novas ligações e não ultrapassaram o limite máximo de sessão. Em seguida, o método seleciona o anfitrião da sessão com o maior número de sessões. Se houver um empate, o método seleciona o anfitrião da primeira sessão na consulta.