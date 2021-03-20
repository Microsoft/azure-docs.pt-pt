---
title: Windows Virtual Desktop (clássico) anfitrião de piscina de carga - Azure
description: Hospedar métodos de equilíbrio de carga da piscina para um ambiente de ambiente de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 940863b983b00dbb3c4af590d75868665372f818
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "88002301"
---
# <a name="host-pool-load-balancing-methods-in-windows-virtual-desktop-classic"></a>Métodos de equilíbrio de carga de piscina de anfitrião no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../host-pool-load-balancing.md).

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