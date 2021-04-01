---
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: ramamill
ms.openlocfilehash: bff9457220efbb6a6b318550a5b2dd9af4ae230f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96019203"
---
Siga os passos para as suas circunstâncias específicas.

### <a name="unregister-a-connected-process-server"></a>Desagregar um servidor de processo conectado

1. Estabeleça uma ligação remota ao servidor de processo como Administrador.
2. No **Painel de Controlo,** abra **os programas > Desinstale um programa**.
3. Desinstalar o programa **Microsoft Azure Site Recovery Mobility Service/Master Target Server**.
4. Desinstalar o programa **Microsoft Azure Site Recovery Configuration/Process Server**.
5. Depois de desinstalados os programas nos passos 3 e 4, desinstale **a Configuração de Recuperação do Site do Microsoft Azure/Dependências do Servidor de Processos**.

### <a name="unregister-a-disconnected-process-server"></a>Não registre um servidor de processo desligado

Utilize apenas estes passos se não houver forma de reanimar a máquina em que o servidor de processo está instalado.

1. Inscreva-se no servidor de configuração como Administrador.
2. Abra um pedido de comando administrativo e navegue para `%ProgramData%\ASR\home\svsystems\bin` .
3. Executar este comando para obter uma lista de um ou mais servidores de processo.

    ```
    perl Unregister-ASRComponent.pl -IPAddress <IP_of_Process_Server> -Component PS
    ```
    - S. Não: o número de série do servidor de processo.
    - IP/Nome: O endereço IP e o nome da máquina que executa o servidor de processo.
    - Batimento cardíaco: Último batimento cardíaco da máquina do servidor de processo.
    ! [A screenshot mostra uma exibição simples de texto de informações sobre os seus servidores de processo e o texto Por favor escolha um dos servidores acima para des-registar. (media/site-recovery-vmware-unregister-process-process-server/Unregister-cmd.PNG)

4. Especifique o número de série do servidor de processo que pretende não registar.
5. Desregralar um servidor de processo remove todos os seus dados do sistema e exibe a mensagem: **> com o nome do servidor não registado com sucesso (endereço IP-servidor)**

