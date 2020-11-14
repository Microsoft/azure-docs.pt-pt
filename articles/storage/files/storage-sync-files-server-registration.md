---
title: Gerir servidores registados com Azure File Sync Microsoft Docs
description: Saiba como registar e desagregá-lo num Servidor do Windows com um Serviço de Sincronização de Sincronização de Sincronização de Ficheiros de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 194b0f2ff94197fe11c189e97dbc65c9d0367932
ms.sourcegitcommit: 9826fb9575dcc1d49f16dd8c7794c7b471bd3109
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/14/2020
ms.locfileid: "94630588"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Gerir servidores registados com Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Fá-lo transformando os seus Servidores Windows numa cache rápida da sua partilha de ficheiros Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

O seguinte artigo ilustra como registar e gerir um servidor com um Serviço de Sincronização de Armazenamento. Veja [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter informações sobre como implementar o Azure File Sync de ponta a ponta.

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registar/não registar um servidor com serviço de sincronização de armazenamento
Registar um servidor com o Azure File Sync estabelece uma relação de confiança entre o Windows Server e o Azure. Esta relação pode então ser usada para criar *pontos finais* do servidor no servidor, que representam pastas específicas que devem ser sincronizadas com uma partilha de ficheiros Azure (também conhecida como *ponto final de nuvem).* 

### <a name="prerequisites"></a>Pré-requisitos
Para registar um servidor com um Serviço de Sincronização de Armazenamento, tem primeiro de preparar o seu servidor com os requisitos necessários:

* O seu servidor deve estar a executar uma versão suportada do Windows Server. Para obter mais informações, consulte [os requisitos do sistema Azure File Sync e interoperabilidade](storage-sync-files-planning.md#windows-file-server-considerations).
* Certifique-se de que foi implantado um Serviço de Sincronização de Armazenamento. Para obter mais informações sobre como implementar um Serviço de Sincronização de Armazenamento, consulte [Como implementar o Azure File Sync](storage-sync-files-deployment-guide.md).
* Certifique-se de que o servidor está ligado à internet e que o Azure está acessível.
* Desative a configuração de segurança melhorada do IE para administradores com o UI do Gestor do Servidor.
    
    ![UI do gestor do servidor com a configuração de segurança melhorada do IE em destaque](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Certifique-se de que o módulo Azure PowerShell está instalado no seu servidor. Se o seu servidor for membro de um Cluster failover, cada nó no cluster necessitará do módulo Az. Mais detalhes sobre como instalar o módulo Az podem ser encontrados na [Instalação e configurar a Azure PowerShell](/powershell/azure/install-Az-ps).

    > [!Note]  
    > Recomendamos a utilização da versão mais recente do módulo Az PowerShell para registar/não registar um servidor. Se o pacote Az tiver sido previamente instalado neste servidor (e a versão PowerShell neste servidor for 5.* ou superior), pode utilizar o `Update-Module` cmdlet para atualizar este pacote. 
* Se utilizar um servidor de procuração de rede no seu ambiente, configufique as definições de procuração no seu servidor para que o agente de sincronização possa utilizar.
    1. Determine o seu endereço IP proxy e número de porta
    2. Editar estes dois ficheiros:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adicione as linhas na figura 1 (por baixo desta secção) em /System.ServiceModel nos dois ficheiros acima alterando 127.0.0.0.1:8888 para o endereço IP correto (substitua 127.0.0.1) e número de porta correto (substitua 8888):
    4. Defina as definições de procuração WinHTTP através da linha de comando:
        * Mostrar o proxy: netsh winhttp show proxy
        * Definir o proxy: netsh winhttp set proxy 127.0.0.1:8888
        * Repor o proxy: netsh winhttp reset proxy
        * se isto for configurado após a instalação do agente, em seguida, reiniciar o nosso agente de sincronização: net stop filesyncsvc
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registar um servidor com serviço de sincronização de armazenamento
Antes de um servidor poder ser utilizado como *ponto final de servidor* num grupo de sincronização de *ficheiros* Azure, este deve ser registado num *Serviço de Sincronização de Armazenamento*. Um servidor só pode ser registado com um Serviço de Sincronização de Armazenamento de cada vez.

#### <a name="install-the-azure-file-sync-agent"></a>Instalar o agente do Azure File Sync
1. [Descarregue o agente Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie o instalador do agente Azure File Sync.
    
    ![O primeiro painel do instalador de agente Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Certifique-se de que ativa as atualizações para o agente Azure File Sync utilizando o Microsoft Update. É importante porque as correções de segurança críticas e as melhorias de funcionalidades no pacote do servidor são enviadas através do Microsoft Update.

    ![Certifique-se de que o Microsoft Update está ativado no painel microsoft update do instalador de agente Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se o servidor não tiver sido registado anteriormente, o UI de registo do servidor aparecerá imediatamente após completar a instalação.

> [!Important]  
> Se o servidor for membro de um Cluster Failover, o agente Azure File Sync tem de ser instalado em todos os nós do cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registar o servidor utilizando o UI de registo de servidor
1. Se o UI de registo do servidor não tiver começado imediatamente após a conclusão da instalação do agente Azure File Sync, pode ser iniciado manualmente executando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe` .
2. Clique *em Iniciar s-in* para aceder à subscrição do Azure. 

    ![Diálogo de abertura do UI de registo do servidor](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Escolha a subscrição correta, o grupo de recursos e o Serviço de Sincronização de Armazenamento a partir do diálogo.

    ![Informações do Serviço de Sincronização de Armazenamento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Na pré-visualização, é necessário mais um pré-aviso para concluir o processo. 

    ![Assine no diálogo](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se o servidor for membro de um Cluster failover, cada servidor precisa de executar o Registo do Servidor. Quando vê os servidores registados no Portal Azure, o Azure File Sync reconhece automaticamente cada nó como membro do mesmo Cluster Failover e agrupa-os adequadamente.

#### <a name="register-the-server-with-powershell"></a>Registar o servidor com PowerShell
Também pode efetuar o registo do servidor através do PowerShell. 

```powershell
Register-AzStorageSyncServer -ResourceGroupName "<your-resource-group-name>" -StorageSyncServiceName "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Desinsusar o servidor com o Serviço de Sincronização de Armazenamento
Existem vários passos que são necessários para desregistar um servidor com um Serviço de Sincronização de Armazenamento. Vamos ver como desagregar um servidor corretamente.

> [!Warning]  
> Não tente resolver problemas com sincronização, nivelamento em nuvem ou qualquer outro aspeto do Azure File Sync não registando e registando um servidor, ou removendo e recriando os pontos finais do servidor, a menos que seja explicitamente instruído por um engenheiro da Microsoft. Não registar um servidor e remover pontos finais do servidor é uma operação destrutiva, e ficheiros hierárquicos nos volumes com pontos finais do servidor não serão "reconectados" às suas localizações na partilha de ficheiros Azure após a recriação dos pontos finais do servidor registado e do servidor, o que resultará em erros de sincronização. Note também que os ficheiros hierárquicos que existem fora de um espaço de nome do ponto final do servidor podem ser permanentemente perdidos. Podem existir ficheiros tiered nos pontos finais do servidor, mesmo que o tiering da nuvem nunca tenha sido ativado.

#### <a name="optional-recall-all-tiered-data"></a>(Opcional) Lembre-se de todos os dados hierárquicos
Se quiser que os ficheiros que estão atualmente nivelados estejam disponíveis após a remoção do Azure File Sync (isto é, uma produção, não um teste, ambiente), lembre-se de todos os ficheiros de cada volume que contenha pontos finais do servidor. Desative o nível da nuvem para todos os pontos finais do servidor e, em seguida, executar o seguinte cmdlet PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Se o volume local que hospeda o ponto final do servidor não tiver espaço livre suficiente para recordar todos os dados hierárquicos, o `Invoke-StorageSyncFileRecall` cmdlet falhará.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Remova o servidor de todos os grupos de sincronização
Antes de não registar o servidor no Serviço de Sincronização de Armazenamento, todos os pontos finais do servidor nesse servidor devem ser removidos. Isto pode ser feito através do portal Azure:

1. Navegue para o Serviço de Sincronização de Armazenamento onde o seu servidor está registado.
2. Remova todos os pontos finais do servidor para este servidor em cada grupo de sincronização no Serviço de Sincronização de Armazenamento. Isto pode ser conseguido clicando à direita no ponto final do servidor relevante no painel de grupo sincronizado.

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

#### <a name="unregister-the-server"></a>Desagregar o servidor
Agora que todos os dados foram recolhidos e o servidor foi removido de todos os grupos de sincronização, o servidor pode não estar registado. 

1. No portal Azure, navegue para a secção de *servidores registados* do Serviço de Sincronização de Armazenamento.
2. Clique no servidor que pretende não registar e clique em "Não registar servidor".

    ![Servidor não registador](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Garantir que o Azure File Sync é um bom vizinho no seu datacenter 
Uma vez que o Azure File Sync raramente será o único serviço em execução no seu datacenter, é melhor limitar a utilização da rede e armazenamento do Azure File Sync.

> [!Important]  
> A definição de limites demasiado baixos afetará o desempenho da sincronização e recolha de sincronização e recuperação de Ficheiros Azure.

### <a name="set-azure-file-sync-network-limits"></a>Definir limites de rede de sincronização de ficheiros Azure
Pode acelerar a utilização da rede do Azure File Sync utilizando os `StorageSyncNetworkLimit` cmdlets.

> [!Note]  
> Os limites de rede não se aplicam quando um ficheiro hierárquico é acedido.

Por exemplo, pode criar um novo limite de aceleração para garantir que o Azure File Sync não utilize mais de 10 Mbps entre as 9:00 e as 17:00 (17:00h) durante a semana de trabalho: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Pode ver o seu limite utilizando o seguinte cmdlet:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Para eliminar os limites de rede, utilize `Remove-StorageSyncNetworkLimit` . Por exemplo, o seguinte comando remove todos os limites de rede:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Use o armazenamento do Windows Server QoS 
Quando o Azure File Sync estiver hospedado numa máquina virtual em execução num anfitrião de virtualização do Windows Server, pode utilizar o Storage QoS (qualidade de serviço de armazenamento) para regular o consumo de IO de armazenamento. A política de Armazenamento QoS pode ser definida como um limite máximo (ou limite, como o limite de ArmazenamentoSyncNetwork é aplicado acima) ou como um mínimo (ou reserva). Definir um mínimo em vez de um máximo permite que o Azure File Sync rebente para utilizar a largura de banda de armazenamento disponível se outras cargas de trabalho não estiverem a usá-lo. Para mais informações, consulte [a Qualidade de Serviço de Armazenamento.](/windows-server/storage/storage-qos/storage-qos-overview)

## <a name="see-also"></a>Ver também
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
- [Resolver problemas do Azure File Sync](storage-sync-files-troubleshoot.md)