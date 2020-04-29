---
title: Gerir servidores registados com O Sincronizado de Ficheiros Azure [ Microsoft Docs
description: Saiba como registar e desregistar um Servidor do Windows com um Serviço de Sincronização de Sincronização de Sincronização de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2656716560b981481273c3032fc0c7b1a06be8a2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79255096"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Gerir servidores registados com O Sincronizado de Ficheiros Azure
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Fá-lo transformando os seus Servidores Windows numa cache rápida da partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

O seguinte artigo ilustra como registar e gerir um servidor com um Serviço de Sincronização de Armazenamento. Consulte [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter informações sobre como implementar o Azure File Sync de ponta a ponta.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registar/não registar um servidor com serviço de sincronização de armazenamento
Registar um servidor com o Azure File Sync estabelece uma relação de confiança entre o Windows Server e o Azure. Esta relação pode então ser usada para criar *pontos finais* do servidor no servidor, que representam pastas específicas que devem ser sincronizadas com uma partilha de ficheiros Azure (também conhecida como ponto final da *nuvem).* 

### <a name="prerequisites"></a>Pré-requisitos
Para registar um servidor com um Serviço de Sincronização de Armazenamento, tem primeiro de preparar o seu servidor com os pré-requisitos necessários:

* O seu servidor deve estar a executar uma versão suportada do Windows Server. Para mais informações, consulte [os requisitos do sistema Desincronização de Ficheiros Azure e a interoperabilidade](storage-sync-files-planning.md#windows-file-server-considerations).
* Certifique-se de que foi implantado um Serviço de Sincronização de Armazenamento. Para obter mais informações sobre como implementar um Serviço de Sincronização de Armazenamento, consulte como implementar o [Azure File Sync](storage-sync-files-deployment-guide.md).
* Certifique-se de que o servidor está ligado à internet e que o Azure está acessível.
* Desative a Configuração de Segurança Melhorada do IE para administradores com o UI do Gestor do Servidor.
    
    ![Gestor de servidorU com a Configuração de Segurança Melhorada iE destacada](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Certifique-se de que o módulo PowerShell Azure está instalado no seu servidor. Se o seu servidor for membro de um Cluster Failover, cada nó do cluster necessitará do módulo Az. Mais detalhes sobre como instalar o módulo Az podem ser encontrados no [Install and configure Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Recomendamos a utilização da versão mais recente do módulo Az PowerShell para registar/desmarcar um servidor. Se o pacote Az tiver sido previamente instalado neste servidor (e a versão PowerShell neste `Update-Module` servidor for de 5.* ou superior), pode utilizar o cmdlet para atualizar este pacote. 
* Se utilizar um servidor proxy de rede no seu ambiente, configure as definições de proxy no seu servidor para que o agente de sincronização utilize.
    1. Determine o seu endereço IP proxy e o número da porta
    2. Editar estes dois ficheiros:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adicione as linhas na figura 1 (abaixo desta secção) em /System.ServiceModel nos dois ficheiros acima que mudam 127.0.0.1:8888 ao endereço IP correto (substitua 127.0.0.1) e corrija o número da porta (substitua 8888):
    4. Definir as definições de procuração WinHTTP através da linha de comando:
        * Mostre o proxy: netsh winhttp mostrar procuração
        * Definir o proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Redefinir o proxy: netsh winhttp redefinir proxy
        * se isto for configurado após a instalação do agente, reinicie o nosso agente de sincronização: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registe um servidor com serviço de sincronização de armazenamento
Antes de um servidor poder ser utilizado como *ponto final* do servidor num grupo de sincronização de *sincronização*de ficheiros Azure, este deve ser registado com um Serviço de *Sincronização*de Armazenamento . Um servidor só pode ser registado com um Serviço de Sincronização de Armazenamento de cada vez.

#### <a name="install-the-azure-file-sync-agent"></a>Instalar o agente do Azure File Sync
1. [Descarregue o agente Desincronização de Ficheiros Azure](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie o instalador de agente Desincronização de Ficheiros Azure.
    
    ![O primeiro painel do instalador de agente Dessincronizado de Ficheiros Azure](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Certifique-se de que permite atualizações ao agente Dessincronização de ficheiros Azure utilizando a Microsoft Update. É importante porque as correções de segurança críticas e as melhorias de funcionalidades para o pacote do servidor são enviadas através do Microsoft Update.

    ![Certifique-se de que o Microsoft Update está ativado no painel de atualização da Microsoft do instalador de ficheiros Do Tetr](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se o servidor não tiver sido registado anteriormente, o UI de registo do servidor aparecerá imediatamente após a conclusão da instalação.

> [!Important]  
> Se o servidor for membro de um Cluster Failover, o agente Dessincronização de Ficheiros Azure precisa de ser instalado em todos os nós do cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registe o servidor utilizando o UI de registo do servidor
> [!Important]  
> As subscrições do Cloud Solution Provider (CSP) não podem utilizar o UI de registo do servidor. Em vez disso, utilize o PowerShell (abaixo desta secção).

1. Se o UI de registo do servidor não ter começado imediatamente após a conclusão da instalação do agente Dessincronização de ficheiros Azure, pode ser iniciado manualmente executando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Clique em *Iniciar sessão* para aceder à subscrição do Azure. 

    ![Diálogo de abertura da UI de registo do servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Escolha a subscrição correta, o grupo de recursos e o Serviço de Sincronização de Armazenamento do diálogo.

    ![Informações do Serviço de Sincronização de Armazenamento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Na pré-visualização, é necessário mais um inserimento para completar o processo. 

    ![Assine o diálogo](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se o servidor for membro de um Cluster Failover, cada servidor tem de executar o Registo do Servidor. Ao visualizar os servidores registados no Portal Azure, o Azure File Sync reconhece automaticamente cada nó como membro do mesmo Cluster Failover e agrupa-os adequadamente.

#### <a name="register-the-server-with-powershell"></a>Registe o servidor com a PowerShell
Também pode efetuar o registo do servidor através do PowerShell. Esta é a única forma suportada de registo de servidores para subscrições do Cloud Solution Provider (CSP):

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Desregistre o servidor com serviço de sincronização de armazenamento
Existem vários passos que são necessários para desregistar um servidor com um Serviço de Sincronização de Armazenamento. Vamos ver como desmarcar corretamente um servidor.

> [!Warning]  
> Não tente resolver problemas com sincronização, tiering na nuvem ou qualquer outro aspeto do Azure File Sync desregistando e registando um servidor, ou removendo e recriando os pontos finais do servidor, a menos que explicitamente instruído por um engenheiro da Microsoft. Desregistar um servidor e remover pontos finais do servidor é uma operação destrutiva, e os ficheiros hierárquicos nos volumes com pontos finais do servidor não serão "reconectados" às suas localizações na partilha de ficheiros Azure após a recriação dos pontos finais do servidor registado e do servidor, o que resultará em erros de sincronização. Note também que os ficheiros hierárquicos que existam fora de um espaço de nome final do servidor podem ser permanentemente perdidos. Os ficheiros hierárquicos podem existir dentro dos pontos finais do servidor, mesmo que o tiering de nuvem nunca tenha sido ativado.

#### <a name="optional-recall-all-tiered-data"></a>(Opcional) Lembre-se de todos os dados hierárquicos
Se quiser que os ficheiros que estão atualmente disponíveis após a remoção do Azure File Sync (isto é, uma produção, não um teste, ambiente), lembre-se de todos os ficheiros de cada volume que contenham pontos finais do servidor. Desative o tiering da nuvem para todos os pontos finais do servidor e, em seguida, executar o seguinte cmdlet PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Se o volume local que acolhe o ponto final do servidor não `Invoke-StorageSyncFileRecall` tiver espaço livre suficiente para recolher todos os dados hierárquicos, o cmdlet falhará.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Remova o servidor de todos os grupos de sincronização
Antes de desregistar o servidor no Serviço de Sincronização de Armazenamento, todos os pontos finais do servidor nesse servidor devem ser removidos. Isto pode ser feito através do portal Azure:

1. Navegue para o Serviço de Sincronização de Armazenamento onde o seu servidor está registado.
2. Remova todos os pontos finais do servidor para este servidor em cada grupo de sincronização no Serviço de Sincronização de Armazenamento. Isto pode ser realizado clicando à direita no ponto final do servidor relevante no painel do grupo de sincronização.

    ![Remover um ponto final do servidor de um grupo de sincronização](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Isto também pode ser realizado com um simples script PowerShell:

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

#### <a name="unregister-the-server"></a>Desregistrar o servidor
Agora que todos os dados foram recolhidos e o servidor foi removido de todos os grupos de sincronização, o servidor pode não ser registado. 

1. No portal Azure, navegue para a secção de *servidores registados* do Serviço de Sincronização de Armazenamento.
2. Clique no servidor que deseja desregistar e clique em "Desregistar Server".

    ![Servidor de desregistar](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Garantir o Azure File Sync é um bom vizinho no seu datacenter 
Uma vez que o Azure File Sync raramente será o único serviço a funcionar no seu datacenter, poderá querer limitar a utilização da rede e armazenamento do Azure File Sync.

> [!Important]  
> A fixação de limites demasiado baixos afetará o desempenho da sincronização e recuperação de sincronização de ficheiros Azure.

### <a name="set-azure-file-sync-network-limits"></a>Definir limites de rede de sincronização de ficheiros Azure
Pode acelerar a utilização da rede do Azure File Sync utilizando os `StorageSyncNetworkLimit` cmdlets.

> [!Note]  
> Os limites de rede não se aplicam quando um ficheiro hierárquico é acedido ou o cmdlet Invoke-StorageSyncRecall é utilizado.

Por exemplo, pode criar um novo limite de aceleração para garantir que o Azure File Sync não utilize mais de 10 Mbps entre as 9:00 e as 17:00 horas (17:00h) durante a semana de trabalho: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Pode ver o seu limite utilizando o seguinte cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Para remover os limites de rede, utilize `Remove-StorageSyncNetworkLimit`. Por exemplo, o seguinte comando remove todos os limites de rede:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Utilize o QoS de armazenamento do Servidor Windows 
Quando o Azure File Sync estiver hospedado numa máquina virtual que funciona num hospedeiro de virtualização do Windows Server, pode utilizar o Storage QoS (qualidade de armazenamento do serviço) para regular o consumo de IO de armazenamento. A política de QoS de armazenamento pode ser definida como um máximo (ou limite, como a forma como o limite de ArmazensyncNetwork é aplicado acima) ou como um mínimo (ou reserva). A definição de um mínimo em vez de um máximo permite que o Azure File Sync rebente para utilizar a largura de banda de armazenamento disponível se outras cargas de trabalho não estiverem a usá-la. Para mais informações, consulte [Qualidade de Serviço de Armazenamento.](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview)

## <a name="see-also"></a>Consulte também
- [Planear uma implementação da Sincronização de Ficheiros do Azure](storage-sync-files-planning.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
- [Resolver problemas da Sincronização de Ficheiros do Azure](storage-sync-files-troubleshoot.md)
