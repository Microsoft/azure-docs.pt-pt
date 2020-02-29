---
title: Backup offline usando caixa de dados Azure
description: Saiba como pode usar a Caixa de Dados Azure para semear grandes dados de backup iniciais offline do Agente MARS para um cofre dos Serviços de Recuperação.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 9a8288939adba8ec1b0cbe38d2243b1bdd84fa2e
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/29/2020
ms.locfileid: "78196472"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Backup offline Azure Backup utilizando caixa de dados Azure

Pode utilizar a Caixa de [Dados Azure](https://docs.microsoft.com/azure/databox/data-box-overview) para semear as suas grandes cópias de segurança iniciais do Microsoft Azure Recovery Services (MARS) offline (sem utilizar a rede) para um cofre dos Serviços de Recuperação. Este processo poupa tempo e largura de banda da rede que, de outra forma, seria consumida movendo grandes quantidades de dados de backup on-line sobre uma rede de alta latência. Esta melhoria está atualmente em pré-visualização. A cópia de segurança offline baseada na Caixa de Dados Azure proporciona duas vantagens distintas sobre a cópia de [segurança offline com base no serviço de importação/exportação Azure:](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export)

* Não há necessidade de adquirir os seus próprios discos e conectores compatíveis com o Azure. A Caixa de Dados Azure envia os discos associados à caixa de dados selecionada [SKU](https://azure.microsoft.com/services/databox/data/).
* A Backup Azure (Agente MARS) pode escrever diretamente dados de backup nas SKUs suportadas da Caixa de Dados Azure. Esta capacidade elimina a necessidade de fornecer um local de paragem para os seus dados de backup iniciais. Também não precisa de utilitários para formatar e copiar esses dados nos discos.

## <a name="azure-data-box-with-the-mars-agent"></a>Caixa de dados Azure com o agente MARS

Este artigo explica como pode usar a Caixa de Dados Azure para semear grandes dados de backup iniciais offline do Agente MARS para um cofre dos Serviços de Recuperação.

## <a name="supported-platforms"></a>Plataformas suportadas

O processo de semente ira dos dados do Agente MARS utilizando a Caixa de Dados Azure é suportado nas seguintes SKUs do Windows.

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Posto de trabalho**                        |                                                              |
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

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho dos dados de backup e SKUs suportado da Caixa de Dados

| Tamanho dos dados de backup (pós-compressão por MARS)* por servidor | Caixa de dados Azure Suportada SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7.2 TB                                                    | [Disco da Caixa de Dados Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7.2 TB e <=80 TB**                                      | [Caixa de Dados Azure (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*As taxas típicas de compressão variam entre 10% e 20%. <br>
**Se espera ter mais de 80 TB de dados de backup iniciais para um único servidor MARS, contacte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).

>[!IMPORTANT]
>Os dados iniciais de backup de um único servidor devem ser contidos numa única instância da Caixa de Dados Azure ou no disco Da Caixa de Dados Azure e não podem ser partilhados entre vários dispositivos das mesmas SKUs ou diferentes. Mas um dispositivo Azure Data Box pode conter cópias de segurança iniciais de vários servidores.

## <a name="prerequisites"></a>Pré-requisitos

### <a name="azure-subscription-and-required-permissions"></a>Assinatura Azure e permissões necessárias

* O processo requer uma subscrição do Azure.
* O processo requer que o utilizador designado para executar a política de backup offline seja um proprietário da subscrição Azure.
* O trabalho da Data Box e o cofre dos Serviços de Recuperação (para o qual os dados precisam de ser semeados) são obrigados a estar nas mesmas subscrições.
* Recomendamos que a conta de armazenamento alvo associada ao trabalho da Caixa de Dados Azure e ao cofre dos Serviços de Recuperação estejam na mesma região. No entanto, isto não é necessário.

### <a name="get-azure-powershell-370"></a>Obtenha Azure PowerShell 3.7.0

*Este é o pré-requisito mais importante para o processo.* Antes de instalar o Azure PowerShell, versão 3.7.0, efetuar as seguintes verificações.

#### <a name="step-1-check-the-powershell-version"></a>Passo 1: Verifique a versão PowerShell

1. Abra o Windows PowerShell e execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Se a saída apresentar uma versão superior a 3.7.0, faça "Passo 2". Caso contrário, salte para o "Passo 3".

#### <a name="step-2-uninstall-the-powershell-version"></a>Passo 2: Desinstalar a versão PowerShell

Desinstale a versão atual do PowerShell.

1. Retire os módulos dependentes executando o seguinte comando no PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Para garantir a eliminação bem sucedida de todos os módulos dependentes, execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passo 3: Instalar a versão PowerShell 3.7.0

Depois de verificar que não existem módulos AzureRM, instale a versão 3.7.0 utilizando um dos seguintes métodos:

* A partir do GitHub, utilize [este link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Ou pode:

* Executar o seguinte comando na janela PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

O Azure PowerShell também poderia ter sido instalado utilizando um ficheiro MSI. Para removê-lo, desinstale-o utilizando a opção **de desinstalar** programas no Painel de Controlo.

### <a name="order-and-receive-the-data-box-device"></a>Encomende e receba o dispositivo Data Box

O processo de backup offline utilizando MARS e Azure Data Box requer que os dispositivos data Box estejam em estado De delivered antes de ativar a cópia de segurança offline utilizando o Agente MARS. Para encomendar o SKU mais adequado para o seu requisito, consulte o tamanho dos dados de [backup e a Caixa de Dados Suportada SKUs](#backup-data-size-and-supported-data-box-skus). Siga os passos no [Tutorial: Encomende um disco de Caixa](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) de Dados Azure para encomendar e receba os seus dispositivos Data Box.

> [!IMPORTANT]
> Não selecione *BlobStorage* para o **tipo Conta**. O Agente MARS requer uma conta que suporte as bolhas de página, que não é suportada quando *o BlobStorage* é selecionado. Selecione **Storage V2 (propósito geral v2)** como o **tipo conta** quando criar a conta de armazenamento alvo para o seu trabalho na Caixa de Dados Azure.

![Escolha o tipo de conta em caso de detalhes](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instale e instale o Agente MARS

1. Certifique-se de que desinstala quaisquer instalações anteriores do Agente MARS.
1. Descarregue o mais recente agente MARS [deste site.](https://aka.ms/azurebackup_agent)
1. Executar *MARSAgentInstaller.exe*, e faça *apenas* os passos para [instalar e registar o agente](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) no cofre dos Serviços de Recuperação onde pretende que as suas cópias de segurança sejam armazenadas.

   > [!NOTE]
   > O cofre dos Serviços de Recuperação deve estar na mesma subscrição que o trabalho da Caixa de Dados Azure.

   Depois de o agente estar registado no cofre dos Serviços de Recuperação, siga os passos nas secções seguintes.

## <a name="set-up-azure-data-box-devices"></a>Configurar dispositivos De Caixa de Dados Azure

Dependendo da Caixa de Dados Azure SKU que encomendou, faça os passos cobertos nas secções apropriadas que se seguem. Os passos mostram-lhe como configurar e preparar os dispositivos Data Box para o Agente MARS identificar e transferir os dados de backup iniciais.

### <a name="set-up-azure-data-box-disks"></a>Configurar discos de caixa de dados Azure

Se encomendou um ou mais discos da Caixa de Dados Azure (até 8 TB cada), siga os passos aqui mencionados para [desembalar, ligar e desbloquear o disco data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>É possível que o servidor com o Agente MARS não tenha uma porta USB. Nessa situação, pode ligar o disco da Caixa de Dados Azure a outro servidor ou cliente e expor a raiz do dispositivo como uma partilha de rede.

### <a name="set-up-azure-data-box"></a>Configurar caixa de dados Azure

Se encomendou uma instância de Caixa de Dados Azure (até 100 TB), siga os passos aqui [para configurar a sua instância data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Monte a sua caixa de dados Azure como um Sistema Local

O Agente MARS opera no contexto do Sistema Local, pelo que requer o mesmo nível de privilégio a ser fornecido ao caminho de montagem onde a instância da Caixa de Dados Azure está ligada. 

Para garantir que pode montar o seu dispositivo Data Box como um Sistema Local utilizando o protocolo NFS:

1. Ative o cliente para a funcionalidade NFS no servidor Windows que tem o Agente MARS instalado. Especifique a fonte alternativa *WIM:D:\Sources\Install.wim:4*.
1. Baixe o PSExec de <https://download.sysinternals.com/files/PSTools.zip> para o servidor com o Agente MARS instalado.
1. Abra um pedido de comando elevado e execute o seguinte comando com o diretório que contém *PSExec.exe* como o atual diretório.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   A janela de comando que se abre como resultado do comando anterior está no contexto do Sistema Local. Utilize esta janela de comando para executar os passos para montar a partilha de blob da página Azure como uma unidade de rede no seu servidor Windows.
1. Siga os passos em [Connect to Data Box](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) para ligar o servidor ao dispositivo Data Box via NFS. Execute o seguinte comando no comando do Sistema Local para montar a parte azure blobs partilhar.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Depois de montada a partilha, verifique se consegue aceder a X: a partir do seu servidor. Se puder, continue com a próxima secção deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados iniciais de backup para dispositivos Da Caixa de Dados Do Azure

1. Abra a aplicação **Microsoft Azure Backup** no seu servidor.
1. No painel **Ações,** selecione **'Backup' de Agenda**.

    ![Selecione Reserva de Agenda](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Siga os passos no **Assistente de Reserva**de Agenda .

1. Adicione itens selecionando o botão **Adicionar Itens.** Mantenha o tamanho total dos itens dentro dos limites de tamanho suportados pela Caixa de [Dados Azure SKU](#backup-data-size-and-supported-data-box-skus) que encomendou e recebeu.

    ![Adicione itens à cópia de segurança](./media/offline-backup-azure-data-box/add-items.png)

1. Selecione o calendário de reserva e a política de retenção apropriados para **Ficheiros e Pastas** e **Estado do Sistema**. O estado do sistema é aplicável apenas para servidores Windows e não para clientes Windows.
1. Na página 'Escolha' Inicial do Tipo de Cópia de **Segurança (Ficheiros e Pastas)** do assistente, selecione a opção Transferir utilizando discos da Caixa de **Dados Microsoft Azure** e selecione **Next**.

    ![Escolha o tipo inicial de backup](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Inscreva-se no Azure quando solicitado utilizando as credenciais de utilizador que têm acesso do Proprietário na subscrição do Azure. Depois de conseguir fazê-lo, deve ver uma página que se assemelhe a esta.

    ![Criar recursos e aplicar permissões necessárias](./media/offline-backup-azure-data-box/creating-resources.png)

   O Agente MARS então procura os trabalhos da Data Box na subscrição que estão no estado entregue.

    ![Buscar empregos de Data Box para ID de subscrição](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Selecione a correta ordem data box para a qual desfez, ligou e desbloqueou o disco data Box. Selecione **Seguinte**.

    ![Selecione encomendas de Caixa de Dados](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Selecione **detetar dispositivo** na página de Deteção de Dispositivos da Caixa de **Dados.** Esta ação faz com que o Agente MARS digitalize os discos da Caixa de Dados Azure anexados localmente e os detete.

    ![Deteção de dispositivos de caixa de dados](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se ligou a instância da Caixa de Dados Azure como uma partilha de rede (devido à indisponibilidade de portas USB ou porque encomendou e montou o dispositivo caixa de dados de 100 TB), a deteção falha no início. É-lhe dada a opção de introduzir o caminho da rede para o dispositivo Data Box.

    ![Insira o caminho da rede](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Forneça o caminho de rede para o diretório raiz do disco Caixa de Dados Azure. Este diretório deve conter um diretório com o nome *PageBlob*.
    >
    >![Diretório raiz do disco Da Caixa de Dados Azure](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Por exemplo, se o caminho do disco for `\\mydomain\myserver\disk1\` e *o disco1* contiver um diretório chamado *PageBlob,* o caminho que introduzir na página do assistente do Agente MARS é `\\mydomain\myserver\disk1\`.
    >
    >Se [configurar um dispositivo Azure Data Box 100-TB,](#set-up-azure-data-box-devices)introduza `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` como o caminho de rede para o dispositivo.

1. Selecione **Next**, e selecione **Finish** na página seguinte para guardar a política de backup e retenção com a configuração de backup offline utilizando a Caixa de Dados Azure.

   A página seguinte confirma que a apólice é salva com sucesso.

    ![A política é salva com sucesso](./media/offline-backup-azure-data-box/policy-saved.png)

1. Selecione **Fechar** na página anterior.

1. Selecione **Back Up Now** no painel de **ações** da consola mars agent. **Selecione Back Up** na página do assistente.

    ![Back Up Now Wizard](./media/offline-backup-azure-data-box/backup-now.png)

O Agente MARS começa a fazer o backup dos dados selecionados para o dispositivo Azure Data Box. Este processo pode demorar de várias horas a alguns dias. O tempo depende do número de ficheiros e da velocidade de ligação entre o servidor com o Agente MARS e o disco Da Caixa de Dados Azure.

Depois de terminar a cópia de segurança dos dados, verá uma página no Agente MARS que se assemelha a esta.

![Progresso de backup mostrado](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Passos pós-cópia

Esta secção explica os passos a tomar após a cópia de segurança dos dados ao Disco de Caixa de Dados Azure ter sucesso.

* Siga os passos deste artigo para [enviar o disco Azure Data Box para o Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Se utilizou um dispositivo Azure Data Box 100-TB, siga estes passos para [enviar o dispositivo Azure Data Box para o Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Monitorize o trabalho da Caixa](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) de Dados no portal Azure. Após o trabalho da Caixa de Dados Azure estar terminado, o Agente MARS transfere automaticamente os dados da conta de armazenamento para o cofre dos Serviços de Recuperação no momento da próxima cópia de segurança programada. Em seguida, marca o trabalho de backup como *Job Completed* se um ponto de recuperação for criado com sucesso.

    >[!NOTE]
    >O Agente MARS aciona cópias de segurança nos horários programados durante a criação de políticas. Estes trabalhos assinalam "À espera que o trabalho da Caixa de Dados Azure esteja concluído" até ao momento em que o trabalho esteja concluído.

* Depois de o Agente MARS criar com sucesso um ponto de recuperação que corresponda à cópia de segurança inicial, pode eliminar a conta de armazenamento ou conteúdos específicos associados ao trabalho da Caixa de Dados Azure.

## <a name="troubleshooting"></a>Resolução de problemas

O Agente de Backup Microsoft Azure (MAB) cria uma aplicação Azure Ative Directory (Azure AD) para si no seu inquilino. Esta aplicação requer um certificado de autenticação que seja criado e carregado quando configura ruma uma política de sementeira offline. Utilizamos o Azure PowerShell para criar e carregar o certificado para a aplicação Azure AD.

### <a name="problem"></a>Problema

Quando configurar a cópia de segurança offline, poderá enfrentar um problema devido a um bug no cmdlet Azure PowerShell. Pode não conseguir adicionar vários certificados à mesma aplicação Azure AD criada pelo Agente MAB. Este problema irá afetá-lo se configurar uma política de sementeção offline para o mesmo ou um servidor diferente.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Verifique se o problema é causado por esta causa raiz específica

Para ver se o seu problema é o mesmo que o anteriormente descrito, faça um dos seguintes passos.

#### <a name="step-1"></a>Passo 1

Verifique se a seguinte mensagem de erro aparece na consola MAB quando configurar a cópia de segurança offline.

![Incapaz de criar a política de backup offline para a conta atual do Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Passo 2

1. Abra a pasta **Temp** no caminho de instalação. O caminho da pasta temporária predefinida é *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Procure o ficheiro *CBUICurr* e abra o ficheiro.

1. No ficheiro *CBUICurr,* percorra até à última linha e verifique se o problema é o mesmo que o desta mensagem de erro: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Solução

Como solução para resolver este problema, faça os seguintes passos e retente a configuração da política.

#### <a name="step-1"></a>Passo 1

Inscreva-se na PowerShell que aparece na UI MAB utilizando uma conta diferente com acesso administrativo na subscrição que terá o emprego de importação ou exportação criado.

#### <a name="step-2"></a>Passo 2

Se nenhum outro servidor tiver sementeação offline configurado e nenhum outro servidor estiver dependente da aplicação `AzureOfflineBackup_<Azure User Id>`, elimine esta aplicação. Selecione **portal Azure** > **registos**de aplicações de diretório ativo > **Azure.**

>[!NOTE]
> Verifique se a aplicação `AzureOfflineBackup_<Azure User Id>` não tem qualquer outra sementeção offline configurada e também se nenhum outro servidor estiver dependente desta aplicação. Vá a **Definições** > **Chaves** sob a secção **Chaves Públicas.** Não devia ter outras chaves públicas adicionadas. Consulte a seguinte imagem para referência.
>
>![Chaves públicas](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Passo 3

A partir do servidor está a tentar configurar para backup offline, execute as seguintes ações.

1. Vá ao **aplicativo de certificado** de computador Gerir > separador **pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`.

2. Selecione o certificado, clique à direita **em Todas as Tarefas**e selecione **Exportar** sem chave privada no formato .cer.

3. Vá ao aplicativo de backup offline Azure mencionado no passo 2. Selecione **Definições** > **teclas** > **carregar a chave pública**. Faça o upload do certificado que exportou no passo anterior.

    ![Carregar a chave pública](./media/offline-backup-azure-data-box/upload-public-key.png)

4. No servidor, abra o registo entrando em **regedite** na janela de execução.

5. Vá ao registo *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Clique no **CloudBackupProvider**e adicione um novo valor de cadeia com o nome `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Para obter o ID do utilizador Azure, execute uma destas ações:
    >
    >* A partir da PowerShell ligada ao Azure, execute o comando `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”`.
    > * Vá para a trajetória de registo *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup* com o nome *CurrentUserId*.

6. Clique na corda adicionada no passo anterior e selecione **Modificar**. No valor, forneça a impressão digital do certificado que exportou no passo 2. Selecione **OK**.

7. Para obter o valor da impressão digital, clique duas vezes no certificado. Selecione o separador **Detalhes** e desloque-se para baixo até ver o campo de impressão digital. Selecione **Impressão Digital**e copie o valor.

    ![Campo de impressão digital de certificado](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Perguntas

Para quaisquer questões ou esclarecimentos sobre quaisquer problemas que tenha enfrentado, contacte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com).
