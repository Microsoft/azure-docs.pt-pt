---
title: O Azure Backup - cópia de segurança Offline ou a propagação inicial com o serviço importar/exportar do Azure
description: Saiba como a cópia de segurança do Azure permite-lhe enviar dados fora da rede com o serviço importar/exportar do Azure. Este artigo explica a propagação offline dos dados de cópia de segurança iniciais com o serviço de exportação de importação do Azure.
services: backup
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: b6f0ce1939b2a78ca191d2feb0140506d130b9b0
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58107462"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de cópias de segurança offline no Azure Backup
O Azure Backup tem vários eficiências incorporadas que salvar os custos de armazenamento e de rede durante as cópias de segurança completas iniciais de dados para o Azure. Normalmente, as cópias de segurança completas iniciais transferem grandes quantidades de dados e exigem mais largura de banda de rede quando comparado com as cópias de segurança subsequentes que transferem apenas as deltas/incrementais. O processo de propagação offline, cópia de segurança do Azure pode utilizar discos para carregar os dados de cópia de segurança offline para o Azure.

O Azure Backup processo propagação offline está estreitamente integrado com o [serviço importar/exportar do Azure](../storage/common/storage-import-export-service.md) que permite-lhe transferir dados de cópia de segurança inicial para o Azure ao utilizar discos. Se tiver terabytes (TB) de dados de cópia de segurança iniciais que têm de ser transferidos através de uma rede de alta latência e baixa largura de banda, pode utilizar o fluxo de trabalho de propagação offline para enviar a cópia de segurança inicial, num ou mais discos rígidos, num Datacenter do Azure. A imagem seguinte fornece uma descrição geral dos passos no fluxo de trabalho.

  ![visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de cópia de segurança offline envolve os seguintes passos:

1. Em vez de enviar os dados de cópia de segurança através da rede, escreva os dados de cópia de segurança para uma localização de transição.
2. Utilize o utilitário de AzureOfflineBackupDiskPrep para escrever os dados em que a localização de transição para um ou mais discos SATA.
3. Como parte do trabalho preparatório, o utilitário de AzureOfflineBackupDiskPrep cria uma tarefa de importação do Azure. Enviar as unidades SATA para o datacenter mais próximo do Azure e a tarefa de importação para ligar as atividades de referência.
4. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
5. Cópia de segurança do Azure copia os dados de cópia de segurança da conta de armazenamento para o Cofre dos serviços de recuperação e cópias de segurança incrementais.

## <a name="supported-configurations"></a>Configurações suportadas 
As seguintes funcionalidades de cópia de segurança do Azure ou cargas de trabalho suportam a utilização de cópia de segurança Offline.

> [!div class="checklist"]
> * Cópia de segurança de ficheiros e pastas com o agente dos serviços de recuperação do Azure (MARS) da Microsoft, também conhecido como o agente de cópia de segurança do Azure. 
> * Cópia de segurança de todos os ficheiros com o System Center Data Protection Manager (SC DPM) e cargas de trabalho 
> * Cópia de segurança de todos os ficheiros e cargas de trabalho com o Microsoft Azure Backup Server <br/>

   > [!NOTE]
   > Cópia de segurança offline não é suportada para cópias de segurança do Estado do sistema efetuadas com o agente de cópia de segurança do Azure. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os pré-requisitos e fluxo de trabalho que se seguem aplicam-se apenas a cópia de segurança Offline de ficheiros e pastas com o [mais recente agente de MARS](https://aka.ms/azurebackup_agent). Para efetuar cópias de segurança Offline para cargas de trabalho com o System Center DPM ou servidor de cópia de segurança do Azure, consulte [este artigo](backup-azure-backup-server-import-export-.md). 

Antes de iniciar o fluxo de trabalho de cópia de segurança Offline, conclua os seguintes pré-requisitos: 
* Criar uma [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md). Para criar um cofre, consulte os passos no [neste artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Certifique-se de que apenas os [mais recente versão do agente do Azure Backup](https://aka.ms/azurebackup_agent) foi instalado no cliente Windows Server/Windows, conforme aplicável e o computador está registado com o Cofre dos serviços de recuperação.
* O Azure PowerShell 3.7.0 é necessária no computador com o agente de cópia de segurança do Azure. Recomenda-se que transfira e [instalar o 3.7.0 versão do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o agente de cópia de segurança do Azure, certificar-se de que é instalado o Microsoft Edge ou o Internet Explorer 11 e o JavaScript está ativado. 
* Crie uma conta de armazenamento do Azure na mesma subscrição que o Cofre de serviços de recuperação. 
* Certifique-se de que tem o [as permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar aplicação do Azure Active Directory. O fluxo de trabalho de cópia de segurança Offline cria uma aplicação do Azure Active Directory na subscrição associada à conta de armazenamento do Azure. O objetivo do aplicativo é fornecer cópia de segurança do Azure com o acesso seguro e âmbito para o serviço de importação do Azure, necessária para o fluxo de trabalho de cópia de segurança Offline. 
* Registe o fornecedor de recursos de Microsoft.ImportExport com a subscrição que contém a conta de armazenamento do Azure. Para registar o fornecedor de recursos:
    1. No menu principal, clique em **subscrições**.
    2. Se está a subscrever várias subscrições, selecione a subscrição que está a utilizar para a cópia de segurança offline. Se utilizar apenas uma subscrição, é apresentada a sua subscrição.
    3. No menu da subscrição, clique em **fornecedores de recursos** para ver a lista de fornecedores.
    4. Na lista de fornecedores de rolagem para baixo para Microsoft.ImportExport. Se o estado é NotRegistered, clique em **registar**.
    ![registar o fornecedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)
* Uma localização de transição, o que poderá ser um compartilhamento de rede ou de qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter sua cópia inicial, é criada. Por exemplo, se estiver a tentar criar cópias de segurança um servidor de ficheiros de 500 GB, certifique-se de que a área de transição é, pelo menos, 500 GB. (Uma quantidade menor é utilizada devido à compactação.)
* Ao enviar os discos para o Azure, utilize apenas 2,5 polegadas SSD ou 2.5 polegadas ou 3.5 polegadas SATA II/III discos rígidos internos. Pode utilizar unidades de disco rígido até 10 TB. Verifique os [documentação do serviço importar/exportar do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para o conjunto mais recente de unidades de que o serviço suporta.
* As unidades SATA tem de estar ligadas a um computador (conhecido como um *computador de cópia*) de onde a cópia de dados de cópia de segurança da *localização de transição* para o SATA unidades é feito. Certifique-se de que o BitLocker está ativado no *computador cópia*.

## <a name="workflow"></a>Fluxo de trabalho
Esta secção descreve o fluxo de trabalho de cópia de segurança offline para que os dados podem ser entregues num Datacenter do Azure e carregados para o armazenamento do Azure. Se tiver dúvidas sobre o serviço de importação ou a todos os aspectos do processo, consulte a [importar documentação de visão geral do serviço](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Iniciar a cópia de segurança offline
1. Quando agendar uma cópia de segurança do agente MARS, verá o ecrã seguinte.

    ![Ecrã de importação](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   A descrição das entradas é o seguinte:

    * **Localização de transição**: A localização de armazenamento temporário para o qual a cópia de segurança inicial é escrita. Localização de transição poderá ser num computador local ou uma partilha de rede. Se o computador de cópia e o computador de origem forem diferentes, recomendamos que especifica o caminho de rede completa da localização de transição.
    * **Conta de armazenamento do Azure Resource Manager**: O nome da conta de armazenamento de tipo de Gestor de recursos de qualquer subscrição do Azure.
    * **Contentor de armazenamento do Azure**: O nome do blob de armazenamento de destino na conta de armazenamento do Azure em que os dados de cópia de segurança são importados antes de ser copiado para o Cofre dos serviços de recuperação.
    * **ID de subscrição do Azure**: O ID de subscrição do Azure onde é criada a conta de armazenamento do Azure.
    * **Nome da tarefa de importação do Azure**: O nome exclusivo ao qual importação de Azure service e do Azure Backup controlam a transferência de dados enviados em discos para o Azure. 
  
   Fornecer as entradas no ecrã e clique em **seguinte**. Guardar fornecido *localização de transição* e o *nome da tarefa de importação do Azure*, como estas informações são necessárias para preparar os discos.

2. Quando lhe for pedido, inicie sessão na sua subscrição do Azure. Tem de iniciar sessão para que o Azure Backup pode criar a aplicação do Azure Active Directory e fornecer as permissões necessárias para aceder ao serviço de importação do Azure.

    ![Cópia de segurança agora](./media/backup-azure-backup-import-export/azurelogin.png)

3. Concluir o fluxo de trabalho e, na consola do agente de cópia de segurança do Azure, clique em **cópia de segurança agora**.

    ![Cópia de segurança agora](./media/backup-azure-backup-import-export/backupnow.png)

4. Na página de confirmação do assistente, clique em **cópia de segurança**. A cópia de segurança inicial é escrita para a área de teste como parte da configuração.

   ![Confirme se está pronto para cópia de segurança agora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Depois de concluída a operação, a localização de transição está pronta para ser utilizada para preparação de disco.

   ![Cópia de segurança agora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure
O *AzureOfflineBackupDiskPrep* utilitário prepara as unidades SATA, que são enviadas para o datacenter mais próximo do Azure. Esse utilitário está disponível no diretório de instalação do agente Azure Backup (no seguinte caminho):

   *\Microsoft azure Recovery Services Agent\Utils\\*

1. Vá para o diretório e copiar o **AzureOfflineBackupDiskPrep** diretório para outro computador em que as unidades SATA estão ligadas. No computador com as unidades SATA ligados, certifique-se:

   * O computador de cópia pode aceder a localização de transição para o fluxo de trabalho de propagação offline com o mesmo caminho de rede que foi fornecido no **iniciar a cópia de segurança offline** fluxo de trabalho.
   * BitLocker está ativado no computador de cópia.
   * O Azure PowerShell 3.7.0 está instalado.
   * Os navegadores compatíveis mais recente (Microsoft Edge ou o Internet Explorer 11) são instalados e o JavaScript está ativado. 
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.
    
     > [!IMPORTANT] 
     > Se o computador de origem for uma máquina virtual, em seguida, o computador de cópia tem de ser um servidor físico diferente ou uma máquina de cliente do computador de origem.

2. Abra uma linha de comandos elevada no computador de cópia com o *AzureOfflineBackupDiskPrep* diretório de utilitário, como o diretório atual e execute o seguinte comando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*caminho de localização de transição*&gt; |A entrada obrigatória usada para fornecer o caminho para a localização de transição que introduziu no **iniciar a cópia de segurança offline** fluxo de trabalho. |
    | p:&lt;*caminho para PublishSettingsFile*&gt; |Entrada opcional que é utilizada para fornecer o caminho para o **definições de publicação do Azure** ficheiro que introduziu no **iniciar a cópia de segurança offline** fluxo de trabalho. |

    Ao executar o comando, o utilitário solicita a seleção da tarefa de importação do Azure que corresponde às unidades que precisam estar preparado. Se apenas uma tarefa de importação único está associada com a localização de transição fornecida, verá um ecrã semelhante ao que se segue.

    ![Entrada de ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Introduza a letra de unidade sem os dois pontos à direita para o disco montado que deseja se preparar para a transferência para o Azure. 
4. Forneça a confirmação para a formatação da unidade quando lhe for pedido.
5. Lhe for pedido para iniciar sessão na sua subscrição do Azure. Forneça as credenciais.

    ![Entrada de ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Em seguida, começa a ferramenta preparar o disco e copiar os dados de cópia de segurança. Terá de anexar discos adicionais quando lhe for pedido pela ferramenta caso o disco fornecido não tem espaço suficiente para os dados de cópia de segurança. <br/>

    No final da execução bem-sucedida da ferramenta, o prompt de comando fornece três partes de informações:
   1. Um ou mais discos que forneceu estão preparados para o envio para o Azure. 
   2. Receberá uma confirmação de que a tarefa de importação foi criada. A tarefa de importação utiliza o nome que indicou.
   3. A ferramenta exibe o endereço de envio para o Centro de dados do Azure.

      ![Preparação de disco do Azure completa](./media/backup-azure-backup-import-export/console2.png)<br/>

6. No final da execução do comando, é possível atualizar as informações de envio.

7. Envie os discos para o endereço que a ferramenta fornecida e manter o número de controlo para referência futura.

   > [!IMPORTANT] 
   > Não existem duas tarefas de importação do Azure pode ter o mesmo número de controlo. Certifique-se de que as unidades preparadas pelo utilitário numa única tarefa de importação do Azure são fornecidas em conjunto num único pacote e que existe um número exclusivo de controlo único para o pacote. Não é combine unidades preparadas como parte das tarefas de importação do Azure separadas num único pacote.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes de envio na tarefa de importação do Azure

O procedimento a seguir atualiza os detalhes de envio de tarefa de importação do Azure. Estas informações incluem detalhes sobre:
* o nome da deteção de carrier e apresenta os discos para o Azure
* devolver detalhes de envio para os discos

1. Inicie sessão na sua subscrição do Azure.
2. No menu principal, clique em **todos os serviços** e, na caixa de diálogo todos os serviços, escreva a importação. Quando vir **tarefas de importação/exportação**, clique no mesmo.
    ![Introduzir informações de envio](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    A lista de **tarefas de importação/exportação** é aberto o menu e é apresentada a lista de todas as tarefas de importação/exportação na subscrição selecionada. 

3. Se tiver várias subscrições, certifique-se de que selecione a subscrição utilizada para importar os dados de cópia de segurança. Em seguida, selecione a tarefa de importação recentemente criada para abrir os respetivos detalhes.

    ![Reveja as informações de envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. No menu de definições para a tarefa de importação, clique em **gerir informações de devolução** e introduza os detalhes de envio de devolução.

    ![Armazenar informações de envio](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Quando tiver o número de controlo da sua operadora de envio, clique na faixa na página de descrição geral da tarefa de importação do Azure e introduza os seguintes detalhes:

   > [!IMPORTANT] 
   > Certifique-se de que as informações de deteção de carrier e o número de controle são atualizados em duas semanas após a criação da tarefa de importação do Azure. Falha ao verificar essas informações em duas semanas pode resultar na tarefa a ser eliminada e as unidades não são processadas.

   ![Armazenar informações de envio](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Armazenar informações de envio](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo a processar as unidades 
A quantidade de tempo que demora a processar uma tarefa de importação do Azure varia de acordo com diferentes fatores como o tempo de envio, tipo, tipo e tamanho dos dados a ser copiados e o tamanho dos discos fornecidos da tarefa. O serviço importar/exportar do Azure não tem um SLA, mas depois dos discos são recebidos o serviço se esforça para concluir a cópia de dados de cópia de segurança à sua conta de armazenamento do Azure dentro de 7 a 10 dias. 

### <a name="monitoring-azure-import-job-status"></a>Monitorização do Estado de tarefa de importação do Azure
Pode monitorizar o estado da sua tarefa de importação do portal do Azure ao navegar para o **tarefas de importação/exportação** página e selecionar o seu trabalho. Para obter mais informações sobre o estado das tarefas de importação, consulte a [serviço de armazenamento importar exportar](../storage/common/storage-import-export-service.md) artigo.

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Depois da tarefa de importação foi concluída com êxito, os dados de cópia de segurança inicial estão disponíveis na sua conta de armazenamento. No momento da cópia de segurança agendada seguinte, cópia de segurança do Azure copia o conteúdo dos dados da conta de armazenamento para o Cofre dos serviços de recuperação, conforme mostrado abaixo: 

   ![Copiar dados para o Cofre dos serviços de recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento da cópia de segurança agendada seguinte, cópia de segurança do Azure efetua uma cópia de segurança incremental.

### <a name="cleaning-up-resources"></a>Limpeza de recursos
Quando a cópia de segurança inicial estiver concluída, pode eliminar em segurança os dados importados para o contentor de armazenamento do Azure e os dados de cópia de segurança na localização de transição.

## <a name="next-steps"></a>Passos Seguintes
* Para quaisquer perguntas sobre o fluxo de trabalho de importar/exportar do Azure, consulte [utilizar o serviço de importação/exportação do Microsoft Azure para transferir dados para o armazenamento de BLOBs](../storage/common/storage-import-export-service.md).
* Consulte a seção de cópia de segurança offline do Azure Backup [FAQ](backup-azure-backup-faq.md) para quaisquer perguntas sobre o fluxo de trabalho.
