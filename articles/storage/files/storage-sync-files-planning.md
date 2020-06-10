---
title: Planejando uma implementação de Azure File Sync / Microsoft Docs
description: Saiba o que considerar ao planear uma implementação de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: f22b7eca6db55f886dbd4fea563d993bbee11ecb
ms.sourcegitcommit: 5a8c8ac84c36859611158892422fc66395f808dc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/10/2020
ms.locfileid: "84661109"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planear uma implementação da Sincronização de Ficheiros do Azure

:::row:::
    :::column:::
        [![Entrevista e demo introduzindo Azure File Sync - clique para reproduzir!](./media/storage-sync-files-planning/azure-file-sync-interview-video-snapshot.png)](https://www.youtube.com/watch?v=nfWLO7F52-s)
    :::column-end:::
    :::column:::
        Azure File Sync é um serviço que permite cache de várias ações de ficheiros Azure num Windows Server ou em nuvem VM. 
        
        Este artigo apresenta-o aos conceitos e funcionalidades do Azure File Sync. Uma vez que esteja familiarizado com o Azure File Sync, considere seguir o [guia de implementação do Azure File Sync](storage-sync-files-deployment-guide.md) para experimentar este serviço.        
    :::column-end:::
:::row-end:::

Os ficheiros serão armazenados na nuvem nas [ações de ficheiros Azure](storage-files-introduction.md). As ações de ficheiros Azure podem ser utilizadas de duas formas: montando diretamente estas ações de ficheiros Azure sem servidor (SMB) ou caching Azure file shares on-in usando Azure File Sync. Qual a opção de implementação que escolhe altera os aspetos que precisa de considerar como planeia para a sua implementação. 

- **Montagem direta de uma partilha de ficheiros Azure**: Uma vez que os Ficheiros Azure fornecem acesso SMB, pode montar ações de ficheiros Azure no local ou na nuvem utilizando o cliente SMB padrão disponível no Windows, macOS e Linux. Como as ações de ficheiros Azure são sem servidor, a implementação para cenários de produção não requer a gestão de um servidor de ficheiros ou de um dispositivo NAS. Isto significa que não tem de aplicar patches de software ou trocar discos físicos. 

- **A partilha de ficheiros Cache Azure no local com o Azure File Sync**: O Azure File Sync permite centralizar as ações de ficheiros da sua organização em Ficheiros Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma um Servidor Windows no local (ou nuvem) numa cache rápida da sua partilha de ficheiros Azure. 

## <a name="management-concepts"></a>Conceitos de gestão
Uma implementação de Azure File Sync tem três objetos de gestão fundamentais:

- **Azure file share**: Uma partilha de ficheiros Azure é uma partilha de ficheiros em nuvem sem servidor, que fornece o *ponto final* em nuvem de uma relação sincronizada de sincronização de ficheiros Azure. Os ficheiros de uma partilha de ficheiros Azure podem ser acedidos diretamente com o protocolo SMB ou FileREST, embora o incentivemos a aceder principalmente aos ficheiros através da cache do Servidor do Windows quando a partilha de ficheiros Azure estiver a ser utilizada com o Azure File Sync. Isto porque o Azure Files hoje em dia carece de um mecanismo de deteção de alterações eficiente como o Windows Server tem, pelo que as alterações na partilha de ficheiros Azure levarão tempo a propagar-se aos pontos finais do servidor.
- **Ponto final do servidor**: O caminho no Servidor do Windows que está a ser sincronizado com uma partilha de ficheiros Azure. Esta pode ser uma pasta específica num volume ou na raiz do volume. Vários pontos finais do servidor podem existir no mesmo volume se os seus espaços de nome não se sobreporem.
- **Sync group**: O objeto que define a relação de sincronização entre um **ponto final de nuvem**ou partilha de ficheiros Azure e um ponto final do servidor. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Se, por exemplo, tiver dois conjuntos distintos de ficheiros que pretende gerir com o Azure File Sync, criará dois grupos de sincronização e adicionaria diferentes pontos finais a cada grupo de sincronização.

### <a name="azure-file-share-management-concepts"></a>Conceitos de gestão de ações de arquivo Azure
[!INCLUDE [storage-files-file-share-management-concepts](../../../includes/storage-files-file-share-management-concepts.md)]

### <a name="azure-file-sync-management-concepts"></a>Conceitos de gestão de Azure File Sync
Os grupos sync são implantados nos **Serviços de Sincronização de Armazenamento,** que são objetos de alto nível que registam servidores para utilização com Azure File Sync e contêm as relações de grupo sincronizadas. O recurso Storage Sync Service é um par do recurso da conta de armazenamento, e pode ser igualmente implantado em grupos de recursos Azure. Um Serviço de Sincronização de Armazenamento pode criar grupos de sincronização que contenham ações de ficheiros Azure em várias contas de armazenamento e vários Servidores do Windows registados.

Antes de criar um grupo de sincronização num Serviço de Sincronização de Armazenamento, tem primeiro de registar um Servidor Windows com o Serviço de Sincronização de Armazenamento. Isto cria um objeto **de servidor registado,** que representa uma relação de confiança entre o seu servidor ou cluster e o Serviço de Sincronização de Armazenamento. Para registar um Serviço de Sincronização de Armazenamento, tem primeiro de instalar o agente Azure File Sync no servidor. Um servidor ou cluster individual pode ser registado com apenas um Serviço de Sincronização de Armazenamento de cada vez.

Um grupo de sincronização contém um ponto final em nuvem, ou partilha de ficheiros Azure, e pelo menos um ponto final do servidor. O ponto final do servidor contém as definições que configuram a capacidade **de tiering** da nuvem, que fornece a capacidade de caching do Azure File Sync. Para sincronizar com uma partilha de ficheiros Azure, a conta de armazenamento que contém a partilha de ficheiros Azure deve estar na mesma região Azure que o Serviço de Sincronização de Armazenamento.

### <a name="management-guidance"></a>Orientação de gestão
Ao implementar o Azure File Sync, recomendamos:

- A implementação de ficheiros Azure partilha 1:1 com ações de ficheiros Do Windows. O objeto de ponta final do servidor dá-lhe um grande grau de flexibilidade na forma como configura a topologia de sincronização no lado do servidor da relação de sincronização. Para simplificar a gestão, faça com que o caminho do ponto final do servidor corresponda ao caminho da partilha de ficheiros do Windows. 

- Utilize o máximo de serviços de sincronização de armazenamento possível. Isto simplificará a gestão quando tiver grupos de sincronização que contenham vários pontos finais do servidor, uma vez que um Servidor Windows só pode ser registado num Serviço de Sincronização de Armazenamento de cada vez. 

- Prestar atenção às limitações de IOPS de uma conta de armazenamento ao implementar ações de ficheiros Azure. Idealmente, você mapeia ações de arquivo 1:1 com contas de armazenamento, no entanto isso pode nem sempre ser possível devido a vários limites e restrições, tanto da sua organização como da Azure. Quando não for possível ter apenas uma ação de ficheiro implantada numa única conta de armazenamento, considere quais as ações que serão altamente ativas e quais as ações menos ativas para garantir que as ações de ficheiros mais quentes não sejam colocadas na mesma conta de armazenamento em conjunto.

## <a name="windows-file-server-considerations"></a>Considerações do servidor de ficheiros do Windows
Para ativar a capacidade de sincronização no Windows Server, tem de instalar o agente descarregável Azure File Sync. O agente Azure File Sync fornece dois componentes principais: `FileSyncSvc.exe` o serviço Windows de fundo responsável pela monitorização das alterações nos pontos finais do servidor e início das sessões de sincronização `StorageSync.sys` e, um filtro do sistema de ficheiros que permite o tiering da nuvem e a rápida recuperação de desastres.  

### <a name="operating-system-requirements"></a>Requisitos do sistema operativo
O Azure File Sync é suportado com as seguintes versões do Windows Server:

| Versão | SKUs apoiados | Opções de implantação suportadas |
|---------|----------------|------------------------------|
| Windows Server 2019 | Datacenter, Standard e IoT | Cheio e Núcleo |
| Windows Server 2016 | Datacenter, Standard e Servidor de Armazenamento | Cheio e Núcleo |
| Windows Server 2012 R2 | Datacenter, Standard e Servidor de Armazenamento | Cheio e Núcleo |

Futuras versões do Windows Server serão adicionadas à medida que forem lançadas.

> [!Important]  
> Recomendamos manter todos os servidores que utiliza com o Azure File Sync atualizados com as últimas atualizações do Windows Update. 

### <a name="minimum-system-resources"></a>Recursos mínimos do sistema
O Azure File Sync requer um servidor, físico ou virtual, com pelo menos um CPU e um mínimo de 2 GiB de memória.

> [!Important]  
> Se o servidor estiver a funcionar numa máquina virtual com memória dinâmica ativada, o VM deve ser configurado com um mínimo de 2048 MiB de memória.

Para a maioria das cargas de trabalho de produção, não recomendamos configurar um servidor de sincronização Azure File Sync apenas com os requisitos mínimos. Consulte [os recursos recomendados](#recommended-system-resources) do sistema para obter mais informações.

### <a name="recommended-system-resources"></a>Recursos recomendados do sistema
Tal como qualquer funcionalidade ou aplicação do servidor, os requisitos de recursos do sistema para Azure File Sync são determinados pela escala da implementação; implementações maiores num servidor requerem mais recursos do sistema. Para O Azure File Sync, a escala é determinada pelo número de objetos através dos pontos finais do servidor e pelo churn no conjunto de dados. Um único servidor pode ter pontos finais do servidor em vários grupos de sincronização e o número de objetos listados nas seguintes contas de tabela para o espaço de nome completo a que um servidor está ligado. 

Por exemplo, o ponto final do servidor A com 10 milhões de objetos + ponto final do servidor B com 10 milhões de objetos = 20 milhões de objetos. Para este exemplo, recomendamos 8 CPUs, 16 GiB de memória para estado estável, e (se possível) 48 GiB de memória para a migração inicial.
 
Os dados do espaço de nome são armazenados na memória por razões de desempenho. Por causa disso, espaços de nome maiores requerem mais memória para manter um bom desempenho, e mais churn requer mais CPU para processar. 
 
Na tabela seguinte, fornecemos tanto o tamanho do espaço de nome como uma conversão para capacidade para as ações típicas de ficheiros de finalidade geral, onde o tamanho médio do ficheiro é de 512 KiB. Se os tamanhos do seu ficheiro forem menores, considere adicionar memória adicional para a mesma quantidade de capacidade. Baseie a configuração da sua memória no tamanho do espaço de nomes.

| Tamanho do espaço de nome - ficheiros & diretórios (milhões)  | Capacidade típica (TiB)  | Núcleos do CPU  | Memória recomendada (GiB) |
|---------|---------|---------|---------|
| 3        | 1.4     | 2        | 8 (sincronização inicial)/ 2 (churn típico)      |
| 5        | 2.3     | 2        | 16 (sincronização inicial)/ 4 (churn típico)    |
| 10       | 4.7     | 4        | 32 (sincronização inicial)/ 8 (churn típico)   |
| 30       | 14.0    | 8        | 48 (sincronização inicial)/ 16 (churn típico)   |
| 50       | 23.3    | 16       | 64 (sincronização inicial)/ 32 (churn típico)  |
| 100*     | 46.6    | 32       | 128 (sincronização inicial)/ 32 (churn típico)  |

\*Sincronizar mais de 100 milhões de ficheiros & diretórios não é recomendado neste momento. Este é um limite suave baseado nos nossos limiares testados. Para obter mais informações, consulte [os objetivos de escalabilidade e desempenho dos Ficheiros Azure.](storage-files-scale-targets.md#azure-file-sync-scale-targets)

> [!TIP]
> A sincronização inicial de um espaço de nome é uma operação intensiva e recomendamos a atribuição de mais memória até que a sincronização inicial esteja completa. Isto não é necessário, mas.pode acelerar a sincronização inicial. 
> 
> O churn típico é de 0,5% do espaço de nome que muda por dia. Para níveis mais elevados de churn, considere adicionar mais CPU. 

- Um volume ligado localmente formatado com o sistema de ficheiros NTFS.

### <a name="evaluation-cmdlet"></a>Cmdlet de avaliação
Antes de implementar o Azure File Sync, deverá avaliar se é compatível com o seu sistema utilizando o cmdlet de avaliação de Ficheiros Azure. Este cmdlet verifica possíveis problemas com o seu sistema de ficheiros e conjunto de dados, tais como caracteres não suportados ou uma versão do sistema operativo não suportada. Os seus controlos abrangem a maioria, mas não todas as características a seguir mencionadas; recomendamos que leia cuidadosamente o resto desta secção para garantir que a sua implantação decorta sem problemas. 

O cmdlet de avaliação pode ser instalado instalando o módulo Az PowerShell, que pode ser instalado seguindo as instruções aqui: [Instalar e configurar a Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps).

#### <a name="usage"></a>Utilização  
Pode invocar a ferramenta de avaliação de várias maneiras: pode efetuar as verificações do sistema, as verificações do conjunto de dados ou ambas. Para efetuar as verificações do sistema e do conjunto de dados: 

```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Para testar apenas o seu conjunto de dados:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Apenas para testar os requisitos do sistema:
```powershell
Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Para mostrar os resultados em CSV:
```powershell
$errors = Invoke-AzStorageSyncCompatibilityCheck […]
$errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="file-system-compatibility"></a>Compatibilidade do sistema de ficheiros
O Azure File Sync só é suportado em volumes NTFS diretamente ligados. O armazenamento direto anexado, ou DAS, no Windows Server significa que o sistema operativo Windows Server é dono do sistema de ficheiros. Das pode ser fornecida através da fixação física de discos ao servidor de ficheiros, anexando discos virtuais a um VM do servidor de ficheiros (como um VM hospedado por Hyper-V), ou mesmo através do ISCSI.

Apenas os volumes NTFS são suportados; ReFS, FAT, FAT32 e outros sistemas de ficheiros não são suportados.

A tabela a seguir mostra o estado de interop das funcionalidades do sistema de ficheiros NTFS: 

| Funcionalidade | Estado do suporte | Notas |
|---------|----------------|-------|
| Listas de controlo de acesso (ACL) | Totalmente suportado | As listas de controlo de acesso discricionário do estilo Windows são preservadas pelo Azure File Sync e são executadas pelo Windows Server nos pontos finais do servidor. Os ACLs também podem ser aplicados ao montar diretamente a partilha de ficheiros Azure, no entanto isso requer uma configuração adicional. Consulte a [secção Identidade](#identity) para mais informações. |
| Ligações fixas | Ignorado | |
| Ligações simbólicas | Ignorado | |
| Pontos de montagem | Parcialmente suportado | Os pontos de montagem podem ser a raiz de um ponto final do servidor, mas são ignorados se estiverem contidos no espaço de nome de um ponto final do servidor. |
| Junções | Ignorado | Por exemplo, pastas DfrsrPrivate e DFSRoots do Sistema de Ficheiros Distribuídos. |
| Pontos de reanálise | Ignorado | |
| Compressão NTFS | Totalmente suportado | |
| Ficheiros escassos | Totalmente suportado | Os ficheiros escassos sincronizam(não estão bloqueados), mas sincronizam-se com a nuvem como um ficheiro completo. Se o conteúdo do ficheiro mudar na nuvem (ou noutro servidor), o ficheiro deixará de ser escasso quando a alteração é descarregada. |
| Fluxos de Dados Alternativos (ADS) | Preservado, mas não sincronizado | Por exemplo, as etiquetas de classificação criadas pela Infraestrutura de Classificação de Ficheiros não são sincronizadas. As etiquetas de classificação existentes em ficheiros em cada um dos pontos finais do servidor são deixadas intactas. |

<a id="files-skipped"></a>O Azure File Sync também saltará certos ficheiros temporários e pastas do sistema:

| Arquivo/pasta | Nota |
|-|-|
| pagefile.sys | Arquivo específico para o sistema |
| Desktop.ini | Arquivo específico para o sistema |
| polegares.db | Arquivo temporário para miniaturas |
| ehthumbs.db | Arquivo temporário para miniaturas de mídia |
| ~$\*.\* | Arquivo temporário do escritório |
| \*.tmp | Arquivo temporário |
| \*.laccdb | Ficheiro de bloqueio DB de acesso|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Arquivo Sincronizado Interno|
| \\Informação sobre o Volume do Sistema | Pasta específica ao volume |
| $RECYCLE. BIN| Pasta |
| \\SyncShareState | Pasta para Sincronização |

### <a name="failover-clustering"></a>Clustering de Ativação Pós-falha
O Cluster de Falha de Falha do Servidor do Windows é suportado pelo Azure File Sync para a opção de implementação "File Server for general use". O Clustering de Failover não é suportado em "Scale-Out File Server for application data" (SOFS) ou em Volumes Compartilhados Agrupados (CSVs).

> [!Note]  
> O agente Azure File Sync deve ser instalado em todos os nós de um Cluster failover para que a sincronização funcione corretamente.

### <a name="data-deduplication"></a>Eliminação de Dados Duplicados
**Windows Server 2016 e Windows Server 2019**   
A Deduplica de Dados é suportada em volumes com tiering de nuvem ativado no Windows Server 2016 e Windows Server 2019. Permitir a desduplicação de dados num volume com nivelamento de nuvem ativado permite cache de mais ficheiros no local sem armazenar mais armazenamento. 

Quando a Deduplicação de Dados é ativada num volume com o tiering de nuvem ativado, os ficheiros otimizados do Dedup dentro da localização do ponto final do servidor serão nivelados semelhantes a um ficheiro normal baseado nas definições da política de tiering da nuvem. Uma vez que os ficheiros otimizados do Dedup tenham sido nivelados, o trabalho de recolha de lixo de deduplica de dados será executado automaticamente para recuperar o espaço do disco, removendo pedaços desnecessários que já não são referenciados por outros ficheiros no volume.

Note que as poupanças de volume só se aplicam ao servidor; os seus dados na partilha de ficheiros Azure não serão desativados.

> [!Note]  
> Para suportar a desduplicação de dados em volumes com nivelamento de nuvem ativado no Windows Server 2019, a atualização do Windows [KB4520062](https://support.microsoft.com/help/4520062) deve ser instalada e é necessária a versão 9.0.0 ou mais recente do agente Azure File Sync.

**Windows Server 2012 R2**  
O Azure File Sync não suporta a desduplicação de dados e o tiering de nuvem no mesmo volume no Windows Server 2012 R2. Se a desduplicação de dados estiver ativada num volume, o nível da nuvem deve ser desativado. 

**Notas**
- Se a Desduplicação de Dados for instalada antes de instalar o agente Azure File Sync, é necessário reiniciar para suportar a desduplicação de dados e o tiering de nuvem no mesmo volume.
- Se a desduplicação de dados for ativada num volume após o tiering da nuvem, o trabalho inicial de otimização de deduplica irá otimizar ficheiros no volume que ainda não estão nivelados e terá o seguinte impacto no tiering da nuvem:
    - A política de espaço livre continuará a tierar ficheiros de acordo com o espaço livre do volume utilizando o mapa de calor.
    - A política de datas saltará o tiering de ficheiros que podem ter sido elegíveis para tiering devido ao trabalho de otimização de Deduplica que acede aos ficheiros.
- Para os trabalhos de otimização de deduplica em curso, o tiering em nuvem com a política de data será adiado pela definição de Data Deduplication [MinimumFileAgeDays,](https://docs.microsoft.com/powershell/module/deduplication/set-dedupvolume?view=win10-ps) se o ficheiro ainda não estiver nivelado. 
    - Exemplo: Se a definição de Preenchimento Mínimo Desembaraçado for de sete dias e a política de data de tiering em nuvem for de 30 dias, a política de datas irá tier-files após 37 dias.
    - Nota: Uma vez que um ficheiro é hierárquico por Azure File Sync, a função de otimização de deduplica saltará o ficheiro.
- Se um servidor que executa o Windows Server 2012 R2 com o agente Azure File Sync instalado for atualizado para o Windows Server 2016 ou Windows Server 2019, devem ser executados os seguintes passos para suportar a desduplicação de dados e o nível de nuvem no mesmo volume:  
    - Desinstale o agente Azure File Sync para o Windows Server 2012 R2 e reinicie o servidor.
    - Descarregue o agente Azure File Sync para a versão do novo sistema operativo do servidor (Windows Server 2016 ou Windows Server 2019).
    - Instale o agente Azure File Sync e reinicie o servidor.  
    
    Nota: As definições de configuração do Azure File Sync no servidor são mantidas quando o agente é desinstalado e reinstalado.

### <a name="distributed-file-system-dfs"></a>Sistema de ficheiros distribuídos (DFS)
A Azure File Sync suporta o interop com os espaços de nomes DFS (DFS-N) e a replicação dfs (DFS-R).

**DFS Namespaces (DFS-N)**: O Azure File Sync está totalmente suportado em servidores DFS-N. Pode instalar o agente Azure File Sync num ou mais membros DFS-N para sincronizar dados entre os pontos finais do servidor e o ponto final da nuvem. Para obter mais informações, consulte [a visão geral do DFS Namespaces](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**Replicação DFS (DFS-R)**: Uma vez que DFS-R e Azure File Sync são ambas soluções de replicação, na maioria dos casos, recomendamos a substituição dfs-R por Azure File Sync. Existem, no entanto, vários cenários em que gostaria de utilizar o DFS-R e o Azure File Sync juntos:

- Está a migrar de uma implementação DFS-R para uma implementação de Azure File Sync. Para obter mais informações, consulte [a implementação de uma replicação DFS (DFS-R) para Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem todos os servidores no local que necessitem de uma cópia dos seus dados de ficheiros podem ser ligados diretamente à internet.
- Os servidores de filial consolidam os dados num único servidor de hub, para o qual gostaria de utilizar o Azure File Sync.

Para que o Azure File Sync e o DFS-R funcionem lado a lado:

1. O nível de nuvem Azure File Sync deve ser desativado em volumes com pastas replicadas DFS-R.
2. Os pontos finais do servidor não devem ser configurados nas pastas de replicação apenas de leitura DFS-R.

Para obter mais informações, consulte [a visão geral da replicação do DFS.](https://technet.microsoft.com/library/jj127250)

### <a name="sysprep"></a>Sysprep
A utilização de sysprep num servidor que tenha o agente Azure File Sync instalado não é suportada e pode levar a resultados inesperados. A instalação do agente e o registo do servidor devem ocorrer após a implementação da imagem do servidor e completar a mini-configuração do Sysprep.

### <a name="windows-search"></a>Windows Search
Se o tiering da nuvem estiver ativado num ponto final do servidor, os ficheiros que são nivelados são ignorados e não indexados pela Pesquisa do Windows. Os ficheiros não hierárquicos estão devidamente indexados.

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções hierárquicas de gestão de armazenamento (HSM)
Nenhuma outra solução HSM deve ser utilizada com a Azure File Sync.

## <a name="identity"></a>Identidade
O Azure File Sync funciona com a sua identidade padrão baseada em AD sem qualquer configuração especial para além de configurar sincronização. Quando está a utilizar o Azure File Sync, a expectativa geral é que a maioria dos acessos passe pelos servidores de caching de ficheiros Azure, em vez de através da partilha de ficheiros Azure. Uma vez que os pontos finais do servidor estão localizados no Windows Server, e o Windows Server tem suportado ACLs de estilo AD e Windows durante muito tempo, nada é necessário para além de garantir que os servidores de ficheiros do Windows registados no Serviço de Sincronização de Armazenamento estão ligados ao domínio. O Azure File Sync irá armazenar ACLs nos ficheiros na partilha de ficheiros Azure e irá replicá-los em todos os pontos finais do servidor.

Apesar de as alterações efetuadas diretamente na partilha de ficheiros Azure demorarem mais tempo a sincronizar os pontos finais do servidor no grupo de sincronização, também poderá querer garantir que pode impor as suas permissões de AD na sua partilha de ficheiros diretamente na nuvem. Para isso, tem de se juntar à sua conta de armazenamento para o seu AD no local, tal como os seus servidores de ficheiros Windows estão ligados ao domínio. Para saber mais sobre o domínio que une a sua conta de armazenamento a um Ative Directory de propriedade do cliente, consulte [a visão geral do Azure Files Ative Directory](storage-files-active-directory-overview.md).

> [!Important]  
> O domínio que une a sua conta de armazenamento ao Ative Directory não é necessário para implementar com sucesso o Azure File Sync. Este é um passo estritamente opcional que permite que a partilha de ficheiros Azure aplique ACLs no local quando os utilizadores montam a partilha de ficheiros Azure diretamente.

## <a name="networking"></a>Redes
O agente Azure File Sync comunica com o seu Serviço de Sincronização de Armazenamento e partilha de ficheiros Azure utilizando o protocolo Azure File Sync REST e o protocolo FileREST, ambos os quais utilizam sempre HTTPS sobre a porta 443. O SMB nunca é utilizado para carregar ou transferir dados entre o seu Servidor do Windows e a partilha de ficheiros Azure. Como a maioria das organizações permite o tráfego HTTPS sobre a porta 443, como requisito para visitar a maioria dos websites, a configuração especial de rede não é geralmente necessária para implementar O Azure File Sync.

Com base na política da sua organização ou requisitos regulamentares únicos, poderá necessitar de uma comunicação mais restritiva com o Azure, pelo que o Azure File Sync fornece vários mecanismos para configurar a rede. Com base nos seus requisitos, pode:

- Sincronização de túneis e carregamento de ficheiros/descarregamento de tráfego sobre o seu ExpressRoute ou Azure VPN. 
- Utilize os recursos de Azure Files e Azure Networking, tais como pontos finais de serviço e pontos finais privados.
- Configure Azure File Sync para suportar o seu representante no seu ambiente.
- Atividade da rede de aceleração a partir de Azure File Sync.

Para saber mais sobre o Azure File Sync e networking, consulte [as considerações de networking do Azure File Sync](storage-sync-files-networking-overview.md).

## <a name="encryption"></a>Encriptação
Ao utilizar o Azure File Sync, existem três camadas diferentes de encriptação a considerar: encriptação no armazenamento em repouso do Windows Server, encriptação em trânsito entre o agente Azure File Sync e Azure, e encriptação no resto dos seus dados na partilha de ficheiros Azure. 

### <a name="windows-server-encryption-at-rest"></a>Encriptação do Servidor do Windows em repouso 
Existem duas estratégias para encriptar dados no Windows Server que funcionam geralmente com o Azure File Sync: encriptação por baixo do sistema de ficheiros de modo a que o sistema de ficheiros e todos os dados escritos nele estejam encriptados e encriptação dentro do próprio formato de ficheiro. Estes métodos não são mutuamente exclusivos; podem ser usados juntos se desejarem, uma vez que o propósito da encriptação é diferente.

Para fornecer encriptação por baixo do sistema de ficheiros, o Windows Server fornece a caixa de entrada BitLocker. O BitLocker é totalmente transparente para o Azure File Sync. A principal razão para usar um mecanismo de encriptação como o BitLocker é impedir a exfiltração física de dados do seu datacenter no local por alguém que rouba os discos e para evitar que o sistema lateralmente um SISTEMA não autorizado execute leituras/escritas não autorizadas para os seus dados. Para saber mais sobre o BitLocker, consulte [a visão geral do BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview).

Os produtos de terceiros que funcionam da mesma forma que o BitLocker, na medida em que se sentam abaixo do volume NTFS, devem igualmente funcionar de forma totalmente transparente com o Azure File Sync. 

O outro método principal para encriptar dados é encriptar o fluxo de dados do ficheiro quando a aplicação guarda o ficheiro. Algumas aplicações podem fazê-lo de forma nativa, no entanto, geralmente não é o caso. Um exemplo de um método para encriptar o fluxo de dados do ficheiro é a Azure Information Protection (AIP)/Azure Rights Management Services (Azure RMS)/Ative Directory RMS. A principal razão para usar um mecanismo de encriptação como o AIP/RMS é impedir a exfiltração de dados da partilha de dados por pessoas que o copiam para localizações alternativas, como uma pen drive, ou enviar por e-mail para uma pessoa não autorizada. Quando o fluxo de dados de um ficheiro é encriptado como parte do formato de ficheiro, este ficheiro continuará a ser encriptado na partilha de ficheiros Azure. 

O Azure File Sync não interopera com o Sistema de Ficheiros Encriptados NTFS (NTFS EFS) ou com soluções de encriptação de terceiros que se situam acima do sistema de ficheiros, mas abaixo do fluxo de dados do ficheiro. 

### <a name="encryption-in-transit"></a>Encriptação de dados em circulação

> [!NOTE]
> O serviço Azure File Sync removerá o suporte para TLS1.0 e 1.1 no dia 1 de agosto de 2020. Todas as versões suportadas do agente Azure File Sync já utilizam tLS1.2 por predefinição. A utilização de uma versão anterior do TLS pode ocorrer se o TLS1.2 for desativado no seu servidor ou se for utilizado um representante. Se estiver a utilizar um representante, recomendamos que verifique a configuração do proxy. As regiões de serviço Azure File Sync adicionadas após 5/1/2020 apenas apoiarão tLS1.2 e o suporte para TLS1.0 e 1.1 será removido das regiões existentes em 1 de agosto de 2020.  Para mais informações, consulte o [guia de resolução de problemas.](storage-sync-files-troubleshoot.md#tls-12-required-for-azure-file-sync)

O agente Azure File Sync comunica com o seu Serviço de Sincronização de Armazenamento e partilha de ficheiros Azure utilizando o protocolo Azure File Sync REST e o protocolo FileREST, ambos os quais utilizam sempre HTTPS sobre a porta 443. O Azure File Sync não envia pedidos não encriptados em HTTP. 

As contas de armazenamento Azure contêm um interruptor para exigir encriptação em trânsito, que é ativado por padrão. Mesmo que o comutador ao nível da conta de armazenamento seja desativado, o que significa que as ligações não encriptadas às suas ações de ficheiros Azure são possíveis, o Azure File Sync ainda utilizará apenas canais encriptados para aceder à sua partilha de ficheiros.

A principal razão para desativar a encriptação em trânsito para a conta de armazenamento é suportar uma aplicação antiga que deve ser executada num sistema operativo mais antigo, como o Windows Server 2008 R2 ou a distribuição linux mais antiga, falando diretamente com uma partilha de ficheiros Azure. Se a aplicação legado falar com a cache do Servidor do Windows da partilha de ficheiros, a torção desta definição não terá qualquer efeito. 

Recomendamos vivamente garantir que a encriptação dos dados em trânsito está ativada.

Para obter mais informações sobre encriptação em trânsito, consulte [a necessidade de transferência segura no armazenamento Azure](../common/storage-require-secure-transfer.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json).

### <a name="azure-file-share-encryption-at-rest"></a>Encriptação de partilha de ficheiros Azure em repouso
[!INCLUDE [storage-files-encryption-at-rest](../../../includes/storage-files-encryption-at-rest.md)]

## <a name="storage-tiers"></a>Camadas de armazenamento
[!INCLUDE [storage-files-tiers-overview](../../../includes/storage-files-tiers-overview.md)]

### <a name="enable-standard-file-shares-to-span-up-to-100-tib"></a>Permitir que as ações de ficheiros padrão se estendem até 100 TiB
[!INCLUDE [storage-files-tiers-enable-large-shares](../../../includes/storage-files-tiers-enable-large-shares.md)]

#### <a name="regional-availability"></a>Disponibilidade regional
[!INCLUDE [storage-files-tiers-large-file-share-availability](../../../includes/storage-files-tiers-large-file-share-availability.md)]

## <a name="azure-file-sync-region-availability"></a>Disponibilidade da região de sincronização de ficheiros Azure
O Azure File Sync está disponível nas seguintes regiões:

| Nuvem do Azure | Região geográfica | Região do Azure | Código da região |
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
| Público | E.A.U. | UAE Central* | `uaecentral` |
| Público | E.A.U. | Uae Norte | `uaenorth` |
| Público | REINO UNIDO | Sul do Reino Unido | `uksouth` |
| Público | REINO UNIDO | Oeste do Reino Unido | `ukwest` |
| Público | EUA | E.U.A. Central | `centralus` |
| Público | EUA | E.U.A. Leste | `eastus` |
| Público | EUA | E.U.A. Leste 2 | `eastus2` |
| Público | EUA | E.U.A. Centro-Norte | `northcentralus` |
| Público | EUA | E.U.A. Centro-Sul | `southcentralus` |
| Público | EUA | E.U.A. Centro-Oeste | `westcentralus` |
| Público | EUA | E.U.A. Oeste | `westus` |
| Público | EUA | E.U.A.Oeste 2 | `westus2` |
| US Gov | EUA | US Gov - Arizona | `usgovarizona` |
| US Gov | EUA | US Gov - Texas | `usgovtexas` |
| US Gov | EUA | US Gov - Virginia | `usgovvirginia` |

O Azure File Sync suporta sincronização apenas com uma partilha de ficheiros Azure que está na mesma região que o Serviço de Sincronização de Armazenamento.

Para as regiões marcadas com asteriscos, deve contactar o Apoio Azure para solicitar o acesso ao Azure Storage nessas regiões. O processo está delineado [neste documento.](https://azure.microsoft.com/global-infrastructure/geographies/)

## <a name="redundancy"></a>Redundância
[!INCLUDE [storage-files-redundancy-overview](../../../includes/storage-files-redundancy-overview.md)]

> [!Important]  
> O armazenamento redundante geo-redundante e geo-zona tem a capacidade de falhar manualmente o armazenamento para a região secundária. Recomendamos que não o faça fora de um desastre quando estiver a utilizar o Azure File Sync devido à maior probabilidade de perda de dados. Em caso de desastre em que gostaria de iniciar uma falha manual de armazenamento, terá de abrir um caso de suporte com a Microsoft para que o Azure File Sync retome a sincronização com o ponto final secundário.

## <a name="migration"></a>Migração
Se tiver um servidor de ficheiros Windows existente, o Azure File Sync pode ser instalado diretamente no local, sem a necessidade de transferir dados para um novo servidor. Se estiver a planear migrar para um novo servidor de ficheiros Windows como parte da adoção do Azure File Sync, existem várias abordagens possíveis para transferir dados:

- Crie pontos finais do servidor para a sua antiga partilha de ficheiros e a sua nova partilha de ficheiros e deixe o Azure File Sync sincronizar os dados entre os pontos finais do servidor. A vantagem desta abordagem é que torna muito fácil subscrever o armazenamento no seu novo servidor de ficheiros, uma vez que o Azure File Sync está ciente do tiering de nuvem. Quando estiver pronto, pode cortar os utilizadores finais para a partilha de ficheiros no novo servidor e remover o ponto final do servidor da antiga partilha de ficheiros.

- Crie um ponto final do servidor apenas no novo servidor de ficheiros e copie dados da antiga partilha de ficheiros utilizando `robocopy` . Dependendo da topologia das ações de ficheiros no seu novo servidor (quantas ações tem em cada volume, quão livre é cada volume, etc.), poderá ter de providenciar temporariamente armazenamento adicional, uma vez que se espera que `robocopy` do seu antigo servidor para o seu novo servidor dentro do seu datacenter no local se complete mais rapidamente do que o Azure File Sync irá mover dados para o Azure.

Também é possível utilizar a Caixa de Dados para migrar dados para uma implementação de Azure File Sync. Na maior parte das vezes, quando os clientes querem usar a Data Box para ingerir dados, fazem-no porque acham que vai aumentar a velocidade da sua implantação ou porque vai ajudar com cenários de largura de banda restritas. Embora seja verdade que usar uma Caixa de Dados para ingerir dados na sua implementação de Azure File Sync diminuirá a utilização da largura de banda, provavelmente será mais rápido para a maioria dos cenários perseguir um upload de dados online através de um dos métodos acima descritos. Para saber mais sobre como utilizar a Caixa de Dados para ingerir dados na sua implementação de Azure File Sync, consulte [os dados da Migração no Azure File Sync com a Azure Data Box](storage-sync-offline-data-transfer.md).

Um erro comum que os clientes cometem ao migrar dados para a sua nova implementação do Azure File Sync é copiar dados diretamente para a partilha de ficheiros Azure, em vez de nos seus servidores de ficheiros Windows. Embora o Azure File Sync identifique todos os novos ficheiros da partilha de ficheiros Azure e os sincronize de volta às partilhas de ficheiros do Windows, este é geralmente consideravelmente mais lento do que carregar dados através do servidor de ficheiros Windows. Ao utilizar ferramentas de cópia Azure, como a AzCopy, é importante utilizar a versão mais recente. Consulte a tabela de ferramentas de cópia de [ficheiros](storage-files-migration-overview.md#file-copy-tools) para obter uma visão geral das ferramentas de cópia do Azure para garantir que pode copiar todos os metadados importantes de um ficheiro, tais como timetamps e ACLs.

## <a name="antivirus"></a>Antivírus
Como o antivírus funciona através da verificação de ficheiros para código malicioso conhecido, um produto antivírus pode causar a recolha de ficheiros hierárquicos. Nas versões 4.0 e acima do agente Azure File Sync, os ficheiros hierárquicos têm o atributo Windows seguro FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS conjunto. Recomendamos consultar o seu fornecedor de software para aprender a configurar a sua solução para saltar ficheiros de leitura com este conjunto de atributos (muitos o fazem automaticamente). 

As soluções antivírus internas da Microsoft, o Windows Defender e o System Center Endpoint Protection (SCEP), ambos saltam automaticamente os ficheiros de leitura que têm este conjunto de atributos. Testámo-los e identificámos um pequeno problema: quando adicionas um servidor a um grupo de sincronização existente, ficheiros com menos de 800 bytes são recolhidos (descarregados) no novo servidor. Estes ficheiros permanecerão no novo servidor e não serão nivelados, uma vez que não cumprem o requisito de tamanho de tiering (>64kb).

> [!Note]  
> Os fornecedores antivírus podem verificar a compatibilidade entre o seu produto e o Azure File Sync, utilizando o [Azure File Sync Antivirus Compatibilidade Test Suite](https://www.microsoft.com/download/details.aspx?id=58322), que está disponível para download no Microsoft Download Center.

## <a name="backup"></a>Cópia de segurança 
Tal como as soluções antivírus, as soluções de backup podem causar a recolha de ficheiros hierárquicos. Recomendamos a utilização de uma solução de backup em nuvem para fazer backup da partilha de ficheiros Azure em vez de um produto de backup no local.

Se estiver a utilizar uma solução de backup no local, devem ser realizadas cópias de segurança num servidor do grupo de sincronização que tenha o tiering da nuvem desativado. Ao efetuar uma restauração, utilize as opções de restauro de nível de volume ou de nível de ficheiro. Os ficheiros restaurados utilizando a opção de restauro do nível de ficheiro serão sincronizados em todos os pontos finais do grupo de sincronização e os ficheiros existentes serão substituídos pela versão restaurada a partir da cópia de segurança.  As restaurações ao nível do volume não substituirão as versões de ficheiros mais recentes na partilha de ficheiros Azure ou noutros pontos finais do servidor.

> [!Note]  
> A restauração de metal nu (BMR) pode causar resultados inesperados e não está atualmente suportada.

> [!Note]  
> Com a versão 9 do agente Azure File Sync, as imagens VSS (incluindo o separador Versões Anteriores) são agora suportadas em volumes que tenham o tiering de nuvem ativado. No entanto, deve ativar a compatibilidade da versão anterior através do PowerShell. [Saiba como.](storage-files-deployment-guide.md)

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Próximos passos
* [Considere as definições de firewall e proxy](storage-sync-files-firewall-and-proxy.md)
* [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
* [Implementar os Ficheiros do Azure](storage-files-deployment-guide.md)
* [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorizar o Azure File Sync](storage-sync-files-monitoring.md)