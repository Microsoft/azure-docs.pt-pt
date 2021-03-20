---
title: Backup offline usando Azure Data Box
description: Saiba como pode utilizar a Caixa de Dados do Azure para semear dados de backup iniciais do Agente MARS para um cofre dos Serviços de Recuperação.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752588"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Backup Azure backup offline usando Azure Data Box

Pode utilizar [a Azure Data Box](../databox/data-box-overview.md) para semear as suas grandes cópias de segurança iniciais do Microsoft Azure Recovery Services (MARS) offline (sem utilizar a rede) para um cofre dos Serviços de Recuperação. Este processo poupa tempo e largura de banda de rede que de outra forma seriam consumidos movendo grandes quantidades de dados de backup on-line sobre uma rede de alta latência. Esta melhoria está atualmente em pré-visualização. A cópia de segurança offline baseada na Azure Data Box oferece duas vantagens distintas sobre [a cópia de segurança offline baseada no serviço Azure Import/Export](./backup-azure-backup-import-export.md):

- Não há necessidade de adquirir os seus próprios discos e conectores compatíveis com o Azure. A Azure Data Box envia os discos associados à [SKU da Caixa de Dados](https://azure.microsoft.com/services/databox/data/)selecionada .
- O Azure Backup (Agente MARS) pode escrever diretamente dados de backup nos SKUs suportados da Caixa de Dados Azure. Esta capacidade elimina a necessidade de fornecer um local de paragem para os seus dados de backup iniciais. Também não precisa de utilitários para formatar e copiar esses dados nos discos.

## <a name="azure-data-box-with-the-mars-agent"></a>Caixa de dados Azure com o Agente MARS

Este artigo explica como pode utilizar a Caixa de Dados Azure para semear dados de backup iniciais do Agente MARS para um cofre dos Serviços de Recuperação.

## <a name="supported-platforms"></a>Plataformas suportadas

O processo para dados de sementes do Agente MARS utilizando a Caixa de Dados Azure é suportado nos seguintes SKUs do Windows.

| **SO**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Estação de trabalho**                        |                                                              |
| Windows 10 de 64 bits                     | Enterprise, Pro, Home                                       |
| Windows 8.1 de 64 bits                    | Enterprise, Pro                                             |
| Windows 8 de 64 bits                      | Enterprise, Pro                                             |
| Windows 7 de 64 bits                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Servidor**                             |                                                              |
| Windows Server 2019 de 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 de 64 bits            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 de 64 bits         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 de 64 bits            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016 de 64 bits    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2 de 64 bits | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 de 64 bits    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1 de 64 bits     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho de dados de backup e SKUs de caixa de dados suportados

| Tamanho de dados de backup (pós-compressão por MARS)* por servidor | Suportada Caixa de Dados Azure SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7.2 TB                                                    | [Disco de caixa de dados Azure](../databox/data-box-disk-overview.md) |
| >7.2 TB e <=80 TB**                                      | [Caixa de Dados Azure (100 TB)](../databox/data-box-overview.md) |

*As taxas típicas de compressão variam entre 10% e 20%. <br>
**Se espera ter mais de 80 TB de dados de cópia de segurança iniciais para um único servidor MARS, contacte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Os dados de cópia de segurança iniciais de um único servidor devem ser contidos numa única instância da Caixa de Dados Azure ou no disco Azure Data Box e não podem ser partilhados entre vários dispositivos dos mesmos SKUs ou diferentes. Mas um dispositivo Azure Data Box pode conter cópias de segurança iniciais de vários servidores.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription-and-required-permissions"></a>Assinatura Azure e permissões necessárias

- O processo requer uma subscrição do Azure.
- O processo requer que o utilizador designado para executar a política de backup offline seja proprietário da subscrição Azure.
- O trabalho da Caixa de Dados e o cofre dos Serviços de Recuperação (para o qual os dados precisam de ser semeados) são obrigados a estar nas mesmas subscrições.
- Recomendamos que a conta de armazenamento-alvo associada ao trabalho da Caixa de Dados Azure e o cofre dos Serviços de Recuperação se encontram na mesma região. No entanto, isto não é necessário.

### <a name="get-azure-powershell-370"></a>Obter Azure PowerShell 3.7.0

*Este é o pré-requisito mais importante para o processo.* Antes de instalar o Azure PowerShell, versão 3.7.0, efetue as seguintes verificações.

#### <a name="step-1-check-the-powershell-version"></a>Passo 1: Verifique a versão PowerShell

1. Abra o Windows PowerShell e execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Se a saída apresentar uma versão superior a 3.7.0, faça "Passo 2". Caso contrário, salte para o "Passo 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Passo 2: Desinstalar a versão PowerShell

Desinstale a versão atual do PowerShell.

1. Remova os módulos dependentes executando o seguinte comando em PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Para garantir a eliminação bem sucedida de todos os módulos dependentes, executar o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passo 3: Instalar a versão PowerShell 3.7.0

Depois de verificar que não existem módulos AzureRM, instale a versão 3.7.0 utilizando um dos seguintes métodos:

- A partir do GitHub, utilize [este link.](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

Ou pode:

- Executar o seguinte comando na janela PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

O Azure PowerShell também poderia ter sido instalado utilizando um ficheiro MSI. Para removê-lo, desinstale-o utilizando a opção **de programas Desinstalar** no Painel de Controlo.

### <a name="order-and-receive-the-data-box-device"></a>Encomende e receba o dispositivo Data Box

O processo de cópia de segurança offline utilizando a MARS e a Azure Data Box requer que os dispositivos Data Box estejam num estado entregue antes de acionar a cópia de segurança offline utilizando o Agente MARS. Para encomendar o SKU mais adequado para a sua exigência, consulte [o tamanho dos dados de backup e SKUs de caixa de dados suportados.](#backup-data-size-and-supported-data-box-skus) Siga os passos em [Tutorial: Encomende um disco Azure Data Box](../databox/data-box-disk-deploy-ordered.md) para encomendar e receber os seus dispositivos Data Box.

> [!IMPORTANT]
> Não selecione *BlobStorage* para o **tipo de Conta**. O Agente MARS requer uma conta que suporte bolhas de página, que não é suportada quando *o BlobStorage* é selecionado. Selecione **o Armazenamento V2 (propósito geral v2)** como o **tipo conta** quando criar a conta de armazenamento alvo para a sua função Azure Data Box.

![Escolha o tipo de conta em detalhes de exemplo](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instale e instale o Agente MARS

1. Certifique-se de desinstalar quaisquer instalações anteriores do Agente MARS.
1. Descarregue o mais recente agente MARS [deste site.](https://aka.ms/azurebackup_agent)
1. Faça *MARSAgentInstaller.exe*, e faça *apenas* os passos para [instalar e registar o agente](./install-mars-agent.md#install-and-register-the-agent) no cofre dos Serviços de Recuperação onde pretende que as suas cópias de segurança sejam armazenadas.

   > [!NOTE]
   > O cofre dos Serviços de Recuperação deve estar na mesma subscrição que o trabalho da Caixa de Dados Azure.

   Depois de o agente estar registado no cofre dos Serviços de Recuperação, siga os passos nas secções seguintes.

## <a name="set-up-azure-data-box-devices"></a>Configurar dispositivos Azure Data Box

Dependendo da Caixa de Dados Azure SKU que encomendou, faça os passos cobertos nas secções apropriadas que se seguem. Os passos mostram-lhe como configurar e preparar os dispositivos Data Box para o Agente MARS identificar e transferir os dados de backup iniciais.

### <a name="set-up-azure-data-box-disks"></a>Configurar discos Azure Data Box

Se encomendou um ou mais discos Azure Data Box (até 8 TB cada), siga os passos aqui mencionados para [desempacotar, ligar e desbloquear o disco data box](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>É possível que o servidor com o Agente MARS não tenha uma porta USB. Nessa situação, pode ligar o disco Azure Data Box a outro servidor ou cliente e expor a raiz do dispositivo como uma partilha de rede.

### <a name="set-up-azure-data-box"></a>Configurar a Caixa de Dados Azure

Se encomendou uma instância Azure Data Box (até 100 TB), siga os passos aqui [para configurar a sua instância data box](../databox/data-box-deploy-set-up.md).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Monte a sua caixa de dados Azure como um sistema local

O Agente MARS opera no contexto do Sistema Local, pelo que requer o mesmo nível de privilégio a ser fornecido ao caminho de montagem onde está ligado a instância Azure Data Box.

Para garantir que pode montar o seu dispositivo Data Box como Sistema Local utilizando o protocolo NFS:

1. Ativar o cliente para a funcionalidade NFS no servidor Windows que tem o Agente MARS instalado. Especificar a fonte alternativa *WIM:D:\Fontes\Instalar.wim:4*.
1. Descarregue o PsExec da página [Sysinternals](/sysinternals/downloads/psexec) para o servidor com o Agente MARS instalado.
1. Abra uma solicitação de comando elevada e executar o seguinte comando com o diretório que contém *PSExec.exe* como o diretório atual.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   A janela de comando que se abre por causa do comando anterior encontra-se no contexto do Sistema Local. Utilize esta janela de comando para executar os passos para montar a partilha de blob de página Azure como uma unidade de rede no servidor Windows.
1. Siga os passos na [Caixa de Dados para](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) ligar o seu servidor ao Agente MARS ao dispositivo Data Box via NFS. Executar o seguinte comando no comando do Sistema Local pronta para montar a partilha de blobs de página Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Depois de a partilha ser montada, verifique se consegue aceder a X: a partir do seu servidor. Se puder, continue com a próxima secção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados de backup iniciais para dispositivos Azure Data Box

1. Abra a aplicação **De backup microsoft Azure** no seu servidor.
1. No painel **de ações,** selecione **Agendar Backup**.

    ![Selecione backup de horário](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Siga os passos no **Agendamento Do Assistente de Cópia de Segurança**.

1. Adicione itens selecionando o botão **Adicionar Itens.** Mantenha o tamanho total dos itens dentro dos limites de [tamanho suportados pela Caixa de Dados Azure SKU](#backup-data-size-and-supported-data-box-skus) que encomendou e recebeu.

    ![Adicione itens a cópia de segurança](./media/offline-backup-azure-data-box/add-items.png)

1. Selecione a política de backup e retenção adequada para **Ficheiros e Pastas** e **Estado do Sistema**. O estado do sistema é aplicável apenas para servidores windows e não para clientes Windows.
1. Na página 'Escolha o **Tipo de Cópia de Segurança Inicial' (Ficheiros e Pastas)** do assistente, selecione a opção Transferir utilizando os discos da Caixa de **Dados do Microsoft Azure** e selecione **Seguinte**.

    ![Escolha o tipo inicial de backup](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Inscreva-se no Azure quando solicitado utilizando as credenciais de utilizador que têm acesso ao Proprietário na subscrição do Azure. Depois de conseguir fazê-lo, deve ver uma página que se assemelhe a esta.

    ![Criar recursos e aplicar permissões necessárias](./media/offline-backup-azure-data-box/creating-resources.png)

   O Agente MARS, em seguida, recolhe os postos de trabalho da Caixa de Dados na subscrição que se encontram no estado entregue.

    ![Procure empregos na Caixa de Dados para iD de subscrição](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selecione a encomenda correta da Caixa de Dados para a qual desempacotou, ligou e desbloqueou o disco da Caixa de Dados. Selecione **Seguinte**.

    ![Selecione encomendas de caixa de dados](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selecione **Dispositivo de Deteção** na página **de deteção de dispositivos de caixa de dados.** Esta ação faz com que o Agente MARS procure discos Azure Data Box ligados localmente e detete-os.

    ![Deteção de dispositivos de caixa de dados](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se ligou a instância Azure Data Box como uma partilha de rede (devido à indisponibilidade das portas USB ou porque encomendou e montou o dispositivo 100-TB Data Box), a deteção falha no início. É-lhe dada a opção de introduzir o caminho da rede para o dispositivo Data Box.

    ![Insira o caminho da rede](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Forneça o caminho da rede para o diretório de raiz do disco Azure Data Box. Este diretório deve conter um diretório com o nome *PageBlob*.
    >
    >![Diretório de raiz do disco Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Por exemplo, se o caminho do disco estiver e o `\\mydomain\myserver\disk1\` *disco 1* contiver um diretório chamado *PageBlob,* o caminho que introduz na página do assistente do Agente MARS é `\\mydomain\myserver\disk1\` .
    >
    >Se [configurar um dispositivo Azure Data Box 100-TB,](#set-up-azure-data-box-devices)introduza `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` como o caminho da rede para o dispositivo.

1. Selecione **Seguinte**, e selecione **Terminar** na página seguinte para guardar a política de backup e retenção com a configuração da cópia de segurança offline utilizando a Caixa de Dados Azure.

   A página seguinte confirma que a apólice é guardada com sucesso.

    ![A política é salva com sucesso](./media/offline-backup-azure-data-box/policy-saved.png)

1. **Selecione Fechar** na página anterior.

1. Selecione **Back Up Now** no painel de **ações** da consola MARS Agent. Selecione **Back Up** na página de assistente.

    ![Back Up Now Wizard](./media/offline-backup-azure-data-box/backup-now.png)

O Agente MARS começa a fazer o backup dos dados selecionados para o dispositivo Azure Data Box. Este processo pode demorar de várias horas a alguns dias. A quantidade de tempo depende do número de ficheiros e da velocidade de ligação entre o servidor com o Agente MARS e o disco Azure Data Box.

Depois de terminar a cópia de segurança dos dados, verá uma página no Agente MARS que se assemelha a esta.

![Progresso de backup mostrado](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Passos pós-backup

Esta secção explica os passos a tomar após a cópia de segurança dos dados para o Disco de Caixa de Dados Azure ter sido bem sucedida.

- Siga os passos deste artigo para [enviar o disco Azure Data Box para a Azure](../databox/data-box-disk-deploy-picked-up.md). Se utilizar um dispositivo Azure Data Box 100-TB, siga estes passos para [enviar o dispositivo Azure Data Box para a Azure](../databox/data-box-deploy-picked-up.md).

- [Monitorize o trabalho da Caixa de Dados](../databox/data-box-disk-deploy-upload-verify.md) no portal Azure. Após o funcionamento do trabalho da Caixa de Dados Azure, o Agente MARS move automaticamente os dados da conta de armazenamento para o cofre dos Serviços de Recuperação no momento da próxima cópia de segurança programada. Em seguida, marca o trabalho de backup como *Job Complete* se um ponto de recuperação for criado com sucesso.

    >[!NOTE]
    >O Agente MARS aciona backups nos horários programados durante a criação de políticas. Estes postos de trabalho assinalam "À espera que o trabalho da Caixa de Dados Azure seja concluído" até ao momento em que o trabalho estiver concluído.

- Depois de o Agente MARS criar com sucesso um ponto de recuperação que corresponda à cópia de segurança inicial, pode eliminar a conta de armazenamento ou conteúdos específicos associados à função Azure Data Box.

## <a name="troubleshooting"></a>Resolução de problemas

O Agente microsoft Azure Recovery Services (MARS) cria uma aplicação Azure Ative Directory (Azure AD) para si no seu inquilino. Esta aplicação requer um certificado de autenticação que é criado e carregado quando configura uma política de sementeira offline. Utilizamos a Azure PowerShell para criar e enviar o certificado para a aplicação AZure AD.

### <a name="problem"></a>Problema

Quando configurar a cópia de segurança offline, poderá enfrentar um problema devido a um bug no cmdlet Azure PowerShell. Poderá não conseguir adicionar vários certificados à mesma aplicação AD AZure criada pelo Agente MAB. Este problema irá afetá-lo se configurar uma política de sementeira offline para o mesmo ou um servidor diferente.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Verifique se o problema é causado por esta causa de raiz específica

Para ver se o seu problema é o mesmo que o anteriormente descrito, faça um dos seguintes passos.

#### <a name="step-1-of-verification"></a>Passo 1 da verificação

Verifique se a seguinte mensagem de erro aparece na consola MAB quando configurar a cópia de segurança offline.

![Incapaz de criar a política de backup offline para a conta Azure atual](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Passo 2 da verificação

1. Abra a pasta **Temporária** no caminho de instalação. O caminho da pasta temporária predefinida é *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Procure o ficheiro *CBUICurr* e abra o ficheiro.

1. No ficheiro *CBUICurr,* percorra a última linha e verifique se o problema é o mesmo que o desta mensagem de erro: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Solução

Como uma solução alternativa para resolver este problema, faça os seguintes passos e reforce a configuração da política.

#### <a name="step-1-of-workaround"></a>Passo 1 da solução alternativa

Faça o sômis no PowerShell que aparece no UI MAB utilizando uma conta diferente com acesso a administrador na subscrição que terá o trabalho de Caixa de Dados criado.

#### <a name="step-2-of-workaround"></a>Passo 2 da solução alternativa

Se nenhum outro servidor tiver sementeira offline configurada e nenhum outro servidor depender da `AzureOfflineBackup_<Azure User Id>` aplicação, elimine esta aplicação. Selecione **registos** de aplicações do portal  >  **Azure Ative Directory**  >  .

>[!NOTE]
> Verifique se a `AzureOfflineBackup_<Azure User Id>` aplicação não tem nenhuma outra semente offline configurada e também se nenhum outro servidor depende desta aplicação. Aceda às **teclas de definições**  >   na secção **Chaves Públicas.** Não deve ter outras chaves públicas adicionadas. Consulte a seguinte imagem de referência.
>
>![Chaves públicas](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Passo 3

A partir do servidor está a tentar configurar para cópia de segurança offline, execute as seguintes ações.

1. Aceda ao **pedido de certificado de computador Manage**  >  **Personal,** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Selecione o certificado, clique com o botão direito **Todas as Tarefas** e selecione **Export** sem uma chave privada no formato .cer.

3. Aceda à aplicação de backup offline Azure mencionada no passo 2. Selecione **As chaves de**  >  **definições**  >  **Carregar a tecla pública**. Faça o upload do certificado que exportou no passo anterior.

    ![Faça upload da chave pública](./media/offline-backup-azure-data-box/upload-public-key.png)

4. No servidor, abra o registo introduzindo **o regedit** na janela de execução.

5. Vá ao *registoComputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Clique com o botão direito **CloudBackupProvider** e adicione um novo valor de corda com o nome `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Para obter o ID do utilizador Azure, execute uma destas ações:
    >
    >- Da PowerShell ligada ao Azure, executar o `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` comando.
    > - Vá para o caminho do registo `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` com o nome *CurrentUserId*.

6. Clique com o botão direito da cadeia adicionada no passo anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que exportou no passo 2. Selecione **OK**.

7. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione o separador **Detalhes** e desloque-se para baixo até ver o campo de impressão digital. Selecione **a impressão digital** e copie o valor.

    ![Campo de impressão digital de certificado](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Perguntas

Para quaisquer questões ou esclarecimentos sobre quaisquer problemas que tenha enfrentado, contacte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
