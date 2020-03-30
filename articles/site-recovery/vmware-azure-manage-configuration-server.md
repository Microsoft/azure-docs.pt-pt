---
title: Gerir o servidor de configuração para recuperação de desastres com a Recuperação do Site Azure
author: Rajeswari-Mamilla
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.author: ramamill
ms.openlocfilehash: 93b10d56ae34ebdfe78dd20705634dea58721274
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257280"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Gerir o servidor de configuração para vMware VM/recuperação de desastres de servidor físico

Configura um servidor de configuração no local quando utilizar a Recuperação do [Site Azure](site-recovery-overview.md) para a recuperação de desastres de VMware VMs e servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre vMware no local e Azure e gere a replicação de dados. Este artigo resume tarefas comuns para gerir o servidor de configuração após a sua implementação.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Atualizar licença Windows

A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. Para uma utilização ininterrupta, deve ativar o Windows com uma licença adquirida. A atualização da licença pode ser feita através de uma chave autónoma ou de uma chave padrão KMS. A orientação está disponível na [linha de comando DISM Windows para executar O](https://docs.microsoft.com/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Para obter chaves, consulte o [cliente KMS configurado](https://docs.microsoft.com/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Servidor de configuração de acesso

Pode aceder ao servidor de configuração da seguinte forma:

* Inicie sessão no VM no qual está implantado e start **Azure Site Recovery Configuration Manager** a partir do atalho do ambiente de trabalho.
* Em alternativa, pode aceder ao servidor de configuração remotamente a partir https://*ConfiguraçãoServerName*/:44315/. Inscreva-se com credenciais de administrador.

## <a name="modify-vmware-server-settings"></a>Modificar as definições do servidor VMware

1. Para associar um servidor VMware diferente ao servidor de configuração, após o iniciar o ['sign-in',](#access-configuration-server)selecione **Adicionar servidor ESXi vCenter Server/vSphere**.
2. Introduza os detalhes e, em seguida, selecione **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Modificar credenciais para descoberta automática

1. Para atualizar as credenciais utilizadas para se ligar ao servidor VMware para a descoberta automática de VMware VMs, após o [registo,](#access-configuration-server)escolha a conta e clique em **Editar**.
2. Introduza as novas credenciais e, em seguida, selecione **OK**.

    ![Modificar VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Também pode modificar as credenciais através do CSPSConfigtool.exe.

1. Inicie sessão no servidor de configuração e lance CSPSConfigtool.exe
2. Escolha a conta que pretende modificar e clique em **Editar**.
3. Introduza as credenciais modificadas e clique em **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar credenciais para instalação do Serviço de Mobilidade

Modifique as credenciais utilizadas para instalar automaticamente o Serviço de Mobilidade nos VMware que ativa para a replicação.

1. Após [o iniciar o 'sign-in',](#access-configuration-server)selecione Gerir as **credenciais de máquina virtual**
2. Escolha a conta que pretende modificar e clique em **Editar**
3. Introduza as novas credenciais e, em seguida, selecione **OK**.

    ![Modificar credenciais do Serviço de Mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Também pode modificar credenciais através de CSPSConfigtool.exe.

1. Inicie sessão no servidor de configuração e lance CSPSConfigtool.exe
2. Escolha a conta que pretende modificar e clique em **Editar**
3. Introduza as novas credenciais e clique em **Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Adicione credenciais para instalação de serviço de mobilidade

Se não tiver sido adicionando credenciais durante a implementação do servidor de configuração OVF,

1. Depois [de iniciar sessão,](#access-configuration-server)selecione **Gerir as credenciais**de máquina virtual .
2. Clique em **Adicionar credenciais de máquina virtual**.
    ![adicionar-mobilidade-credenciais](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Introduza as novas credenciais e clique em **Adicionar**.

Também pode adicionar credenciais através de CSPSConfigtool.exe.

1. Inicie sessão no servidor de configuração e lance CSPSConfigtool.exe
2. Clique em **Adicionar,** introduza as novas credenciais e clique em **Ok**.

## <a name="modify-proxy-settings"></a>Modificar as definições de procuração

Modifique as definições de procuração utilizadas pela máquina de servidor de configuração para acesso à Internet ao Azure. Se tiver uma máquina de servidor de processo para além do servidor de processo sinuoso a funcionar na máquina do servidor de configuração, modifique as definições em ambas as máquinas.

1. Depois [de iniciar sessão](#access-configuration-server) no servidor de configuração, selecione **Gerir a conectividade**.
2. Atualize os valores de procuração. Em seguida, selecione **Guardar** para atualizar as definições.

## <a name="add-a-network-adapter"></a>Adicione um adaptador de rede

O modelo de formato de virtualização aberta (OVF) implementa o VM do servidor de configuração com um único adaptador de rede.

- Pode [adicionar um adaptador adicional ao VM,](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)mas tem de o adicionar antes de registar o servidor de configuração no cofre.
- Para adicionar um adaptador depois de registar o servidor de configuração no cofre, adicione o adaptador nas propriedades VM. Então precisa voltar a [registar](#reregister-a-configuration-server-in-the-same-vault) o servidor no cofre.


## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Reregistre um servidor de configuração no mesmo cofre

Pode voltar a registar o servidor de configuração no mesmo cofre, se necessário. Se tiver uma máquina de servidor de processo adicional, para além do servidor de processo predefinido que está a funcionar na máquina do servidor de configuração, reregistre ambas as máquinas.


1. No cofre, **abra** > **os servidores**de configuração de configuração de**infraestrutura** > de recuperação do site .
2. Nos **Servidores,** selecione **a chave de registo de descarregamento** para descarregar o ficheiro de credenciais de cofre.
3. Inscreva-se na máquina do servidor de configuração.
4. Em **%ProgramData%\ASR\home\svsystems\bin,** **cspsconfigtool.exe**.
5. No separador **Registo do Cofre,** selecione **Browse**e localize o ficheiro de credenciais do cofre que descarregou.
6. Se necessário, forneça detalhes do servidor proxy. Em seguida, selecione **Registar**.
7. Abra uma janela de comando powerShell e executar o seguinte comando:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Para **retirar os mais recentes certificados** do servidor de configuração para o servidor de processo satisfaça o comando " *\<Unidade de Instalação\Microsoft Azure Site Recovery\agent\cdpcli.exe>" --registermt*

8. Finalmente, reinicie o motor de obengine executando o seguinte comando.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registe um servidor de configuração com um cofre diferente

> [!WARNING]
> O passo seguinte dissocia o servidor de configuração do cofre atual, e a replicação de todas as máquinas virtuais protegidas sob o servidor de configuração é interrompida.

1. Inicie sessão no servidor de configuração.
2. Abra uma janela de comando powerShell e executar o seguinte comando:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Lance o portal do navegador do servidor de configuração utilizando o atalho no seu ambiente de trabalho.
4. Execute os passos de registo semelhantes a um novo [registo](vmware-azure-tutorial.md#register-the-configuration-server)do servidor de configuração .

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Executa os rollups de atualização para atualizar o servidor de configuração. As atualizações podem ser aplicadas até versões N-4. Por exemplo:

- Se correr 9.7, 9.8, 9.9 ou 9.10, pode fazer upgrade diretamente para 9.11.
- Se executar 9.6 ou mais cedo e pretender fazer upgrade para 9.11, tem primeiro de atualizar para a versão 9.7. antes das 9:11.

Para obter orientações detalhadas sobre a declaração de suporte dos componentes de recuperação do site Azure consulte [aqui](https://aka.ms/asr_support_statement).
As ligações para atualizar rollups para upgrade para todas as versões do servidor de configuração estão disponíveis [aqui](https://aka.ms/asr_update_rollups).

> [!IMPORTANT]
> Com cada nova versão 'N' de um componente azure de recuperação de sites que é lançado, todas as versões abaixo de 'N-4' são consideradas fora de suporte. É sempre aconselhável fazer upgrade para as versões mais recentes disponíveis.</br>
> Para obter orientações detalhadas sobre a declaração de suporte dos componentes de recuperação do site Azure consulte [aqui](https://aka.ms/asr_support_statement).

Atualize o servidor da seguinte forma:

1. No cofre, vá **gerir** > os servidores de configuração de > **infraestruturas**de recuperação do**local.**
2. Se estiver disponível uma atualização, aparece um link na versão do **agente** > coluna.
    ![Atualização](./media/vmware-azure-manage-configuration-server/update2.png)
3. Descarregue o ficheiro do instalador de atualizações para o servidor de configuração.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update1.png)

4. Clique duas vezes para executar o instalador.
5. O instalador deteta a versão atual a funcionar na máquina. Clique em **Sim** para iniciar a atualização.
6. Quando a atualização completa, a configuração do servidor valida.

    ![Atualizar](./media/vmware-azure-manage-configuration-server/update3.png)

7. Clique em **Terminar** para fechar o instalador.
8. Para atualizar o resto dos componentes de Recuperação do Site, consulte a nossa [orientação](https://aka.ms/asr_vmware_upgrades)de upgrade .

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Atualizar servidor/servidor de processo de configuração a partir da linha de comando

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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou desmarcar um servidor de configuração

1. [Desative a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todos os VMs sob o servidor de configuração.
2. [Desassociater](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [eliminar](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
3. [Elimine](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores/anfitriões vSphere que estejam associados ao servidor de configuração.
4. No cofre, aberto**sinuosos servidores**de configuração de **infraestrutura** > de recuperação do local.
5. Selecione o servidor de configuração que pretende remover. Em seguida, na página **Detalhes,** **selecione Eliminar**.

    ![Eliminar servidor de configuração](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Eliminar com powerShell

Pode eliminar opcionalmente o servidor de configuração utilizando o PowerShell.

1. [Instale](https://docs.microsoft.com/powershell/azure/install-Az-ps) o módulo Azure PowerShell.
2. Inscreva-se na sua conta Azure utilizando este comando:

    `Connect-AzAccount`
3. Selecione a assinatura do cofre.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Desconte o contexto do cofre.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recupere o servidor de configuração.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Elimine o servidor de configuração.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Pode utilizar a opção **-Força** no Remove-AzSiteRecoveryFabric para a eliminação forçada do servidor de configuração.

## <a name="generate-configuration-server-passphrase"></a>Gerar frase de passe do servidor de configuração

1. Inscreva-se no seu servidor de configuração e, em seguida, abra uma janela de solicitação de comando como administrador.
2. Para alterar o diretório para a pasta do lixo, execute o cd de comando **%ProgramData%\ASR\home\svsystems\bin**
3. Para gerar o ficheiro de palavra-passe, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. A sua palavra-passe será armazenada no ficheiro localizado em **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## <a name="renew-ssl-certificates"></a>Renovar certificados SSL

O servidor de configuração tem um servidor web incorporado, que orquestra atividades do Serviço de Mobilidade, servidores de processos e servidores-alvo principais ligados a ele. O servidor web utiliza um certificado SSL para autenticar clientes. O certificado expira após três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verificar expiração

Para implementações de servidores de configuração antes de maio de 2016, a expiração do certificado foi definida para um ano. Se tiver um certificado que vai expirar, ocorre o seguinte:

- Quando a data de validade é de dois meses ou menos, o serviço começa a enviar notificações no portal, e por e-mail (se tiver subscrito as notificações de Recuperação do Site).
- Um banner de notificação aparece na página de recursos do cofre. Para mais informações, selecione o banner.
- Se vir um botão **Upgrade Now,** indica que alguns componentes do seu ambiente não foram atualizados para versões 9.4.xxxx.x ou superiores. Atualize os componentes antes de renovar o certificado. Não pode renovar em versões mais antigas.

### <a name="renew-the-certificate"></a>Renovar o certificado

1. No cofre, aberto servidor de**configuração**de **infraestrutura** > de recuperação do local. Selecione o servidor de configuração necessário.
2. A data de validade aparece sob **a saúde**do Servidor de Configuração .
3. **Selecione Renovar Certificados**.

## <a name="refresh-configuration-server"></a>Servidor de configuração de atualização

1. No**Manage** > portal Azure, navegue para a infraestrutura > de**recuperação**do cofre de **recuperação** > do cofre**para VMware &** > servidores de configuração de máquinas**físicas**
2. Clique no servidor de configuração que pretende atualizar.
3. Na lâmina com detalhes do servidor de configuração escolhido, clique em **Mais** > **Refresh Server**.
4. Monitorize o progresso do trabalho no âmbito dos **trabalhos** > de recuperação do cofre de**monitorização** > do local de recuperação do**local.**

## <a name="failback-requirements"></a>Requisitos de reativação pós-falha

Durante a reproteção e o recuo, o servidor de configuração no local deve estar em execução e em estado de ligação. Para um retrocesso bem sucedido, a falha da máquina virtual deve existir na base de dados do servidor de configuração.

Certifique-se de que tem cópias de segurança regulares do seu servidor de configuração. Se ocorrer um desastre e o servidor de configuração estiver perdido, primeiro deve restaurar o servidor de configuração a partir de uma cópia de cópia de cópia de cópia de cópia de segurança e certificar-se de que o servidor de configuração restaurado tem o mesmo endereço IP com o qual foi registado no cofre. O failback não funcionará se for utilizado um endereço IP diferente para o servidor de configuração restaurado.

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para a criação de uma recuperação de desastres de [VMware VMs](vmware-azure-tutorial.md) para Azure.
