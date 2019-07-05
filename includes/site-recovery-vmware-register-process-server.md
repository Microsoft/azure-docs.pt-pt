---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 6cb4dd784dd5a6c72edaff56cf6bcdcd8c579ee7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2019
ms.locfileid: "67534707"
---
1. Estabelece uma ligação de ambiente de trabalho remota do computador que executa o servidor de processos. 
2. Execute cspsconfigtool.exe para iniciar a ferramenta de configuração do servidor de processos de recuperação de Site do Azure.
    - A ferramenta é iniciada automaticamente na primeira vez que iniciar sessão no servidor de processos.
    - Se não abrir automaticamente, clique em seu atalho no ambiente de trabalho.

3. Na **servidor de configuração IP ou FQDN**, especifique o nome ou endereço IP do servidor de configuração com a qual registar o servidor de processos.
4. Na **porta do servidor de configuração**, certifique-se de que a 443 é especificado. Esta é a porta em que o servidor de configuração escuta pedidos.
5. Na **frase de acesso de ligação**, especifique a frase de acesso que especificou quando configurou o servidor de configuração. Para localizar a frase de acesso:
    -  No servidor de configuração, navegue para a pasta de instalação do Site Recovery * *\home\svssystems\bin\** :
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Execute o comando para imprimir a frase de acesso atual abaixo:
    ```
    genpassphrase.exe -n
    ```

6. Na **porta de transferência de dados**, deixe o valor predefinido, a menos que tiver especificado uma porta personalizada.

7. Clique em **guardar** guardar as definições e registar o servidor de processos.

    
    ![Registar o servidor de processos](./media/site-recovery-vmware-register-process-server/register-ps.png)
