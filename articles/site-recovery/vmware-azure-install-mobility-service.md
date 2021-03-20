---
title: Prepare máquinas de origem para instalar o Serviço de Mobilidade através de uma instalação de push para a recuperação de VMware e servidores físicos para a Azure | Microsoft Docs
description: Aprenda a preparar o seu servidor para instalar o agente mobility através da instalação push para a recuperação de VMware vMs e servidores físicos para a Azure utilizando o serviço de Recuperação do Local Azure.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: 78fddb5b4512883f8e78d6ed53f6e3dbbeba0e4f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "90525002"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Preparar máquina de origem para a instalação de push de agente de mobilidade

Quando configurar a recuperação de desastres para VMware VMs e servidores físicos utilizando [a Recuperação do Site Azure,](site-recovery-overview.md)instale o [serviço de Mobilidade de Recuperação](vmware-physical-mobility-service-overview.md) do Site em cada VMware VMware e servidor físico no local.  O serviço Mobility captura os dados escritos na máquina e reencaminha-os para o servidor de processo de Recuperação do Site.

## <a name="install-on-windows-machine"></a>Instalar na máquina Windows

Em cada máquina Windows que pretende proteger, faça o seguinte:

1. Certifique-se de que existe conectividade de rede entre a máquina e o servidor de processo. Se ainda não criou um servidor de processo separado, por defeito está a funcionar no servidor de configuração.
1. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ter direitos de administrador, locais ou domínios. Utilize esta conta apenas para a instalação push e para atualizações do agente.
2. Se não utilizar uma conta de domínio, desative o controlo de acesso ao utilizador remoto no computador local da seguinte forma:
    - Sob HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System chave de registo, adicione um novo DWORD: **LocalAccountTokenFilterPolicy**. Desa um valor para **1**.
    -  Para fazê-lo num pedido de comando, executar o seguinte comando:
    
       ```
       REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1 /f
       ```

3. No Windows Firewall na máquina que pretende proteger, selecione **Deixe uma aplicação ou funcionalidade através do Firewall**. Ativar a **partilha de ficheiros e impressoras** e **a instrumentação de gestão do Windows (WMI)**. Para computadores que pertencem a um domínio, pode configurar as definições de firewall utilizando um objeto de Política de Grupo (GPO).

   ![Definições de firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Adicione a conta que criou no CSPSConfigtool. Para isso, faça o sôm.
5. Abra **cspsconfigtool.exe**. Está disponível como atalho no ambiente de trabalho e na pasta %ProgramData%\ASR\home\svsystems\bin.
6. No **separador 'Gerir Contas',** selecione **'Adicionar'.**
7. Adicione a conta que criou.
8. Introduza as credenciais que utiliza para ativar a replicação para um computador.

## <a name="install-on-linux-machine"></a>Instalar na máquina Linux

Em cada máquina Linux que pretende proteger, faça o seguinte:

1. Certifique-se de que existe conectividade de rede entre a máquina Linux e o servidor de processo.
2. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ser um utilizador **raiz** no servidor Linux de origem. Utilize esta conta apenas para a instalação push e para atualizações.
3. Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
4. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
5. Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
4. Ativar o subsistema SFTP e a autenticação de senha no ficheiro sshd_config. Para isso, inscreva-se como **raiz.**
5. No ficheiro **/etc/ssh/sshd_config,** encontre a linha que começa com **a PasswordAuthentication**.
6. Descomprimir a linha, e mudar o valor para **sim.**
7. Encontre a linha que começa com **o Subsistema** e desconsusa a linha.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Reinicie o serviço **sshd**.
9. Adicione a conta que criou no CSPSConfigtool. Para isso, faça o sôm.
10. Abra **cspsconfigtool.exe**. Está disponível como atalho no ambiente de trabalho e na pasta %ProgramData%\home\home\svsystems\bin.
11. No **separador 'Gerir Contas',** selecione **'Adicionar'.**
12. Adicione a conta que criou.
13. Introduza as credenciais que utiliza para ativar a replicação para um computador.
1. Passo adicional para atualizar ou proteger as máquinas SUSE Linux Enterprise Server 11 SP3 OU RHEL 5 ou CentOS 5 ou Debian 7. [Certifique-se de que a versão mais recente está disponível no servidor de configuração](vmware-physical-mobility-service-overview.md#download-latest-mobility-agent-installer-for-suse-11-sp3-rhel-5-debian-7-server).

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que pretende replicar tiverem um software antivírus ativo em funcionamento, certifique-se de que exclui a pasta de instalação do serviço mobility das operações antivírus *(C:\ProgramData\ASR\agent*). Isto garante que a replicação funciona como esperado.

## <a name="next-steps"></a>Passos seguintes

Depois de instalado o Serviço de Mobilidade, no portal Azure, selecione **+ Replicar** para começar a proteger estes VMs. Saiba mais sobre a replicação de [VMware VMware](vmware-azure-enable-replication.md) e [servidores físicos](physical-azure-disaster-recovery.md#enable-replication).


