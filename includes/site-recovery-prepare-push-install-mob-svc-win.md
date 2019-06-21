---
author: rayne-wiselman
ms.service: site-recovery
ms.topic: include
ms.date: 10/26/2018
ms.author: raynew
ms.openlocfilehash: ffc9b09c72ef1bf5180a0d626908d09b6fdd41ca
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/18/2019
ms.locfileid: "67203719"
---
### <a name="prepare-for-a-push-installation-on-a-windows-computer"></a>Preparar para uma instalação de push num computador Windows

1. Certifique-se de que existe conectividade de rede entre o computador do Windows e o servidor de processos.
1. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ter direitos de administrador local ou domínio. Utilize esta conta apenas para a instalação de push e para as atualizações do agente.

   > [!NOTE]
   > Se não usar uma conta de domínio, desative o controlo de acesso de utilizador remoto no computador local. Para desativar o controlo de acesso de utilizador remoto, na chave do Registro HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione um DWORD novo: **LocalAccountTokenFilterPolicy**. Defina o valor como **1**. Para executar esta tarefa no prompt de comando, execute o seguinte comando:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`
   >
   >
1. Na Firewall do Windows no computador que pretende proteger, selecione **permitir uma aplicação ou funcionalidade através da Firewall**. Ativar **de ficheiros e partilha de impressoras** e **Windows Management Instrumentation (WMI)** . Para computadores que pertençam a um domínio, pode configurar as definições de firewall utilizando um objeto de diretiva de grupo (GPO).

   ![Definições de firewall](./media/site-recovery-prepare-push-install-mob-svc-win/mobility1.png)

1. Adicione a conta que criou no CSPSConfigtool. Siga estes passos.

    a. Inicie sessão no servidor de configuração.

    b. Abra **cspsconfigtool.exe**. Está disponível como um atalho na área de trabalho e na pasta %programdata%\home\svsystems\bin.

    c. Sobre o **gerir contas** separador, selecione **adicionar conta**.

    d. Adicione a conta que criou.

    e. Introduza as credenciais que utiliza para ativar a replicação para um computador.
