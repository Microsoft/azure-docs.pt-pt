---
title: Planejando uma implantação de Sincronização de Arquivos do Azure | Microsoft Docs
description: Saiba o que deve ser considerado ao planejar uma implantação de arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 2/7/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9c46181d5ab449d28c2e2e93cc583a3551f114bc
ms.sourcegitcommit: 388c8f24434cc96c990f3819d2f38f46ee72c4d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/27/2019
ms.locfileid: "70061752"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planear uma implementação da Sincronização de Ficheiros do Azure
Use Sincronização de Arquivos do Azure para centralizar os compartilhamentos de arquivos da sua organização em arquivos do Azure, mantendo, ao mesmo tempo, a flexibilidade, o desempenho e a compatibilidade de um servidor de arquivos local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Você pode usar qualquer protocolo que esteja disponível no Windows Server para acessar seus dados localmente, incluindo SMB, NFS e FTPS. Você pode ter quantos caches forem necessários em todo o mundo.

Este artigo descreve considerações importantes para uma implantação de Sincronização de Arquivos do Azure. É recomendável que você também leia [planejando uma implantação de arquivos do Azure](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Terminologia Sincronização de Arquivos do Azure
Antes de entrar nos detalhes do planejamento de uma implantação de Sincronização de Arquivos do Azure, é importante entender a terminologia.

### <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento
O serviço de sincronização de armazenamento é o recurso de nível superior do Azure para Sincronização de Arquivos do Azure. O recurso de serviço de sincronização de armazenamento é um par do recurso de conta de armazenamento e pode ser implantado da mesma forma em grupos de recursos do Azure. Um recurso de nível superior distinto do recurso de conta de armazenamento é necessário porque o serviço de sincronização de armazenamento pode criar relações de sincronização com várias contas de armazenamento por meio de vários grupos de sincronização. Uma assinatura pode ter vários recursos do serviço de sincronização de armazenamento implantados.

### <a name="sync-group"></a>Grupo de sincronização
Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Se, por exemplo, você tiver dois conjuntos distintos de arquivos que deseja gerenciar com Sincronização de Arquivos do Azure, você criaria dois grupos de sincronização e adicionará pontos de extremidade diferentes a cada grupo de sincronização. Um serviço de sincronização de armazenamento pode hospedar quantos grupos de sincronização forem necessários.  

### <a name="registered-server"></a>Servidor registado
O objeto de servidor registrado representa uma relação de confiança entre seu servidor (ou cluster) e o serviço de sincronização de armazenamento. Você pode registrar quantos servidores desejar em uma instância do serviço de sincronização de armazenamento. No entanto, um servidor (ou cluster) pode ser registrado com apenas um serviço de sincronização de armazenamento por vez.

### <a name="azure-file-sync-agent"></a>Agente de Sincronização de Arquivos do Azure
O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure. O agente de Sincronização de Arquivos do Azure tem três componentes principais: 
- **FileSyncSvc.exe**: O serviço Windows em segundo plano que é responsável por monitorar alterações nos pontos de extremidade do servidor e para iniciar sessões de sincronização para o Azure.
- **StorageSync.sys**: O filtro do sistema de arquivos Sincronização de Arquivos do Azure, que é responsável por enfileirar arquivos para arquivos do Azure (quando a disposição em camadas da nuvem está habilitada).
- **Cmdlets de gerenciamento do PowerShell**: Cmdlets do PowerShell que você usa para interagir com o provedor de recursos do Azure Microsoft. StorageSync. Você pode encontrá-los nos seguintes locais (padrão):
    - C:\Arquivos de Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Ponto de extremidade do servidor
Os pontos finais de servidor representam uma localização específica num servidor registado, como uma pasta num volume do servidor. Vários pontos de extremidade de servidor podem existir no mesmo volume se seus namespaces não se sobrepõem (por exemplo `F:\sync1` , `F:\sync2`e). Você pode configurar políticas de camadas de nuvem individualmente para cada ponto de extremidade do servidor. 

Você pode criar um ponto de extremidade do servidor por meio de um mountpoint. Observe que montagem no ponto de extremidade do servidor são ignorados.  

Você pode criar um ponto de extremidade do servidor no volume do sistema, mas há duas limitações se você fizer isso:
* Não é possível habilitar a disposição em camadas na nuvem.
* Restauração rápida de namespace (onde o sistema abre rapidamente todo o namespace e, em seguida, começa a recuperar conteúdo) não é executado.


> [!Note]  
> Somente volumes não removíveis têm suporte.  Não há suporte para unidades mapeadas de um compartilhamento remoto para um caminho de ponto de extremidade do servidor.  Além disso, um ponto de extremidade do servidor pode estar localizado no volume do sistema Windows, embora a disposição em camadas da nuvem não seja suportada no volume do sistema.

Se você adicionar um local de servidor que tenha um conjunto existente de arquivos como um ponto de extremidade de servidor a um grupo de sincronização, esses arquivos serão mesclados com outros arquivos que já estão em outros pontos de extremidade no grupo de sincronização.

### <a name="cloud-endpoint"></a>Ponto de extremidade de nuvem
Um ponto de extremidade de nuvem é um compartilhamento de arquivos do Azure que faz parte de um grupo de sincronização. Todo o compartilhamento de arquivos do Azure é sincronizado e um compartilhamento de arquivos do Azure pode ser membro de apenas um ponto de extremidade de nuvem. Portanto, um compartilhamento de arquivos do Azure pode ser membro de apenas um grupo de sincronização. Se você adicionar um compartilhamento de arquivos do Azure que tem um conjunto existente de arquivos como um ponto de extremidade de nuvem a um grupo de sincronização, os arquivos existentes serão mesclados com todos os outros arquivos que já estão em outros pontos de extremidade no grupo de sincronização.

> [!Important]  
> Sincronização de Arquivos do Azure dá suporte diretamente para fazer alterações no compartilhamento de arquivos do Azure. No entanto, todas as alterações feitas no compartilhamento de arquivos do Azure primeiro precisam ser descobertas por um trabalho de detecção de Sincronização de Arquivos do Azure alteração. Um trabalho de detecção de alteração é iniciado para um ponto de extremidade de nuvem somente uma vez a cada 24 horas. Além disso, as alterações feitas em um compartilhamento de arquivos do Azure no protocolo REST não atualizarão a hora da última modificação do SMB e não serão vistas como uma alteração por sincronização. Para obter mais informações, consulte [perguntas frequentes sobre os arquivos do Azure](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Disposição em camadas na cloud 
A camada de nuvem é um recurso opcional do Sincronização de Arquivos do Azure em que os arquivos acessados com frequência são armazenados em cache localmente no servidor, enquanto todos os outros arquivos estão em camadas para os arquivos do Azure com base nas configurações de política. Para obter mais informações, consulte [noções básicas sobre camadas de nuvem](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Requisitos e interoperabilidade do sistema Sincronização de Arquivos do Azure 
Esta seção aborda Sincronização de Arquivos do Azure requisitos de sistema do agente e a interoperabilidade com recursos e funções do Windows Server e soluções de terceiros.

### <a name="evaluation-cmdlet"></a>Cmdlet de avaliação
Antes de implantar Sincronização de Arquivos do Azure, você deve avaliar se ele é compatível com seu sistema usando o cmdlet de avaliação Sincronização de Arquivos do Azure. Esse cmdlet verifica possíveis problemas com o seu sistema de arquivos e conjunto de banco de arquivo, como caracteres sem suporte ou uma versão do sistema operacional sem suporte. Observe que suas verificações abordam a maioria dos recursos mencionados abaixo, mas não todos eles. Recomendamos que você leia o restante desta seção com cuidado para garantir que sua implantação seja tranqüila. 

O cmdlet Evaluation pode ser instalado instalando o módulo AZ PowerShell, que pode ser instalado seguindo as instruções aqui: [Instalar e configurar Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Utilização  
Você pode invocar a ferramenta de avaliação de algumas maneiras diferentes: você pode executar as verificações do sistema, o conjunto de pontos de verificação ou ambos. Para executar as verificações de sistema e de conjunto de pesquisa: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Para testar somente o conjunto de seus conjuntos de seus:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Para testar somente os requisitos do sistema:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Para exibir os resultados em CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Requisitos de Sistema
- Um servidor que executa o Windows Server 2012 R2, o Windows Server 2016 ou o Windows Server 2019:

    | Version | SKUs com suporte | Opções de implantação com suporte |
    |---------|----------------|------------------------------|
    | Windows Server de 2019 | Datacenter e Standard | Completo e central |
    | Windows Server 2016 | Datacenter e Standard | Completo e central |
    | Windows Server 2012 R2 | Datacenter e Standard | Completo e central |

    As versões futuras do Windows Server serão adicionadas à medida que forem lançadas.

    > [!Important]  
    > É recomendável manter todos os servidores que você usa com o Sincronização de Arquivos do Azure atualizado com as atualizações mais recentes do Windows Update. 

- Um servidor com um mínimo de 2 GiB de memória.

    > [!Important]  
    > Se o servidor estiver sendo executado em uma máquina virtual com memória dinâmica habilitada, a VM deverá ser configurada com uma MiB de memória mínima 2048.
    
- Um volume anexado localmente formatado com o sistema de arquivos NTFS.

### <a name="file-system-features"></a>Recursos do sistema de arquivos

| Funcionalidade | Status de suporte | Notas |
|---------|----------------|-------|
| ACLs (listas de controle de acesso) | Com suporte total | As ACLs do Windows são preservadas por Sincronização de Arquivos do Azure e são impostas pelo Windows Server em pontos de extremidade do servidor. As ACLs do Windows ainda não têm suporte dos arquivos do Azure se os arquivos forem acessados diretamente na nuvem. |
| Ligações fixas | Ignorada | |
| Links simbólicos | Ignorada | |
| Pontos de montagem | Com suporte parcial | Os pontos de montagem podem ser a raiz de um ponto de extremidade do servidor, mas serão ignorados se estiverem contidos no namespace de um ponto de extremidade do servidor. |
| Junções | Ignorada | Por exemplo, Sistema de Arquivos Distribuído pastas Dfsroots e Dfrsrprivate. |
| Pontos de reanálise | Ignorada | |
| Compactação NTFS | Com suporte total | |
| Arquivos esparsos | Com suporte total | Sincronização de arquivos esparsos (não são bloqueados), mas eles são sincronizados com a nuvem como um arquivo completo. Se o conteúdo do arquivo for alterado na nuvem (ou em outro servidor), o arquivo não será mais esparso quando a alteração for baixada. |
| Fluxos de dados alternativos (ADS) | Preservado, mas não sincronizado | Por exemplo, as marcas de classificação criadas pela infraestrutura de classificação de arquivos não são sincronizadas. As marcas de classificação existentes nos arquivos em cada um dos pontos de extremidade do servidor são deixadas inalteradas. |

> [!Note]  
> Somente volumes NTFS têm suporte. Não há suporte para ReFS, FAT, FAT32 e outros sistemas de arquivos.

### <a name="files-skipped"></a>Arquivos ignorados

| Arquivo/pasta | Nota |
|-|-|
| Desktop.ini | Arquivo específico para o sistema |
| ethumbs.db$ | Arquivo temporário para miniaturas |
| ~$\*.\* | Arquivo temporário do Office |
| \*.tmp | Arquivo temporário |
| \*.laccdb | Acessar arquivo de bloqueio do BD|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Arquivo de sincronização interno|
| \\Informações de volume do sistema | Pasta específica ao volume |
| $RECYCLE.BIN| Pasta |
| \\SyncShareState | Pasta para sincronização |

### <a name="failover-clustering"></a>Clustering de failover
O Windows Server failover clustering tem suporte pelo Sincronização de Arquivos do Azure para a opção de implantação "servidor de arquivos para uso geral". O clustering de failover não tem suporte em "Servidor de Arquivos de Escalabilidade Horizontal para dados de aplicativos" (SOFS) ou em volumes compartilhados clusterizados (CSVs).

> [!Note]  
> O agente de Sincronização de Arquivos do Azure deve ser instalado em cada nó em um cluster de failover para que a sincronização funcione corretamente.

### <a name="data-deduplication"></a>Eliminação de duplicação de dados
**Versão do agente 5.0.2.0 ou mais recente**   
A eliminação de duplicação de dados tem suporte em volumes com camadas de nuvem habilitadas no Windows Server 2016 e no Windows Server 2019. Habilitar a eliminação de duplicação de dados em um volume com camada de nuvem habilitada permite que você armazene em cache mais arquivos localmente sem provisionar mais armazenamento. 

Quando a eliminação de duplicação de dados está habilitada em um volume com disposição em camadas de nuvem habilitada, os arquivos otimizados para eliminação de duplicatas no local do ponto de extremidade do servidor serão em camadas semelhantes a um arquivo normal com base nas configurações de política de camadas de nuvem. Depois que os arquivos otimizados para eliminação de duplicação estiverem em camadas, o trabalho de coleta de lixo de eliminação de duplicatas de dados será executado automaticamente para recuperar espaço em disco removendo partes desnecessárias que não são mais referenciadas por outros arquivos no volume.

Observe que a economia de volume se aplica somente ao servidor; seus dados no compartilhamento de arquivos do Azure não serão eliminação de duplicação.

**Versões do agente do Windows Server 2012 R2 ou mais antigas**  
Para volumes que não têm camadas de nuvem habilitadas, o Sincronização de Arquivos do Azure dá suporte à eliminação de duplicação de dados do Windows Server habilitada no volume.

**Notas**
- Se a eliminação de duplicação de dados for instalada antes da instalação do agente de Sincronização de Arquivos do Azure, uma reinicialização será necessária para dar suporte à eliminação de duplicação de dados e à camada de nuvem no mesmo volume.
- Se a eliminação de duplicação de dados estiver habilitada em um volume depois que a disposição em camadas de nuvem estiver habilitada, o trabalho de otimização de eliminação de duplicação inicial otimizará os arquivos no volume que ainda não estão em camadas e terá o seguinte impacto na camada de nuvem:
    - A política de espaço livre continuará a hierarquizar arquivos de acordo com o espaço livre no volume usando o calor.
    - A política de data ignorará a camada de arquivos que, de outra forma, podem ter sido elegíveis para camadas devido ao trabalho de otimização de eliminação de duplicação que acessa os arquivos.
- Para trabalhos de otimização de eliminação de duplicação em andamento, as camadas de nuvem com a política de data serão atrasadas pela configuração [MinimumFileAgeDays](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) de eliminação de duplicação de dados, se o arquivo ainda não estiver em camadas. 
    - Exemplo: Se a configuração de MinimumFileAgeDays for de 7 dias e a política de data de camadas de nuvem for de 30 dias, a política de data fará a camada dos arquivos após 37 dias.
    - Nota: Quando um arquivo está em camadas por Sincronização de Arquivos do Azure, o trabalho de otimização de eliminação de duplicação ignorará o arquivo.
- Se um servidor que executa o Windows Server 2012 R2 com o agente de Sincronização de Arquivos do Azure instalado for atualizado para o Windows Server 2016 ou o Windows Server 2019, as etapas a seguir deverão ser executadas para dar suporte à eliminação de duplicação de dados e à camada de nuvem no mesmo volume:  
    - Desinstale o agente de Sincronização de Arquivos do Azure para Windows Server 2012 R2 e reinicie o servidor.
    - Baixe o agente de Sincronização de Arquivos do Azure para a nova versão do sistema operacional do servidor (Windows Server 2016 ou Windows Server 2019).
    - Instale o agente de Sincronização de Arquivos do Azure e reinicie o servidor.  
    
    Nota: As definições de configuração de Sincronização de Arquivos do Azure no servidor são mantidas quando o agente é desinstalado e reinstalado.

### <a name="distributed-file-system-dfs"></a>Sistema de Arquivos Distribuído (DFS)
O Sincronização de Arquivos do Azure dá suporte à interoperabilidade com namespaces DFS (DFS-N) e Replicação do DFS (DFS-R).

**Namespaces DFS (DFS-N)** : O Sincronização de Arquivos do Azure tem suporte total em servidores DFS-N. Você pode instalar o agente de Sincronização de Arquivos do Azure em um ou mais membros do DFS-N para sincronizar dados entre os pontos de extremidade do servidor e o Endpoint da nuvem. Para obter mais informações, consulte [visão geral dos namespaces do DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replicação do DFS (DFS-R)** : Como o DFS-R e o Sincronização de Arquivos do Azure são soluções de replicação, na maioria dos casos, é recomendável substituir o DFS-R por Sincronização de Arquivos do Azure. No entanto, há vários cenários em que você desejaria usar o DFS-R e Sincronização de Arquivos do Azure juntos:

- Você está migrando de uma implantação do DFS-R para uma implantação Sincronização de Arquivos do Azure. Para obter mais informações, consulte [migrar uma implantação de replicação do DFS (DFS-R) para sincronização de arquivos do Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem todo servidor local que precisa de uma cópia dos dados do arquivo pode ser conectado diretamente à Internet.
- Os servidores de ramificação consolidam dados em um único servidor de Hub, para o qual você gostaria de usar Sincronização de Arquivos do Azure.

Para Sincronização de Arquivos do Azure e o DFS-R para trabalhar lado a lado:

1. Sincronização de Arquivos do Azure camada de nuvem deve ser desabilitada em volumes com pastas replicadas DFS-R.
2. Os pontos de extremidade do servidor não devem ser configurados em pastas de replicação somente leitura do DFS-R.

Para obter mais informações, consulte [replicação do DFS visão geral](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Submeti
O uso do Sysprep em um servidor que tem o agente de Sincronização de Arquivos do Azure instalado não tem suporte e pode levar a resultados inesperados. A instalação do agente e o registro do servidor devem ocorrer após a implantação da imagem do servidor e a conclusão da mini-instalação do Sysprep.

### <a name="windows-search"></a>Pesquisa do Windows
Se a disposição em camadas da nuvem estiver habilitada em um ponto de extremidade do servidor, os arquivos que estiverem em camadas serão ignorados e não indexados pelo Windows Search. Arquivos não em camadas são indexados corretamente.

### <a name="antivirus-solutions"></a>Soluções antivírus
Como o antivírus funciona examinando arquivos em busca de código mal-intencionado conhecido, um produto antivírus pode causar a recall de arquivos em camadas. Nas versões 4,0 e superiores do agente de Sincronização de Arquivos do Azure, os arquivos em camadas têm o atributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS do Windows seguro definido. Recomendamos consultar seu fornecedor de software para saber como configurar sua solução para ignorar a leitura de arquivos com esse conjunto de atributos (muitos fazem isso automaticamente). 

As soluções antivírus internas da Microsoft, o Windows Defender e o System Center Endpoint Protection (SCEP), ambos ignoram automaticamente a leitura de arquivos que têm esse atributo definido. Nós os testamos e identificamos um problema secundário: quando você adiciona um servidor a um grupo de sincronização existente, os arquivos com menos de 800 bytes são rechamados (baixados) no novo servidor. Esses arquivos permanecerão no novo servidor e não serão em camadas, pois não atendem ao requisito de tamanho de camadas (> 64 KB).

> [!Note]  
> Os fornecedores de antivírus podem verificar a compatibilidade entre seus produtos e Sincronização de Arquivos do Azure usando o [sincronização de arquivos do Azure antivírus Compatibility Suite](https://www.microsoft.com/download/details.aspx?id=58322), que está disponível para download no centro de download da Microsoft.

### <a name="backup-solutions"></a>Soluções de backup
Assim como as soluções antivírus, as soluções de backup podem causar a recall de arquivos em camadas. É recomendável usar uma solução de backup de nuvem para fazer backup do compartilhamento de arquivos do Azure em vez de um produto de backup local.

Se você estiver usando uma solução de backup local, os backups devem ser executados em um servidor no grupo de sincronização que tem a camada de nuvem desabilitada. Ao executar uma restauração, use as opções de restauração no nível do volume ou do arquivo. Os arquivos restaurados usando a opção de restauração no nível de arquivo serão sincronizados com todos os pontos de extremidade no grupo de sincronização e os arquivos existentes serão substituídos pela versão restaurada do backup.  As restaurações em nível de volume não substituirão as versões de arquivo mais recentes no compartilhamento de arquivos do Azure ou em outros pontos de extremidade do servidor.

> [!Note]  
> A restauração de bare-metal (BMR) pode causar resultados inesperados e não tem suporte no momento.

> [!Note]  
> Os instantâneos do VSS (incluindo a guia versões anteriores) não têm suporte atualmente em volumes que têm a camada de nuvem habilitada. Se a disposição em camadas da nuvem estiver habilitada, use os instantâneos de compartilhamento de arquivos do Azure para restaurar um arquivo do backup.

### <a name="encryption-solutions"></a>Soluções de criptografia
O suporte para soluções de criptografia depende de como elas são implementadas. Sincronização de Arquivos do Azure é conhecido por trabalhar com:

- Criptografia do BitLocker
- Proteção de informações do Azure, serviços de Rights Management do Azure (Azure RMS) e Active Directory RMS

Sincronização de Arquivos do Azure é conhecido por não funcionar com:

- Sistema de arquivos criptografados NTFS (EFS)

Em geral, Sincronização de Arquivos do Azure deve dar suporte à interoperabilidade com soluções de criptografia que ficam abaixo do sistema de arquivos, como o BitLocker, e com soluções implementadas no formato de arquivo, como a proteção de informações do Azure. Nenhuma interoperabilidade especial foi feita para soluções que ficam acima do sistema de arquivos (como o EFS do NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de HSM (gerenciamento de armazenamento hierárquico)
Nenhuma outra solução HSM deve ser usada com Sincronização de Arquivos do Azure.

## <a name="region-availability"></a>Disponibilidade de região
Sincronização de Arquivos do Azure está disponível somente nas seguintes regiões:

| Região | Localização do datacenter |
|--------|---------------------|
| Leste da Austrália | Nova Gales do Sul |
| Sudeste da Austrália | Vitória |
| Sul do Brasil | Estado de São Paulo |
| Canadá Central | Toronto |
| Leste do Canadá | Cidade do Quebeque |
| Índia Central | Pune |
| EUA Central | Iowa |
| Ásia Oriental | R.A.E. Hong Kong |
| East US | Virginia |
| E.U.A. Leste 2 | Virginia |
| França Central | Paris |
| Sul da França * | Marselha |
| Coreia do Sul Central | Seul |
| Coreia do Sul | Busan |
| Leste do Japão | Tóquio, Saitama |
| Oeste do Japão | Osaka |
| EUA Centro-Norte | Illinois |
| Europa do Norte | Irlanda |
| Norte da África do Sul | Joanesburgo |
| Oeste da África do Sul * | Cidade do Cabo |
| EUA Centro-Sul | Texas |
| Sul da Índia | Chennai |
| Sudeste Asiático | Singapura |
| Reino Unido Sul | Londres |
| Reino Unido Oeste | Cardiff |
| Gov (US) - Arizona | Arizona |
| Gov (US) - Texas | Texas |
| Gov (US) - Virginia | Virginia |
| Europa Ocidental | Países Baixos |
| EUA Centro-Oeste | Wyoming |
| EUA Oeste | California |
| EUA Oeste 2 | Washington |

Sincronização de Arquivos do Azure dá suporte à sincronização somente com um compartilhamento de arquivos do Azure que está na mesma região que o serviço de sincronização de armazenamento.

Para as regiões marcadas com asteriscos, você deve contatar o suporte do Azure para solicitar acesso ao armazenamento do Azure nessas regiões. O processo é descrito neste [documento](https://azure.microsoft.com/global-infrastructure/geographies/).

### <a name="azure-disaster-recovery"></a>Recuperação de desastre do Azure
Para proteger contra a perda de uma região do Azure, o Sincronização de Arquivos do Azure se integra com a opção grs ( [redundância de armazenamento geograficamente redundante](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) ). O armazenamento GRS funciona usando a replicação de bloco assíncrono entre o armazenamento na região primária, com a qual você normalmente interage e armazenamento na região secundária emparelhada. No caso de um desastre que faz com que uma região do Azure fique temporariamente ou permanentemente offline, a Microsoft fará o failover do armazenamento para a região emparelhada. 

> [!Warning]  
> Se você estiver usando o compartilhamento de arquivos do Azure como um ponto de extremidade de nuvem em uma conta de armazenamento GRS, não deverá iniciar o failover da conta de armazenamento. Fazer isso fará com que a sincronização pare de funcionar e também pode causar perda de dados inesperada no caso de arquivos recentemente em camadas. No caso de perda de uma região do Azure, a Microsoft disparará o failover da conta de armazenamento de forma que seja compatível com Sincronização de Arquivos do Azure.

Para dar suporte à integração de failover entre o armazenamento com redundância geográfica e Sincronização de Arquivos do Azure, todas as regiões de Sincronização de Arquivos do Azure são emparelhadas com uma região secundária que corresponde à região secundária usada pelo armazenamento. Esses pares são os seguintes:

| Região primária      | Região emparelhada      |
|---------------------|--------------------|
| Leste da Austrália      | Sudeste da Austrália|
| Sudeste da Austrália | Leste da Austrália     |
| Sul do Brasil        | EUA Centro-Sul   |
| Canadá Central      | Leste do Canadá        |
| Leste do Canadá         | Canadá Central     |
| Índia Central       | Sul da Índia        |
| EUA Central          | EUA Leste 2          |
| Ásia Oriental           | Sudeste Asiático     |
| East US             | EUA Oeste            |
| EUA Leste 2           | EUA Central         |
| França Central      | Sul de França       |
| Sul de França        | França Central     |
| Leste do Japão          | Oeste do Japão         |
| Oeste do Japão          | Leste do Japão         |
| Coreia do Sul Central       | Coreia do Sul        |
| Coreia do Sul         | Coreia do Sul Central      |
| Europa do Norte        | Europa Ocidental        |
| EUA Centro-Norte    | EUA Centro-Sul   |
| Norte da África do Sul  | Oeste da África do Sul  |
| Oeste da África do Sul   | Norte da África do Sul |
| EUA Centro-Sul    | EUA Centro-Norte   |
| Sul da Índia         | Índia Central      |
| Sudeste Asiático      | Ásia Oriental          |
| Reino Unido Sul            | Reino Unido Oeste            |
| Reino Unido Oeste             | Reino Unido Sul           |
| Gov (US) - Arizona      | Gov (US) - Texas       |
| US Gov - Iowa         | Gov (US) - Virginia    |
| Gov (US) - Virginia      | Gov (US) - Texas       |
| Europa Ocidental         | Europa do Norte       |
| EUA Centro-Oeste     | EUA Oeste 2          |
| EUA Oeste             | East US            |
| EUA Oeste 2           | EUA Centro-Oeste    |

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Considerar as configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
* [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
* [Implantar arquivos do Azure](storage-files-deployment-guide.md)
* [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
