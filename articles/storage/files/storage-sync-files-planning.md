---
title: Planejamento de uma implantação de Sincronização de Ficheiros Azure  Microsoft Docs
description: Saiba o que considerar ao planear uma implementação de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 0684f626553946619a0db2cd895df39576bd17b9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79255122"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planear uma implementação da Sincronização de Ficheiros do Azure
[Os Ficheiros Azure](storage-files-introduction.md) podem ser implementados de duas formas principais: montando diretamente as ações de ficheiros Azure sem servidor ou por cache ingequedem ações de ficheiros Azure no local utilizando o Azure File Sync. Qual a opção de implementação que escolher altera as coisas que precisa de considerar como planeia para a sua implantação. 

- **Montagem direta de uma partilha de ficheiros Azure**: Uma vez que o Azure Files fornece acesso SMB, pode montar ações de ficheiroS Azure no local ou na nuvem utilizando o cliente Padrão SMB disponível no Windows, macOS e Linux. Uma vez que as partilhas de ficheiros Azure são inservidoras, a implementação para cenários de produção não requer a gestão de um servidor de ficheiros ou de um dispositivo NAS. Isto significa que não tem de aplicar patches de software ou trocar discos físicos. 

- **A partilha de ficheiros Cache Azure no local com**o Azure File Sync : Azure File Sync permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo ao mesmo tempo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma um Windows Server no local (ou cloud) numa cache rápida da sua partilha de ficheiros Azure. 

Este artigo aborda principalmente considerações de implementação para a implementação do Ficheiro Sincronizado Azure. Para planear a implantação de ações de ficheiros Azure para ser montada diretamente por um cliente no local ou na nuvem, consulte O Planeamento para uma implementação de [Ficheiros Azure](storage-files-planning.md).

## <a name="management-concepts"></a>Conceitos de gestão
Uma implantação de Sincronização de Ficheiros Azure tem três objetos de gestão fundamentais:

- **Partilha de ficheiros Azure**: Uma partilha de ficheiros Azure é uma partilha de ficheiros em nuvem sem servidor, que fornece o *ponto final* da nuvem de uma relação de sincronização de sincronização de ficheiros Azure. Os ficheiros de uma partilha de ficheiros Azure podem ser acedidos diretamente com o Protocolo SMB ou FileREST, embora o encorajemos a aceder principalmente aos ficheiros através da cache do Windows Server quando a partilha de ficheiros Azure estiver a ser utilizada com o Ficheiro Sync do Azure. Isto porque o Azure Files hoje em dia carece de um mecanismo de deteção de alterações eficiente como o Windows Server, pelo que as alterações na partilha de ficheiros Azure levarão algum tempo a propagar-se de volta aos pontos finais do servidor.
- **Ponto final**do servidor : O caminho no Servidor do Windows que está a ser sincronizado com uma partilha de ficheiros Azure. Esta pode ser uma pasta específica num volume ou na raiz do volume. Vários pontos finais do servidor podem existir no mesmo volume se os seus espaços de nome não se sobreporem.
- **Sync group**: O objeto que define a relação de sincronização entre um **ponto final**de nuvem , ou partilha de ficheiros Azure, e um ponto final do servidor. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Se, por exemplo, tiver dois conjuntos distintos de ficheiros que pretende gerir com o Azure File Sync, criaria dois grupos de sincronização e adicionaria diferentes pontos finais a cada grupo de sincronização.

### <a name="azure-file-share-management-concepts"></a>Conceitos de gestão de partilha seleções de ficheiros Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Conceitos de gestão de sincronização de ficheiros Azure
Os grupos sync são implantados em **Storage Sync Services,** que são objetos de alto nível que registam servidores para uso com o Azure File Sync e contêm as relações de grupo sincronizado. O recurso Storage Sync Service é um par do recurso da conta de armazenamento, e pode ser igualmente implantado para grupos de recursos Azure. Um Serviço de Sincronização de Armazenamento pode criar grupos de sincronização que contenham partilhas de ficheiros Azure em várias contas de armazenamento e vários Servidores Windows registados.

Antes de poder criar um grupo de sincronização num Serviço de Sincronização de Armazenamento, tem primeiro de registar um Servidor Windows com o Serviço de Sincronização de Armazenamento. Isto cria um objeto de **servidor registado,** que representa uma relação de confiança entre o seu servidor ou cluster e o Serviço de Sincronização de Armazenamento. Para registar um Serviço de Sincronização de Armazenamento, tem primeiro de instalar o agente Dessincronização de ficheiros Azure no servidor. Um servidor ou cluster individual pode ser registado com apenas um Serviço de Sincronização de Armazenamento de cada vez.

Um grupo de sincronização contém um ponto final em nuvem, ou partilha de ficheiros Azure, e pelo menos um ponto final do servidor. O objeto de ponto final do servidor contém as definições que configuram a capacidade de tiering da **nuvem,** que fornece a capacidade de cache do Ficheiro Sincronizado Azure. Para sincronizar com uma parte de ficheiro Azure, a conta de armazenamento que contém a parte do ficheiro Azure deve estar na mesma região do Azure que o Serviço de Sincronização de Armazenamento.

### <a name="management-guidance"></a>Orientação de gestão
Ao implementar o Azure File Sync, recomendamos:

- A implementação de ficheiros Azure partilha 1:1 com as partilhas de ficheiros windows. O objeto final do servidor dá-lhe um grande grau de flexibilidade na forma como configura a topologia de sincronização no lado do servidor da relação de sincronização. Para simplificar a gestão, faça com que o caminho do ponto final do servidor corresponda ao caminho da partilha de ficheiros do Windows. 

- Utilize o menor número possível de Serviços de Sincronização de Armazenamento. Isto simplificará a gestão quando tiver grupos de sincronização que contenham vários pontos finais do servidor, uma vez que um Servidor Windows só pode ser registado num Serviço de Sincronização de Armazenamento de cada vez. 

- Prestando atenção às limitações iops de uma conta de armazenamento ao implementar ações de ficheiros Azure. Idealmente, você mapearia as partilhas de ficheiros 1:1 com contas de armazenamento, no entanto isso pode nem sempre ser possível devido a vários limites e restrições, tanto da sua organização como do Azure. Quando não for possível ter apenas uma parte de ficheiro implantada numa conta de armazenamento, considere quais as ações que serão altamente ativas e quais as ações que serão menos ativas para garantir que as ações de ficheiro mais quentes não sejam colocadas na mesma conta de armazenamento em conjunto.

## <a name="windows-file-server-considerations"></a>Considerações de servidor de ficheiros do Windows
Para ativar a capacidade de sincronização no Windows Server, tem de instalar o agente descarregado rentreficheiro Sinifáto Azure. O agente Dossincronização de ficheiros Azure fornece dois componentes principais: `FileSyncSvc.exe`, o serviço de fundo Windows responsável pela monitorização de alterações nos pontos finais do servidor e início de sessões de sincronização, e `StorageSync.sys`, um filtro de sistema de ficheiros que permite o tiering em nuvem e a rápida recuperação de desastres.  

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo
O Azure File Sync é suportado com as seguintes versões do Windows Server:

| Versão | SKUs suportados | Opções de implementação apoiadas |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard e IoT | Full e Core |
| Windows Server 2016 | Datacenter, Standard e Storage Server | Full e Core |
| Windows Server 2012 R2 | Datacenter, Standard e Storage Server | Full e Core |

Futuras versões do Windows Server serão adicionadas à medida que forem lançadas.

> [!Important]  
> Recomendamos manter todos os servidores que utiliza com o Azure File Sync atualizado sem as mais recentes atualizações a partir do Windows Update. 

### <a name="minimum-system-resources"></a>Recursos mínimos do sistema
O Azure File Sync requer um servidor, físico ou virtual, com pelo menos um CPU e um mínimo de 2 GiB de memória.

> [!Important]  
> Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de memória de 2048 MiB.

Para a maioria das cargas de trabalho de produção, não recomendamos configurar um servidor de sincronização De Sincronização de Ficheiros Azure apenas com os requisitos mínimos. Consulte [os recursos do sistema recomendados](#recommended-system-resources) para obter mais informações.

### <a name="recommended-system-resources"></a>Recursos do sistema recomendados
Tal como qualquer funcionalidade ou aplicação do servidor, os requisitos de recursos do sistema para o Sync de Ficheiros Azure são determinados pela escala da implementação; implementações maiores num servidor requerem maiores recursos do sistema. Para o Sync de Ficheiros Azure, a escala é determinada pelo número de objetos nos pontos finais do servidor e pelo churn no conjunto de dados. Um único servidor pode ter pontos finais do servidor em vários grupos de sincronização e o número de objetos listados nas seguintes contas de tabela para o espaço de nome completo a que um servidor está ligado. 

Por exemplo, ponto final do servidor A com 10 milhões de objetos + ponto final do servidor B com 10 milhões de objetos = 20 milhões de objetos. Para este exemplo, recomendamos 8 CPUs, 16 GiB de memória para um estado estável, e (se possível) 48 GiB de memória para a migração inicial.
 
Os dados do espaço de nome são armazenados na memória por razões de desempenho. Por isso, espaços de nome maiores requerem mais memória para manter um bom desempenho, e mais churn requer mais CPU para processar. 
 
Na tabela seguinte, fornecemos tanto o tamanho do espaço de nome como uma conversão para a capacidade para ações típicas de ficheiros de finalidade geral, onde o tamanho médio do ficheiro é de 512 KiB. Se os tamanhos dos seus ficheiros forem menores, considere adicionar memória adicional para a mesma quantidade de capacidade. Baseie a configuração da sua memória no tamanho do espaço de nome.

| Tamanho do espaço de nome - ficheiros e diretórios (milhões)  | Capacidade típica (TiB)  | Núcleos de CPU  | Memória recomendada (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronização inicial)/ 2 (churn típico)      |
| 5        | 2.3     | 2        | 16 (sincronização inicial)/ 4 (churn típico)    |
| 10       | 4.7     | 4        | 32 (sincronização inicial)/ 8 (churn típico)   |
| 30       | 14.0    | 8        | 48 (sincronização inicial)/ 16 (churn típico)   |
| 50       | 23.3    | 16       | 64 (sincronização inicial)/ 32 (churn típico)  |
| 100 *     | 46.6    | 32       | 128 (sincronização inicial)/ 32 (churn típico)  |

\*Sincronizar mais de 100 milhões de ficheiros e diretórios não é recomendado neste momento. Este é um limite suave baseado nos nossos limiares testados. Para mais informações, consulte a escalabilidade e os alvos de desempenho do [Azure Files.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> A sincronização inicial de um espaço de nome é uma operação intensiva e recomendamos a atribuição de mais memória até que a sincronização inicial esteja completa. Isto não é necessário, mas.pode acelerar a sincronização inicial. 
> 
> O churn típico é de 0,5% do espaço de nome sinuoso por dia. Para níveis mais elevados de churn, considere adicionar mais CPU. 

- Um volume localmente ligado formado com o sistema de ficheiros NTFS.

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

### <a name="file-system-compatibility"></a>Compatibilidade do sistema de ficheiros
O Azure File Sync só é suportado em volumes NTFS diretamente ligados. Armazenamento em anexo direto, ou DAS, no Windows Server significa que o sistema operativo Windows Server possui o sistema de ficheiros. A DAS pode ser fornecida através da fixação física de discos ao servidor de ficheiros, anexando discos virtuais a um VM do servidor de ficheiros (como um VM hospedado por Hyper-V), ou mesmo através do ISCSI.

Apenas os volumes NTFS são suportados; ReFS, FAT, FAT32 e outros sistemas de ficheiros não são suportados.

A tabela que se segue mostra o estado interop das funcionalidades do sistema de ficheiros NTFS: 

| Funcionalidade | Estado de apoio | Notas |
|---------|----------------|-------|
| Listas de controlo de acesso (ACLs) | Totalmente apoiado | As listas de controlo discricionáriode de acesso ao estilo do Windows são preservadas pelo Azure File Sync e são aplicadas pelo Windows Server nos pontos finais do servidor. Os ACLs também podem ser aplicados ao montar em direto a parte do ficheiro Azure, no entanto isso requer uma configuração adicional. Consulte a [secção Identidade](#identity) para mais informações. |
| Ligações fixas | Ignorado | |
| Ligações simbólicas | Ignorado | |
| Pontos de montagem | Parcialmente apoiado | Os pontos de montagem podem ser a raiz de um ponto final do servidor, mas são ignorados se estiverem contidos no espaço de nome de um ponto final do servidor. |
| Junções | Ignorado | Por exemplo, dfrsrPrivate e dfSRoots pastas do Sistema de Ficheiros Distribuídos. |
| Pontos de reanálise | Ignorado | |
| Compressão NTFS | Totalmente apoiado | |
| Ficheiros escassos | Totalmente apoiado | Sincronização de ficheiros escassos (não estão bloqueadas), mas sincronizam-se com a nuvem como um ficheiro completo. Se o conteúdo do ficheiro mudar na nuvem (ou noutro servidor), o ficheiro já não é escasso quando a alteração é descarregada. |
| Fluxos de dados alternativos (ADS) | Preservado, mas não sincronizado | Por exemplo, as etiquetas de classificação criadas pela Infraestrutura de Classificação de Ficheiros não são sincronizadas. As etiquetas de classificação existentes em ficheiros em cada um dos pontos finais do servidor não são tocadas. |

<a id="files-skipped"></a>O Azure File Sync também ignorará certos ficheiros temporários e pastas do sistema:

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

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções hierárquicas de gestão de armazenamento (HSM)
Nenhuma outra solução HSM deve ser utilizada com o Azure File Sync.

## <a name="identity"></a>Identidade
O Azure File Sync trabalha com a sua identidade padrão baseada em AD sem qualquer configuração especial para além da configuração da sincronização. Quando está a utilizar o Azure File Sync, a expectativa geral é que a maioria dos acessos passem pelos servidores de cache do Ficheiro Azure, em vez de através da partilha de ficheiros Azure. Uma vez que os pontos finais do servidor estão localizados no Windows Server, e o Windows Server tem suportado ACLs de Estilo AD e Windows durante muito tempo, nada é necessário além de garantir que os servidores de ficheiros do Windows registados no Serviço de Sincronização de Armazenamento são de domínio. O Azure File Sync armazenará ACLs nos ficheiros da partilha de ficheiros Azure e irá replicá-los em todos os pontos finais do servidor.

Mesmo que as alterações feitas diretamente na parte de ficheiro Sintetizador de Ficheiros Azure devam demorar mais tempo a sincronizar os pontos finais do servidor no grupo de sincronização, também poderá querer garantir que pode fazer cumprir as suas permissões de AD na sua partilha de ficheiros diretamente na nuvem. Para isso, deve integrar a sua conta de armazenamento no seu AD no local, tal como a forma como os seus servidores de ficheiros Windows são associados ao domínio. Para saber mais sobre a adesão ao domínio da sua conta de armazenamento a um Diretório Ativo propriedade do cliente, consulte a [visão geral do Diretório Ativo dos Ficheiros Azure.](storage-files-active-directory-overview.md)

> [!Important]  
> O domínio que se junta à sua conta de armazenamento ao Ative Directory não é necessário para implementar com sucesso o Azure File Sync. Este é um passo estritamente opcional que permite que a parte de ficheiro Azure aplique os ACLs no local quando os utilizadores montam a partilha de ficheiros Azure diretamente.

## <a name="networking"></a>Redes
O agente Azure File Sync comunica com o seu Serviço de Sincronização de Armazenamento e partilha de ficheiros Azure utilizando o protocolo De sincronização de ficheiros Azure e o protocolo FileREST, ambos utilizando sempre HTTPS sobre a porta 443. O SMB nunca é utilizado para fazer upload ou descarregamento de dados entre o seu Servidor Windows e a partilha de ficheiros Azure. Como a maioria das organizações permite o tráfego HTTPS sobre o porto 443, como requisito para visitar a maioria dos websites, a configuração especial de rede geralmente não é necessária para implementar O Sincronizado de Ficheiros Azure.

Com base na política da sua organização ou requisitos regulamentares únicos, poderá necessitar de uma comunicação mais restritiva com o Azure, pelo que o Azure File Sync fornece vários mecanismos para configurar a rede. Com base nos seus requisitos, pode:

- Sincronização de túneis e tráfego de upload/download de ficheiros sobre a sua ExpressRoute ou VPN Azure. 
- Utilize as funcionalidades de Azure Files e Azure Networking, tais como pontos finais de serviço e pontos finais privados.
- Configure o Sync de Ficheiros Azure para apoiar o seu representante no seu ambiente.
- Atividade de rede de aceleração do Azure File Sync.

Para saber mais sobre a configuração da funcionalidade de networking do Azure File Sync, consulte:
- [Definições do proxy e da firewall do Azure File Sync](storage-sync-files-firewall-and-proxy.md)
- [Garantir o Azure File Sync é um bom vizinho no seu datacenter](storage-sync-files-server-registration.md)

## <a name="encryption"></a>Encriptação
Ao utilizar o Azure File Sync, existem três camadas diferentes de encriptação a considerar: encriptação no armazenamento no descanso do Windows Server, encriptação em trânsito entre o agente Dessincronização de ficheiros Azure e Azure, e encriptação no resto dos seus dados na partilha de ficheiros Azure. 

### <a name="windows-server-encryption-at-rest"></a>Encriptação do Windows Server em repouso 
Existem duas estratégias para encriptar dados no Windows Server que funcionam em geral com o Azure File Sync: encriptação por baixo do sistema de ficheiros de tal forma que o sistema de ficheiros e todos os dados que lhe foram escritos estão encriptados e encriptação dentro do próprio formato de ficheiro. Estes métodos não são mutuamente exclusivos; podem ser usados juntos, se desejarem, uma vez que o propósito da encriptação é diferente.

Para fornecer encriptação abaixo do sistema de ficheiros, o Windows Server fornece uma caixa de entrada BitLocker. O BitLocker é totalmente transparente para o Azure File Sync. A principal razão para usar um mecanismo de encriptação como o BitLocker é prevenir a exfiltração física de dados do seu centro de dados no local por alguém que rouba os discos e para evitar a colocação de um SO não autorizado para realizar leituras/escritos não autorizados aos seus dados. Para saber mais sobre o BitLocker, consulte a [visão geral do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Os produtos de terceiros que funcionam de forma semelhante ao BitLocker, na medida em que se situam abaixo do volume NTFS, devem funcionar de forma totalmente transparente com o Azure File Sync. 

O outro método principal para encriptar dados é encriptar o fluxo de dados do ficheiro quando a aplicação guarda o ficheiro. Algumas aplicações podem fazê-lo de forma nativa, no entanto, normalmente não é o caso. Um exemplo de um método para encriptar o fluxo de dados do ficheiro é a Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Ative Directory RMS. A principal razão para usar um mecanismo de encriptação como o AIP/RMS é impedir a exfiltração de dados da partilha de ficheiros por pessoas que os copiam para locais alternativos, como uma pen ou enviando-os por e-mail para uma pessoa não autorizada. Quando o fluxo de dados de um ficheiro é encriptado como parte do formato de ficheiro, este ficheiro continuará a ser encriptado na parte do ficheiro Azure. 

O Azure File Sync não opera com o NTFS Encrypted File System (NTFS EFS) ou com soluções de encriptação de terceiros que se sentam acima do sistema de ficheiros, mas abaixo do fluxo de dados do ficheiro. 

### <a name="encryption-in-transit"></a>Encriptação em trânsito
O agente Azure File Sync comunica com o seu Serviço de Sincronização de Armazenamento e partilha de ficheiros Azure utilizando o protocolo De sincronização de ficheiros Azure e o protocolo FileREST, ambos utilizando sempre HTTPS sobre a porta 443. O Azure File Sync não envia pedidos não encriptados em HTTP. 

As contas de armazenamento azure contêm um interruptor para exigir encriptação em trânsito, que é ativado por padrão. Mesmo que o interruptor ao nível da conta de armazenamento seja desativado, o que significa que as ligações não encriptadas às suas ações de ficheiro Saqueado são possíveis, o Azure File Sync continuará a utilizar apenas canais encriptados para aceder à sua partilha de ficheiros.

A principal razão para desativar a encriptação em trânsito para a conta de armazenamento é suportar uma aplicação antiga que deve ser executada num sistema operativo mais antigo, como o Windows Server 2008 R2 ou a distribuição mais antiga do Linux, falando diretamente com uma partilha de ficheiros Azure. Se a aplicação legacy falar com o cache do Windows Server da partilha de ficheiros, a toggling desta definição não terá qualquer efeito. 

Recomendamos vivamente que a encriptação de dados em trânsito esteja ativada.

Para obter mais informações sobre encriptação em trânsito, consulte a necessidade de [transferência segura no armazenamento do Azure.](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)

### <a name="azure-file-share-encryption-at-rest"></a>Encriptação de partilha de ficheiros Azure em repouso
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Níveis de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Ativar as ações de ficheiro padrão até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilidade da região de sincronização de ficheiros Azure
O Azure File Sync está disponível nas seguintes regiões:

| Nuvem azul | Região geográfica | Região do Azure | Código da região |
|-------------|-------------------|--------------|-------------|
| Público | Ásia | Ásia Leste | `eastasia` |
| Público | Ásia | Ásia Sudeste | `southeastasia` |
| Público | Austrália | Leste da Austrália | `australiaeast` |
| Público | Austrália | Austrália Sudeste | `australiasoutheast` |
| Público | Brasil | Sul do Brasil | `brazilsouth` |
| Público | Canadá | Canadá Central | `canadacentral` |
| Público | Canadá | Leste do Canadá | `canadaeast` |
| Público | Europa | Europa do Norte | `northeurope` |
| Público | Europa | Europa Ocidental | `westeurope` |
| Público | França | França Central | `francecentral` |
| Público | França | França Sul* | `francesouth` |
| Público | Índia | Índia Central | `centralindia` |
| Público | Índia | Sul da Índia | `southindia` |
| Público | Japão | Leste do Japão | `japaneast` |
| Público | Japão | Oeste do Japão | `japanwest` |
| Público | Coreia | Coreia do Sul Central | `koreacentral` |
| Público | Coreia | Sul da Coreia do Sul | `koreasouth` |
| Público | África do Sul | África do Sul Norte | `southafricanorth` |
| Público | África do Sul | África do Sul Oeste* | `southafricawest` |
| Público | E.A.U. | Central dos Emirados Emirados ArMeTos* | `uaecentral` |
| Público | E.A.U. | Emirados Unidos norte | `uaenorth` |
| Público | RU | Sul do Reino Unido | `uksouth` |
| Público | RU | Oeste do Reino Unido | `ukwest` |
| Público | EUA | E.U.A. Central | `centralus` |
| Público | EUA | E.U.A. Leste | `eastus` |
| Público | EUA | E.U.A. Leste 2 | `eastus2` |
| Público | EUA | E.U.A. Centro-Norte | `northcentralus` |
| Público | EUA | E.U.A. Centro-Sul | `southcentralus` |
| Público | EUA | E.U.A. Centro-Oeste | `westcentralus` |
| Público | EUA | E.U.A. Oeste | `westus` |
| Público | EUA | E.U.A.Oeste 2 | `westus2` |
| Gov dos EUA | EUA | US Gov - Arizona | `usgovarizona` |
| Gov dos EUA | EUA | US Gov - Texas | `usgovtexas` |
| Gov dos EUA | EUA | US Gov - Virginia | `usgovvirginia` |

O Azure File Sync suporta sincronizar apenas com uma partilha de ficheiros Azure que está na mesma região que o Serviço de Sincronização de Armazenamento.

Para as regiões marcadas com asteriscos, deve contactar o Azure Support para solicitar o acesso ao Armazenamento Azure nessas regiões. O processo está delineado [neste documento.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> O armazenamento redundante e redundante geo-zona tem a capacidade de falhar manualmente o armazenamento na região secundária. Recomendamos que não o faça fora de um desastre quando estiver a utilizar o Azure File Sync devido à maior probabilidade de perda de dados. Em caso de desastre em que deseja iniciar uma falha manual de armazenamento, terá de abrir um caso de suporte com a Microsoft para que o Azure File Sync retome a sincronização com o ponto final secundário.

## <a name="migration"></a>Migração
Se tiver um servidor de ficheiros Windows existente, o Azure File Sync pode ser instalado diretamente no local, sem a necessidade de transferir dados para um novo servidor. Se planeia migrar para um novo servidor de ficheiros Windows como parte da adoção do Azure File Sync, existem várias abordagens possíveis para mover dados:

- Crie pontos finais do servidor para a sua antiga partilha de ficheiros e a sua nova partilha de ficheiros e deixe o Sync do Ficheiro Azure sincronizar os dados entre os pontos finais do servidor. A vantagem desta abordagem é que torna muito fácil subscrever demasiado o armazenamento no seu novo servidor de ficheiros, uma vez que o Azure File Sync está ciente de um tiering em nuvem. Quando estiver pronto, pode cortar os utilizadores finais para a partilha de ficheiros no novo servidor e remover o ponto final do servidor da antiga partilha de ficheiros.

- Crie um ponto final do servidor apenas no novo servidor de ficheiros e copie dados a partir da antiga partilha de ficheiros utilizando `robocopy`. Dependendo da topologia das partilhas de ficheiros no seu novo servidor (quantas ações tem em cada volume, quão livre é cada volume, etc.), poderá ter de fornecer temporariamente armazenamento adicional, uma vez que se espera que `robocopy` do seu antigo servidor para o seu novo servidor dentro do seu datacenter no local, será concluída mais rapidamente do que o Azure File Sync irá mover dados para o Azure.

Também é possível utilizar a Data Box para migrar dados para uma implementação de Sincronização de Ficheiros Azure. Na maior parte das vezes, quando os clientes querem usar a Data Box para ingerir dados, fazem-no porque pensam que vai aumentar a velocidade da sua implantação ou porque ajudará com cenários de largura de banda limitados. Embora seja verdade que a utilização de uma Caixa de Dados para ingerir dados na sua implementação do Sync de Ficheiros Azure diminuirá a utilização da largura de banda, é provável que seja mais rápida para a maioria dos cenários prosseguir um upload de dados online através de um dos métodos acima descritos. Para saber mais sobre como utilizar a Data Box para ingerir dados na sua implementação de Sincronização de Ficheiros Azure, consulte os [dados migrate para o Sync de Ficheiros Azure com](storage-sync-offline-data-transfer.md)a Caixa de Dados Azure .

Um erro comum que os clientes cometem ao migrar dados para a sua nova implementação do Web file Sync é copiar dados diretamente para a partilha de ficheiros Do Azure, em vez de nos seus servidores de ficheiros Windows. Apesar de o Azure File Sync identificar todos os novos ficheiros na partilha de ficheiros Azure e sincronizá-los de volta às partilhas dos ficheiros do Windows, este é geralmente consideravelmente mais lento do que carregar dados através do servidor de ficheiros Windows. Muitas ferramentas de cópia Azure, como a AzCopy, têm a desvantagem adicional de não copiar todos os metadados importantes de um ficheiro, como selos temporais e ACLs.

## <a name="antivirus"></a>Antivírus
Como o antivírus funciona através da digitalização de ficheiros para código malicioso conhecido, um produto antivírus pode causar a recolha de ficheiros hierárquicos. Nas versões 4.0 e acima do agente DoSinsem de Ficheiros Azure, os ficheiros hierárquicos têm o atributo seguro do Windows FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definido. Recomendamos consultar o seu fornecedor de software para aprender a configurar a sua solução para ignorar ficheiros de leitura com este conjunto de atributos (muitos fazem-no automaticamente). 

As soluções antivírus internas da Microsoft, o Windows Defender e o System Center Endpoint Protection (SCEP), ambos automaticamente ignoram ficheiros de leitura que tenham este conjunto de atributos. Testámo-los e identificámos um pequeno problema: quando adicionas um servidor a um grupo de sincronização existente, os ficheiros inferiores a 800 bytes são recordados (descarregados) no novo servidor. Estes ficheiros permanecerão no novo servidor e não serão nilutos, uma vez que não cumprem o requisito de tamanho de nidível (>64kb).

> [!Note]  
> Os fornecedores antivírus podem verificar a compatibilidade entre o seu produto e o Azure File Sync utilizando o [Azure File Sync Antivirus Compatibility Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), que está disponível para download no Microsoft Download Center.

## <a name="backup"></a>Cópia de segurança 
Tal como as soluções antivírus, as soluções de backup podem causar a recuperação de ficheiros hierárquicos. Recomendamos a utilização de uma solução de backup em nuvem para fazer backup a partilha de ficheiros Azure em vez de um produto de reserva no local.

Se estiver a utilizar uma solução de backup no local, as cópias de segurança devem ser efetuadas num servidor no grupo de sincronização que tenha desativado o nível de nuvem. Ao efetuar uma restauração, utilize as opções de restauro do nível de volume ou do nível de ficheiro. Os ficheiros restaurados utilizando a opção de restauro do nível de ficheiro serão sincronizados em todos os pontos finais do grupo de sincronização e os ficheiros existentes serão substituídos pela versão restaurada a partir de cópia de segurança.  As restaurações de nível de volume não irão substituir as versões de ficheiromais recentes na partilha de ficheiros Azure ou em outros pontos finais do servidor.

> [!Note]  
> A restauração de bare-metal (BMR) pode causar resultados inesperados e não é suportada atualmente.

> [!Note]  
> Com a versão 9 do agente Dessincronização de Ficheiros Azure, os instantâneos VSS (incluindo o separador Versões Anteriores) são agora suportados em volumes que têm o tiering em nuvem ativado. No entanto, deve ativar a compatibilidade da versão anterior através do PowerShell. [Saiba como](storage-files-deployment-guide.md).

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passos seguintes
* [Considere configurações de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
* [Planning for an Azure Files deployment](storage-files-planning.md) (Planear uma implementação de Ficheiros do Azure)
* [Implementar ficheiros Azure](storage-files-deployment-guide.md)
* [Implementar o Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)