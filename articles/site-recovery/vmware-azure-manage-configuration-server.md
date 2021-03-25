---
title: Gerir o servidor de configuração para recuperação de desastres com a recuperação do site Azure
description: Conheça as tarefas comuns para gerir um servidor de configuração no local para a recuperação de desastres de VMware VMs e servidores físicos para Azure com Azure Site Recovery.
author: Rajeswari-Mamilla
ms.author: ramamill
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/15/2019
ms.openlocfilehash: 67f25c97e0b4433ed09cb8c6337afe671424ba0b
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105048549"
---
# <a name="manage-the-configuration-server-for-vmware-vmphysical-server-disaster-recovery"></a>Gerir o servidor de configuração para recuperação após desastre dos servidores físicos/VM VMware

Configura um servidor de configuração no local quando utiliza [a Recuperação do Site Azure](site-recovery-overview.md) para a recuperação de desastres de VMware VMs e servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre o VMware e o Azure e gere a replicação de dados. Este artigo resume tarefas comuns para gerir o servidor de configuração após a sua implementação.


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="update-windows-license"></a>Atualizar licença do Windows

A licença fornecida com o modelo OVF é uma licença de avaliação válida por 180 dias. Para uma utilização ininterrupta, deve ativar o Windows com uma licença adquirida. A atualização da licença pode ser feita através de uma chave autónoma ou da chave padrão KMS. A orientação está disponível na [linha de comando do DISM Windows para a execução do SISTEMA](/windows-hardware/manufacture/desktop/dism-windows-edition-servicing-command-line-options). Para obter as chaves, consulte o [cliente KMS configurado](/windows-server/get-started/kmsclientkeys).

## <a name="access-configuration-server"></a>Servidor de configuração de acesso

Pode aceder ao servidor de configuração da seguinte forma:

* Inicie o sismo no VM no qual está implantado e inicie o Gestor de Configuração do **Site Azure** a partir do atalho do ambiente de trabalho.
* Em alternativa, pode aceder ao servidor de configuração remotamente a partir de https://*ConfigurationServerName*/:44315/. Inscreva-se com credenciais de administrador.

## <a name="modify-vmware-server-settings"></a>Modificar as definições do servidor VMware

1. Para associar um servidor VMware diferente ao servidor de configuração, após [o início de súd](#access-configuration-server)inserção, **selecione Adicionar vCenter Server/vSphere ESXi servidor**.
2. Introduza os detalhes e, em seguida, selecione **OK**.

## <a name="modify-credentials-for-automatic-discovery"></a>Modificar credenciais para descoberta automática

1. Para atualizar as credenciais utilizadas para ligar ao servidor VMware para a descoberta automática de VMware VMs, após [o início de sessão,](#access-configuration-server)escolha a conta e clique em **Editar**.
2. Introduza as novas credenciais e, em seguida, selecione **OK**.

    ![Modificar o VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)

Também pode modificar as credenciais através de CSPSConfigtool.exe.

1. Inicie sessão no servidor de configuração e lance CSPSConfigtool.exe
2. Escolha a conta que pretende modificar e clique em **Editar.**
3. Insira as credenciais modificadas e clique em **Ok**

## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar credenciais para instalação do Serviço de Mobilidade

Modifique as credenciais utilizadas para instalar automaticamente o Serviço de Mobilidade nos VMware VMs que ativa para a replicação.

1. Após [o s-in- Iniciar sinsucione,](#access-configuration-server) **selecione Gerir as credenciais de máquina virtual**
2. Escolha a conta que deseja modificar e clique em **Editar**
3. Introduza as novas credenciais e, em seguida, selecione **OK**.

    ![Modificar credenciais do Serviço de Mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

Também pode modificar credenciais através de CSPSConfigtool.exe.

1. Inicie sessão no servidor de configuração e lance CSPSConfigtool.exe
2. Escolha a conta que deseja modificar e clique em **Editar**
3. Insira as novas credenciais e clique **em Ok**.

## <a name="add-credentials-for-mobility-service-installation"></a>Adicionar credenciais para instalação de serviço de mobilidade

Se não tiver adição de credenciais durante a implementação do servidor de configuração OVF,

1. Após [o iniciar s-in](#access-configuration-server), selecione **Gerir as credenciais de máquina virtual**.
2. Clique em **Adicionar credenciais de máquina virtual**.
    ![Screenshot mostra Gerir o painel de credenciais de máquina virtual com o link de credenciais de máquina virtual Add.](media/vmware-azure-manage-configuration-server/add-mobility-credentials.png)
3. Introduza as novas credenciais e clique em **Adicionar**.

Também pode adicionar credenciais através de CSPSConfigtool.exe.

1. Inicie sessão no servidor de configuração e lance CSPSConfigtool.exe
2. Clique em **Adicionar,** insira as novas credenciais e clique em **Ok**.

## <a name="modify-proxy-settings"></a>Modificar as definições de procuração

Modifique as definições de procuração utilizadas pela máquina do servidor de configuração para acesso à Internet ao Azure. Se tiver uma máquina de servidor de processo para além do servidor de processo predefinido que está a ser acionado na máquina do servidor de configuração, modifique as definições em ambas as máquinas.

1. Após [iniciar sção no](#access-configuration-server) servidor de configuração, **selecione Gerir a conectividade**.
2. Atualize os valores de procuração. Em seguida, **selecione Guardar** para atualizar as definições.

## <a name="add-a-network-adapter"></a>Adicione um adaptador de rede

O modelo Open Virtualization Format (OVF) implementa o servidor de configuração VM com um único adaptador de rede.

- Pode [adicionar um adaptador adicional ao VM,](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter)mas tem de o adicionar antes de registar o servidor de configuração no cofre.
- Para adicionar um adaptador depois de registar o servidor de configuração no cofre, adicione o adaptador nas propriedades VM. Então tens [de voltar](#reregister-a-configuration-server-in-the-same-vault) a registar o servidor no cofre.

## <a name="how-to-renew-ssl-certificates"></a>Como renovar certificados SSL

O servidor de configuração tem um servidor web incorporado, que orquestra atividades dos agentes de Mobilidade em todas as máquinas protegidas, servidores de processos incorporados/escalados e servidores-alvo principais ligados a ele. O servidor web utiliza um certificado SSL para autenticar clientes. O certificado expira após três anos e pode ser renovado a qualquer momento.

### <a name="check-expiry"></a>Verifique a expiração

A data de validade aparece na **saúde do Servidor de Configuração**. Para implementações de servidores de configuração antes de maio de 2016, a expiração do certificado foi fixada para um ano. Se tiver um certificado que vai expirar, ocorre o seguinte:

- Quando a data de validade é de dois meses ou menos, o serviço começa a enviar notificações no portal e por e-mail (se subscrever as notificações de Recuperação do Site).
- Um banner de notificação aparece na página de recursos do cofre. Para mais informações, selecione o banner.
- Se vir um botão **'Atualizar Agora',** indica que alguns componentes do seu ambiente não foram atualizados para versões 9.4.xxxx.x ou versões mais altas. Atualize os componentes antes de renovar o certificado. Não se pode renovar em versões mais antigas.

### <a name="if-certificates-are-yet-to-expire"></a>Se os certificados ainda estiverem para expirar

1. Para renovar, no cofre, abra o Servidor de Configuração **da Infraestrutura de**  >  **Recuperação do** Local . Selecione o servidor de configuração necessário.
2. Certifique-se de que todos os servidores de processos de escala de componentes, servidores-alvo principais e agentes de mobilidade em todas as máquinas protegidas estão nas versões mais recentes e estão em estado conectado.
3. Agora, selecione **Renovar Certificados.**
4. Siga cuidadosamente as instruções nesta página e clique em "ok" para renovar os certificados no servidor de configuração selecionado e os seus componentes associados.

### <a name="if-certificates-have-already-expired"></a>Se os certificados já expirarem

1. Após a expiração, os certificados **não podem ser renovados a partir do portal Azure**. Antes de prosseguir, certifique-se de que todos os servidores de processos de escala de componentes, servidores-alvo principais e agentes de mobilidade em todas as máquinas protegidas estão nas versões mais recentes e estão em estado de ligação.
2. **Siga este procedimento apenas se os certificados já expirarem.** Iniciar sessão no servidor de configuração, navegar para unidade C > Desemprovação de > DeResínio do Site > casa > svsystems > e execute a ferramenta executora "RenewCerts" como administrador.
3. Uma janela de execução PowerShell aparece e despoleta a renovação de certificados. Esta ação pode demorar até 15 minutos. Não feche a janela até à conclusão da renovação.

:::image type="content" source="media/vmware-azure-manage-configuration-server/renew-certificates.png" alt-text="Renovar Certificados":::

## <a name="reregister-a-configuration-server-in-the-same-vault"></a>Reregisterar um servidor de configuração no mesmo cofre

Pode voltar a registar o servidor de configuração no mesmo cofre, se for necessário. Se tiver uma máquina de servidor de processo adicional, para além do servidor de processo predefinido que está a funcionar na máquina do servidor de configuração, reregiste ambas as máquinas.


1. No cofre, **abra** os  >  servidores de configuração **da infraestrutura de**  >  **recuperação do** local .
2. Em **Servers**, selecione Baixar a **chave de registo** para descarregar o ficheiro de credenciais do cofre.
3. Inscreva-se na máquina do servidor de configuração.
4. Em **%ProgramData%\ASR\home\home\svsystems\bin,** abra **cspsconfigtool.exe**.
5. No **separador Registo** do Cofre, selecione **Browse** e localize o ficheiro de credenciais do cofre que descarregou.
6. Se necessário, forneça detalhes do servidor proxy. Em seguida, selecione **Registar**.
7. Abra uma janela de comando powerShell de administração e executar o seguinte comando:
   ```
    $pwd = ConvertTo-SecureString -String MyProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumber – ProxyUserName domain\username -ProxyPassword $pwd
   ```

    >[!NOTE]
    >Para **extrair os certificados mais recentes** do servidor de configuração para o servidor de processo de escala executar o comando " *\<Installation Drive\Microsoft Azure Site Recovery\agent\cdpcli.exe> --registermt*

8. Finalmente, reinicie o obengenhador executando o seguinte comando.
   ```
        net stop obengine
        net start obengine
   ```


## <a name="register-a-configuration-server-with-a-different-vault"></a>Registar um servidor de configuração com um cofre diferente

> [!WARNING]
> O passo seguinte desassocia o servidor de configuração do cofre atual, e a replicação de todas as máquinas virtuais protegidas sob o servidor de configuração é interrompida.

1. Faça login no servidor de configuração.
2. Abra uma janela de comando powerShell de administração e executar o seguinte comando:

    ```
    reg delete "HKLM\Software\Microsoft\Azure Site Recovery\Registration"
    net stop dra
    ```
3. Lance o portal do navegador de aparelhos de servidor de configuração utilizando o atalho no seu ambiente de trabalho.
4. Execute as etapas de registo semelhantes a um novo [registo](vmware-azure-tutorial.md#register-the-configuration-server)de servidor de configuração .

## <a name="upgrade-the-configuration-server"></a>Atualizar o servidor de configuração

Execute rollups de atualização para atualizar o servidor de configuração. As atualizações podem ser aplicadas até versões N-4. Por exemplo:

- Se correr 9.7, 9.8, 9.9 ou 9.10, pode fazer upgrade diretamente para 9.11.
- Se correr 9.6 ou mais cedo e quiser fazer upgrade para 9.11, tem primeiro de fazer o upgrade para a versão 9.7. antes das 9:11.

Para obter orientações detalhadas sobre a declaração de suporte dos componentes do Azure Site Recovery consulte [aqui](./service-updates-how-to.md#support-statement-for-azure-site-recovery).
As ligações para atualizações de rollups para atualização para todas as versões do servidor de configuração estão disponíveis [aqui](./service-updates-how-to.md#links-to-currently-supported-update-rollups).

> [!IMPORTANT]
> Com cada nova versão 'N' de um componente de Recuperação do Site Azure que é lançado, todas as versões abaixo de 'N-4' são consideradas fora do suporte. É sempre aconselhável fazer upgrade para as versões mais recentes disponíveis.</br>
> Para obter orientações detalhadas sobre a declaração de suporte dos componentes do Azure Site Recovery consulte [aqui](./service-updates-how-to.md#support-statement-for-azure-site-recovery).

Atualize o servidor da seguinte forma:

1. No cofre, vá para **gerir servidores de**  >  configuração da **infraestrutura de**  >  **recuperação do local**.
2. Se houver uma atualização disponível, aparece um link na coluna > versão do **agente.**
    ![Atualização](./media/vmware-azure-manage-configuration-server/update2.png)
3. Descarregue o ficheiro do instalador de atualização para o servidor de configuração.

    ![Screenshot que mostra onde clicar para descarregar o ficheiro do instalador de atualização.](./media/vmware-azure-manage-configuration-server/update1.png)

4. Clique duas vezes para executar o instalador.
5. O instalador deteta a versão atual em funcionamento na máquina. Clique **em Sim** para iniciar a atualização.
6. Quando a atualização completa, a configuração do servidor valida.

    ![Screenshot que mostra a configuração de validação do servidor concluída.](./media/vmware-azure-manage-configuration-server/update3.png)

7. Clique **em Terminar** para fechar o instalador.
8. Para atualizar o resto dos componentes de Recuperação do Local, consulte a nossa [orientação de atualização.](./service-updates-how-to.md#vmware-vmphysical-server-disaster-recovery-to-azure)

## <a name="upgrade-configuration-serverprocess-server-from-the-command-line"></a>Atualizar servidor de configuração/servidor de processo a partir da linha de comando

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

|Nome do Parâmetro| Tipo | Description| Valores|
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
MySQLRootPassword = "Password>"
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

## <a name="delete-or-unregister-a-configuration-server"></a>Excluir ou não registar um servidor de configuração

1. [Desative a proteção](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) para todos os VMs no servidor de configuração.
2. [Desassociem](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) e [eliminem](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) todas as políticas de replicação do servidor de configuração.
3. [Elimine](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) todos os servidores/vSphere do vCenter que estejam associados ao servidor de configuração.
4. No cofre, abra os servidores de configuração **da infraestrutura** do local  >  .
5. Selecione o servidor de configuração que pretende remover. Em seguida, na página **Detalhes,** selecione **Delete**.

    ![Eliminar servidor de configuração](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)


### <a name="delete-with-powershell"></a>Excluir com PowerShell

Pode eliminar opcionalmente o servidor de configuração utilizando o PowerShell.

1. [Instale](/powershell/azure/install-Az-ps) o módulo Azure PowerShell.
2. Inscreva-se na sua conta Azure utilizando este comando:

    `Connect-AzAccount`
3. Selecione a subscrição do cofre.

     `Get-AzSubscription –SubscriptionName <your subscription name> | Select-AzSubscription`
3.  Desa parte do contexto do cofre.

    ```
    $vault = Get-AzRecoveryServicesVault -Name <name of your vault>
    Set-AzSiteRecoveryVaultSettings -ARSVault $vault
    ```
4. Recupere o servidor de configuração.

    `$fabric = Get-AzSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Elimine o servidor de configuração.

    `Remove-AzSiteRecoveryFabric -Fabric $fabric [-Force]`

> [!NOTE]
> Pode utilizar a opção **-Force** em Remove-AzSiteRecoveryFabric para a eliminação forçada do servidor de configuração.

## <a name="generate-configuration-server-passphrase"></a>Gerar servidor de configuração Passphrase

1. Inscreva-se no servidor de configuração e, em seguida, abra uma janela de pedido de comando como administrador.
2. Para alterar o diretório para a pasta do caixote do lixo, execute o cd de comando **%ProgramData%\ASR\home\svsystems\bin**
3. Para gerar o ficheiro de palavras-passe, execute **genpassphrase.exe -v > mobSvc.passphrase**.
4. A sua palavra-passe será armazenada no ficheiro localizado em **%ProgramData%\ASR\home\home\svsystems\bin\MobSvc.passphrase**.

## <a name="refresh-configuration-server"></a>Atualizar servidor de configuração

1. No portal Azure, navegue para **serviços de recuperação Vault**  >  **Manage**  >  **Site Recovery Infrastructure** for  >  **VMware & Physical Machines** Configuration  >  **Servers**
2. Clique no servidor de configuração que deseja atualizar.
3. Na lâmina com detalhes do servidor de configuração escolhido, clique em **Mais**  >  **Refresh Server**.
4. Monitorize o progresso do trabalho no âmbito dos **trabalhos** de recuperação do local  >  **de**  >  **recuperação do local de monitorização dos serviços de recuperação de cofres.**

## <a name="failback-requirements"></a>Requisitos de reativação pós-falha

Durante a reproteção e o failback, o servidor de configuração no local deve estar a funcionar e num estado ligado. Para um failback bem sucedido, a máquina virtual que está a ser falhada deve existir na base de dados do servidor de configuração.

Certifique-se de que faz cópias de segurança regulares do seu servidor de configuração. Se ocorrer um desastre e o servidor de configuração for perdido, deve primeiro restaurar o servidor de configuração a partir de uma cópia de backup e certificar-se de que o servidor de configuração restaurado tem o mesmo endereço IP com o qual foi registado no cofre. O Failback não funcionará se for utilizado um endereço IP diferente para o servidor de configuração restaurado.

## <a name="next-steps"></a>Passos seguintes

Reveja os tutoriais para a criação de [VMware VMs](vmware-azure-tutorial.md) para Azure.
