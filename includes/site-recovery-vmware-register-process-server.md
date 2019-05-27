---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: cf39baf34096691144181332566cf567ebc02310
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/23/2019
ms.locfileid: "66169997"
---
1. Estabelece uma ligação de ambiente de trabalho remota do computador que executa o servidor de processos. 
2. Execute cspsconfigtool.exe para iniciar a ferramenta de configuração do servidor de processos de recuperação de Site do Azure.
    - A ferramenta é iniciada automaticamente na primeira vez que iniciar sessão no servidor de processos.
    - Se não abrir automaticamente, clique em seu atalho no ambiente de trabalho.

3. Na **servidor de configuração IP ou FQDN**, especifique o nome ou endereço IP do servidor de configuração com a qual registar o servidor de processos.
4. Na **porta do servidor de configuração**, certifique-se de que a 443 é especificado. Esta é a porta em que o servidor de configuração escuta pedidos.
5. Na **frase de acesso de ligação**, especifique a frase de acesso que especificou quando configurou o servidor de configuração. Para localizar a frase de acesso:
    -  No servidor de configuração, navegue para a pasta de instalação do Site Recovery * *\home\svssystems\bin\** . 
    - Execute este comando: **genpassphrase.exe.n**. Isto mostra-lhe a localização da frase de acesso, o que pode, em seguida, observar.

6. Na **porta de transferência de dados**, deixe o valor predefinido, a menos que tiver especificado uma porta personalizada.

7. Clique em **guardar** guardar as definições e registar o servidor de processos.

    
    ![Registar o servidor de processos](./media/site-recovery-vmware-register-process-server/register-ps.png)
