---
title: Planejamento de uma implantação de Sincronização de Ficheiros Azure  Microsoft Docs
description: Saiba o que considerar ao planear uma implementação de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 483603b8ff2f4b51f85d21d6ff4f02ad6f8a8272
ms.sourcegitcommit: 76bc196464334a99510e33d836669d95d7f57643
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/12/2020
ms.locfileid: "77162094"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planear uma implementação da Sincronização de Ficheiros do Azure
Utilize o Azure File Sync para centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode utilizar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Podes ter as caches que precisares em todo o mundo.

Este artigo descreve considerações importantes para uma implementação de Sincronização de Ficheiros Azure. Recomendamos que leia também o Planeamento para uma implementação de [Ficheiros Azure.](storage-files-planning.md) 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Terminologia de Sincronização de Ficheiros Azure
Antes de entrar nos detalhes do planeamento para uma implementação de Sincronização de Ficheiros Azure, é importante entender a terminologia.

### <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento
O Serviço de Sincronização de Armazenamento é o recurso Azure de alto nível para o Azure File Sync. O recurso Storage Sync Service é um par do recurso da conta de armazenamento, e pode ser igualmente implantado para grupos de recursos Azure. É necessário um recurso distinto de nível superior a partir do recurso da conta de armazenamento porque o Serviço de Sincronização de Armazenamento pode criar relações sincronizadas com várias contas de armazenamento através de múltiplos grupos de sincronização. Uma subscrição pode ter vários recursos do Serviço de Sincronização de Armazenamento implantados.

### <a name="sync-group"></a>Grupo Sync
Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Se, por exemplo, tiver dois conjuntos distintos de ficheiros que pretende gerir com o Azure File Sync, criaria dois grupos de sincronização e adicionaria diferentes pontos finais a cada grupo de sincronização. Um Serviço de Sincronização de Armazenamento pode acolher os grupos de sincronização que precisar.  

### <a name="registered-server"></a>Servidor registado
O objeto de servidor registado representa uma relação de confiança entre o seu servidor (ou cluster) e o Serviço de Sincronização de Armazenamento. Pode registar o número de servidores numa instância do Serviço de Sincronização de Armazenamento que desejar. No entanto, um servidor (ou cluster) pode ser registado com apenas um Serviço de Sincronização de Armazenamento de cada vez.

### <a name="azure-file-sync-agent"></a>Agente de sincronização de ficheiros Azure
O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure. O agente Dessincronização de Ficheiros Azure tem três componentes principais: 
- **FileSyncSvc.exe**: O serviço de fundo responsável pela monitorização das alterações nos pontos finais do servidor e pelo início das sessões de sincronização para o Azure.
- **ArmazenamentoSync.sys**: O filtro do sistema de ficheiros Azure File Sync, responsável pela nivete de ficheiros para Ficheiros Azure (quando o tiering em nuvem está ativado).
- **Cmdlets de gestão PowerShell**: PowerShell cmdlets que utiliza para interagir com o fornecedor de recursos Microsoft.StorageSync Azure. Pode encontrá-los nos seguintes locais (padrão):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Ponto final do servidor
Os pontos finais de servidor representam uma localização específica num servidor registado, como uma pasta num volume do servidor. Vários pontos finais do servidor podem existir no mesmo volume se os seus espaços de nome não se sobreporem (por exemplo, `F:\sync1` e `F:\sync2`). Pode configurar as políticas de tiering em nuvem individualmente para cada ponto final do servidor. 

Pode criar um ponto final do servidor através de um ponto de montagem. Nota, os pontos de montagem dentro do ponto final do servidor são ignorados.  

Pode criar um ponto final do servidor no volume do sistema, mas existem duas limitações se o fizer:
* O tiering de nuvem não pode ser ativado.
* Não é realizado um espaço de nome rápido (onde o sistema rapidamente derruba todo o espaço de nome e depois começa a recordar o conteúdo).


> [!Note]  
> Apenas são suportados volumes não amovíveis.  As unidades mapeadas a partir de uma parte remota não são suportadas para um caminho de ponto final do servidor.  Além disso, um ponto final do servidor pode estar localizado no volume do sistema Windows, embora o tiering em nuvem não seja suportado no volume do sistema.

Se adicionar uma localização do servidor que tenha um conjunto de ficheiros existente como ponto final do servidor a um grupo de sincronização, esses ficheiros são fundidos com quaisquer outros ficheiros que já se encontram em outros pontos finais do grupo de sincronização.

### <a name="cloud-endpoint"></a>Ponto final da nuvem
Um ponto final em nuvem é uma partilha de ficheiros Azure que faz parte de um grupo de sincronização. Toda a partilha de ficheiros Azure sincroniza, e uma partilha de ficheiros Azure pode ser um membro de apenas um ponto final em nuvem. Portanto, uma partilha de ficheiros Azure pode ser membro de apenas um grupo de sincronização. Se adicionar uma partilha de ficheiros Azure que tem um conjunto de ficheiros existente como ponto final de nuvem a um grupo de sincronização, os ficheiros existentes são fundidos com quaisquer outros ficheiros que já se encontram em outros pontos finais do grupo de sincronização.

> [!Important]  
> O Azure File Sync suporta fazer alterações na partilha de ficheiros Azure diretamente. No entanto, quaisquer alterações efetuadas na partilha de ficheiros Azure têm primeiro de ser descobertas por um trabalho de deteção de alterações de ficheiros Azure. Um trabalho de deteção de mudanças é iniciado para um ponto final de nuvem apenas uma vez a cada 24 horas. Além disso, as alterações introduzidas numa partilha de ficheiros Azure sobre o protocolo REST não atualizarão o SMB no último tempo modificado e não serão vistas como uma alteração por sincronização. Para mais informações, consulte [os Ficheiros Azure frequentemente questionados](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Disposição em camadas na cloud 
O tiering em nuvem é uma funcionalidade opcional do Azure File Sync, no qual os ficheiros frequentemente acedidos são protegidos localmente no servidor, enquanto todos os outros ficheiros são tiered para Ficheiros Azure com base em definições de política. Para mais informações, consulte [Understanding Cloud Tiering](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Requisitos e interoperabilidade do sistema De sincronização de ficheiros Azure 
Esta secção cobre os requisitos do sistema de ficheiros Azure Sync e a interoperabilidade com funcionalidades e funções do Windows Server e soluções de terceiros.

### <a name="evaluation-cmdlet"></a>Avaliação cmdlet
Antes de implementar o Azure File Sync, deve avaliar se é compatível com o seu sistema utilizando o cmdlet de avaliação do Ficheiro Azure. Este cmdlet verifica potenciais problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão do sistema operativo não suportado. Os seus controlos cobrem a maior parte, mas nem todas as características mencionadas abaixo; recomendamos que leia atentamente o resto desta secção para garantir que a sua implementação corra bem. 

O cmdlet de avaliação pode ser instalado instalando o módulo Az PowerShell, que pode ser instalado seguindo as instruções aqui: [Instale e configure o Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Utilização  
Pode invocar a ferramenta de avaliação de várias maneiras: pode efetuar as verificações do sistema, as verificações do conjunto de dados, ou ambas. Para efetuar tanto o sistema como as verificações de conjuntos de dados: 

```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Para testar apenas o seu conjunto de dados:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Para testar apenas os requisitos do sistema:
```powershell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Para exibir os resultados em CSV:
```powershell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Requisitos de Sistema
- Um servidor que executa uma das seguintes versões do sistema operativo:

    | Versão | SKUs suportados | Opções de implementação apoiadas |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter e Standard | Full e Core |
    | Windows Server 2016 | Datacenter e Standard | Full e Core |
    | Windows Server 2012 R2 | Datacenter e Standard | Full e Core |
    | Windows Server IoT 2019 para Armazenamento| Datacenter e Standard | Full e Core |
    | Windows Storage Server 2016| Datacenter e Standard | Full e Core |
    | Windows Storage Server 2012 R2| Datacenter e Standard | Full e Core |

    Futuras versões do Windows Server serão adicionadas à medida que forem lançadas.

    > [!Important]  
    > Recomendamos manter todos os servidores que utiliza com o Azure File Sync atualizado sem as mais recentes atualizações a partir do Windows Update. 

- Um servidor com um mínimo de 2 GiB de memória.

    > [!Important]  
    > Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um MiB mínimo de 2048.
    
- Um volume localmente ligado formado com o sistema de ficheiros NTFS.

### <a name="file-system-features"></a>Funcionalidades do sistema de ficheiros

| Funcionalidade | Estado de apoio | Notas |
|---------|----------------|-------|
| Listas de controlo de acesso (ACLs) | Totalmente apoiado | Os CALs do Windows são preservados pelo Sync de Ficheiros Azure e são aplicados pelo Windows Server nos pontos finais do servidor. Os ACLs do Windows não são (ainda) suportados por Ficheiros Azure se os ficheiros forem acedidos diretamente na nuvem. |
| Ligações fixas | Ignorado | |
| Ligações simbólicas | Ignorado | |
| Pontos de montagem | Parcialmente apoiado | Os pontos de montagem podem ser a raiz de um ponto final do servidor, mas são ignorados se estiverem contidos no espaço de nome de um ponto final do servidor. |
| Junções | Ignorado | Por exemplo, dfrsrPrivate e dfSRoots pastas do Sistema de Ficheiros Distribuídos. |
| Pontos de reanálise | Ignorado | |
| Compressão NTFS | Totalmente apoiado | |
| Ficheiros escassos | Totalmente apoiado | Sincronização de ficheiros escassos (não estão bloqueadas), mas sincronizam-se com a nuvem como um ficheiro completo. Se o conteúdo do ficheiro mudar na nuvem (ou noutro servidor), o ficheiro já não é escasso quando a alteração é descarregada. |
| Fluxos de dados alternativos (ADS) | Preservado, mas não sincronizado | Por exemplo, as etiquetas de classificação criadas pela Infraestrutura de Classificação de Ficheiros não são sincronizadas. As etiquetas de classificação existentes em ficheiros em cada um dos pontos finais do servidor não são tocadas. |

> [!Note]  
> Apenas os volumes NTFS são suportados. ReFS, FAT, FAT32 e outros sistemas de ficheiros não são suportados.

### <a name="files-skipped"></a>Ficheiros ignorados

| Arquivo/pasta | Nota |
|-|-|
| pagefile.sys | Ficheiro específico do sistema |
| Desktop.ini | Ficheiro específico do sistema |
| thumbs.db | Arquivo temporário para miniaturas |
| ehthumbs.db | Arquivo temporário para miniaturas de mídia |
| ~$\*.\* | Arquivo temporário do escritório |
| \*.tmp | Arquivo temporário |
| \*.laccdb | Ficheiro de bloqueio DB de acesso|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Ficheiro Sincronizado Interno|
| informações sobre o volume do sistema de \\ | Pasta específica ao volume |
| $RECYCLE.BIN| Pasta |
| \\SyncShareState | Pasta para Sincronização |

### <a name="failover-clustering"></a>Agrupamento failover
O Clusterde failover do Windows Server é suportado pelo WebE File Sync para a opção de implementação "File Server for general use". O Clusterde Failover não é suportado no "Scale-Out File Server para dados de aplicação" (SOFS) ou em Volumes Partilhados Clustered (CSVs).

> [!Note]  
> O agente Dessincronização de ficheiros Azure deve ser instalado em todos os nós de um Cluster Failover para que a sincronização funcione corretamente.

### <a name="data-deduplication"></a>Eliminação de Dados Duplicados
**Windows Server 2016 e Windows Server 2019**   
A Deduplicação de Dados é suportada em volumes com tiering em nuvem ativado no Windows Server 2016 e Windows Server 2019. Permitir a desduplicação de dados num volume com tiering em nuvem ativado permite-lhe guardar mais ficheiros no local sem fornecer mais armazenamento. 

Quando a desduplicação de dados estiver ativada num volume com o tiering em nuvem ativado, os ficheiros otimizados do Dedup dentro da localização do ponto final do servidor serão hierárquicos semelhantes a um ficheiro normal baseado nas definições da política de tiering da nuvem. Uma vez que os ficheiros otimizados de Dedup tenham sido hierárquicos, o trabalho de recolha de lixo de Deduplicação de Dados funcionará automaticamente para recuperar o espaço do disco, removendo pedaços desnecessários que já não são referenciados por outros ficheiros no volume.

Note que as poupanças de volume aplicam-se apenas ao servidor; os seus dados na partilha de ficheiros Azure não serão desenganados.

> [!Note]  
> Para suportar a Deduplicação de Dados em volumes com tiering em nuvem ativado no Windows Server 2019, a atualização do Windows [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalada e é necessária a versão 9.0.0.0 ou mais recente do agente Dom

**Windows Server 2012 R2**  
O Azure File Sync não suporta a desduplicação de dados e o tiering em nuvem no mesmo volume no Windows Server 2012 R2. Se a desduplicação de dados estiver ativada num volume, o tiering em nuvem deve ser desativado. 

**Notas**
- Se a deduplicação de dados for instalada antes da instalação do agente Dessincronização de Ficheiros Azure, é necessário reiniciar a desduplicação de dados e o tiering da nuvem no mesmo volume.
- Se a desduplicação de dados estiver ativada num volume após a ativação do tiering em nuvem, o trabalho inicial de otimização da deduplicação otimizará ficheiros sobre o volume que ainda não estão nivilhados e terá o seguinte impacto no tiering da nuvem:
    - A política de espaço livre continuará a diferenciar ficheiros de acordo com o espaço livre no volume utilizando o mapa de calor.
    - A política de datas irá ignorar o tiering de ficheiros que podem ter sido elegíveis para tiering devido ao trabalho de otimização da deduplicação que acedeu aos ficheiros.
- Para trabalhos de otimização de deduplicação em curso, o tiering em nuvem com a política de data sairá adiado pela definição de Data Deduplication [MinimumFileAgeDays,](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) se o ficheiro ainda não estiver niveado. 
    - Exemplo: Se a definição mínimadetempos é de sete dias e a política de data de nível de nuvem é de 30 dias, a política de datas irá diferenciar ficheiros após 37 dias.
    - Nota: Uma vez que um ficheiro é nivelado por Azure File Sync, o trabalho de otimização da deduplicação saltará o ficheiro.
- Se um servidor que executa o Windows Server 2012 R2 com o agente Dessincronizado de Ficheiros Azure instalado for atualizado para o Windows Server 2016 ou windows Server 2019, devem ser realizados os seguintes passos para suportar a Deduplicação de Dados e o tiering em nuvem no mesmo volume:  
    - Desinstale o agente Desinstalação de Ficheiros Azure para o Windows Server 2012 R2 e reinicie o servidor.
    - Descarregue o agente DoSc de Ficheiros Azure para a nova versão do sistema operativo do servidor (Windows Server 2016 ou Windows Server 2019).
    - Instale o agente Dessincronização de ficheiros Azure e reinicie o servidor.  
    
    Nota: As definições de configuração de sincronização de ficheiros Azure no servidor são retidas quando o agente está desinstalado e reinstalado.

### <a name="distributed-file-system-dfs"></a>Sistema de Ficheiros Distribuídos (DFS)
O Azure File Sync suporta o interop com espaços de nomedfs (DFS-N) e replicação DFS (DFS-R).

**DFS Namespaces (DFS-N)** : O Sync de Ficheiros Azure é totalmente suportado nos servidores DFS-N. Pode instalar o agente Dessincronização de ficheiros Azure num ou mais membros DODFS-N para sincronizar dados entre os pontos finais do servidor e o ponto final da nuvem. Para mais informações, consulte a [visão geral dos espaços de nomes do DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replicação DFS (DFS-R)** : Uma vez que DFS-R e Azure File Sync são ambas soluções de replicação, na maioria dos casos, recomendamos a substituição do DFS-R por Sincronização de Ficheiros Azure. Existem, no entanto, vários cenários em que desejaria utilizar juntos dfS-R e Azure File Sync:

- Está a migrar de uma implantação DFS-R para uma implantação de Sincronização de Ficheiros Azure. Para mais informações, consulte [migrate uma implementação de replicação DFS (DFS-R) para O Sincronizado de Ficheiros Azure](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem todos os servidores no local que necessitem de uma cópia dos seus dados de ficheiros podem ser ligados diretamente à internet.
- Os servidores de sucursais consolidam os dados num único servidor hub, para o qual deseja utilizar o Azure File Sync.

Para o Azure File Sync e dfS-R trabalharem lado a lado:

1. O diferencial de nuvem De sincronização de ficheiros Azure deve ser desativado em volumes com pastas replicadas DFS-R.
2. Os pontos finais do servidor não devem ser configurados nas pastas de replicação apenas dfS-R.

Para mais informações, consulte a [visão geral da replicação do DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
A utilização de sysprep num servidor que tenha o agente Dessincronizado de Ficheiros Azure instalado não é suportada e pode levar a resultados inesperados. A instalação do agente e o registo do servidor devem ocorrer após a implementação da imagem do servidor e de completar a mini-configuração de sysprep.

### <a name="windows-search"></a>Pesquisa do Windows
Se o tiering de nuvem estiver ativado num ponto final do servidor, os ficheiros que são hierárquicos são ignorados e não indexados pelo Windows Search. Os ficheiros não nidificantes estão devidamente indexados.

### <a name="antivirus-solutions"></a>Soluções antivírus
Como o antivírus funciona através da digitalização de ficheiros para código malicioso conhecido, um produto antivírus pode causar a recolha de ficheiros hierárquicos. Nas versões 4.0 e acima do agente DoSinsem de Ficheiros Azure, os ficheiros hierárquicos têm o atributo seguro do Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definido. Recomendamos consultar o seu fornecedor de software para aprender a configurar a sua solução para ignorar ficheiros de leitura com este conjunto de atributos (muitos fazem-no automaticamente). 

As soluções antivírus internas da Microsoft, o Windows Defender e o System Center Endpoint Protection (SCEP), ambos automaticamente ignoram ficheiros de leitura que tenham este conjunto de atributos. Testámo-los e identificámos um pequeno problema: quando adicionas um servidor a um grupo de sincronização existente, os ficheiros inferiores a 800 bytes são recordados (descarregados) no novo servidor. Estes ficheiros permanecerão no novo servidor e não serão nilutos, uma vez que não cumprem o requisito de tamanho de nidível (>64kb).

> [!Note]  
> Os fornecedores antivírus podem verificar a compatibilidade entre o seu produto e o Azure File Sync utilizando o [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), que está disponível para download no Microsoft Download Center.

### <a name="backup-solutions"></a>Soluções de backup
Tal como as soluções antivírus, as soluções de backup podem causar a recuperação de ficheiros hierárquicos. Recomendamos a utilização de uma solução de backup em nuvem para fazer backup a partilha de ficheiros Azure em vez de um produto de reserva no local.

Se estiver a utilizar uma solução de backup no local, as cópias de segurança devem ser efetuadas num servidor no grupo de sincronização que tenha desativado o nível de nuvem. Ao efetuar uma restauração, utilize as opções de restauro do nível de volume ou do nível de ficheiro. Os ficheiros restaurados utilizando a opção de restauro do nível de ficheiro serão sincronizados em todos os pontos finais do grupo de sincronização e os ficheiros existentes serão substituídos pela versão restaurada a partir de cópia de segurança.  As restaurações de nível de volume não irão substituir as versões de ficheiromais recentes na partilha de ficheiros Azure ou em outros pontos finais do servidor.

> [!Note]  
> A restauração de bare-metal (BMR) pode causar resultados inesperados e não é suportada atualmente.

> [!Note]  
> Com a versão 9 do agente Dessincronização de Ficheiros Azure, os instantâneos VSS (incluindo o separador Versões Anteriores) são agora suportados em volumes que têm o tiering em nuvem ativado. No entanto, deve ativar a compatibilidade da versão anterior através do PowerShell. [Saiba como](storage-files-deployment-guide.md).

### <a name="encryption-solutions"></a>Soluções de encriptação
O suporte para soluções de encriptação depende da forma como são implementadas. O Azure File Sync é conhecido por trabalhar com:

- Encriptação BitLocker
- Proteção da Informação Azure, Serviços de Gestão de Direitos Azure (Azure RMS) e Diretório Ativo RMS

O Azure File Sync é conhecido por não trabalhar com:

- Sistema de Ficheiros Encriptados NTFS (EFS)

Em geral, o Azure File Sync deve suportar a interoperabilidade com soluções de encriptação que se situam abaixo do sistema de ficheiros, como o BitLocker, e com soluções que são implementadas no formato de ficheiros, como a Proteção de Informação do Azure. Não foi feita nenhuma interoperabilidade especial para soluções que se sentam acima do sistema de ficheiros (como o NTFS EFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções hierárquicas de gestão de armazenamento (HSM)
Nenhuma outra solução HSM deve ser utilizada com o Azure File Sync.

## <a name="region-availability"></a>Disponibilidade de região
O Azure File Sync só está disponível nas seguintes regiões:

| Região | Localização do datacenter |
|--------|---------------------|
| Leste da Austrália | Nova Gales do Sul |
| Sudeste da Austrália | Vitória |
| Sul do Brasil | Estado de São Paulo |
| Canadá Central | Toronto |
| Leste do Canadá | Cidade do Quebeque |
| Índia Central | Pune |
| E.U.A. Central | Rio Iowa |
| Ásia Oriental | RAE de Hong Kong |
| E.U.A. Leste | Virgínia |
| E.U.A. Leste 2 | Virgínia |
| França Central | Paris |
| França Sul* | Marselha |
| Coreia do Sul Central | Seoul |
| Sul da Coreia do Sul | Busan |
| Leste do Japão | Tóquio |
| Oeste do Japão | Osaka |
| E.U.A. Centro-Norte | Illinois |
| Europa do Norte | Irlanda |
| África do Sul Norte | Joanesburgo |
| África do Sul Oeste* | Cidade do Cabo |
| E.U.A. Centro-Sul | Texas |
| Sul da Índia | Chennai |
| Sudeste Asiático | Singapura |
| Sul do Reino Unido | Londres |
| Oeste do Reino Unido | Cardiff |
| US Gov - Arizona | Arizona |
| US Gov - Texas | Texas |
| US Gov - Virginia | Virgínia |
| Emirados Unidos norte | Rio Dubai |
| Central dos Emirados Emirados ArMeTos* | Abu Dhabi |
| Europa Ocidental | Países Baixos |
| E.U.A. Centro-Oeste | Rio Wyoming |
| E.U.A. Oeste | Califórnia |
| E.U.A.Oeste 2 | Washington |

O Azure File Sync suporta sincronizar apenas com uma partilha de ficheiros Azure que está na mesma região que o Serviço de Sincronização de Armazenamento.

Para as regiões marcadas com asteriscos, deve contactar o Azure Support para solicitar o acesso ao Armazenamento Azure nessas regiões. O processo está delineado [neste documento.](https://azure.microsoft.com/global-infrastructure/geographies/)

### <a name="azure-disaster-recovery"></a>Recuperação de desastres de Azure
Para proteger contra a perda de uma região do Azure, o Azure File Sync [integra-se com o armazenamento geo-redundante](../common/storage-redundancy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) (GRS). O armazenamento GRS funciona utilizando replicação de blocos assíncronos entre o armazenamento na região primária, com o qual normalmente interage, e armazenamento na região secundária emparelhada. Em caso de desastre que faça com que uma região do Azure fique temporariamente ou permanentemente offline, a Microsoft falhará o armazenamento na região emparelhada. 

> [!Warning]  
> Se estiver a usar a sua quota de ficheiroS Azure como ponto final de nuvem numa conta de armazenamento GRS, não deve iniciar o failover da conta de armazenamento. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão. Em caso de perda de uma região do Azure, a Microsoft irá desencadear a falha da conta de armazenamento de uma forma compatível com o Azure File Sync.

Para apoiar a integração de falhas entre o armazenamento geo-redundante e o Azure File Sync, todas as regiões de Sincronização de Ficheiros Azure são emparelhadas com uma região secundária que corresponde à região secundária utilizada pelo armazenamento. Estes pares são os seguintes:

| Região primária      | Região emparelhada      |
|---------------------|--------------------|
| Leste da Austrália      | Sudeste da Austrália|
| Sudeste da Austrália | Leste da Austrália     |
| Sul do Brasil        | E.U.A. Centro-Sul   |
| Canadá Central      | Leste do Canadá        |
| Leste do Canadá         | Canadá Central     |
| Índia Central       | Sul da Índia        |
| E.U.A. Central          | E.U.A. Leste 2          |
| Ásia Oriental           | Sudeste Asiático     |
| E.U.A. Leste             | E.U.A. Oeste            |
| E.U.A. Leste 2           | E.U.A. Central         |
| França Central      | Sul de França       |
| Sul de França        | França Central     |
| Leste do Japão          | Oeste do Japão         |
| Oeste do Japão          | Leste do Japão         |
| Coreia do Sul Central       | Sul da Coreia do Sul        |
| Sul da Coreia do Sul         | Coreia do Sul Central      |
| Europa do Norte        | Europa Ocidental        |
| E.U.A. Centro-Norte    | E.U.A. Centro-Sul   |
| África do Sul Norte  | África do Sul Ocidental  |
| África do Sul Ocidental   | África do Sul Norte |
| E.U.A. Centro-Sul    | E.U.A. Centro-Norte   |
| Sul da Índia         | Índia Central      |
| Sudeste Asiático      | Ásia Oriental          |
| Sul do Reino Unido            | Oeste do Reino Unido            |
| Oeste do Reino Unido             | Sul do Reino Unido           |
| US Gov - Arizona      | US Gov - Texas       |
| US Gov - Iowa         | US Gov - Virginia    |
| US Gov - Virginia      | US Gov - Texas       |
| Europa Ocidental         | Europa do Norte       |
| E.U.A. Centro-Oeste     | E.U.A.Oeste 2          |
| E.U.A. Oeste             | E.U.A. Leste            |
| E.U.A.Oeste 2           | E.U.A. Centro-Oeste    |

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="recommended-azure-file-sync-machine-configuration"></a>Configuração recomendada da máquina de sincronização de ficheiros Azure

Os requisitos da máquina De sincronização de ficheiros Azure são determinados pelo número de objetos no espaço de nome e pelo churn no conjunto de dados. Um único servidor pode ser ligado a múltiplos grupos de sincronização e o número de objetos listados nas seguintes contas de tabela para o espaço de nome completo a que um servidor está ligado. Por exemplo, ponto final do servidor A com 10 milhões de objetos + ponto final do servidor B com 10 milhões de objetos = 20 milhões de objetos. Para este exemplo, recomendamos 8CPU, 16GiB de memória para um estado estável, e (se possível) 48GiB de memória para a migração inicial.
 
Os dados do espaço de nome são armazenados na memória por razões de desempenho. Por isso, espaços de nome maiores requerem mais memória para manter um bom desempenho, e mais churn requer mais CPU para processar. 
 
Na tabela seguinte, fornecemos tanto o tamanho do espaço de nome como uma conversão para a capacidade para ações típicas de ficheiros de finalidade geral, onde o tamanho médio do ficheiro é de 512KiB. Se os tamanhos dos seus ficheiros forem menores, considere adicionar memória adicional para a mesma quantidade de capacidade. Baseie a configuração da sua memória no tamanho do espaço de nome.

| Tamanho do espaço de nome - ficheiros e diretórios (milhões)  | Capacidade típica (TiB)  | Núcleos de CPU  | Memória recomendada (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronização inicial)/ 2 (churn típico)      |
| 5        | 2.4     | 2        | 16 (sincronização inicial)/ 4 (churn típico)    |
| 10       | 4.8     | 4        | 32 (sincronização inicial)/ 8 (churn típico)   |
| 30       | 14,3    | 8        | 48 (sincronização inicial)/ 16 (churn típico)   |
| 50       | 23,8    | 16       | 64 (sincronização inicial)/ 32 (churn típico)  |
| 100 *     | 47,7   | 32       | 128 (sincronização inicial)/ 32 (churn típico)  |

\*Mais de 100 milhões de ficheiros e diretórios não foram testados. Este é um limite suave.

> [!TIP]
> A sincronização inicial de um espaço de nome é uma operação intensiva e recomendamos a atribuição de mais memória até que a sincronização inicial esteja completa. Isto não é necessário, mas.pode acelerar a sincronização inicial. 
> 
> O churn típico é de 0,5% do espaço de nome sinuoso por dia. Para níveis mais elevados de churn, considere adicionar mais CPU. 

## <a name="next-steps"></a>Passos seguintes
* [Considere configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
* [Planning for an Azure Files deployment](storage-files-planning.md) (Planear uma implementação de Ficheiros do Azure)
* [Implementar ficheiros Azure](storage-files-deployment-guide.md)
* [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)
