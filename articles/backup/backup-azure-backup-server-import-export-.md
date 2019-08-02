---
title: Backup do Azure-backup offline para DPM e Servidor de Backup do Azure
description: Saiba como o backup do Azure permite que você envie dados fora da rede usando o serviço de importação/exportação do Azure. Este artigo explica a propagação offline dos dados de backup iniciais usando o serviço de importação e exportação do Azure.
ms.reviewer: saurse
author: dcurwin
manager: carmonm
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: dacurwin
ms.openlocfilehash: 4bc0421f16d56d34e3f3156700a7a141d38fd63d
ms.sourcegitcommit: d585cdda2afcf729ed943cfd170b0b361e615fae
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68689388"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Fluxo de trabalho de backup offline para DPM e Servidor de Backup do Azure
O backup do Azure tem várias eficiências internas que economizam os custos de rede e armazenamento durante os backups completos iniciais dos dados no Azure. Os backups completos iniciais normalmente transferem grandes quantidades de dados e exigem mais largura de banda de rede em comparação com os backups subsequentes que transferem apenas os deltas/incrementos. O backup do Azure compacta os backups iniciais. Por meio do processo de propagação offline, o backup do Azure pode usar discos para carregar os dados de backup inicial compactados offline no Azure.

O processo de propagação offline do backup do Azure é totalmente integrado ao serviço de [importação/exportação do Azure](../storage/common/storage-import-export-service.md) que permite que você transfira dados para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup inicial que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, você poderá usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial em um ou mais discos rígidos para um datacenter do Azure. Este artigo fornece uma visão geral e mais detalhes etapas que concluem este fluxo de trabalho para o System Center DPM e o Servidor de Backup do Azure.

> [!NOTE]
> O processo de backup offline para o agente de Serviços de Recuperação do Microsoft Azure (MARS) é diferente do System Center DPM e Servidor de Backup do Azure. Para obter informações sobre como usar o backup offline com o agente MARS, consulte [Este artigo](backup-azure-backup-import-export.md). O backup offline não tem suporte para backups de estado do sistema feitos usando o agente de backup do Azure.
>

## <a name="overview"></a>Descrição geral
Com a capacidade de propagação offline do backup do Azure e a importação/exportação do Azure, é simples carregar os dados offline no Azure usando discos. O processo de backup offline envolve as seguintes etapas:

> [!div class="checklist"]
> * Os dados de backup, em vez de serem enviados pela rede, são gravados em um *local de preparo*
> * Os dados no *local de preparo* são gravados em um ou mais discos SATA usando o utilitário *AzureOfflineBackupDiskPrep*
> * Um trabalho de importação do Azure é criado automaticamente pelo utilitário
> * As unidades SATA são então enviadas para o datacenter do Azure mais próximo
> * Depois que o upload dos dados de backup para o Azure for concluído, o backup do Azure copiará os dados de backup para o cofre de backup e os backups incrementais serão agendados.

## <a name="supported-configurations"></a>Configurações suportadas
O backup offline tem suporte para todos os modelos de implantação do backup do Azure que os dados de backup externo do local para o Microsoft Cloud. Isso inclui

> [!div class="checklist"]
> * Backup de arquivos e pastas com o agente de Serviços de Recuperação do Microsoft Azure (MARS) ou o agente de backup do Azure.
> * Backup de todas as cargas de trabalho e arquivos com o System Center Data Protection Manager (SC DPM)
> * Backup de todas as cargas de trabalho e arquivos com o Backup do Microsoft Azure Server <br/>

## <a name="prerequisites"></a>Pré-requisitos
Verifique se os pré-requisitos a seguir foram atendidos antes de iniciar o fluxo de trabalho de backup offline
* Um [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) foi criado. Para criar um, consulte as etapas neste [artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* O agente de backup do Azure ou Servidor de Backup do Azure ou SC DPM foi instalado no Windows Server/cliente Windows, conforme aplicável e o computador está registrado no cofre dos serviços de recuperação. Certifique-se de que apenas a [versão mais recente do backup do Azure](https://go.microsoft.com/fwlink/?linkid=229525) seja usada.
* [Baixe o arquivo de configurações de publicação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) no computador do qual você planeja fazer backup dos dados. A assinatura da qual você baixa o arquivo de configurações de publicação pode ser diferente da assinatura que contém o cofre dos serviços de recuperação. Se sua assinatura estiver em nuvens soberanas Azure, use os links a seguir, conforme apropriado, para baixar o arquivo de configurações de publicação do Azure.

    | Região de nuvem do soberanas | Link do arquivo de configurações de publicação do Azure |
    | --- | --- |
    | Estados Unidos | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Link](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Uma conta de armazenamento do Azure com o modelo de implantação *clássico* foi criada na assinatura da qual você baixou o arquivo de configurações de publicação, conforme mostrado abaixo:

  ![Criando uma conta de armazenamento clássica](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Um local de preparo, que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter a cópia inicial, é criado. Por exemplo, se você estiver tentando fazer backup de um servidor de arquivos de 500 GB, verifique se a área de preparação tem pelo menos 500 GB. (Uma quantidade menor é usada devido à compactação.)
* Com relação aos discos que serão enviados para o Azure, certifique-se de que somente discos rígidos internos de SSD de 2,5 polegadas ou de 2,5 polegadas ou 3,5 polegadas são usados. Você pode usar discos rígidos de até 10 TB. Verifique a [documentação do serviço de importação/exportação do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para obter o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA precisam estar conectadas a um computador (conhecido como um *computador de cópia*) de onde a cópia dos dados de backup do *local de preparo* para as unidades SATA é feita. Verifique se o BitLocker está habilitado no *computador de cópia*

## <a name="workflow"></a>Fluxo de trabalho
As informações contidas nesta seção ajudam a concluir o fluxo de trabalho de backup offline para que seus dados possam ser entregues a um datacenter do Azure e carregados no armazenamento do Azure. Se você tiver dúvidas sobre o serviço de importação ou qualquer aspecto do processo, consulte a documentação de [visão geral do serviço de importação](../storage/common/storage-import-export-service.md) referenciada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar backup offline
1. Ao agendar um backup, você verá a tela a seguir (no Windows Server, Windows Client ou System Center Data Protection Manager).

    ![Importar tela](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Aqui está a tela correspondente no System Center Data Protection Manager: <br/>
    ![Tela de importação do SC DPM e servidor de backup do Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    A descrição das entradas é a seguinte:

   * **Local de preparo**: O local de armazenamento temporário para o qual a cópia de backup inicial é gravada. O local de preparo pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem forem diferentes, recomendamos que você especifique o caminho de rede completo do local de preparo.
   * **Nome do trabalho de importação do Azure**: O nome exclusivo pelo qual o serviço de importação do Azure e o backup do Azure acompanham a transferência de dados enviados em discos para o Azure.
   * **Configurações de publicação do Azure**: Forneça o caminho local para o arquivo de configurações de publicação.
   * **ID da assinatura do Azure**: A ID da assinatura do Azure para a assinatura de onde você baixou o arquivo de configurações de publicação do Azure.
   * **Conta de armazenamento do Azure**: O nome da conta de armazenamento na assinatura do Azure associada ao arquivo de configurações de publicação do Azure.
   * **Contêiner de armazenamento do Azure**: O nome do blob de armazenamento de destino na conta de armazenamento do Azure em que os dados de backup são importados.

     Salve o *local de preparo* e o *nome do trabalho de importação do Azure* que você forneceu, pois ele é necessário para preparar os discos.  

2. Conclua o fluxo de trabalho e inicie a cópia de backup offline, clique em **fazer backup agora** no console de gerenciamento do agente de backup do Azure. O backup inicial é gravado na área de preparação como parte desta etapa.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para concluir o fluxo de trabalho correspondente no System Center Data Protection Manager ou no servidor de backup do Azure, clique com o botão direito do mouse no **grupo de proteção**e escolha a opção **criar ponto de recuperação** . Em seguida, escolha a opção **proteção online** .

    ![Fazer backup do SC DPM e do servidor de backup do Azure agora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Após a conclusão da operação, o local de preparo estará pronto para ser usado para a preparação do disco.

    ![Progresso do backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure
O utilitário *AzureOfflineBackupDiskPrep* é usado para preparar as unidades SATA que são enviadas para o datacenter do Azure mais próximo. Esse utilitário está disponível no diretório de instalação do agente dos serviços de recuperação no seguinte caminho:

*\\Utilitários de\\agente de serviços de recuperação do Microsoft Azure\\*

1. Vá para o diretório e copie o diretório **AzureOfflineBackupDiskPrep** para um computador de cópia no qual as unidades SATA a serem preparadas estejam conectadas. Verifique o seguinte em relação ao computador de cópia:

   * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede fornecido no fluxo de trabalho **Iniciar backup offline** .
   * O BitLocker está habilitado no computador de cópia.
   * O computador de cópia pode acessar o portal do Azure.

     Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, será obrigatório usar um servidor físico ou computador cliente diferente como o computador de cópia.


2. Abra um prompt de comando com privilégios elevados no computador de cópia com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual e execute o seguinte comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*caminho do local de preparo*&gt; |Entrada obrigatória que é usada para fornecer o caminho para o local de preparo que você inseriu no fluxo de trabalho **Iniciar backup offline** . |
    | p:&lt;*caminho para PublishSettingsFile*&gt; |Entrada opcional que é usada para fornecer o caminho para o arquivo de **configurações de publicação do Azure** que você inseriu no fluxo de trabalho **Iniciar backup offline** . |

    > [!NOTE]
    > O &lt;caminho para o&gt; valor de AzurePublishSettingFile é obrigatório quando o computador de cópia e o computador de origem são diferentes.
    >
    >

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de importação do Azure que corresponde às unidades que precisam ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de preparo fornecido, você verá uma tela como a mostrada a seguir.

    ![Entrada da ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Insira a letra da unidade sem os dois pontos à direita para o disco montado que você deseja preparar para a transferência para o Azure. Forneça a confirmação para a formatação da unidade quando solicitado.

    Em seguida, a ferramenta começa a preparar o disco e copiar os dados de backup. Talvez seja necessário anexar discos adicionais quando solicitado pela ferramenta caso o disco fornecido não tenha espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, um ou mais discos fornecidos estão preparados para envio ao Azure. Além disso, um trabalho de importação com o nome que você forneceu durante o processo de **iniciar o backup offline** é criado no Azure. Por fim, a ferramenta exibe o endereço de envio para o datacenter do Azure onde os discos precisam ser enviados.

    ![Preparação do disco do Azure concluída](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. No final da execução do comando, você também verá a opção para atualizar as informações de envio, conforme mostrado abaixo:

    ![Atualizar a opção de informações de remessa](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Você pode inserir os detalhes imediatamente. A ferramenta orienta você pelo processo que envolve uma série de entradas. No entanto, se você não tiver informações como o número de controle ou outros detalhes relacionados ao envio, você poderá encerrar a sessão. As etapas para atualizar os detalhes de envio posteriormente são fornecidas neste artigo.

6. Envie os discos para o endereço que a ferramenta forneceu e mantenha o número de rastreamento para referência futura.

   > [!IMPORTANT]
   > Dois trabalhos de importação do Azure não podem ter o mesmo número de acompanhamento. Verifique se as unidades preparadas pelo utilitário em um único trabalho de importação do Azure são enviadas juntas em um único pacote e se há um único número de controle exclusivo para o pacote. Não combine unidades preparadas como parte de **diferentes** trabalhos de importação do Azure em um único pacote.

5. Quando você tiver as informações de número de rastreamento, vá para o computador de origem, que está aguardando a conclusão do trabalho de importação e execute o seguinte comando em um prompt de comando com privilégios elevados com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Opcionalmente, você pode executar o seguinte comando em um computador diferente, como o *computador de cópia*, com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | Descrição |
    | --- | --- |
    | u: | Entrada obrigatória usada para atualizar os detalhes de envio para um trabalho de importação do Azure |
    | s:&lt;*caminho do local de preparo*&gt; | Entrada obrigatória quando o comando não é executado no computador de origem. Usado para fornecer o caminho para o local de preparo que você inseriu no fluxo de trabalho **Iniciar backup offline** . |
    | p:&lt;*caminho para PublishSettingsFile*&gt; | Entrada obrigatória quando o comando não é executado no computador de origem. Usado para fornecer o caminho para o arquivo de **configurações de publicação do Azure** que você inseriu no fluxo de trabalho **Iniciar backup offline** . |

    O utilitário detecta automaticamente o trabalho de importação que o computador de origem está aguardando ou os trabalhos de importação associados ao local de preparo quando o comando é executado em um computador diferente. Em seguida, ele fornece a opção de atualizar as informações de envio por meio de uma série de entradas, conforme mostrado abaixo:

    ![Inserindo informações de remessa](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Depois que todas as entradas forem fornecidas, examine os detalhes cuidadosamente e confirme as informações de envio fornecidas digitando *Sim*.

    ![Examinar informações de envio](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Ao atualizar as informações de envio com êxito, o utilitário fornece um local local onde os detalhes de envio inseridos por você são armazenados conforme mostrado abaixo

    ![Armazenando informações de envio](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Certifique-se de que as unidades atinjam o datacenter do Azure dentro de duas semanas de fornecimento das informações de envio usando o utilitário *AzureOfflineBackupDiskPrep* . Não fazer isso pode resultar na não processamento das unidades.  

Depois de concluir as etapas acima, o datacenter do Azure estará pronto para receber as unidades e processá-las ainda mais para transferir os dados de backup das unidades para a conta de armazenamento do Azure do tipo clássico que você criou.

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades
O tempo necessário para processar um trabalho de importação do Azure varia de acordo com os diferentes fatores, como tempo de envio, tipo de trabalho, tipo e tamanho dos dados que estão sendo copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação do Azure não tem um SLA, mas depois que os discos são recebidos, o serviço se esforça para concluir a cópia de dados de backup para sua conta de armazenamento do Azure em 7 a 10 dias. A próxima seção detalha como você pode monitorar o status do trabalho de importação do Azure.

### <a name="monitoring-azure-import-job-status"></a>Monitorando o status do trabalho de importação do Azure
Enquanto as unidades estiverem em trânsito ou no datacenter do Azure para serem copiadas para a conta de armazenamento, o agente de backup do Azure ou o SC DPM ou o console do servidor de backup do Azure no computador de origem mostrará o status do trabalho a seguir para os backups agendados.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Siga as etapas abaixo para verificar o status do trabalho de importação.
1. Abra um prompt de comando com privilégios elevados no computador de origem e execute o seguinte comando:

     `AzureOfflineBackupDiskPrep.exe u:`

2.  A saída mostra o status atual do trabalho de importação, conforme mostrado abaixo:

    ![Verificando o status do trabalho de importação](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para obter mais informações sobre os vários Estados do trabalho de importação do Azure, consulte [Este artigo](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Depois que o trabalho de importação for concluído, os dados de backup iniciais estarão disponíveis em sua conta de armazenamento. No momento do próximo backup agendado, o backup do Azure copia o conteúdo dos dados da conta de armazenamento para o cofre dos serviços de recuperação, conforme mostrado abaixo:

   ![Copiando dados para o cofre dos serviços de recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento do próximo backup agendado, o backup do Azure executa o backup incremental sobre a cópia de backup inicial.

## <a name="next-steps"></a>Passos seguintes
* Para perguntas sobre o fluxo de trabalho de importação/exportação do Azure, consulte [usar o serviço de importação/exportação Microsoft Azure para transferir dados para o armazenamento de BLOBs](../storage/common/storage-import-export-service.md).
* Consulte a seção backup offline das [perguntas frequentes](backup-azure-backup-faq.md) sobre o backup do Azure para dúvidas sobre o fluxo de trabalho.
