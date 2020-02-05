---
title: Backup offline usando caixa de dados Azure
description: Saiba como pode usar a Caixa de Dados Azure para semear grandes dados iniciais de backup offline do Agente MARS para um Cofre de Serviços de Recuperação Azure.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027033"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Backup de backup offline Azure usando caixa de dados Azure

Pode utilizar o serviço [Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) para semear as suas grandes cópias de segurança iniciais do MARS offline (sem utilizar a rede) para um cofre dos Serviços de Recuperação Azure.  Isto poupa tempo e largura de banda da rede, que de outra forma seria consumida movendo grandes quantidades de dados de backup on-line sobre uma rede de alta latência. Esta melhoria está atualmente em pré-visualização. A Backup Offline da Azure Data Box fornece duas vantagens distintas sobre a cópia de segurança offline baseada em Serviços de [Importação/Exportação Azure](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Não é necessário adquirir os seus próprios discos e conectores compatíveis com o Azure. Serviço Azure Data Box envia os discos associados à Caixa de Dados selecionada [SKU](https://azure.microsoft.com/services/databox/data/)

2. A Backup Azure (Agente MARS) pode escrever diretamente dados de backup nas SKUs suportadas da Caixa de Dados Azure. Isto elimina a necessidade de fornecer um local de preparação para os seus dados de backup iniciais e a necessidade de os utilitários formatarem e copiarem esses dados nos discos.

## <a name="azure-data-box-with-mars-agent"></a>Caixa de dados Azure com agente MARS

Este artigo explica como pode usar a Caixa de Dados Azure para semear grandes dados de backup iniciais offline do Agente MARS para um Cofre de Serviços de Recuperação Azure. O artigo é dividido nas seguintes partes:

* Plataformas Suportadas
* Caixa de dados suportada SKUs
* Pré-requisitos
* Configuração mars agente
* Caixa de dados Azure configuração
* Transferência de dados de backup para caixa de dados Azure
* Passos pós-backup

## <a name="supported-platforms"></a>Plataformas Suportadas

O processo de semente de dados do Agente MARS utilizando a Caixa de Dados Azure é suportado nas seguintes SKUs do Windows:

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Posto de trabalho**                        |                                                              |
| Windows 10 64 bit                     | Enterprise, Pro, Home                                       |
| Windows 8.1 64 bit                    | Enterprise, Pro                                             |
| Windows 8 64 bit                      | Enterprise, Pro                                             |
| Windows 7 64 bit                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Servidor**                             |                                                              |
| Windows Server 2019 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2016 64 bit            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2 64 bit         | Standard, Datacenter, Fundação                            |
| Windows Server 2012 64 bit            | Datacenter, Fundação, Standard                            |
| Windows Storage Server 2016 64 bit    | Standard, Grupo de Trabalho                                         |
| Windows Storage Server 2012 R2 64 bit | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012 64 bit    | Standard, Grupo de Trabalho                                         |
| Windows Server 2008 R2 SP1 64 bit     | Standard, Enterprise, Datacenter, Fundação                |
| Windows Server 2008 SP2 64 bit        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Tamanho de dados de backup e SKUs de caixa de dados suportado

| Tamanho de dados de backup (compressão de post por MARS)* por servidor | Caixa de dados Azure Suportada SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7.2 TB                                                    | [Disco de caixa de dados azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7.2 TB e <= 80 TB**                                      | [Caixa de Dados Azure (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*As taxas típicas de compressão variam entre 10 a 20% <br>
** Contacte [aAskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) se espera ter mais de 80 TB de dados de backup iniciais para um único servidor MARS.

>[!IMPORTANT]
>Os dados iniciais de backup de um único servidor devem ser contidos numa única Caixa de Dados Azure ou no Disco de Caixa de Dados Azure e não podem ser partilhados entre vários dispositivos das mesmas SKUs ou diferentes. No entanto, um dispositivo Azure Data Box pode conter cópias de segurança iniciais de vários servidores.

## <a name="pre-requisites"></a>Pré-requisitos

### <a name="azure-subscription-and-required-permissions"></a>Assinatura Azure e permissões necessárias

* O processo requer uma subscrição do Azure
* O processo requer que o utilizador designado para executar a política de backup offline seja um "Proprietário" da Subscrição Azure
* O trabalho da Data Box e o Cofre de Serviços de Recuperação (para o qual os dados precisam de ser semeados) são obrigados a estar nas mesmas subscrições.
* Recomenda-se que a conta de armazenamento-alvo associada ao trabalho da Caixa de Dados Azure e ao Cofre de Serviços de Recuperação esteja na mesma região. No entanto, isto não é necessário.

### <a name="get-azure-powershell-370"></a>Obtenha Azure PowerShell 3.7.0

**Este é o pré-requisito mais importante para o processo.** Antes de instalar o Azure PowerShell (ver. 3.7.0), efetuar as seguintes verificações**

#### <a name="step-1-check-powershell-version"></a>Passo 1: Verificar a versão PowerShell

* Abra o Windows PowerShell e execute o seguinte comando:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Se a saída apresentar uma versão superior a **3.7.0,** execute o passo 2 abaixo. Caso contrário, avance para o passo 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Passo 2: Desinstalar a versão PowerShell

Desinstale a versão atual do PowerShell fazendo as seguintes ações:

* Retire os módulos dependentes executando o seguinte comando no PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Executar o seguinte comando para garantir a eliminação bem sucedida de todos os módulos dependentes:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Passo 3: Instalar a versão PowerShell 3.7.0

Depois de ter verificado que não existem módulos AzureRM, instale a versão 3.7.0 utilizando um dos seguintes métodos:

* Do GitHub, [link](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

OU

* Executar o seguinte comando na janela PowerShell:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

O Azure PowerShell também poderia ter sido instalado com um ficheiro MSI. Para removê-lo, desinstale-o utilizando a opção de desinstalar programas no Painel de Controlo.

### <a name="order-and-receive-the-data-box-device"></a>Encomende e receba o dispositivo Data Box

O processo de backup offline utilizando MARS e Azure Data Box requer que os dispositivos data box necessários estejam em estado "Delivered" antes de ativar a cópia de segurança offline utilizando o Agente MARS. Consulte o tamanho dos dados de [backup e as SKUs suportadas](#backup-data-size-and-supported-data-box-skus) para encomendar o SKU mais adequado para o seu requisito. Siga os passos [deste artigo](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) para encomendar e receba os seus dispositivos Data Box.

>[!IMPORTANT]
>Não selecione BlobStorage para o tipo Conta. O agente MARS requer uma conta que suporte as Blobs page que não é suportada quando o BlobStorage é selecionado. Aconselhamos fortemente a seleção do *Storage V2* *(propósito geral v2*) como o tipo de Conta ao criar a conta de armazenamento alvo para o seu trabalho na Caixa de Dados Azure.

![Escolha o tipo de conta em caso de detalhes](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>Instalar e Configurar o Agente MARS

* Certifique-se de que desinstala quaisquer instalações anteriores do Agente MARS.

* Faça o download do mais recente agente mars [daqui.](https://aka.ms/azurebackup_agent)

* Execute *MARSAgentInstaller.exe* e execute ***apenas** os passos para [instalar e registar o Agente](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) no Cofre de Serviços de Recuperação para o qual pretende que as suas cópias de segurança sejam armazenadas.

  >[!NOTE]
  > O Cofre de Serviços de Recuperação deve estar na mesma subscrição que o trabalho da Caixa de Dados Azure.

* Assim que o agente estiver registado no Cofre dos Serviços de Recuperação, siga os passos nas secções abaixo.

## <a name="setup-azure-data-box-devices"></a>Configuração do dispositivo caixa de dados Azure

Dependendo da Caixa de Dados Azure SKU que encomendou, execute os passos cobertos nas secções apropriadas abaixo para configurar e preparar o dispositivo(s) data Box para o Agente MARS identificar e transferir os dados de backup iniciais.

### <a name="setup-azure-data-box-disk"></a>Configuração do disco de caixa de dados azure

Se encomendou um ou mais Discos de Caixa de Dados Azure (até 8 TB cada), siga os passos aqui mencionados para [Desembalar, conecte e desbloqueie o disco](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up)da caixa de dados .

>[!NOTE]
>É possível que o servidor com o Agente MARS não tenha uma porta USB. Nessa situação pode ligar o disco de caixa de dados Do Azure a outro servidor/cliente e expor a raiz do dispositivo como uma partilha de rede.

### <a name="setup-azure-data-box"></a>Caixa de dados Azure configuração

Se encomendou uma Caixa de Dados Azure (até 100 TB), siga os passos aqui mencionados [para configurar a sua Caixa](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up)de Dados .

#### <a name="mount-your-azure-data-box-as-local-system"></a>Monte a sua Caixa de Dados Azure como Sistema Local

O Agente MARS opera no contexto do Sistema Local e, por isso, requer o mesmo nível de privilégio a ser fornecido ao caminho de montagem onde a Caixa de Dados Azure está ligada. Siga os passos abaixo para garantir que pode montar o seu dispositivo Data Box como Sistema Local utilizando o Protocolo NFS:

* Ative a funcionalidade Cliente para NFS no Servidor windows (que tem o agente MARS instalado).<br>
  Especifique fonte alternativa: *WIM:D:\\Source\Install.wim:4*

* Baixe o PSExec de <https://download.sysinternals.com/files/PSTools.zip> para o servidor com o Agente MARS instalado.

* Abra um pedido de comando elevado e execute o seguinte comando com o diretório contendo PSExec.exe como o atual diretório:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* A janela de comando que se abre como resultado do comando acima está no contexto do Sistema Local. Utilize esta janela de comando para executar os passos para montar o Azure Page Blob Share como uma unidade de rede no seu Servidor Windows.

* Siga os passos [aqui](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) para ligar o seu servidor ao dispositivo Data Box via NFS e execute o seguinte comando no comando do Sistema Local para montar a partilha de Blobs da Página Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* Uma vez montado, verifique se pode aceder a X: a partir do seu servidor. Se sim, continue com a próxima parte deste artigo.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Transferir dados iniciais de backup para dispositivos da Caixa de Dados Azure

* Abra a aplicação **Microsoft Azure Backup** no seu servidor.

* Clique em **Agendar Backup** no painel **Ações.**

    ![Clique na cópia de segurança do calendário](./media/offline-backup-azure-data-box/schedule-backup.png)

* Siga os passos no **Assistente de Reserva de Agenda**

* **Adicione itens** de tal forma que o tamanho total dos itens está dentro dos limites de [tamanho suportados pelo Azure Data Box SKU](#backup-data-size-and-supported-data-box-skus) que encomendou e recebeu.

    ![Adicione itens à cópia de segurança](./media/offline-backup-azure-data-box/add-items.png)

* Selecione a política de retenção e agenda de reserva apropriada para Ficheiros e Pastas e Estado de Sistema (o estado do sistema é aplicável apenas para servidores windows e não para clientes windows)

* No ecrã 'Type' inicial de **backup (Ficheiros e Pastas)** do assistente, escolha a opção Transferência utilizando discos da Caixa de **Dados Microsoft Azure** e clique em **Seguinte**

    ![Escolha o tipo inicial de backup](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Inscreva-se no Azure quando solicitado usando as credenciais de utilizador que têm acesso ao proprietário na Subscrição Azure. Depois de conseguir fazê-lo, deve ver um ecrã que se assemelha ao abaixo:

    ![Criação de recursos e aplicação de permissões necessárias](./media/offline-backup-azure-data-box/creating-resources.png)

* O Agente MARS irá então buscar os trabalhos da Data Box na subscrição que se encontram em estado "Entregue".

    ![Buscar empregos de caixa de dados para ID de subscrição](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Selecione a correta ordem da caixa de dados para a qual desfez, ligou e desbloqueou o disco data Box. Clique em **Seguinte**.

    ![Selecione ordem s caixa de dados](./media/offline-backup-azure-data-box/select-databox-order.png)

* Clique no **Dispositivo de Deteção** no ecrã de deteção de dispositivos de caixa de **dados.** Isto faz com que o Agente MARS digitalize os discos da Caixa de Dados Azure anexados localmente e detete-os como mostrado abaixo:

    ![Deteção de dispositivos de caixa de dados](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Se tiver ligado a Caixa de Dados Azure como uma Partilha de Rede (devido à indisponibilidade de portas USB ou porque encomendou e montou o dispositivo 100 TB Data Box), a deteção falhará no início, mas dar-lhe-á a opção de introduzir a rota de rede para o dispositivo Data Box como s Uime abaixo:

    ![Insira o caminho da rede](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Forneça o caminho de rede para o diretório raiz do disco Caixa de Dados Azure. Este diretório deve conter um diretório com o nome *PageBlob,* como mostrado abaixo:
    >
    >![Diretório raiz do disco Da Caixa de Dados Azure](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Por exemplo, se o caminho do disco for `\\mydomain\myserver\disk1\` e *o disco1* contiver um diretório chamado *PageBlob,* o caminho a ser fornecido no assistente do agente MARS é `\\mydomain\myserver\disk1\`
    >
    >Se [configurar um dispositivo Azure Data Box 100 TB,](#setup-azure-data-box)forneça o seguinte como o caminho de rede para o dispositivo `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Clique em **Seguinte** e clique em **Terminar** no ecrã seguinte para guardar a política de backup e retenção com a configuração de backup offline utilizando a Caixa de Dados Azure.

* O ecrã seguinte confirma que a apólice é guardada com sucesso:

    ![A política é salva com sucesso](./media/offline-backup-azure-data-box/policy-saved.png)

* Clique em **Fechar** no ecrã acima.

* Clique em ***Back Up Now** na painel de **ações** da consola do Agente MARS e clique em **Back Up** no ecrã do assistente, como mostrado abaixo:

    ![Back Up Now Wizard](./media/offline-backup-azure-data-box/backup-now.png)

* O Agente MARS começará a fazer o backup dos dados selecionados para o dispositivo Azure Data Box. Isto pode demorar de várias horas a alguns dias dependendo do número de ficheiros e velocidade de ligação entre o servidor com o Agente MARS e o Disco de Caixa de Dados Azure.

* Uma vez concluída a cópia de segurança dos dados, verá um ecrã no Agente MARS que se assemelha ao abaixo:

    ![Progresso de backup mostrado](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Passos pós-backup

Esta secção explica os passos a tomar assim que a cópia de segurança dos dados para o Disco de Caixa de Dados Azure for bem sucedida.

* Siga os passos deste artigo para [enviar o disco Azure Data Box para o Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Se utilizou um dispositivo Azure Data Box 100-TB, siga estes passos para enviar a Caixa de [Dados Azure para O Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Monitorize o trabalho da Caixa](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) de Dados no portal Azure. Uma vez que o trabalho da Caixa de Dados Azure esteja "Completo", o Agente MARS transferirá automaticamente os dados da Conta de Armazenamento para o Cofre de Serviços de Recuperação no momento da próxima cópia de segurança programada. Marcará então o trabalho de backup como "Job Completed" se for criado um ponto de recuperação com sucesso.

    >[!NOTE]
    >O Agente MARS irá desencadear backups nos horários programados durante a criação de políticas. No entanto, estes trabalhos vão sinalizar "À espera que o trabalho da Caixa de Dados Azure esteja concluído" até ao momento em que o trabalho esteja concluído.

* Depois de o Agente MARS criar com sucesso um ponto de recuperação correspondente à cópia de segurança inicial, poderá eliminar a Conta de Armazenamento (ou conteúdo específico) associada ao trabalho da Caixa de Dados Azure.

## <a name="troubleshooting"></a>Resolução de problemas

O agente Microsoft Azure Backup (MAB) cria uma aplicação Azure AD para si no seu inquilino. Esta aplicação requer um certificado de autenticação que seja criado e carregado ao configurar a política de sementeira offline. Utilizamos o Azure PowerShell para criar e carregar o certificado para a Aplicação AD Azure.

### <a name="issue"></a>Problema

No momento da configuração da cópia de segurança offline poderá enfrentar um problema, em que devido a um bug no cmdlet Azure PowerShell não pode adicionar vários certificados à mesma Aplicação AD Azure criada pelo agente MAB. Isto irá impactá-lo se tiver configurado a política de sementeing offline para o mesmo ou um servidor diferente.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Como verificar se o problema é causado por esta causa raiz específica

Para garantir que a falha se deve à questão acima referida, execute uma das seguintes etapas:

#### <a name="step-1"></a>Passo 1

Verifique se vê a seguinte mensagem de erro na consola MAB no momento da configuração da cópia de segurança offline:

![Incapaz de criar a política de backup offline para a conta atual do Azure](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Passo 2

* Abra a pasta **Temp** na trajetória de instalação (o caminho da pasta temporária padrão é *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Procure o ficheiro **CBUICurr** e abra o ficheiro.

* No ficheiro **CBUICurr,** percorra até à última linha e verifique se a falha se deve a `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Solução

Como um trabalho para resolver este problema, execute os seguintes passos e retente a configuração da política.

#### <a name="first-step"></a>Primeiro passo

Inscreva-se na PowerShell que aparece na UI MAB utilizando uma conta diferente com acesso administrativo na subscrição que terá o emprego de exportação de importação criado.

#### <a name="second-step"></a>Segundo passo

Se nenhum outro servidor tiver sementeção offline configurado e nenhum outro servidor estiver dependente da aplicação `AzureOfflineBackup_<Azure User Id>`, então elimine esta aplicação do **portal Azure** > registos **de Diretório Ativo** > **App.**

>[!NOTE]
> Verifique se a aplicação `AzureOfflineBackup_<Azure User Id>` não tem qualquer outra sementeção offline configurada e também nenhum outro servidor está dependente desta aplicação. Vá a **Definições** > **Chaves** sob a secção **Chaves Públicas** não deve ter outras chaves públicas adicionadas. Consulte a seguinte imagem para referência:
>
>![Chaves Públicas](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Terceiro passo

A partir do servidor está a tentar configurar a cópia de segurança offline, realizar as seguintes ações:

1. Abra a **aplicação de certificado** de computador > separador **pessoal** e procure o certificado com o nome `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Selecione o certificado acima, clique à direita **em Todas as Tarefas** e **Exportação** sem chave privada, no formato .cer.

3. Vá à aplicação de backup offline Azure mencionada no **ponto 2**. Nas **Definições** > **Teclas** > **carregar chave pública,** faça upload do certificado exportado no passo acima.

    ![Carregar chave pública](./media/offline-backup-azure-data-box/upload-public-key.png)

4. No servidor, abra o registo digitando **regedite** na janela de execução.

5. Vá ao registo *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Clique à direita no **CloudBackupProvider** e adicione um novo valor de cadeia com o nome `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Para obter o Id do Utilizador Azure realizar uma das seguintes ações:
    >
    >1. A partir do Comando PowerShell ligado ao Azure, executa o comando `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”`.
    > 2. Navegue para a trajetória de registo: *Computador\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup*; Nome: *CurrentUserId*

6. Clique à direita na corda adicionada no degrau acima e selecione **Modificar**. No valor, forneça a impressão digital do certificado que exportou no **ponto 2** e **clique**OK .

7. Para obter o valor da impressão digital, clique duas vezes no certificado, em seguida, selecione o separador **Detalhes** e desloque-se para baixo até ver o campo de impressão digital. Clique na impressão digital e **copie** o valor.

    ![Campo de impressão digital de certificado](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Dúvidas

Para quaisquer questões ou esclarecimentos, sobre quaisquer questões que se colocam, contacte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
