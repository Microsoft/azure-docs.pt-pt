---
title: Fazer uma cópia de segurança de máquinas do Windows com o agente MARS de cópia de segurança do Azure
description: Utilize o agente de cópia de segurança Microsoft recuperação dos serviços Azure (MARS) para fazer uma cópia de segurança de máquinas do Windows.
services: backup
author: rayne-wiselman
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: raynew
ms.openlocfilehash: 7a1bd6da68b49481429709c7e4fd37dd5c07ae2c
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58200791"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Fazer uma cópia de segurança de máquinas do Windows com o agente MARS de cópia de segurança do Azure

Este artigo explica como fazer uma cópia de segurança de máquinas do Windows com o [Azure Backup](backup-overview.md) serviço e o agente dos serviços de recuperação do Azure (MARS) da Microsoft, também conhecido como o agente de cópia de segurança do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
> * Verifique os pré-requisitos e crie um cofre dos serviços de recuperação.
> * Transferir e configurar o Agente MARS
> * Crie uma política de cópia de segurança e a agenda.
> * Efetue um ad-hoc cópia de segurança.

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O agente de MARS é utilizado pela cópia de segurança do Azure para fazer uma cópia de segurança de ficheiros, pastas e estado do sistema de máquinas no local e VMs do Azure para um cofre dos serviços de recuperação de cópia de segurança no Azure. Pode executar o agente da seguinte forma:

- Execute o agente diretamente em máquinas do Windows no local para que eles podem criar cópias de segurança diretamente para um cofre de serviços de recuperação de cópia de segurança no Azure.
- Execute as VMs do Azure de agente com o Windows (lado a lado com a extensão de cópia de segurança de VM do Azure) para criar cópias de segurança específicos ficheiros e pastas na VM.
- Execute o agente num Microsoft Azure Backup Server (MABS) ou uma proteção de dados no System Center - server Manager (DPM). Neste cenário, as máquinas e cargas de trabalho de cópia de segurança para o MABS/DPM e, em seguida, MABS/DPM cria cópias de segurança para um cofre no Azure com o agente MARS.
O que pode criar cópias de segurança depende de onde o agente está instalado.

> [!NOTE]
> O método primário de backup das VMs do Azure é usar uma extensão de cópia de segurança do Azure na VM. Isso faz o backup de toda a VM. Pode querer instalar e utilizar o agente de MARS juntamente com a extensão, se pretender criar cópias de segurança específicos ficheiros e pastas na VM. [Saiba mais](backup-architecture.md#architecture-direct-backup-of-azure-vms).

![Passos do processo de cópia de segurança](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Antes de começar

- [Saiba como](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) cópia de segurança do Azure cria uma cópia de segurança de máquinas do Windows com o agente MARS.
- [Saiba mais sobre](backup-architecture.md#architecture-back-up-to-dpmmabs) a arquitetura de cópia de segurança que executa o agente de MARS num servidor MABS ou DPM secundário.
- [Revisão](backup-support-matrix-mars-agent.md) o que é suportado e o que pode ser uma cópia de segurança com o agente MARS.
- Verificar o acesso de internet nas máquinas que pretende criar cópias de segurança.
- Para fazer uma cópia de segurança de um servidor ou cliente para o Azure, precisa de uma conta do Azure. Se não tiver uma, pode criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

### <a name="verify-internet-access"></a>Verifique se o acesso à internet

Se a sua máquina limitou o acesso à internet, certifique-se de que as definições da firewall na máquina ou proxy permitem estes URLs e endereços IP:

**URLs**

- www\.msftncsi.com
- *.Microsoft.com
- *.WindowsAzure.com
- *.microsoftonline.com
- \*.windows.net

**Endereço IP**

- 20.190.128.0/18
- 40.126.0.0/18


## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação 

Um cofre dos serviços de recuperação armazena todas as cópias de segurança e pontos de recuperação que criar ao longo do tempo e contém a política de cópia de segurança aplicada às máquinas de cópia de segurança. Crie um cofre da seguinte forma:

1. Inicie sessão para o [Portal do Azure](https://portal.azure.com/) com a sua subscrição do Azure.
2. Na pesquisa, escreva **serviços de recuperação** e clique em **cofres dos serviços de recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/open-rs-vault-list.png)

3. Sobre o **cofres dos serviços de recuperação** menu, clique em **+ adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Para o **Nome**, introduza um nome amigável para identificar o cofre.

   - O nome tem de ser exclusivo para a subscrição do Azure.
   - Pode conter 2 e 50 carateres.
   - Ele tem de começar com uma letra e pode conter apenas letras, números e hífenes.

5. Selecione a subscrição do Azure, o grupo de recursos e a região geográfica na qual deve ser criado no cofre. Dados de cópia de segurança são enviados para o cofre. Em seguida, clique em **Criar**.

    ![Passo 3 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   - Pode demorar algum tempo para o cofre a ser criada.
   - Monitorize as notificações de estado na área de canto superior direito do portal. Se após vários minutos não vir o cofre, clique em **atualizar**.

     ![Clique no botão Atualizar](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Redundância de armazenamento do conjunto

Armazenamento para o Cofre lida automaticamente com o Azure Backup. Tem de especificar a forma como os que o armazenamento é replicado.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Sob o **configurações** secção, clique em **propriedades**.
2. Na **propriedades**, em **configuração de cópia de segurança**, clique em **atualização**.

3. Selecione o tipo de replicação de armazenamento e clique em **guardar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

- Recomendamos que se estiver a utilizar o Azure como um ponto final de armazenamento de cópia de segurança primário, continuar a utilizar a predefinição **georredundante** definição.
- Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
- Saiba mais sobre [geo](../storage/common/storage-redundancy-grs.md) e [local](../storage/common/storage-redundancy-lrs.md) redundância.

## <a name="download-the-mars-agent"></a>Transferir o agente de MARS

Transferir o agente de MARS para instalação nos computadores que pretende criar cópias de segurança.

- Se já tiver instalado o agente em qualquer máquinas, certifique-se de que está a executar a versão mais recente.
- A versão mais recente está disponível no portal ou utilizando um [transferência direta](https://aka.ms/azurebackup_agent)

1. No cofre, sob **introdução**, clique em **cópia de segurança**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. Na **em que a sua carga de trabalho é executado?**, selecione **locais**. Deve selecionar esta opção, mesmo que deseja instalar o agente de MARS numa VM do Azure.
3. Na **o que fazer quiser a cópia de segurança?**, selecione **ficheiros e pastas** e/ou **estado do sistema**. Há várias outras opções disponíveis, mas estas só são suportadas caso esteja a executar um servidor secundário de cópia de segurança. Clique em **preparar infraestrutura**.

      ![Configurar ficheiros e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Sobre o **preparar a infraestrutura**, em **agente dos serviços de recuperação instalar**, transferir o agente de MARS.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. No menu de pop-up de transferência, clique em **Guardar**. Por predefinição, o ficheiro **MARSagentinstaller.exe** é guardado na pasta Transferências.

6. Agora, verifique **já está download ou ao utilizar o agente de serviços de recuperação mais recente**e, em seguida, transferir as credenciais do cofre.

    ![transferir as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Clique em **Guardar**. O ficheiro é transferido para a pasta de transferência. Não é possível abrir o ficheiro de credenciais do cofre.

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

1. Executar o **MARSagentinstaller.exe** ficheiros nas máquinas que pretende criar cópias de segurança.
2. No Assistente de configuração do agente MARS > **definições de instalação**, especifique onde pretende instalar o agente e um local para utilizar para a cache. Clique depois em **Seguinte**.
   - O Azure Backup utiliza a cache para armazenar instantâneos de dados antes de os enviar para o Azure.
   - A localização da cache deve ter espaço livre igual a, pelo menos, 5% do tamanho dos dados que pode criar uma cópia de segurança.

     ![Definições de instalação do Assistente de MARS](./media/backup-configure-vault/mars1.png)

2. Na **configuração do Proxy**, especifique a forma do agente em execução na máquina do Windows se vai ligar à internet. Clique depois em **Seguinte**.

   - Se estiver a utilizar um personalizado proxy especificar as definições de proxy e as credenciais se necessário.
   - Lembre-se de que o agente tem acesso ao [estes URLs](#verify-internet-access).

     ![Acesso à internet do MARS Assistente](./media/backup-configure-vault/mars2.png)

3. Na **instalação** reveja a verificação de pré-requisitos e clique em **instalar**.
4. Depois do agente está instalado, clique em **avançar para o registo**.
5. Na **Assistente de registo do servidor** > **identificação de cofre**, navegue e selecione o ficheiro de credenciais que transferiu. Clique depois em **Seguinte**.

    ![Registre-se - as credenciais do Cofre](./media/backup-configure-vault/register1.png)

6. Na **definição de encriptação**, especifique uma frase de acesso que será utilizada para encriptar e desencriptar as cópias de segurança para a máquina.

    - Guarde a frase de acesso de encriptação numa localização segura.
    - Se perder ou se esqueça a frase de acesso, Microsoft não pode ajudar a recuperar os dados de cópia de segurança. Guarde o ficheiro numa localização segura. Que é necessária para restaurar uma cópia de segurança.

7. Clique em **concluir**. O agente está agora instalado e a máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## <a name="create-a-backup-policy"></a>Criar uma política de cópia de segurança

A política de cópia de segurança especifica quando deve criar instantâneos dos dados para criar pontos de recuperação e durante quanto tempo para manter os pontos de recuperação.

- Configurar uma política de cópia de segurança com o agente MARS.
- Cópia de segurança do Azure automaticamente não tem o horário de Verão (horário de Verão) em conta. Isso poderia causar alguma discrepância entre a hora real e a hora de cópia de segurança agendada.

Crie uma política da seguinte forma:

1. Em cada máquina, abra o agente MARS. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.
2. Na **ações**, clique em **Agendar cópia de segurança**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)

3. No Assistente de cópia de segurança de agenda > **introdução**, clique em **próxima**.
4. Na **selecionar itens para cópia de segurança**, clique em **adicionar itens**.
5. Na **selecionar itens**, selecione o que pretende criar cópias de segurança. Em seguida, clique em **OK**.
6. Na **selecionar itens para cópia de segurança** página, clique em **próxima**.
7. Na **Especificar agenda de cópia de segurança** , especifique quando pretender efetuar cópias de segurança diárias ou semanais. Clique depois em **Seguinte**.

    - Um ponto de recuperação é criado quando é feita uma cópia de segurança.
    - O número de pontos de recuperação criados no seu ambiente é depende da sua agenda de cópia de segurança.

1. Pode agendar cópias de segurança diárias, até três vezes por dia. Por exemplo, a captura de ecrã mostra duas cópias de segurança diárias, uma à meia-noite e um para as 18:00.

    ![Agenda diária](./media/backup-configure-vault/day-schedule.png)

9. Também pode executar cópias de segurança semanais. Por exemplo, a captura de ecrã mostra as cópias de segurança criadas todas as alternativas Domingo & quarta-feira às 9:30h e 1:00.

    ![Agendamento semanal](./media/backup-configure-vault/week-schedule.png)

8. Sobre o **selecionar política de retenção** , especifique como armazenar cópias históricas de seus dados. Clique depois em **Seguinte**.

   - Definições de retenção de especificam quais os pontos de recuperação devem ser armazenados e o tempo que devem ser armazenados para.
   - Por exemplo, ao definir uma definição de retenção diárias, indica que no momento especificado para o período de retenção diário, o ponto de recuperação mais recente será retido durante o número especificado de dias. Ou, como outro exemplo, pode especificar uma política de retenção mensais para indicar que o ponto de recuperação criado em 30th de cada mês deve ser armazenado durante 12 meses.
   - Retenção do ponto de recuperação diária e semanal geralmente coincide com a agenda de cópia de segurança. O que significa que, quando a cópia de segurança é acionada de acordo com a agenda, o ponto de recuperação criado pela cópia de segurança é armazenado durante o período indicado na diária ou semanalmente política de retenção.
   - Por exemplo, na seguinte captura de ecrã:
     - Cópias de segurança diárias em meia-noite e 18:00 são mantidas durante sete dias.
     - Cópias de segurança criadas num sábado à meia-noite e 18:00 são mantidas durante 4 semanas.
     - Cópias de segurança criadas no Sábado na última semana do mês em meia-noite e 18:00 são mantidas durante 12 meses. -Cópias de segurança criadas num Sábado na última semana de Março são mantidas há 10 anos.

   ![Exemplo de retenção](./media/backup-configure-vault/retention-example.png)

11. Na **Escolher tipo de cópia de segurança inicial** especificar como tirar inicial, cópia de segurança, através da rede ou offline. Clique depois em **Seguinte**.

10. Na **confirmação**, reveja as informações e, em seguida, clique em **concluir**.
11. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

### <a name="perform-the-initial-backup-offline"></a>Efetuar a cópia de segurança inicial offline

Pode executar um inicial de cópia de segurança automaticamente através da rede ou offline. Seeding offline para uma cópia de segurança inicial é útil se tiver grandes quantidades de dados que irão exigir muita largura de banda de rede para transferir. Faça uma transferência offline da seguinte forma:

1. Escrever os dados de cópia de segurança para uma localização de transição.
2. Utilize a ferramenta de AzureOfflineBackupDiskPrep para copiar os dados de localização de transição para um ou mais discos SATA.
3. A ferramenta cria uma tarefa de importação do Azure. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) sobre a importação do Azure e exportação.
4. Envie os discos SATA num Datacenter do Azure.
5. No datacenter, os dados do disco são copiados para uma conta de armazenamento do Azure.
6. Cópia de segurança do Azure copia os dados da conta de armazenamento para o Cofre e cópias de segurança incrementais.

[Saiba mais](backup-azure-backup-import-export.md) sobre propagação offline.

### <a name="enable-network-throttling"></a>Ativar a limitação de rede

Pode controlar como a largura de banda de rede é utilizada pelo agente de MARS ao ativar a limitação de rede. A limitação é útil se precisar de criar cópias de segurança durante as horas de trabalho, mas se quiser controlar a largura de banda é utilizada para cópia de segurança e restaurar a atividade.

- Rede de cópia de segurança do Azure utiliza a limitação [Quality of Service (QoS)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) no sistema operativo local.
- Limitação para cópia de segurança de rede está disponível no Windows Server 2008 R2 e posteriores e, o seu Windows 7 e posteriores. Sistemas operativos devem estar a executar os service packs mais recentes.

Ative a limitação de rede da seguinte forma:

1. No agente de MARS, clique em **alterar propriedades**.
2. Sobre o **limitação** separador, verificação **ativar a limitação para operações de cópia de segurança de utilização de largura de banda de internet**.

    ![Limitação de rede](./media/backup-configure-vault/throttling-dialog.png)
3. Especifique a largura de banda permitida durante o trabalho e fora do horário de trabalho. Valores de largura de banda começam no 512 Kbps e ir até 1,023 MBps. Em seguida, clique em **OK**.

## <a name="run-an-ad-hoc-backup"></a>Executar cópias de segurança ad hoc

1. No agente de MARS, clique em **cópia de segurança agora**. Isso ativa a replicação inicial através da rede.

    ![Efetuar a cópia de segurança do Windows Server agora](./media/backup-configure-vault/backup-now.png)

2. Na **confirmação**, reveja as definições e clique em **cópia de segurança**.
3. Clique em **Fechar** para fechar o assistente. Se fizer isso, antes da cópia de segurança estar concluído, o assistente continua em execução em segundo plano.

Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

## <a name="next-steps"></a>Passos Seguintes

[Saiba como](backup-azure-restore-windows-server.md) restaurar ficheiros.
