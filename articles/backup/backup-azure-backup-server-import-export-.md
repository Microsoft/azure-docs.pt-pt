---
title: Cópia de segurança offline para o DPM e Azure Backup Server
description: A Azure Backup permite-lhe enviar dados da rede utilizando o serviço De Importação/Exportação Azure. Este artigo explica o fluxo de trabalho de backup offline para DPM e Azure Backup Server (MABS).
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 6be75062ab0ce06784d8cd7c833e0070476acf60
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022584"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Fluxo de trabalho offline-backup para DPM e Servidor de Backup Azure

A Azure Backup tem várias eficiências incorporadas que poupam custos de rede e armazenamento durante as cópias de segurança iniciais completas de dados para o Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e requerem mais largura de banda da rede quando comparadas com as cópias de segurança subsequentes que transferem apenas os deltas/incrementais. O backup do Azure compacta os backups iniciais. Através do processo de sementeira offline, o Azure Backup pode usar discos para carregar os dados de backup inicial comprimido offline para O Azure.

O processo offline de sementeir da Azure Backup está fortemente integrado com o [serviço de importação/exportação Azure](../storage/common/storage-import-export-service.md) que lhe permite transferir dados para o Azure utilizando discos. Se tiver terabytes (TBs) de dados de backup iniciais que precisam de ser transferidos através de uma rede de alta latência e largura de banda, pode utilizar o fluxo de trabalho offline para enviar a cópia de cópia de cópia de reserva inicial num ou mais discos rígidos para um centro de dados Azure. Este artigo fornece uma visão geral e mais detalhes passos que completam este fluxo de trabalho para O DPM do System Center E O Servidor de Backup Azure.

> [!NOTE]
> O processo de backup offline para o agente microsoft Azure Recovery Services (MARS) é distinto do System Center DPM e do Azure Backup Server. Para obter informações sobre a utilização de backup offline com o agente MARS, consulte [este artigo](backup-azure-backup-import-export.md). A cópia de segurança offline não é suportada para cópias de segurança do System State feitas utilizando o agente de backup Azure.
>

## <a name="overview"></a>Visão geral

Com a capacidade offline de sementeir de Azure Backup e Azure Import/Export, é simples enviar os dados offline para O Azure utilizando discos. O processo de backup offline envolve os seguintes passos:

> [!div class="checklist"]
>
> * Os dados de backup, em vez de serem enviados sobre a rede, estão escritos para um local de *encenação*
> * Os dados sobre a localização da *encenação* são então escritos a um ou mais discos SATA utilizando o utilitário *AzureOfflineBackupDiskPrep*
> * Um trabalho de Importação Azure é automaticamente criado pela utilidade
> * As unidades SATA são então enviadas para o centro de dados Azure mais próximo
> * Após o upload dos dados de backup para o Azure, o Azure Backup copia os dados de backup para o cofre de backup e as cópias de segurança incrementais estão agendadas.

## <a name="supported-configurations"></a>Configurações suportadas

Offline Backup é suportado para todos os modelos de implementação de Backup Azure que dados de backup offsite de no local para o Microsoft Cloud. Isto inclui

> [!div class="checklist"]
>
> * Cópia de segurança de ficheiros e pastas com o Agente de Serviços de Recuperação do Microsoft Azure (MARS) ou o agente de backup Azure.
> * Backup de todas as cargas de trabalho e ficheiros com System Center Data Protection Manager (SC DPM)
> * Backup de todas as cargas de trabalho e ficheiros com o Microsoft Azure Backup Server

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de dar início ao fluxo de trabalho offline backup

* Foi criado um cofre dos Serviços de [Recuperação.](backup-azure-recovery-services-vault-overview.md) Para criar um, consulte os passos [deste artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* O agente de backup Azure ou o Azure Backup Server ou SC DPM foram instalados em qualquer cliente do Windows Server/Windows, conforme aplicável e o computador está registado no Cofre de Serviços de Recuperação. Certifique-se de que apenas a [versão mais recente do Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) é utilizada.
* [Descarregue o ficheiro](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) de definições da Azure Publish no computador a partir do qual planeia fazer o back-up dos seus dados. A subscrição a partir da qual descarrega o ficheiro de definições de publicação pode ser diferente da subscrição que contém o Cofre de Serviços de Recuperação. Se a sua subscrição estiver em Nuvens Azure soberanas, utilize os seguintes links conforme apropriado para descarregar o ficheiro de definições da Azure Publish.

    | Região de nuvens soberanas | Ligação de ficheiros da Azure Publish |
    | --- | --- |
    | Estados Unidos | [Ligação](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Ligação](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Foi criada uma conta de Armazenamento Azure com o modelo de implementação do Gestor de *Recursos* na subscrição a partir da qual descarregou o ficheiro de definições de publicação, como mostrado abaixo:

  ![Criação de uma conta de armazenamento com desenvolvimento do Gestor de Recursos](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* É criado um local de encenação, que pode ser uma partilha de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço suficiente para guardar a sua cópia inicial. Por exemplo, se estiver a tentar fazer o apoio a um servidor de ficheiros de 500 GB, certifique-se de que a área de paragem é de pelo menos 500 GB. (Uma quantidade menor é utilizada devido à compressão.)
* No que diz respeito aos discos que serão enviados para o Azure, certifique-se de que apenas são utilizados discos de 2,5 polegadas, ou 2,5 polegadas ou 3,5 polegadas sATA II/III unidades rígidos internas. Pode utilizar discos rígidos até 10 TB. Consulte a documentação do [serviço Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) para obter o mais recente conjunto de unidades que o serviço suporta.
* As unidades SATA têm de ser ligadas a um computador (referido como *um computador de cópia)* de onde a cópia dos dados de cópia da localização de *paragem* para as unidades SATA é feita. Certifique-se de que o BitLocker está ativado no *computador de cópia*

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Prepare o Servidor para o processo de backup offline

>[!NOTE]
> Se não encontrar os utilitários listados, como *O AzureOfflineBackupCertGen.exe* na sua instalação do agente MARS, escreva para AskAzureBackupTeam@microsoft.com para ter acesso aos mesmos.

* Abra um pedido de comando elevado no servidor e execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    A ferramenta criará uma Aplicação AD De Backup Offline Azure se não existir.

    Se já existir uma Aplicação, esta executável pedir-lhe-á que carregue manualmente o certificado para o pedido no arrendatário. Siga os passos abaixo [nesta secção](#manually-upload-offline-backup-certificate) para fazer o upload manual do certificado para a aplicação.

* A ferramenta AzureOfflineBackup.exe gerará um ficheiro OfflineApplicationParams.xml.  Copie este ficheiro para o servidor com MABS ou DPM.
* Instale o [mais recente agente MARS](https://aka.ms/azurebackup_agent) no servidor DPM/Azure Backup (MABS).
* Registe o servidor no Azure.
* Execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* O comando acima criará o ficheiro `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`

## <a name="manually-upload-offline-backup-certificate"></a>Carregar manualmente certificado de backup offline

Siga os passos abaixo para carregar manualmente o certificado de backup offline para uma aplicação de Diretório Ativo Azure previamente criada, destinada a Backup Offline.

1. Inicie sessão no Portal do Azure.
2. Vá aos **registos** **de Diretório Ativo** > Azure
3. Navegue para o separador **Aplicações Próprias** e localize uma aplicação com o formato de nome de exibição `AzureOfflineBackup _<Azure User Id` conforme mostrado abaixo:

    ![Localizar aplicação no separador De Aplicações Próprias](./media/backup-azure-backup-import-export/owned-applications.png)

4. Clique na aplicação. Sob o separador **Gerir** no painel esquerdo, vá a **Certificados e segredos**.
5. Verifique se existem certificados ou chaves públicas pré-existentes. Se não houver nenhuma, pode eliminar a aplicação com segurança clicando no botão **Eliminar** na página **'Overview'** da aplicação. A seguir a isto, pode voltar a tentar os passos para [preparar o Servidor para o](#prepare-the-server-for-the-offline-backup-process) processo de backup offline e saltar os passos abaixo. Caso contrário, execute os seguintes passos do servidor DPM /Azure Backup Server (MABS) onde pretende configurar a Cópia de Segurança Offline.
6. Abra a **aplicação de certificado** de computador > separador **pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`
7. Selecione o certificado acima, clique à direita em **Todas as Tarefas** e, em seguida, **exportar,** sem chave privada, no formato .cer.
8. Vá à aplicação de backup offline Azure no portal Azure.
9. Clique em **Gerir** **certificados e segredos** de >  > **certificado de upload,** e faça upload do certificado exportado no passo anterior.

    ![Faça upload do certificado](./media/backup-azure-backup-import-export/upload-certificate.png)
10. No servidor, abra o registo digitando **regedite** na janela de execução.
11. Vá para a entrada de registo *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
12. Clique à direita no **CloudBackupProvider** e adicione um novo valor de cadeia com o nome `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Nota: Para encontrar o Id do Utilizador Azure, execute um dos seguintes passos:
    >
    >1. A partir do Comando PowerShell ligado ao Azure, executa o comando `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”`.
    >2. Navegue para o caminho do registo: `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`

13. Clique à direita na corda adicionada no passo anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que exportou no passo 7 e **clique**OK .
14. Para obter o valor da impressão digital, clique duas vezes no certificado, em seguida, selecione o separador **Detalhes** e desloque-se para baixo até ver o campo de impressão digital. Clique na impressão digital e **copie** o valor.

    ![Valor de cópia do campo de impressão digital](./media/backup-azure-backup-import-export/thumbprint-field.png)

15. Continue na secção [Workflow](#workflow) para prosseguir com o processo de backup offline.

## <a name="workflow"></a>Fluxo de Trabalho

A informação nesta secção ajuda-o a completar o fluxo de trabalho offline-backup para que os seus dados possam ser entregues num centro de dados Azure e enviados para o Armazenamento Azure. Se tiver dúvidas sobre o serviço de importação ou qualquer aspeto do processo, consulte a documentação geral do [serviço de importação](../storage/common/storage-import-export-service.md) referenciada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar cópia seleção offline

1. Ao agendar uma cópia de segurança, vê o seguinte ecrã (no Windows Server, cliente windows ou System Center Data Protection Manager).

    ![Tela de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Aqui está o ecrã correspondente no System Center Data Protection Manager: <br/>
    ![ecrã de importação de servidor estoque SC DPM e Azure Backup](./media/backup-azure-backup-import-export/dpmoffline.png)

    A descrição das inputs é a seguinte:

   * **Local de preparação**: O local de armazenamento temporário para o qual a cópia inicial de cópia de cópia de reserva está escrita. A localização da encenação pode estar numa partilha de rede ou num computador local. Se o computador de cópia e o computador de origem forem diferentes, recomendamos que especifique o percurso completo de rede do local de paragem.
   * Nome do trabalho de **importação azure**: O nome único pelo qual o serviço de importação de Azure e a Cópia de Segurança Azure acompanham a transferência de dados enviados em discos para o Azure.
   * **Definições**de publicação do Azure : Forneça o caminho local para o ficheiro de definições de publicação.
   * **ID de subscrição Do Azure**: O ID de subscrição do Azure para a subscrição a partir do qual descarregou o ficheiro de definições da Azure Publish.
   * **Conta de Armazenamento Azure**: O nome da conta de armazenamento na subscrição Azure associada ao ficheiro de definições da Azure Publish.
   * **Recipiente de armazenamento Azure**: O nome da bolha de armazenamento de destino na conta de armazenamento Azure onde os dados de backup são importados.

     Guarde o local de *preparação* e o Nome de *Trabalho de Importação Azure* que forneceu como é necessário para preparar os discos.  

2. Complete o fluxo de trabalho e, para iniciar a cópia de backup offline, clique em **Back Up Now** na consola de gestão de agente de backup Azure. O backup inicial está escrito para a área de preparação como parte deste passo.

    ![Backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para completar o fluxo de trabalho correspondente no System Center Data Protection Manager ou no servidor de backup Azure, clique no Grupo de **Proteção,** clique no grupo de proteção e, em seguida, escolha a opção de ponto de **recuperação Create.** Em seguida, escolha a opção **De Proteção Online.**

    ![SC DPM e servidor de backup Azure voltam agora](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Após o fim da operação, o local de preparação está pronto para ser utilizado para a preparação do disco.

    ![Progresso de backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Prepare unidades da SATA e envie para Azure

O utilitário *AzureOfflineBackupDiskPrep* é utilizado para preparar as unidades SATA que são enviadas para o centro de dados azure mais próximo. Este utilitário está disponível no diretório de instalação do agente de Serviços de Recuperação no seguinte caminho:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Vá ao diretório e copie o diretório **AzureOfflineDiskPrep** para um computador de cópia no qual as unidades SATA para serem preparadas estão ligadas. Certifique-se do seguinte no que diz respeito ao computador de cópia:

   * O computador de cópia pode aceder ao local de preparação para o fluxo de trabalho offline de sementeing utilizando o mesmo caminho de rede que foi fornecido no fluxo de trabalho de **backup offline Iniciar.**
   * O BitLocker está ativado no computador de cópia.
   * O computador de cópia pode aceder ao portal Azure.

     Se necessário, o computador de cópia pode ser o mesmo que o computador-fonte.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, então é obrigatório utilizar um servidor físico diferente ou uma máquina cliente como computador de cópia.

2. Abra um pedido de comando elevado no computador de cópia com o diretório de utilidade seleção *AzureOfflineDiskPrep* como o diretório atual, e execute o seguinte comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;percurso de *localização de encenação*&gt; |Entrada obrigatória que é usada para fornecer o caminho para o local de paragem que você entrou no fluxo de trabalho de **backup offline Iniciar.** |
    | p: *&lt;Caminho para publicarDefiniçõesFile*&gt; |Entrada opcional que é usada para fornecer o caminho para o ficheiro Definições de **Publicação Azure** que inseriu no fluxo de trabalho de **backup offline Iniciar.** |

    > [!NOTE]
    > O &lt;Caminho para o AzurePublishSettingFile&gt; valor é obrigatório quando o computador de cópia e o computador de origem são diferentes.
    >
    >

    Quando dirige o comando, o utilitário solicita a seleção do trabalho de Importação Azure que corresponde às unidades que precisam de ser preparadas. Se apenas um único trabalho de importação estiver associado à localização de preparação fornecida, você vê um ecrã como o que se segue.

    ![Entrada da ferramenta de preparação do disco azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

3. Introduza a letra de unidade sem o cólon de rasto para o disco montado que pretende preparar para a transferência para O Azure. Forneça confirmação para a formatação da unidade quando solicitado.

    A ferramenta começa então a preparar o disco e a copiar os dados de cópia. Pode ser necessário anexar discos adicionais quando solicitado pela ferramenta no caso de o disco fornecido não dispor de espaço suficiente para os dados de backup. <br/>

    No final da execução bem sucedida da ferramenta, um ou mais discos fornecidos estão preparados para o envio para O Azure. Além disso, é criado em Azure um trabalho de importação com o nome que forneceu durante o fluxo de trabalho de **backup offline Iniciar.** Por fim, a ferramenta apresenta o endereço de envio para o centro de dados Azure, onde os discos precisam de ser enviados.

    ![Preparação do disco azure completa](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

4. No final da execução do comando, você também vê a opção de atualizar as informações de envio como mostrado abaixo:

    ![Atualizar a opção de informação de envio](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

5. Pode introduzir os detalhes imediatamente. A ferramenta guia-o através do processo envolvendo uma série de inputs. No entanto, se não tiver informações como número de rastreio ou outros detalhes relacionados com o Envio, pode terminar a sessão. As etapas para atualizar os detalhes do envio são fornecidas mais tarde neste artigo.

6. Envie os discos para o endereço que a ferramenta forneceu e guarde o número de rastreio para referência futura.

   > [!IMPORTANT]
   > Nenhum emprego de importação azure pode ter o mesmo número de rastreamento. Certifique-se de que as unidades preparadas pela utilidade sob um único Azure Import Job são enviadas juntas num único pacote e que existe um único número de rastreamento único para o pacote. Não combine unidades preparadas como parte de **diferentes** empregos de importação de Azure num único pacote.

7. Quando tiver a informação sobre o número de rastreio, dirija-se ao computador de origem, que aguarda a conclusão do Import Job e execute o seguinte comando num pedido de comando elevado com o diretório de utilidade seleção *AzureOfflineDiskPrep* como o diretório atual:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Pode executar opcionalmente o seguinte comando a partir de um computador diferente, como o *computador de cópia,* com o diretório de utilidade seletiva *AzureOfflineDiskPrep* como o diretório atual:

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | Descrição |
    | --- | --- |
    | u: | Entrada obrigatória usada para atualizar detalhes de envio para um trabalho de Importação Azure |
    | s:&lt;percurso de *localização de encenação*&gt; | Entrada obrigatória quando o comando não estiver executado no computador de origem. Usado para fornecer o caminho para o local de paragem que inseriu no fluxo de trabalho de **backup offline Iniciar.** |
    | p: *&lt;Caminho para publicarDefiniçõesFile*&gt; | Entrada obrigatória quando o comando não estiver executado no computador de origem. Utilizado para fornecer o caminho para o ficheiro Definições de **Publicação Do Azure** que introduziu no fluxo de trabalho de **backup offline Iniciar.** |

    O utilitário deteta automaticamente o trabalho de Importação que o computador-fonte aguarda ou os postos de importação associados ao local de paragem quando o comando é executado num computador diferente. Em seguida, fornece a opção de atualizar as informações de envio através de uma série de inputs, como mostrado abaixo:

    ![Introdução de Informações de Envio](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

8. Uma vez fornecidas todas as inputs, reveja cuidadosamente os detalhes e comprometa as informações de envio fornecidas escrevendo *sim*.

    ![Rever informações sobre envio](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

9. Ao atualizar a informação de envio com sucesso, o utilitário fornece uma localização local onde os dados de envio introduzidos por você são armazenados como mostrado abaixo

    ![Armazenar informações sobre o envio](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Certifique-se de que as unidades chegam ao Centro de Dados Azure no prazo de duas semanas após fornecer as informações de envio utilizando o utilitário *AzureOfflineBackupDiskPrep.* Se não o fizer, as unidades não sejam processadas.  

Uma vez concluído os passos acima, o Azure Datacenter está pronto para receber as unidades e processá-los para transferir os dados de backup das unidades para a conta de armazenamento de tipo clássico Azure que criou.

### <a name="time-to-process-the-drives"></a>Hora de processar as unidades

O tempo que demora a processar um trabalho de importação do Azure varia consoante os diferentes fatores, tais como o tempo de envio, o tipo de trabalho, o tipo e a dimensão dos dados que estão a ser copiados, e o tamanho dos discos fornecidos. O serviço azure import/exportação não tem um SLA, mas após a receber os discos, o serviço esforça-se por completar a cópia de dados de backup na sua conta de armazenamento Azure em 7 a 10 dias. A secção seguinte detalha como pode monitorizar o estado do trabalho de importação de Azure.

### <a name="monitoring-azure-import-job-status"></a>Monitorizar o estatuto de emprego de importação de Azure

Enquanto as suas unidades estão em trânsito ou no centro de dados Azure para ser copiada para a conta de armazenamento, o agente de backup Azure ou O DPM SC ou a consola do servidor Azure Backup no computador de origem mostram o seguinte estado de trabalho para as suas cópias de segurança programadas.

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Siga os passos abaixo, para verificar o estado do Emprego de Importação.

1. Abra um pedido de comando elevado no computador de origem e execute o seguinte comando:

     `AzureOfflineBackupDiskPrep.exe u:`

2. A produção mostra o estado atual do Emprego de Importação, tal como mostrado a seguir:

    ![Verificação do estatuto do emprego de importação](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para obter mais informações sobre os vários estados do trabalho de importação de Azure, consulte [este artigo](../storage/common/storage-import-export-view-drive-status.md)

### <a name="complete-the-workflow"></a>Complete o fluxo de trabalho

Após o trabalho de importação terminar, os dados iniciais de backup estão disponíveis na sua conta de armazenamento. No momento da próxima cópia de segurança agendada, a cópia de cópia sinuosa do Azure copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação, conforme mostrado abaixo:

   ![Copiar dados para o Cofre de Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento da próxima cópia de segurança agendada, o Azure Backup executa cópia de backup incremental sobre a cópia inicial de backup.

## <a name="next-steps"></a>Passos seguintes

* Para quaisquer questões sobre o fluxo de trabalho de importação/exportação do Azure, consulte [a Utilização do serviço de importação/exportação do Microsoft Azure para transferir dados para](../storage/common/storage-import-export-service.md)o armazenamento blob .
