---
title: Preparar computadores de origem para instalar o serviço de mobilidade por meio da instalação por push para recuperação de desastre de VMs VMware e servidores físicos no Azure | Microsoft Docs
description: Saiba como preparar seu servidor para instalar o agente de mobilidade por meio da instalação por push para recuperação de desastre de VMs VMware e servidores físicos no Azure usando o serviço Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/25/2019
ms.author: ramamill
ms.openlocfilehash: a2f4bdb96b8d1ecb23ddcec844726439ec46fff2
ms.sourcegitcommit: dbde4aed5a3188d6b4244ff7220f2f75fce65ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/19/2019
ms.locfileid: "74186455"
---
# <a name="prepare-source-machine-for-push-installation-of-mobility-agent"></a>Preparar o computador de origem para a instalação por push do agente de mobilidade

Ao configurar a recuperação de desastre para VMs VMware e servidores físicos usando [Azure site Recovery](site-recovery-overview.md), você instala o [serviço de mobilidade de site Recovery](vmware-physical-mobility-service-overview.md) em cada servidor físico e VM VMware local.  O serviço de mobilidade captura gravações de dados no computador e as encaminha para o servidor de processo de Site Recovery.

## <a name="install-on-windows-machine"></a>Instalar no computador Windows

Em cada computador Windows que você deseja proteger, faça o seguinte:

1. Verifique se há conectividade de rede entre o computador e o servidor de processo. Se você ainda não configurou um servidor de processo separado, por padrão ele está em execução no servidor de configuração.
1. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ter direitos de administrador, seja local ou de domínio. Use essa conta somente para a instalação por push e para atualizações de agente.
2. Se você não usar uma conta de domínio, desabilite o controle de acesso de usuário remoto no computador local da seguinte maneira:
    - Em HKEY_LOCAL_MACHINE chave do registro \SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, adicione um novo DWORD: **LocalAccountTokenFilterPolicy**. Defina o valor como **1**.
    -  Para fazer isso em um prompt de comando, execute o seguinte comando:  
   `REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d
3. No firewall do Windows no computador que você deseja proteger, selecione **permitir um aplicativo ou recurso por meio do firewall**. Habilite o **compartilhamento de arquivos e impressoras** e **Instrumentação de gerenciamento do Windows (WMI)** . Para computadores que pertencem a um domínio, você pode definir as configurações de firewall usando um objeto de Política de Grupo (GPO).

   ![Definições de firewall](./media/vmware-azure-install-mobility-service/mobility1.png)

4. Adicione a conta que criou no CSPSConfigtool. Para fazer isso, entre no servidor de configuração.
5. Abra **cspsconfigtool.exe**. Ele está disponível como um atalho na área de trabalho e na pasta%ProgramData%\ASR\home\svsystems\bin.
6. Na guia **gerenciar contas** , selecione **adicionar conta**.
7. Adicione a conta que criou.
8. Introduza as credenciais que utiliza para ativar a replicação para um computador.

## <a name="install-on-linux-machine"></a>Instalar no computador Linux

Em cada computador Linux que você deseja proteger, faça o seguinte:

1. Verifique se há conectividade de rede entre o computador Linux e o servidor de processo.
2. Crie uma conta que possa ser utilizada pelo servidor de processos para aceder ao computador. A conta deve ser um utilizador **raiz** no servidor Linux de origem. Use essa conta somente para a instalação por push e para atualizações.
3. Verifique se o ficheiro /etc/hosts no servidor Linux de origem tem entradas que mapeiam o nome de anfitrião local para endereços IP associados a todos os adaptadores de rede.
4. Instale os pacotes openssh, openssh-server e openssl mais recentes no computador que quer replicar.
5. Certifique-se de que o Secure Shell (SSH) está ativado e em execução na porta 22.
4. Habilite a autenticação de subsistema e senha do SFTP no arquivo de sshd_config. Para fazer isso, entre como **raiz**.
5. No arquivo **/etc/ssh/sshd_config** , localize a linha que começa com **PasswordAuthentication**.
6. Remova a marca de comentário da linha e altere o valor para **Sim**.
7. Localize a linha que começa com **subsistema**e remova a marca de comentário da linha.

      ![Linux](./media/vmware-azure-install-mobility-service/mobility2.png)

8. Reinicie o serviço **sshd**.
9. Adicione a conta que criou no CSPSConfigtool. Para fazer isso, entre no servidor de configuração.
10. Abra **cspsconfigtool.exe**. Ele está disponível como um atalho na área de trabalho e na pasta%ProgramData%\home\svsystems\bin.
11. Na guia **gerenciar contas** , selecione **adicionar conta**.
12. Adicione a conta que criou.
13. Introduza as credenciais que utiliza para ativar a replicação para um computador.

## <a name="anti-virus-on-replicated-machines"></a>Antivírus em máquinas replicadas

Se as máquinas que você deseja replicar tiverem um software antivírus ativo em execução, certifique-se de excluir a pasta de instalação do serviço de mobilidade das operações de antivírus (*C:\ProgramData\ASR\agent*). Isso garante que a replicação funcione conforme o esperado.

## <a name="next-steps"></a>Passos seguintes

Depois que o serviço de mobilidade estiver instalado, no portal do Azure, selecione **+ replicar** para começar a proteger essas VMs. Saiba mais sobre como habilitar a replicação para [VMs VMware](vmware-azure-enable-replication.md) e [servidores físicos](physical-azure-disaster-recovery.md#enable-replication).


