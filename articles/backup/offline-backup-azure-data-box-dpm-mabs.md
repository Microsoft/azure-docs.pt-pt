---
title: Backup offline com caixa de dados Azure para DPM e MABS
description: Pode utilizar a Caixa de Dados Azure para semear dados de backup iniciais offline a partir de DPM e MABS.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752554"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Sementeira offline usando Azure Data Box para DPM e MABS (Pré-visualização)

> [!NOTE]
> Esta funcionalidade é aplicável ao Gestor de Proteção de Dados (DPM) 2019 UR2 e posteriormente.<br><br>
> Esta funcionalidade encontra-se atualmente em pré-visualização para o Microsoft Azure Backup Server (MABS). Se estiver interessado em utilizar a Azure Data Box para sementeira offline com mABS, contacte-nos em [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

Este artigo explica como pode usar a Caixa de Dados Azure para semear dados de backup iniciais offline de DPM e MABS para um cofre dos Serviços de Recuperação do Azure.

Pode utilizar [a Caixa de Dados Azure](../databox/data-box-overview.md) para semear as suas grandes cópias de segurança DPM/MABS iniciais offline (sem utilizar a rede) para um cofre dos Serviços de Recuperação. Este processo poupa tempo e largura de banda de rede que de outra forma seriam consumidos movendo grandes quantidades de dados de backup on-line sobre uma rede de alta latência. Esta funcionalidade encontra-se em pré-visualização.

A cópia de segurança offline baseada na Azure Data Box oferece duas vantagens distintas sobre [a cópia de segurança offline baseada no serviço Azure Import/Export](backup-azure-backup-server-import-export.md):

- Não precisa de obter os seus próprios discos e conectores compatíveis com o Azure. A Azure Data Box envia os discos associados à [SKU da Caixa de Dados](https://azure.microsoft.com/services/databox/data/)selecionada .

- O Azure Backup (Agente MARS) pode escrever diretamente dados de backup nos SKUs suportados da Caixa de Dados Azure. Esta capacidade elimina a necessidade de fornecer um local de paragem para os seus dados de backup iniciais. Também não precisa de utilitários para formatar e copiar esses dados nos discos.

## <a name="supported-platforms"></a>Plataformas suportadas

São suportadas as seguintes plataformas:

- Windows Server 2019 64 bit (Standard, Datacenter, Essentials)
- Windows Server 2016 64 bit (Standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho de dados de backup e SKUs de caixa de dados suportados

São suportados os seguintes SKUs da Caixa de Dados:

| Tamanho de dados de backup (após compressão por MARS) \* por servidor | Suportada Caixa de Dados Azure SKU |
| --- | --- |
| \<= 7,2 TB | [Disco de caixa de dados Azure](../databox/data-box-disk-overview.md) |
| > 7,2 TB e <= 80 TB\*\* | [Caixa de Dados Azure (100 TB)](../databox/data-box-overview.md) |

\*As taxas típicas de compressão variam entre 10 e 20% <br>
\*\*Contacte [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) se espera ter mais de 80 TB de dados de backup iniciais para uma única fonte de dados.

> [!IMPORTANT]
> Os dados de cópia de segurança iniciais de uma única fonte de dados devem ser contidos num único disco Azure Data Box ou Azure Data Box, e não podem ser partilhados entre vários dispositivos dos mesmos SKUs ou diferentes. No entanto, uma Caixa de Dados Azure pode conter cópias de segurança iniciais de várias fontes de dados.

## <a name="before-you-begin"></a>Antes de começar

O agente MARS em execução no DPM/MABS deve ser atualizado para a [versão mais recente](https://aka.ms/azurebackup_agent) (2.0.9171.0 ou mais tarde).

Certifique-se do seguinte:

### <a name="azure-subscription-and-required-permissions"></a>Assinatura Azure e permissões necessárias

- Uma subscrição válida da Azure.
- O utilizador destinado a executar a política de backup offline deve ser proprietário da subscrição Azure.
- O trabalho da Caixa de Dados e o cofre dos Serviços de Recuperação para o qual os dados precisam de ser semeados devem estar disponíveis nas mesmas subscrições.
    > [!NOTE]
    > Recomendamos que a conta de armazenamento alvo e o cofre dos Serviços de Recuperação estejam na mesma região. No entanto, isto não é obrigatório.

### <a name="order-and-receive-the-data-box-device"></a>Encomende e receba o dispositivo Data Box

Certifique-se de que os dispositivos data box necessários estão no estado *de Entrega* antes de ativar a cópia de segurança offline. Consulte [o tamanho dos dados de backup e skus de caixa de dados suportados](#backup-data-size-and-supported-data-box-skus) para encomendar o SKU mais adequado para a sua exigência. Siga os passos [deste artigo](../databox/data-box-disk-deploy-ordered.md) para encomendar e receba os seus dispositivos Data Box.

> [!IMPORTANT]
> Não selecione *BlobStorage* para o **tipo de Conta**. O servidor DPM/MABS requer uma conta que suporta Page Blobs que não é suportada quando *o BlobStorage* é selecionado. Selecione  **o Armazenamento V2 (propósito geral v2)** como o **tipo conta** ao criar a conta de armazenamento alvo para a sua função Azure Data Box.

![Configuração azure databox](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Configurar dispositivos Azure Data Box

Assim que receber o dispositivo Azure Data Box, dependendo do SKU da Caixa de Dados Azure que encomendou, execute os passos nas secções apropriadas abaixo para configurar e preparar os dispositivos Data Box para o Servidor DPM/MABS identificar e transferir os dados de cópia de segurança iniciais.

### <a name="setup-azure-data-box-disk"></a>Configuração Disco de caixa de dados Azure

Se encomendou um ou mais discos Azure Data Box (até 8 TB cada), siga os passos [aqui](../databox/data-box-disk-deploy-set-up.md) mencionados para desempacotar, ligar e desbloquear o disco data box.

> [!NOTE]
> É possível que o servidor DPM/MABS não tenha uma porta USB. Neste cenário, pode ligar o disco Azure Data Box a outro servidor/cliente e expor a raiz do dispositivo como uma partilha de rede.

## <a name="setup-azure-data-box"></a>Caixa de dados Azure configuração

Se encomendou uma Caixa de Dados Azure (até 100 TB), siga os passos [aqui](../databox/data-box-deploy-set-up.md) mencionados para configurar a sua Caixa de Dados.

### <a name="mount-your-azure-data-box-as-local-system"></a>Monte a sua Caixa de Dados Azure como sistema local

O servidor DPM/MABS funciona no contexto do Sistema e, por isso, requer o mesmo nível de privilégio a ser fornecido ao caminho de montagem onde a Caixa de Dados Azure está ligada. Siga os passos abaixo para se certificar de que pode montar o seu dispositivo Data Box como sistema local utilizando o protocolo NFS.

1. Ativar a função Cliente para NFS no Servidor DPM/MABS.
Especificar fonte alternativa: *WIM:D:\Fontes\Instalar.wim:4*
2. Faça o download **do PSExec** [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) para o servidor DPM/MABS.
3. Abra uma solicitação de comando elevada e execute o seguinte comando com o diretório contendo *PSExec.exe* como diretório atual.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. A janela de comando que se abre como resultado do comando acima está no contexto do Sistema Local. Utilize esta janela de comando para executar passos para montar o Azure Page Blob Share como uma unidade de rede no seu Servidor Windows.
5. Siga os passos [aqui](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) para ligar o seu Servidor DPM/MABS ao dispositivo Caixa de Dados via NFS e execute o seguinte comando no comando do Sistema Local para montar a partilha das Blobs de Página Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Uma vez montado, verifique se consegue aceder a X: a partir do seu servidor. Se puder, continue com a próxima secção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados de backup iniciais para dispositivos Azure Data Box

1. No seu Servidor DPM/MABS, siga os passos para [criar um novo grupo de proteção](/system-center/dpm/create-dpm-protection-groups). Se estiver a adicionar uma proteção on-line ao grupo de proteção existente, clique com o botão direito no grupo de proteção existente e selecione **Add Online Protection** e comece a partir do passo **8**.
2. Na página **'Selecionar Membros do Grupo',** especifique os computadores e as fontes que pretende fazer.
3. Na página do **método de proteção de dados Selecione,** especifique como pretende lidar com a cópia de segurança a curto e longo prazo. Certifique-se de que **seleciona que quero proteção online.**

   ![Criar novo grupo de proteção](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Na página **de objetivos de curto prazo,** especifique como pretende voltar ao armazenamento de curto prazo no disco.
5. Na página de atribuição do **disco Review,** reveja o espaço do disco de armazenamento atribuído ao grupo de proteção.
6. Na página do **método de criação de réplicas,** selecione **automaticamente através da rede.**
7. Na página **Escolha opções de verificação de consistência,** selecione como pretende automatizar verificações de consistência.
8. Na página **de dados de proteção on-line Specify,** selecione o membro que deseja para ativar a proteção on-line.

    ![Especificar dados de proteção on-line](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Na página de **agendamento de backup on-line Espec,** especifique com que frequência devem ocorrer cópias de segurança incrementais para o Azure.
10. Na página de **política de retenção on-line Specify,** especifique como os pontos de recuperação criados a partir das cópias de segurança diárias/semanais/mensais são mantidos em Azure.
11. No ecrã de **replicação on-line** do assistente, escolha a opção **Transferir usando discos da Microsoft** e selecione **Next**.

    ![Escolha a replicação inicial on-line](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > A opção de selecionar **Transferência usando discos da Microsoft** não está disponível para MABS v3 uma vez que a funcionalidade está em pré-visualização. Contacte-nos [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) se pretender utilizar esta funcionalidade para MABS v3.

12. Inscreva-se no Azure quando solicitado, utilizando as credenciais do utilizador que têm acesso ao proprietário na Subscrição Azure. Depois de uma sposição bem sucedida, é apresentado o seguinte ecrã:

    ![Após login bem sucedido](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     O servidor DPM/MABS irá então buscar os postos de trabalho da Caixa de Dados na subscrição que se encontram em estado *de Entrega.*

     > [!NOTE]
     > A primeira vez que o sinsu máximo demora mais do que o habitual. O módulo Azure PowerShell é instalado em segundo plano, e também está registada a Aplicação AD Azure.
     >
     >  - São instalados os seguintes módulos PowerShell:<br>
          - AzurerM.Perfil     *5.8.3*<br>
          - AzurerM.Resources   *6.7.3*<br>
          - AzurerM.Storage     *5.2.0*<br>
          - Azure.Storage       *4.6.1*<br>
     >  - O pedido AD Azure está registado como *AzureOfflineBackup_ \<object GUID of the user>*.

13. Selecione a encomenda correta da caixa de dados para a qual desempacotou, ligou e desbloqueou o disco da Caixa de Dados. Selecione **Seguinte**.

    ![Selecione a caixa de dados](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. No ecrã **'Detetar a Caixa de Dados',** insira o caminho do seu dispositivo Data Box e, em seguida, selecione **Detect Device**.

    ![Insira o caminho da rede](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Forneça o caminho da rede para o diretório de raiz do disco Azure Data Box. Este diretório deve conter um diretório com o nome *PageBlob,* como mostrado abaixo:
    >
    > ![Unidade USB](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Por exemplo, se o caminho do disco é e o `\\mydomain\myserver\disk1\` *disco1* contém um diretório chamado *PageBlob,* o caminho a fornecer no assistente do Servidor DPM/MABS é `\\mydomain\myserver\disk1\` .
    > Se [configurar um dispositivo Azure Data Box 100 TB,](./offline-backup-azure-data-box.md#set-up-azure-data-box)forneça o seguinte como caminho de rede para o dispositivo `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Selecione **Seguinte**. Na página **Resumo,** reveja as definições e selecione **Criar Grupo**.

    ![Detetar caixa de dados](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    O seguinte ecrã confirma que o Grupo de Proteção foi criado com sucesso.

    ![Grupo de proteção criado](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. **Selecione Fechar** no ecrã acima.

    Com isto, a replicação inicial dos dados ocorre ao disco DPM/MABS. Quando terminar a proteção, o estado de grupo mostrará o estado de proteção como **OK** na página **Proteção.**

17. Para iniciar a cópia de cópia de backup offline no seu dispositivo Azure Data Box, clique com o botão direito para o **Grupo de Proteção** e, em seguida, escolha a opção **Ponto de recuperação Criar.** Em seguida, escolha a opção **Proteção Online.**

    ![Criar ponto de recuperação](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Ponto de recuperação](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   O servidor DPM/MABS começará a fazer o backup dos dados selecionados para o dispositivo Azure Data Box. Isto pode demorar de várias horas a alguns dias, dependendo do tamanho dos dados e da velocidade de ligação entre o servidor DPM/MABS e o Disco caixa de dados Azure.

   Pode monitorizar o estado do trabalho no painel **de monitorização.** Assim que a cópia de segurança dos dados estiver concluída, verá um ecrã que se assemelha ao abaixo:

   ![Consola de administrador](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Passos pós-backup

Siga estes passos assim que a cópia de segurança dos dados para o Disco caixa de dados Azure for bem sucedida.

- Siga os passos [deste artigo](../databox/data-box-disk-deploy-picked-up.md) para enviar o disco Azure Data Box para Azure. Se utilizar um dispositivo Azure Data Box 100-TB, siga [estes passos](../databox/data-box-deploy-picked-up.md) para enviar a Caixa de Dados Azure para Azure.
- [Monitorize o trabalho da Caixa de Dados](../databox/data-box-disk-deploy-upload-verify.md) no portal Azure. Uma vez *concluída* a função Azure Data Box, o servidor DPM/MABS move automaticamente os dados da Conta de Armazenamento para o cofre dos Serviços de Recuperação no momento da próxima cópia de segurança programada. Em seguida, marcará o trabalho de backup como *Job Complete* se um ponto de recuperação for criado com sucesso.

  > [!NOTE]
  > O servidor DPM/MABS ativa as cópias de segurança nos horários programados durante a criação do grupo de proteção. No entanto, estes trabalhos serão assinalados À espera que o *trabalho da Caixa de Dados Azure esteja concluído* até ao momento em que o trabalho estiver concluído.

- Depois de o DPM/MABS Server criar com sucesso um ponto de recuperação correspondente à cópia de segurança inicial, poderá eliminar a Conta de Armazenamento (ou conteúdo específico) associada à função Caixa de Dados Azure.

## <a name="troubleshooting"></a>Resolução de problemas

O agente Microsoft Azure Backup (MAB) no servidor DPM cria uma aplicação AD Azure para si, no seu inquilino. Esta aplicação requer um certificado de autenticação que é criado e carregado ao configurar a política de sementeira offline.

Utilizamos a Azure PowerShell para criar e carregar o certificado para a Aplicação AD Azure.

### <a name="issue"></a>Problema

No momento da configuração da cópia de segurança offline, devido a um defeito de código conhecido no cmdlet Azure PowerShell, não é possível adicionar vários certificados à mesma Aplicação AD Azure criada pelo agente MAB. Isto irá impactá-lo se tiver configurado uma política de sementeira offline para o mesmo ou um servidor diferente.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Verifique se o problema é causado por esta causa de raiz específica

Para garantir que a falha se deve à [questão acima,](#issue) execute um dos seguintes passos:

#### <a name="step-1"></a>Passo 1

Verifique se vê a seguinte mensagem de erro na consola DPM/MABS no momento da configuração da cópia de segurança offline:

![Agente de serviços de recuperação da Azure](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Passo 2

1. Abra a pasta **Temp** no caminho de instalação (o caminho da pasta temporária predefinida é *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Procure o ficheiro *CBUICurr* e abra o ficheiro.
2. No ficheiro *CBUICurr,* percorra a última linha e verifique se a falha se deve a "Não conseguir criar uma credencial de aplicação AD Azure na conta do cliente. Exceção: Não é permitida a atualização da credencial existente com o \<some guid> KeyId".

### <a name="workaround"></a>Solução

Para resolver este problema, faça os seguintes passos e redoça a configuração da política.

1. Inscreva-se na página de entrada de Azure que aparece no UI do servidor DPM/MABS utilizando uma conta diferente com acesso a administração na subscrição que terá o trabalho de Caixa de Dados criado.
2. Se nenhum outro servidor tiver sementeira offline configurada e nenhum outro servidor depender da `AzureOfflineBackup_<Azure User Id>` aplicação, então elimine esta aplicação do **portal Azure > registos de Azure Ative Directory > App**.

   > [!NOTE]
   > Verifique se a aplicação `AzureOfflineBackup_<Azure User Id>` não tem nenhuma outra semente offline configurada e também nenhum outro servidor depende desta aplicação. Aceda às **definições > teclas** na secção Chaves Públicas. Não deve ter outras **chaves públicas** adicionadas. Consulte a seguinte imagem de referência:
   >
   > ![Chaves públicas](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Passo 3

A partir do servidor DPM/MABS está a tentar configurar a cópia de segurança offline, faça as seguintes ações:

1. Abra o **pedido de certificado de computador Gerir** o  >  separador **pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Selecione o certificado acima, clique com o botão direito **Todas as Tarefas** e **Exporte** sem chave privada, no formato .cer.
3. Aceda à aplicação de backup offline Azure mencionada no **ponto 2**. Nas **Chaves de Definições**  >    >  **Carrevare a chave pública,** carre faça o upload do certificado exportado no degrau acima.

   ![Carregar chaves públicas](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. No servidor, abra o registo digitando **o regedit** na janela **Run.**
5. Aceda ao registo *Computer\HKEY \_ LOCAL \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackUpProvider*. Clique com o botão direito **CloudBackupProvider** e adicione um novo valor de cadeia com o nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Para obter o ID do utilizador Azure, execute uma destas ações:
    >
    >- Da PowerShell ligada ao Azure, executar o `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` comando.
    > - Navegue para o caminho do registo `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` com o nome *CurrentUserId*.

6. Clique com o botão direito na cadeia adicionada no degrau acima e selecione **Modificar**. No valor, forneça a impressão digital do certificado que exportou no **ponto 2** e selecione **OK**.
7. Para obter o valor da impressão digital, clique duas vezes no certificado, em seguida, selecione **Detalhes**  e desloque-se até ver o campo de impressão digital. Selecione **a impressão digital** e copie o valor.

   ![Valor da impressão digital](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Passos seguintes

- [Sementeira offline utilizando o disco próprio (utilizando o serviço Azure Import/Export)](backup-azure-backup-server-import-export.md)
