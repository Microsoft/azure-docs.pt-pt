---
title: Backup offline para Gestor de Proteção de Dados (DPM) e Microsoft Azure Backup Server (MABS) - versões anteriores
description: Com a Azure Backup, pode enviar dados para fora da rede utilizando o serviço Azure Import/Export. Este artigo explica o fluxo de trabalho de backup offline para versões anteriores do DPM e do Azure Backup Server.
ms.topic: conceptual
ms.date: 06/08/2020
ms.openlocfilehash: 0405ab66b7714f00349419e94bb064267ca711a6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98702190"
---
# <a name="offline-backup-workflow-for-dpm-and-azure-backup-server-previous-versions"></a>Fluxo de trabalho de backup offline para DPM e Azure Backup Server (versões anteriores)

>[!IMPORTANT]
>Estes passos aplicam-se ao DPM 2019 RTM e versões anteriores e versões MABS v3 RTM e anteriores.

O Azure Backup tem várias eficiências incorporadas que poupam custos de rede e armazenamento durante as cópias de segurança completas iniciais de dados para o Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e requerem mais largura de banda de rede quando comparadas com as cópias de segurança subsequentes que transferem apenas os deltas/incrementais. O Azure Backup comprime as cópias de segurança iniciais. Através do processo de sementeira offline, o Azure Backup pode usar discos para enviar os dados de backup iniciais comprimidos offline para Azure.

O processo de sementeira offline da Azure Backup está fortemente integrado com o [serviço Azure Import/Export](../import-export/storage-import-export-service.md). Pode utilizar este serviço para transferir dados para a Azure utilizando discos. Se tiver terabytes (TBs) de dados de backup iniciais que precisam de ser transferidos através de uma rede de alta latência e de baixa largura de banda, pode utilizar o fluxo de trabalho de sementeira offline para enviar a cópia de backup inicial num ou mais discos rígidos para um datacenter Azure. Este artigo fornece uma visão geral e outros passos que terminam este fluxo de trabalho para System Center Data Protection Manager (DPM) e Microsoft Azure Backup Server (MABS).

> [!NOTE]
> O processo de cópia de segurança offline para o Agente microsoft Azure Recovery Services (MARS) é distinto do DPM e do MABS. Para obter informações sobre a utilização de cópias de segurança offline com o Agente MARS, consulte [o fluxo de trabalho de backup offline no Azure Backup](backup-azure-backup-import-export.md). A cópia de segurança offline não é suportada por cópias de segurança do estado do sistema feitas utilizando o Agente de Backup Azure.
>

## <a name="overview"></a>Descrição Geral

Com a capacidade de sementeira offline do Azure Backup e do serviço Azure Import/Export, é simples enviar os dados offline para Azure utilizando discos. O processo de backup offline envolve os seguintes passos:

> [!div class="checklist"]
>
> * Os dados de backup são escritos para um local de preparação em vez de serem enviados através da rede.
> * Os dados sobre a localização da encenação são então escritos para um ou mais discos SATA utilizando o utilitário *AzureOfflineBacklineBackDiskPrep.*
> * Um trabalho de importação Azure é automaticamente criado pela utilidade.
> * As unidades SATA são então enviadas para o centro de dados Azure mais próximo.
> * Após o envio dos dados de backup para a Azure, o Azure Backup copia os dados de backup para o cofre de backup e as cópias de segurança incrementais estão agendadas.

## <a name="supported-configurations"></a>Configurações suportadas

A cópia de segurança offline é suportada por todos os modelos de implementação do Azure Backup que faz o backup de dados das instalações para a nuvem da Microsoft. Estes modelos incluem:

> [!div class="checklist"]
>
> * Cópia de segurança de ficheiros e pastas com o Agente MARS ou o Agente de Reserva Azure.
> * Cópia de segurança de todas as cargas de trabalho e ficheiros com DPM.
> * Cópia de segurança de todas as cargas de trabalho e ficheiros com MABS.

>[!NOTE]
>As subscrições do Azure CSP não são suportadas para utilização com sementeira offline para DPM 2019 RTM e versões anteriores, e MABS v3 RTM e versões anteriores. As cópias de segurança online da rede ainda são suportadas.

## <a name="prerequisites"></a>Pré-requisitos

Certifique-se de que os seguintes pré-requisitos são cumpridos antes de iniciar o fluxo de trabalho de backup offline:

* Foi criado [um cofre dos Serviços de Recuperação.](backup-azure-recovery-services-vault-overview.md) Para criar um, siga os passos no [cofre criar um cofre de serviços de recuperação](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Um Agente de Backup Azure ou MABS ou DPM foi instalado no Windows Server ou num cliente Windows, conforme aplicável, e o computador está registado no cofre dos Serviços de Recuperação. Certifique-se de que apenas a [versão mais recente do Azure Backup](https://go.microsoft.com/fwlink/?linkid=229525) é utilizada.
* [Descarregue o ficheiro de definições de publicação Azure](https://portal.azure.com/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) no computador a partir do qual planeia fazer o armazenamento dos seus dados. A subscrição a partir da qual descarrega o ficheiro de definições de publicação pode ser diferente da subscrição que contém o cofre dos Serviços de Recuperação. Se a sua subscrição estiver em nuvens Azure soberanas, use os seguintes links conforme apropriado para descarregar o ficheiro de definições de publicação Azure.

    | Região de nuvem soberana | Azure publicar o link de ficheiros de definições |
    | --- | --- |
    | Estados Unidos da América | [Ligação](https://portal.azure.us#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |
    | China | [Ligação](https://portal.azure.cn/#blade/Microsoft_Azure_ClassicResources/PublishingProfileBlade) |

* Foi criada uma conta de armazenamento Azure com o modelo de implementação do Gestor de Recursos na subscrição a partir da qual descarregou o ficheiro de definições de publicação. Na conta de armazenamento, crie um novo recipiente blob, que será usado como destino.

  ![Criar uma conta de armazenamento com desenvolvimento do Gestor de Recursos](./media/offline-backup-dpm-mabs-previous-versions/storage-account-resource-manager.png)

* É criada uma localização de encenação, que pode ser uma partilha de rede ou qualquer unidade adicional no computador, interna ou externa, com espaço suficiente em disco para segurar a sua cópia inicial. Por exemplo, se pretender fazer o back up de um servidor de ficheiros de 500 GB, certifique-se de que a área de paragem é de pelo menos 500 GB. (Uma quantidade menor é utilizada devido à compressão.)
* Para os discos enviados para a Azure, certifique-se de que apenas são utilizados discos rígidos internos SATA II/III de 2,5 polegadas ou 3,5 polegadas. Pode utilizar discos rígidos até 10 TB. Consulte a documentação do [serviço Azure Import/Export](../import-export/storage-import-export-requirements.md#supported-hardware) para obter o mais recente conjunto de unidades que o serviço suporta.
* As unidades SATA devem ser ligadas a um computador (referido como *um computador de cópia*) de onde é feita a cópia dos dados de backup do local de preparação para as unidades SATA. Certifique-se de que o BitLocker está ativado no computador de cópia.

## <a name="prepare-the-server-for-the-offline-backup-process"></a>Prepare o servidor para o processo de backup offline

>[!NOTE]
> Se não conseguir encontrar os utilitários listados, como *AzureOfflineBackupCertGen.exe,* na sua instalação do Agente MARS, escreva AskAzureBackupTeam@microsoft.com para ter acesso aos mesmos.

* Abra uma solicitação de comando elevada no servidor e execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe CreateNewApplication SubscriptionId:<Subs ID>
    ```

    A ferramenta cria uma aplicação de diretório ativo de backup offline Azure se não existir.

    Se já existe uma aplicação, esta executável pede-lhe para fazer o upload manual do certificado para o pedido no arrendatário. Siga os passos [desta secção](#manually-upload-an-offline-backup-certificate) para fazer o upload do certificado manualmente para a aplicação.

* A ferramenta *AzureOfflineBackupCertGen.exe* gera um ficheiro *OfflineApplicationParams.xml.* Copie este ficheiro para o servidor com MABS ou DPM.
* Instale o [mais recente agente MARS](https://aka.ms/azurebackup_agent) na instância DPM ou no servidor Azure Backup.
* Registe o servidor para Azure.
* Execute o seguinte comando:

    ```cmd
    AzureOfflineBackupCertGen.exe AddRegistryEntries SubscriptionId:<subscriptionid> xmlfilepath:<path of the OfflineApplicationParams.xml file>  storageaccountname:<storageaccountname to be used for offline backup>
    ```

* O comando anterior cria o ficheiro `C:\Program Files\Microsoft Azure Recovery Services Agent\Scratch\MicrosoftBackupProvider\OfflineApplicationParams_<Storageaccountname>.xml` .

## <a name="manually-upload-an-offline-backup-certificate"></a>Faça o upload manual de um certificado de backup offline

Siga estes passos para enviar manualmente o certificado de cópia de segurança offline para uma aplicação de Diretório Azure Ative anteriormente criada destinada a cópias de segurança offline.

1. Inicie sessão no portal do Azure.
1. Aceda às inscrições da **Azure Ative Directory**  >  **App**.
1. No **separador aplicações Possuídas,** localize uma aplicação com o formato de nome de `AzureOfflineBackup _<Azure User Id` exibição.

    ![Localizar aplicação no separador de aplicações próprias](./media/offline-backup-dpm-mabs-previous-versions/owned-applications.png)

1. Selecione a inscrição. Under **Manage** no painel esquerdo, vá a **Certificados & segredos**.
1. Verifique se existem certificados ou chaves públicas. Se não houver nenhuma, pode eliminar com segurança a aplicação selecionando o botão **Eliminar** na página **'Vista Geral'** da aplicação. Em seguida, pode voltar a tentar os passos para [preparar o servidor para o](#prepare-the-server-for-the-offline-backup-process) processo de backup offline e saltar os seguintes passos. Caso contrário, continue a seguir estes passos a partir da instância DPM ou do servidor Azure Backup onde pretende configurar a cópia de segurança offline.
1. From **Start** – **Run**, type *Certlm.msc*. Nos **Certificados -** Janela do Computador Local, selecione os Certificados – Separador Pessoal **do Computador Local.** Procure o certificado com o nome  >   `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
1. Selecione o certificado, clique com o botão direito **Todas as Tarefas** e, em seguida, selecione **Export**, sem uma chave privada, no formato .cer.
1. Aceda à aplicação de backup offline Azure no portal Azure.
1. **Selecione Gerir**  >  **certificados &**  >  **segredos Enviar por isso certificado de upload** de segredos . Faça o upload do certificado exportado no passo anterior.

    ![Carregar o certificado](./media/offline-backup-dpm-mabs-previous-versions/upload-certificate.png)

1. No servidor, abra o registo introduzindo **o regedit** na janela de execução.
1. Vá ao registo *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.*
1. Clique com o botão direito **CloudBackupProvider** e adicione um novo valor de corda com o nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Para encontrar o ID do utilizador Azure, faça um dos seguintes passos:
    >
    >* Da PowerShell ligada ao Azure, executar o `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as appears in the portal"` comando.
    >* Vá para o caminho do `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup; Name: CurrentUserId;` registo.

1. Clique com o botão direito da cadeia adicionada no passo anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que exportou no passo 7. Em seguida, selecione **OK**.
1. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione o separador **Detalhes** e desloque-se para baixo até ver o campo de impressão digital. Selecione **a impressão digital** e copie o valor.

    ![Valor de cópia do campo de impressão digital](./media/offline-backup-dpm-mabs-previous-versions/thumbprint-field.png)

1. Continue na secção [workflow](#workflow) para prosseguir com o processo de backup offline.

## <a name="workflow"></a>Fluxo de trabalho

As informações nesta secção ajudam-no a terminar o fluxo de trabalho de backup offline para que os seus dados possam ser entregues num datacenter Azure e enviados para o Azure Storage. Se tiver dúvidas sobre o serviço de importação ou qualquer aspeto do processo, consulte a [documentação geral](../import-export/storage-import-export-service.md) do serviço De importação referenciada anteriormente.

### <a name="initiate-offline-backup"></a>Iniciar backup offline

1. Quando agenda uma cópia de segurança, vê a página seguinte no Windows Server, num cliente Windows ou no DPM.

    ![Página de importação](./media/offline-backup-dpm-mabs-previous-versions/offline-backup-screen-inputs.png)

    Aqui está a página correspondente no DPM. <br/>

    ![Página de importação de servidor de backup DPM e Azure](./media/offline-backup-dpm-mabs-previous-versions/dpm-offline.png)

    As caixas que preenche são:

   * **Localização de localização**: Local de armazenamento temporário para o qual está escrita a cópia de reserva inicial. A localização da paragem pode estar numa partilha de rede ou num computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o caminho completo da rede do local de paragem.
   * **Definições de publicação Azure**: O caminho local para o ficheiro de definições de publicação.
   * **Nome de emprego de importação Azure**: O nome único pelo qual o serviço Azure Import/Export e o Azure Backup acompanham a transferência de dados enviados em discos para a Azure.
   * **ID de assinatura Azure**: O ID de subscrição Azure para a subscrição de onde descarregou o ficheiro de definições de publicação Azure.
   * **Conta de Armazenamento Azure**: O nome da conta de armazenamento na subscrição Azure associada ao ficheiro de definições de publicação Azure.
   * **Recipiente de armazenamento Azure**: O nome da bolha de armazenamento de destino na conta de armazenamento Azure onde os dados de reserva são importados.

   Guarde a **localização de preparação** e as informações sobre o **Nome do Trabalho da Importação de Azure** que forneceu. É necessário preparar os discos.

1. Termine o fluxo de trabalho. Para iniciar a cópia de backup offline, selecione **Back Up Now** na consola de gestão de agente de backup Azure. A cópia de segurança inicial é escrita para a área de preparação como parte deste passo.

    ![Backup agora](./media/offline-backup-dpm-mabs-previous-versions/backup-now.png)

    Para terminar o fluxo de trabalho correspondente no DPM ou no Azure Backup Server, clique com o botão direito do **Grupo de Proteção**. Selecione a opção **Ponto de recuperação Criar.** Em seguida, selecione a opção **Proteção Online.**

    ![DPM e MABS Back Up Agora](./media/offline-backup-dpm-mabs-previous-versions/dpm-backup-now.png)

    Após o fim da operação, o local de preparação está pronto para ser utilizado para a preparação do disco.

    ![Progresso de backup](./media/offline-backup-dpm-mabs-previous-versions/op-backup-now.png)

### <a name="prepare-sata-drives-and-ship-to-azure"></a>Prepare as unidades da SATA e envie-a para Azure

O utilitário *AzureOfflineBackupDiskPrep* é utilizado para preparar as unidades SATA que são enviadas para o centro de dados Azure mais próximo. Este utilitário está disponível no diretório de instalação do Agente de Serviços de Recuperação no seguinte caminho:

`*\\Microsoft Azure Recovery Services Agent\Utils\*`

1. Vá ao diretório e copie o diretório *AzureOfflineBackupDiskPrep* para um computador de cópia no qual as unidades SATA a serem preparadas estão ligadas. Certifique-se de que:

   * O computador de cópia pode aceder ao local de preparação para o fluxo de trabalho de sementeira offline utilizando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na secção "Iniciar cópia de segurança offline".
   * O BitLocker está ativado no computador de cópia.
   * O computador de cópia pode aceder ao portal Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, então é obrigatório usar um servidor físico diferente ou uma máquina de cliente como o computador de cópia.

1. Abra um pedido de comando elevado no computador de cópia com o diretório de utilidade *AzureOfflineBackupDiskPrep* como o diretório atual. Execute o seguinte comando:

    `*.\AzureOfflineBackupDiskPrep.exe*   s:<*Staging Location Path*>   [p:<*Path to AzurePublishSettingsFile*>]`

    | Parâmetro | Descrição |
    | --- | --- |
    | s: &lt; *Caminho de localização de encenação*&gt; |Esta entrada obrigatória é utilizada para fornecer o caminho para o local de preparação que inseriu no fluxo de trabalho na secção "Iniciar backup offline". |
    | p: &lt; *Caminho para publicarSettingsFile*&gt; |Esta entrada opcional é utilizada para fornecer o caminho para o ficheiro de definições de publicação Azure que inseriu no fluxo de trabalho na secção "Iniciar cópia de segurança offline". |

    > [!NOTE]
    > O &lt; valor Caminho para AzurePublishSettingFile é obrigatório quando o computador de cópia e o computador de &gt; origem são diferentes.
    >
    >

    Quando você dirige o comando, o utilitário solicita a seleção do trabalho de importação Azure que corresponde às unidades que precisam de ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de preparação fornecido, você vê uma página como esta.

    ![Entrada da ferramenta de preparação do disco Azure](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-drive-input.png) <br/>

1. Introduza a letra de unidade sem o cólon de fuga para o disco montado que deseja preparar para a transferência para Azure. Quando solicitado, forneça a confirmação da formatação da unidade.

    A ferramenta começa então a preparar o disco e a copiar os dados de cópia de segurança. Pode ser necessário anexar discos adicionais quando solicitado pela ferramenta se o disco fornecido não tiver espaço suficiente para os dados de cópia de segurança. <br/>

    Depois de a ferramenta terminar com sucesso, um ou mais discos que forneceu estão preparados para o envio para Azure. Um trabalho de importação com o nome que forneceu durante o fluxo de trabalho na secção "Iniciar backup offline" também é criado em Azure. Finalmente, a ferramenta exibe o endereço de envio para o centro de dados Azure onde os discos precisam de ser enviados.

    ![A preparação do disco azul terminou](./media/offline-backup-dpm-mabs-previous-versions/azure-disk-preparation-tool-success.png)<br/>

1. No final da execução do comando, também vê a opção de atualizar as informações de envio.

    ![Atualizar opção de informação de envio](./media/offline-backup-dpm-mabs-previous-versions/update-shipping-utility.png)<br/>

1. Pode inserir os detalhes imediatamente. A ferramenta guia-o através do processo que envolve uma série de entradas. Se não tiver informações como o número de rastreio ou outros detalhes relacionados com o envio, pode terminar a sessão. As medidas para atualizar os detalhes do envio mais tarde são fornecidas neste artigo.

1. Envie os discos para o endereço que a ferramenta forneceu. Mantenha o número de rastreio para referência futura.

   > [!IMPORTANT]
   > Nenhum dos dois postos de trabalho de importação da Azure pode ter o mesmo número de rastreio. Certifique-se de que os impulsos preparados pela utilidade sob uma única tarefa de importação Azure são enviados juntos num único pacote e que há um único número de rastreamento único para o pacote. Não combine impulsos preparados como parte de diferentes trabalhos de importação da Azure num único pacote.

1. Quando tiver a informação do número de rastreio, vá ao computador de origem, que aguarda a conclusão do trabalho de importação. Executar o seguinte comando num pedido de comando elevado com o diretório de utilidade *azureOfflineBackdiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:`

   Pode executar opcionalmente o seguinte comando a partir de um computador diferente, como o computador de cópia, com o diretório de utilidade *AzureOfflineBackdiskPrep* como o diretório atual.

   `*.\AzureOfflineBackupDiskPrep.exe*  u:  s:<*Staging Location Path*>   p:<*Path to AzurePublishSettingsFile*>`

    | Parâmetro | Descrição |
    | --- | --- |
    | u: | Esta entrada obrigatória é usada para atualizar detalhes de envio para um trabalho de importação Azure. |
    | s: &lt; *Caminho de localização de encenação*&gt; | Esta entrada obrigatória é utilizada quando o comando não é executado no computador de origem. É usado para fornecer o caminho para o local de preparação que inseriu no fluxo de trabalho na secção "Iniciar backup offline". |
    | p: &lt; *Caminho para publicarSettingsFile*&gt; | Esta entrada obrigatória é utilizada quando o comando não é executado no computador de origem. É utilizado para fornecer o caminho para o ficheiro de definições de publicação Azure que inseriu no fluxo de trabalho na secção "Iniciar backup offline". |

    O utilitário deteta automaticamente o trabalho de importação que o computador de origem está à espera ou os postos de trabalho associados ao local de paragem quando o comando é executado num computador diferente. Em seguida, fornece a opção de atualizar as informações de envio através de uma série de entradas.

    ![Insira informações de envio](./media/offline-backup-dpm-mabs-previous-versions/shipping-inputs.png)<br/>

1. Depois de todas as entradas fornecidas, reveja os detalhes cuidadosamente e comprometa as informações de envio fornecidas inserindo **sim**.

    ![Rever informações de envio](./media/offline-backup-dpm-mabs-previous-versions/review-shipping-information.png)<br/>

1. Após a atualização da informação de envio com sucesso, o utilitário fornece uma localização local onde os detalhes de envio introduzidos são armazenados.

    ![Informações de envio de lojas](./media/offline-backup-dpm-mabs-previous-versions/storing-shipping-information.png)<br/>

   > [!IMPORTANT]
   > Certifique-se de que as unidades chegam ao centro de dados Azure no prazo de duas semanas após fornecer as informações de envio utilizando o utilitário *AzureOfflineBackupDiskPrep.* Se não o fizer, as unidades não são processadas.

Depois de terminar os passos anteriores, o datacenter Azure está pronto para receber as unidades e processá-los para transferir os dados de backup das unidades para a conta de armazenamento Azure de tipo clássico que criou.

### <a name="time-to-process-the-drives"></a>Hora de processar as unidades

O tempo que demora a processar um trabalho de importação da Azure varia. O tempo de processo depende de fatores como o tempo de envio, o tipo de trabalho, o tipo e o tamanho dos dados que estão a ser copiados e o tamanho dos discos fornecidos. O serviço Azure Import/Export não tem SLA. Após a recebidação dos discos, o serviço esforça-se por terminar a cópia de dados de backup na sua conta de armazenamento Azure em 7 a 10 dias. A secção seguinte descreve como pode monitorizar o estado do trabalho de importação do Azure.

### <a name="monitor-azure-import-job-status"></a>Monitor Azure importe estatuto de trabalho

Enquanto as suas unidades estão em trânsito ou no datacenter Azure a ser copiado para a conta de armazenamento, o Agente de Backup Azure ou dPM ou a consola MABS no computador de origem mostra o seguinte estado de trabalho para as suas cópias de segurança programadas:

  `Waiting for Azure Import Job to complete. Please check on Azure Management portal for more information on job status`

Para verificar o estado do trabalho de importação:

1. Abra uma solicitação de comando elevada no computador de origem e execute o seguinte comando:

     `AzureOfflineBackupDiskPrep.exe u:`

1. A produção mostra o estado atual do trabalho de importação.

    ![Verificar o estado do trabalho das importações](./media/offline-backup-dpm-mabs-previous-versions/import-job-status-reporting.png)<br/>

Para obter mais informações sobre os vários Estados do trabalho de importação de Azure, consulte [o estatuto dos postos de trabalho de importação/exportação de Azure.](../import-export/storage-import-export-view-drive-status.md)

### <a name="finish-the-workflow"></a>Termine o fluxo de trabalho

Após o fim do trabalho de importação, os dados de backup iniciais estão disponíveis na sua conta de armazenamento. No momento da próxima cópia agendada, a Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação.

   ![Copiar dados para o cofre dos Serviços de Recuperação](./media/offline-backup-dpm-mabs-previous-versions/copying-from-storage-account-to-azure-backup.png)<br/>

No momento da próxima cópia de segurança programada, o Azure Backup executa cópia de backup incremental sobre a cópia inicial de backup.

## <a name="next-steps"></a>Passos seguintes

* Para qualquer dúvida sobre o fluxo de trabalho do serviço Azure Import/Export, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento blob](../import-export/storage-import-export-service.md).
