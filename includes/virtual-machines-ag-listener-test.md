---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: d579e7a4fd83c1a0ce335e0b2357dcbafb217398
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66165453"
---
Neste passo, vai testar o serviço de escuta do grupo de disponibilidade, utilizando uma aplicação cliente que está em execução na mesma rede.

Conectividade de cliente tem os seguintes requisitos:

* Ligações de cliente para o serviço de escuta tem de ser provenientes de máquinas que residem num serviço cloud diferente daquele que aloja as réplicas de disponibilidade Always On.
* Se as réplicas Always On estiverem em sub-redes diferentes, os clientes têm de especificar *MultisubnetFailover = True* na cadeia de ligação. Esta condição resulta em tentativas de ligação paralela para réplicas em várias sub-redes. Este cenário inclui uma implementação de entre regiões Always On disponibilidade grupo.

Um exemplo é para ligar ao serviço de escuta, a partir de uma das VMs na mesma rede virtual do Azure (mas não um que aloja uma réplica). Uma forma fácil de concluir este teste é tentar conectar-se o SQL Server Management Studio para o serviço de escuta do grupo de disponibilidade. Outro método simple consiste em executar [SQLCMD.exe](https://technet.microsoft.com/library/ms162773.aspx), da seguinte forma:

    sqlcmd -S "<ListenerName>,<EndpointPort>" -d "<DatabaseName>" -Q "select @@servername, db_name()" -l 15

> [!NOTE]
> Se for o valor de EndpointPort *1433*, não é necessário especificá-lo na chamada. A chamada anterior também pressupõe que o computador cliente está associado ao mesmo domínio e se o chamador foram concedido as permissões na base de dados utilizando a autenticação do Windows.
> 
> 

Ao testar o serviço de escuta, certifique-se de que a ativação pós-falha o grupo de disponibilidade para se certificar de que os clientes podem ligar para o serviço de escuta em ativações pós-falha.

