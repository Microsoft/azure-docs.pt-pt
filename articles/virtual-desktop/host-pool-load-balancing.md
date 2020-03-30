---
title: Windows Virtual Desktop anfitrião de pool balance-balance - Azure
description: Métodos de equilíbrio de carga de piscina anfitrião para um ambiente de ambiente de trabalho virtual windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 193821ed0df09b87f19e45a82ca42026405a0dc4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79127868"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga do agrupamento de anfitriões

O Windows Virtual Desktop suporta dois métodos de equilíbrio de carga. Cada método determina qual o anfitrião da sessão que irá acolher a sessão de um utilizador quando se ligar a um recurso numa piscina de anfitriões.

Os seguintes métodos de equilíbrio de carga estão disponíveis no Windows Virtual Desktop:

- O equilíbrio de carga de primeira largura permite-lhe distribuir as sessões de utilizador uniformemente através dos anfitriões da sessão numa piscina de anfitriões.
- O equilíbrio de carga de primeira profundidade permite-lhe saturar um anfitrião de sessão com sessões de utilizador numa piscina de anfitriões. Uma vez que a primeira sessão atinge o seu limite de sessão, o equilibrador de carga direciona quaisquer novas ligações do utilizador para o anfitrião da próxima sessão na piscina anfitriã até atingir o seu limite, e assim por diante.

Cada piscina hospedeira só pode configurar um tipo de equilíbrio de carga específico. No entanto, ambos os métodos de equilíbrio de carga partilham os seguintes comportamentos, independentemente da piscina de acolhimento em que se encontram:

- Se um utilizador já tiver uma sessão na piscina anfitriã e estiver a reconectar-se com essa sessão, o equilibrista de carga irá redirecioná-los com sucesso para o anfitrião da sessão com a sessão existente. Este comportamento aplica-se mesmo que a propriedade do anfitrião da sessão AllowNewConnections esteja definida como Falsa.
- Se um utilizador ainda não tiver uma sessão na piscina anfitriã, então o equilibrista de carga não considerará os anfitriões da sessão cuja propriedade AllowNewConnections está definida como Falsa durante o equilíbrio de carga.

## <a name="breadth-first-load-balancing-method"></a>Método de equilíbrio de carga de primeira amplitude

O método de equilíbrio de carga de primeira amplitude permite-lhe distribuir as ligações do utilizador para otimizar para este cenário. Este método é ideal para organizações que desejam proporcionar a melhor experiência para os utilizadores conectarem-se ao seu ambiente de trabalho virtual pooled.

A primeira sessão de consultas do método amplitude-primeiro apresenta que permitem novas ligações. O método seleciona então o anfitrião da sessão com o menor número de sessões. Se houver um empate, o método seleciona o anfitrião da primeira sessão na consulta.

## <a name="depth-first-load-balancing-method"></a>Método de equilíbrio de carga de primeira profundidade

O método de equilíbrio de carga de primeira profundidade permite-lhe saturar um anfitrião de uma sessão de cada vez para otimizar para este cenário. Este método é ideal para organizações conscientes de custos que querem um maior controlo granular sobre o número de máquinas virtuais que alocaram para uma piscina hospedeira.

A primeira sessão de consultas de profundidade primeiro método acolhe que permitem novas ligações e não ultrapassaram o seu limite máximo de sessão. O método seleciona então o anfitrião da sessão com o maior número de sessões. Se houver um empate, o método seleciona o anfitrião da primeira sessão na consulta.