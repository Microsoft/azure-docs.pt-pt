---
title: Reserva offline de sementes com o serviço de importação/exportação
description: Saiba como o Azure Backup permite enviar dados da rede utilizando o serviço De Importação/Exportação Azure. Este artigo explica a sementeação offline dos dados iniciais de backup utilizando o serviço de exportação de importações de Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 162d129eaea83ef6623daaa063e8a088c021e25d
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77022618"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de cópias de segurança offline no Azure Backup

A Azure Backup tem várias eficiências incorporadas que poupam custos de rede e armazenamento durante as cópias de segurança iniciais completas de dados para o Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e requerem mais largura de banda da rede quando comparadas com as cópias de segurança subsequentes que transferem apenas os deltas/incrementais. Através do processo de sementeira offline, o Azure Backup pode usar discos para enviar os dados de backup offline para o Azure.

O processo de sementeira offline Azure Backup está fortemente integrado com o [serviço de importação/exportação Azure](../storage/common/storage-import-export-service.md) que lhe permite transferir dados iniciais de backup para o Azure utilizando discos. Se tiver terabytes (TBs) de dados de backup iniciais que precisam de ser transferidos através de uma rede de alta latência e baixa largura de banda, pode utilizar o fluxo de trabalho offline para enviar a cópia inicial de backup, em um ou mais discos rígidos, para um centro de dados Azure. A imagem seguinte fornece uma visão geral dos passos no fluxo de trabalho.

  ![visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de backup offline envolve os seguintes passos:

1. Em vez de enviar os dados de backup para a rede, escreva os dados de cópia de segurança para um local de paragem.
2. Utilize o utilitário AzureOfflineBackupDiskPrep para escrever os dados no local de paragem para um ou mais discos SATA.
3. Como parte do trabalho preparatório, o utilitário AzureOfflineBackupDiskPrep cria um trabalho de Importação Azure. Envie as unidades SATA para o centro de dados Azure mais próximo e refira o trabalho de importação para ligar as atividades.
4. No centro de dados Azure, os dados dos discos são copiados para uma conta de armazenamento Azure.
5. O Azure Backup copia os dados de backup da conta de armazenamento para o cofre dos Serviços de Recuperação, e estão agendados backups incrementais.

## <a name="supported-configurations"></a>Configurações suportadas

As seguintes funcionalidades de Backup Azure ou cargas de trabalho suportam a utilização de Backup Offline.

> [!div class="checklist"]
>
> * Cópia de segurança de ficheiros e pastas com o agente microsoft Azure Recovery Services (MARS), também referido como o agente de backup Azure.
> * Backup de todas as cargas de trabalho e ficheiros com System Center Data Protection Manager (SC DPM)
> * Backup de todas as cargas de trabalho e ficheiros com o Microsoft Azure Backup Server
 
   > [!NOTE]
   > A cópia de segurança offline não é suportada para cópias de segurança do System State feitas utilizando o agente de backup Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os seguintes pré-requisitos e fluxo de trabalho aplicam-se apenas à cópia de segurança offline de ficheiros e pastas utilizando o [mais recente agente MARS](https://aka.ms/azurebackup_agent). Para executar cópias de segurança offline para cargas de trabalho utilizando o System Center DPM ou o Azure Backup Server, consulte [este artigo](backup-azure-backup-server-import-export-.md).

Antes de dar início ao fluxo de trabalho offline de backup, preencha os seguintes pré-requisitos:

* Crie um cofre de Serviços de [Recuperação.](backup-azure-recovery-services-vault-overview.md) Para criar um cofre, consulte os passos [deste artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Certifique-se de que apenas a [versão mais recente do agente Decópia Azure](https://aka.ms/azurebackup_agent) foi instalada no cliente Windows Server/Windows, conforme aplicável e o computador está registado no Cofre de Serviços de Recuperação.
* O Azure PowerShell 3.7.0 é necessário no computador que executa o agente de backup Azure. Recomenda-se que descarregue e [instale a versão 3.7.0 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o agente de backup Azure, certifique-se de que o Microsoft Edge ou o Internet Explorer 11 estão instalados e o JavaScript está ativado.
* Crie uma conta de Armazenamento Azure na mesma subscrição que o cofre dos Serviços de Recuperação.
* Certifique-se de que tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar a aplicação Azure Ative Directory. O fluxo de trabalho offline backup cria uma aplicação Azure Ative Directory na subscrição associada à conta de Armazenamento Azure. O objetivo da aplicação é fornecer ao Azure Backup um acesso seguro e acessível ao Serviço de Importação De Azure, necessário para o fluxo de trabalho de backup offline.
* Registe o fornecedor de recursos Microsoft.ImportExport com a subscrição que contém a conta De armazenamento Azure. Para registar o fornecedor de recursos:
    1. No menu principal, clique em **Assinaturas**.
    2. Se estiver subscrito a várias subscrições, selecione a subscrição que está a utilizar para a cópia de segurança offline. Se utilizar apenas uma subscrição, a sua subscrição aparece.
    3. No menu de subscrição, clique em **Fornecedores** de Recursos para ver a lista de fornecedores.
    4. Na lista de fornecedores deslocam-se até à Microsoft.ImportExport. Se o Estado não estiver registado, clique em **Registar**.
    ![a registar o fornecedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)
* É criado um local de encenação, que pode ser uma partilha de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço suficiente para guardar a sua cópia inicial. Por exemplo, se estiver a tentar fazer o apoio a um servidor de ficheiros de 500 GB, certifique-se de que a área de paragem é de pelo menos 500 GB. (Uma quantidade menor é utilizada devido à compressão.)
* Ao enviar discos para o Azure, utilize apenas 2,5 polegadas SSD, ou discos internos SATA II/III de 2,5 polegadas ou 3,5 polegadas. Pode utilizar discos rígidos até 10 TB. Consulte a documentação do [serviço Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) para obter o mais recente conjunto de unidades que o serviço suporta.
* As unidades SATA devem ser ligadas a um computador (referido como *um computador de cópia)* de onde a cópia dos dados de cópia da localização de *paragem* para as unidades SATA é feita. Certifique-se de que o BitLocker está ativado no *computador de cópia*.

## <a name="workflow"></a>Fluxo de Trabalho

Esta secção descreve o fluxo de trabalho offline-backup para que os seus dados possam ser entregues num centro de dados Azure e enviados para o Armazenamento Azure. Se tiver dúvidas sobre o serviço de importação ou qualquer aspeto do processo, consulte a documentação geral do [serviço de importação.](../storage/common/storage-import-export-service.md)

## <a name="initiate-offline-backup"></a>Iniciar cópia seleção offline

1. Quando marcar uma cópia de segurança no Agente MARS, consulte o seguinte ecrã.

    ![Tela de importação](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

2. Selecione a opção **Transferir utilizando os meus próprios discos**.

    >[!NOTE]
    >Recomendamos a utilização da opção Caixa de Dados Azure para transferir dados iniciais de backup offline. Esta opção poupa o esforço necessário para adquirir os seus próprios discos compatíveis com o Azure, fornecendo dispositivos de caixa Azure Data à prova de Microsoft, seguros e à prova de adulteração, aos quais os dados de backup podem ser diretamente escritos pelo Agente MARS.

3. Clique em **Seguir** e preencha cuidadosamente as inputs:

    ![Introduza os detalhes do disco](./media/backup-azure-backup-import-export/your-disk-details.png)

   A descrição das inputs é a seguinte:

    * **Local de preparação**: O local de armazenamento temporário para o qual a cópia inicial de cópia de cópia de reserva está escrita. A localização da encenação pode estar numa partilha de rede ou num computador local. Se o computador de cópia e o computador de origem forem diferentes, recomendamos que especifique o percurso completo de rede do local de paragem.
    * Conta de Armazenamento do **Gestor de Recursos Azure**: O nome da conta de armazenamento do tipo Gestor de Recursos (finalidade geral v1 ou finalidade geral v2) em qualquer subscrição do Azure.
    * **Recipiente de armazenamento Azure**: O nome da bolha de armazenamento de destino na conta de armazenamento azure onde os dados de backup são importados antes de ser copiado para o cofre dos Serviços de Recuperação.
    * **ID de subscrição Azure**: O ID para a subscrição Azure onde a conta de Armazenamento Azure é criada.
    * Nome do trabalho de **importação azure**: O nome único pelo qual o serviço de importação de Azure e a Cópia de Segurança Azure acompanham a transferência de dados enviados em discos para o Azure.
  
   Forneça as inputs no ecrã e clique em **Seguinte**. Guarde a localização de *preparação* fornecida e o Nome de Trabalho de *Importação Azure,* uma vez que esta informação é necessária para preparar os discos.

4. Quando solicitado, assine a sua assinatura Azure. Deve iniciar sessão para que o Azure Backup possa criar a aplicação Azure Ative Directory e fornecer as permissões necessárias para aceder ao Serviço de Importação Azure.

    ![Backup agora](./media/backup-azure-backup-import-export/azure-login.png)

5. Complete o fluxo de trabalho e na consola do agente de backup Azure clique em **Back Up Now**.

    ![Backup agora](./media/backup-azure-backup-import-export/backupnow.png)

6. Na página de Confirmação do assistente, clique em **Back Up**. O backup inicial está escrito na área de preparação como parte da configuração.

   ![Confirme que está pronto para recuar agora.](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Após o fim da operação, o local de preparação está pronto para ser utilizado para a preparação do disco.

   ![Backup agora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Prepare unidades da SATA e envie para Azure

O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas para o centro de dados Azure mais próximo. Este utilitário está disponível no diretório de instalação do agente de backup Azure (no seguinte caminho):

    *\Microsoft Azure Recovery Services Agent\Utils\\*

1. Vá ao diretório e copie o diretório **AzureOfflineDiskPrep** para outro computador onde as unidades SATA estão ligadas. No computador com as unidades SATA ligadas, certifique-se:

   * O computador de cópia pode aceder ao local de preparação para o fluxo de trabalho offline de sementeing utilizando o mesmo caminho de rede que foi fornecido no fluxo de trabalho de **backup offline Iniciar.**
   * O BitLocker está ativado no computador de cópia.
   * Está instalado o Azure PowerShell 3.7.0.
   * Os navegadores compatíveis mais recentes (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está ativado.
   * O computador de cópia pode aceder ao portal Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador-fonte.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, então o computador copiador deve ser um servidor físico diferente ou máquina cliente a partir do computador de origem.

2. Abra um pedido de comando elevado no computador de cópia com o diretório de utilidade seleção *AzureOfflineDiskPrep* como o diretório atual, e execute o seguinte comando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;percurso de *localização de encenação*&gt; |Entrada obrigatória utilizada para fornecer o caminho para o local de paragem que inseriu no fluxo de trabalho de **backup offline Iniciar.** |
    | p: *&lt;Caminho para publicarDefiniçõesFile*&gt; |Entrada opcional que é usada para fornecer o caminho para o ficheiro Definições de **Publicação Azure** que inseriu no fluxo de trabalho de **backup offline Iniciar.** |

    Quando dirige o comando, o utilitário solicita a seleção do trabalho de Importação Azure que corresponde às unidades que precisam de ser preparadas. Se apenas um único trabalho de importação estiver associado à localização de preparação fornecida, você vê um ecrã como o que se segue.

    ![Entrada da ferramenta de preparação do disco azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Introduza a letra de unidade sem o cólon de rasto para o disco montado que pretende preparar para a transferência para O Azure.
4. Forneça confirmação para a formatação da unidade quando solicitado.
5. É-lhe pedido que assine a sua assinatura Azure. Forneça as suas credenciais.

    ![Entrada da ferramenta de preparação do disco azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    A ferramenta começa então a preparar o disco e a copiar os dados de cópia. Pode ser necessário anexar discos adicionais quando solicitado pela ferramenta no caso de o disco fornecido não dispor de espaço suficiente para os dados de backup. <br/>

    No final da execução bem sucedida da ferramenta, o pedido de comando fornece três peças de informação:
   1. Um ou mais discos fornecidos estão preparados para o envio para O Azure.
   2. Recebe a confirmação de que o seu trabalho de importação foi criado. O trabalho de importação usa o nome que forneceu.
   3. A ferramenta exibe o endereço de envio para o datacenter Azure.

      ![Preparação do disco azure completa](./media/backup-azure-backup-import-export/console2.png)<br/>

6. No final da execução do comando, pode atualizar as informações de envio.

7. Envie os discos para o endereço que a ferramenta forneceu e guarde o número de rastreio para referência futura.

   > [!IMPORTANT]
   > Nenhum emprego de importação azure pode ter o mesmo número de rastreamento. Certifique-se de que as unidades preparadas pela utilidade sob um único trabalho de importação azure são enviadas em conjunto num único pacote e que existe um único número de rastreamento único para o pacote. Não combine unidades preparadas como parte de trabalhos separados da Importação azure num único pacote.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes do envio sobre o trabalho de Importação de Azure

O procedimento seguinte atualiza os detalhes do envio de postos de trabalho da Azure Import. Estas informações incluem detalhes sobre:

* o nome do transportador que entrega os discos a Azure
* devolver detalhes de envio para os seus discos

1. Entre em sua assinatura do Azure.
2. No menu principal, clique em **Todos os serviços** e no diálogo de todos os serviços, escreva Import. Quando vir **empregos de importação/exportação,** clique nele.
    ![a entrada em informações de envio](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Abre a lista do menu de postos de **trabalho de importação/exportação** e aparece a lista de todos os postos de trabalho de importação/exportação na subscrição selecionada.

3. Se tiver várias subscrições, certifique-se de selecionar a subscrição utilizada para importar os dados de backup. Em seguida, selecione o recém-criado trabalho de Importação para abrir os seus detalhes.

    ![Rever informações sobre envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. No menu Definições para o trabalho de Importação, clique em **Gerir informações** de envio e insira os detalhes de envio de devolução.

    ![Armazenar informações sobre o envio](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Quando tiver o número de rastreio da sua transportadora de transporte, clique no banner na página de visão geral do trabalho do Azure Import e introduza os seguintes detalhes:

   > [!IMPORTANT]
   > Confirme que as informações da transportadora e que o número de acompanhamento estão atualizados num prazo de duas semanas após a criação do trabalho de importação do Azure. A não verificação desta informação no prazo de duas semanas pode resultar na eliminação do trabalho e não ser processado.

   ![Armazenar informações sobre o envio](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Armazenar informações sobre o envio](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Hora de processar as unidades

O tempo que demora a processar um trabalho de importação do Azure varia consoante os diferentes fatores, tais como o tempo de envio, o tipo de trabalho, o tipo e a dimensão dos dados que estão a ser copiados, e o tamanho dos discos fornecidos. O serviço azure import/exportação não tem um SLA, mas após a receber os discos, o serviço esforça-se por completar a cópia de dados de backup na sua conta de armazenamento Azure em 7 a 10 dias.

### <a name="monitoring-azure-import-job-status"></a>Monitorizar o estatuto de emprego de importação de Azure

Pode monitorizar o estado do seu trabalho de Importação a partir do portal Azure navegando até à página de **empregos de Importação/Exportação** e selecionando o seu trabalho. Para obter mais informações sobre o estado dos postos de trabalho de importação, consulte o artigo do serviço de exportação de importações de [armazenamento.](../storage/common/storage-import-export-service.md)

### <a name="complete-the-workflow"></a>Complete o fluxo de trabalho

Após o trabalho de importação concluído com sucesso, os dados iniciais de backup estão disponíveis na sua conta de armazenamento. No momento da próxima cópia de segurança agendada, o Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação, conforme mostrado abaixo:

   ![Copiar dados para o Cofre de Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento da próxima cópia de segurança agendada, o Azure Backup realiza uma cópia de segurança incremental.

### <a name="cleaning-up-resources"></a>Limpeza de recursos

Uma vez concluída a cópia de segurança inicial, pode eliminar com segurança os dados importados para o Contentor de Armazenamento Azure e os dados de backup no Local de Paragem.

## <a name="next-steps"></a>Passos seguintes

* Para quaisquer questões sobre o fluxo de trabalho de importação/exportação do Azure, consulte [a Utilização do serviço de importação/exportação do Microsoft Azure para transferir dados para](../storage/common/storage-import-export-service.md)o armazenamento blob .
