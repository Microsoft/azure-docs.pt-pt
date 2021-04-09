---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96008492"
---
1. Estabelecer uma ligação de ambiente de trabalho remoto à máquina que executa o servidor de processo. 
2. Executar cspsconfigtool.exe para iniciar a ferramenta de configuração do Servidor de Recuperação do Processo de Recuperação do Local Azure.
    - A ferramenta é lançada automaticamente na primeira vez que iniciar sôs no servidor de processo.
    - Se não abrir automaticamente, clique no atalho no ambiente de trabalho.

3. No **servidor de configuração FQDN ou IP**, especifique o nome ou endereço IP do servidor de configuração com o qual registar o servidor de processo.
4. Na **porta do servidor de configuração,** certifique-se de que o 443 está especificado. Esta é a porta na qual o servidor de configuração ouve pedidos.
5. Em **Conexão Passphrase**, especifique a frase de passagem que especificou quando configurar o servidor de configuração. Para encontrar a frase de passagem:
    -  No servidor de configuração, navegue para a pasta de instalação de recuperação do local **\home\svssystems\bin \**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Executar o comando abaixo para imprimir a frase de passagem atual:
    ```
    genpassphrase.exe -n
    ```

6. Na **Porta de Transferência de Dados,** deixe o valor predefinido a menos que tenha especificado uma porta personalizada.

7. Clique **em Guardar** as definições e registar o servidor de processo.

    
    ![Registar o servidor de processo](./media/site-recovery-vmware-register-process-server/register-ps.png)
