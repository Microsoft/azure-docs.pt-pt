---
title: Gerir o servidor de configuração para servidores físicos na Recuperação do Site Azure
description: Este artigo descreve como gerir o servidor de configuração de recuperação de sites do Azure para recuperação de desastres de servidor físico para o Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: eb7e891c031be5ac01295905d5c3304dc6818737
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478977"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Gerir o servidor de configuração para recuperação de desastres de servidor físico

Configura um servidor de configuração no local quando utilizar o serviço de recuperação de [sites Azure](site-recovery-overview.md) para a recuperação de desastres de servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre as máquinas no local e o Azure, e gere a replicação de dados. Este artigo resume tarefas comuns para gerir o servidor de configuração depois de implementado.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

A tabela resume os pré-requisitos para a implementação da máquina de servidor de configuração no local.

| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 16 GB|
| Número de discos | 3, incluindo o disco OS, o disco de cache do servidor de processo e a unidade de retenção para o failback |
| Espaço livre no disco (cache do servidor de processos) | 600 GB
| Espaço livre no disco (disco de retenção) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Região do sistema operativo | Inglês (E.U.A.)|
| Versão do VMware vSphere PowerCLI | Não é necessária|
| Funções do Windows Server | Não ative estes papéis: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V |
| Políticas de grupo| Não permita estas políticas de grupo: <br> - Impedir o acesso ao pedido de comando <br> - Impedir o acesso a ferramentas de edição de registos <br> - Lógica de confiança para anexos de ficheiros <br> - Ativar a execução do guião <br> [Mais informações](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | - Nenhum website preexistente <br> - Ativar [a autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> - Ativar a definição [de FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> - Nenhum site/aplicação pré-existente ouvindo na porta 443<br>|
| Tipo NIC | VMXNET3 (quando implantado como VMware VM) |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor precisa de acesso a estes URLs: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - `https://management.azure.com` <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi(não necessário para servidores de processos scale-out) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)|

## <a name="download-the-latest-installation-file"></a>Descarregue o mais recente ficheiro de instalação

A versão mais recente do ficheiro de instalação do servidor de configuração está disponível no portal recovery do site. Além disso, pode ser descarregado diretamente do [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Inicie sessão no portal Azure e navegue até ao seu Cofre de Serviços de Recuperação.
2. Navegue nos servidores de**configuração** da **infraestrutura** > de recuperação do site (em VMware & máquinas físicas).
3. Clique no botão **+Servers.**
4. Na página **Add Server,** clique no botão Descarregamento para descarregar a tecla Registration. Precisa desta chave durante a instalação do Servidor de Configuração para registrá-la com o serviço de recuperação do site Azure.
5. Clique no **descarregue o link de configuração unificado** de recuperação do site do Microsoft Azure para descarregar a versão mais recente do Servidor de Configuração.

   ![Página de descarregamento](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalar e registar o servidor

1. Execute o ficheiro de instalação Configuração Unificada.
2. Antes **de começar,** selecione **Instale o servidor de configuração e processe**o servidor .

    ![Antes de começar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Em **Licença de Software de Terceiros**, clique em **Aceito**, para transferir e instalar o MySQL.
4. Em **Definições da Internet**, especifique a forma como o Fornecedor em execução no servidor estabelece ligação ao Azure Site Recovery através da Internet. Certifique-se de ter permitido os URLs necessários.

    - Se pretender ligar-se ao proxy atualmente configurado na máquina, selecione **Connect to Azure Site Recovery utilizando um servidor proxy**.
    - Se pretender que o Fornecedor se conectem diretamente, selecione **Connect diretamente para a Recuperação do Site Azure sem um servidor proxy**.
    - Se o representante existente necessitar de autenticação, ou se pretender utilizar um proxy personalizado para a ligação do Fornecedor, selecione Connect com definições de **procuração personalizadas**e especifique o endereço, a porta e as credenciais.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Em **Verificação de Pré-requisitos**, a Configuração executa uma verificação para se certificar de que a instalação pode ser executada. Se for apresentado um aviso sobre a **Verificação de sincronização de hora global**, certifique-se de que a hora no relógio do sistema (definições de **Data e Hora**) é a mesma que o fuso horário.

    ![Pré-requisitos](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Em **Configuração do MySQL**, crie as credenciais para iniciar sessão na instância de servidor do MySQL que está instalada.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Se estiver, então a configuração verifica se o PowerCLI 6.0 está instalado.
9. Em **Localização de Instalação**, selecione onde pretende instalar os binários e armazenar a cache. A unidade que selecionar tem de ter, pelo menos, 5 GB de espaço disponível no disco, mas recomenda-se uma unidade de cache com, pelo menos, 600 GB de espaço livre.

    ![Localização de instalação](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Na **Seleção de Rede,** primeiro selecione o NIC que o servidor de processo incorporado utiliza para a instalação de serviço de mobilidade em máquinas de origem e, em seguida, selecione o NIC que o Servidor de Configuração utiliza para a conectividade com o Azure. A porta 9443 é a porta predefinida utilizada para envio e receção de tráfego de replicação, mas pode modificar este número de porta para melhor corresponda às necessidades do seu ambiente. Além da porta 9443, podemos também abrir a porta 443, que é utilizada por um servidor Web para orquestrar operações de replicação. Não utilize a porta 443 para o envio ou receção do tráfego de replicação.

    ![Seleção de rede](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Em **Resumo**, consulte as informações e clique em **Instalar**. Quando a instalação estiver concluída, é gerada uma frase de acesso. Irá precisar dela, quando ativar a replicação, por isso, copie-a e mantenha-a numa localização segura.


Após o registo terminar, o servidor é apresentado na lâmina **'Servidores de Definições'** > **Servers** no cofre.


## <a name="install-from-the-command-line"></a>Instalar a partir da linha de comando

Executar o ficheiro de instalação da seguinte forma:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Utilização da amostra
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
|/EnvType|Necessário|Tipo de ambiente que pretende proteger |VMware<br>NonVMware|
|/PSIP|Necessário|Endereço IP do NIC a ser utilizado para transferência de dados de replicação| Qualquer endereço IP válido|
|/CSIP|Necessário|O endereço IP do NIC em que o servidor de configuração está a ouvir| Qualquer endereço IP válido|
|/PassphraseFilePath|Necessário|O caminho completo para a localização do ficheiro da frase de acesso|Caminho do ficheiro válido|
|/BypassProxy|Opcional|Especifica que o servidor de configuração estabelece uma ligação ao Azure sem um proxy|Para obter este valor do Venu|
|/ProxySettingsFilePath|Opcional|Definições de proxy (o proxy predefinido necessita de autenticação ou de um proxy personalizado)|O ficheiro deve estar no formato especificado abaixo|
|DataTransferSecurePort|Opcional|Número da porta no PSIP a ser utilizado para dados de replicação| Número de Porta Válido (o valor predefinido é 9433)|
|/SkipSpaceCheck|Opcional|Ignorar a verificação de espaços para a cache do disco| |
|/AcceptThirdpartyEULA|Necessário|O sinalizador indica a aceitação do EULA de terceiros| |
|/ShowThirdpartyEULA|Opcional|Apresenta o EULA de terceiros. Se for fornecido como entrada, todos os outros parâmetros são ignorados| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Criar entrada de ficheiro para MYSQLCredsFilePath

O parâmetro MySQLCredsFilePath tem um ficheiro como entrada. Crie o ficheiro utilizando o seguinte formato e passe-o como parâmetro De entrada MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Criar entrada de ficheiro para ProxySettingsFilePath
O parâmetro ProxySettingsFilePath tem um ficheiro como entrada. Crie o ficheiro utilizando o seguinte formato e passe-o como parâmetro ProxySettingsFilePath de entrada.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modificar as definições de procuração

Pode modificar as definições de proxy para a máquina de servidor de configuração da seguinte forma:

1. Inicie sessão no servidor de configuração.
2. Lance o cspsconfigtool.exe utilizando o atalho no seu ambiente de trabalho.
3. Clique no separador registo do **cofre.**
4. Descarregue um novo ficheiro de registo do cofre a partir do portal e forneça-o como entrada para a ferramenta.

   ![registro-configuração-servidor](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os novos detalhes da procuração e clique no botão **Registar.**
6. Abra uma janela de comando Da Admin PowerShell.
7. Execute o seguinte comando:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Se tiver servidores de processo adicionais ligados ao servidor de configuração, tem de [corrigir as definições de procuração em todos os servidores de processo sem escala](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) na sua implementação.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Reregistre um servidor de configuração com o mesmo cofre
1. Inicie sessão no seu Servidor de Configuração.
2. Lance o cspsconfigtool.exe utilizando o atalho no seu ambiente de trabalho.
3. Clique no separador registo do **cofre.**
4. Descarregue um novo ficheiro de registo a partir do portal e forneça-o como entrada para a ferramenta.
      ![registro-configuração-servidor](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os detalhes do Proxy Server e clique no botão **Registar.**  
6. Abra uma janela de comando Da Admin PowerShell.
7. Execute o seguinte comando

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Se tiver um servidor de processos múltiplos, tem de voltar a [registá-los](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registe um servidor de configuração com um cofre diferente

> [!WARNING]
> O passo seguinte dissocia o servidor de configuração do cofre atual, e a replicação de todas as máquinas virtuais protegidas sob o servidor de configuração é interrompida.

1. Iniciar sessão no servidor de configuração
2. a partir de um pedido de comando administrativo, executar o comando:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Lance o cspsconfigtool.exe utilizando o atalho no seu ambiente de trabalho.
4. Clique no separador registo do **cofre.**
5. Descarregue um novo ficheiro de registo a partir do portal e forneça-o como entrada para a ferramenta.
6. Forneça os detalhes do Proxy Server e clique no botão **Registar.**  
7. Abra uma janela de comando Da Admin PowerShell.
8. Execute o seguinte comando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Atualizar um servidor de configuração

Executa os rollups de atualização para atualizar o servidor de configuração. As atualizações podem ser aplicadas até versões N-4. Por exemplo:

- Se estiver a correr 9.7, 9.8, 9.9 ou 9.10 - pode fazer upgrade diretamente para 9.11.
- Se estiver a executar 9.6 ou mais cedo, e quiser fazer upgrade para 9.11, tem primeiro de atualizar para a versão 9.7. antes das 9:11.

As ligações para atualizar os rollups para atualização para todas as versões do servidor de configuração estão disponíveis na página de [atualizações wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Atualize o servidor da seguinte forma:

1. Descarregue o ficheiro do instalador de atualizações para o servidor de configuração.
2. Clique duas vezes para executar o instalador.
3. O instalador deteta a versão atual a funcionar na máquina.
4. Clique em **OK** para confirmar e executar a atualização. 


## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou desmarcar um servidor de configuração

> [!WARNING]
> Certifique-se de que o seguinte é antes de começar a desativar o seu Servidor de Configuração.
> 1. [Desative a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) de todas as máquinas virtuais sob este Servidor de Configuração.
> 2. [Desassociatee](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [elimine](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do Servidor de Configuração.
> 3. [Elimine](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores/anfitriões vSphere que estejam associados ao Servidor de Configuração.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Eliminar o Servidor de Configuração do portal Azure
1. No portal Azure, navegue pelos servidores de > **configuração** da infraestrutura de **recuperação**do site a partir do menu Vault.
2. Clique no servidor de configuração que pretende desativar.
3. Na página de detalhes do Servidor de Configuração, clique no botão **Eliminar.**
4. Clique **em Sim** para confirmar a eliminação do servidor.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Desinstalar o servidor de configuração e as suas dependências
> [!TIP]
>   Se planeia reutilizar novamente o Servidor de Configuração com a Recuperação do Site Azure, então pode saltar para o passo 4 diretamente

1. Inicie sessão no Servidor de Configuração como Administrador.
2. Abra o painel de controlo > programa > desinstalar programas
3. Desinstale os programas na seguinte sequência:
   * Agente de Serviços de Recuperação do Microsoft Azure
   * Serviço de Mobilidade de Recuperação de Sites microsoft Azure/servidor master target
   * Fornecedor de recuperação de sites do Microsoft Azure
   * Servidor/servidor de processo de configuração de configuração do site do Microsoft Azure
   * Dependências do servidor de configuração de configuração do site do Microsoft Azure
   * Servidor MySQL 5.5
4. Executar o seguinte comando de e comando administrativo.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Eliminar ou desmarcar um servidor de configuração (PowerShell)

1. [Instalar](https://docs.microsoft.com/powershell/azure/install-Az-ps) Módulo Azure PowerShell
2. Inicie sessão na sua conta Azure utilizando o comando
    
    `Connect-AzAccount`
3. Selecione a subscrição sob a qual o cofre está presente

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Agora configura o teu contexto de cofre
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Obtenha selecionar o seu servidor de configuração

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminar o Servidor de Configuração

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> A opção **-Força** no Remove-AzSiteRecoveryFabric pode ser utilizada para forçar a remoção/eliminação do servidor de configuração.

## <a name="renew-tlsssl-certificates"></a>Renovar certificados TLS/SSL
O servidor de configuração tem um servidor web incorporado, que orquestra atividades do serviço mobility, servidores de processos e servidores-alvo principais ligados a ele. O servidor web utiliza um certificado TLS/SSL para autenticar clientes. O certificado expira após três anos, e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implementações de servidores de configuração antes de maio de 2016, a expiração do certificado foi definida para um ano. Se tiver um certificado expirará, ocorrem os seguintes:

- Quando a data de validade é de dois meses ou menos, o serviço começa a enviar notificações no portal, e por e-mail (se subscrever as notificações de Recuperação do Site Azure).
- Um banner de notificação aparece na página de recursos do cofre. Clique no banner para mais detalhes.
- Se vir um botão **Upgrade Now,** isto indica que existem alguns componentes no seu ambiente que não foram atualizados para versões 9.4.xxxx.x ou superiores. Atualize os componentes antes de renovar o certificado. Não pode renovar em versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra o Servidor de Configuração de > **Configuração**de **Infraestruturade Recuperação**do Site e clique no servidor de configuração necessário.
2. A data de validade aparece sob **a saúde do Servidor** de Configuração
3. Clique em **Renovar Certificados**. 




## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para a configuração da recuperação de desastres de [servidores físicos](tutorial-physical-to-azure.md) para o Azure.

