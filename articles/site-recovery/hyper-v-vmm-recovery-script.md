---
title: Adicionar um script a um plano de recuperação no Azure Site Recovery
description: Saiba como adicionar um script do VMM a um plano de recuperação para recuperação de desastres de VMs do Hyper-V em nuvens do VMM.
author: rajani-janaki-ram
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: rajanaki
ms.openlocfilehash: 6902876e066649ae4dff4134fb8cc462f30dd0b7
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2019
ms.locfileid: "74084878"
---
# <a name="add-a-vmm-script-to-a-recovery-plan"></a>Adicionar um script do VMM a um plano de recuperação

Este artigo descreve como criar um script System Center Virtual Machine Manager (VMM) e adicioná-lo a um plano de recuperação no [Azure site Recovery](site-recovery-overview.md).

Poste comentários ou perguntas na parte inferior deste artigo ou no [Fórum dos serviços de recuperação do Azure](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="prerequisites"></a>Pré-requisitos

Você pode usar scripts do PowerShell em seus planos de recuperação. Para poder ser acessado do plano de recuperação, você deve criar o script e colocá-lo na biblioteca do VMM. Tenha em mente as seguintes considerações ao escrever o script:

* Verifique se os scripts usam blocos try-catch, para que as exceções sejam tratadas normalmente.
    - Se ocorrer uma exceção no script, o script parará de ser executado e a tarefa será mostrada como com falha.
    - Se ocorrer um erro, o restante do script não é executado.
    - Se ocorrer um erro quando você executar um failover não planejado, o plano de recuperação continuará.
    - Se ocorrer um erro quando você executar um failover planejado, o plano de recuperação será interrompido. Corrija o script, verifique se ele é executado conforme o esperado e, em seguida, execute o plano de recuperação novamente.
        - O comando `Write-Host` não funciona em um script de plano de recuperação. Se você usar o comando `Write-Host` em um script, o script falhará. Para criar a saída, crie um script de proxy que, por sua vez, execute o script principal. Para garantir que toda a saída seja canalizada, use o comando **\>\>** .
        - O script expira se não retornar dentro de 600 segundos.
        - Se qualquer coisa for gravada em STDERR, o script será classificado como com falha. Essas informações são exibidas nos detalhes de execução do script.

* Os scripts em um plano de recuperação são executados no contexto da conta de serviço do VMM. Verifique se essa conta tem permissões de leitura para o compartilhamento remoto no qual o script está localizado. Teste o script para executar com o mesmo nível de direitos de usuário que a conta de serviço do VMM.
* Os cmdlets do VMM são entregues em um módulo do Windows PowerShell. O módulo é instalado quando você instala o console do VMM. Para carregar o módulo em seu script, use o seguinte comando no script: 

    `Import-Module -Name virtualmachinemanager`

    Para obter mais informações, consulte Introdução [ao Windows PowerShell e ao VMM](https://technet.microsoft.com/library/hh875013.aspx).
* Verifique se você tem pelo menos um servidor de biblioteca na sua implantação do VMM. Por padrão, o caminho de compartilhamento de biblioteca para um servidor VMM está localizado localmente no servidor do VMM. O nome da pasta é MSCVMMLibrary.

  Se o caminho de compartilhamento de biblioteca for remoto (ou se for local, mas não compartilhado com MSCVMMLibrary), configure o compartilhamento da seguinte maneira, usando \\libserver2. contoso. com\share\ como um exemplo:
  
  1. Abra o editor do registro e vá para **HKEY_LOCAL_MACHINE site do \Software\microsoft\azure Recovery\Registration**.

  1. Altere o valor de **ScriptLibraryPath** para **\\\libserver2.contoso.com\share\\** . Especifique o FQDN completo. Forneça permissões para o local de compartilhamento. Esse é o nó raiz do compartilhamento. Para verificar o nó raiz, no VMM, vá para o nó raiz na biblioteca. O caminho que é aberto é a raiz do caminho. Esse é o caminho que você deve usar na variável.

  1. Teste o script usando uma conta de usuário que tenha o mesmo nível de direitos de usuário que a conta de serviço do VMM. O uso desses direitos de usuário verifica se os scripts autônomos testados são executados da mesma maneira que são executados nos planos de recuperação. No servidor do VMM, defina a política de execução como bypass, da seguinte maneira:

     a. Abra o console do **Windows PowerShell de 64 bits** como administrador.
     
     b. Insira **Set-ExecutionPolicy bypass**. Para obter mais informações, consulte [usando o cmdlet Set-ExecutionPolicy](https://technet.microsoft.com/library/ee176961.aspx).

     > [!IMPORTANT]
     > Defina o **bypass Set-ExecutionPolicy** somente no console do PowerShell de 64 bits. Se você defini-lo para o console do PowerShell de 32 bits, os scripts não são executados.

## <a name="add-the-script-to-the-vmm-library"></a>Adicionar o script à biblioteca do VMM

Se você tiver um site de origem do VMM, poderá criar um script no servidor do VMM. Em seguida, inclua o script em seu plano de recuperação.

1. No compartilhamento de biblioteca, crie uma nova pasta. Por exemplo, \<nome do servidor do VMM > \MSSCVMMLibrary\RPScripts. Coloque a pasta nos servidores VMM de origem e de destino.
1. Crie o script. Por exemplo, nomeie o script RPScript. Verifique se o script funciona conforme o esperado.
1. Coloque o script no \<nome do servidor VMM > pasta \MSSCVMMLibrary nos servidores VMM de origem e de destino.

## <a name="add-the-script-to-a-recovery-plan"></a>Adicionar o script a um plano de recuperação

Depois de adicionar VMs ou grupos de replicação a um plano de recuperação e criar o plano, você pode adicionar o script ao grupo.

1. Abra o plano de recuperação.
1. Na lista **etapa** , selecione um item. Em seguida, selecione **script** ou **ação manual**.
1. Especifique se deseja adicionar o script ou a ação antes ou depois do item selecionado. Para mover a posição do script para cima ou para baixo, selecione os botões **mover para cima** e **mover para baixo** .
1. Se você adicionar um script do VMM, selecione **failover para o script do VMM**. Em **caminho do script**, insira o caminho relativo para o compartilhamento. Por exemplo, digite **\RPScripts\RPScript.ps1**.
1. Se você adicionar um runbook de automação do Azure, especifique a conta de automação na qual o runbook está localizado. Em seguida, selecione o script de runbook do Azure que você deseja usar.
1. Para garantir que o script funcione conforme o esperado, faça um failover de teste do plano de recuperação.


## <a name="next-steps"></a>Passos seguintes
* Saiba mais sobre a [execução de failovers](site-recovery-failover.md).

