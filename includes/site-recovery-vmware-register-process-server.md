---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 04/28/2019
ms.author: raynew
ms.openlocfilehash: 088cd5447b1f96dbf172b5918c29e4f3293289a6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "67534707"
---
1. Estabeleça uma ligação remota de ambiente de trabalho à máquina que executa o servidor de processo. 
2. Executar cspsconfigtool.exe para iniciar a ferramenta de configuração do servidor do processo de recuperação do site Azure.
    - A ferramenta é lançada automaticamente na primeira vez que entra no servidor de processos.
    - Se não abrir automaticamente, clique no atalho no ambiente de trabalho.

3. No **servidor de configuração FQDN ou IP,** especifique o nome ou endereço IP do servidor de configuração com o qual registar o servidor de processo.
4. Na porta do servidor de **configuração,** certifique-se de que o 443 está especificado. Esta é a porta em que o servidor de configuração ouve pedidos.
5. Na **frase de passe de Ligação,** especifique a frase-passe que especificou quando configura o servidor de configuração. Para encontrar a frase-passe:
    -  No servidor de configuração, navegue para a pasta de instalação de recuperação do site **\home\svssystems\bin\**:
    ```
    cd %ProgramData%\ASR\home\svsystems\bin
    ```
    - Executar o comando abaixo para imprimir a frase-passe atual:
    ```
    genpassphrase.exe -n
    ```

6. Na **Porta de Transferência de Dados,** deixe o valor predefinido a menos que tenha especificado uma porta personalizada.

7. Clique em **Guardar** as definições e registar o servidor de processos.

    
    ![Registe o servidor de processos](./media/site-recovery-vmware-register-process-server/register-ps.png)
