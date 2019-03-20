---
title: Gerir a capacidade de armazenamento no Azure Stack | Documentos da Microsoft
description: Monitorizar e gerir o espaço de armazenamento disponível para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: conceptual
ms.date: 03/19/2019
ms.author: mabrigg
ms.reviewer: xiaofmao
ms.lastreviewed: 01/14/2019
ms.openlocfilehash: 617696c842ab90fc36c68e74831ffd1d79d14bc4
ms.sourcegitcommit: 12d67f9e4956bb30e7ca55209dd15d51a692d4f6
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2019
ms.locfileid: "58225710"
---
# <a name="manage-storage-capacity-for-azure-stack"></a>Gerir a capacidade de armazenamento para o Azure Stack 

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

As informações neste artigo ajudam o monitor de operador de cloud do Azure Stack e gerir a capacidade de armazenamento da sua implementação do Azure Stack. A infraestrutura de armazenamento do Azure Stack aloca um subconjunto da capacidade de armazenamento total da implementação do Azure Stack, a ser utilizado para **serviços de armazenamento**. Os serviços de armazenamento armazenam dados de um inquilino em partilhas em volumes que correspondem a nós da implementação.

Como um operador de nuvem, tem uma quantidade limitada de armazenamento para trabalhar com. A quantidade de armazenamento é definida pela solução que é implementar. A solução é fornecida pelo seu fornecedor de OEM, quando usar uma solução com vários nó, ou pelo hardware no qual instala o Development Kit do Azure Stack.

Como o Azure Stack não suporta a expansão de capacidade de armazenamento, é importante [monitor](#monitor-shares) o armazenamento disponível para garantir operações eficazes são mantidos.  

Quando a capacidade livre restante de uma partilha de fica limitada, planeie [gerir o espaço de](#manage-available-space) para impedir que as partilhas de falta de capacidade.

As opções para gerir a capacidade incluem:
- Recuperar a capacidade
- Migrar de um contentor

Quando uma partilha é 100% utilizados, o armazenamento já não serviço funções para que a partilha. Para obter assistência em operações para a partilha de restauro, contacte o suporte da Microsoft.

## <a name="understand-volumes-and-shares-containers-and-disks"></a>Compreender os volumes e partilhas, contentores e discos
### <a name="volumes-and-shares"></a>Volumes e partilhas
O *serviço de armazenamento* particiona o armazenamento disponível em separado e iguais volumes alocados para armazenar dados de inquilino. O número de volumes é igual ao número de nós na implementação do Azure Stack:

- Numa implementação de quatro nós, há quatro volumes. Cada volume tem uma única partilha. Numa implementação de vários nós, o número de partilhas não é reduzido, se um nó é removido ou com funcionamento incorreto.
- Se utilizar o Azure Stack Development Kit, há um único volume com uma única partilha.

Uma vez que as partilhas do serviço de armazenamento são para utilização exclusiva de serviços de armazenamento, tem não diretamente modificar, adicionar ou remover todos os ficheiros nas partilhas de. Apenas os serviços de armazenamento devem trabalhar nos ficheiros armazenados nestes volumes.

Partilhas em volumes de armazenar dados de inquilino. Dados de inquilinos incluem blobs de páginas, blobs de blocos, acrescentar blobs, tabelas, filas, bases de dados e relacionadas com arquivos de metadados. Como os objetos de armazenamento (blobs, etc.) individualmente estão contidos dentro de uma única partilha, o tamanho máximo de cada objeto não pode exceder o tamanho de uma partilha. O tamanho máximo de novos objetos depende na capacidade que permanece num compartilhamento como espaço não utilizado quando esse novo objeto é criado.

Quando uma partilha é baixa no espaço livre e ações para [recuperar](#reclaim-capacity) espaço não for concluída com êxito ou está disponível, o operador de cloud do Azure Stack pode migrar os contentores de BLOBs a partir de uma partilha para outro.

- Para obter mais informações sobre contentores e blobs, veja [armazenamento de BLOBs](azure-stack-key-features.md#blob-storage) nos principais recursos e conceitos no Azure Stack.
- Para obter informações sobre o funcionam de utilizadores de inquilino com o armazenamento de BLOBs no Azure Stack, veja [serviços de armazenamento do Azure Stack](/azure/azure-stack/user/azure-stack-storage-overview#azure-stack-storage-services).


### <a name="containers"></a>Contentores
Os utilizadores de inquilino criam contentores que, em seguida, são utilizadas para armazenar dados de Blobs. Enquanto o usuário decide no contentor que colocar blobs, o serviço de armazenamento usa um algoritmo para determinar em qual é o volume para colocar o contentor. O algoritmo geralmente escolhe o volume com o maior espaço disponível.  

Depois de um blob é colocado num contentor, pode aumentar esse blob para utilizar mais espaço. À medida que adiciona novos blobs e existente blobs crescer, o espaço disponível no volume que contém esse contêiner diminui.  

Contentores não estão limitados a uma única partilha. Quando os dados combinados BLOBs num contentor que aumenta a utilização de 80% ou mais de espaço disponível, o contentor entra *estouro* modo. Quando no modo de capacidade excedida, os blobs novas criadas nesse contentor são alocados para um volume diferente que tenha espaço suficiente. Ao longo do tempo, um contentor no modo de estouro pode ter blobs que são distribuídas por vários volumes.

Quando é utilizada a 80% e, em seguida, 90% do espaço disponível num volume, o sistema gera alertas no portal de administrador do Azure Stack. Operadores da nuvem devem reveja a capacidade de armazenamento disponível e planeie a reequilibrar o conteúdo. O serviço de armazenamento deixa de funcionar quando um disco é 100% utilizado e não existem alertas adicionais são geradas.

### <a name="disks"></a>Discos
Os discos VM são adicionados a contentores pelos inquilinos e incluem um disco de sistema operativo. VMs também podem ter um ou mais discos de dados. Ambos os tipos de discos são armazenados como blobs de páginas. A orientação aos inquilinos é colocar cada disco num contentor separado para melhorar o desempenho da VM.
- Cada contentor que retém um disco (blob de páginas) de uma VM é considerado um contêiner anexado à VM que é proprietário do disco.
- Um contentor que não contenha qualquer disco de uma VM é considerado um contêiner gratuito.

As opções para libertar espaço num contêiner anexado [estão limitados](#move-vm-disks).
> [!TIP]  
> Operadores da nuvem não gere diretamente discos, que estão anexados às máquinas virtuais (VMs) que os inquilinos podem adicionar a um contentor. No entanto, ao planejar a gerir o espaço em partilhas de armazenamento, podem ser úteis para compreender como discos se relacionam a contentores e partilhas.

## <a name="monitor-shares"></a>Partilhas de monitor
Utilize o PowerShell ou o portal de administração para monitorizar as partilhas para que possa entender quando o espaço livre é limitado. Ao utilizar o portal, receber alertas sobre partilhas que estão baixas no espaço.    

### <a name="use-powershell"></a>Utilizar o PowerShell
Como um operador de cloud, pode monitorizar a capacidade de armazenamento de uma partilha com o PowerShell **Get-AzsStorageShare** cmdlet. O cmdlet Get-AzsStorageShare devolve o espaço total, alocado e livre em bytes em cada uma das partilhas.   
![Exemplo: Devolver o espaço livre para partilhas](media/azure-stack-manage-storage-shares/free-space.png)

- **Capacidade total** é o espaço total em bytes, que estão disponíveis na partilha. Este espaço é utilizado para dados e metadados que é mantido pelos serviços de armazenamento.
- **Utilizado capacidade** é a quantidade de dados em bytes, que é utilizado pelas todas as extensões dos arquivos que armazenam os dados de inquilino e metadados associados.

### <a name="use-the-administrator-portal"></a>Utilizar o portal de administrador
Como um operador de cloud, pode utilizar o portal de administração para ver a capacidade de todas as partilhas de armazenamento.

1. Inicie sessão para o [do portal de administração](https://adminportal.local.azurestack.external).
2. Selecione **todos os serviços** > **armazenamento** > **partilhas de ficheiros** para abrir a lista de partilha de ficheiros onde pode ver as informações de utilização. 

    ![Exemplo: Partilhas de ficheiros de armazenamento](media/azure-stack-manage-storage-shares/storage-file-shares.png)

   - **TOTAL** é o espaço total em bytes, que estão disponíveis na partilha. Este espaço é utilizado para dados e metadados que é mantido pelos serviços de armazenamento.
   - **UTILIZADO** é a quantidade de dados em bytes, que é utilizado pelas todas as extensões dos arquivos que armazenam os dados de inquilino e metadados associados.

### <a name="storage-space-alerts"></a>Alertas de espaço de armazenamento
Quando utiliza o portal de administração, receber alertas sobre partilhas que estão baixas no espaço.

> [!IMPORTANT]
> Como um operador de cloud, mantenha partilhas cheguem a utilização completa. Quando uma partilha é 100% utilizados, o armazenamento já não serviço funções para que a partilha. Para recuperar o espaço livre e restaurar as operações num compartilhamento que é 100% utilizadas, tem de contactar o suporte da Microsoft.

**Aviso**: Quando uma partilha de ficheiros é mais de 80% utilizados, receberá um *aviso* alerta no portal de administração: ![Exemplo: Alerta de aviso](media/azure-stack-manage-storage-shares/alert-warning.png)


**Crítico**: Quando uma partilha de ficheiros é mais de 90% utilizados, receberá um *crítico* alerta no portal de administração: ![Exemplo: Alerta crítico](media/azure-stack-manage-storage-shares/alert-critical.png)

**Ver detalhes**: No portal de administração que pode abrir os detalhes de um alerta para ver as opções de atenuação: ![Exemplo: Ver detalhes do alerta](media/azure-stack-manage-storage-shares/alert-details.png)


## <a name="manage-available-space"></a>Gerir o espaço disponível
Quando for necessário para o espaço livre num compartilhamento, utilize os métodos menos invasivas pela primeira vez. Por exemplo, tente recuperar o espaço antes de optar por migrar de um contentor.  

### <a name="reclaim-capacity"></a>Recuperar a capacidade
*Esta opção aplica-se para implementações de vários nós e o Kit de desenvolvimento do Azure Stack.*

Pode recuperar a capacidade utilizada por contas de inquilino foram eliminadas. Esta capacidade é automaticamente recuperado quando os dados [período de retenção](azure-stack-manage-storage-accounts.md#set-the-retention-period) for atingido, ou pode agir para recuperá-lo imediatamente.

Para obter mais informações, consulte [recuperar a capacidade](azure-stack-manage-storage-accounts.md#reclaim) em gerir recursos de armazenamento.

### <a name="migrate-a-container-between-volumes"></a>Migrar contentores entre volumes
*Esta opção só se aplica a implementações de vários nós.*

Devido a padrões de utilização do inquilino, algumas partilhas de inquilino utilizam mais espaço que outras pessoas. O resultado pode ser uma partilha de que a execução lenta em espaço antes de outras partilhas que estão relativamente não utilizados.

Pode tentar libertar espaço num compartilhamento de usado em excesso ao migrar manualmente alguns contentores de BLOBs para uma partilha diferente. Pode migrar vários contentores mais pequenos para uma única partilha, com capacidade para conter todos eles. Pode utilizar a migração para mover *gratuita* contentores. Gratuitos contentores são contentores que não contêm um disco para uma VM.   

Migração consolida todas as um blob de contentores na partilha de novo.

- Se um contentor entrou em modo de estouro e colocou blobs em volumes adicionais, a nova partilha tem de ter capacidade suficiente para conter todos os blobs para o contentor de que migrar. Isto inclui os blobs que se encontram em partilhas adicionais.

- O cmdlet do PowerShell *Get-AzsStorageContainer* identifica apenas o espaço utilizado no volume inicial para um contentor. O cmdlet não identifica o espaço utilizado por blobs de colocar em volumes adicionais. Por conseguinte, o tamanho máximo de um contentor pode não ser evidente. É possível que a consolidação de um contentor numa nova partilha de pode enviar essa nova partilha numa condição de estouro de onde ele coloca os dados em partilhas adicionais. Como resultado, poderá ter de rebalancear as partilhas novamente.

- Se não têm permissões para um grupo de recursos e não é possível utilizar o PowerShell para consultar os volumes adicionais para dados de capacidade excedida, trabalhe com o proprietário desses grupos de recursos e contentores para compreender o tamanho total dos dados de migração antes de migrar esses dados.  

> [!IMPORTANT]
> Migração de blobs para um contentor é uma operação offline, que requer a utilização do PowerShell. Até que a migração estiver concluída, todos os blobs para o contentor que está a migrar permanecerem offline e não podem ser utilizados. Também deve evitar a atualização do Azure Stack, até que seja concluída a migração em curso todos os.

#### <a name="to-migrate-containers-using-powershell"></a>A migração de contentores com o PowerShell
1. Confirme se tem [do Azure PowerShell instalada e configurada](https://azure.microsoft.com/documentation/articles/powershell-install-configure/). Para obter mais informações, veja [Using Azure PowerShell with Azure Resource Manager (Utilizar o Azure PowerShell com o Azure Resource Manager)](https://go.microsoft.com/fwlink/?LinkId=394767).
2. Examine o contentor para compreender os dados que estão na partilha que pretende migrar. Para identificar os contentores de Release candidate melhor para migração de um volume, utilize o **Get-AzsStorageContainer** cmdlet:

   ```PowerShell  
   $farm_name = (Get-AzsStorageFarm)[0].name
   $shares = Get-AzsStorageShare -FarmName $farm_name
   $containers = Get-AzsStorageContainer -ShareName $shares[0].ShareName -FarmName $farm_name
   ```
   Em seguida, examine $containers:

   ```PowerShell
   $containers
   ```

   ![Exemplo: $Containers](media/azure-stack-manage-storage-shares/containers.png)

3. Identificar as partilhas de destino melhor para manter o contentor que é migrar:

   ```PowerShell
   $destinationshares = Get-AzsStorageShare -SourceShareName
   $shares[0].ShareName -Intent ContainerMigration
   ```

   Em seguida, examine $destinationshares:

   ```PowerShell 
   $destinationshares
   ```

   ![Exemplo: $destination partilhas](media/azure-stack-manage-storage-shares/examine-destinationshares.png)

4. Inicie a migração para um contentor. A migração é assíncrona. Se começar a migração de contentores adicionais antes de concluir a migração primeiro, utilize o ID da tarefa para controlar o estado de cada.

   ```PowerShell
   $job_id = Start-AzsStorageContainerMigration -StorageAccountName $containers[0].Accountname -ContainerName $containers[0].Containername -ShareName $containers[0].Sharename -DestinationShareUncPath $destinationshares[0].UncPath -FarmName $farm_name
   ```

   Em seguida, examine $jobId. No exemplo seguinte, substitua *d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0* com o ID da tarefa que pretende examinar:

   ```PowerShell
   $jobId
   d62f8f7a-8b46-4f59-a8aa-5db96db4ebb0
   ```

5. Utilize o ID da tarefa para verificar o estado da tarefa de migração. Quando a migração de contentor estiver concluída, **MigrationStatus** está definida como **concluída**.

   ```PowerShell 
   Get-AzsStorageContainerMigrationStatus -JobId $job_id -FarmName $farm_name
   ```

   ![Exemplo: Estado da migração](media/azure-stack-manage-storage-shares/migration-status1.png)

6. Pode cancelar uma tarefa de migração em curso. Cancelar a migração em tarefas são processadas de forma assíncrona. Pode controlar o cancelamento utilizando $jobid:

   ```PowerShell
   Stop-AzsStorageContainerMigration -JobId $job_id -FarmName $farm_name
   ```

   ![Exemplo: Estado de reversão](media/azure-stack-manage-storage-shares/rollback.png)

7. Pode executar o comando no passo 6 novamente, até que o estado confirma que a tarefa de migração é **cancelado**:  

    ![Exemplo: Estado cancelado](media/azure-stack-manage-storage-shares/cancelled.png)

### <a name="move-vm-disks"></a>Mover os discos VM
*Esta opção só se aplica a implementações de vários nós.*

O método mais extremo para gerir o espaço envolve a movimentação de discos de máquina virtual. Como mover um contentor anexado (um que contém um disco VM) é complexo, contacte o Microsoft Support para realizar esta ação.

## <a name="next-steps"></a>Próximos Passos
Saiba mais sobre [máquinas virtuais de oferta para os utilizadores](azure-stack-tutorial-tenant-vm.md).
