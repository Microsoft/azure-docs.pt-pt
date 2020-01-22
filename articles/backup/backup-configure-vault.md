---
title: Fazer backup de computadores Windows com o agente MARS
description: Use o agente MARS (serviços de recuperação da Microsoft) de backup do Azure para fazer backup de computadores Windows.
ms.topic: conceptual
ms.date: 06/04/2019
ms.openlocfilehash: c6e5ea1ed1ec9dd922793dfc6834238c431ddc38
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/07/2020
ms.locfileid: "76290874"
---
# <a name="back-up-windows-machines-with-the-azure-backup-mars-agent"></a>Fazer a cópia de segurança de computadores Windows com o agente MARS do Azure Backup

Este artigo explica como fazer backup de computadores Windows usando o serviço de [backup do Azure](backup-overview.md) e o agente de serviços de recuperação do Microsoft Azure (MARS), também conhecido como o agente de backup do Azure.

Neste artigo, vai aprender a:

> [!div class="checklist"]
>
> * Verifique os pré-requisitos e crie um cofre dos serviços de recuperação.
> * Transferir e configurar o Agente MARS
> * Crie uma política e um agendamento de backup.
> * Execute um backup sob demanda.

## <a name="about-the-mars-agent"></a>Sobre o agente MARS

O agente MARS é usado pelo backup do Azure para fazer backup de arquivos, pastas e estado do sistema de máquinas locais e VMs do Azure para um cofre de serviços de recuperação de backup no Azure. Você pode executar o agente da seguinte maneira:

* Execute o agente diretamente em máquinas locais do Windows para que eles possam fazer backup diretamente em um cofre de serviços de recuperação de backup no Azure.
* Execute o agente em VMs do Azure executando o Windows (lado a lado com a extensão de backup da VM do Azure) para fazer backup de arquivos e pastas específicos na VM.
* Execute o agente em um servidor de Backup do Microsoft Azure (MABS) ou um servidor do System Center Data Protection-Manager (DPM). Nesse cenário, as máquinas e as cargas de trabalho fazem backup no MABS/DPM e, em seguida, o MABS/DPM faz backup de um cofre no Azure usando o agente MARS.
O que você pode fazer backup depende de onde o agente está instalado.

> [!NOTE]
> O método principal para fazer backup de VMs do Azure é usando uma extensão de backup do Azure na VM. Isso faz o backup de toda a VM. Talvez você queira instalar e usar o agente MARS junto com a extensão se quiser fazer backup de arquivos e pastas específicos na VM. [Saiba mais](backup-architecture.md#architecture-built-in-azure-vm-backup).

![Etapas do processo de backup](./media/backup-configure-vault/initial-backup-process.png)

## <a name="before-you-start"></a>Antes de começar

* [Saiba como](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) O backup do Azure faz backup de computadores Windows com o agente MARS.
* [Saiba mais sobre](backup-architecture.md#architecture-back-up-to-dpmmabs) a arquitetura de backup que executa o agente Mars em um servidor secundário mAbs ou DPM.
* [Examine](backup-support-matrix-mars-agent.md) o que tem suporte e o que pode ser feito backup com o agente Mars.
* Verifique o acesso à Internet nos computadores que você deseja fazer backup.
* Para fazer backup de um servidor ou cliente no Azure, você precisa de uma conta do Azure. Se você não tiver uma, poderá criar uma [conta gratuita](https://azure.microsoft.com/free/) em apenas alguns minutos.

### <a name="verify-internet-access"></a>Verificar acesso à Internet

Se o seu computador tiver acesso limitado à Internet, verifique se as configurações de firewall no computador ou proxy permitem essas URLs e o endereço IP:

#### <a name="urls"></a>URLs

* www\.msftncsi.com
* *.Microsoft.com
* *.WindowsAzure.com
* *.microsoftonline.com
* *.windows.net

#### <a name="ip-addresses"></a>Endereços IP

* 20.190.128.0/18
* 40.126.0.0/18

O acesso a todas as URLs e endereços IP listados acima usa o protocolo HTTPS na porta 443.

## <a name="create-a-recovery-services-vault"></a>Criar um cofre dos Serviços de Recuperação

Um cofre dos serviços de recuperação armazena todos os pontos de backup e recuperação que você cria ao longo do tempo e contém a política de backup aplicada a computadores de backup. Crie um cofre da seguinte maneira:

1. Inicie sessão no [portal do Azure](https://portal.azure.com/) com a sua subscrição do Azure.

2. Pesquise e selecione **cofres dos serviços de recuperação**.

    ![Passo 1 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-configure-vault/open-recovery-services-vaults.png)

3. No menu **cofres dos serviços de recuperação** , clique em **+ Adicionar**.

    ![Passo 2 da Criação do Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-menu.png)

4. Para o **Nome**, introduza um nome amigável para identificar o cofre.

   * O nome tem de ser exclusivo para a subscrição do Azure.
   * Ele pode conter de 2 a 50 caracteres.
   * Ele deve começar com uma letra e pode conter apenas letras, números e hifens.

5. Selecione a assinatura do Azure, o grupo de recursos e a região geográfica na qual o cofre deve ser criado. Os dados de backup são enviados para o cofre. Em seguida, clique em **Criar**.

    ![Passo 3 da Criação de um Cofre dos Serviços de Recuperação](./media/backup-try-azure-backup-in-10-mins/rs-vault-step-3.png)

   * Pode levar algum tempo para que o cofre seja criado.
   * Monitore as notificações de status na área superior direita do Portal. Se depois de alguns minutos você não vir o cofre, clique em **Atualizar**.

     ![Clique no botão Atualizar](./media/backup-try-azure-backup-in-10-mins/refresh-button.png)

### <a name="set-storage-redundancy"></a>Definir redundância de armazenamento

O backup do Azure manipula automaticamente o armazenamento para o cofre. Você precisa especificar como esse armazenamento é replicado.

1. No painel **Cofres dos Serviços de Recuperação**, clique em novo cofre. Na seção **configurações** , clique em **Propriedades**.
2. Em **Propriedades**, em **configuração de backup**, clique em **Atualizar**.

3. Selecione o tipo de replicação de armazenamento e clique em **salvar**.

      ![Definir a configuração de armazenamento do novo cofre](./media/backup-try-azure-backup-in-10-mins/recovery-services-vault-backup-configuration.png)

* Recomendamos que, se você estiver usando o Azure como um ponto de extremidade de armazenamento de backup primário, continue a usar a configuração padrão **com redundância geográfica** .
* Se não utilizar o Azure como um ponto final do armazenamento de cópia de segurança primário, escolha **Localmente redundante**, o que reduz os custos de armazenamento do Azure.
* Saiba mais sobre a redundância [geográfica](../storage/common/storage-redundancy-grs.md) e [local](../storage/common/storage-redundancy-lrs.md) .

## <a name="download-the-mars-agent"></a>Baixar o agente MARS

Baixe o agente MARS para instalação em computadores dos quais você deseja fazer backup.

* Se você já tiver instalado o agente em qualquer computador, verifique se está executando a versão mais recente.
* A versão mais recente está disponível no portal ou usando um [download direto](https://aka.ms/azurebackup_agent)

1. No cofre, em **introdução**, clique em **backup**.

    ![Painel Abrir objetivo de cópia de segurança](./media/backup-try-azure-backup-in-10-mins/open-backup-settings.png)

2. Em **onde sua carga de trabalho está em execução?** , selecione **local**. Você deve selecionar essa opção mesmo se quiser instalar o agente MARS em uma VM do Azure.
3. Em **o que você deseja fazer backup?** , selecione **arquivos e pastas** e/ou **estado do sistema**. Há várias outras opções disponíveis, mas elas só têm suporte se você estiver executando um servidor de backup secundário. Clique em **preparar infraestrutura**.

      ![Configurar ficheiros e pastas](./media/backup-try-azure-backup-in-10-mins/set-file-folder.png)

4. Na **infraestrutura preparar**, em **instalar o agente dos serviços de recuperação**, baixe o agente Mars.

    ![preparar infraestrutura](./media/backup-try-azure-backup-in-10-mins/choose-agent-for-server-client.png)

5. No menu de pop-up de transferência, clique em **Guardar**. Por predefinição, o ficheiro **MARSagentinstaller.exe** é guardado na pasta Transferências.

6. Agora, verifique se **já fez download ou usando o agente de serviços de recuperação mais recente**e baixe as credenciais do cofre.

    ![transferir as credenciais do cofre](./media/backup-try-azure-backup-in-10-mins/download-vault-credentials.png)

7. Clique em **Guardar**. O arquivo é baixado para a pasta de download. Você não pode abrir o arquivo de credenciais do cofre.

## <a name="install-and-register-the-agent"></a>Instalar e registar o agente

1. Execute o arquivo **MARSagentinstaller. exe** em computadores dos quais você deseja fazer backup.
2. No assistente de instalação do agente MARS > **configurações de instalação**, especifique onde você deseja instalar o agente e um local a ser usado para o cache. Clique depois em **Seguinte**.
   * O backup do Azure usa o cache para armazenar instantâneos de dados antes de enviá-los para o Azure.
   * O local do cache deve ter espaço livre igual a pelo menos 5% do tamanho dos dados que você fará backup.

    ![Configurações de instalação do assistente de MARS](./media/backup-configure-vault/mars1.png)

3. Em **configuração de proxy**, especifique como o agente em execução no computador Windows se conectará à Internet. Clique depois em **Seguinte**.

   * Se você estiver usando um proxy personalizado, especifique as configurações de proxy e as credenciais, se necessário.
   * Lembre-se de que o agente precisa de acesso a [essas URLs](#verify-internet-access).

     ![Assistente de MARS acesso à Internet](./media/backup-configure-vault/mars2.png)

4. Em **instalação** , examine a verificação de pré-requisitos e clique em **instalar**.
5. Depois que o agente for instalado, clique em **prosseguir para o registro**.
6. No **Assistente para registrar servidor** > **identificação do cofre**, procure e selecione o arquivo de credenciais que você baixou. Clique depois em **Seguinte**.

    ![Credenciais do registrador de cofre](./media/backup-configure-vault/register1.png)

7. Em **configuração de criptografia**, especifique uma senha que será usada para criptografar e descriptografar backups para o computador.

    * Guarde a frase de acesso de encriptação numa localização segura.
    * Se você perder ou esquecer a senha, a Microsoft não poderá ajudar a recuperar os dados de backup. Guarde o ficheiro numa localização segura. Você precisa dele para restaurar um backup.

8. Clique em **Concluir**. O agente está agora instalado e a máquina está registada no cofre. Está pronto para configurar e agendar a cópia de segurança.

## <a name="create-a-backup-policy"></a>Criar uma política de backup

A política de backup especifica quando tirar instantâneos dos dados para criar pontos de recuperação e por quanto tempo os pontos de recuperação são mantidos.

* Você configura uma política de backup usando o agente MARS.
* O backup do Azure não faz a conta de horário de Verão (DST) automaticamente. Isso pode causar alguma discrepância entre a hora real e o horário de backup agendado.

Crie uma política da seguinte maneira:
1. Depois de baixar e registrar o agente MARS, inicie o console do agente. Pode encontrá-lo ao pesquisar na máquina por **Cópia de Segurança do Microsoft Azure**.  
2. Em **ações**, clique em **agendar backup**.

    ![Agendar uma cópia de segurança do Windows Server](./media/backup-configure-vault/schedule-first-backup.png)
3. No assistente de agendamento de backup > **Guia de introdução**, clique em **Avançar**.
4. Em **selecionar itens para fazer backup**, clique em **Adicionar itens**.

    ![Selecionar itens para backup](./media/backup-azure-manage-mars/select-item-to-backup.png)

5. Em **selecionar itens**, selecione o que você deseja fazer backup e clique em **OK**.

    ![Itens selecionados para backup](./media/backup-azure-manage-mars/selected-items-to-backup.png)

6. Na página **selecionar itens para fazer backup** , clique em **Avançar**.
7. Na página **especificar agendamento de backup** , especifique quando deseja fazer backups diários ou semanais. Clique depois em **Seguinte**.

    - Um ponto de recuperação é criado quando um backup é feito.
    - O número de pontos de recuperação criados em seu ambiente depende do seu agendamento de backup.


8. Você pode agendar backups diários, até três vezes por dia. Por exemplo, a captura de tela mostra dois backups diários, um à meia-noite e um às 6:00 PM.

    ![Agenda diária](./media/backup-configure-vault/day-schedule.png)


9. Você também pode executar backups semanais. Por exemplo, a captura de tela mostra os backups feitos a cada domingo alternativo & quarta-feira às 9:30 A.M. e 1:00.

    ![Agenda semanal](./media/backup-configure-vault/week-schedule.png)


10. Na página **selecionar política de retenção** , especifique como você armazena as cópias históricas dos seus dados. Clique depois em **Seguinte**.

    - As configurações de retenção especificam quais pontos de recuperação devem ser armazenados e por quanto tempo eles devem ser armazenados.
    - Por exemplo, quando você define uma configuração de retenção diária, indica que no momento especificado para a retenção diária, o último ponto de recuperação será retido para o número de dias especificado. Ou, como outro exemplo, você pode especificar uma política de retenção mensal para indicar que o ponto de recuperação criado no dia 30 de cada mês deve ser armazenado por 12 meses.
    - A retenção de ponto de recuperação diária e semanal geralmente coincide com o agendamento de backup. O que significa que, quando o backup é disparado de acordo com o agendamento, o ponto de recuperação criado pelo backup é armazenado pela duração indicada na política de retenção diária ou semanal.
    - Como exemplo, na seguinte captura de tela:

        -   Os backups diários à meia-noite e 6:00 PM são mantidos por sete dias.
        -   Os backups feitos em um sábado à meia-noite e 6:00 PM são mantidos por quatro semanas.
        -   Os backups feitos no sábado na última semana do mês à meia-noite e 6:00 PM são mantidos por 12 meses.
        -   Os backups feitos em um sábado na última semana de março são mantidos por 10 anos.

    ![Exemplo de retenção](./media/backup-configure-vault/retention-example.png)


11. Em **escolher tipo de backup inicial** , decida se deseja fazer o backup inicial pela rede ou usar o backup offline (para obter mais informações sobre o backup offline, consulte este [artigo](backup-azure-backup-import-export.md)). Para fazer o backup inicial pela rede, selecione **automaticamente pela rede** e clique em **Avançar**.

    ![Tipo de backup inicial](./media/backup-azure-manage-mars/choose-initial-backup-type.png)


12. Em **confirmação**, examine as informações e clique em **concluir**.

    ![Confirmar tipo de backup](./media/backup-azure-manage-mars/confirm-backup-type.png)


13. Depois de o assistente ter criado a agenda da cópia de segurança, clique em **Fechar**.

    ![Confirmar o processo de modificação de backup](./media/backup-azure-manage-mars/confirm-modify-backup-process.png)

Você deve criar uma política em cada computador em que o agente está instalado.

### <a name="perform-the-initial-backup-offline"></a>Executar o backup inicial offline

Você pode executar um backup inicial automaticamente pela rede ou offline. A propagação offline para um backup inicial será útil se você tiver grandes quantidades de dados que exigirão muita largura de banda de rede para transferência. Faça uma transferência offline da seguinte maneira:

1. Você grava os dados de backup em um local de preparo.
2. Use a ferramenta AzureOfflineBackupDiskPrep para copiar os dados do local de preparo para um ou mais discos SATA.
3. A ferramenta cria um trabalho de importação do Azure. [Saiba mais](https://docs.microsoft.com/azure/storage/common/storage-import-export-service) sobre a importação e exportação do Azure.
4. Você envia os discos SATA para um datacenter do Azure.
5. No datacenter, os dados do disco são copiados para uma conta de armazenamento do Azure.
6. O backup do Azure copia os dados da conta de armazenamento para o cofre e os backups incrementais são agendados.

[Saiba mais](backup-azure-backup-import-export.md) sobre a propagação offline.

### <a name="enable-network-throttling"></a>Habilitar limitação de rede

Você pode controlar como a largura de banda de rede é usada pelo agente MARS habilitando a limitação de rede. A limitação será útil se você precisar fazer backup de dados durante o horário de trabalho, mas quiser controlar a quantidade de largura de banda usada para a atividade de backup e restauração.

* A limitação de rede de backup do Azure usa [QoS (qualidade de serviço)](https://docs.microsoft.com/windows-server/networking/technologies/qos/qos-policy-top) no sistema operacional local.
* A limitação de rede para o backup está disponível no Windows Server 2012 em diante e no Windows 8 em diante. Os sistemas operacionais devem estar executando os service packs mais recentes.

Habilite a limitação de rede da seguinte maneira:

1. No agente MARS, clique em **alterar propriedades**.
2. Na guia **limitação** , marque **habilitar limitação do uso de largura de banda da Internet para operações de backup**.

    ![Limitação da rede](./media/backup-configure-vault/throttling-dialog.png)
3. Especifique a largura de banda permitida durante o trabalho e fora do horário de trabalho. Os valores de largura de banda começam em 512 kbps e vão até 1.023 MBps. Em seguida, clique em **OK**.

## <a name="run-an-on-demand-backup"></a>Executar um backup sob demanda

1. No agente MARS, clique em **fazer backup agora**.

    ![Efetuar a cópia de segurança do Windows Server agora](./media/backup-configure-vault/backup-now.png)

2. Se a versão do agente MARS for 2.0.9169.0 ou mais recente, uma retenção personalizada poderá ser definida. Na seção **manter backup até** , escolha uma data no calendário apresentado:

   ![Manter o calendário de backup](./media/backup-configure-vault/mars-ondemand.png)

3. Em **confirmação**, examine as configurações e clique em **fazer backup**.
4. Clique em **Fechar** para fechar o assistente. Se você fizer isso antes de o backup ser concluído, o assistente continuará a ser executado em segundo plano.
5. Depois de concluída a cópia de segurança inicial, o estado **Tarefa concluída** é apresentado na consola de Cópia de Segurança.

## <a name="on-demand-backup-policy-retention-behavior"></a>Comportamento de retenção da política de backup sob demanda

>[!NOTE]
>Aplicável somente a versões de agente MARS anteriores a 2.0.9169.0
>

* Para obter mais informações, consulte a etapa 8 de [criar uma política de backup](backup-configure-vault.md#create-a-backup-policy)

| Opção de agendamento de backup | Por quanto tempo os dados submetidos a backup serão retidos?
| -- | --
| Agendar um backup a cada: * dia | **Retenção padrão**: equivalente à "retenção em dias para backups diários" <br/><br/> **Exceção**: se um conjunto de backup agendado diário para retenção de longo prazo (semanas, meses, anos) falhar, um backup sob demanda será disparado logo após esse backup agendado com falha ser considerado para retenção de longo prazo. Caso contrário, o próximo backup agendado será considerado para retenção de longo prazo.<br/><br/> **Exemplo**: se (digamos) o backup agendado feito na quinta-feira 8:00 falhar e o mesmo backup tiver sido considerado para retenção semanal/mensal/anual, o primeiro backup sob demanda disparado antes do próximo backup agendado (digamos) sexta-feira, 8:00 será marcado automaticamente para a retenção semanal/mensal/anual, conforme aplicável ao backup da quinta-feira 8:00.
| Agendar um backup a cada: * semanalmente | **Retenção padrão**: 1 dia <br/> Backups sob demanda feitos para uma fonte de dados com política de backup semanal são excluídos no dia seguinte, mesmo se forem os backups mais recentes para a fonte de dados. <br/><br/> **Exceção**: se um conjunto de backup semanal agendado para retenção de longo prazo (semanas, meses, anos) falhar, um backup sob demanda será disparado logo após esse backup agendado com falha ser considerado para retenção de longo prazo. Caso contrário, o próximo backup agendado será considerado para retenção de longo prazo. <br/><br/> **Exemplo**: se (digamos) o backup agendado realizado na quinta-feira 8:00 falha e o mesmo backup foi considerado para retenção mensal/anual, o primeiro backup sob demanda disparado antes do próximo backup agendado (digamos), 8:00, será marcado automaticamente para retenção mensal/anual, conforme aplicável ao backup da quinta-feira 8:00

## <a name="next-steps"></a>Passos seguintes

[Saiba como restaurar arquivos](backup-azure-restore-windows-server.md).
