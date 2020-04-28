---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 6a4fbc5ccf7cca9486fe881c069c321017628f20
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67184204"
---
Neste passo, testa o ouvinte do grupo de disponibilidade utilizando uma aplicação de cliente que está a funcionar na mesma rede.

A conectividade do cliente tem os seguintes requisitos:

* As ligações do cliente ao ouvinte devem vir de máquinas que residem num serviço de nuvem diferente daquele que acolhe as réplicas de disponibilidade Always On.
* Se as réplicas Always On estiverem em subnets diferentes, os clientes devem especificar *MultisubnetFailover=True* na cadeia de ligação. Esta condição resulta em tentativas de ligação paralela a réplicas nas várias subredes. Este cenário inclui uma implantação de grupo seletiva sempre em região.

Um exemplo é ligar-se ao ouvinte de um dos VMs na mesma rede virtual Azure (mas não uma que acolhe uma réplica). Uma maneira fácil de completar este teste é tentar ligar o SQL Server Management Studio ao ouvinte do grupo de disponibilidade. Outro método simples é executar [SQLCMD.exe,](https://technet.microsoft.com/library/ms162773.aspx)da seguinte forma:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Se o valor EndpointPort for *1433,* não é necessário especiá-lo na chamada. A chamada anterior também pressupõe que a máquina cliente está unida ao mesmo domínio e que o chamador foi autorizado na base de dados utilizando a autenticação do Windows.
> 
> 

Quando testar o ouvinte, certifique-se de falhar sobre o grupo de disponibilidade para garantir que os clientes podem ligar-se ao ouvinte através de falhas.

