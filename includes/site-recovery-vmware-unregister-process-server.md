---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67184787"
---
Siga os passos para suas circunstâncias específicas.

### <a name="unregister-a-connected-process-server"></a>Anular o registo de um servidor de processos ligados

1. Estabelece uma ligação remota ao servidor de processos como administrador.
2. Na **painel de controlo**, abra **programas > desinstalar um programa**.
3. Desinstalar o programa **Microsoft Azure Site Recovery Mobility/principal de serviço de destino Server**.
4. Desinstalar o programa **o servidor de configuração/processos do Microsoft Azure Site Recovery**.
5. Depois dos programas nos passos 3 e 4 são desinstalados, desinstale **Microsoft Azure Site Recovery processo de configuração/dependências do servidor**.

### <a name="unregister-a-disconnected-process-server"></a>Anular o registo de um servidor de processos desligado

Só deve Utilize estes passos se não existir nenhuma forma de reativar o computador no qual o servidor de processos está instalado.

1. Inicie sessão no servidor de configuração como administrador.
2. Abra uma linha de comandos administrativa e procure `%ProgramData%\ASR\home\svsystems\bin`.
3. Execute este comando para obter uma lista de um ou mais servidores de processos.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Não: o número de série do servidor de processo.
    - Nome/IP: O endereço IP e o nome do computador com o servidor de processos.
    - Heartbeat: Último heartbeat a partir da máquina do servidor de processo.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Especifique o número de série do servidor de processos que pretende anular o registo.
5. Anular o registo de um servidor de processos de remover todos os detalhes do sistema e apresenta a mensagem: **Anulado com êxito o nome do servidor > (endereço de IP de servidor)**

