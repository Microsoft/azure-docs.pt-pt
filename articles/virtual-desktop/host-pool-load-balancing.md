---
title: Windows Virtual Desktop Preview conjunto balanceamento de carga métodos de host - Azure
description: Conjunto balanceamento de carga métodos de Host para um ambiente de pré-visualização de ambiente de Trabalho Virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 8b18224339654c067d8ab9b543fa49a9c7d55ddd
ms.sourcegitcommit: 81fa781f907405c215073c4e0441f9952fe80fe5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/25/2019
ms.locfileid: "58400187"
---
# <a name="host-pool-load-balancing-methods"></a>Métodos de balanceamento de carga de agrupamento do anfitrião

Windows Virtual Desktop Preview suporta dois métodos de balanceamento de carga. Cada método determina qual host de sessão irá alojar a sessão de um utilizador quando se ligam a um recurso de um conjunto de anfitrião.

Os seguintes métodos de balanceamento de carga estão disponíveis na área de Trabalho Virtual do Windows:

- Balanceamento de carga da amplitude em primeiro lugar, permite-lhe distribuir uniformemente sessões de utilizador entre os hosts de sessão de um conjunto de anfitrião.
- Balanceamento de carga de profundidade em primeiro lugar, saturar um host de sessão com sessões de utilizador de um conjunto de anfitrião. Assim que a primeira sessão atinge o limiar de limite de sessão, o Balanceador de carga direciona quaisquer novas ligações de utilizador para o anfitrião de sessão seguinte no agrupamento de host até atingir seu limite e assim por diante.

Cada pool de anfitrião só pode configurar um tipo de balanceamento de carga específico ao mesmo. No entanto, o balanceamento de carga métodos compartilham os seguintes comportamentos, não importa a alojam o conjunto estão na:

- Se um utilizador já tem uma sessão do conjunto de host e está a ligar novamente para a sessão, o Balanceador de carga irá redirecioná-los com êxito para o anfitrião de sessão com a sessão existente. Este comportamento aplica-se mesmo que esse anfitrião de sessões AllowNewConnections estiver definida como False.
- Se um utilizador já não tem uma sessão do conjunto de anfitrião, o Balanceador de carga não considere cuja propriedade AllowNewConnections é definida como False durante o balanceamento de carga de hosts de sessão.

## <a name="breadth-first-load-balancing-method"></a>Método de balanceamento de carga da amplitude em primeiro lugar

O método de balanceamento de carga da amplitude em primeiro lugar, permite que distribua as ligações de utilizador para otimizar para este cenário. Este método é ideal para as organizações que pretendem proporcionar a melhor experiência para os utilizadores a ligar ao seu ambiente de área de trabalho virtual agrupado.

O método de extensão em primeiro lugar consultará primeiro as hosts de sessão que permitem ligações do novo. O método, em seguida, seleciona o host de sessão com o menor número de sessões. Se houver um empate, o método seleciona o host de sessão primeiro na consulta.

## <a name="depth-first-load-balancing-method"></a>Começando pelo método de balanceamento de carga

O método de balanceamento de carga de profundidade de primeiro permite-lhe saturar um anfitrião de sessões em simultâneo para otimizar para este cenário. Este método é ideal para organizações de econômico que desejam controle mais granular sobre o número de máquinas virtuais que alocou para um conjunto de anfitrião.

O método começando pelo primeiro consulta hosts de sessão que permitem novas conexões e não passaram acima do respetivo limite máximo da sessão. O método, em seguida, seleciona o anfitrião de sessões com maior número de sessões. Se houver um empate, o método seleciona o host de sessão primeiro na consulta.