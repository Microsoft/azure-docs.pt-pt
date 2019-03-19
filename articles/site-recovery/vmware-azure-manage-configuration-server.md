---
title: Gerir o servidor de configuração para VMware e de recuperação de desastres do servidor físico com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como gerir um servidor de configuração existente para recuperação após desastre de VMs de VMware e servidores físicos no Azure com o Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 02/12/2018
ms.author: ramamill
ms.openlocfilehash: da7750198f76bc9e17c23b1347e9fc78262aa06c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58086960"
---
# <a name="manage-the-configuration-server-for-vmware-vm-disaster-recovery"></a>Gerir o servidor de configuração para a recuperação de desastres da VM de VMware

Configurar um servidor de configuração no local quando utiliza [do Azure Site Recovery](site-recovery-overview.md) para recuperação após desastre de VMs de VMware e servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre VMware e Azure no local e gere a replicação de dados. Este artigo resume as tarefas comuns para gerir o servidor de configuração após a sua implementação.

## <a name="access-configuration-server"></a>Servidor de configuração de acesso

Pode acessar o servidor de configuração da seguinte forma:

* Entrar para a VM no qual está implementada e inicie **Gestor de configuração do Azure Site Recovery** de atalho de ambiente de trabalho.
* Em alternativa, pode acessar o servidor de configuração remotamente de https://*ConfigurationServerName*/:44315 /. Inicie sessão com credenciais de administrador.

## <a name="modify-vmware-server-settings"></a>Modificar as definições do servidor VMware

1. Para associar um servidor diferente do VMware com o servidor de configuração, após [início de sessão](#access-configuration-server), selecione **Adicionar servidor do vCenter Server/vSphere ESXi**.
2. Introduza os detalhes e, em seguida, selecione **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Modificar as credenciais para a deteção automática

1. Para atualizar as credenciais utilizadas para ligar ao servidor do VMware para a deteção automática de VMs de VMware, após [início de sessão](#access-configuration-server), selecione a conta e clique em **editar**.
2. Introduza as credenciais de novo e, em seguida, selecione **OK**.

    ![Modificar o VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Também pode modificar as credenciais por meio de CSPSConfigtool.exe.

1. Início de sessão para o servidor de configuração e lançamento CSPSConfigtool.exe
2. Selecione a conta que pretende modificar e clique em **editar**.
3. Introduza as credenciais modificadas e clique em **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar as credenciais para a instalação do serviço de mobilidade

Modificar as credenciais utilizadas para instalar automaticamente o serviço de mobilidade nas VMs de VMware ativar para replicação.

1. Após [início de sessão](#access-configuration-server), selecione **gerir as credenciais da máquina virtual**
2. Selecione a conta que pretende modificar e clique em **editar**
3. Introduza as credenciais de novo e, em seguida, selecione **OK**.

    ![Modificar as credenciais do serviço de mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Também pode modificar as credenciais através da CSPSConfigtool.exe.

1. Início de sessão para o servidor de configuração e lançamento CSPSConfigtool.exe
2. Selecione a conta que pretende modificar e clique em **editar**
3. Introduza as credenciais de novo e clique em **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Adicionar credenciais para a instalação do serviço de mobilidade

Se não adicionar credenciais durante a implementação de OVF do servidor de configuração,

1. Após [início de sessão](#access-configuration-server), selecione **gerir as credenciais da máquina virtual**.
2. Clique em **adicionar credenciais de máquina virtual**.
    ![add-mobility-credentials](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Introduza as credenciais de novo e clique em **adicionar**.

Também pode adicionar as credenciais através da CSPSConfigtool.exe.

1. Início de sessão para o servidor de configuração e lançamento CSPSConfigtool.exe
2. Clique em **Add**, introduza as credenciais de novo e clique em **Ok**.

## <a name="modify-proxy-settings"></a>Modificar as definições de proxy

Modifica as definições de proxy utilizadas pela máquina do servidor de configuração para acesso à internet para o Azure. Se tiver uma máquina do servidor de processo, além do servidor de processos padrão em execução na máquina do servidor de configuração, modifique as definições em ambas as máquinas.

1. Após [início de sessão](#access-configuration-server) para o servidor de configuração, selecione **gerir conectividade**.
2. Atualize os valores de proxy. Em seguida, selecione **guardar** para atualizar as definições.

## <a name="add-a-network-adapter"></a>Adicionar uma placa de rede

O modelo de formato OVF (Open Virtualization) implementa o servidor de configuração de VM com um único adaptador de rede.

- Pode [adicionar outro adaptador à VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mas deve adicioná-lo antes de registar o servidor de configuração no cofre.
- Para adicionar um adaptador depois de registar o servidor de configuração no cofre, adicione o adaptador nas propriedades da VM. Em seguida, precisa [voltar a registar](#reregister-a-configuration-server-in-the-same-vault) o servidor no cofre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Voltar a registar um servidor de configuração no mesmo Cofre

Pode voltar a registar o servidor de configuração no mesmo cofre se for necessário. Se tiver uma máquina de servidor de processos adicionais, além do servidor de processos padrão em execução na máquina do servidor de configuração, voltar a registar ambas as máquinas.


1. No cofre, abra **Manage** > **infraestrutura do Site Recovery** > **Configuration Servers**.
2. Na **servidores**, selecione **chave de registo do Download** para transferir o ficheiro de credenciais do cofre.
3. Inicie sessão na máquina do servidor de configuração.
4. Na **%ProgramData%\ASR\home\svsystems\bin**, abra **cspsconfigtool.exe**.
5. Sobre o **registo do cofre** separador, selecione **procurar**e localize o ficheiro de credenciais de cofre que transferiu.
6. Se for necessário, forneça detalhes do servidor proxy. Em seguida, selecione **Registar**.
7. Abra uma janela de comando do PowerShell de administrador e execute o seguinte comando:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Para **solicitar certificados mais recentes** do servidor de configuração para o servidor de processos de escalamento horizontal, execute o comando *"< Drive\Microsoft de instalação do Azure Site Recovery\agent\cdpcli.exe >"-- registermt*

8. Por fim, reinicie o obengine executando o seguinte comando.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registar um servidor de configuração com outro Cofre

> [!WARNING]
> O passo seguinte disassociates o servidor de configuração do cofre do atual e a replicação de todas as máquinas de virtuais protegidas sob o servidor de configuração está parada.

1. Inicie sessão no servidor de configuração.
2. Abra uma janela de comando do PowerShell de administrador e execute o seguinte comando:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Inicie o portal de browser da aplicação do configuration server usando o atalho no ambiente de trabalho.
4. Execute os passos de registo semelhantes para um novo servidor de configuração [registo](vmware-azure-tutorial.md#register-the-configuration-server).

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Execute update rollups para atualizar o servidor de configuração. As atualizações podem ser aplicadas para até versões N-4. Por exemplo:

- Se executar 9.7, 9.8, 9.9 ou 9.10, pode atualizar diretamente para 9.11.
- Se executar 9.6 ou anterior e pretende atualizar para o 9.11, primeiro tem de atualizar para versão 9.7. antes de 9.11.

Para obter instruções detalhadas sobre os componentes do Azure Site Recovery consulte a declaração de suporte [aqui](https://aka.ms/asr_support_statement).
Links para update rollups para o atualizar para todas as versões do servidor de configuração estão disponíveis [aqui](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> A cada nova versão "n" de um Azure Site Recovery componente que for lançado, todas as versões anteriores ao ' n-4' é considerado sem suporte. É sempre aconselhável atualizar para versões mais recentes disponíveis.</br>
> Para obter instruções detalhadas sobre os componentes do Azure Site Recovery consulte a declaração de suporte [aqui](https://aka.ms/asr_support_statement).

Atualize o servidor da seguinte forma:

1. No cofre, aceda ao **Manage** > **infraestrutura do Site Recovery** > **Configuration Servers**.
2. Se está disponível uma atualização, um link será exibido na **versão do agente** > coluna.
    ![Atualização](./media/vmware-azure-manage-configuration-server/update2.png)
3. Transfira o ficheiro de instalador de atualização para o servidor de configuração.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update1.png)

4. Clique duas vezes para executar o instalador.
5. O instalador Deteta a versão atual em execução na máquina. Clique em **Sim** para iniciar a atualização.
6. Quando a atualização for concluída valida a configuração do servidor.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update3.png)

7. Clique em **concluir** para fechar o instalador.
8. Para atualizar o resto dos componentes do Site Recovery, consulte a nossa [orientações de atualização](https://aka.ms/asr_vmware_upgrades).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Atualizar o servidor de processo do servidor de configuração a partir da linha de comandos

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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Eliminar ou anular o registo de um servidor de configuração

1. [Desative a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as VMs sob o servidor de configuração.
2. [Desassociar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [eliminar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
3. [Eliminar](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os anfitriões de vSphere/servidores vCenter que estão associados com o servidor de configuração.
4. No cofre, abra **infraestrutura do Site Recovery** > **servidores de configuração**.
5. Selecione o servidor de configuração que pretende remover. Em seguida, no **detalhes** página, selecione **eliminar**.

    ![Eliminar o servidor de configuração](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Eliminar com o PowerShell

Opcionalmente, pode eliminar o servidor de configuração com o PowerShell.

1. [Instalar](https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-4.4.0) o módulo Azure PowerShell.
2. Inicie sessão sua conta do Azure, utilizando este comando:

    `Connect-AzureRmAccount`
3. Selecione a subscrição do cofre.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Defina o contexto do cofre.

    ```
    $vault = Get-AzureRmRecoveryServicesVault -Name <name of your vault>
    Set-AzureRmSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Obter o servidor de configuração.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Elimine o servidor de configuração.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> Pode utilizar o **-força** opção em Remove-AzureRmSiteRecoveryFabric para eliminação forçada do servidor de configuração.

## <a name="generate-configuration-server-passphrase"></a>Gerar frase de acesso do servidor de configuração

1. Inicie sessão no seu servidor de configuração e, em seguida, abra uma janela de linha de comandos como administrador.
2. Para alterar o diretório para a pasta Reciclagem, execute o comando **cd %ProgramData%\ASR\home\svsystems\bin**
3. Para gerar o ficheiro da frase de acesso, execute **genpassphrase.exe - v > MobSvc.passphrase**.
4. A frase de acesso será armazenado no arquivo localizado em **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Renovar certificados SSL

O servidor de configuração tem um servidor de web incorporadas, que coordena as atividades do serviço de mobilidade, servidores de processos e os servidores de destino mestre conectados a ele. O servidor web utiliza um certificado SSL para autenticar clientes. O certificado expira após três anos e pode ser renovado em qualquer altura.

### <a name="check-expiry"></a>Verificação de expiração

Para implementações de servidor de configuração antes de Maio de 2016, a expiração de certificado foi definida para um ano. Se tiver um certificado que está prestes a expirar, ocorre o seguinte:

- Quando a data de expiração é de dois meses ou menos, o serviço começa a enviar notificações no portal e por e-mail (se subscrever notificações de recuperação de Site).
- É apresentada uma faixa de notificação na página de recursos do cofre. Para obter mais informações, selecione a faixa.
- Se vir uma **atualizar agora** botão, ele indica que alguns componentes no seu ambiente ainda não foram atualizados para 9.4.xxxx.x ou versões superiores. Atualize os componentes antes de renovar o certificado. Não é possível renovar em versões anteriores.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra **infraestrutura do Site Recovery** > **servidor de configuração**. Selecione o servidor de configuração necessárias.
2. A data de expiração é apresentado em **estado de funcionamento do servidor de configuração**.
3. Selecione **renovar certificados**.

## <a name="refresh-configuration-server"></a>Atualizar o servidor de configuração

1. No portal do Azure, navegue até **cofre dos serviços de recuperação** > **gerir** > **infraestrutura do Site Recovery**  >   **Para máquinas do VMware e físicas** > **servidores de configuração**
2. Clique no servidor de configuração que pretende atualizar.
3. No painel com detalhes do servidor de configuração escolhido, clique em **mais** > **atualizar servidor**.
4. Monitorizar o progresso da tarefa sob **cofre dos serviços de recuperação** > **monitorização** > **tarefas do Site Recovery**.

## <a name="update-windows-license"></a>Atualizar a licença do Windows

A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. Para utilização sem interrupções, tem de ativar o Windows com uma licença procured.

## <a name="failback-requirements"></a>Requisitos de reativação pós-falha

Durante a reproteção e reativação pós-falha, o servidor de configuração no local tem de ser em execução e num estado ligado. Para a reativação pós-falha concluída com êxito, a máquina virtual que está a ser realizarão a reativação pós-falha tem de existir na base de dados de servidor de configuração.

Certifique-se de que obter cópias de segurança agendadas regulares do seu servidor de configuração. Se ocorrer um desastre, e o servidor de configuração é perdido, tem de restaurar o servidor de configuração a partir de uma cópia de segurança e certifique-se de que o servidor de configuração restaurada tem o mesmo endereço IP com a qual ele foi registado no cofre. Reativação pós-falha não irá funcionar se um endereço IP diferente é utilizado para o servidor de configuração restaurada.

## <a name="next-steps"></a>Passos Seguintes

Reveja os tutoriais para configurar a recuperação após desastre de [VMs de VMware](vmware-azure-tutorial.md) para o Azure.
