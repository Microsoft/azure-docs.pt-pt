---
title: Cópia de segurança offline para o DPM e Azure Backup Server
description: Com o Azure Backup, pode enviar dados da rede utilizando o serviço De Importação/Exportação Azure. Este artigo explica o fluxo de trabalho de backup offline para DPM e Azure Backup Server.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 1/28/2020
ms.openlocfilehash: 080b0bc53b2058bd186e90f354b8f5bcda510414
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "78197085"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server"></a>Fluxo de trabalho de backup offline para DPM e Servidor de Backup Azure

A Azure Backup tem várias eficiências incorporadas que poupam custos de rede e armazenamento durante as cópias de segurança iniciais completas de dados para o Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e requerem mais largura de banda da rede quando comparadas com as cópias de segurança subsequentes que transferem apenas os deltas/incrementais. O Azure Backup comprime as cópias de segurança iniciais. Através do processo de sementeira offline, o Azure Backup pode usar discos para carregar os dados de backup inicial comprimido offline para O Azure.

O processo de sementeação offline da Azure Backup está fortemente integrado com o [serviço de importação/exportação Azure.](../storage/common/storage-import-export-service.md) Pode utilizar este serviço para transferir dados para o Azure utilizando discos. Se tiver terabytes (TBs) de dados de backup iniciais que precisam de ser transferidos através de uma rede de alta latência e largura de banda, pode utilizar o fluxo de trabalho offline para enviar a cópia de cópia de cópia de reserva inicial num ou mais discos rígidos para um centro de dados Azure. Este artigo fornece uma visão geral e outros passos que terminam este fluxo de trabalho para System Center Data Protection Manager (DPM) e Microsoft Azure Backup Server (MABS).

> [!NOTE]
> O processo de backup offline para o Agente de Serviços de Recuperação do Microsoft Azure (MARS) é distinto do DPM e do MABS. Para obter informações sobre a utilização de cópias de segurança offline com o Agente MARS, consulte o [fluxo de trabalho de backup offline em Azure Backup](backup-azure-backup-import-export.md). A cópia de segurança offline não é suportada para cópias de segurança do estado do sistema feitas usando o Agente de Backup Azure.
>

## <a name="overview"></a>Descrição geral

Com a capacidade offline de sementeir do Azure Backup e do serviço de importação/exportação Azure, é simples enviar os dados offline para o Azure utilizando discos. O processo de backup offline envolve os seguintes passos:

> [!div class="checklist"]
>
> * Os dados de cópia de segurança são escritos para um local de encenação em vez de serem enviados pela rede.
> * Os dados sobre a localização da encenação são então escritos para um ou mais discos SATA utilizando o utilitário *AzureOfflineBackupDiskPrep.*
> * Um trabalho de importação azure é automaticamente criado pela utilidade.
> * As unidades SATA são então enviadas para o centro de dados Azure mais próximo.
> * Após o upload dos dados de backup para o Azure, o Azure Backup copia os dados de backup para o cofre de backup e as cópias de segurança incrementais estão agendadas.

## <a name="supported-configurations"></a>Configurações suportadas

A cópia de segurança offline é suportada para todos os modelos de implementação do Azure Backup que confirmam os dados desde o local até à nuvem da Microsoft. Estes modelos incluem:

> [!div class="checklist"]
>
> * Cópia de segurança de ficheiros e pastas com o Agente MARS ou o Agente de Backup Azure.
> * Cópia de segurança de todas as cargas de trabalho e ficheiros com DPM.
> * Cópia de segurança de todas as cargas de trabalho e ficheiros com MABS.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de iniciar o fluxo de trabalho de backup offline:

* Foi criado um cofre dos Serviços de [Recuperação.](backup-azure-recovery-services-vault-overview.md) Para criar um, siga os passos em Criar um cofre de Serviços de [Recuperação](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Um Agente de Backup Azure ou MABS ou DPM foi instalado no Windows Server ou num cliente Windows, conforme aplicável, e o computador está registado no cofre dos Serviços de Recuperação. Certifique-se de que apenas a [versão mais recente do Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) é utilizada.
* [Descarregue o ficheiro](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) de definições de publicação do Azure no computador a partir do qual planeia fazer o back-up dos seus dados. A subscrição a partir da qual descarrega o ficheiro de definições de publicação pode ser diferente da subscrição que contém o cofre dos Serviços de Recuperação. Se a sua subscrição estiver em nuvens azure soberanas, utilize os seguintes links conforme apropriado para descarregar o ficheiro de definições de publicação do Azure.

    | Região de nuvens soberanas | Azure publicar link de ficheiros de definições |
    | --- | --- |
    | Estados Unidos | [Ligação](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Ligação](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Foi criada uma conta de armazenamento Azure com o modelo de implementação do Gestor de Recursos na subscrição a partir da qual descarregou o ficheiro de definições de publicação.

  ![Criar uma conta de armazenamento com desenvolvimento do Gestor de Recursos](./media/backup-azure-backup-import-export/storage-account-resource-manager.png)

* É criado um local de encenação, que pode ser uma partilha de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço suficiente para guardar a sua cópia inicial. Por exemplo, se quiser fazer o reforço de um servidor de ficheiros de 500 GB, certifique-se de que a área de paragem é de pelo menos 500 GB. (Uma quantidade menor é utilizada devido à compressão.)
* Para discos enviados para o Azure, certifique-se de que são utilizados apenas 2,5 polegadas SSD ou 2,5 polegadas ou 3,5 polegadas de disco rígido interno SATA II/III. Pode utilizar discos rígidos até 10 TB. Consulte a documentação do [serviço Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) para obter o mais recente conjunto de unidades que o serviço suporta.
* As unidades SATA devem ser ligadas a um computador (referido como *um computador de cópia)* de onde a cópia dos dados de cópia da localização de paragem para as unidades SATA é feita. Certifique-se de que o BitLocker está ativado no computador de cópia.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Prepare o servidor para o processo de backup offline

>[!NOTE]
> Se não encontrar os utilitários listados, como *o AzureOfflineBackupCertGen.exe*, na sua AskAzureBackupTeam@microsoft.com instalação do Agente MARS, escreva para ter acesso aos mesmos.

* Abra um pedido de comando elevado no servidor e execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    A ferramenta cria uma Aplicação de Diretório Ativo de Backup Offline Azure se não existir.

    Se já existir uma aplicação, esta executável pede-lhe que carregue manualmente o certificado para o pedido no arrendatário. Siga os passos [nesta secção](#manually-upload-an-offline-backup-certificate) para fazer o upload do certificado manualmente para a aplicação.

* A ferramenta *AzureOfflineBackup.exe* gera um ficheiro *OfflineApplicationParams.xml.* Copie este ficheiro para o servidor com MABS ou DPM.
* Instale o [mais recente agente MARS](https://aka.ms/azurebackup_agent) na instância DPM ou no servidor de backup Azure.
* Registe o servidor no Azure.
* Execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname configured with Azure Data Box>
    ```

* O comando anterior `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml`cria o ficheiro .

## <a name="manually-upload-an-offline-backup-certificate"></a>Faça upload manual de um certificado de backup offline

Siga estes passos para carregar manualmente o certificado de backup offline para uma aplicação de Diretório Ativo Azure previamente criada, destinada a cópia de segurança offline.

1. Inicie sessão no Portal do Azure.
1. Vá às > **inscrições**da App de **Diretório Ativo Azure.**
1. No separador **de aplicações Owned,** localize `AzureOfflineBackup _<Azure User Id`uma aplicação com o formato de nome do ecrã .

    ![Localizar aplicação no separador de aplicações próprias](./media/backup-azure-backup-import-export/owned-applications.png)

1. Selecione a aplicação. Sob **a Gestão** no painel esquerdo, vá a **Certificados & segredos.**
1. Verifique se existem certificados pré-existentes ou chaves públicas. Se não houver nenhuma, pode eliminar a aplicação com segurança selecionando o botão **Eliminar** na página **de visão geral** da aplicação. Em seguida, pode voltar a tentar os passos para [preparar o servidor para o](#prepare-the-server-for-the-offline-backup-process) processo de backup offline e saltar os seguintes passos. Caso contrário, continue a seguir estes passos a partir da instância DPM ou do servidor de backup Azure, onde pretende configurar a cópia de segurança offline.
1. Selecione o separador > **pessoal** de formulário de `CB_AzureADCertforOfflineSeeding_<ResourceId>` **computador Gerir.** Procure o certificado com o nome .
1. Selecione o certificado, clique à direita **em Todas as Tarefas,** e, em seguida, selecione **Exportação,** sem chave privada, no formato .cer.
1. Vá à aplicação de backup offline Azure no portal Azure.
1. Selecione **Gerir** > **Certificados & segredos** > **Enviar certificado**. Faça o upload do certificado exportado no passo anterior.

    ![Carregar o certificado](./media/backup-azure-backup-import-export/upload-certificate.png)

1. No servidor, abra o registo entrando em **regedite** na janela de execução.
1. Vá para a entrada de registo *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*.
1. Clique no **CloudBackupProvider**e adicione um novo `AzureADAppCertThumbprint_<Azure User Id>`valor de cadeia com o nome .

    >[!NOTE]
    > Para encontrar o ID do utilizador Azure, faça um dos seguintes passos:
    >
    >* A partir da PowerShell ligada `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as appears in the portal”` ao Azure, executar o comando.
    >* Vá para o `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;`caminho do registo.

1. Clique na corda adicionada no passo anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que exportou no passo 7. Em seguida, selecione **OK**.
1. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione o separador **Detalhes** e desloque-se para baixo até ver o campo de impressão digital. Selecione **Impressão Digital**e copie o valor.

    ![Valor de cópia do campo de impressão digital](./media/backup-azure-backup-import-export/thumbprint-field.png)

1. Continue na secção [Workflow](#workflow) para prosseguir com o processo de backup offline.

## <a name="workflow"></a>Fluxo de trabalho

A informação nesta secção ajuda-o a terminar o fluxo de trabalho de backup offline para que os seus dados possam ser entregues num centro de dados Azure e enviados para o Armazenamento Azure. Se tiver dúvidas sobre o serviço de importação ou qualquer aspeto do processo, consulte a [documentação](../storage/common/storage-import-export-service.md) geral do serviço de importação referenciada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar cópia seleção offline

1. Ao agendar uma cópia de segurança, vê a seguinte página no Windows Server, num cliente do Windows ou em DPM.

    ![Página de importação](./media/backup-azure-backup-import-export/offlineBackupscreenInputs.png)

    Aqui está a página correspondente no DPM. <br/>
    
    ![Página de importação do Servidor de Backup DPM e Azure](./media/backup-azure-backup-import-export/dpmoffline.png)

    As caixas que preenche são:

   * **Local de preparação**: O local de armazenamento temporário para o qual a cópia inicial de cópia de cópia de reserva está escrita. O local de preparação pode estar numa partilha de rede ou num computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o percurso de rede completo do local de paragem.
   * **Definições**de publicação do Azure : O caminho local para o ficheiro de definições de publicação.
   * Nome do trabalho de **importação azure**: O nome único pelo qual o serviço azure import/exportação e a cópia de segurança Azure acompanham a transferência de dados enviados em discos para o Azure.
   * **ID de subscrição Azure**: O ID de subscrição do Azure para a subscrição a partir do qual descarregou o ficheiro de definições de publicação do Azure.
   * **Conta de Armazenamento Azure**: O nome da conta de armazenamento na subscrição Azure associada ao ficheiro de definições de publicação do Azure.
   * **Recipiente de armazenamento Azure**: O nome da bolha de armazenamento de destino na conta de armazenamento Azure onde os dados de backup são importados.

   Guarde a localização de **preparação** e as informações do Nome de **Trabalho de Importação Azure** que forneceu. É necessário preparar os discos.

1. Termine o fluxo de trabalho. Para iniciar a cópia de cópia de backup offline, selecione **Back Up Now** na consola de gestão do Agente de Backup Azure. O backup inicial está escrito para a área de preparação como parte deste passo.

    ![Backup agora](./media/backup-azure-backup-import-export/backupnow.png)

    Para terminar o fluxo de trabalho correspondente no DPM ou no Servidor de Backup Azure, clique à direita no Grupo de **Proteção**. Selecione a opção ponto de **recuperação Criar.** Em seguida, selecione a opção **De Proteção Online.**

    ![DPM e MABS back up now](./media/backup-azure-backup-import-export/dpmbackupnow.png)

    Após o fim da operação, o local de preparação está pronto para ser utilizado para a preparação do disco.

    ![Progresso de backup](./media/backup-azure-backup-import-export/opbackupnow.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Prepare unidades da SATA e envie para Azure

O utilitário *AzureOfflineBackupDiskPrep* é utilizado para preparar as unidades SATA que são enviadas para o centro de dados Azure mais próximo. Este utilitário está disponível no diretório de instalação do Agente de Serviços de Recuperação no seguinte caminho:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Vá ao diretório e copie o diretório *AzureOfflineDiskPrep* para um computador de cópia no qual as unidades SATA para serem preparadas estão ligadas. Certifique-se de que:

   * O computador de cópia pode aceder ao local de paragem do fluxo de trabalho offline de sementeing utilizando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na secção "Iniciar cópias de segurança offline".
   * O BitLocker está ativado no computador de cópia.
   * O computador de cópia pode aceder ao portal Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador-fonte.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, então é obrigatório usar um servidor físico diferente ou máquina cliente como computador de cópia.

1. Abra um pedido de comando elevado no computador de cópia com o diretório de utilidade seleção *AzureOfflineBackupPrep* como o diretório atual. Execute o seguinte comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*Caminho de Localização de Encenação*&gt; |Esta entrada obrigatória é utilizada para fornecer o caminho para o local de paragem que inseriu no fluxo de trabalho na secção "Iniciar backup offline". |
    | p:&lt;*Caminho para publicarDefiniçõesFile*&gt; |Esta entrada opcional é utilizada para fornecer o caminho para o ficheiro de definições de publicação do Azure que introduziu no fluxo de trabalho na secção "Iniciar backup offline". |

    > [!NOTE]
    > O &lt;valor Do Caminho&gt; para o AzurePublishSettingFile é obrigatório quando o computador de cópia e o computador de origem são diferentes.
    >
    >

    Quando dirige o comando, o utilitário solicita a seleção do trabalho de importação de Azure que corresponde às unidades que precisam de ser preparadas. Se apenas um único trabalho de importação estiver associado à localização de preparação fornecida, você vê uma página como esta.

    ![Entrada da ferramenta de preparação do disco azure](./media/backup-azure-backup-import-export/azureDiskPreparationToolDriveInput.png) <br/>

1. Introduza a letra de unidade sem o cólon de rasto para o disco montado que pretende preparar para a transferência para O Azure. Quando solicitado, forneça confirmação para a formatação da unidade.

    A ferramenta começa então a preparar o disco e a copiar os dados de cópia. Pode ser necessário anexar discos adicionais quando solicitado pela ferramenta no caso de o disco fornecido não dispor de espaço suficiente para os dados de backup. <br/>

    Depois de a ferramenta terminar com sucesso, um ou mais discos que forneceu estão preparados para o envio para o Azure. Um trabalho de importação com o nome que forneceu durante o fluxo de trabalho na secção "Iniciar backup offline" também é criado em Azure. Por fim, a ferramenta apresenta o endereço de envio para o centro de dados Azure, onde os discos precisam de ser enviados.

    ![Preparação do disco azure terminada](./media/backup-azure-backup-import-export/azureDiskPreparationToolSuccess.png)<br/>

1. No final da execução do comando, você também vê a opção de atualizar informações de envio.

    ![Atualizar a opção de informação de envio](./media/backup-azure-backup-import-export/updateshippingutility.png)<br/>

1. Pode introduzir os detalhes imediatamente. A ferramenta guia-o através do processo que envolve uma série de inputs. Se não tiver informações como o número de rastreio ou outros detalhes relacionados com o envio, pode terminar a sessão. As etapas para atualizar os detalhes do envio são fornecidas mais tarde neste artigo.

1. Envie os discos para o endereço que a ferramenta forneceu. Mantenha o número de rastreio para referência futura.

   > [!IMPORTANT]
   > Nenhum emprego de importação azure pode ter o mesmo número de rastreio. Certifique-se de que as unidades preparadas pela utilidade sob um único trabalho de importação azure são enviadas juntas num único pacote e que há um único número de rastreamento único para o pacote. Não combine unidades preparadas como parte de diferentes empregos de importação azure num único pacote.

1. Quando tiver a informação do número de rastreio, vá ao computador de origem, que aguarda a conclusão do emprego de importação. Execute o seguinte comando num pedido de comando elevado com o diretório de utilidade seleção *AzureOfflineDiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Pode executar opcionalmente o seguinte comando a partir de um computador diferente, como o computador de cópia, com o diretório de utilidade seletiva *AzureOfflineDiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | Descrição |
    | --- | --- |
    | u: | Esta entrada obrigatória é usada para atualizar detalhes de envio para um trabalho de importação azure. |
    | s:&lt;*Caminho de Localização de Encenação*&gt; | Esta entrada obrigatória é usada quando o comando não é executado no computador de origem. É usado para fornecer o caminho para o local de paragem que você entrou no fluxo de trabalho na secção "Iniciar backup offline". |
    | p:&lt;*Caminho para publicarDefiniçõesFile*&gt; | Esta entrada obrigatória é usada quando o comando não é executado no computador de origem. É utilizado para fornecer o caminho para o ficheiro de definições de publicação do Azure que introduziu no fluxo de trabalho na secção "Iniciar backup offline". |

    O utilitário deteta automaticamente o trabalho de importação que o computador-fonte aguarda ou os postos de importação associados ao local de paragem quando o comando é executado num computador diferente. Em seguida, fornece a opção de atualizar informações de envio através de uma série de inputs.

    ![Insira informações de envio](./media/backup-azure-backup-import-export/shippinginputs.png)<br/>

1. Depois de todas as inputs serem fornecidas, reveja cuidadosamente os detalhes e comprometa as informações de envio que forneceu ao introduzir **sim**.

    ![Rever as informações de envio](./media/backup-azure-backup-import-export/reviewshippinginformation.png)<br/>

1. Após a atualização das informações de envio com sucesso, o utilitário fornece uma localização local onde os dados de envio que inseriu são armazenados.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/storingshippinginformation.png)<br/>

   > [!IMPORTANT]
   > Certifique-se de que as unidades chegam ao centro de dados Azure no prazo de duas semanas após fornecer as informações de envio utilizando o utilitário *AzureOfflineBackupDiskPrep.* Se não o fizer, as unidades não sejam processadas. 

Depois de terminar os passos anteriores, o datacenter Azure está pronto para receber as unidades e processá-los para transferir os dados de backup das unidades para a conta de armazenamento azure de tipo clássico que criou.

### <a name="time-to-process-the-drives"></a>Hora de processar as unidades

O tempo que demora a processar um trabalho de importação de Azure varia. O tempo de processo depende de fatores como o tempo de envio, o tipo de trabalho, o tipo e o tamanho dos dados que estão a ser copiados, e o tamanho dos discos fornecidos. O serviço de importação/exportação Azure não tem um SLA. Após a recebido dos discos, o serviço esforça-se por terminar a cópia de dados de cópia de cópia de cópia de cópia de segurança na sua conta de armazenamento Azure em 7 a 10 dias. A secção seguinte descreve como pode monitorizar o estado do trabalho de importação de Azure.

### <a name="monitor-azure-import-job-status"></a>Monitor Azure importe estatuto de emprego

Enquanto as suas unidades estão em trânsito ou no centro de dados Azure para serem copiadas para a conta de armazenamento, o Agente de Backup Azure ou DPM ou a consola MABS no computador de origem mostram o seguinte estado de trabalho para as suas cópias de segurança programadas:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Para verificar o estado do emprego de importação:

1. Abra um pedido de comando elevado no computador de origem e execute o seguinte comando:

     `AzureOfflineBackupDiskPrep.exe u:`

1. O resultado mostra o estado atual do trabalho de importação.

    ![Verifique o estado do emprego de importação](./media/backup-azure-backup-import-export/importjobstatusreporting.png)<br/>

Para obter mais informações sobre os vários estados do emprego de importação de Azure, consulte o estatuto dos postos de trabalho de [importação/exportação de Azure.](../storage/common/storage-import-export-view-drive-status.md)

### <a name="finish-the-workflow"></a>Termine o fluxo de trabalho

Após o trabalho de importação terminar, os dados iniciais de backup estão disponíveis na sua conta de armazenamento. No momento da próxima cópia de segurança agendada, o Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação.

   ![Copiar dados para cofre de Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento da próxima cópia de segurança agendada, o Azure Backup executa cópia de backup incremental sobre a cópia inicial de backup.

## <a name="next-steps"></a>Passos seguintes

* Para quaisquer questões sobre o fluxo de trabalho do serviço de importação/exportação Azure, consulte [Utilize o serviço de importação/exportação da Microsoft Azure para transferir dados para](../storage/common/storage-import-export-service.md)o armazenamento blob .
