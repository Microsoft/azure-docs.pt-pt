---
title: Migrar servidores Windows Server 2008 no local para o Azure com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como migrar máquinas Windows Server 2008 no local para o Azure, com o Azure Site Recovery.
author: bsiva
manager: abhemraj
ms.service: site-recovery
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.date: 11/27/2018
ms.author: bsiva
ms.custom: MVC
ms.openlocfilehash: c91fa2c269b507988d2bdefcd2882b2ab55fec35
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58094870"
---
# <a name="migrate-servers-running-windows-server-2008-to-azure"></a>Migrar servidores com o Windows Server 2008 para o Azure

Este tutorial mostra como migrar servidores no local com o Windows Server 2008 ou 2008 R2 para o Azure, com o Azure Site Recovery. Neste tutorial, ficará a saber como:

> [!div class="checklist"]
> * Preparar o ambiente no local para a migração
> * Configurar o ambiente de destino
> * Configurar uma política de replicação
> * Ativar a replicação
> * Executar uma migração de teste para confirmar que está tudo a funcionar conforme o previsto
> * Fazer a ativação pós-falha no Azure e concluir a migração

A secção de limitações e problemas conhecidos apresenta algumas limitações e soluções para problemas conhecidos com que se poderá deparar ao migrar máquinas com o Windows Server 2008 para o Azure. 


## <a name="supported-operating-systems-and-environments"></a>Sistemas operativos e ambientes suportados


|Sistema Operativo  | Ambiente no local  |
|---------|---------|
|Windows Server 2008 SP2 - 32 bits e 64 bits (IA-32 e x86-64)</br>- Standard</br>- Enterprise</br>- Datacenter   |     VMs de VMware, VMs de Hyper-V e Servidores Físicos    |
|Windows Server 2008 R2 SP1 - 64 bits</br>- Standard</br>- Enterprise</br>- Datacenter     |     VMs de VMware, VMs de Hyper-V e Servidores Físicos|

> [!WARNING]
> - A migração de servidores com o Server Core não é suportada.
> - Antes da migração, certifique-se de que tem o service pack e as atualizações do Windows mais recentes instalados.


## <a name="prerequisites"></a>Pré-requisitos

Antes de começar, é útil rever a arquitetura do Azure Site Recovery para a [migração de servidores Físicos e de VMware](vmware-azure-architecture.md) ou a [migração de máquinas virtuais de Hyper-V](hyper-v-azure-architecture.md) 

Para migrar máquinas virtuais de Hyper-V com o Windows Server 2008 ou Windows Server 2008 R2, siga os passos indicados no tutorial [Migrar máquinas no local para o Azure](migrate-tutorial-on-premises-azure.md).

O resto deste tutorial mostra como pode migrar máquinas virtuais de VMware no local e Servidores físicos com o Windows Server 2008 ou 2008 R2.
> [!TIP]
> À procura de uma forma sem agente migrar VMs de VMware para o Azure? [Clique aqui](https://aka.ms/migrateVMs-signup)


## <a name="limitations-and-known-issues"></a>Limitações e problemas conhecidos

- O Servidor de Configuração, os servidores de processos adicionais e o serviço de mobilidade utilizados para migrar servidores Windows Server 2008 SP2 devem executar a versão 9.19.0.0 ou posterior do software Azure Site Recovery.

- Os pontos de recuperação consistentes com a aplicação e a funcionalidade de consistência de várias VMS não são suportados na replicação de servidores com o Windows Server 2008 SP2. Os servidores Windows Server 2008 SP2 devem ser migrados para um ponto de recuperação consistente com falhas. Os pontos de recuperação consistentes com falhas são gerados a cada 5 minutos por predefinição. A utilização de uma política de replicação com uma frequência de instantâneo consistente com a aplicação configurada irá fazer com que o estado de funcionamento da replicação passe a ser crítico devido à inexistência de pontos de recuperação consistentes com a aplicação. Para evitar falsos positivos, defina a frequência de instantâneo consistente com a aplicação na política de replicação como "Desativada".

- Os servidores que estão a ser migrados devem ter o .NET Framework 3.5 Service Pack 1 para que o serviço de mobilidade funcione.

- Se o servidor tiver discos dinâmicos, poderá reparar em determinadas configurações que estes discos no servidor de ativação pós-falha estão marcados como offline ou são apresentados como discos externos. Também poderá reparar que o estado do conjunto espelhado para volumes espelhados em discos dinâmicos está marcado como "Ocorreu uma falha na redundância". Para corrigir este problema a partir de diskmgmt.msc, importe manualmente estes discos e reative-os.

- Os servidores que estão a ser migrados devem ter o controlador vmstorfl.sys. A ativação pós-falha poderá falhar se o controlador não estiver presente no servidor que está a ser migrado. 
  > [!TIP]
  >Verifique se o controlador está presente em "C:\Windows\system32\drivers\vmstorfl.sys" . Se o controlador não for encontrado, pode solucionar o problema criando um ficheiro fictício em alternativa. 
  >
  > Abra a linha de comandos (run > cmd) e execute o seguinte comando: "copy nul c:\Windows\system32\drivers\vmstorfl.sys"

- Pode não ser possível fazer o RDP para servidores Windows Server 2008 SP2 com o sistema operativo de 32 bits imediatamente após a respetiva ativação pós-falha ou ativação pós-falha de teste no Azure. Reinicie a máquina virtual na qual foi feita a ativação pós-falha a partir do portal do Azure e tente ligar novamente. Se ainda assim não conseguir ligar, verifique se o servidor está configurado para permitir ligações ao ambiente de trabalho remoto e certifique-se de que não existem regras de firewall ou grupos de segurança de rede que estejam a bloquear a ligação. 
  > [!TIP]
  > Antes de migrar servidores, é altamente recomendada uma ativação pós-falha de teste. Certifique-se de que, feita pelo menos uma ativação pós-falha de teste em cada servidor que está a migrar. Como parte da ativação pós-falha de teste, ligue à máquina na qual foi executada a ativação pós-falha de teste e certifique-se de que tudo está a funcionar como esperado.
  >
  >A operação de ativação pós-falha de teste não é disruptiva e ajuda a testar as migrações através da criação de máquinas virtuais numa rede isolada à sua escolha. Ao contrário da operação de ativação pós-falha, durante a operação de ativação pós-falha de teste, a replicação de dados continua a ser executada. Pode executar a quantidade de ativações pós-falha de teste que quiser até estar preparado para executar a migração. 
  >
  >


## <a name="getting-started"></a>Introdução

Execute as seguintes tarefas para preparar a subscrição do Azure e o ambiente de VMware/Físico no local:

1. [Preparar o Azure](tutorial-prepare-azure.md)
2. Preparar o [VMware](vmware-azure-tutorial-prepare-on-premises.md) no local


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

1. Inicie sessão no [portal do Azure](https://portal.azure.com) > **Serviços de Recuperação**.
2. Clique em **criar um recurso** > **ferramentas de gestão** > **cópia de segurança e recuperação de Site**.
3. Em **Nome**, especifique o nome amigável **W2K8-migration**. Se tiver mais do que uma subscrição, selecione a que for adequada.
4. Crie um grupo de recursos com o nome **w2k8migrate**.
5. Selecione uma região do Azure. Para verificar as regiões suportadas, veja a disponibilidade geográfica em [Detalhes dos Preços do Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Para aceder rapidamente ao cofre a partir do dashboard, clique em **Afixar ao dashboard** e clique em **Criar**.

   ![Novo cofre](media/migrate-tutorial-windows-server-2008/migrate-windows-server-2008-vault.png)

O cofre novo é adicionado ao **Dashboard** em **Todos os recursos** e na página principal **Cofres dos Serviços de Recuperação**.


## <a name="prepare-your-on-premises-environment-for-migration"></a>Preparar o ambiente no local para a migração

- Para migrar máquinas virtuais Windows Server 2008 em execução em VMware, [configure o Servidor de Configuração no local em VMware](vmware-azure-tutorial.md#set-up-the-source-environment).
- Se não for possível configurar o Servidor de Configuração como uma máquina virtual de VMware, [configure o Servidor de Configuração num servidor físico ou máquina virtual no local](physical-azure-disaster-recovery.md#set-up-the-source-environment).

## <a name="set-up-the-target-environment"></a>Configurar o ambiente de destino

Selecione e verifique os recursos de destino.

1. Clique em **Preparar a infraestrutura** > **Destino** e selecione a subscrição do Azure que pretende utilizar.
2. Especifique o modelo de implementação do Resource Manager.
3. A Recuperação de Sites verifica que tem uma ou mais contas de armazenamento e redes do Azure compatíveis.


## <a name="set-up-a-replication-policy"></a>Configurar uma política de replicação

1. Para criar uma nova política de replicação, clique em **Infraestrutura do Site Recovery** > **Políticas de Replicação** > **+Política de Replicação**.
2. Em **Criar política de replicação**, especifique um nome de política.
3. Em **Limiar RPO**, especifique o limite do objetivo de ponto de recuperação (RPO). Se o RPO de replicação exceder este limite, será gerado um alerta.
4. Em **Retenção do ponto de recuperação**, especifique (em horas) a duração da janela de retenção para cada ponto de recuperação. Servidores replicadas podem ser recuperadas para qualquer ponto nessa janela. É suportada uma retenção de até 24 horas para máquinas replicadas para o armazenamento premium e até 72 horas para armazenamento standard.
5. Em **Frequência de instantâneo consistente com a aplicação**, especifique **Desativada**. Clique em **OK** para criar a política.

A política é associada automaticamente ao servidor de configuração.

> [!WARNING]
> Certifique-se de que especifica **DESATIVADA** na definição Frequência de instantâneo consistente com a aplicação da política de replicação. Apenas os pontos de recuperação consistentes com falhas são suportados ao replicar servidores com o Windows Server 2008. Especificar qualquer outro valor para a frequência de instantâneo consistente com a aplicação irá resultar em alertas falsas ao ativar o estado de funcionamento de replicação do servidor crítico devido à ausência de pontos de recuperação consistente com a aplicação.

   ![Criar política de replicação](media/migrate-tutorial-windows-server-2008/create-policy.png)

## <a name="enable-replication"></a>Ativar a replicação

[Ative a replicação](physical-azure-disaster-recovery.md#enable-replication) para que o servidor Windows Server 2008 SP2/Windows Server 2008 R2 SP1 seja migrado.
   
   ![Adicionar um servidor físico](media/migrate-tutorial-windows-server-2008/Add-physical-server.png)

   ![Ativar a replicação](media/migrate-tutorial-windows-server-2008/Enable-replication.png)

## <a name="run-a-test-migration"></a>Executar uma migração de teste

Pode executar uma ativação pós-falha de teste da replicação de servidores após a conclusão da replicação inicial e o estado do servidor passar para **Protegido**.

Execute uma [ativação pós-falha de teste](tutorial-dr-drill-azure.md) para o Azure para confirmar que está tudo a funcionar conforme o previsto.

   ![Ativação pós-falha de teste](media/migrate-tutorial-windows-server-2008/testfailover.png)


## <a name="migrate-to-azure"></a>Migrar para o Azure

Execute a ativação pós-falha nos computadores que quer migrar.

1. Em **Definições** > **Itens replicados**, clique no computador > **Ativação Pós-falha**.
2. Em **Ativação pós-falha**, selecione um **Ponto de Recuperação** para o qual fazer a ativação pós-falha. Selecione o ponto de recuperação mais recente.
3. Selecione **Encerrar a máquina antes de iniciar a ativação pós-falha**. O Site Recovery tentará encerrar o servidor antes de acionar a ativação pós-falha. A ativação pós-falha continua, mesmo que o encerramento falhe. Pode seguir o progresso da ativação pós-falha na página **Tarefas**.
4. Certifique-se de que a VM do Azure é apresentada no Azure conforme esperado.
5. Na **itens replicados**, clique com o botão direito do servidor > **concluir a migração**. Isso faz o seguinte:

    - O processo de migração é concluído, para a replicação para o servidor e interrompe a faturação do Site Recovery para a servir.
    - Este passo limpa os dados de replicação. Não elimina as VMs migradas.

   ![Concluir a migração](media/migrate-tutorial-windows-server-2008/complete-migration.png)


> [!WARNING]
> **Não cancelar uma ativação pós-falha em curso**: Replicação de servidor está parada antes de o início da ativação pós-falha. Se cancelar uma ativação pós-falha em curso, paradas de ativação pós-falha, mas o servidor não continua a replicar.
