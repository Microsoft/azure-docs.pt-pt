---
title: Gerir o servidor de configuração para recuperação após desastre de servidores físicos no local para o Azure com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como gerir o servidor de configuração do Azure Site Recovery para a recuperação de desastres do servidor físico para o Azure.
services: site-recovery
author: mayurigupta13
ms.service: site-recovery
ms.topic: article
ms.date: 02/28/2019
ms.author: mayg
ms.openlocfilehash: 80fbc84c2284b7078b07040a74566cf1e8d57fb4
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2019
ms.locfileid: "57341090"
---
# <a name="manage-the-configuration-server-for-physical-server-disaster-recovery"></a>Gerir o servidor de configuração para a recuperação de desastres do servidor físico

Configurar um servidor de configuração no local ao utilizar o [do Azure Site Recovery](site-recovery-overview.md) serviço para recuperação após desastre de servidores físicos no Azure. O servidor de configuração coordena as comunicações entre máquinas no local e o Azure e gere a replicação de dados. Este artigo resume as tarefas comuns para gerir o servidor de configuração depois de ser implementado.

## <a name="prerequisites"></a>Pré-requisitos

A tabela resume os pré-requisitos para implementar a máquina no local do servidor de configuração.

| **Componente** | **Requisito** |
| --- |---|
| Núcleos de CPU| 8 |
| RAM | 16 GB|
| Número de discos | 3, incluindo o disco de SO, disco de cache do servidor de processo e unidade de retenção para a reativação pós-falha |
| Espaço livre no disco (cache do servidor de processos) | 600 GB
| Espaço livre no disco (disco de retenção) | 600 GB|
| Sistema operativo  | Windows Server 2012 R2 <br> Windows Server 2016 |
| Região do sistema operativo | English (US)|
| Versão do VMware vSphere PowerCLI | [PowerCLI 6.0](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1 "PowerCLI 6.0")|
| Funções do Windows Server | Não ative estas funções: <br> - Active Directory Domain Services <br>- Serviços de Informação da Internet <br> - Hyper-V |
| Políticas de grupo| Não ative estas políticas de grupo: <br> -Impedir o acesso ao prompt de comando <br> -Impedir o acesso a ferramentas de edição do registo <br> -Lógica para anexos de ficheiros de fidedignidade <br> – Ative a execução do Script <br> [Saiba mais](https://technet.microsoft.com/library/gg176671(v=ws.10).aspx)|
| IIS | -Sem Web site predefinido do já existente <br> -Permitir [autenticação anónima](https://technet.microsoft.com/library/cc731244(v=ws.10).aspx) <br> -Ativar [FastCGI](https://technet.microsoft.com/library/cc753077(v=ws.10).aspx) definição  <br> -Sem pré-existente/aplicação do Web site escute na porta 443<br>|
| Tipo NIC | VMXNET3 (quando implementado como uma VM VMware) |
| Tipo de endereço IP | Estático |
| Acesso à Internet | O servidor precisa de acesso a estes URLs: <br> - \*.accesscontrol.windows.net<br> - \*.backup.windowsazure.com <br>- \*.store.core.windows.net<br> - \*.blob.core.windows.net<br> - \*.hypervrecoverymanager.windowsazure.com <br> - https://management.azure.com <br> - *.services.visualstudio.com <br> - https://dev.mysql.com/get/Downloads/MySQLInstaller/mysql-installer-community-5.7.20.0.msi (não necessário para os servidores de processos de escalamento horizontal) <br> - time.nist.gov <br> - time.windows.com |
| Portas | 443 (Canal de controlo e orquestração)<br>9443 (Transporte de dados)|

## <a name="download-the-latest-installation-file"></a>Transferir o ficheiro de instalação mais recente

A versão mais recente do ficheiro de instalação do servidor de configuração está disponível no portal do Site Recovery. Além disso, pode ser transferido diretamente a partir da [Microsoft Download Center](https://aka.ms/unifiedsetup).

1. Inicie sessão no portal do Azure e navegue para o seu Cofre dos serviços de recuperação.
2. Navegue até **infraestrutura do Site Recovery** > **servidores de configuração** (em para VMware e máquinas físicas).
3. Clique nas **+ servidores** botão.
4. Sobre o **Adicionar servidor** página, clique no botão de transferência para transferir a chave de registo. Precisará desta chave durante a instalação de servidor de configuração para o registar no serviço Azure Site Recovery.
5. Clique nas **transferir o Microsoft Azure Site Recovery Unified Setup** link para baixar a versão mais recente do servidor de configuração.

  ![Página de transferência](./media/physical-manage-configuration-server/downloadcs.png)


## <a name="install-and-register-the-server"></a>Instalar e registar o servidor

1. Execute o ficheiro de instalação Configuração Unificada.
2. Na **antes de começar**, selecione **instalar o servidor de configuração e o servidor de processos**.

    ![Antes de começar](./media/physical-manage-configuration-server/combined-wiz1.png)

3. Em **Licença de Software de Terceiros**, clique em **Aceito**, para transferir e instalar o MySQL.
4. Em **Definições da Internet**, especifique a forma como o Fornecedor em execução no servidor estabelece ligação ao Azure Site Recovery através da Internet. Certifique-se de que ter permitido os URLs necessários.

    - Se pretender estabelecer ligação com o proxy que está atualmente configurado no computador, selecione **ligar ao Azure Site Recovery com um servidor proxy**.
    - Se pretender que o fornecedor se ligue diretamente, selecione **ligar diretamente ao Azure Site Recovery sem um servidor proxy**.
    - Se o seu proxy existente requer autenticação ou se pretender utilizar um proxy personalizado para a ligação de fornecedor, selecione **ligar com definições de proxy personalizado**e especifique o endereço, porta e credenciais.
     ![Firewall](./media/physical-manage-configuration-server/combined-wiz4.png)
6. Em **Verificação de Pré-requisitos**, a Configuração executa uma verificação para se certificar de que a instalação pode ser executada. Se for apresentado um aviso sobre a **Verificação de sincronização de hora global**, certifique-se de que a hora no relógio do sistema (definições de **Data e Hora**) é a mesma que o fuso horário.

    ![Pré-requisitos](./media/physical-manage-configuration-server/combined-wiz5.png)
7. Em **Configuração do MySQL**, crie as credenciais para iniciar sessão na instância de servidor do MySQL que está instalada.

    ![MySQL](./media/physical-manage-configuration-server/combined-wiz6.png)
8. Em **Detalhes do ambiente**, selecione se pretende replicar VMs do VMware. Se estiver, a configuração verifica se o PowerCLI 6.0 está instalado.
9. Em **Localização de Instalação**, selecione onde pretende instalar os binários e armazenar a cache. A unidade que selecionar tem de ter, pelo menos, 5 GB de espaço disponível no disco, mas recomenda-se uma unidade de cache com, pelo menos, 600 GB de espaço livre.

    ![Localização de instalação](./media/physical-manage-configuration-server/combined-wiz8.png)
10. Na **seleção de rede**, primeiro selecione o NIC que o servidor de processos incorporado utiliza para a instalação de push e de deteção do serviço de mobilidade nas máquinas de origem e, em seguida, selecione o NIC que utiliza o servidor de configuração para conectividade com o Azure. A porta 9443 é a porta predefinida utilizada para envio e receção de tráfego de replicação, mas pode modificar este número de porta para melhor corresponda às necessidades do seu ambiente. Além da porta 9443, podemos também abrir a porta 443, que é utilizada por um servidor Web para orquestrar operações de replicação. Não utilize a porta 443 para enviar ou receber tráfego de replicação.

    ![Seleção de rede](./media/physical-manage-configuration-server/combined-wiz9.png)


11. Em **Resumo**, consulte as informações e clique em **Instalar**. Quando a instalação estiver concluída, é gerada uma frase de acesso. Irá precisar dela, quando ativar a replicação, por isso, copie-a e mantenha-a numa localização segura.


Após a conclusão de registo, o servidor é apresentado no painel **Definições** > **Servidores** no cofre.


## <a name="install-from-the-command-line"></a>Instalar a partir da linha de comandos

Execute o ficheiro de instalação da seguinte forma:

  ```
  UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]
  ```

### <a name="sample-usage"></a>Utilização de exemplo
  ```
  MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted
  cd C:\Temp\Extracted
  UNIFIEDSETUP.EXE /AcceptThirdpartyEULA /servermode "CS" /InstallLocation "D:\" /MySQLCredsFilePath "C:\Temp\MySQLCredentialsfile.txt" /VaultCredsFilePath "C:\Temp\MyVault.vaultcredentials" /EnvType "VMWare"
  ```


### <a name="parameters"></a>Parâmetros

|Nome do Parâmetro| Type | Descrição| Valores|
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



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Criar a entrada de arquivo para /mysqlcredsfilepath

O parâmetro /mysqlcredsfilepath usa um arquivo como entrada. Criar o ficheiro com o seguinte formato e passá-la como parâmetro de entrada /mysqlcredsfilepath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password"
MySQLUserPassword = "Password"
```
### <a name="create-file-input-for-proxysettingsfilepath"></a>Criar a entrada de arquivo para ProxySettingsFilePath
Parâmetro de ProxySettingsFilePath apanha um arquivo como entrada. Criar o ficheiro com o seguinte formato e passá-la como parâmetro de entrada ProxySettingsFilePath.

```ini
[ProxySettings]
ProxyAuthentication = "Yes/No"
Proxy IP = "IP Address"
ProxyPort = "Port"
ProxyUserName="UserName"
ProxyPassword="Password"
```
## <a name="modify-proxy-settings"></a>Modificar as definições de proxy

Pode modificar as definições de proxy para a máquina do servidor de configuração da seguinte forma:

1. Inicie sessão no servidor de configuração.
2. Inicie o cspsconfigtool.exe usando o atalho no seu.
3. Clique nas **registo de cofre** separador.
4. Transfira um novo ficheiro de registo do cofre a partir do portal e fornecê-lo como entrada para a ferramenta.

  ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
5. Forneça os detalhes do proxy nova e clique nas **registar** botão.
6. Abra uma janela de comando do PowerShell para administradores.
7. Execute o seguinte comando:

  ```PowerShell
  $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
  Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
  net stop obengine
  net start obengine
  ```

  >[!WARNING]
  Se tiver ligados ao servidor de configuração de servidores de processos adicionais, terá [corrigir as definições de proxy em todos os servidores de processo de escalamento horizontal](vmware-azure-manage-process-server.md#modify-proxy-settings-for-an-on-premises-process-server) na sua implementação.

## <a name="reregister-a-configuration-server-with-the-same-vault"></a>Voltar a registar um servidor de configuração com o mesmo Cofre
  1. Inicie sessão no seu servidor de configuração.
  2. Inicie o cspsconfigtool.exe usando o atalho no ambiente de trabalho.
  3. Clique nas **registo de cofre** separador.
  4. Transfira um novo ficheiro de registo a partir do portal e fornecê-lo como entrada para a ferramenta.
        ![register-configuration-server](./media/physical-manage-configuration-server/register-csconfiguration-server.png)
  5. Forneça os detalhes do servidor Proxy e clique nas **registar** botão.  
  6. Abra uma janela de comando do PowerShell para administradores.
  7. Execute o seguinte comando

      ```PowerShell
      $Pwd = ConvertTo-SecureString -String MyProxyUserPassword
      Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $Pwd
      net stop obengine
      net start obengine
      ```

  >[!WARNING]
  Se tiver vários servidores de processo, terá [voltar a registá-los](vmware-azure-manage-process-server.md#reregister-a-process-server).

## <a name="register-a-configuration-server-with-a-different-vault"></a>Registar um servidor de configuração com outro Cofre

> [!WARNING]
> O passo seguinte disassociates o servidor de configuração do cofre do atual e a replicação de todas as máquinas de virtuais protegidas sob o servidor de configuração está parada.

1. Inicie sessão no servidor de configuração
2. a partir de uma linha de comandos de administrador, execute o comando:

    ```
    reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
    net stop dra
    ```
3. Inicie o cspsconfigtool.exe usando o atalho no ambiente de trabalho.
4. Clique nas **registo de cofre** separador.
5. Transfira um novo ficheiro de registo a partir do portal e fornecê-lo como entrada para a ferramenta.
6. Forneça os detalhes do servidor Proxy e clique nas **registar** botão.  
7. Abra uma janela de comando do PowerShell para administradores.
8. Execute o seguinte comando
    ```powershell
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber –ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
    ```

## <a name="upgrade-a-configuration-server"></a>Atualizar um servidor de configuração

Execute update rollups para atualizar o servidor de configuração. As atualizações podem ser aplicadas para até versões N-4. Por exemplo:

- Se estiver a executar o 9.7, 9.8, 9.9 ou 9.10 - pode atualizar diretamente para 9.11.
- Se estiver a executar o 9.6 ou anterior e pretende atualizar para o 9.11, primeiro tem de atualizar para versão 9.7. antes de 9.11.

Links para update rollups para o atualizar para todas as versões do servidor de configuração estão disponíveis no [página de atualizações de wiki](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Atualize o servidor da seguinte forma:

1. Transfira o ficheiro de instalador de atualização para o servidor de configuração.
2. Clique duas vezes para executar o instalador.
3. O instalador Deteta a versão atual em execução na máquina.
4. Clique em **OK** para confirmar e executar a atualização. 


## <a name="delete-or-unregister-a-configuration-server"></a>Eliminar ou anular o registo de um servidor de configuração

> [!WARNING]
> Certifique-se o seguinte antes de iniciar a desativação de seu servidor de configuração.
> 1. [Desative a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) todas as máquinas virtuais neste servidor de configuração.
> 2. [Desassociar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [eliminar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
> 3. [Eliminar](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os anfitriões de servidores/vSphere vCenters que estão associados ao servidor de configuração.


### <a name="delete-the-configuration-server-from-azure-portal"></a>Eliminar o servidor de configuração do portal do Azure
1. No portal do Azure, navegue até **infraestrutura do Site Recovery** > **servidores de configuração** no menu do cofre.
2. Clique no servidor de configuração que pretende desativar.
3. Na página de detalhes do servidor de configuração, clique nas **eliminar** botão.
4. Clique em **Sim** para confirmar a eliminação do servidor.

### <a name="uninstall-the-configuration-server-and-its-dependencies"></a>Desinstalar o servidor de configuração e as respetivas dependências
  > [!TIP]
  Se planejar reutilizar o servidor de configuração com o Azure Site Recovery novamente, em seguida, pode avançar para o passo 4 diretamente

1. Inicie sessão no servidor de configuração como administrador.
2. Abra o painel de controlo > programas > desinstalar programas
3. Desinstale os programas da seguinte sequência:
  * Agente dos Serviços de Recuperação do Microsoft Azure
  * Servidor do Microsoft Azure Site Recovery Mobility Service/destino principal
  * Fornecedor do Microsoft Azure Site Recovery
  * Servidor de processo do servidor de configuração do Microsoft Azure Site Recovery
  * Dependências do servidor de configuração do Microsoft Azure Site Recovery
  * Servidor MySQL 5.5
4. Execute o comando seguinte a partir e a linha de comandos de administrador.
  ```
  reg delete HKLM\Software\Microsoft\Azure Site Recovery\Registration
  ```

## <a name="delete-or-unregister-a-configuration-server-powershell"></a>Eliminar ou anular o registo de um servidor de configuração (PowerShell)

1. [Instalar](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.4.0) módulo Azure PowerShell
2. Início de sessão na sua conta do Azure com o comando
    
    `Connect-AzureRmAccount`
3. Selecione a subscrição sob a qual o Cofre está presente

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Agora configurar o contexto do Cofre
    
    ```PowerShell
    $Vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $Vault
    ```
4. Selecione obter seu servidor de configuração

    `$Fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Eliminar o servidor de configuração

    `Remove-AzureRmSiteRecoveryFabric -Fabric $Fabric [-Force] `

> [!NOTE]
> O **-forçar** opção em Remove-AzureRmSiteRecoveryFabric pode ser usada para forçar a remoção/eliminação do servidor de configuração.

## <a name="renew-ssl-certificates"></a>Renovar certificados SSL
O servidor de configuração tem um servidor de web incorporadas, que coordena as atividades do serviço de mobilidade, servidores de processos e servidores de destino mestre ligados ao mesmo. O servidor web utiliza um certificado SSL para autenticar clientes. O certificado expira após três anos e pode ser renovado em qualquer altura.

### <a name="check-expiry"></a>Verificação de expiração

Para implementações de servidor de configuração antes de Maio de 2016, a expiração de certificado foi definida para um ano. Se tiver um certificado está prestes a expirar, ocorre o seguinte:

- Quando a data de expiração é de dois meses ou menos, o serviço começa a enviar notificações no portal e por e-mail (se subscrever notificações do Azure Site Recovery).
- É apresentada uma faixa de notificação na página de recursos do cofre. Clique na faixa para obter mais detalhes.
- Se vir uma **atualizar agora** botão, isto indica que existem alguns componentes no seu ambiente que ainda não foram atualizados para 9.4.xxxx.x ou versões superiores. Atualize componentes antes de renovar o certificado. Não é possível renovar em versões anteriores.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra **infraestrutura do Site Recovery** > **servidor de configuração**e clique no servidor de configuração necessárias.
2. A data de expiração é apresentado em **estado de funcionamento do servidor de configuração**
3. Clique em **renovar certificados**. 




## <a name="common-issues"></a>Problemas comuns
[!INCLUDE [site-recovery-vmware-to-azure-install-register-issues](../../includes/site-recovery-vmware-to-azure-install-register-issues.md)]

## <a name="next-steps"></a>Passos Seguintes

Reveja os tutoriais para configurar a recuperação após desastre de [servidores físicos](tutorial-physical-to-azure.md) para o Azure.

