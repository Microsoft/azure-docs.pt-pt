---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 03/11/2019
ms.author: ramamill
ms.openlocfilehash: 0d090f43b69b42a07f1c8949d1662e8e720f3cf4
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "57908540"
---
Os passos para anular o registo de um servidor de processos difere consoante o estado de ligação com o Servidor de Configuração.

### <a name="unregister-a-process-server-that-is-in-a-connected-state"></a>Anule o registo de um servidor de processos que está num estado ligado

1. Inicie sessão no servidor de processos como Administrador.
2. Iniciar o **painel de controlo** e abra **programas > desinstalar um programa**.
3. Desinstalar um programa designado **Microsoft Azure Site Recovery Mobility/principal de serviço de destino Server**.
4. Desinstalar um programa designado **o servidor de configuração/processos do Microsoft Azure Site Recovery**.
5. Quando os programas nos passos 3 e 4 são desinstalados, pode desinstalar **Microsoft Azure Site Recovery processo de configuração/dependências do servidor**.

### <a name="unregister-a-process-server-that-is-in-a-disconnected-state"></a>Anule o registo de um servidor de processos que está num estado desligado

> [!WARNING]
> Utilize o procedimento abaixo se não existir nenhuma forma de reativar a máquina virtual em que o servidor de processos estava instalado.

1. Inicie sessão no seu servidor de configuração como administrador.
2. Abra uma linha de comandos Administrativa e procure o diretório `%ProgramData%\ASR\home\svsystems\bin`.
3. Agora, execute o comando.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
4. O comando acima irá fornecer a lista de servidores de processo (pode ser mais do que um, em caso de entradas duplicadas) com serial number(S.No), o endereço IP (IP), o nome da VM em que servidor de processos é implementado (nome), atingir coração da VM (Heartbeat), conforme mostrado abaixo.
    ![Unregister-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)
5. Agora, introduza o número de série do servidor de processos que pretende anular o registo.
6. Isto irá remover os detalhes do servidor de processos do sistema e irá apresentar a mensagem: **Anulado com êxito o nome do servidor > (endereço de IP de servidor)**

