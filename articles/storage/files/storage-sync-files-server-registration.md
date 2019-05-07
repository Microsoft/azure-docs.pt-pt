---
title: Gerir servidores registados com o Azure File Sync | Documentos da Microsoft
description: Saiba como registar e anular o registo do Windows Server com um serviço de sincronização de armazenamento de sincronização de ficheiros de Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 07/19/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 6b9996716621137945b5aeb2f0699bf1bddba4a6
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65189985"
---
# <a name="manage-registered-servers-with-azure-file-sync"></a>Gerir servidores registados com o Azure File Sync
O Azure File Sync permite-lhe centralizar as partilhas de ficheiros da sua organização nos Ficheiros do Azure sem abdicar da flexibilidade, do desempenho e da compatibilidade de um servidor de ficheiros no local. Ele faz isso ao transformar os seus servidores do Windows numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente (incluindo SMB, NFS e FTPS) e pode ter o número de caches que precisar em todo o mundo.

O artigo seguinte ilustra como registar e gerir um servidor com um serviço de sincronização de armazenamento. Ver [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md) para obter informações sobre como implementar o Azure File Sync ponto-a-ponto.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="registerunregister-a-server-with-storage-sync-service"></a>Registre-se/anular o registo de um servidor com o serviço de sincronização de armazenamento
Registar um servidor com o Azure File Sync estabelece uma relação de confiança entre o Windows Server e o Azure. Esta relação, em seguida, pode ser utilizada para criar *pontos finais do servidor* no servidor, que representam as pastas específicas que devem ser sincronizadas com uma partilha de ficheiros do Azure (também conhecido como um *ponto final da cloud*). 

### <a name="prerequisites"></a>Pré-requisitos
Para registar um servidor com um serviço de sincronização de armazenamento, primeiro tem de preparar o seu servidor com os pré-requisitos necessários:

* O servidor deve estar a executar uma versão suportada do Windows Server. Para obter mais informações, consulte [requisitos de sistema de sincronização de ficheiros do Azure e interoperabilidade](storage-sync-files-planning.md#azure-file-sync-system-requirements-and-interoperability).
* Certifique-se de que um serviço de sincronização de armazenamento foi implementado. Para obter mais informações sobre como implementar um serviço de sincronização de armazenamento, consulte [como implementar o Azure File Sync](storage-sync-files-deployment-guide.md).
* Certifique-se de que o servidor está ligado à internet e que o Azure está acessível.
* Desative a configuração de segurança avançada do IE para os administradores com a IU do Gestor de servidor.
    
    ![IU de Gestor de servidor com a configuração avançada do IE segurança realçado](media/storage-sync-files-server-registration/server-manager-ie-config.png)

* Certifique-se de que o módulo Azure PowerShell está instalado no seu servidor. Se o servidor for um membro de um Cluster de ativação pós-falha, cada nó do cluster irá exigir o módulo de Az. Podem encontrar mais detalhes sobre como instalar o módulo de Az sobre o [instalar e configurar o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

    > [!Note]  
    > Recomendamos que utilize a versão mais recente do módulo do PowerShell de Az para registar/anular o registo de um servidor. Se o pacote de Az tiver sido instalado anteriormente neste servidor (e a versão de PowerShell neste servidor é 5.* ou superior), pode usar o `Update-Module` cmdlet para atualizar este pacote. 
* Se utilizar um servidor de proxy de rede no seu ambiente, configure as definições de proxy no seu servidor para o agente de sincronização utilizar.
    1. Determinar o número de porta e o endereço IP do proxy
    2. Edite esses dois arquivos:
        * C:\Windows\Microsoft.NET\Framework64\v4.0.30319\Config\machine.config
        * C:\Windows\Microsoft.NET\Framework\v4.0.30319\Config\machine.config
    3. Adicione as linhas na figura 1 (abaixo nesta secção) em /System.ServiceModel nos ficheiros de dois acima alterar 127.0.0.1:8888 para o endereço IP correto (substitua 127.0.0.1) e o número de porta correto (substitua 8888):
    4. Configurar as definições de proxy de WinHTTP via linha de comando:
        * Mostrar o proxy: netsh winhttp Mostrar proxy
        * Definir o proxy: netsh winhttp define 127.0.0.1:8888 de proxy
        * Repor o proxy: netsh winhttp repor proxy
        * Se se tratar de configuração após a instalação do agente, em seguida, reinicie o nosso agente de sincronização: filesyncsvc net stop
    
```XML
    Figure 1:
    <system.net>
        <defaultProxy enabled="true" useDefaultCredentials="true">
            <proxy autoDetect="false" bypassonlocal="false" proxyaddress="http://127.0.0.1:8888" usesystemdefault="false" />
        </defaultProxy>
    </system.net>
```    

### <a name="register-a-server-with-storage-sync-service"></a>Registar um servidor com o serviço de sincronização de armazenamento
Antes de um servidor de pode ser utilizado como uma *ponto final do servidor* num Azure File Sync *grupo de sincronização*, tem de ser registado com um *serviço de sincronização de armazenamento*. Um servidor só pode ser registrado com um serviço de sincronização de armazenamento de cada vez.

#### <a name="install-the-azure-file-sync-agent"></a>Instalar o agente do Azure File Sync
1. [Transferir o agente do Azure File Sync](https://go.microsoft.com/fwlink/?linkid=858257).
2. Inicie o instalador do agente do Azure File Sync.
    
    ![O primeiro painel do programa de instalação de agente do Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-1.png)

3. Certifique-se de que permitir que o agente de sincronização de ficheiros do Azure utilizando o Microsoft Update. É importante porque correções críticas de segurança e aprimoramentos de recursos para o pacote do servidor são fornecidos através do Microsoft Update.

    ![Certifique-se o que Microsoft Update está ativado no painel do Microsoft Update do programa de instalação de agente do Azure File Sync](media/storage-sync-files-server-registration/install-afs-agent-2.png)

4. Se o servidor não foi registado anteriormente, o registo do servidor da interface do Usuário será apresentada imediatamente depois de concluir a instalação.

> [!Important]  
> Se o servidor for um membro de um Cluster de ativação pós-falha, o agente de sincronização de ficheiros do Azure tem de ser instalado em cada nó do cluster.

#### <a name="register-the-server-using-the-server-registration-ui"></a>Registar o servidor com o registo do servidor da interface do Usuário
> [!Important]  
> Subscrições do fornecedor de soluções (CSP) de cloud não é possível utilizar o registo do servidor da interface do Usuário. Em vez disso, utilize o PowerShell (abaixo nesta secção).

1. Se o registo do servidor da interface do Usuário não foram iniciados imediatamente depois de concluir a instalação do agente do Azure File Sync, que pode ser iniciado manualmente, executando `C:\Program Files\Azure\StorageSyncAgent\ServerRegistration.exe`.
2. Clique em *início de sessão* para aceder à sua subscrição do Azure. 

    ![Caixa de diálogo de abertura do registo do servidor da interface do Usuário](media/storage-sync-files-server-registration/server-registration-ui-1.png)

3. Escolha a subscrição correta, o grupo de recursos e o serviço de sincronização de armazenamento na caixa de diálogo.

    ![Informações do serviço de sincronização de armazenamento](media/storage-sync-files-server-registration/server-registration-ui-2.png)

4. Em pré-visualização, um mais início de sessão, é necessário para concluir o processo. 

    ![Inicie sessão na caixa de diálogo](media/storage-sync-files-server-registration/server-registration-ui-3.png)

> [!Important]  
> Se o servidor for um membro de um Cluster de ativação pós-falha, cada servidor tem de executar o registo do servidor. Ao visualizar os servidores registrados no Portal do Azure, Azure File Sync automaticamente reconhece a cada nó como um membro do mesmo Cluster de ativação pós-falha e as agrupa em conjunto adequadamente.

#### <a name="register-the-server-with-powershell"></a>Registar o servidor com o PowerShell
Também pode efetuar o registo do servidor através do PowerShell. Esta é a única forma compatível de registo do servidor para as subscrições do fornecedor de soluções Cloud (CSP):

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll"
Login-AzStorageSync -SubscriptionID "<your-subscription-id>" -TenantID "<your-tenant-id>"
Register-AzStorageSyncServer -SubscriptionId "<your-subscription-id>" - ResourceGroupName "<your-resource-group-name>" - StorageSyncService "<your-storage-sync-service-name>"
```

### <a name="unregister-the-server-with-storage-sync-service"></a>Anular o registo do servidor com o serviço de sincronização de armazenamento
Há várias etapas necessárias para anular o registo de um servidor com um serviço de sincronização de armazenamento. Vamos dar uma olhada em como corretamente anular o registo de um servidor.

> [!Warning]  
> Não tente resolver problemas com a sincronização, na cloud em camadas ou qualquer outro aspecto do Azure File Sync ao anular o registo e registar um servidor, ou remover e recriar os pontos de extremidade do servidor, a menos que explicitamente instruído para por um engenheiro da Microsoft. Anular o registo de um servidor e a remoção de pontos finais do servidor são uma operação destrutiva e ficheiros em camadas nos volumes com pontos finais do servidor serão não ser "novamente" para as respetivas localizações na partilha de ficheiros do Azure depois do servidor registado e pontos finais do servidor recriado, que irá resultar em sincronia erros. Observe também, os ficheiros em camadas existentes fora de um espaço de nomes de ponto final de servidor podem ser permanentemente perdidos. Ficheiros em camada podem existir no servidor de pontos finais, mesmo em camada de cloud nunca tiverem sido ativadas.

#### <a name="optional-recall-all-tiered-data"></a>(Opcional) Lembre-se todos os dados em camadas
Se gostaria de ficheiros que estão atualmente em camadas para estar disponível após a remoção do Azure File Sync (ou seja, esta é uma de produção, não um teste, ambiente), lembre-se todos os ficheiros em cada volume que contém os pontos finais do servidor. Desativar a camada para todos os pontos finais do servidor de cloud e, em seguida, execute o seguinte cmdlet do PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <a-volume-with-server-endpoints-on-it>
```

> [!Warning]  
> Se o volume local que aloja o ponto final do servidor não tem espaço livre suficiente para lembrar todos os dados em camadas, o `Invoke-StorageSyncFileRecall` cmdlet irá falhar.  

#### <a name="remove-the-server-from-all-sync-groups"></a>Remover o servidor de todos os grupos de sincronização
Antes de anular o registo do servidor no serviço de sincronização de armazenamento, tem de ser removidos todos os pontos finais do servidor nesse servidor. Isso pode ser feito através do portal do Azure:

1. Navegue para o serviço de sincronização de armazenamento onde o servidor está registado.
2. Remova todos os pontos finais do servidor para este servidor em cada grupo de sincronização no serviço de sincronização de armazenamento. Isso pode ser feito clicando com o ponto final de servidor relevante no painel do grupo de sincronização.

    ![Remover um ponto final do servidor de um grupo de sincronização](media/storage-sync-files-server-registration/sync-group-server-endpoint-remove-1.png)

Também pode ser feito com um simple script do PowerShell:

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

#### <a name="unregister-the-server"></a>Anular o registo do servidor
Agora que todos os dados foram recuperados e o servidor foi removido de todos os grupos de sincronização, o servidor pode ser anulado. 

1. No portal do Azure, navegue para o *servidores registados* secção do serviço de sincronização de armazenamento.
2. Clique com o botão direito no servidor que pretende anular o registo e clique em "Anular registo do servidor".

    ![Anular o registo do servidor](media/storage-sync-files-server-registration/unregister-server-1.png)

## <a name="ensuring-azure-file-sync-is-a-good-neighbor-in-your-datacenter"></a>Garantir que o Azure File Sync é um bom vizinho no seu datacenter 
Uma vez que o Azure File Sync raramente será o único serviço em execução no seu datacenter, pode querer limitar a utilização de rede e armazenamento do Azure File Sync.

> [!Important]  
> A definição demasiado baixa limites terá impacto no desempenho de sincronização do Azure File Sync e lembre-se.

### <a name="set-azure-file-sync-network-limits"></a>Definir limites de rede do Azure File Sync
É possível limitar a utilização de rede do Azure File Sync utilizando o `StorageSyncNetworkLimit` cmdlets.

> [!Note]  
> Limites de rede não se aplicam quando um ficheiro em camadas é acedido ou o cmdlet Invoke-StorageSyncFileRecall é utilizado.

Por exemplo, pode criar um novo limite de limitação para se certificar de que o Azure File Sync não usa mais de 10 Mbps entre as 09:00 e as 5 pm (17:00 h) durante a semana de trabalho: 

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
New-StorageSyncNetworkLimit -Day Monday, Tuesday, Wednesday, Thursday, Friday -StartHour 9 -EndHour 17 -LimitKbps 10000
```

Pode ver o seu limite utilizando o cmdlet seguinte:

```powershell
Get-StorageSyncNetworkLimit # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

Para remover limites de rede, utilize `Remove-StorageSyncNetworkLimit`. Por exemplo, o seguinte comando remove todos os limites de rede:

```powershell
Get-StorageSyncNetworkLimit | ForEach-Object { Remove-StorageSyncNetworkLimit -Id $_.Id } # assumes StorageSync.Management.ServerCmdlets.dll is imported
```

### <a name="use-windows-server-storage-qos"></a>Utilizar o QoS de armazenamento do Windows Server 
Quando o Azure File Sync está alojado numa máquina virtual em execução num anfitrião de Virtualização do Windows Server, pode utilizar o QoS de armazenamento (qualidade de serviço de armazenamento) de regular o consumo de e/s de armazenamento. A política de QoS de armazenamento pode ser definida como um máximo (ou limite, como a forma como o limite de StorageSyncNetwork é imposta acima) ou como um mínimo (ou reserva). Definir um mínimo, em vez de um máximo permite que o Azure File Sync expandir para utilizar a largura de banda de armazenamento disponível se outras cargas de trabalho não estão a utilizá-la. Para obter mais informações, consulte [qualidade de serviço de armazenamento](https://docs.microsoft.com/windows-server/storage/storage-qos/storage-qos-overview).

## <a name="see-also"></a>Consulte também
- [Planear uma implementação do Azure File Sync](storage-sync-files-planning.md)
- [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
- [Monitorizar a sincronização de ficheiros do Azure](storage-sync-files-monitoring.md)
- [Resolver problemas da sincronização de ficheiros do Azure](storage-sync-files-troubleshoot.md)
