---
title: Semente offline backup com o serviço Azure Import/Export
description: Saiba como pode utilizar o Azure Backup para enviar dados da rede utilizando o serviço de importação/exportação Azure. Este artigo explica a sementeação offline dos dados iniciais de backup utilizando o serviço de importação/exportação Azure.
ms.reviewer: saurse
ms.topic: conceptual
ms.date: 05/17/2018
ms.openlocfilehash: 1359616e074f36a1324a418d5b2c889076ced52d
ms.sourcegitcommit: 1fa2bf6d3d91d9eaff4d083015e2175984c686da
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/01/2020
ms.locfileid: "78206763"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de backup offline em Backup Azure

A Azure Backup tem várias eficiências incorporadas que poupam custos de rede e armazenamento durante as cópias de segurança iniciais completas de dados para o Azure. As cópias de segurança completas iniciais normalmente transferem grandes quantidades de dados e requerem mais largura de banda da rede quando comparadas com as cópias de segurança subsequentes que transferem apenas os deltas/incrementais. Através do processo de sementeira offline, o Azure Backup pode usar discos para enviar os dados de backup offline para o Azure.

O processo de sementeing offline Azure Backup está fortemente integrado com o [serviço de importação/exportação Azure](../storage/common/storage-import-export-service.md). Pode utilizar este serviço para transferir dados iniciais de backup para o Azure utilizando discos. Se tiver terabytes (TBs) de dados de backup iniciais que precisam de ser transferidos através de uma rede de alta latência e baixa largura de banda, pode utilizar o fluxo de trabalho offline para enviar a cópia inicial de backup, em um ou mais discos rígidos para um centro de dados Azure. A imagem seguinte fornece uma visão geral dos passos no fluxo de trabalho.

  ![visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de backup offline envolve estes passos:

1. Em vez de enviar os dados de backup para a rede, escreva os dados de cópia de segurança para um local de paragem.
1. Utilize o utilitário *AzureOfflineBackupDiskPrep* para escrever os dados no local de paragem para um ou mais discos SATA.
1. Como parte do trabalho preparatório, o utilitário *AzureOfflineBackupDiskPrep* cria um trabalho de importação Azure. Envie as unidades SATA para o centro de dados Azure mais próximo e refira o trabalho de importação para ligar as atividades.
1. No centro de dados Azure, os dados dos discos são copiados para uma conta de armazenamento Azure.
1. O Azure Backup copia os dados de backup da conta de armazenamento para o cofre dos Serviços de Recuperação, e estão agendados backups incrementais.

## <a name="supported-configurations"></a>Configurações suportadas

As seguintes funcionalidades de backup do Azure ou cargas de trabalho suportam a utilização de cópias de segurança offline para:

> [!div class="checklist"]
>
> * Cópia de segurança de ficheiros e pastas com o Agente de Serviços de Recuperação do Microsoft Azure (MARS), também referido como o Agente de Backup Azure.
> * Backup de todas as cargas de trabalho e ficheiros com System Center Data Protection Manager (DPM).
> * Backup de todas as cargas de trabalho e ficheiros com o Microsoft Azure Backup Server.
 
   > [!NOTE]
   > A cópia de segurança offline não é suportada para cópias de segurança do estado do sistema feitas usando o Agente de Backup Azure.

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os seguintes pré-requisitos e fluxo de trabalho aplicam-se apenas à cópia de segurança offline de ficheiros e pastas utilizando o mais recente Agente de Serviços de [Recuperação do Azure](https://aka.ms/azurebackup_agent). Para efetuar cópias de segurança offline para cargas de trabalho utilizando o System Center DPM ou o Azure Backup Server, consulte o fluxo de [trabalho de backup offline para dPM e servidor](backup-azure-backup-server-import-export-.md)de backup Azure .

Antes de iniciar o fluxo de trabalho de backup offline, preencha os seguintes pré-requisitos:

* Crie um cofre de Serviços de [Recuperação.](backup-azure-recovery-services-vault-overview.md) Para criar um cofre, siga os passos em Criar um cofre de Serviços de [Recuperação.](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Certifique-se de que apenas a [versão mais recente do Agente de Backup Azure](https://aka.ms/azurebackup_agent) está instalada no Windows Server ou no cliente Windows, conforme aplicável, e o computador está registado no cofre dos Serviços de Recuperação.
* O Azure PowerShell 3.7.0 é necessário no computador que executa o Agente de Backup Azure. Descarregue e [instale a versão 3.7.0 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o Agente de Backup Azure, certifique-se de que o Microsoft Edge ou o Internet Explorer 11 estão instalados e o JavaScript está ativado.
* Crie uma conta de armazenamento Azure na mesma subscrição que o cofre dos Serviços de Recuperação.
* Certifique-se de que tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar a aplicação Azure Ative Directory. O fluxo de trabalho de backup offline cria uma aplicação Azure Ative Directory na subscrição associada à conta de armazenamento Azure. O objetivo da aplicação é fornecer ao Azure Backup um acesso seguro e acessível ao serviço de importação/exportação Azure, que é necessário para o fluxo de trabalho de backup offline.
* Registe o fornecedor de recursos *Microsoft.ImportExport* com a subscrição que contém a conta de armazenamento Azure. Para registar o fornecedor de recursos:
    1. No menu principal, selecione **Subscrições**.
    1. Se estiver subscrito a várias subscrições, selecione a subscrição que planeia utilizar para a cópia de segurança offline. Se utilizar apenas uma subscrição, a sua subscrição aparece.
    1. No menu de subscrição, selecione **fornecedores** de Recursos para ver a lista de fornecedores.
    1. Na lista de fornecedores, desça até *microsoft.ImportExport*. Se o **Estado** não estiver **Registado,** selecione **Registar**.

        ![Registar o fornecedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)

* É criado um local de encenação, que pode ser uma partilha de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço suficiente para guardar a sua cópia inicial. Por exemplo, se quiser fazer o reforço de um servidor de ficheiros de 500 GB, certifique-se de que a área de paragem é de pelo menos 500 GB. (Uma quantidade menor é utilizada devido à compressão.)
* Quando enviar discos para o Azure, utilize apenas 2,5 polegadas SSD ou 2,5 polegadas ou 3,5 polegadas sATA II/III discos rígidos internos. Pode utilizar discos rígidos até 10 TB. Consulte a documentação do [serviço Azure Import/Export](../storage/common/storage-import-export-requirements.md#supported-hardware) para obter o mais recente conjunto de unidades que o serviço suporta.
* As unidades SATA devem ser ligadas a um computador (referido como *um computador de cópia)* de onde a cópia dos dados de cópia da localização de paragem para as unidades SATA é feita. Certifique-se de que o BitLocker está ativado no computador de cópia.

## <a name="workflow"></a>Fluxo de trabalho

Esta secção descreve o fluxo de trabalho de backup offline para que os seus dados possam ser entregues num centro de dados Azure e enviados para o Armazenamento Azure. Se tiver dúvidas sobre o serviço de importação ou sobre qualquer aspeto do processo, consulte a documentação geral do serviço de [importação/exportação do Azure](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Iniciar cópia seleção offline

1. Quando marca um backup no Agente de Serviços de Recuperação, vê esta página.

    ![Página de importação](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

1. Selecione a opção **Transferir utilizando os meus próprios discos**.

    > [!NOTE]
    > Utilize a opção Caixa de Dados Azure para transferir dados iniciais de backup offline. Esta opção poupa o esforço necessário para adquirir os seus próprios discos compatíveis com o Azure. Fornece dispositivos Microsoft-proprietárias, seguras e invioláveis da Caixa de Dados Azure para os quais os dados de backup podem ser diretamente escritos pelo Agente de Serviços de Recuperação.

1. Selecione **Seguinte**e preencha cuidadosamente as caixas.

    ![Introduza os detalhes do disco](./media/backup-azure-backup-import-export/your-disk-details.png)

   As caixas que preenche são:

    * **Local de preparação**: O local de armazenamento temporário para o qual a cópia inicial de cópia de cópia de reserva está escrita. O local de preparação pode estar numa partilha de rede ou num computador local. Se o computador de cópia e o computador de origem forem diferentes, especifique o percurso de rede completo do local de paragem.
    * Conta de Armazenamento do **Gestor de Recursos Azure**: O nome da conta de armazenamento do tipo Gestor de Recursos (finalidade geral v1 ou finalidade geral v2) em qualquer subscrição do Azure.
    * **Recipiente de armazenamento Azure**: O nome da bolha de armazenamento de destino na conta de armazenamento Azure onde os dados de backup são importados antes de serem copiados para o cofre dos Serviços de Recuperação.
    * **ID de subscrição Azure**: O ID para a subscrição Azure onde a conta de armazenamento Azure é criada.
    * Nome do trabalho de **importação azure**: O nome único pelo qual o serviço azure import/exportação e a cópia de segurança Azure acompanham a transferência de dados enviados em discos para o Azure.
  
   Depois de preencher as caixas, selecione **Next**. Guarde a localização de **preparação** e as informações de nome de **trabalho de importação azure.** É necessário preparar os discos.

1. Quando solicitado, inscreva-se na sua assinatura Azure. Deve iniciar sessão para que o Azure Backup possa criar a aplicação Azure Ative Directory. Introduza as permissões necessárias para aceder ao serviço de importação/exportação Azure.

    ![Página de inscrição por subscrição Azure](./media/backup-azure-backup-import-export/azure-login.png)

1. Termine o fluxo de trabalho. Na consola do Agente de Backup Azure, selecione **Back Up Now**.

    ![Back Up Now](./media/backup-azure-backup-import-export/backupnow.png)

1. Na página de **confirmação** do assistente, selecione **Back Up**. O backup inicial está escrito na área de preparação como parte da configuração.

   ![Confirme que está pronto para recuar agora.](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Após o fim da operação, o local de preparação está pronto para ser utilizado para a preparação do disco.

   ![Página de back up now wizard](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Prepare unidades da SATA e envie para Azure

O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas para o centro de dados Azure mais próximo. Este utilitário está disponível no diretório de instalação do Agente de Backup Azure no seguinte caminho:

```*\Microsoft Azure Recovery Services Agent\Utils\\*```

1. Vá ao diretório e copie o diretório *AzureOfflineDiskPrep* para outro computador onde as unidades SATA estão ligadas. No computador com as unidades SATA ligadas, certifique-se de que:

   * O computador de cópia pode aceder ao local de paragem do fluxo de trabalho offline de sementeing utilizando o mesmo caminho de rede que foi fornecido no fluxo de trabalho na secção "Iniciar cópias de segurança offline".
   * O BitLocker está ativado no computador de cópia.
   * Está instalado o Azure PowerShell 3.7.0.
   * Os navegadores compatíveis mais recentes (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está ativado.
   * O computador de cópia pode aceder ao portal Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador-fonte.

     > [!IMPORTANT]
     > Se o computador de origem for uma máquina virtual, então o computador copiador deve ser um servidor físico diferente ou máquina cliente a partir do computador de origem.

1. Abra um pedido de comando elevado no computador de cópia com o diretório de utilidade seleção *AzureOfflineBackupPrep* como o diretório atual. Execute o seguinte comando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;percurso de *localização de encenação*&gt; |Esta entrada obrigatória é utilizada para fornecer o caminho para o local de paragem que inseriu no fluxo de trabalho na secção "Iniciar backup offline". |
    | p: *&lt;Caminho para publicarDefiniçõesFile*&gt; |Esta entrada opcional é utilizada para fornecer o caminho para o ficheiro de definições de publicação do Azure que introduziu no fluxo de trabalho na secção "Iniciar backup offline". |

    Quando dirige o comando, o utilitário solicita a seleção do trabalho de importação de Azure que corresponde às unidades que precisam de ser preparadas. Se apenas um único trabalho de importação estiver associado à localização de preparação fornecida, você vê uma página como esta.

    ![Entrada da ferramenta de preparação do disco azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

1. Introduza a letra de unidade sem o cólon de rasto para o disco montado que pretende preparar para a transferência para O Azure.
1. Forneça confirmação para a formatação da unidade quando solicitado.
1. Foi-lhe pedido que se inscreva na sua assinatura Azure. Introduza as suas credenciais.

    ![Inscrição de subscrição azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    A ferramenta começa então a preparar o disco e a copiar os dados de cópia. Pode ser necessário anexar discos adicionais quando solicitado pela ferramenta no caso de o disco fornecido não dispor de espaço suficiente para os dados de backup. <br/>

    No final da execução bem sucedida da ferramenta, o pedido de comando fornece três peças de informação:

   1. Um ou mais discos fornecidos estão preparados para o envio para O Azure.
   1. Recebe a confirmação de que o seu trabalho de importação foi criado. O trabalho de importação usa o nome que forneceu.
   1. A ferramenta exibe o endereço de envio para o datacenter Azure.

      ![Preparação do disco azure terminada](./media/backup-azure-backup-import-export/console2.png)<br/>

1. No final da execução do comando, pode atualizar as informações de envio.

1. Envie os discos para o endereço que a ferramenta forneceu. Mantenha o número de rastreio para referência futura.

   > [!IMPORTANT]
   > Nenhum emprego de importação azure pode ter o mesmo número de rastreio. Certifique-se de que as unidades preparadas pela utilidade sob um único trabalho de importação azure são enviadas juntas num único pacote e que há um único número de rastreamento único para o pacote. Não combine unidades preparadas como parte de trabalhos de importação de Azure separados num único pacote.

## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes do envio sobre o trabalho de importação de Azure

O procedimento seguinte atualiza os detalhes do transporte de emprego de importação do Azure. Estas informações incluem detalhes sobre:

* O nome do porta-aviões que entrega os discos ao Azure.
* Devolva os detalhes do envio para os seus discos.

1. Inscreva-se na sua assinatura Azure.
1. No menu principal, selecione **Todos os serviços.** Na caixa de diálogo **de todos os serviços,** introduza **Import**. Quando vir **empregos de importação/exportação,** selecione-o.

    ![Insira informações de envio](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    Abre o menu de **empregos de importação/exportação** e aparece a lista de todos os postos de trabalho de importação/exportação na subscrição selecionada.

1. Se tiver várias subscrições, selecione a subscrição utilizada para importar os dados de backup. Em seguida, selecione o recém-criado trabalho de importação para abrir os seus detalhes.

    ![Rever as informações de envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

1. No menu **Definições** para o trabalho de importação, selecione **Gerir informações**de envio . Insira os detalhes de envio de devolução.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

1. Quando tiver o número de rastreio da sua transportadora de transporte marítimo, selecione o banner na página de visão geral do trabalho de importação do Azure e introduza os seguintes detalhes.

   > [!IMPORTANT]
   > Confirme que as informações da transportadora e que o número de acompanhamento estão atualizados num prazo de duas semanas após a criação do trabalho de importação do Azure. A não verificação desta informação no prazo de duas semanas pode resultar na eliminação do trabalho e no facto de as unidades não serem processadas.

   ![Alerta de atualização de informação de rastreio](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Informações e número de rastreio da transportadora](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Hora de processar as unidades

O tempo que demora a processar um trabalho de importação de Azure varia. O tempo de processamento baseia-se em fatores como o tempo de envio, o tipo de trabalho, o tipo e o tamanho dos dados que estão a ser copiados, e o tamanho dos discos fornecidos. O serviço de importação/exportação Azure não tem um SLA. Após a sua entrada em disco, o serviço esforça-se por completar a cópia de dados de cópia de cópia de cópia de cópia de segurança na sua conta de armazenamento Azure em 7 a 10 dias.

### <a name="monitor-azure-import-job-status"></a>Monitor Azure importe estatuto de emprego

Pode monitorizar o estado do seu trabalho de importação a partir do portal Azure. Vá à página de **empregos de Importação/Exportação** e selecione o seu trabalho. Para obter mais informações sobre o estado dos postos de trabalho de importação, consulte [o serviço de importação/exportação de Azure?](../storage/common/storage-import-export-service.md)

### <a name="finish-the-workflow"></a>Termine o fluxo de trabalho

Após o trabalho de importação concluído com sucesso, os dados iniciais de backup estão disponíveis na sua conta de armazenamento. No momento da próxima cópia de segurança agendada, o Azure Backup copia o conteúdo dos dados da conta de armazenamento para o cofre dos Serviços de Recuperação.

   ![Copiar dados para cofre de Serviços de Recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento da próxima cópia de segurança agendada, o Azure Backup realiza uma cópia de segurança incremental.

### <a name="clean-up-resources"></a>Limpar recursos

Após a cópia de segurança inicial estar concluída, pode eliminar com segurança os dados importados para o contentor de armazenamento Azure e os dados de backup no local de paragem.

## <a name="next-steps"></a>Passos seguintes

* Para quaisquer questões sobre o fluxo de trabalho do serviço de importação/exportação Azure, consulte [Utilize o serviço de importação/exportação da Microsoft Azure para transferir dados para](../storage/common/storage-import-export-service.md)o armazenamento blob .
