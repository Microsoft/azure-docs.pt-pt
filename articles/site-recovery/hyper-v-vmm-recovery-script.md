---
title: Adicione um script a um plano de recuperação na Recuperação do Site Azure
description: Aprenda a adicionar um script VMM a um plano de recuperação de desastres de VMs em nuvens vmm.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74084878"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Adicione um script VMM a um plano de recuperação

Este artigo descreve como criar um script de Gestor de Máquinas Virtuais (VMM) do System Center e adicioná-lo a um plano de recuperação em [Azure Site Recovery](site-recovery-overview.md).

Publique quaisquer comentários ou perguntas na parte inferior deste artigo, ou no fórum dos Serviços de [Recuperação do Azure.](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="prerequisites"></a>Pré-requisitos

Pode utilizar scripts PowerShell nos seus planos de recuperação. Para estar acessível a partir do plano de recuperação, deve ser autor do guião e colocar o guião na biblioteca VMM. Tenha em mente as seguintes considerações enquanto escreve o guião:

* Certifique-se de que os scripts utilizam blocos de captura de tentativas, de modo a que as exceções sejam manuseadas graciosamente.
    - Se ocorrer uma exceção no script, o script para de funcionar e a tarefa mostra como falhada.
    - Se ocorrer um erro, o restante do guião não funciona.
    - Se ocorrer um erro quando executa uma falha não planeada, o plano de recuperação continua.
    - Se ocorrer um erro quando executa uma falha planeada, o plano de recuperação para. Corrija o script, verifique se funciona como esperado e, em seguida, executar o plano de recuperação novamente.
        - O `Write-Host` comando não funciona num guião de plano de recuperação. Se usares `Write-Host` o comando num guião, o guião falha. Para criar saída, crie um script proxy que, por sua vez, executa o seu script principal. Para garantir que toda a saída ** \> ** é canalizada, utilize o comando.
        - O guião tem tempo se não voltar dentro de 600 segundos.
        - Se algo for escrito para STDERR, o script é classificado como falhado. Esta informação é exibida nos detalhes da execução do script.

* Scripts em um plano de recuperação executado no contexto da conta de serviço VMM. Certifique-se de que esta conta leu permissões para a parte remota em que o script está localizado. Teste o script para executar com o mesmo nível de direitos de utilizador que a conta de serviço VMM.
* Os cmdlets VMM são entregues num módulo PowerShell do Windows. O módulo está instalado quando instala a consola VMM. Para carregar o módulo no seu script, utilize o seguinte comando no script: 

    `Import-Module -Name virtualmachinemanager`

    Para mais informações, consulte [Começar com o Windows PowerShell e VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Certifique-se de que tem pelo menos um servidor de biblioteca na sua implementação vMM. Por padrão, o caminho de partilha da biblioteca para um servidor VMM está localizado localmente no servidor VMM. O nome da pasta é MSCVMMLibrary.

  Se o seu caminho de partilha da biblioteca for remoto (ou se for local mas não \\partilhado com a MSCVMMLibrary), configure a partilha da seguinte forma, utilizando libserver2.contoso.com\share\ como exemplo:
  
  1. Abra o Editor de Registo e, em seguida, vá para **HKEY_LOCAL_MACHINE\SOFTWARE\MICROSOFT\Azure Site Recovery\Registration**.

  1. Altere o valor do **ScriptLibraryPath** para ** \\\libserver2.contoso.com\share\\**. Especifique o FQDN completo. Forneça permissões para o local da partilha. Este é o nó raiz da parte. Para verificar se o nó de raiz, em VMM, vá ao nó raiz na biblioteca. O caminho que se abre é a raiz do caminho. Este é o caminho que deve usar na variável.

  1. Teste o script utilizando uma conta de utilizador que tenha o mesmo nível de direitos de utilizador que a conta de serviço VMM. A utilização destes direitos de utilizador verifica que os scripts autónomos, testados, funcionam da mesma forma que funcionam nos planos de recuperação. No servidor VMM, detete a política de execução para contornar, da seguinte forma:

     a. Abra a consola **Windows PowerShell de 64 bits** como administrador.
     
     b. Introduza o **bypass da política de execução de set**. Para mais informações, consulte [A utilização do cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Detete o bypass da **política de execução** de set apenas na consola PowerShell de 64 bits. Se o definires para a consola PowerShell de 32 bits, os scripts não funcionam.

## <a name="add-the-script-to-the-vmm-library"></a>Adicione o script à biblioteca VMM

Se tiver um site de origem VMM, pode criar um script no servidor VMM. Então, inclua o guião no seu plano de recuperação.

1. Na partilha da biblioteca, crie uma nova pasta. Por exemplo, \<o nome do servidor VMM>\MSSCVMMLibrary\RPScripts. Coloque a pasta nos servidores VMM de origem e alvo.
1. Crie o guião. Por exemplo, nomeie o script RPScript. Verifique se o guião funciona como esperado.
1. Coloque o script \<no nome do servidor VMM>\MSSCVMMLibrary pasta nos servidores VMM de origem e alvo.

## <a name="add-the-script-to-a-recovery-plan"></a>Adicione o script a um plano de recuperação

Depois de adicionar VMs ou grupos de replicação a um plano de recuperação e criar o plano, pode adicionar o script ao grupo.

1. Abra o plano de recuperação.
1. Na lista **Step,** selecione um item. Em seguida, selecione **script** ou **ação manual**.
1. Especifique se deve adicionar o script ou ação antes ou depois do item selecionado. Para mover a posição do script para cima ou para baixo, selecione os botões **Move Up** e **Move Down.**
1. Se adicionar um script VMM, selecione **Failover para vMM script**. No **Script Path,** insira o caminho relativo para a partilha. Por exemplo, **introduza \RPScripts\RPScript.PS1**.
1. Se adicionar um livro de execução da Automação Azure, especifique a conta Automation na qual o livro de execução está localizado. Em seguida, selecione o script do livro de execução Azure que pretende utilizar.
1. Para garantir que o guião funciona como esperado, faça um teste de failover do plano de recuperação.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre [executar falhas.](site-recovery-failover.md)

