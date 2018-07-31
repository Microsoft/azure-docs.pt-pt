---
title: Gerir o servidor de configuração para a recuperação de desastre do VMware com o Azure Site Recovery | Documentos da Microsoft
description: Este artigo descreve como gerir um servidor de configuração existente para a recuperação de desastre do VMware no Azure com o Azure Site RecoveryS.
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: df5b2ecce2a5c9d7c263ee0acc3a49b859b93f7f
ms.sourcegitcommit: 30fd606162804fe8ceaccbca057a6d3f8c4dd56d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/30/2018
ms.locfileid: "39346125"
---
# <a name="manage-the-configuration-server-for-vmware-vms"></a>Gerir o servidor de configuração para VMs de VMware

Configurar um servidor de configuração no local quando utiliza [do Azure Site Recovery](site-recovery-overview.md) para recuperação após desastre de VMs de VMware e servidores físicos para o Azure. O servidor de configuração coordena as comunicações entre VMware e Azure no local e gere a replicação de dados. Este artigo resume as tarefas comuns para gerir o servidor de configuração após a sua implementação.



## <a name="modify-vmware-settings"></a>Modificar as definições do VMware

Pode acessar o servidor de configuração da seguinte forma:
    - Iniciar sessão para a VM no qual está implementada e iniciar o Azure Site Recovery Configuration Manager a partir do atalho de desktop.
    - Em alternativa, pode acessar o servidor de configuração remotamente a partir de **https://*ConfigurationServerName*/:44315 /**. Inicie sessão com credenciais de administrador.
   
### <a name="modify-vmware-server-settings"></a>Modificar as definições do servidor VMware

1. Para associar um servidor diferente do VMware com o servidor de configuração, após o início de sessão, selecione **Adicionar servidor do vCenter Server/vSphere ESXi**.
2. Introduza os detalhes e, em seguida, selecione **OK**.


### <a name="modify-credentials-for-automatic-discovery"></a>Modificar as credenciais para a deteção automática

1. Para atualizar as credenciais utilizadas para ligar ao servidor do VMware para a deteção automática de VMs de VMware, após o início de sessão, selecione **editar**.
2. Introduza as credenciais de novo e, em seguida, selecione **OK**.

    ![Modificar o VMware](./media/vmware-azure-manage-configuration-server/modify-vmware-server.png)


## <a name="modify-credentials-for-mobility-service-installation"></a>Modificar as credenciais para a instalação do serviço de mobilidade

Modificar as credenciais utilizadas para instalar automaticamente o serviço de mobilidade nas VMs de VMware ativar para replicação.

1. Após o início de sessão, selecione **gerir as credenciais da máquina virtual**
2. Introduza as credenciais de novo e, em seguida, selecione **OK**.

    ![Modificar as credenciais do serviço de mobilidade](./media/vmware-azure-manage-configuration-server/modify-mobility-credentials.png)

## <a name="modify-proxy-settings"></a>Modificar as definições de proxy

Modifica as definições de proxy utilizadas pela máquina do servidor de configuração para acesso à internet para o Azure. Se tiver uma máquina do servidor de processo, além do servidor de processos padrão em execução na máquina do servidor de configuração, modifique as definições em ambas as máquinas.

1. Depois de iniciar sessão no servidor de configuração, selecione **gerir conectividade**.
2. Atualize os valores de proxy. Em seguida, selecione **guardar** para atualizar as definições.

## <a name="add-a-network-adapter"></a>Adicionar uma placa de rede

O modelo de formato OVF (Open Virtualization) implementa o servidor de configuração de VM com um único adaptador de rede.

- Pode [adicionar outro adaptador à VM](vmware-azure-deploy-configuration-server.md#add-an-additional-adapter), mas deve adicioná-lo antes de registar o servidor de configuração no cofre.
- Para adicionar um adaptador depois de registar o servidor de configuração no cofre, adicione o adaptador nas propriedades da VM. Em seguida, terá de voltar a registar o servidor no cofre.


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

## Upgrade the configuration server

You run update rollups to update the configuration server. Updates can be applied for up to N-4 versions. For example:

- If you run 9.7, 9.8, 9.9, or 9.10, you can upgrade directly to 9.11.
- If you run 9.6 or earlier and you want to upgrade to 9.11, you must first upgrade to version 9.7. before 9.11.

Links to update rollups for upgrading to all versions of the configuration server are available in the [wiki updates page](https://social.technet.microsoft.com/wiki/contents/articles/38544.azure-site-recovery-service-updates.aspx).

Upgrade the server as follows:

1. In the vault, go to **Manage** > **Site Recovery Infrastructure** > **Configuration Servers**.
2. If an update is available, a link appears in the **Agent Version** > column.
    ![Update](./media/vmware-azure-manage-configuration-server/update2.png)
3. Download the update installer file to the configuration server.

    ![Update](./media/vmware-azure-manage-configuration-server/update1.png)

4. Double-click to run the installer.
5. The installer detects the current version running on the machine. Click **Yes** to start the upgrade.
6. When the upgrade completes the server configuration validates.

    ![Update](./media/vmware-azure-manage-configuration-server/update3.png)
    
7. Click **Finish** to close the installer.

## Delete or unregister a configuration server

1. [Disable protection](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) for all VMs under the configuration server.
2. [Disassociate](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) and [delete](vmware-azure-set-up-replication.md#disassociate-or-delete-a-replication-policy) all replication policies from the configuration server.
3. [Delete](vmware-azure-manage-vcenter.md#delete-a-vcenter-server) all vCenter servers/vSphere hosts that are associated with the configuration server.
4. In the vault, open **Site Recovery Infrastructure** > **Configuration Servers**.
5. Select the configuration server that you want to remove. Then, on the **Details** page, select **Delete**.

    ![Delete configuration server](./media/vmware-azure-manage-configuration-server/delete-configuration-server.png)
   

### Delete with PowerShell

You can optionally delete the configuration server by using PowerShell.

1. [Install](https://docs.microsoft.com/powershell/azure/install-azurerm-ps?view=azurermps-4.4.0) the Azure PowerShell module.
2. Sign in to your Azure account by using this command:
    
    `Connect-AzureRmAccount`
3. Select the vault subscription.

     `Get-AzureRmSubscription –SubscriptionName <your subscription name> | Select-AzureRmSubscription`
3.  Set the vault context.
    
    ```
    $vault = get-AzureRmRecoveryServicesVault-nome <name of your vault> AzureRmSiteRecoveryVaultSettings conjunto - ARSVault $vault
    ```
4. Retrieve the configuration server.

    `$fabric = Get-AzureRmSiteRecoveryFabric -FriendlyName <name of your configuration server>`
6. Delete the configuration server.

    `Remove-AzureRmSiteRecoveryFabric -Fabric $fabric [-Force] `

> [!NOTE]
> You can use the **-Force** option in Remove-AzureRmSiteRecoveryFabric for forced deletion of the configuration server.
 
## Generate configuration server Passphrase

1. Sign in to your configuration server, and then open a command prompt window as an administrator.
2. To change the directory to the bin folder, execute the command **cd %ProgramData%\ASR\home\svsystems\bin**
3. To generate the passphrase file, execute **genpassphrase.exe -v > MobSvc.passphrase**.
4. Your passphrase will be stored in the file located at **%ProgramData%\ASR\home\svsystems\bin\MobSvc.passphrase**.

## Renew SSL certificates

The configuration server has an inbuilt web server, which orchestrates activities of the Mobility Service, process servers, and master target servers connected to it. The web server uses an SSL certificate to authenticate clients. The certificate expires after three years and can be renewed at any time.

### Check expiry

For configuration server deployments before May 2016, certificate expiry was set to one year. If you have a certificate that is going to expire, the following occurs:

- When the expiry date is two months or less, the service starts sending notifications in the portal, and by email (if you subscribed to Site Recovery notifications).
- A notification banner appears on the vault resource page. For more information, select the banner.
- If you see an **Upgrade Now** button, it indicates that some components in your environment haven't been upgraded to 9.4.xxxx.x or higher versions. Upgrade the components before you renew the certificate. You can't renew on older versions.

### Renew the certificate

1. In the vault, open **Site Recovery Infrastructure** > **Configuration Server**. Select the required configuration server.
2. The expiry date appears under **Configuration Server health**.
3. Select **Renew Certificates**. 


## Next steps

Review the tutorials for setting up disaster recovery of [VMware VMs](vmware-azure-tutorial.md) to Azure.
