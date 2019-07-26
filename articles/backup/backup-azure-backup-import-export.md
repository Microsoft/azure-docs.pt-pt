---
title: Backup do Azure-backup offline ou propagação inicial usando o serviço de importação/exportação do Azure
description: Saiba como o backup do Azure permite que você envie dados fora da rede usando o serviço de importação/exportação do Azure. Este artigo explica a propagação offline dos dados de backup iniciais usando o serviço de importação e exportação do Azure.
author: saurabhsensharma
manager: shivamg
ms.service: backup
ms.topic: conceptual
ms.date: 05/17/2018
ms.author: saurse
ms.openlocfilehash: e852e1595be5b564bd1a6326d41115496284506f
ms.sourcegitcommit: c72ddb56b5657b2adeb3c4608c3d4c56e3421f2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/24/2019
ms.locfileid: "68466779"
---
# <a name="offline-backup-workflow-in-azure-backup"></a>Fluxo de trabalho de cópias de segurança offline no Azure Backup
O backup do Azure tem várias eficiências internas que economizam os custos de rede e armazenamento durante os backups completos iniciais dos dados no Azure. Os backups completos iniciais normalmente transferem grandes quantidades de dados e exigem mais largura de banda de rede em comparação com os backups subsequentes que transferem apenas os deltas/incrementos. Por meio do processo de propagação offline, o backup do Azure pode usar discos para carregar os dados de backup offline no Azure.

O processo de propagação offline do backup do Azure está totalmente integrado ao [serviço de importação/exportação do Azure](../storage/common/storage-import-export-service.md) que permite transferir dados de backup iniciais para o Azure usando discos. Se você tiver terabytes (TBs) de dados de backup inicial que precisam ser transferidos por uma rede de alta latência e baixa largura de banda, você poderá usar o fluxo de trabalho de propagação offline para enviar a cópia de backup inicial, em um ou mais discos rígidos, para um datacenter do Azure. A imagem a seguir fornece uma visão geral das etapas no fluxo de trabalho.

  ![Visão geral do processo de fluxo de trabalho de importação offline](./media/backup-azure-backup-import-export/offlinebackupworkflowoverview.png)

O processo de backup offline envolve as seguintes etapas:

1. Em vez de enviar os dados de backup pela rede, grave os dados de backup em um local de preparo.
2. Use o utilitário AzureOfflineBackupDiskPrep para gravar os dados no local de preparo para um ou mais discos SATA.
3. Como parte do trabalho preparatória, o utilitário AzureOfflineBackupDiskPrep cria um trabalho de importação do Azure. Envie as unidades SATA para o datacenter do Azure mais próximo e referencie o trabalho de importação para conectar as atividades.
4. No datacenter do Azure, os dados nos discos são copiados para uma conta de armazenamento do Azure.
5. O backup do Azure copia os dados de backup da conta de armazenamento para o cofre dos serviços de recuperação e os backups incrementais são agendados.

## <a name="supported-configurations"></a>Configurações suportadas 
Os seguintes recursos ou cargas de trabalho de backup do Azure dão suporte ao uso de backup offline.

> [!div class="checklist"]
> * Backup de arquivos e pastas com o agente de Serviços de Recuperação do Microsoft Azure (MARS), também conhecido como o agente de backup do Azure. 
> * Backup de todas as cargas de trabalho e arquivos com o System Center Data Protection Manager (SC DPM) 
> * Backup de todas as cargas de trabalho e arquivos com o Backup do Microsoft Azure Server <br/>

   > [!NOTE]
   > O backup offline não tem suporte para backups de estado do sistema feitos usando o agente de backup do Azure. 

[!INCLUDE [backup-upgrade-mars-agent.md](../../includes/backup-upgrade-mars-agent.md)]

## <a name="prerequisites"></a>Pré-requisitos

  > [!NOTE]
  > Os seguintes pré-requisitos e fluxos de trabalho se aplicam somente ao backup offline de arquivos e pastas usando o [agente Mars mais recente](https://aka.ms/azurebackup_agent). Para executar backups offline para cargas de trabalho usando o System Center DPM ou Servidor de Backup do Azure, consulte [Este artigo](backup-azure-backup-server-import-export-.md). 

Antes de iniciar o fluxo de trabalho de backup offline, conclua os seguintes pré-requisitos: 
* Crie um [cofre dos serviços de recuperação](backup-azure-recovery-services-vault-overview.md). Para criar um cofre, consulte as etapas neste [artigo](tutorial-backup-windows-server-to-azure.md#create-a-recovery-services-vault)
* Verifique se apenas a [versão mais recente do agente de backup do Azure](https://aka.ms/azurebackup_agent) foi instalada no cliente Windows Server/Windows, conforme aplicável, e se o computador está registrado no cofre dos serviços de recuperação.
* Azure PowerShell 3.7.0 é necessário no computador que executa o agente de backup do Azure. É recomendável baixar e [instalar a versão 3.7.0 do Azure PowerShell](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).
* No computador que executa o agente de backup do Azure, verifique se o Microsoft Edge ou o Internet Explorer 11 está instalado e se o JavaScript está habilitado. 
* Crie uma conta de armazenamento do Azure na mesma assinatura que o cofre dos serviços de recuperação. 
* Verifique se você tem as [permissões necessárias](../active-directory/develop/howto-create-service-principal-portal.md) para criar o aplicativo Azure Active Directory. O fluxo de trabalho de backup offline cria um aplicativo Azure Active Directory na assinatura associada à conta de armazenamento do Azure. O objetivo do aplicativo é fornecer o backup do Azure com acesso seguro e com escopo ao serviço de importação do Azure, necessário para o fluxo de trabalho de backup offline. 
* Registre o provedor de recursos Microsoft. ImportExport com a assinatura que contém a conta de armazenamento do Azure. Para registrar o provedor de recursos:
    1. No menu principal, clique em **assinaturas**.
    2. Se você se inscreveu em várias assinaturas, selecione a assinatura que você está usando para o backup offline. Se você usar apenas uma assinatura, sua assinatura será exibida.
    3. No menu assinatura, clique em **provedores de recursos** para exibir a lista de provedores.
    4. Na lista de provedores, role para baixo até Microsoft. ImportExport. Se o status for não registrado, clique em **registrar**.
    ![Registrando o provedor de recursos](./media/backup-azure-backup-import-export/registerimportexport.png)
* Um local de preparo, que pode ser um compartilhamento de rede ou qualquer unidade adicional no computador, interno ou externo, com espaço em disco suficiente para manter a cópia inicial, é criado. Por exemplo, se você estiver tentando fazer backup de um servidor de arquivos de 500 GB, verifique se a área de preparação tem pelo menos 500 GB. (Uma quantidade menor é usada devido à compactação.)
* Ao enviar discos para o Azure, use somente unidades de disco rígido internas SATA II de 2,5 polegadas ou 2,5 polegadas ou 3,5 polegadas. Você pode usar discos rígidos de até 10 TB. Verifique a [documentação do serviço de importação/exportação do Azure](../storage/common/storage-import-export-requirements.md#supported-hardware) para obter o conjunto mais recente de unidades às quais o serviço dá suporte.
* As unidades SATA devem ser conectadas a um computador (conhecido como um *computador de cópia*) de onde a cópia dos dados de backup do *local de preparo* para as unidades SATA é feita. Verifique se o BitLocker está habilitado no *computador de cópia*.

## <a name="workflow"></a>Fluxo de trabalho
Esta seção descreve o fluxo de trabalho de backup offline para que seus dados possam ser entregues a um datacenter do Azure e carregados no armazenamento do Azure. Se você tiver dúvidas sobre o serviço de importação ou qualquer aspecto do processo, consulte a [documentação visão geral do serviço de importação](../storage/common/storage-import-export-service.md).

## <a name="initiate-offline-backup"></a>Iniciar backup offline
1. Ao agendar um backup no agente MARS, você verá a tela a seguir.

    ![Importar tela](./media/backup-azure-backup-import-export/offlinebackup_inputs.png)

   A descrição das entradas é a seguinte:

    * **Local de preparo**: O local de armazenamento temporário para o qual a cópia de backup inicial é gravada. O local de preparo pode estar em um compartilhamento de rede ou em um computador local. Se o computador de cópia e o computador de origem forem diferentes, recomendamos que você especifique o caminho de rede completo do local de preparo.
    * **Azure Resource Manager conta de armazenamento**: O nome da conta de armazenamento do tipo de Gerenciador de recursos (uso geral v1 ou uso geral v2) em qualquer assinatura do Azure.
    * **Contêiner de armazenamento do Azure**: O nome do blob de armazenamento de destino na conta de armazenamento do Azure em que os dados de backup são importados antes de serem copiados para o cofre dos serviços de recuperação.
    * **ID da assinatura do Azure**: A ID da assinatura do Azure em que a conta de armazenamento do Azure é criada.
    * **Nome do trabalho de importação do Azure**: O nome exclusivo pelo qual o serviço de importação do Azure e o backup do Azure acompanham a transferência de dados enviados em discos para o Azure. 
  
   Forneça as entradas na tela e clique em **Avançar**. Salve o *local de preparo* fornecido e o *nome do trabalho de importação do Azure*, pois essas informações são necessárias para preparar os discos.

2. Quando solicitado, entre em sua assinatura do Azure. Você deve entrar para que o backup do Azure possa criar o aplicativo Azure Active Directory e fornecer as permissões necessárias para acessar o serviço de importação do Azure.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/azurelogin.png)

3. Conclua o fluxo de trabalho e, no console do agente de backup do Azure, clique em **fazer backup agora**.

    ![Fazer backup agora](./media/backup-azure-backup-import-export/backupnow.png)

4. Na página confirmação do assistente, clique em **fazer backup**. O backup inicial é gravado na área de preparação como parte da configuração.

   ![Confirme que você está pronto para fazer o backup agora](./media/backup-azure-backup-import-export/backupnow-confirmation.png)

    Após a conclusão da operação, o local de preparo estará pronto para ser usado para a preparação do disco.

   ![Fazer backup agora](./media/backup-azure-backup-import-export/opbackupnow.png)

## <a name="prepare-sata-drives-and-ship-to-azure"></a>Preparar unidades SATA e enviar para o Azure
O utilitário *AzureOfflineBackupDiskPrep* prepara as unidades SATA que são enviadas para o datacenter do Azure mais próximo. Esse utilitário está disponível no diretório de instalação do agente de backup do Azure (no seguinte caminho):

   *\Microsoft serviços de recuperação do Azure Agent\Utils\\*

1. Vá para o diretório e copie o diretório **AzureOfflineBackupDiskPrep** para outro computador em que as unidades SATA estão conectadas. No computador com as unidades SATA conectadas, verifique se:

   * O computador de cópia pode acessar o local de preparo para o fluxo de trabalho de propagação offline usando o mesmo caminho de rede fornecido no fluxo de trabalho **Iniciar backup offline** .
   * O BitLocker está habilitado no computador de cópia.
   * Azure PowerShell 3.7.0 está instalado.
   * Os navegadores compatíveis mais recentes (Microsoft Edge ou Internet Explorer 11) estão instalados e o JavaScript está habilitado. 
   * O computador de cópia pode acessar o portal do Azure. Se necessário, o computador de cópia pode ser o mesmo que o computador de origem.
    
     > [!IMPORTANT] 
     > Se o computador de origem for uma máquina virtual, o computador de cópia deverá ser um computador cliente ou servidor físico diferente do computador de origem.

2. Abra um prompt de comando com privilégios elevados no computador de cópia com o diretório do utilitário *AzureOfflineBackupDiskPrep* como o diretório atual e execute o seguinte comando:

    ```.\AzureOfflineBackupDiskPrep.exe s:<Staging Location Path>```

    | Parâmetro | Descrição |
    | --- | --- |
    | s:&lt;*caminho do local de preparo*&gt; |Entrada obrigatória usada para fornecer o caminho para o local de preparo que você inseriu no fluxo de trabalho **Iniciar backup offline** . |
    | p:&lt;*caminho para PublishSettingsFile*&gt; |Entrada opcional que é usada para fornecer o caminho para o arquivo de **configurações de publicação do Azure** que você inseriu no fluxo de trabalho **Iniciar backup offline** . |

    Quando você executa o comando, o utilitário solicita a seleção do trabalho de importação do Azure que corresponde às unidades que precisam ser preparadas. Se apenas um único trabalho de importação estiver associado ao local de preparo fornecido, você verá uma tela como a mostrada a seguir.

    ![Entrada da ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/diskprepconsole0_1.png) <br/>

3. Insira a letra da unidade sem os dois pontos à direita para o disco montado que você deseja preparar para a transferência para o Azure. 
4. Forneça a confirmação para a formatação da unidade quando solicitado.
5. Você será solicitado a entrar em sua assinatura do Azure. Forneça suas credenciais.

    ![Entrada da ferramenta de preparação de disco do Azure](./media/backup-azure-backup-import-export/signindiskprep.png) <br/>

    Em seguida, a ferramenta começa a preparar o disco e copiar os dados de backup. Talvez seja necessário anexar discos adicionais quando solicitado pela ferramenta caso o disco fornecido não tenha espaço suficiente para os dados de backup. <br/>

    No final da execução bem-sucedida da ferramenta, o prompt de comando fornece três informações:
   1. Um ou mais discos fornecidos estão preparados para envio ao Azure. 
   2. Você recebe a confirmação de que o trabalho de importação foi criado. O trabalho de importação usa o nome fornecido.
   3. A ferramenta exibe o endereço de envio para o datacenter do Azure.

      ![Preparação do disco do Azure concluída](./media/backup-azure-backup-import-export/console2.png)<br/>

6. No final da execução do comando, você pode atualizar as informações de envio.

7. Envie os discos para o endereço que a ferramenta forneceu e mantenha o número de rastreamento para referência futura.

   > [!IMPORTANT] 
   > Dois trabalhos de importação do Azure não podem ter o mesmo número de acompanhamento. Verifique se as unidades preparadas pelo utilitário em um único trabalho de importação do Azure são enviadas juntas em um único pacote e se há um único número de controle exclusivo para o pacote. Não combine unidades preparadas como parte de trabalhos de importação do Azure separados em um único pacote.



## <a name="update-shipping-details-on-the-azure-import-job"></a>Atualizar detalhes de envio no trabalho de importação do Azure

O procedimento a seguir atualiza os detalhes de envio do trabalho de importação do Azure. Essas informações incluem detalhes sobre:
* o nome da transportadora que entrega os discos para o Azure
* retornar detalhes de envio para seus discos

1. Entre em sua assinatura do Azure.
2. No menu principal, clique em **todos os serviços** e, na caixa de diálogo todos os serviços, digite importar. Quando você vir **trabalhos de importação/exportação**, clique nele.
    ![Inserindo informações de remessa](./media/backup-azure-backup-import-export/search-import-job.png)<br/>

    O menu lista de **trabalhos de importação/exportação** é aberto e a lista de todos os trabalhos de importação/exportação na assinatura selecionada é exibida. 

3. Se você tiver várias assinaturas, certifique-se de selecionar a assinatura usada para importar os dados de backup. Em seguida, selecione o trabalho de importação criado recentemente para abrir seus detalhes.

    ![Examinar informações de envio](./media/backup-azure-backup-import-export/import-job-found.png)<br/>

4. No menu configurações do trabalho de importação, clique em **gerenciar informações de envio** e insira os detalhes de envio de retorno.

    ![Armazenando informações de envio](./media/backup-azure-backup-import-export/shipping-info.png)<br/>

5. Quando você tiver o número de rastreamento de sua operadora de entrega, clique na faixa na página Visão geral do trabalho de importação do Azure e insira os seguintes detalhes:

   > [!IMPORTANT] 
   > Confirme que as informações da transportadora e que o número de acompanhamento estão atualizados num prazo de duas semanas após a criação do trabalho de importação do Azure. A falha de verificação dessas informações dentro de duas semanas pode resultar na exclusão do trabalho e nas unidades não processadas.

   ![Armazenando informações de envio](./media/backup-azure-backup-import-export/joboverview.png)<br/>

   ![Armazenando informações de envio](./media/backup-azure-backup-import-export/tracking-info.png)

### <a name="time-to-process-the-drives"></a>Tempo para processar as unidades 
O tempo necessário para processar um trabalho de importação do Azure varia de acordo com os diferentes fatores, como tempo de envio, tipo de trabalho, tipo e tamanho dos dados que estão sendo copiados e o tamanho dos discos fornecidos. O serviço de importação/exportação do Azure não tem um SLA, mas depois que os discos são recebidos, o serviço se esforça para concluir a cópia de dados de backup para sua conta de armazenamento do Azure em 7 a 10 dias. 

### <a name="monitoring-azure-import-job-status"></a>Monitorando o status do trabalho de importação do Azure
Você pode monitorar o status do trabalho de importação na portal do Azure navegando até a página **trabalhos de importação/exportação** e selecionando seu trabalho. Para obter mais informações sobre o status dos trabalhos de importação, consulte o artigo [serviço de exportação de importação de armazenamento](../storage/common/storage-import-export-service.md) .

### <a name="complete-the-workflow"></a>Concluir o fluxo de trabalho
Depois que o trabalho de importação for concluído com êxito, os dados de backup iniciais estarão disponíveis em sua conta de armazenamento. No momento do próximo backup agendado, o backup do Azure copia o conteúdo dos dados da conta de armazenamento para o cofre dos serviços de recuperação, conforme mostrado abaixo: 

   ![Copiando dados para o cofre dos serviços de recuperação](./media/backup-azure-backup-import-export/copyingfromstorageaccounttoazurebackup.png)<br/>

No momento do próximo backup agendado, o backup do Azure executa um backup incremental.

### <a name="cleaning-up-resources"></a>Limpando recursos
Depois que o backup inicial for concluído, você poderá excluir com segurança os dados importados para o contêiner de armazenamento do Azure e os dados de backup no local de preparo.

## <a name="next-steps"></a>Passos seguintes
* Para perguntas sobre o fluxo de trabalho de importação/exportação do Azure, consulte [usar o serviço de importação/exportação Microsoft Azure para transferir dados para o armazenamento de BLOBs](../storage/common/storage-import-export-service.md).
* Consulte a seção backup offline das [perguntas frequentes](backup-azure-backup-faq.md) sobre o backup do Azure para dúvidas sobre o fluxo de trabalho.
