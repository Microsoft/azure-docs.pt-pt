---
title: Gerenciar servidores registrados com o Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba como registrar e cancelar o registro de um Windows Server com um serviço de sincronização de armazenamento Sincronização de Arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9bbeda33f25aec15124bacb605513a3c52c3f07e
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2019
ms.locfileid: "68699279"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Gerenciar servidores registrados com o Sincronização de Arquivos do Azure
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Ele faz isso transformando os servidores do Windows em um cache rápido do compartilhamento de arquivos do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

O artigo a seguir ilustra como registrar e gerenciar um servidor com um serviço de sincronização de armazenamento. Consulte [como implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md) para obter informações sobre como implantar sincronização de arquivos do Azure de ponta a ponta.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registrar/cancelar o registro de um servidor com o serviço de sincronização de armazenamento
O registro de um servidor com Sincronização de Arquivos do Azure estabelece uma relação de confiança entre o Windows Server e o Azure. Essa relação pode ser usada para criar *pontos de extremidade de servidor* no servidor, que representam pastas específicas que devem ser sincronizadas com um compartilhamento de arquivos do Azure (também conhecido como *ponto de extremidade de nuvem*). 

### <a name="prerequisites"></a>Pré-requisitos
Para registrar um servidor com um serviço de sincronização de armazenamento, primeiro você deve preparar o servidor com os pré-requisitos necessários:

* O servidor deve estar executando uma versão com suporte do Windows Server. Para obter mais informações, consulte [sincronização de arquivos do Azure requisitos e](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability)interoperabilidade do sistema.
* Verifique se um serviço de sincronização de armazenamento foi implantado. Para obter mais informações sobre como implantar um serviço de sincronização de armazenamento, consulte [como implantar sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md).
* Verifique se o servidor está conectado à Internet e se o Azure está acessível.
* Desabilite a configuração de segurança avançada do IE para administradores com a interface do usuário do Gerenciador do Servidor.
    
    ![Gerenciador do Servidor interface do usuário com a configuração de segurança aprimorada do IE realçada](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Verifique se o módulo Azure PowerShell está instalado no servidor. Se o servidor for membro de um cluster de failover, cada nó do cluster exigirá o módulo AZ. Mais detalhes sobre como instalar o módulo AZ podem ser encontrados no [Azure PowerShell instalar e configurar](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > É recomendável usar a versão mais recente do módulo AZ PowerShell para registrar/cancelar o registro de um servidor. Se o pacote AZ tiver sido instalado anteriormente neste servidor (e a versão do PowerShell nesse servidor for 5. * ou superior), você poderá usar o `Update-Module` cmdlet para atualizar este pacote. 
* Se você utilizar um servidor proxy de rede em seu ambiente, defina as configurações de proxy no servidor para que o agente de sincronização utilize.
    1. Determinar o endereço IP do proxy e o número da porta
    2. Edite estes dois arquivos:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adicione as linhas na Figura 1 (abaixo desta seção) em/System.ServiceModel nos dois arquivos acima alterando 127.0.0.1:8888 para o endereço IP correto (Replace 127.0.0.1) e o número da porta correto (substitua 8888):
    4. Defina as configurações de proxy do WinHTTP por meio da linha de comando:
        * Mostrar o proxy: netsh WinHTTP show proxy
        * Defina o proxy: netsh WinHTTP Set proxy 127.0.0.1:8888
        * Redefinir o proxy: netsh WinHTTP Reset proxy
        * Se essa configuração for feita depois que o agente for instalado, reinicie nosso agente de sincronização: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registrar um servidor com o serviço de sincronização de armazenamento
Antes que um servidor possa ser usado como um *ponto de extremidade do servidor* em um grupo de *sincronização*sincronização de arquivos do Azure, ele deve ser registrado com um serviço de *sincronização de armazenamento*. Um servidor só pode ser registrado com um serviço de sincronização de armazenamento por vez.

#### <a name="install-the-azure-file-sync-agent"></a>Instalar o agente do Azure File Sync
1. [Baixe o agente de sincronização de arquivos do Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie o instalador do agente de Sincronização de Arquivos do Azure.
    
    ![O primeiro painel do instalador do agente de Sincronização de Arquivos do Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Certifique-se de habilitar as atualizações para o agente de Sincronização de Arquivos do Azure usando Microsoft Update. É importante porque correções de segurança críticas e aprimoramentos de recursos para o pacote do servidor são fornecidos por meio de Microsoft Update.

    ![Verifique se Microsoft Update está habilitado no painel de Microsoft Update do instalador do agente de Sincronização de Arquivos do Azure](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se o servidor não tiver sido registrado anteriormente, a interface do usuário de registro do servidor será exibida imediatamente após a conclusão da instalação.

> [!Important]  
> Se o servidor for membro de um cluster de failover, o agente de Sincronização de Arquivos do Azure precisará ser instalado em todos os nós do cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registrar o servidor usando a interface do usuário de registro do servidor
> [!Important]  
> As assinaturas do CSP (provedor de soluções na nuvem) não podem usar a interface do usuário de registro do servidor. Em vez disso, use o PowerShell (abaixo desta seção).

1. Se a interface do usuário de registro do servidor não for iniciada imediatamente após a conclusão da instalação do agente de Sincronização de Arquivos do Azure, ela `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`poderá ser iniciada manualmente executando.
2. Clique em *entrar* para acessar sua assinatura do Azure. 

    ![Caixa de diálogo de abertura da interface do usuário de registro do servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Escolha a assinatura, o grupo de recursos e o serviço de sincronização de armazenamento corretos da caixa de diálogo.

    ![Informações do serviço de sincronização de armazenamento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Na visualização, é necessário mais uma entrada para concluir o processo. 

    ![Caixa de diálogo entrar](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se o servidor for membro de um cluster de failover, cada servidor precisará executar o registro do servidor. Quando você exibe os servidores registrados no portal do Azure, Sincronização de Arquivos do Azure reconhece automaticamente cada nó como um membro do mesmo cluster de failover e agrupa-os adequadamente.

#### <a name="register-the-server-with-powershell"></a>Registrar o servidor com o PowerShell
Você também pode executar o registro do servidor por meio do PowerShell. Essa é a única maneira com suporte de registro de servidor para assinaturas do CSP (provedor de soluções na nuvem):

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Cancelar o registro do servidor com o serviço de sincronização de armazenamento
Há várias etapas que são necessárias para cancelar o registro de um servidor com um serviço de sincronização de armazenamento. Vamos dar uma olhada em como cancelar corretamente o registro de um servidor.

> [!Warning]  
> Não tente solucionar problemas com sincronização, camadas de nuvem ou qualquer outro aspecto de Sincronização de Arquivos do Azure cancelando o registro e registrando um servidor ou removendo e recriando os pontos de extremidade do servidor, a menos que seja explicitamente instruído por um engenheiro da Microsoft. O cancelamento do registro de um servidor e a remoção de pontos de extremidade do servidor é uma operação destrutiva, e os arquivos em camadas nos volumes com pontos de extremidade do servidor não serão "reconectados" a seus locais no compartilhamento de arquivos do Azure após o servidor registrado e os pontos de extremidade do servidor serem recriado, o que resultará em erros de sincronização. Observe também que arquivos em camadas que existem fora de um namespace de ponto de extremidade de servidor podem ser permanentemente perdidos. Os arquivos em camadas podem existir nos pontos de extremidade do servidor, mesmo que a camada de nuvem nunca tenha sido habilitada.

#### <a name="optional-recall-all-tiered-data"></a>Adicional Recuperar todos os dados em camadas
Se você quiser que os arquivos que estão em camadas no momento estejam disponíveis após a remoção de Sincronização de Arquivos do Azure (isto é, um ambiente de produção, não um teste), lembre-se de todos os arquivos em cada volume que contém os pontos de extremidade do servidor. Desabilite a disposição em camadas de nuvem para todos os pontos de extremidade do servidor e execute o seguinte cmdlet do PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Se o volume local que hospeda o ponto de extremidade do servidor não tiver espaço livre suficiente para recuperar todos os dados em `Invoke-StorageSyncFileRecall` camadas, o cmdlet falhará.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Remover o servidor de todos os grupos de sincronização
Antes de cancelar o registro do servidor no serviço de sincronização de armazenamento, todos os pontos de extremidade do servidor nesse servidor devem ser removidos. Isso pode ser feito por meio do portal do Azure:

1. Navegue até o serviço de sincronização de armazenamento no qual o servidor está registrado.
2. Remova todos os pontos de extremidade do servidor deste servidor em cada grupo de sincronização no serviço de sincronização de armazenamento. Isso pode ser feito clicando com o botão direito do mouse no ponto de extremidade do servidor relevante no painel grupo de sincronização.

    ![Removendo um ponto de extremidade do servidor de um grupo de sincronização](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Isso também pode ser feito com um script simples do PowerShell:

```powershell
Connect-AzAccount

$storageSyncServiceName = "<your-storage-sync-service>"
$resourceGroup = "<your-resource-group>"

Get-AzStorageSyncGroup -ResourceGroupName $resourceGroup -StorageSyncServiceName $storageSyncServiceName | ForEach-Object { 
    $syncGroup = $_; 
    Get-AzStorageSyncServerEndpoint -ParentObject $syncGroup | Where-Object { $_.ServerEndpointName -eq $env:ComputerName } | ForEach-Object { 
        Remove-AzStorageSyncServerEndpoint -InputObject $_ 
    } 
}
```

#### <a name="unregister-the-server"></a>Cancelar o registro do servidor
Agora que todos os dados foram recuperados e o servidor foi removido de todos os grupos de sincronização, o servidor pode ter o registro cancelado. 

1. Na portal do Azure, navegue até a seção *servidores registrados* do serviço de sincronização de armazenamento.
2. Clique com o botão direito do mouse no servidor no qual você deseja cancelar o registro e clique em "cancelar registro do servidor".

    ![Anular o registo do servidor](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Garantindo Sincronização de Arquivos do Azure é um bom vizinho em seu datacenter 
Como Sincronização de Arquivos do Azure raramente será o único serviço em execução no seu datacenter, talvez você queira limitar o uso de rede e armazenamento de Sincronização de Arquivos do Azure.

> [!Important]  
> Definir limites muito baixos afetará o desempenho do Sincronização de Arquivos do Azure a sincronização e a recuperação.

### <a name="set-azure-file-sync-network-limits"></a>Definir Sincronização de Arquivos do Azure limites de rede
Você pode limitar a utilização de rede de sincronização de arquivos do Azure usando os `StorageSyncNetworkLimit` cmdlets.

> [!Note]  
> Os limites de rede não se aplicam quando um arquivo em camadas é acessado ou o cmdlet Invoke-StorageSyncFileRecall é usado.

Por exemplo, você pode criar um novo limite de limitação para garantir que Sincronização de Arquivos do Azure não use mais de 10 Mbps entre 9h e 17h (17:17h) durante a semana de trabalho: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Você pode ver seu limite usando o seguinte cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Para remover os limites de rede `Remove-StorageSyncNetworkLimit`, use. Por exemplo, o comando a seguir remove todos os limites de rede:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Usar o QoS de armazenamento do Windows Server 
Quando Sincronização de Arquivos do Azure é hospedado em uma máquina virtual em execução em um host de virtualização do Windows Server, você pode usar a QoS de armazenamento (qualidade de serviço de armazenamento) para regulamentar o consumo de e/s de armazenamento. A política de QoS de armazenamento pode ser definida como um máximo (ou limite, como o limite de StorageSyncNetwork é imposto acima) ou como um mínimo (ou reserva). Definir um mínimo em vez de um máximo permite que Sincronização de Arquivos do Azure seja intermitente para usar a largura de banda de armazenamento disponível se outras cargas de trabalho não estiverem usando. Para obter mais informações, consulte [qualidade de serviço de armazenamento](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Consulte também
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
- [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)
