---
title: Adicione um script a um plano de recuperação na Recuperação do Local de Azure
description: Saiba como adicionar um script VMM a um plano de recuperação para a recuperação de desastres de VMs Hiper-V em nuvens VMM.
author: Sharmistha-Rai
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sharrai
ms.openlocfilehash: 3217c30737a133c1c1092fc4a8a8caaa0338e980
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "89425879"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Adicione um script VMM a um plano de recuperação

Este artigo descreve como criar um script do System Center Virtual Machine Manager (VMM) e adicioná-lo a um plano de recuperação na [Recuperação do Local de Azure](site-recovery-overview.md).

Publique quaisquer comentários ou perguntas na parte inferior deste artigo, ou na página de perguntas do [Microsoft Q&A página de perguntas para serviços de recuperação do Azure](/answers/topics/azure-site-recovery.html).

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar scripts PowerShell nos seus planos de recuperação. Para estar acessível a partir do plano de recuperação, deve ser autor do guião e colocar o script na biblioteca VMM. Tenha em mente as seguintes considerações enquanto escreve o guião:

* Certifique-se de que os scripts utilizam blocos de captura, de modo a que as exceções sejam manuseadas graciosamente.
    - Se ocorrer uma exceção no script, o script deixa de funcionar e a tarefa mostra como falhou.
    - Se ocorrer um erro, o restante do script não é executado.
    - Se ocorrer um erro quando se corre uma falha não planeada, o plano de recuperação continua.
    - Se ocorrer um erro quando se faz uma falha planeada, o plano de recuperação para. Corrija o script, verifique se funciona como esperado e, em seguida, executar o plano de recuperação novamente.
        - O `Write-Host` comando não funciona num guião de plano de recuperação. Se usar o `Write-Host` comando num script, o script falha. Para criar saída, crie um script proxy que, por sua vez, executa o seu script principal. Para garantir que toda a saída é canalizada para fora, utilize o **\>\>** comando.
        - O guião acaba se não voltar dentro de 600 segundos.
        - Se alguma coisa for escrita para STDERR, o script é classificado como falhado. Esta informação é exibida nos detalhes da execução do script.

* Scripts num plano de recuperação executados no contexto da conta de serviço VMM. Certifique-se de que esta conta leu permissões para a partilha remota em que o script está localizado. Teste o script para executar com o mesmo nível de direitos de utilizador que a conta de serviço VMM.
* Os cmdlets VMM são entregues num módulo Windows PowerShell. O módulo é instalado quando instala a consola VMM. Para carregar o módulo no seu script, utilize o seguinte comando no script: 

    `Import-Module -Name virtualmachinemanager`

    Para obter mais informações, consulte [Começar com o Windows PowerShell e vMM](/previous-versions/system-center/system-center-2012-R2/hh875013(v=sc.12)).
* Certifique-se de que tem pelo menos um servidor de biblioteca na sua implementação VMM. Por predefinição, o caminho de partilha da biblioteca para um servidor VMM está localizado localmente no servidor VMM. O nome da pasta é MSCVMMLibrary.

  Se o seu caminho de partilha da biblioteca for remoto (ou se for local mas não partilhado com a MSCVMMLibrary), configur a partilha da seguinte forma, utilizando \\ libserver2.contoso.com\share\ como exemplo:
  
  1. Abra o Editor de Registos e, em seguida, vá para **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Altere o valor **do ScriptLibraryPath** para **\\ \libserver2.contoso.com\share \\**. Especifique o FQDN completo. Fornecer permissões para a localização da partilha. Este é o nó raiz da parte. Para verificar o nó de raiz, em VMM, vá ao nó de raiz na biblioteca. O caminho que se abre é a raiz do caminho. Este é o caminho que deve usar na variável.

  1. Teste o script utilizando uma conta de utilizador que tenha o mesmo nível de direitos de utilizador que a conta de serviço VMM. A utilização destes direitos de utilizador verifica que scripts autónomos e testados funcionam da mesma forma que funcionam nos planos de recuperação. No servidor VMM, desagravar a política de execução para contornar, da seguinte forma:

     a. Abra a consola **Windows PowerShell de 64 bits** como administrador.
     
     b. **Insira o bypass de política de execução de conjuntos**. Para obter mais informações, consulte [utilizar o Set-ExecutionPolicy cmdlet](/previous-versions/windows/it-pro/windows-powershell-1.0/ee176961(v=technet.10)).

     > [!IMPORTANT]
     > **Descose** o bypass de política de execução de set apenas na consola PowerShell de 64 bits. Se o definires para a consola PowerShell de 32 bits, os scripts não funcionam.

## <a name="add-the-script-to-the-vmm-library"></a>Adicione o script à biblioteca VMM

Se tiver um site de origem VMM, pode criar um script no servidor VMM. Então, inclua o guião no seu plano de recuperação.

1. Na partilha da biblioteca, crie uma nova pasta. Por exemplo, \<VMM server name> \MSSCVMMLibrary\RPScripts. Coloque a pasta nos servidores VMM de origem e alvo.
1. Cria o guião. Por exemplo, nomeie o script RPScript. Verifique se o guião funciona como esperado.
1. Coloque o script na \<VMM server name> pasta \MSSCVMMLibrary nos servidores VMM de origem e alvo.

## <a name="add-the-script-to-a-recovery-plan"></a>Adicione o guião a um plano de recuperação

Depois de adicionar VMs ou grupos de replicação a um plano de recuperação e criar o plano, pode adicionar o script ao grupo.

1. Abra o plano de recuperação.
1. Na lista **Step,** selecione um item. Em seguida, selecione **script** ou **Ação Manual**.
1. Especificar se deve adicionar o script ou ação antes ou depois do item selecionado. Para mover a posição do script para cima ou para baixo, selecione os botões **Move Up** e **Move Down.**
1. Se adicionar um script VMM, selecione **Failover para o script VMM**. Em **Script Path,** insira o caminho relativo para a partilha. Por exemplo, **introduza\RPScripts\RPScript.PS1**.
1. Se adicionar um runbook Azure Automation, especifique a conta Automation em que o livro de execução está localizado. Em seguida, selecione o script do livro de execução Azure que pretende utilizar.
1. Para garantir que o script funciona como esperado, faça um teste de failover do plano de recuperação.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [execução de falhas.](site-recovery-failover.md)

