---
title: Gerenciar o servidor de configuração para recuperação de desastres do VMware e do servidor físico com o Azure Site Recovery | Microsoft Docs
description: Este artigo descreve como gerenciar um servidor de configuração existente para recuperação de desastres de VMs VMware e servidores físicos no Azure com Azure Site Recovery.
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 66022b5e4885c515bd6117f9a44b8108ff84ae5c
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/16/2019
ms.locfileid: "68250103"
---
# <a name="manage-the-configuration-server-for-vmware-vm-disaster-recovery"></a>Gerenciar o servidor de configuração para recuperação de desastre de VM VMware

Você configura um servidor de configuração local quando usa [Azure site Recovery](site-recovery-overview.md) para recuperação de desastre de VMs VMware e servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre o VMware local e o Azure e gerencia a replicação de dados. Este artigo resume as tarefas comuns para gerenciar o servidor de configuração após sua implantação.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="access-configuration-server"></a>Servidor de configuração de acesso

Você pode acessar o servidor de configuração da seguinte maneira:

* Entre na VM na qual ela está implantada e inicie **Azure Site Recovery Configuration Manager** do atalho da área de trabalho.
* Como alternativa, você pode acessar o servidor de configuração remotamente de https://*ConfigurationServerName*/: 44315/. Entre com as credenciais de administrador.

## <a name="modify-vmware-server-settings"></a>Modificar configurações do servidor VMware

1. Para associar um servidor VMware diferente ao servidor de configuração, após a [entrada](#access-configuration-server), selecione **Adicionar servidor vCenter Server/vSphere ESXi**.
2. Insira os detalhes e, em seguida, selecione **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Modificar credenciais para descoberta automática

1. Para atualizar as credenciais usadas para se conectar ao servidor VMware para a descoberta automática de VMs VMware, depois de [entrar](#access-configuration-server), escolha a conta e clique em **Editar**.
2. Insira as novas credenciais e, em seguida, selecione **OK**.

    ![Modificar o VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Você também pode modificar as credenciais por meio de CSPSConfigtool. exe.

1. Faça logon no servidor de configuração e inicie o CSPSConfigtool. exe
2. Escolha a conta que você deseja modificar e clique em **Editar**.
3. Insira as credenciais modificadas e clique em **OK**

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar credenciais para instalação do serviço de mobilidade

Modifique as credenciais usadas para instalar automaticamente o serviço de mobilidade nas VMs VMware habilitadas para replicação.

1. Depois de [entrar](#access-configuration-server), selecione **gerenciar credenciais da máquina virtual**
2. Escolha a conta que você deseja modificar e clique em **Editar**
3. Insira as novas credenciais e, em seguida, selecione **OK**.

    ![Modificar credenciais do serviço de mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Você também pode modificar credenciais por meio de CSPSConfigtool. exe.

1. Faça logon no servidor de configuração e inicie o CSPSConfigtool. exe
2. Escolha a conta que você deseja modificar e clique em **Editar**
3. Insira as novas credenciais e clique em **OK**.

## <a name="add-credentials-for-mobility-service-installation"></a>Adicionar credenciais para instalação do serviço de mobilidade

Se você tiver perdido a adição de credenciais durante a implantação OVF do servidor de configuração,

1. Depois de [entrar](#access-configuration-server), selecione **gerenciar credenciais da máquina virtual**.
2. Clique em **Adicionar credenciais da máquina virtual**.
    ![add-mobility-credentials](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Insira as novas credenciais e clique em **Adicionar**.

Você também pode adicionar credenciais por meio de CSPSConfigtool. exe.

1. Faça logon no servidor de configuração e inicie o CSPSConfigtool. exe
2. Clique em **Adicionar**, insira as novas credenciais e clique em **OK**.

## <a name="modify-proxy-settings"></a>Modificar configurações de proxy

Modifique as configurações de proxy usadas pelo computador do servidor de configuração para acesso à Internet para o Azure. Se você tiver um computador do servidor de processo além do servidor de processo padrão em execução no computador do servidor de configuração, modifique as configurações em ambos os computadores.

1. Depois de [entrar](#access-configuration-server) no servidor de configuração, selecione **gerenciar conectividade**.
2. Atualize os valores de proxy. Em seguida, selecione **salvar** para atualizar as configurações.

## <a name="add-a-network-adapter"></a>Adicionar um adaptador de rede

O modelo Open Virtualization Format (OVF) implanta a VM do servidor de configuração com um único adaptador de rede.

- Você pode [Adicionar um adaptador adicional à VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mas deve adicioná-lo antes de registrar o servidor de configuração no cofre.
- Para adicionar um adaptador depois de registrar o servidor de configuração no cofre, adicione o adaptador nas propriedades da VM. Em seguida, você precisa [registrar novamente](#reregister-a-configuration-server-in-the-same-vault) o servidor no cofre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Registrar novamente um servidor de configuração no mesmo cofre

Você pode registrar novamente o servidor de configuração no mesmo cofre, se necessário. Se você tiver uma máquina de servidor de processo adicional, além do servidor de processo padrão em execução no computador do servidor de configuração, registre novamente os dois computadores.


1. No cofre, abra **gerenciar** > site Recovery**servidores de configuração**de**infraestrutura** > .
2. Em **servidores**, selecione **baixar chave de registro** para baixar o arquivo de credenciais do cofre.
3. Entre no computador do servidor de configuração.
4. Em **%ProgramData%\ASR\home\svsystems\bin**, abra **cspsconfigtool. exe**.
5. Na guia **registro do cofre** , selecione **procurar**e localize o arquivo de credenciais do cofre que você baixou.
6. Se necessário, forneça os detalhes do servidor proxy. Em seguida, selecione **Registar**.
7. Abra uma janela de comando do PowerShell de administrador e execute o seguinte comando:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Para efetuar **pull dos certificados mais recentes** do servidor de configuração para o servidor de processo de expansão, execute o comando *\<"Installation Drive\Microsoft Azure site Recovery\agent\cdpcli.exe >"--registermt*

8. Por fim, reinicie o obengine executando o comando a seguir.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registrar um servidor de configuração com um cofre diferente

> [!WARNING]
> A etapa a seguir desassociará o servidor de configuração do cofre atual e a replicação de todas as máquinas virtuais protegidas no servidor de configuração será interrompida.

1. Faça logon no servidor de configuração.
2. Abra uma janela de comando do PowerShell de administrador e execute o seguinte comando:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Inicie o portal do navegador do dispositivo do servidor de configuração usando o atalho em sua área de trabalho.
4. Execute as etapas de registro semelhantes a um novo [registro](vmware-azure-tutorial.md#register-the-configuration-server)de servidor de configuração.

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Você executa pacotes cumulativos de atualizações para atualizar o servidor de configuração. As atualizações podem ser aplicadas para até N-4 versões. Por exemplo:

- Se você executar 9,7, 9,8, 9,9 ou 9,10, poderá atualizar diretamente para o 9,11.
- Se você executar o 9,6 ou anterior e quiser atualizar para o 9,11, deverá primeiro atualizar para a versão 9,7. antes de 9,11.

Para obter diretrizes detalhadas sobre a instrução de suporte a componentes do Azure Site Recovery, consulte [aqui](https://aka.ms/asr_support_statement).
Links para pacotes cumulativos de atualizações para atualização para todas as versões do servidor de configuração estão disponíveis [aqui](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> Com cada nova versão ' n' de um componente Azure Site Recovery que é lançada, todas as versões abaixo de ' N-4 ' são consideradas sem suporte. É sempre aconselhável atualizar para as versões mais recentes disponíveis.</br>
> Para obter diretrizes detalhadas sobre a instrução de suporte a componentes do Azure Site Recovery, consulte [aqui](https://aka.ms/asr_support_statement).

Atualize o servidor da seguinte maneira:

1. No cofre, vá para **gerenciar** > **site Recovery infraestrutura** > de**configuração de servidores**.
2. Se uma atualização estiver disponível, um link aparecerá na coluna **versão do agente** >.
    ![Atualização](./media/vmware-azure-manage-configuration-server/update2.png)
3. Baixe o arquivo do instalador de atualização para o servidor de configuração.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update1.png)

4. Clique duas vezes para executar o instalador.
5. O instalador detecta a versão atual em execução no computador. Clique em **Sim** para iniciar a atualização.
6. Quando a atualização for concluída, a configuração do servidor será validada.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update3.png)

7. Clique em **concluir** para fechar o instalador.
8. Para atualizar o restante dos componentes do Site Recovery, consulte nossas [diretrizes de atualização](https://aka.ms/asr_vmware_upgrades).

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Atualizar servidor de configuração/servidor de processo da linha de comando

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

|Nome do Parâmetro| Type | Descrição| Valores|
|-|-|-|-|
| /ServerMode|Necessário|Especifica se a configuração e os servidores de processos devem ser instalados, ou apenas o servidor de processos|CS<br>PS|
|/InstallLocation|Necessário|A pasta na qual os componentes são instalados| Qualquer pasta no computador|
|/MySQLCredsFilePath|Necessário|O caminho do ficheiro no qual as credenciais do servidor MySQL são armazenadas|O ficheiro deve estar no formato especificado abaixo|
|/VaultCredsFilePath|Necessário|O caminho do ficheiro de credenciais do cofre|Caminho do ficheiro válido|
|/EnvType|Necessário|Tipo de ambiente que você deseja proteger |VMware<br>NonVMware|
|/PSIP|Necessário|Endereço IP do NIC a ser utilizado para transferência de dados de replicação| Qualquer endereço IP válido|
|/CSIP|Necessário|O endereço IP do NIC em que o servidor de configuração está a ouvir| Qualquer endereço IP válido|
|/PassphraseFilePath|Necessário|O caminho completo para a localização do ficheiro da frase de acesso|Caminho do ficheiro válido|
|/BypassProxy|Opcional|Especifica que o servidor de configuração estabelece uma ligação ao Azure sem um proxy|Para obter este valor do Venu|
|/ProxySettingsFilePath|Opcional|Definições de proxy (o proxy predefinido necessita de autenticação ou de um proxy personalizado)|O ficheiro deve estar no formato especificado abaixo|
|DataTransferSecurePort|Opcional|Número da porta no PSIP a ser utilizado para dados de replicação| Número de Porta Válido (o valor predefinido é 9433)|
|/SkipSpaceCheck|Opcional|Ignorar a verificação de espaços para a cache do disco| |
|/AcceptThirdpartyEULA|Necessário|O sinalizador indica a aceitação do EULA de terceiros| |
|/ShowThirdpartyEULA|Opcional|Apresenta o EULA de terceiros. Se for fornecido como entrada, todos os outros parâmetros são ignorados| |



### <a name="create-file-input-for-mysqlcredsfilepath"></a>Criar entrada de arquivo para MYSQLCredsFilePath

O parâmetro MySQLCredsFilePath usa um arquivo como entrada. Crie o arquivo usando o formato a seguir e passe-o como parâmetro de entrada MySQLCredsFilePath.
```ini
[MySQLCredentials]
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou cancelar o registro de um servidor de configuração

1. [Desabilite a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todas as VMs no servidor de configuração.
2. [Desassocie](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [exclua](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
3. [Exclua](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores vCenter/hosts vSphere associados ao servidor de configuração.
4. No cofre, abra site Recovery**servidores de configuração**de **infraestrutura** > .
5. Selecione o servidor de configuração que você deseja remover. Em seguida, na página **detalhes** , selecione **excluir**.

    ![Excluir servidor de configuração](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Excluir com o PowerShell

Opcionalmente, você pode excluir o servidor de configuração usando o PowerShell.

1. [Instale](https://docs.microsoft.com/powershell/azure/install-Az-ps) o módulo Azure PowerShell.
2. Entre em sua conta do Azure usando este comando:

    `Connect-AzAccount`
3. Selecione a assinatura do cofre.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Defina o contexto do cofre.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recupere o servidor de configuração.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Exclua o servidor de configuração.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Você pode usar a opção **-Force** em Remove-AzSiteRecoveryFabric para a exclusão forçada do servidor de configuração.

## <a name="generate-configuration-server-passphrase"></a>Gerar frase secreta do servidor de configuração

1. Entre no servidor de configuração e abra uma janela de prompt de comando como administrador.
2. Para alterar o diretório para a pasta bin, execute o comando **CD%ProgramData%\ASR\home\svsystems\bin**
3. Para gerar o arquivo de senha, execute **genpassphrase. exe-v > arquivo mobsvc. passphrase**.
4. Sua frase secreta será armazenada no arquivo localizado em **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Renovar certificados SSL

O servidor de configuração tem um servidor web embutido, que orquestra as atividades do serviço de mobilidade, dos servidores de processo e dos servidores de destino mestre conectados a ele. O servidor Web usa um certificado SSL para autenticar clientes. O certificado expira após três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implantações do servidor de configuração antes de 2016 de maio, a expiração do certificado foi definida como um ano. Se você tiver um certificado que vai expirar, ocorrerá o seguinte:

- Quando a data de expiração for de dois meses ou menos, o serviço começará a enviar notificações no portal e por email (se você se inscreveu em notificações de Site Recovery).
- Uma faixa de notificação é exibida na página de recursos do cofre. Para obter mais informações, selecione a faixa.
- Se você vir um botão **Atualizar agora** , isso indica que alguns componentes em seu ambiente não foram atualizados para 9.4. xxxx. x ou versões posteriores. Atualize os componentes antes de renovar o certificado. Não é possível renovar em versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, abra site Recovery**servidor de configuração**de **infraestrutura** > . Selecione o servidor de configuração necessário.
2. A data de expiração aparece em **integridade do servidor de configuração**.
3. Selecione **renovar certificados**.

## <a name="refresh-configuration-server"></a>Atualizar servidor de configuração

1.  > Na portal do Azure, navegue até **cofre dos serviços de recuperação** **gerenciar** > **site Recovery infraestrutura** > **para VMware & configuração de computadores físicos** >  **Servidores** do
2. Clique no servidor de configuração que você deseja atualizar.
3. Na folha com detalhes do servidor de configuração escolhido, clique em **mais** > **Atualizar servidor**.
4. Monitore o progresso do trabalho > no **cofre dos serviços de recuperação** **monitoramento** > **site Recovery trabalhos**.

## <a name="update-windows-license"></a>Atualizar licença do Windows

A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. Para uso ininterrupto, você deve ativar o Windows com uma licença adquirida.

## <a name="failback-requirements"></a>Requisitos de reativação pós-falha

Durante a nova proteção e o failback, o servidor de configuração local deve estar em execução e em um estado conectado. Para um failback bem-sucedido, a máquina virtual que está sendo reprovada deve existir no banco de dados do servidor de configuração.

Certifique-se de fazer backups agendados regulares do seu servidor de configuração. Se ocorrer um desastre e o servidor de configuração for perdido, primeiro você deverá restaurar o servidor de configuração a partir de uma cópia de backup e garantir que o servidor de configuração restaurado tenha o mesmo endereço IP com o qual ele foi registrado no cofre. O failback não funcionará se um endereço IP diferente for usado para o servidor de configuração restaurado.

## <a name="next-steps"></a>Passos Seguintes

Examine os tutoriais para configurar a recuperação de desastre de [VMs do VMware](vmware-azure-tutorial.md) no Azure.
