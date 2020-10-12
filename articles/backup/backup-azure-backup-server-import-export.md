---
title: Cópia de segurança offline para o DPM e Azure Backup Server
description: Com a Azure Backup, pode enviar dados para fora da rede utilizando o serviço Azure Import/Export. Este artigo explica o fluxo de trabalho de backup offline para DPM e Azure Backup Server.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 368ae846a24ec04ee4b7da9b5971c00180be611d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89378462"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-mabs"></a>Fluxo de trabalho de backup offline para DPM e Azure Backup Server (MABS)

>[!IMPORTANT]
> Estes passos são aplicáveis para DPM 2019 UR1 (ou superior) e MABS v3 UR1 (ou acima).

O Gestor de Proteção de Dados do Centro de Sistemas e o Servidor de Backup do Azure (MABS) integram-se com o Azure Backup e utilizam várias eficiências incorporadas que poupam custos de rede e armazenamento durante as cópias de segurança completas iniciais dos dados para o Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e requerem mais largura de banda de rede quando comparadas com as cópias de segurança subsequentes que transferem apenas os deltas/incrementais. O Azure Backup comprime as cópias de segurança iniciais. Através do processo de sementeira offline, o Azure Backup pode usar discos para enviar os dados de backup iniciais comprimidos offline para Azure.

O processo de sementeira offline da Azure Backup está fortemente integrado com o [serviço Azure Import/Export](../storage/common/storage-import-export-service.md). Pode utilizar este serviço para transferir dados para a Azure utilizando discos. Se tiver terabytes (TBs) de dados de backup iniciais que precisam de ser transferidos através de uma rede de alta latência e de baixa largura de banda, pode utilizar o fluxo de trabalho de sementeira offline para enviar a cópia de backup inicial num ou mais discos rígidos para um datacenter Azure. Este artigo fornece uma visão geral e outros passos que terminam este fluxo de trabalho para System Center Data Protection Manager (DPM) e Microsoft Azure Backup Server (MABS).

> [!NOTE]
> O processo de cópia de segurança offline para o Agente microsoft Azure Recovery Services (MARS) é distinto do DPM e do MABS. Para obter informações sobre a utilização de cópias de segurança offline com o Agente MARS, consulte [o fluxo de trabalho de backup offline no Azure Backup](backup-azure-backup-import-export.md). A cópia de segurança offline não é suportada por cópias de segurança do estado do sistema feitas utilizando o Agente de Backup Azure.
>
> A atualização MABS UR1 também traz a pré-visualização para Cópia de Segurança Offline utilizando a Caixa de Dados Azure em MABS. Contato [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) para saber mais.

## <a name="overview"></a>Descrição geral

Com a capacidade de sementeira offline do Azure Backup e do serviço Azure Import/Export, é simples enviar os dados offline para Azure utilizando discos. O processo de backup offline envolve os seguintes passos:

> [!div class="checklist"]
>
> * Os dados de backup são escritos para um local de preparação em vez de serem enviados através da rede.
> * Os dados sobre a localização da encenação são então escritos para um ou mais discos SATA utilizando o utilitário *AzureOfflineBacklineBackDiskPrep.*
> * Um trabalho de importação Azure é automaticamente criado pela utilidade.
> * As unidades SATA são então enviadas para o centro de dados Azure mais próximo.
> * Após o envio dos dados de backup para a Azure, o Azure Backup copia os dados de backup para o cofre de backup e as cópias de segurança incrementais estão agendadas.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de iniciar o fluxo de trabalho de backup offline:

* Foi criado [um cofre dos Serviços de Recuperação.](backup-azure-recovery-services-vault-overview.md) Para criar um, siga os passos no [Create a Recovery Services vault](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)tutorial-backup-windows-server-to-azure#create-a-recovery-services-vault).
* Certifique-se de que apenas a [versão mais recente do agente microsoft Azure Recovery Services](https://aka.ms/azurebackup_agent) está instalada no SC DPM ou MABS e registada no cofre dos Serviços de Recuperação.
* Update Rollup 1 está instalado no SC DPM 2019 ou MABS v3.

  > [!NOTE]
  > Com DPM 2019 UR1 e MABS v3 UR1, a sementeira offline autentica usando o Azure Ative Directory.

* No servidor DPM ou MABS, certifique-se de que o Microsoft Edge ou o Internet Explorer 11 estão instalados e o JavaScript está ativado.
* Crie uma conta de Armazenamento Azure na mesma subscrição que o cofre dos Serviços de Recuperação.
* Certifique-se de que tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar a aplicação Azure Ative Directory. O fluxo de trabalho de Backup Offline cria uma aplicação Azure Ative Directory na subscrição associada à conta de Armazenamento Azure. O objetivo da aplicação é fornecer ao Azure Backup um acesso seguro e alargado ao Serviço de Importação Azure, necessário para o fluxo de trabalho de backup offline.
* Registe o fornecedor de recursos Microsoft.ImportExport com a subscrição que contém a conta de Armazenamento Azure. Para registar o fornecedor de recursos:
    1. No menu principal, **selecione Subscrições**.
    2. Se estiver subscrito em várias subscrições, selecione a subscrição que está a utilizar para a cópia de segurança offline. Se utilizar apenas uma subscrição, a sua assinatura aparece.
    3. No menu de subscrição, selecione **Fornecedores de Recursos** para ver a lista de fornecedores.
    4. Na lista de fornecedores desloque-se até microsoft.ImportExport. Se o Estado não estiver registado, **selecione Registar-se**.

       ![Registo do fornecedor de recursos](./media/backup-azure-backup-server-import-export/register-import-export.png)

* É criada uma localização de encenação, que pode ser uma partilha de rede ou qualquer unidade adicional no computador, interna ou externa, com espaço suficiente em disco para segurar a sua cópia inicial. Por exemplo, se pretender fazer o back up de um servidor de ficheiros de 500 GB, certifique-se de que a área de paragem é de pelo menos 500 GB. (Uma quantidade menor é utilizada devido à compressão.)
* Para os discos enviados para a Azure, certifique-se de que apenas são utilizados discos rígidos internos SATA II/III de 2,5 polegadas ou 3,5 polegadas. Pode utilizar discos rígidos até 10 TB. Consulte a documentação do [serviço Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) para obter o mais recente conjunto de unidades que o serviço suporta.
* As unidades SATA devem ser ligadas a um computador (referido como *um computador de cópia*) de onde é feita a cópia dos dados de backup do local de preparação para as unidades SATA. Certifique-se de que o BitLocker está ativado no computador de cópia.

## <a name="workflow"></a>Fluxo de trabalho

As informações nesta secção ajudam-no a completar o fluxo de trabalho de backup offline para que os seus dados possam ser entregues num datacenter Azure e enviados para o Azure Storage. Se tiver dúvidas sobre o serviço Import ou qualquer aspeto do processo, consulte a documentação geral do [serviço De importação](../storage/common/storage-import-export-service.md) referenciada anteriormente.

## <a name="initiate-offline-backup"></a>Iniciar backup offline

1. Quando criar um novo grupo de proteção com proteção on-line ou adicionar proteção on-line ao grupo de proteção existente, vê o seguinte ecrã. Para selecionar o método inicial de replicação on-line, selecione **Transferir usando o meu próprio disco** e selecione **Next**.

    ![Tela de importação](./media/backup-azure-backup-server-import-export/create-new-protection-group.png)

2. A página de inscrição do Azure abrirá. Inscreva-se na sua conta de utilizador Azure, que tem permissão de função *do proprietário* na Subscrição Azure.

    ![Página de inscrição de Azure](./media/backup-azure-backup-server-import-export/choose-initial-online-replication.png)

3. Forneça as entradas na página **'Utilizar o seu próprio disco'.**

   ![Entradas para utilizar o seu próprio disco](./media/backup-azure-backup-server-import-export/use-your-own-disk.png)

   A descrição das entradas é a seguinte:

   * **Localização de localização**: Local de armazenamento temporário para o qual está escrita a cópia de reserva inicial. A localização da paragem pode estar numa partilha de rede ou num computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho completo da rede do local de paragem.
   * **Conta de Armazenamento do Gestor de Recursos Azure**: O nome da conta de armazenamento do tipo de gestor de recursos (finalidade geral v1 ou finalidade geral v2) em qualquer subscrição do Azure.
   * **Recipiente de armazenamento Azure**: O nome do recipiente de armazenamento do blob de destino na conta de armazenamento Azure onde os dados de reserva são importados.
   * **ID de assinatura Azure**: O ID para a subscrição Azure onde a conta de armazenamento Azure é criada.
   * **Nome de emprego de importação Azure**: O nome único pelo qual o serviço Azure Import e a Azure Backup acompanham a transferência de dados enviados em discos para a Azure.

    Guarde a **localização de preparação** e as informações sobre o **Nome do Trabalho da Importação de Azure** que forneceu. É necessário preparar os discos.

4. Preencha o fluxo de trabalho para criar ou atualizar a proteção. E para iniciar a cópia de backup offline, clique com o botão direito do **Grupo de Proteção**e, em seguida, escolha a opção **Ponto de recuperação Criar.** Em seguida, escolha a opção **Proteção Online.**

   ![Criar ponto de recuperação](./media/backup-azure-backup-server-import-export/create-recovery-point.png)

5. Monitorize o trabalho de Criação de Réplicas Online no painel de monitorização. O trabalho deve ser concluído com sucesso com o aviso À espera que o *trabalho da Azure Import termine.*

   ![Ponto de recuperação completo](./media/backup-azure-backup-server-import-export/complete-recovery-point.png)

6. Após o fim da operação, o local de preparação está pronto para ser utilizado para a preparação do disco.

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Prepare as unidades da SATA e envie-a para Azure

O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas para o centro de dados Azure mais próximo. Este utilitário está disponível no diretório de instalação do agente Azure Backup (no seguinte caminho): `*\Microsoft Azure Recovery Services Agent\Utils\\*`

1. Vá ao diretório e copie o diretório **AzureOfflineBackupDiskPrep** para outro computador onde as unidades SATA estão ligadas. No computador com as unidades SATA ligadas, certifique-se:

   * O computador de cópia pode aceder ao local de preparação para o fluxo de trabalho de sementeira offline utilizando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na secção "Iniciar cópia de segurança offline".
   * O BitLocker está ativado no computador de cópia.
   * O Azure PowerShell 3.7.0 está instalado no computador Copy (não é necessário se estiver a executar o utilitário AzureOfflineBackupDiskPrep no servidor DPM ou MABS).
   * Os navegadores mais recentes compatíveis (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está ativado.
   * O computador de cópia pode aceder ao portal Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, então é obrigatório usar um servidor físico diferente ou uma máquina de cliente como o computador de cópia.

1. Abra um pedido de comando elevado no computador de cópia com o diretório de utilidade *AzureOfflineBackupDiskPrep* como o diretório atual. Execute o seguinte comando:

    ```console
    .\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>
    ```

    | Parâmetro | Descrição |
    | --- | --- |
    | s: &lt; *Caminho de localização de encenação*&gt; |Esta entrada obrigatória é utilizada para fornecer o caminho para o local de preparação que inseriu no fluxo de trabalho na secção "Iniciar backup offline". |
    | p: &lt; *Caminho para publicarSettingsFile*&gt; |Esta entrada opcional é utilizada para fornecer o caminho para o ficheiro de definições de publicação Azure. |

    Quando você dirige o comando, o utilitário solicita a seleção do trabalho Azure Import que corresponde às unidades que precisam de ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de paragem fornecido, você vê um ecrã como o que se segue.

      ![Consola de preparação de discos](./media/backup-azure-backup-server-import-export/disk-prep-console.png)

1. Introduza a letra de unidade sem o cólon de fuga para o disco montado que deseja preparar para a transferência para Azure.
1. Forneça a confirmação da formatação da unidade quando solicitado.
1. É-lhe pedido que assine a sua assinatura Azure. Forneça as suas credenciais.

    ![Ecrã de inscrição de Azure](./media/backup-azure-backup-server-import-export/signin-disk-prep.png)

    A ferramenta começa então a preparar o disco e a copiar os dados de cópia de segurança. Pode ser necessário anexar discos adicionais quando solicitado pela ferramenta se o disco fornecido não tiver espaço suficiente para os dados de cópia de segurança. <br/>

    No final da execução bem sucedida da ferramenta, o pedido de comando fornece três peças de informação:
    * Um ou mais discos que forneceu estão preparados para o envio para Azure.
    * Recebe a confirmação de que o seu trabalho de importação foi criado. O trabalho de importação usa o nome que forneceu.
    * A ferramenta exibe o endereço de envio do datacenter Azure.

     ![Preparação do disco azul completa](./media/backup-azure-backup-server-import-export/console.png)

1. No final da execução do comando, também vê a opção de atualizar as informações de envio.

1. Envie os discos para o endereço que a ferramenta forneceu e mantenha o número de rastreio para referência futura.

   > [!IMPORTANT]
   > Nenhum dos dois postos de trabalho de importação da Azure pode ter o mesmo número de rastreio. Certifique-se de que os impulsos preparados pela utilidade sob uma única tarefa de importação Azure são enviados juntos num único pacote e que há um único número de rastreamento único para o pacote. Não combine impulsos preparados como parte de diferentes trabalhos de importação da Azure num único pacote.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes de envio sobre o trabalho de importação de Azure

O procedimento seguinte atualiza os detalhes do envio de emprego da Azure Import. Estas informações incluem detalhes sobre:

* o nome da transportadora que entrega os discos ao Azure
* devolução de dados para os seus discos

   1. Inscreva-se na sua assinatura Azure.
   2. No menu principal, selecione **Todos os serviços** e no diálogo de todos os serviços, escreva Import. Quando vir **empregos de importação/exportação,** selecione-o.
       ![Insira informações de envio](./media/backup-azure-backup-server-import-export/search-import-job.png)

       Abre a lista do menu de **empregos de importação/exportação** e aparece a lista de todos os postos de trabalho de importação/exportação na subscrição selecionada.

   3. Se tiver várias subscrições, não se esqueça de selecionar a subscrição utilizada para importar os dados de backup. Em seguida, selecione o recém-criado trabalho de Importação para abrir os seus detalhes.

       ![Rever informações de envio](./media/backup-azure-backup-server-import-export/import-job-found.png)

   4. No menu Definições para o trabalho De Importação, **selecione Gerir informações de envio** e insira os detalhes do envio de retorno.

       ![Armazenar informações de envio](./media/backup-azure-backup-server-import-export/shipping-info.png)

   5. Quando tiver o número de rastreio da sua transportadora de envio, selecione o banner na página geral de trabalho da Azure Import e introduza os seguintes detalhes:

      > [!IMPORTANT]
      > Confirme que as informações da transportadora e que o número de acompanhamento estão atualizados num prazo de duas semanas após a criação do trabalho de importação do Azure. A não verificação desta informação no prazo de duas semanas pode resultar na perda do trabalho e na não formação.

      ![Visão geral do trabalho](./media/backup-azure-backup-server-import-export/job-overview.png)

      ![Informação de rastreio](./media/backup-azure-backup-server-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Hora de processar as unidades

O tempo que demora a processar um trabalho de importação da Azure varia. O tempo de processo depende de fatores como o tempo de envio, o tipo de trabalho, o tipo e o tamanho dos dados que estão a ser copiados e o tamanho dos discos fornecidos. O serviço Azure Import/Export não tem SLA. Após a recebidação dos discos, o serviço esforça-se por terminar a cópia de dados de backup na sua conta de armazenamento Azure em 7 a 10 dias. A secção seguinte descreve como pode monitorizar o estado do trabalho de importação do Azure.

### <a name="monitor-azure-import-job-status"></a>Monitor Azure importe estatuto de trabalho

Pode monitorizar o estado do seu trabalho de Importação a partir do portal Azure navegando na página **de empregos de Importação/Exportação** e selecionando o seu trabalho. Para obter mais informações sobre o estado dos postos de trabalho nas importações, consulte o artigo do [serviço de exportação de importações de armazenamento.](../storage/common/storage-import-export-service.md)

### <a name="complete-the-workflow"></a>Complete o fluxo de trabalho

Após o fim do trabalho de importação, os dados de backup iniciais estão disponíveis na sua conta de armazenamento. No momento da próxima cópia agendada, a Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação.

No momento do próximo trabalho de criação de réplicas online programado, o Data Protection Manager realiza backup incremental sobre a cópia inicial de backup.

## <a name="next-steps"></a>Passos seguintes

* Para qualquer dúvida sobre o fluxo de trabalho do serviço Azure Import/Export, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento blob](../storage/common/storage-import-export-service.md).
