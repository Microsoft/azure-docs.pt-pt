---
title: Backup offline de sementes com o serviço Azure Import/Export
description: Saiba como pode utilizar o Azure Backup para enviar dados para fora da rede utilizando o serviço Azure Import/Export. Este artigo explica a sementeira offline dos dados de backup iniciais utilizando o serviço Azure Import/Export.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 3ea470c2e732b7e0ef46e9e5fa78c744aa30c955
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/22/2021
ms.locfileid: "98704368"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de backup offline em Azure Backup

O Azure Backup tem várias eficiências incorporadas que poupam custos de rede e armazenamento durante as cópias de segurança completas iniciais de dados para o Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e requerem mais largura de banda de rede quando comparadas com as cópias de segurança subsequentes que transferem apenas os deltas/incrementais. Através do processo de sementeira offline, o Azure Backup pode usar discos para enviar os dados de backup offline para o Azure.

O processo de sementeira offline Azure Backup está fortemente integrado com o [serviço Azure Import/Export](../import-export/storage-import-export-service.md). Pode utilizar este serviço para transferir dados de backup iniciais para a Azure utilizando discos. Se tiver terabytes (TBs) de dados de backup iniciais que precisam de ser transferidos através de uma rede de alta latência e de baixa largura de banda, pode utilizar o fluxo de trabalho de sementeira offline para enviar a cópia de backup inicial, em um ou mais discos rígidos para um centro de dados Azure. A imagem a seguir fornece uma visão geral dos passos no fluxo de trabalho.

  ![Visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de backup offline envolve estes passos:

1. Em vez de enviar os dados de backup através da rede, escreva os dados de backup para um local de preparação.
1. Utilize o utilitário *AzureOfflineBacklineBackdiskPrep* para escrever os dados no local de localização para um ou mais discos SATA.
1. Como parte dos trabalhos preparatórios, o utilitário *AzureOfflineBackupDiskPrep* cria um trabalho de importação Azure. Envie as unidades da SATA para o datacenter Azure mais próximo e refira-se ao trabalho de importação para ligar as atividades.
1. No datacenter Azure, os dados dos discos são copiados para uma conta de armazenamento Azure.
1. A Azure Backup copia os dados de backup da conta de armazenamento para o cofre dos Serviços de Recuperação, e estão agendadas cópias de segurança incrementais.

## <a name="supported-configurations"></a>Configurações suportadas

As seguintes funcionalidades ou cargas de trabalho do Azure Backup suportam a utilização de cópias de segurança offline para:

> [!div class="checklist"]
>
> * Cópia de segurança de ficheiros e pastas com o Agente microsoft Azure Recovery Services (MARS), também referido como O Agente de Backup Azure.
> * Cópia de segurança de todas as cargas de trabalho e ficheiros com o Gestor de Proteção de Dados do Centro de Sistema (DPM).
> * Cópia de segurança de todas as cargas de trabalho e ficheiros com o Microsoft Azure Backup Server.

   > [!NOTE]
   > A cópia de segurança offline não é suportada por cópias de segurança do estado do sistema feitas utilizando o Agente de Backup Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os seguintes pré-requisitos e fluxo de trabalho aplicam-se apenas à cópia de segurança offline de ficheiros e pastas utilizando o [mais recente Agente de Serviços de Recuperação do Azure](https://aka.ms/azurebackup_agent). Para realizar cópias de segurança offline para cargas de trabalho utilizando o System Center DPM ou o Azure Backup Server, consulte [o fluxo de trabalho de backup offline para DPM e Azure Backup Server](backup-azure-backup-server-import-export.md).

Antes de iniciar o fluxo de trabalho de backup offline, complete os seguintes pré-requisitos:

* Criar um [cofre dos Serviços de Recuperação.](backup-azure-recovery-services-vault-overview.md) Para criar um cofre, siga os passos no [cofre criar um cofre de Serviços de Recuperação.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Certifique-se de que apenas a [versão mais recente do Agente de Backup Azure](https://aka.ms/azurebackup_agent) está instalada no windows server ou no cliente Windows, conforme aplicável, e o computador está registado no cofre dos Serviços de Recuperação.
* Azure PowerShell 3.7.0 é necessária no computador que executa o Agente de Backup Azure. Faça o download e [instale a versão 3.7.0 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o Agente de Backup Azure, certifique-se de que o Microsoft Edge ou o Internet Explorer 11 estão instalados e o JavaScript está ativado.
* Crie uma conta de armazenamento Azure na mesma subscrição que o cofre dos Serviços de Recuperação.
* Certifique-se de que tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar a aplicação Azure Ative Directory. O fluxo de trabalho de backup offline cria uma aplicação Azure Ative Directory na subscrição associada à conta de armazenamento Azure. O objetivo da aplicação é fornecer ao Azure Backup um acesso seguro e alargado ao serviço Azure Import/Export, que é necessário para o fluxo de trabalho de backup offline.
* Registe o fornecedor de recursos *Microsoft.ImportExport* com a subscrição que contém a conta de armazenamento Azure. Para registar o fornecedor de recursos:
    1. No menu principal, **selecione Subscrições**.
    1. Se estiver subscrito em várias subscrições, selecione a subscrição que pretende utilizar para a cópia de segurança offline. Se utilizar apenas uma subscrição, a sua assinatura aparece.
    1. No menu de subscrição, selecione **fornecedores de recursos** para ver a lista de fornecedores.
    1. Na lista de fornecedores, desça até *microsoft.ImportExport*. Se o **Estado** não estiver **registado,** selecione **Registar-se**.

        ![Registar o fornecedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)

* É criada uma localização de encenação, que pode ser uma partilha de rede ou qualquer unidade adicional no computador, interna ou externa, com espaço suficiente em disco para segurar a sua cópia inicial. Por exemplo, se pretender fazer o back up de um servidor de ficheiros de 500 GB, certifique-se de que a área de paragem é de pelo menos 500 GB. (Uma quantidade menor é utilizada devido à compressão.)
* Quando enviar discos para Azure, utilize apenas discos SSD de 2,5 polegadas ou discos internos SATA II/III de 2,5 polegadas ou 3,5 polegadas. Pode utilizar discos rígidos até 10 TB. Consulte a documentação do [serviço Azure Import/Export](../import-export/storage-import-export-requirements.md#supported-hardware) para obter o mais recente conjunto de unidades que o serviço suporta.
* As unidades SATA devem ser ligadas a um computador (referido como *um computador de cópia*) de onde é feita a cópia dos dados de backup do local de preparação para as unidades SATA. Certifique-se de que o BitLocker está ativado no computador de cópia.

## <a name="workflow"></a>Fluxo de trabalho

Esta secção descreve o fluxo de trabalho de backup offline para que os seus dados possam ser entregues num datacenter Azure e enviados para o Azure Storage. Se tiver dúvidas sobre o serviço de importação ou qualquer aspeto do processo, consulte a documentação geral do [serviço Azure Import/Export](../import-export/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Iniciar backup offline

1. Quando agendar uma cópia de segurança no Agente dos Serviços de Recuperação, consulte esta página.

    ![Página de importação](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selecione a opção **Transferir usando os meus próprios discos.**

    > [!NOTE]
    > Utilize a opção Azure Data Box para transferir os dados de cópia de segurança iniciais offline. Esta opção poupa o esforço necessário para obter os seus próprios discos compatíveis com o Azure. Fornece dispositivos Azure Data Box proprietários, seguros e inviolávels da Microsoft aos quais os dados de cópia de segurança podem ser diretamente escritos pelo Agente de Serviços de Recuperação.

1. Selecione **Seguinte**, e preencha as caixas cuidadosamente.

    ![Insira os seus detalhes do disco](./media/backup-azure-backup-import-export/your-disk-details.png)

   As caixas que preenche são:

    * **Localização de localização**: Local de armazenamento temporário para o qual está escrita a cópia de reserva inicial. A localização da paragem pode estar numa partilha de rede ou num computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho completo da rede do local de paragem.
    * **Conta de Armazenamento do Gestor de Recursos Azure**: O nome da conta de armazenamento do tipo de gestor de recursos (finalidade geral v1 ou finalidade geral v2) em qualquer subscrição do Azure.
    * **Recipiente de armazenamento Azure**: O nome do recipiente de armazenamento de blob de destino na conta de armazenamento Azure onde os dados de reserva são importados antes de serem copiados para o cofre dos Serviços de Recuperação.
    * **ID de assinatura Azure**: O ID para a subscrição Azure onde a conta de armazenamento Azure é criada.
    * **Nome de emprego de importação Azure**: O nome único pelo qual o serviço Azure Import/Export e o Azure Backup acompanham a transferência de dados enviados em discos para a Azure.
  
   Depois de preencher as caixas, selecione **Next**. Guarde a **localização de preparação** e as informações sobre o **nome do trabalho da importação de Azure.** É necessário preparar os discos.

1. Quando solicitado, inscreva-se na sua assinatura Azure. Tem de iniciar sessão para que o Azure Backup possa criar a aplicação Azure Ative Directory. Introduza as permissões necessárias para aceder ao serviço Azure Import/Export.

    ![Página de inscrição de assinatura Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Termine o fluxo de trabalho. Na consola Azure Backup Agent, selecione **Back Up Now**.

    ![Back Up Now](./media/backup-azure-backup-import-export/backupnow.png)

1. Na página **de confirmação** do assistente, selecione **Back Up**. A cópia de segurança inicial é escrita na área de preparação como parte da configuração.

   ![Confirme que está pronto para recuar agora.](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Após o fim da operação, o local de preparação está pronto para ser utilizado para a preparação do disco.

   ![Página de Assistente de Back Up Now](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Prepare as unidades da SATA e envie-a para Azure

O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas para o centro de dados Azure mais próximo. Este utilitário está disponível no diretório de instalação do Agente de Backup Azure no seguinte caminho:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Vá ao diretório e copie o diretório *AzureOfflineBackupDiskPrep* para outro computador onde as unidades SATA estão ligadas. No computador com as unidades SATA ligadas, certifique-se de que:

   * O computador de cópia pode aceder ao local de preparação para o fluxo de trabalho de sementeira offline utilizando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na secção "Iniciar cópia de segurança offline".
   * O BitLocker está ativado no computador de cópia.
   * Azure PowerShell 3.7.0 está instalado.
   * Os navegadores mais recentes compatíveis (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está ativado.
   * O computador de cópia pode aceder ao portal Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, então o computador de cópia deve ser um servidor físico diferente ou uma máquina de cliente do computador de origem.

1. Abra um pedido de comando elevado no computador de cópia com o diretório de utilidade *AzureOfflineBackupDiskPrep* como o diretório atual. Execute o seguinte comando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | Descrição |
    | --- | --- |
    | s: &lt; *Caminho de localização de encenação*&gt; |Esta entrada obrigatória é utilizada para fornecer o caminho para o local de preparação que inseriu no fluxo de trabalho na secção "Iniciar backup offline". |
    | p: &lt; *Caminho para publicarSettingsFile*&gt; |Esta entrada opcional é utilizada para fornecer o caminho para o ficheiro de definições de publicação Azure.  |

    Quando você dirige o comando, o utilitário solicita a seleção do trabalho de importação Azure que corresponde às unidades que precisam de ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de preparação fornecido, você vê uma página como esta.

    ![Entrada da ferramenta de preparação do disco Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Introduza a letra de unidade sem o cólon de fuga para o disco montado que deseja preparar para a transferência para Azure.
1. Forneça a confirmação da formatação da unidade quando solicitado.
1. Foi-lhe pedido que inscreva-se na sua assinatura Azure. Introduza as suas credenciais.

    ![Azure subscrição de inscrição](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    A ferramenta começa então a preparar o disco e a copiar os dados de cópia de segurança. Pode ser necessário anexar discos adicionais quando solicitado pela ferramenta se o disco fornecido não tiver espaço suficiente para os dados de cópia de segurança. <br/>

    No final da execução bem sucedida da ferramenta, o pedido de comando fornece três peças de informação:

   1. Um ou mais discos que forneceu estão preparados para o envio para Azure.
   1. Recebe a confirmação de que o seu trabalho de importação foi criado. O trabalho de importação usa o nome que forneceu.
   1. A ferramenta exibe o endereço de envio do datacenter Azure.

      ![A preparação do disco azul terminou](./media/backup-azure-backup-import-export/console2.png)<br/>

1. No final da execução do comando, pode atualizar a informação de envio.

1. Envie os discos para o endereço que a ferramenta forneceu. Mantenha o número de rastreio para referência futura.

   > [!IMPORTANT]
   > Nenhum dos dois postos de trabalho de importação da Azure pode ter o mesmo número de rastreio. Certifique-se de que os impulsos preparados pela utilidade sob uma única tarefa de importação Azure são enviados juntos num único pacote e que há um único número de rastreamento único para o pacote. Não combine unidades preparadas como parte de trabalhos separados de importação de Azure num único pacote.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes de envio sobre o trabalho de importação do Azure

O procedimento seguinte atualiza os dados de envio de postos de trabalho importados da Azure. Estas informações incluem detalhes sobre:

* O nome do portador que entrega os discos ao Azure.
* Devolva os detalhes do envio para os seus discos.

1. Inscreva-se na sua assinatura Azure.
1. No menu principal, selecione **Todos os serviços**. Na caixa de diálogo **de todos os serviços,** insira **a Importação.** Quando vir **empregos de importação/exportação,** selecione-o.

    ![Insira informações de envio](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Abre-se o menu **de empregos de importação/exportação** e aparece a lista de todos os postos de trabalho de importação/exportação na subscrição selecionada.

1. Se tiver várias subscrições, selecione a subscrição utilizada para importar os dados de backup. Em seguida, selecione o trabalho de importação recém-criado para abrir os seus detalhes.

    ![Rever informações de envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. No menu **Definições** para o trabalho de importação, **selecione Gerir as informações de envio**. Insira os detalhes do envio de retorno.

    ![Informações de envio de lojas](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Quando tiver o número de rastreio da sua transportadora de envio, selecione o banner na página geral de trabalho de importação Azure e insira os seguintes detalhes.

   > [!IMPORTANT]
   > Confirme que as informações da transportadora e que o número de acompanhamento estão atualizados num prazo de duas semanas após a criação do trabalho de importação do Azure. A não verificação destas informações no prazo de duas semanas pode resultar na perda do trabalho e na não formação de unidades.

   ![Alerta de atualização de informação de rastreio](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informação do transportador e número de rastreio](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Hora de processar as unidades

O tempo que demora a processar um trabalho de importação da Azure varia. O tempo de processamento baseia-se em fatores como o tempo de envio, o tipo de trabalho, o tipo e o tamanho dos dados que estão a ser copiados e o tamanho dos discos fornecidos. O serviço Azure Import/Export não tem SLA. Após a recebidação dos discos, o serviço esforça-se por completar a cópia de dados de backup na sua conta de armazenamento Azure em 7 a 10 dias.

### <a name="monitor-azure-import-job-status"></a>Monitor Azure importe estatuto de trabalho

Pode monitorizar o estado do seu trabalho de importação a partir do portal Azure. Vá à página **de empregos de Importação/Exportação** e selecione o seu trabalho. Para obter mais informações sobre o estado dos postos de trabalho de importação, [veja-se o que é o serviço de importação/exportação Azure?](../import-export/storage-import-export-service.md)

### <a name="finish-the-workflow"></a>Termine o fluxo de trabalho

Após a conclusão do trabalho de importação com sucesso, os dados de backup iniciais estão disponíveis na sua conta de armazenamento. No momento da próxima cópia agendada, a Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação.

   ![Copiar dados para o cofre dos Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento do próximo backup programado, o Azure Backup executa uma cópia de segurança incremental.

### <a name="clean-up-resources"></a>Limpar os recursos

Depois de terminada a cópia de segurança inicial, pode eliminar com segurança os dados importados para o contentor de armazenamento Azure e os dados de backup no local de paragem.

## <a name="next-steps"></a>Próximos passos

* Para qualquer dúvida sobre o fluxo de trabalho do serviço Azure Import/Export, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento blob](../import-export/storage-import-export-service.md).
