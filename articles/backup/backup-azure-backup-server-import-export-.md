---
title: O Azure Backup - cópia de segurança Offline para o DPM e o servidor de cópia de segurança do Azure
description: Saiba como a cópia de segurança do Azure permite-lhe enviar dados fora da rede com o serviço importar/exportar do Azure. Este artigo explica a propagação offline dos dados de cópia de segurança iniciais com o serviço de exportação de importação do Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 5/8/2018
ms.author: saurse
ms.openlocfilehash: 8d15eb03055aed32c8a99121b750ee5767a87b50
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58094978"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Fluxo de trabalho de cópia de segurança offline do DPM e o servidor de cópia de segurança do Azure
O Azure Backup tem vários eficiências incorporadas que salvar os custos de armazenamento e de rede durante as cópias de segurança completas iniciais de dados para o Azure. Normalmente, as cópias de segurança completas iniciais transferem grandes quantidades de dados e exigem mais largura de banda de rede quando comparado com as cópias de segurança subsequentes que transferem apenas as deltas/incrementais. O Azure Backup compacta as cópias de segurança iniciais. O processo de propagação offline, cópia de segurança do Azure pode utilizar discos para carregar a iniciais cópia de segurança de dados comprimidos offline para o Azure.

O processo de propagação offline da cópia de segurança do Azure está estreitamente integrado com o [serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) que permite-lhe transferir dados para o Azure ao utilizar discos. Se tiver terabytes (TB) de dados de cópia de segurança iniciais que têm de ser transferidos através de uma rede de alta latência e baixa largura de banda, pode utilizar o fluxo de trabalho de propagação offline para enviar a cópia de segurança inicial num ou mais discos rígidos num Datacenter do Azure. Este artigo fornece uma descrição geral e ainda mais os passos de detalhes que realizar este fluxo de trabalho para o System Center DPM e o servidor de cópia de segurança do Azure.

> [!NOTE]
> O processo de cópia de segurança Offline para o agente dos serviços de recuperação do Azure (MARS) da Microsoft é diferente do System Center DPM e o servidor de cópia de segurança do Azure. Para obter informações sobre como utilizar a cópia de segurança Offline com o agente MARS, consulte [este artigo](backup-azure-backup-import-export.md). Cópia de segurança offline não é suportada para cópias de segurança do Estado do sistema efetuadas com o agente de cópia de segurança do Azure. 
>

## <a name="overview"></a>Descrição geral
Com a capacidade de propagação offline de mensagens em fila de cópia de segurança do Azure e importar/exportar do Azure, é simples de carregar os dados offline para o Azure ao utilizar discos. O processo de cópia de segurança Offline envolve os seguintes passos:

> [!div class="checklist"]
> * Os dados de cópia de segurança, em vez de a ser enviados através da rede, são escritos para um *localização de transição*
> * Os dados no *localização de transição* , em seguida, é escrito para um ou mais discos SATA utilizando o *AzureOfflineBackupDiskPrep* utilitário
> * Uma tarefa de importação do Azure é criada automaticamente pelo utilitário
> * As unidades SATA, em seguida, são enviadas para o datacenter mais próximo do Azure
> * Após a conclusão do carregamento dos dados de cópia de segurança para o Azure, a cópia de segurança do Azure copia os dados de cópia de segurança para o Cofre de cópias de segurança e cópias de segurança incrementais.

## <a name="supported-configurations"></a>Configurações suportadas 
Cópia de segurança offline é suportada para todos os modelos de implementação do Azure Backup que dados de cópia de segurança fora do local do local para a Cloud da Microsoft. Isto inclui

> [!div class="checklist"]
> * Cópia de segurança de ficheiros e pastas com o agente dos serviços de recuperação do Azure (MARS) da Microsoft ou o agente de cópia de segurança do Azure. 
> * Cópia de segurança de todos os ficheiros com o System Center Data Protection Manager (SC DPM) e cargas de trabalho 
> * Cópia de segurança de todos os ficheiros e cargas de trabalho com o Microsoft Azure Backup Server <br/>

## <a name="prerequisites"></a>Pré-requisitos
Certifique-se de que os seguintes pré-requisitos são cumpridos antes de iniciar o fluxo de trabalho de cópia de segurança Offline
* R [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md) foi criado. Para criar um, consulte os passos no [neste artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Agente de cópia de segurança do Azure ou servidor de cópia de segurança do Azure ou SC DPM foi instalado em qualquer um dos cliente do Windows Server/Windows, conforme aplicável e o computador está registado com o Cofre dos serviços de recuperação. Certifique-se de que apenas os [a versão mais recente do Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) é utilizado. 
* [Transfira o ficheiro de definições de publicação do Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) no computador do qual planeja fazer uma cópia dos seus dados. A subscrição a partir do qual transferir o ficheiro de definições de publicação pode ser diferente da subscrição que contém o Cofre dos serviços de recuperação. Se a sua subscrição está no soberanas Clouds do Azure, em seguida, utilize as seguintes ligações conforme apropriado para transferir o ficheiro de definições de publicação do Azure.

    | Região de cloud soberanas | Ligação do ficheiro de definições de publicação do Azure |
    | --- | --- |
    | Estados Unidos | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Link](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Uma conta de armazenamento do Azure com *clássico* modelo de implementação foi criado na subscrição a partir da qual transferiu o ficheiro de definições de publicação conforme mostrado abaixo: 

  ![Criar uma conta de armazenamento clássicas](./media/backup-azure-backup-import-export/storageaccountclassiccreate.png)

* Uma localização de transição, o que poderá ser um compartilhamento de rede ou de qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter sua cópia inicial, é criada. Por exemplo, se estiver a tentar criar cópias de segurança um servidor de ficheiros de 500 GB, certifique-se de que a área de transição é, pelo menos, 500 GB. (Uma quantidade menor é utilizada devido à compactação.)
* Com respeito a discos que serão enviados para o Azure, certifique-se de que apenas 2,5 polegadas SSD ou 2.5 polegadas ou 3.5 polegadas SATA II/III discos rígidos internos são utilizados. Pode utilizar unidades de disco rígido até 10 TB. Verifique os [documentação do serviço importar/exportar do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para o conjunto mais recente de unidades de que o serviço suporta.
* As unidades SATA precisam estar conectado a um computador (conhecido como um *computador de cópia*) de onde a cópia de dados de cópia de segurança da *localização de transição* para o SATA unidades é feito. Certifique-se de que o BitLocker está ativado no *computador de cópia* 

## <a name="workflow"></a>Fluxo de trabalho
As informações nesta secção ajudam-o a concluir o fluxo de trabalho de cópia de segurança offline para que os dados podem ser entregues num Datacenter do Azure e carregados para o armazenamento do Azure. Se tiver dúvidas sobre o serviço de importação ou a todos os aspectos do processo, consulte a [descrição geral do serviço de importação](../storage/common/storage-import-export-service.md) documentação mencionada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar a cópia de segurança offline
1. Quando agendar uma cópia de segurança, verá o ecrã seguinte (no Windows Server, o cliente do Windows ou o System Center Data Protection Manager).

    ![Ecrã de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Esta é a tela correspondente no System Center Data Protection Manager: <br/>
    ![Ecrã de importação de servidor do DPM de SC e cópia de segurança do Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    A descrição das entradas é o seguinte:

   * **Localização de transição**: A localização de armazenamento temporário para o qual a cópia de segurança inicial é escrita. Localização de transição poderá ser num computador local ou uma partilha de rede. Se o computador de cópia e o computador de origem forem diferentes, recomendamos que especifica o caminho de rede completa da localização de transição.
   * **Nome da tarefa de importação do Azure**: O nome exclusivo ao qual importação de Azure service e do Azure Backup controlam a transferência de dados enviados em discos para o Azure.
   * **Definições de publicação do Azure**: Forneça o caminho local para o ficheiro de definições de publicação.
   * **ID de subscrição do Azure**: O ID de subscrição do Azure para a subscrição de onde transferiu o ficheiro de definições de publicação do Azure. 
   * **Conta de armazenamento do Azure**: O nome da conta de armazenamento na subscrição do Azure associada ao ficheiro de definições de publicação do Azure.
   * **Contentor de armazenamento do Azure**: O nome do blob de armazenamento de destino na conta de armazenamento do Azure em que os dados de cópia de segurança são importados.

     Guardar a *localização de transição* e o *nome da tarefa de importação do Azure* fornecidas conforme é necessário para preparar os discos.  
     
2. Concluir o fluxo de trabalho e, para iniciar a cópia da cópia de segurança offline, clique em **cópia de segurança agora** na consola de gestão do agente Azure Backup. A cópia de segurança inicial é escrita para a área de teste como parte deste passo.

    ![Cópia de segurança agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para concluir o fluxo de trabalho correspondente no System Center Data Protection Manager ou o Azure Backup server, com o botão direito a **grupo de proteção**e, em seguida, escolha a **criar ponto de recuperação** opção. Em seguida, escolha o **proteção Online** opção.

    ![SC DPM e o servidor de cópia de segurança do Azure backup agora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Depois de concluída a operação, a localização de transição está pronta para ser utilizada para preparação de disco.

    ![Progresso de cópia de segurança](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure
O *AzureOfflineBackupDiskPrep* utilitário é utilizado para preparar as unidades SATA, que são enviadas para o Datacenter mais próximo do Azure. Esse utilitário está disponível no diretório de instalação do agente dos serviços de recuperação no seguinte caminho:

   *\Microsoft* *Azure* *Recovery* *Services* *Agent\Utils\*

1. Vá para o diretório e copiar o **AzureOfflineBackupDiskPrep** diretório para um computador de cópia em que as unidades SATA para estar preparado estão ligadas. Certifique-se o seguinte com respeito o computador de cópia:

   * O computador de cópia pode aceder a localização de transição para o fluxo de trabalho de propagação offline com o mesmo caminho de rede que foi fornecido no **iniciar a cópia de segurança offline** fluxo de trabalho.
   * BitLocker está ativado no computador de cópia.
   * O computador de cópia pode acessar o portal do Azure.

     Se necessário, o computador de cópia pode ser o mesmo que o computador de origem. 
    
     > [!IMPORTANT] 
     > Se o computador de origem for uma máquina virtual, em seguida, é obrigatório para utilizar uma máquina de cliente ou servidor físico diferente que o computador de cópia.
    
    
2. Abra uma linha de comandos elevada no computador de cópia com o *AzureOfflineBackupDiskPrep* diretório de utilitário, como o diretório atual e execute o seguinte comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*caminho de localização de transição*&gt; |Entrada obrigatória, que é utilizada para fornecer o caminho para a localização de transição que introduziu no **iniciar a cópia de segurança offline** fluxo de trabalho. |
    | p:&lt;*caminho para PublishSettingsFile*&gt; |Entrada opcional que é utilizada para fornecer o caminho para o **definições de publicação do Azure** ficheiro que introduziu no **iniciar a cópia de segurança offline** fluxo de trabalho. |

    > [!NOTE]
    > O &lt;caminho para AzurePublishSettingFile&gt; valor é obrigatório quando o computador de cópia e o computador de origem são diferentes.
    >
    >

    Ao executar o comando, o utilitário solicita a seleção da tarefa de importação do Azure que corresponde às unidades que precisam estar preparado. Se apenas uma tarefa de importação único está associada com a localização de transição fornecida, verá um ecrã semelhante ao que se segue.

    ![Entrada de ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>
    
3. Introduza a letra de unidade sem os dois pontos à direita para o disco montado que deseja se preparar para a transferência para o Azure. Forneça a confirmação para a formatação da unidade quando lhe for pedido.

    Em seguida, começa a ferramenta preparar o disco e copiar os dados de cópia de segurança. Terá de anexar discos adicionais quando lhe for pedido pela ferramenta caso o disco fornecido não tem espaço suficiente para os dados de cópia de segurança. <br/>

    No final da execução bem-sucedida da ferramenta, um ou mais discos que forneceu estão preparados para o envio para o Azure. Além disso, uma tarefa de importação com o nome indicou durante a **iniciar a cópia de segurança offline** fluxo de trabalho é criado no Azure. Por fim, a ferramenta exibe o endereço de envio para o datacenter do Azure em que os discos têm de ser enviado.

    ![Preparação de disco do Azure completa](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>
    
4. No final da execução do comando Consulte também a opção para atualizar informações de envio, conforme mostrado abaixo:

    ![Opção de informações de remessa de atualização](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Pode introduzir os detalhes de imediato. A ferramenta orienta-o ao longo do processo que envolve uma série de entradas. No entanto se não tiver informações como o número ou outros detalhes relacionados com o envio de controlo, pode terminar a sessão. Os passos para atualizar os detalhes de envio mais tarde são fornecidos neste artigo. 

6. Envie os discos para o endereço que a ferramenta fornecida e manter o número de controlo para referência futura.

   > [!IMPORTANT] 
   > Não existem duas tarefas de importação do Azure pode ter o mesmo número de controlo. Certifique-se de que as unidades preparadas pelo utilitário numa única tarefa de importação do Azure são fornecidas em conjunto num único pacote e que existe um número exclusivo de controlo único para o pacote. Não os combine unidades preparadas como parte da **diferentes** tarefas de importação do Azure num único pacote. 

5. Quando tem o número de informações, vá para o computador de origem, o que está aguardando a conclusão da tarefa de importação e execute o seguinte comando na linha de comandos elevada com de controlo *AzureOfflineBackupDiskPrep* diretório de utilitário, como o diretório atual: 

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Pode, opcionalmente, execute o seguinte comando num computador diferente, como o *computador de cópia*, com *AzureOfflineBackupDiskPrep* diretório de utilitário, como o diretório atual:
   
   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | Descrição |
    | --- | --- |
    | u: | Entrada obrigatória utilizada para atualizar os detalhes de envio para uma tarefa de importação do Azure |
    | s:&lt;*caminho de localização de transição*&gt; | Quando o comando não é executado no computador de origem de entrada obrigatória. Utilizado para fornecer o caminho para a localização de transição que introduziu no **iniciar a cópia de segurança offline** fluxo de trabalho. |
    | p:&lt;*caminho para PublishSettingsFile*&gt; | Quando o comando não é executado no computador de origem de entrada obrigatória. Utilizado para fornecer o caminho para o **definições de publicação do Azure** ficheiro que introduziu no **iniciar a cópia de segurança offline** fluxo de trabalho. |
    
    O utilitário Deteta automaticamente a tarefa de importação que está aguardando o computador de origem ou as tarefas de importação associadas com a localização de transição, quando o comando é executado num computador diferente. Em seguida, fornece a opção para atualizar informações de remessa por uma série de entradas, conforme mostrado abaixo: 
    
    ![Introduzir informações de envio](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

6. Depois de todas as entradas são fornecidas, reveja com atenção os detalhes e consolidar as informações de envio que forneceu ao escrever *Sim*. 

    ![Reveja as informações de envio](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

7. Sobre como atualizar as informações de envio com êxito, o utilitário fornece uma localização local onde os detalhes de envio introduzidos por si são armazenados como mostrado abaixo 

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT] 
   > Certifique-se de que as unidades atingirem o Datacenter do Azure em duas semanas de fornecer a informação de envio utilizando o *AzureOfflineBackupDiskPrep* utilitário. Falha ao fazer isso pode resultar em unidades não são processadas.  

Depois de concluir os passos acima, o Centro de dados do Azure está pronto para receber as unidades e continuar a processá-los para transferir os dados de cópia de segurança das unidades para a conta de armazenamento do Azure do tipo clássico que criou. 

### <a name="time-to-process-the-drives"></a>Tempo a processar as unidades 
A quantidade de tempo que demora a processar uma tarefa de importação do Azure varia de acordo com diferentes fatores como o tempo de envio, tipo, tipo e tamanho dos dados a ser copiados e o tamanho dos discos fornecidos da tarefa. O serviço importar/exportar do Azure não tem um SLA, mas depois dos discos são recebidos o serviço se esforça para concluir a cópia de dados de cópia de segurança à sua conta de armazenamento do Azure dentro de 7 a 10 dias. A secção seguinte fornece detalhes sobre como pode monitorizar o estado da tarefa de importação do Azure. 

### <a name="monitoring-azure-import-job-status"></a>Monitorização do Estado de tarefa de importação do Azure
Embora a unidades estão em trânsito ou no datacenter do Azure para ser copiado para a conta de armazenamento, o agente de Azure Backup ou SC DPM ou a consola do servidor de cópia de segurança do Azure no computador de origem mostra o estado da tarefa seguinte para seus backups agendados. 

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Siga os passos abaixo, para verificar o estado da tarefa de importação. 
1. Abra uma linha de comandos elevada no computador de origem e execute o seguinte comando:
    
     `AzureOfflineBackupDiskPrep.exe u:`
    
2.  A saída mostra o estado atual da tarefa de importação, conforme mostrado abaixo: 

    ![A verificar o estado da tarefa de importação](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para obter mais informações sobre os diversos Estados da tarefa de importação do Azure, consulte [neste artigo](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Após a conclusão da tarefa de importação, os dados de cópia de segurança inicial estão disponíveis na sua conta de armazenamento. No momento da cópia de segurança agendada seguinte, o Azure backup copia o conteúdo dos dados da conta de armazenamento para o Cofre dos serviços de recuperação conforme mostrado abaixo: 

   ![Copiar dados para o Cofre dos serviços de recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento da cópia de segurança agendada seguinte, cópia de segurança do Azure efetua a cópia de segurança incremental sobre a cópia de segurança inicial.

## <a name="next-steps"></a>Passos Seguintes
* Para quaisquer perguntas sobre o fluxo de trabalho de importar/exportar do Azure, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../storage/common/storage-import-export-service.md).
* Consulte a seção de cópia de segurança offline do Azure Backup [FAQ](backup-azure-backup-faq.md) para quaisquer perguntas sobre o fluxo de trabalho.
