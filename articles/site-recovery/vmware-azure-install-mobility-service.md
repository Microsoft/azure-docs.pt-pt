---
title: Prepare máquinas de origem para instalar o Serviço de Mobilidade através de uma instalação push para recuperação de desastres de VMware VMs e servidores físicos para o Azure [ Microsoft Docs
description: Aprenda a preparar o seu servidor para instalar o agente mobility através da instalação push para recuperação de vMware vMs e servidores físicos para o Azure utilizando o serviço de recuperação do site Azure.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "74186455"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Preparar a máquina de origem para a instalação de impulso do agente de mobilidade

Quando configurar a recuperação de desastres para VMware VMs e servidores físicos utilizando a Recuperação do [Site Azure,](site-recovery-overview.md)instala o serviço de Mobilidade de [Recuperação](vmware-physical-mobility-service-overview.md) do Site em cada VM e servidor físico no local.  O serviço de Mobilidade captura os dados escritos na máquina e encaminha-os para o servidor do processo de Recuperação do Site.

## <a name="install-on-windows-machine"></a>Instalar na máquina Windows

Em cada máquina do Windows que pretende proteger, faça o seguinte:

1. Certifique-se de que existe conectividade de rede entre a máquina e o servidor de processos. Se não tiver configurado um servidor de processo separado, então por padrão está a funcionar no servidor de configuração.
1. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ter direitos de administrador, locais ou de domínio. Utilize esta conta apenas para a instalação push e para atualizações do agente.
2. Se não utilizar uma conta de domínio, desative o controlo de acesso remoto ao utilizador no computador local da seguinte forma:
    - Em HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System', adicione uma nova DWORD: **LocalAccountTokenFilterPolicy**. Desloque o valor para **1**.
    -  Para fazê-lo a uma solicitação de comando, execute o seguinte comando:  
   'REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. No Windows Firewall na máquina que pretende proteger, selecione **Permitir uma aplicação ou funcionalidade através do Firewall**. Ativar a partilha de **ficheiros e impressoras** e a instrumentação de gestão do **windows (WMI)**. Para computadores que pertencem a um domínio, pode configurar as definições de firewall utilizando um objeto de Política de Grupo (GPO).

   ![Definições de firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Adicione a conta que criou no CSPSConfigtool. Para isso, inscreva-se no seu servidor de configuração.
5. Abra **cspsconfigtool.exe**. Está disponível como um atalho no ambiente de trabalho e na pasta %ProgramData%\ASR\home\svsystems\bin.
6. No separador **'Gerir Contas',** selecione **Adicionar Conta**.
7. Adicione a conta que criou.
8. Introduza as credenciais que utiliza para ativar a replicação para um computador.

## <a name="install-on-linux-machine"></a>Instalar na máquina Linux

Em cada máquina Linux que pretende proteger, faça o seguinte:

1. Certifique-se de que existe conectividade de rede entre a máquina Linux e o servidor de processos.
2. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ser um utilizador **raiz** no servidor Linux de origem. Utilize esta conta apenas para a instalação push e para atualizações.
3. Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
4. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
5. Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
4. Ativar o subsistema SFTP e a autenticação de palavra-passe no ficheiro sshd_config. Para isso, inscreva-se como **raiz.**
5. No ficheiro **/etc/ssh/sshd_config,** encontre a linha que começa com a **Autenticação password**.
6. Descodere a linha, e mude o valor para **sim**.
7. Encontre a linha que começa com **o Subsistema**e descomente a linha.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Reinicie o serviço **sshd**.
9. Adicione a conta que criou no CSPSConfigtool. Para isso, inscreva-se no seu servidor de configuração.
10. Abra **cspsconfigtool.exe**. Está disponível como um atalho no ambiente de trabalho e na pasta %ProgramData%\home\svsystems\bin.
11. No separador **'Gerir Contas',** selecione **Adicionar Conta**.
12. Adicione a conta que criou.
13. Introduza as credenciais que utiliza para ativar a replicação para um computador.

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que pretende replicar tiverem software antivírus ativo em funcionamento, certifique-se de que exclui a pasta de instalação do serviço mobility de operações antivírus *(C:\ProgramData\ASR\agent).* Isto garante que a replicação funciona como esperado.

## <a name="next-steps"></a>Passos seguintes

Depois de instalado o Serviço de Mobilidade, no portal Azure, selecione **+ Replicate** para começar a proteger estes VMs. Saiba mais sobre a replicação de [VMware VMs](vmware-azure-enable-replication.md) e [servidores físicos](physical-azure-disaster-recovery.md#enable-replication).


