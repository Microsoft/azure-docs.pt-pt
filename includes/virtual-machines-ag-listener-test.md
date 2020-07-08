---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2689e81b089147dfc913fb119e0a499d60574b60
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050311"
---
Neste passo, você testa o ouvinte do grupo de disponibilidade usando uma aplicação de cliente que está executando na mesma rede.

A conectividade do cliente tem os seguintes requisitos:

* As ligações do cliente ao ouvinte devem vir de máquinas que residem num serviço de nuvem diferente daquele que acolhe as réplicas de disponibilidade Always On.
* Se as réplicas Always On estiverem em sub-redes diferentes, os clientes devem especificar *MultisubnetFailover=True* na cadeia de ligação. Esta condição resulta em tentativas de ligação paralelas a réplicas nas várias sub-redes. Este cenário inclui uma implantação de grupos de disponibilidade inter-região.

Um exemplo é ligar ao ouvinte de um dos VMs na mesma rede virtual Azure (mas não um que acolhe uma réplica). Uma forma fácil de completar este teste é tentar ligar o SQL Server Management Studio ao ouvinte do grupo de disponibilidade. Outro método simples é executar [SQLCMD.exe, ](https://technet.microsoft.com/library/ms162773.aspx)da seguinte forma:

```console
sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15
```

> [!NOTE]
> Se o valor EndpointPort for *de 1433,* não é obrigado a especificá-lo na chamada. A chamada anterior também pressupõe que a máquina do cliente está unida ao mesmo domínio e que o chamador recebeu permissões na base de dados utilizando a autenticação do Windows.
> 
> 

Ao testar o ouvinte, certifique-se de que falha sobre o grupo de disponibilidade para se certificar de que os clientes podem ligar-se ao ouvinte através de falhas.

