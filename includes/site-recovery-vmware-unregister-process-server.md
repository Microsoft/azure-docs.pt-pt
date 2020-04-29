---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: 00b0c1b1a40ad16db177916c57dba6e9d5a187a7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67184787"
---
Siga os passos para as suas circunstâncias específicas.

### <a name="unregister-a-connected-process-server"></a>Desmarque um servidor de processo conectado

1. Estabeleça uma ligação remota ao servidor de processos como Administrador.
2. No Painel de **Controlo,** programas **abertos > Desinstalar um programa**.
3. Desinstale o programa **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Desinstale o programa **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Depois de os programas nos passos 3 e 4 não estarem instalados, desinstale a **configuração de recuperação do site do Microsoft Azure/dependências**do servidor de processos .

### <a name="unregister-a-disconnected-process-server"></a>Desregistre um servidor de processo desligado

Utilize apenas estes passos se não houver forma de reanimar a máquina na qual o servidor de processo sou instalado.

1. Inscreva-se no servidor de configuração como Administrador.
2. Abra um pedido de comando `%ProgramData%\ASR\home\svsystems\bin`administrativo e navegue até .
3. Execute este comando para obter uma lista de um ou mais servidores de processos.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Não: o número de série do servidor de processos.
    - IP/Nome: O endereço IP e o nome da máquina que executa o servidor de processo.
    - Batimento cardíaco: Último batimento cardíaco da máquina do servidor de processos.
    ![Não registro-cmd](media/site-recovery-vmware-unregister-process-server/Unregister-cmd.PNG)

4. Especifique o número de série do servidor de processo supérno que deseja desregistar.
5. Desregistar um servidor de processo, remova todos os seus detalhes do sistema e apresenta a mensagem:> de nome de servidor não registado com sucesso (endereço IP **do servidor)**

