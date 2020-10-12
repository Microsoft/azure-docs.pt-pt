---
title: Windows Virtual Desktop host pool load-balance - Azure
description: Saiba mais sobre os métodos de equilíbrio de carga da piscina do anfitrião para um ambiente de ambiente de trabalho virtual do Windows.
author: Heidilohr
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 2b977d64dea1cef3b8142758e57d91e92e5bcc02
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89461124"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga do agrupamento de anfitriões

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop com objetos de ambiente de trabalho virtuais do Windows, gestor de recursos do Azure. Se estiver a utilizar o Windows Virtual Desktop (clássico) sem objetos Azure Resource Manager, consulte [este artigo](./virtual-desktop-fall-2019/host-pool-load-balancing-2019.md).

O Windows Virtual Desktop suporta dois métodos de equilíbrio de carga. Cada método determina qual o anfitrião da sessão que irá acolher uma sessão de utilizador quando se conecta a um recurso numa piscina hospedeira.

Os seguintes métodos de equilíbrio de carga estão disponíveis no Windows Virtual Desktop:

- O equilíbrio de primeira carga permite-lhe distribuir uniformemente as sessões de utilizador através dos anfitriões da sessão numa piscina de anfitriões.
- O equilíbrio de carga de profundidade permite-lhe saturar um anfitrião de sessão com sessões de utilizador numa piscina de anfitriões. Uma vez que a primeira sessão atinja o limiar limite de sessão, o balanceador de carga direciona quaisquer novas ligações do utilizador para o anfitrião da sessão seguinte na piscina de anfitrião até atingir o seu limite, e assim por diante.

Cada piscina hospedeira só pode configurar um tipo de carregamento específico. No entanto, ambos os métodos de equilíbrio de carga partilham os seguintes comportamentos, independentemente da piscina hospedeira em que estejam:

- Se um utilizador já tiver uma sessão na piscina de anfitrião e estiver a reconectar-se a essa sessão, o equilibrador de carga irá redirecioná-los com sucesso para o anfitrião da sessão com a sessão existente. Este comportamento aplica-se mesmo que a propriedade AllowNewConnections do anfitrião da sessão esteja definida como Falsa.
- Se um utilizador ainda não tiver uma sessão na piscina de anfitrião, então o balanceador de carga não considerará os anfitriões de sessão cuja propriedade AllowNewConnections está definida como Falsa durante o equilíbrio de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de equilíbrio de carga primeiro

O método de equilíbrio de carga primeiro permite-lhe distribuir ligações de utilizador para otimizar este cenário. Este método é ideal para organizações que queiram proporcionar a melhor experiência para os utilizadores conectarem-se ao seu ambiente de ambiente de trabalho virtual em conjunto.

O primeiro método de primeira consulta acolhe anfitriões que permitem novas ligações. Em seguida, o método seleciona um anfitrião de sessão aleatoriamente a partir de metade do conjunto de anfitriões de sessão com o menor número de sessões. Por exemplo, se houver nove máquinas com 11, 12, 13, 14, 15, 16, 17, 18 e 19 sessões, uma nova sessão que cria não vai automaticamente para a primeira máquina. Em vez disso, pode ir a qualquer uma das cinco primeiras máquinas com o menor número de sessões (11, 12, 13, 14, 15).

## <a name="depth-first-load-balancing-method"></a>Método de equilíbrio de carga de profundidade primeiro

O método de balanço de carga de profundidade primeiro permite-lhe saturar um anfitrião de cada vez para otimizar para este cenário. Este método é ideal para organizações conscientes do custo que querem mais controlo granular sobre o número de máquinas virtuais que atribuíram para uma piscina de anfitriões.

O método de primeira consulta de profundidade acolhe anfitriões que permitem novas ligações e não ultrapassaram o limite máximo de sessão. Em seguida, o método seleciona o anfitrião da sessão com o maior número de sessões. Se houver um empate, o método seleciona o anfitrião da primeira sessão na consulta.