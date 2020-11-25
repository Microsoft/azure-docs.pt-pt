---
title: Gerir o servidor de configuração para servidores físicos na Recuperação do Site Azure
description: Este artigo descreve como gerir o servidor de configuração de recuperação do site Azure para a recuperação de desastres do servidor físico para Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: ff612b7c052ead5658ea4bbfafd7aace51ba3c02
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/25/2020
ms.locfileid: "96017445"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Gerir o servidor de configuração para recuperação de desastres de servidor físico

Configura um servidor de configuração no local quando utiliza o serviço de Recuperação do [Local Azure](site-recovery-overview.md) para a recuperação de desastres de servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre máquinas no local e Azure, e gere a replicação de dados. Este artigo resume tarefas comuns para gerir o servidor de configuração depois de ter sido implementado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

A tabela resume os pré-requisitos para a implantação da máquina de servidor de configuração no local.

| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 16 GB|
| Número de discos | 3, incluindo o disco de so, o disco de cache do servidor de processo e a unidade de retenção para falha |
| Espaço livre no disco (cache do servidor de processos) | 600 GB
| Espaço livre no disco (disco de retenção) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Região do sistema operativo | Inglês (E.U.A.)|
| Versão do VMware vSphere PowerCLI | não é necessário|
| Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V |
| Políticas de grupo| Não ative estas políticas de grupo: <br> - Impedir o acesso à solicitação de comando <br> - Impedir o acesso a ferramentas de edição de registos <br> - Lógica de confiança para anexos de ficheiros <br> - Ligue a execução do guião <br> [Saiba mais](/previous-versions/windows/it-pro/windows-7/gg176671(v=ws.10))|
| IIS | - Nenhum website pre-existente <br> - Ativar  [a autenticação anónima](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc731244(v=ws.10)) <br> - Ativar a definição [fastCGI](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/cc753077(v=ws.10))  <br> - Nenhum website/aplicação pré-existente na porta 443<br>|
| Tipo NIC | VMXNET3 (quando implementado como VMware VM) |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor precisa de acesso a estes URLs: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (não é necessário para servidores de processos de escala) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)|

## <a name="download-the-latest-installation-file"></a>Descarregue o mais recente ficheiro de instalação

A versão mais recente do ficheiro de instalação do servidor de configuração está disponível no portal de Recuperação do Site. Além disso, pode ser descarregado diretamente a partir do [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Inicie sessão no portal Azure e navegue no cofre dos Serviços de Recuperação.
2. Navegue para servidores de configuração **da infraestrutura de**  >  **recuperação do local** (em VMware & Máquinas Físicas).
3. Clique no botão **+Servidores.**
4. Na página **'Adicionar Servidor',** clique no botão Descarregar para descarregar a tecla 'Registar'. Necessita desta chave durante a instalação do Servidor de Configuração para registá-la com o serviço de Recuperação do Site Azure.
5. Clique no **Link de Configuração Unificada de Recuperação do Site microsoft Azure** para descarregar a versão mais recente do Servidor de Configuração.

   ![Página de descarregamento](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalar e registar o servidor

1. Execute o ficheiro de instalação Configuração Unificada.
2. Antes **de começar**, selecione **Instale o servidor de configuração e o servidor de processo**.

    ![Antes de começar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Em **Licença de Software de Terceiros**, clique em **Aceito**, para transferir e instalar o MySQL.
4. Em **Definições da Internet**, especifique a forma como o Fornecedor em execução no servidor estabelece ligação ao Azure Site Recovery através da Internet. Certifique-se de ter permitido os URLs necessários.

    - Se pretender ligar-se ao proxy que está atualmente configurado na máquina, selecione **Connect to Azure Site Recovery utilizando um servidor proxy**.
    - Se pretender que o Fornecedor se conecte diretamente, selecione **Connect diretamente para a Recuperação do Site Azure sem um servidor proxy**.
    - Se o representante existente necessitar de autenticação, ou se pretender utilizar um representante personalizado para a ligação do Fornecedor, selecione **Connect com configurações de procuração personalizadas** e especifique o endereço, porta e credenciais.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Em **Verificação de Pré-requisitos**, a Configuração executa uma verificação para se certificar de que a instalação pode ser executada. Se for apresentado um aviso sobre a **Verificação de sincronização de hora global**, certifique-se de que a hora no relógio do sistema (definições de **Data e Hora**) é a mesma que o fuso horário.

    ![Pré-requisitos](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Em **Configuração do MySQL**, crie as credenciais para iniciar sessão na instância de servidor do MySQL que está instalada.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Se estiver, então a Configuração verifique se o PowerCLI 6.0 está instalado.
9. Em **Localização de Instalação**, selecione onde pretende instalar os binários e armazenar a cache. A unidade que selecionar tem de ter, pelo menos, 5 GB de espaço disponível no disco, mas recomenda-se uma unidade de cache com, pelo menos, 600 GB de espaço livre.

    ![Localização de instalação](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Na **Seleção de Rede**, selecione primeiro o NIC que o servidor de processo incorporado utiliza para a descoberta e a instalação de serviço de mobilidade em máquinas de origem e, em seguida, selecione o NIC que o Servidor de Configuração utiliza para conectividade com o Azure. A porta 9443 é a porta predefinida utilizada para envio e receção de tráfego de replicação, mas pode modificar este número de porta para melhor corresponda às necessidades do seu ambiente. Além da porta 9443, podemos também abrir a porta 443, que é utilizada por um servidor Web para orquestrar operações de replicação. Não utilize a porta 443 para enviar ou receber tráfego de replicação.

    ![Seleção de rede](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Em **Resumo**, consulte as informações e clique em **Instalar**. Quando a instalação estiver concluída, é gerada uma frase de acesso. Irá precisar dela, quando ativar a replicação, por isso, copie-a e mantenha-a numa localização segura.


Após o início do registo, o servidor é apresentado na lâmina **'Servidores' de Definições**  >  **Servers** no cofre.


## <a name="install-from-the-command-line"></a>Instalação a partir da linha de comando

Executar o ficheiro de instalação da seguinte forma:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Utilização de amostras
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parâmetros

|Nome do Parâmetro| Tipo | Descrição| Valores|
|-|-|-|-|
| /ServerMode|Necessário|Especifica se a configuração e os servidores de processos devem ser instalados, ou apenas o servidor de processos|CS<br>PS|
|/InstallLocation|Necessário|A pasta na qual os componentes são instalados| Qualquer pasta no computador|
|/MySQLCredsFilePath|Necessário|O caminho do ficheiro no qual as credenciais do servidor MySQL são armazenadas|O ficheiro deve estar no formato especificado abaixo|
|/VaultCredsFilePath|Necessário|O caminho do ficheiro de credenciais do cofre|Caminho do ficheiro válido|
|/EnvType|Necessário|Tipo de ambiente que quer proteger |VMware<br>NonVMware|
|/PSIP|Necessário|Endereço IP do NIC a ser utilizado para transferência de dados de replicação| Qualquer endereço IP válido|
|/CSIP|Necessário|O endereço IP do NIC em que o servidor de configuração está a ouvir| Qualquer endereço IP válido|
|/PassphraseFilePath|Necessário|O caminho completo para a localização do ficheiro da frase de acesso|Caminho do ficheiro válido|
|/BypassProxy|Opcional|Especifica que o servidor de configuração estabelece uma ligação ao Azure sem um proxy|Para obter este valor do Venu|
|/ProxySettingsFilePath|Opcional|Definições de proxy (o proxy predefinido necessita de autenticação ou de um proxy personalizado)|O ficheiro deve estar no formato especificado abaixo|
|DataTransferSecurePort|Opcional|Número da porta no PSIP a ser utilizado para dados de replicação| Número de Porta Válido (o valor predefinido é 9433)|
|/SkipSpaceCheck|Opcional|Ignorar a verificação de espaços para a cache do disco| |
|/AcceptThirdpartyEULA|Necessário|O sinalizador indica a aceitação do EULA de terceiros| |
|/ShowThirdpartyEULA|Opcional|Apresenta o EULA de terceiros. Se for fornecido como entrada, todos os outros parâmetros são ignorados| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Criar entrada de ficheiros para MYSQLCredsFilePath

O parâmetro MySQLCredsFilePath toma um ficheiro como entrada. Crie o ficheiro utilizando o seguinte formato e passe-o como parâmetro MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Criar entrada de ficheiro para ProxySettingsFilePath
O parâmetro ProxySettingsFilePath toma um ficheiro como entrada. Crie o ficheiro utilizando o seguinte formato e passe-o como parâmetro ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modificar as definições de procuração

Pode modificar as definições de procuração para a máquina do servidor de configuração da seguinte forma:

1. Inicie sessão no servidor de configuração.
2. Lance o cspsconfigtool.exe utilizando o atalho no seu ambiente de trabalho.
3. Clique no **separador Registo** do Cofre.
4. Descarregue um novo ficheiro de registo de cofre a partir do portal e forneça-o como entrada para a ferramenta.

   ![registo-configuração-servidor](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os novos detalhes do proxy e clique no botão **Registar.**
6. Abra uma janela de comando Admin PowerShell.
7. Execute o seguinte comando:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Se tiver servidores de processo adicionais ligados ao servidor de configuração, tem de [corrigir as definições de procuração em todos os servidores de processo de escala](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) na sua implementação.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Reregisterar um servidor de configuração com o mesmo cofre
1. Inicie sessão no seu Servidor de Configuração.
2. Lance o cspsconfigtool.exe utilizando o atalho no seu ambiente de trabalho.
3. Clique no **separador Registo** do Cofre.
4. Faça o download de um novo ficheiro de registo a partir do portal e forneça-o como entrada para a ferramenta.
      ![registo-configuração-servidor](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os detalhes do Servidor Proxy e clique no botão **Registar.**  
6. Abra uma janela de comando Admin PowerShell.
7. Execute o seguinte comando

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Se tiver vários servidores de processo, tem de [os voltar](vmware-azure-manage-process-server.md#reregister-a-process-server)a registar .

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registar um servidor de configuração com um cofre diferente

> [!WARNING]
> O passo seguinte desassocia o servidor de configuração do cofre atual, e a replicação de todas as máquinas virtuais protegidas sob o servidor de configuração é interrompida.

1. Inicie sessão no servidor de configuração
2. a partir de um pedido de comando administrativo, executar o comando:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Lance o cspsconfigtool.exe utilizando o atalho no seu ambiente de trabalho.
4. Clique no **separador Registo** do Cofre.
5. Faça o download de um novo ficheiro de registo a partir do portal e forneça-o como entrada para a ferramenta.
6. Forneça os detalhes do Servidor Proxy e clique no botão **Registar.**  
7. Abra uma janela de comando Admin PowerShell.
8. Execute o seguinte comando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Atualizar um servidor de configuração

Execute rollups de atualização para atualizar o servidor de configuração. As atualizações podem ser aplicadas até versões N-4. Por exemplo:

- Se estiver a correr 9.7, 9.8, 9.9 ou 9.10 - pode fazer upgrade diretamente para 9.11.
- Se estiver a correr 9.6 ou mais cedo, e quiser fazer upgrade para 9.11, tem primeiro de atualizar para a versão 9.7. antes das 9:11.

As ligações para atualizações de rollups para atualização para todas as versões do servidor de configuração estão disponíveis na [página de atualizações](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx)wiki .

Atualize o servidor da seguinte forma:

1. Descarregue o ficheiro do instalador de atualização para o servidor de configuração.
2. Clique duas vezes para executar o instalador.
3. O instalador deteta a versão atual em funcionamento na máquina.
4. Clique **em OK** para confirmar e execute a atualização. 


## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou não registar um servidor de configuração

> [!WARNING]
> Certifique-se de que o seguinte é antes de começar a desativar o seu Servidor de Configuração.
> 1. [Desative a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as máquinas virtuais sob este Servidor de Configuração.
> 2. [Desassociem](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [eliminem](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do Servidor de Configuração.
> 3. [Elimine](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores/vSphere dos vCenters que estejam associados ao Servidor de Configuração.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Eliminar o Servidor de Configuração do portal Azure
1. No portal Azure, navegue pelos Servidores de Configuração **da Infraestrutura de**  >  **Recuperação** do Local a partir do menu Vault.
2. Clique no servidor de configuração que pretende desativar.
3. Na página de detalhes do Servidor de Configuração, clique no botão **Eliminar.**
4. Clique **em Sim** para confirmar a eliminação do servidor.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Desinstalar o servidor de configuração e as suas dependências
> [!TIP]
>   Se planeia reutilizar o Servidor de Configuração com recuperação do site Azure novamente, então pode saltar para o passo 4 diretamente

1. Inicie sessão no Servidor de Configuração como Administrador.
2. Abrir programa de > do Painel de Controlo > Desinstalar Programas
3. Desinstalar os programas na seguinte sequência:
   * Agente de serviços de recuperação da Microsoft Azure
   * Microsoft Azure Site Recovery Mobility Service/Master Target server
   * Provedor de recuperação do site microsoft Azure
   * Servidor de configuração de recuperação do site do Microsoft Azure
   * Dependências do servidor de configuração de recuperação do site do Microsoft Azure
   * Servidor MySQL 5.5
4. Executar o seguinte comando e pedido de comando de administração.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Excluir ou não registar um servidor de configuração (PowerShell)

1. [Instalar](/powershell/azure/install-Az-ps) Módulo Azure PowerShell
2. Inicie sessão na sua conta Azure utilizando o comando
    
    `Connect-AzAccount`
3. Selecione a subscrição sob a qual o cofre está presente

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Agora crie o seu contexto de abóbada
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Selecione o seu servidor de configuração

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminar o Servidor de Configuração

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> A opção **-Força** no Remove-AzSiteRecoveryFabric pode ser usada para forçar a remoção/eliminação do servidor de Configuração.

## <a name="renew-tlsssl-certificates"></a>Renovar certificados TLS/SSL
O servidor de configuração tem um servidor web incorporado, que orquestra atividades do serviço mobility, servidores de processo e servidores-alvo principais ligados a ele. O servidor web utiliza um certificado TLS/SSL para autenticar clientes. O certificado expira após três anos, e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verifique a expiração

Para implementações de servidores de configuração antes de maio de 2016, a expiração do certificado foi fixada para um ano. Se tiver um certificado expirará, ocorre o seguinte:

- Quando a data de validade é de dois meses ou menos, o serviço começa a enviar notificações no portal, e por e-mail (se subscrever as notificações de Recuperação do Site Azure).
- Um banner de notificação aparece na página de recursos do cofre. Clique no banner para mais detalhes.
- Se vir um botão **Upgrade Now,** isto indica que existem alguns componentes no seu ambiente que não foram atualizados para versões 9.4.xxxx.x ou mais altas. Atualize os componentes antes de renovar o certificado. Não se pode renovar em versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra o Servidor de Configuração **da Infraestrutura de**  >  **Recuperação** do Local e clique no servidor de configuração necessário.
2. A data de validade aparece na **saúde do Servidor de Configuração**
3. Clique **em Renovar Certificados.** 




## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para configurar a recuperação de desastres de [servidores físicos](./physical-azure-disaster-recovery.md) para Azure.
