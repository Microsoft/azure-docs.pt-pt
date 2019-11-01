---
title: Gerenciar o servidor de configuração para recuperação de desastre de servidores físicos locais para o Azure com o Azure Site Recovery | Microsoft Docs '
description: Este artigo descreve como gerenciar o servidor de configuração de Azure Site Recovery para recuperação de desastres do servidor físico no Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: f87210cd14570687eebae88896830bb3ee00b74e
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/31/2019
ms.locfileid: "73242995"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Gerenciar o servidor de configuração para recuperação de desastres do servidor físico

Você configura um servidor de configuração local quando usa o serviço de [Azure site Recovery](site-recovery-overview.md) para recuperação de desastre de servidores físicos no Azure. O servidor de configuração coordena as comunicações entre as máquinas locais e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração após sua implantação.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Pré-requisitos

A tabela resume os pré-requisitos para implantar o computador do servidor de configuração local.

| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 16 GB|
| Número de discos | 3, incluindo o disco do sistema operacional, o disco de cache do servidor de processo e a unidade de retenção para failback |
| Espaço livre no disco (cache do servidor de processos) | 600 GB
| Espaço livre no disco (disco de retenção) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Região do sistema operativo | English (US)|
| Versão do VMware vSphere PowerCLI | Não necessário|
| Funções do Windows Server | Não habilite estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V |
| Políticas de grupo| Não habilite essas políticas de Grupo: <br> -Impedir o acesso ao prompt de comando <br> -Impedir o acesso às ferramentas de edição do registro <br> -Lógica de confiança para anexos de arquivo <br> -Ativar a execução do script <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Nenhum site padrão já existente <br> -Habilitar [autenticação anônima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Habilitar configuração de [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx)  <br> -Nenhum site/aplicativo já existente escutando na porta 443<br>|
| Tipo de NIC | VMXNET3 (quando implantado como uma VM VMware) |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor precisa de acesso a essas URLs: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> -*. services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (não é necessário para servidores de processo de expansão) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)|

## <a name="download-the-latest-installation-file"></a>Baixar o arquivo de instalação mais recente

A versão mais recente do arquivo de instalação do servidor de configuração está disponível no portal de Site Recovery. Além disso, ele pode ser baixado diretamente do [centro de download da Microsoft](https://aka.ms/unifiedsetup).

1. Faça logon no portal do Azure e navegue até o cofre dos serviços de recuperação.
2. Navegue até **site Recovery infraestrutura** > **servidores de configuração** (em para VMware & computadores físicos).
3. Clique no botão **+ servidores** .
4. Na página **Adicionar servidor** , clique no botão baixar para baixar a chave de registro. Você precisa dessa chave durante a instalação do servidor de configuração para registrá-la no serviço Azure Site Recovery.
5. Clique no link **baixar o Microsoft Azure site Recovery Unified setup** para baixar a versão mais recente do servidor de configuração.

   ![Página de download](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalar e registrar o servidor

1. Execute o ficheiro de instalação Configuração Unificada.
2. Em **antes de começar**, selecione **instalar o servidor de configuração e o servidor de processo**.

    ![Antes de começar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Em **Licença de Software de Terceiros**, clique em **Aceito**, para transferir e instalar o MySQL.
4. Em **Definições da Internet**, especifique a forma como o Fornecedor em execução no servidor estabelece ligação ao Azure Site Recovery através da Internet. Verifique se você permitiu as URLs necessárias.

    - Se você quiser se conectar com o proxy que está configurado atualmente no computador, selecione **conectar-se a Azure site Recovery usando um servidor proxy**.
    - Se você quiser que o provedor se conecte diretamente, selecione **conectar diretamente a Azure site Recovery sem um servidor proxy**.
    - Se o proxy existente exigir autenticação ou se você quiser usar um proxy personalizado para a conexão do provedor, selecione **conectar-se com configurações de proxy personalizadas**e especifique o endereço, a porta e as credenciais.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Em **Verificação de Pré-requisitos**, a Configuração executa uma verificação para se certificar de que a instalação pode ser executada. Se for apresentado um aviso sobre a **Verificação de sincronização de hora global**, certifique-se de que a hora no relógio do sistema (definições de **Data e Hora**) é a mesma que o fuso horário.

    ![Pré-requisitos](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Em **Configuração do MySQL**, crie as credenciais para iniciar sessão na instância de servidor do MySQL que está instalada.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Se você estiver, a instalação verificará se o PowerCLI 6,0 está instalado.
9. Em **Localização de Instalação**, selecione onde pretende instalar os binários e armazenar a cache. A unidade que selecionar tem de ter, pelo menos, 5 GB de espaço disponível no disco, mas recomenda-se uma unidade de cache com, pelo menos, 600 GB de espaço livre.

    ![Localização de instalação](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Em **seleção de rede**, primeiro selecione a NIC que o servidor de processo interno usa para descoberta e instalação por push do serviço de mobilidade em computadores de origem e, em seguida, selecione a NIC que o servidor de configuração usa para conectividade com o Azure. A porta 9443 é a porta predefinida utilizada para envio e receção de tráfego de replicação, mas pode modificar este número de porta para melhor corresponda às necessidades do seu ambiente. Além da porta 9443, podemos também abrir a porta 443, que é utilizada por um servidor Web para orquestrar operações de replicação. Não use a porta 443 para enviar ou receber tráfego de replicação.

    ![Seleção de rede](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Em **Resumo**, consulte as informações e clique em **Instalar**. Quando a instalação estiver concluída, é gerada uma frase de acesso. Irá precisar dela, quando ativar a replicação, por isso, copie-a e mantenha-a numa localização segura.


Após a conclusão de registo, o servidor é apresentado no painel **Definições** > **Servidores** no cofre.


## <a name="install-from-the-command-line"></a>Instalar a partir da linha de comando

Execute o arquivo de instalação da seguinte maneira:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Exemplo de uso
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parâmetros

|Nome do Parâmetro| Tipo | Descrição| Valores|
|-|-|-|-|
| /ServerMode|Obrigatório|Especifica se a configuração e os servidores de processos devem ser instalados, ou apenas o servidor de processos|CS<br>PS|
|/InstallLocation|Obrigatório|A pasta na qual os componentes são instalados| Qualquer pasta no computador|
|/MySQLCredsFilePath|Obrigatório|O caminho do ficheiro no qual as credenciais do servidor MySQL são armazenadas|O ficheiro deve estar no formato especificado abaixo|
|/VaultCredsFilePath|Obrigatório|O caminho do ficheiro de credenciais do cofre|Caminho do ficheiro válido|
|/EnvType|Obrigatório|Tipo de ambiente que você deseja proteger |VMware<br>NonVMware|
|/PSIP|Obrigatório|Endereço IP do NIC a ser utilizado para transferência de dados de replicação| Qualquer endereço IP válido|
|/CSIP|Obrigatório|O endereço IP do NIC em que o servidor de configuração está a ouvir| Qualquer endereço IP válido|
|/PassphraseFilePath|Obrigatório|O caminho completo para a localização do ficheiro da frase de acesso|Caminho do ficheiro válido|
|/BypassProxy|Opcional|Especifica que o servidor de configuração estabelece uma ligação ao Azure sem um proxy|Para obter este valor do Venu|
|/ProxySettingsFilePath|Opcional|Definições de proxy (o proxy predefinido necessita de autenticação ou de um proxy personalizado)|O ficheiro deve estar no formato especificado abaixo|
|DataTransferSecurePort|Opcional|Número da porta no PSIP a ser utilizado para dados de replicação| Número de Porta Válido (o valor predefinido é 9433)|
|/SkipSpaceCheck|Opcional|Ignorar a verificação de espaços para a cache do disco| |
|/AcceptThirdpartyEULA|Obrigatório|O sinalizador indica a aceitação do EULA de terceiros| |
|/ShowThirdpartyEULA|Opcional|Apresenta o EULA de terceiros. Se for fornecido como entrada, todos os outros parâmetros são ignorados| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Criar entrada de arquivo para MYSQLCredsFilePath

O parâmetro MySQLCredsFilePath usa um arquivo como entrada. Crie o arquivo usando o formato a seguir e passe-o como parâmetro de entrada MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Criar entrada de arquivo para ProxySettingsFilePath
O parâmetro ProxySettingsFilePath usa um arquivo como entrada. Crie o arquivo usando o formato a seguir e passe-o como parâmetro de entrada ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modificar configurações de proxy

Você pode modificar as configurações de proxy para o computador do servidor de configuração da seguinte maneira:

1. Faça logon no servidor de configuração.
2. Inicie o cspsconfigtool. exe usando o atalho em sua área de trabalho.
3. Clique na guia **registro do cofre** .
4. Baixe um novo arquivo de registro do cofre do portal e forneça-o como entrada para a ferramenta.

   ![registrar-configuração-servidor](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os novos detalhes de proxy e clique no botão **registrar** .
6. Abra uma janela de comando do PowerShell de administrador.
7. Execute o seguinte comando:

   ```powershell
   $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
   Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
   net stop obengine
   net start obengine
   ```

   > [!WARNING]
   > Se você tiver servidores de processo adicionais anexados ao servidor de configuração, será necessário [corrigir as configurações de proxy em todos os servidores de processo de expansão](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) em sua implantação.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Registrar novamente um servidor de configuração com o mesmo cofre
1. Faça logon no servidor de configuração.
2. Inicie o cspsconfigtool. exe usando o atalho em sua área de trabalho.
3. Clique na guia **registro do cofre** .
4. Baixe um novo arquivo de registro do portal e forneça-o como entrada para a ferramenta.
      ![registrar-configuração-servidor](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os detalhes do servidor proxy e clique no botão **registrar** .  
6. Abra uma janela de comando do PowerShell de administrador.
7. Execute o seguinte comando

    ```powershell
    $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
    net stop obengine
    net start obengine
    ```

   > [!WARNING]
   > Se você tiver vários servidores de processo, precisará [registrá-los novamente](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrar um servidor de configuração com um cofre diferente

> [!WARNING]
> A etapa a seguir desassociará o servidor de configuração do cofre atual e a replicação de todas as máquinas virtuais protegidas no servidor de configuração será interrompida.

1. Faça logon no servidor de configuração
2. em um prompt de comando do administrador, execute o comando:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Inicie o cspsconfigtool. exe usando o atalho em sua área de trabalho.
4. Clique na guia **registro do cofre** .
5. Baixe um novo arquivo de registro do portal e forneça-o como entrada para a ferramenta.
6. Forneça os detalhes do servidor proxy e clique no botão **registrar** .  
7. Abra uma janela de comando do PowerShell de administrador.
8. Execute o seguinte comando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Atualizar um servidor de configuração

Você executa pacotes cumulativos de atualizações para atualizar o servidor de configuração. As atualizações podem ser aplicadas para até N-4 versões. Por exemplo:

- Se você estiver executando 9,7, 9,8, 9,9 ou 9,10, poderá atualizar diretamente para o 9,11.
- Se você estiver executando o 9,6 ou anterior e quiser atualizar para o 9,11, deverá primeiro atualizar para a versão 9,7. antes de 9,11.

Links para pacotes cumulativos de atualizações para atualização para todas as versões do servidor de configuração estão disponíveis na [página atualizações do wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Atualize o servidor da seguinte maneira:

1. Baixe o arquivo do instalador de atualização para o servidor de configuração.
2. Clique duas vezes para executar o instalador.
3. O instalador detecta a versão atual em execução no computador.
4. Clique em **OK** para confirmar e executar a atualização. 


## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou cancelar o registro de um servidor de configuração

> [!WARNING]
> Antes de começar a encerrar o servidor de configuração, verifique o seguinte:
> 1. [Desabilite a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as máquinas virtuais neste servidor de configuração.
> 2. [Desassocie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [exclua](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
> 3. [Exclua](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores vCenters/hosts vSphere associados ao servidor de configuração.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Excluir o servidor de configuração do portal do Azure
1. Em portal do Azure, navegue até **site Recovery infraestrutura** > **servidores de configuração** no menu do cofre.
2. Clique no servidor de configuração que você deseja encerrar.
3. Na página detalhes do servidor de configuração, clique no botão **excluir** .
4. Clique em **Sim** para confirmar a exclusão do servidor.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Desinstalar o servidor de configuração e suas dependências
> [!TIP]
>   Se você planeja reutilizar o servidor de configuração com Azure Site Recovery novamente, você pode passar para a etapa 4 diretamente

1. Faça logon no servidor de configuração como administrador.
2. Abrir o painel de controle > Programa > desinstalar programas
3. Desinstale os programas na seguinte sequência:
   * Agente de Serviços de Recuperação do Microsoft Azure
   * Serviço de mobilidade Microsoft Azure Site Recovery/servidor de destino mestre
   * Provedor de Site Recovery de Microsoft Azure
   * Servidor de configuração do Microsoft Azure Site Recovery/servidor de processo
   * Microsoft Azure Site Recovery dependências do servidor de configuração
   * MySQL Server 5,5
4. Execute o comando a seguir no e no prompt de comando do administrador.
   ```
   reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
   ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Excluir ou cancelar o registro de um servidor de configuração (PowerShell)

1. [Instalar](https://docs.microsoft.com/powershell/azure/install-Az-ps) o Módulo Azure PowerShell
2. Faça logon em sua conta do Azure usando o comando
    
    `Connect-AzAccount`
3. Selecione a assinatura na qual o cofre está presente

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Agora configure seu contexto do cofre
    
    ```powershell
    $Vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Obter selecione o servidor de configuração

    `$Fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Excluir o servidor de configuração

    `Remove-AzSiteRecoveryFabric -Fabric $Fabric [-Force]`

> [!NOTE]
> A opção **-Force** em Remove-AzSiteRecoveryFabric pode ser usada para forçar a remoção/exclusão do servidor de configuração.

## <a name="renew-ssl-certificates"></a>Renovar certificados SSL
O servidor de configuração tem um servidor web embutido, que orquestra as atividades do serviço de mobilidade, dos servidores de processo e dos servidores de destino mestre conectados a ele. O servidor Web usa um certificado SSL para autenticar clientes. O certificado expira após três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implantações do servidor de configuração antes de 2016 de maio, a expiração do certificado foi definida como um ano. Se você tiver um certificado prestes a expirar, ocorrerá o seguinte:

- Quando a data de expiração for de dois meses ou menos, o serviço começará a enviar notificações no portal e por email (se você se inscreveu em notificações de Azure Site Recovery).
- Uma faixa de notificação é exibida na página de recursos do cofre. Clique na faixa para obter mais detalhes.
- Se você vir um botão **Atualizar agora** , isso indica que há alguns componentes em seu ambiente que não foram atualizados para o 9.4. xxxx. x ou versões posteriores. Atualize os componentes antes de renovar o certificado. Não é possível renovar em versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra **site Recovery infraestrutura** > **servidor de configuração**e clique no servidor de configuração necessário.
2. A data de expiração aparece em **integridade do servidor de configuração**
3. Clique em **renovar certificados**. 




## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Passos seguintes

Examine os tutoriais para configurar a recuperação de desastre de [servidores físicos](tutorial-physical-to-azure.md) para o Azure.

