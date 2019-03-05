---
title: Planear uma implementação do Azure File Sync | Documentos da Microsoft
description: Saiba o que considerar quando planear uma implementação de ficheiros do Azure.
services: storage
author: wmgries
ms.service: storage
ms.topic: article
ms.date: 2/7/2019
ms.author: wgries
ms.subservice: files
ms.openlocfilehash: 97508c05ea26df1aaae7f41738835380767c81d2
ms.sourcegitcommit: 3f4ffc7477cff56a078c9640043836768f212a06
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/04/2019
ms.locfileid: "57312720"
---
# <a name="planning-for-an-azure-file-sync-deployment"></a>Planear uma implementação da Sincronização de Ficheiros do Azure
Utilize o Azure File Sync para centralizar as partilhas de ficheiros da sua organização nos ficheiros do Azure, mantendo a flexibilidade, desempenho e compatibilidade de um servidor de ficheiros no local. O Azure File Sync transforma o Windows Server numa cache rápida da sua partilha de ficheiros do Azure. Pode usar qualquer protocolo disponível no Windows Server para aceder aos seus dados localmente, incluindo SMB, NFS e FTPS. Pode ter o número de caches que precisar em todo o mundo.

Este artigo descreve considerações importantes para uma implementação do Azure File Sync. Recomendamos que leia também [planear uma implementação de ficheiros do Azure](storage-files-planning.md). 

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="azure-file-sync-terminology"></a>Terminologia de sincronização de ficheiros do Azure
Antes de entrar em detalhes sobre como planear uma implementação de sincronização de ficheiros do Azure, é importante compreender a terminologia.

### <a name="storage-sync-service"></a>Serviço de Sincronização de Armazenamento
O serviço de sincronização de armazenamento é o recurso de nível superior do Azure para o Azure File Sync. O recurso de serviço de sincronização de armazenamento é um elemento de rede do recurso de conta de armazenamento e da mesma forma pode ser implementado em grupos de recursos do Azure. Recursos de nível superior distintos do recurso de conta de armazenamento é necessário porque o serviço de sincronização de armazenamento podem criar relacionamentos de sincronização com várias contas de armazenamento através de vários grupos de sincronização. Uma subscrição pode ter vários recursos de serviço de sincronização de armazenamento implementados.

### <a name="sync-group"></a>Grupo de sincronização
Os grupos de sincronização definem a topologia da sincronização para um conjunto de ficheiros. Os pontos finais num grupo de sincronização são mantidos em sincronia entre si. Se, por exemplo, tem dois conjuntos distintos de ficheiros que pretende gerir com o Azure File Sync, poderia criar dois grupos de sincronização e adicionar pontos de extremidade diferentes para cada grupo de sincronização. Um serviço de sincronização de armazenamento pode alojar os grupos de sincronização, conforme necessário.  

### <a name="registered-server"></a>Servidor registado
O objeto de servidor registado representa uma relação de confiança entre o servidor (ou cluster) e o serviço de sincronização de armazenamento. Pode registar os servidores a uma instância de serviço de sincronização de armazenamento que quiser. No entanto, um servidor (ou cluster) pode ser registrado com apenas um serviço de sincronização de armazenamento cada vez.

### <a name="azure-file-sync-agent"></a>Agente de sincronização de ficheiros do Azure
O agente do Azure File Sync é um pacote transferível que permite a sincronização do Windows Server com uma partilha de ficheiros do Azure. O agente de sincronização de ficheiros do Azure tem três componentes principais: 
- **FileSyncSvc.exe**: O serviço em segundo plano Windows que é responsável pelo monitorização das alterações nos pontos finais de servidor e para iniciar sessões de sincronização para o Azure.
- **StorageSync.sys**: O filtro de sistema de ficheiros do Azure File Sync, que é responsável por camadas de ficheiros para ficheiros do Azure (quando na cloud em camadas estiver ativada).
- **Cmdlets de gestão do PowerShell**: Cmdlets do PowerShell que utilizar para interagir com o fornecedor de recursos do Microsoft.StorageSync Azure. Pode encontrá-los nos seguintes locais (predefinição):
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.PowerShell.Cmdlets.dll
    - C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll

### <a name="server-endpoint"></a>Ponto final do servidor
Os pontos finais de servidor representam uma localização específica num servidor registado, como uma pasta num volume do servidor. Vários pontos de extremidade do servidor podem existir no mesmo volume se seus espaços de nomes não se sobrepõem (por exemplo, `F:\sync1` e `F:\sync2`). Pode configurar políticas de camadas de cloud individualmente para cada ponto de final do servidor. 

Pode criar um ponto final do servidor por meio de um ponto de montagem. Tenha em atenção que a mountpoints dentro do ponto final de servidor são ignorados.  

Pode criar um ponto final do servidor no volume do sistema, mas há duas limitações se fizer isso:
* Na cloud em camadas não podem ser ativada.
* Restauro rápido do espaço de nomes (em que o sistema rapidamente traz para baixo de todo o namespace e, em seguida, começa a se lembrar de conteúdo) não é efetuado.


> [!Note]  
> Apenas os volumes não amovíveis são suportados.  Unidades mapeadas a partir de uma partilha remota não são suportadas para um caminho de ponto final de servidor.  Além disso, um ponto final do servidor pode estar localizado no Windows volume de sistema através da cloud em camadas não é suportada no volume do sistema.

Se adicionar uma localização do servidor que tenha um conjunto existente de ficheiros como um ponto final do servidor para um grupo de sincronização, esses arquivos são mesclados com outros ficheiros que já estejam nesse outros pontos de extremidade no grupo de sincronização.

### <a name="cloud-endpoint"></a>Ponto final da cloud
Um ponto final da cloud é uma partilha de ficheiros do Azure que faz parte de um grupo de sincronização. As sincronizações de partilha de ficheiros do Azure completa e uma partilha de ficheiros do Azure podem ser um membro do ponto final de uma nuvem apenas. Por conseguinte, uma partilha de ficheiros do Azure pode ser um membro de apenas um grupo de sincronização. Se adicionar uma partilha de ficheiros do Azure que tenha um conjunto existente de ficheiros como um ponto final da cloud para um grupo de sincronização, os ficheiros existentes são mesclados com outros ficheiros que já estejam nesse outros pontos de extremidade no grupo de sincronização.

> [!Important]  
> O Azure File Sync suporta diretamente a efetuar alterações à partilha de ficheiros do Azure. No entanto, todas as alterações feitas na partilha de ficheiros do Azure primeiro tem de ser detetado por uma tarefa de deteção de alteração de sincronização de ficheiros do Azure. Uma tarefa de deteção de alteração de início de um ponto final da cloud apenas uma vez a cada 24 horas. Além disso, as alterações feitas para uma partilha de ficheiros do Azure através do protocolo REST não atualizará a hora da última modificação de SMB e não irão ser vistas como uma alteração por sincronização. Para obter mais informações, consulte [ficheiros do Azure, perguntas mais frequentes sobre](storage-files-faq.md#afs-change-detection).

### <a name="cloud-tiering"></a>Disposição em camadas na cloud 
Na cloud em camadas são uma funcionalidade opcional do Azure File Sync em que frequentemente ficheiros acedidos são colocadas em cache localmente no servidor, enquanto todos os outros ficheiros são dispostos em camadas para ficheiros do Azure com base nas definições de política. Para obter mais informações, consulte [camada de Cloud de compreensão](storage-sync-cloud-tiering.md).

## <a name="azure-file-sync-system-requirements-and-interoperability"></a>Requisitos de sistema de sincronização de ficheiros do Azure e interoperabilidade 
Esta secção abrange os requisitos de sistema do agente de sincronização de ficheiros do Azure e a interoperabilidade com recursos do Windows Server e funções e soluções de terceiros.

### <a name="evaluation-tool"></a>Ferramenta de avaliação
Antes de implementar o Azure File Sync, deve avaliar se é compatível com o seu sistema usando a ferramenta de avaliação do Azure File Sync. Essa ferramenta é um cmdlet do PowerShell do Azure que verifica a existência de potenciais problemas com o seu sistema de ficheiros e o conjunto de dados, tais como carateres não suportados ou uma versão de SO não suportada. Tenha em atenção que suas verificações abrangem mais, mas não todos os recursos mencionados abaixo; Recomendamos que leia o restante desta seção com cuidado para garantir que sua implementação transcorre normalmente. 

#### <a name="download-instructions"></a>Instruções para download
1. Certifique-se de que tem a versão mais recente do PackageManagement, e o PowerShellGet instalado (Isto permite-lhe instalar os módulos de pré-visualização)
    
    ```PowerShell
        Install-Module -Name PackageManagement -Repository PSGallery -Force
        Install-Module -Name PowerShellGet -Repository PSGallery -Force
    ```
 
2. Reinicie o PowerShell
3. Instalar os módulos
    
    ```PowerShell
        Install-Module -Name Az.StorageSync -AllowPrerelease -AllowClobber -Force
    ```

#### <a name="usage"></a>Utilização  
É possível invocar a ferramenta de avaliação de algumas formas diferentes: pode efetuar as verificações do sistema, as verificações de conjunto de dados ou ambos. Para realizar verificações do sistema e o conjunto de dados: 

```PowerShell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path>
```

Para testar apenas o conjunto de dados:
```PowerShell
    Invoke-AzStorageSyncCompatibilityCheck -Path <path> -SkipSystemChecks
```
 
Para testar apenas os requisitos de sistema:
```PowerShell
    Invoke-AzStorageSyncCompatibilityCheck -ComputerName <computer name>
```
 
Para exibir os resultados no CSV:
```PowerShell
    $errors = Invoke-AzStorageSyncCompatibilityCheck […]
    $errors | Select-Object -Property Type, Path, Level, Description | Export-Csv -Path <csv path>
```

### <a name="system-requirements"></a>Requisitos de Sistema
- Um servidor com o Windows Server 2012 R2, Windows Server 2016 ou Windows Server 2019:

    | Versão | SKUs suportados | Opções de implementação suportadas |
    |---------|----------------|------------------------------|
    | Windows Server 2019 | Datacenter e Standard | Completo (servidor com uma interface do Usuário) |
    | Windows Server 2016 | Datacenter e Standard | Completo (servidor com uma interface do Usuário) |
    | Windows Server 2012 R2 | Datacenter e Standard | Completo (servidor com uma interface do Usuário) |

    Versões futuras do Windows Server serão adicionadas à medida que são lançadas. Versões anteriores do Windows podem ser adicionadas com base nos comentários dos utilizadores.

    > [!Important]  
    > É recomendável manter todos os servidores que utiliza com o Azure File Sync atualizados com as atualizações mais recentes do Windows Update. 

- Um servidor com um mínimo de 2 GiB de memória.

    > [!Important]  
    > Se o servidor estiver em execução numa máquina virtual com memória dinâmica ativada, a VM deve ser configurada com um MiB 2048 mínima de memória.
    
- Um volume localmente anexado formatado com o sistema de ficheiros NTFS.

### <a name="file-system-features"></a>Recursos de sistema de arquivo
| Funcionalidade | Estado do suporte | Notas |
|---------|----------------|-------|
| Listas de controlo de acesso (ACLs) | Totalmente suportado | ACLs do Windows são mantidas pelo Azure File Sync e são impostas pelo Windows Server em pontos finais do servidor. ACLs do Windows não estão (ainda) suportada pelo serviço ficheiros do Azure, se os ficheiros são acedidos diretamente na cloud. |
| Ligações fixas | Ignorada | |
| Links simbólicos | Ignorada | |
| Pontos de montagem | Parcialmente suportada | Pontos de montagem podem ser a raiz do ponto final do servidor, mas eles são ignorados, se eles estão contidos no espaço de nomes de um servidor do ponto de extremidade. |
| Junções | Ignorada | Por exemplo, as DfrsrPrivate de sistema de ficheiros distribuído e DFSRoots pastas. |
| Pontos de reanálise | Ignorada | |
| Compressão NTFS | Totalmente suportado | |
| Ficheiros dispersos | Totalmente suportado | Sincronização de ficheiros dispersos (não estão bloqueadas), mas que a sincronização para a cloud como um ficheiro completo. Se o conteúdo do ficheiro alterar na cloud (ou noutro servidor), o ficheiro já não for esparsa, quando a alteração é transferida. |
| Fluxos de dados alternativos (ADS) | Preservados, mas não sincronizado | Por exemplo, as etiquetas de classificação criadas pela infraestrutura de classificação de ficheiros não estão sincronizadas. Etiquetas de classificação existentes nos ficheiros em cada um dos pontos finais do servidor são à esquerda inalteradas. |

> [!Note]  
> São suportados apenas os volumes NTFS. O reFS, FAT, FAT32 e outros sistemas de ficheiros não são suportados.

### <a name="files-skipped"></a>Ficheiros ignorados
| Ficheiro/pasta | Nota |
|-|-|
| Desktop.ini | Específica do sistema de ficheiros |
| ethumbs.db$ | Ficheiro temporário para miniaturas |
| ~$\*.\* | Ficheiro temporário do Office |
| \*.tmp | Ficheiro temporário |
| \*.laccdb | Ficheiro de bloqueio do acesso DB|
| 635D02A9D91C401B97884B82B3BCDAEA.* | Ficheiro de sincronização interno|
| \\Informações de Volume do sistema | Pasta específica para o volume |
| $RECYCLE.BIN| Pasta |
| \\SyncShareState | Pasta de sincronização |

### <a name="failover-clustering"></a>Clustering de ativação pós-falha
Clustering de ativação pós-falha do Windows Server é suportada pelo Azure File Sync para a opção de implementação de "Servidor de ficheiros para utilização geral". Clustering de ativação pós-falha não é suportado em "Servidor de ficheiros de escalamento horizontal para dados de aplicação" (SOFS) ou em Volumes Partilhados de cluster (CSVs).

> [!Note]  
> O agente do Azure File Sync tem de ser instalado em cada nó num Cluster de ativação pós-falha para a sincronização funcione corretamente.

### <a name="data-deduplication"></a>A eliminação de duplicados de dados
**Versão do agente 5.0.2.0**   
Eliminação de dados duplicados é suportada em volumes com camadas ativado no Windows Server 2016 e Windows Server 2019 da cloud. Ativar a eliminação de duplicados num volume com camadas ativado da cloud permite-lhe colocar em cache mais ficheiros no local sem aprovisionar mais armazenamento.

**Windows Server 2012 R2 ou versões mais antigas do agente**  
Para volumes que não têm a cloud em camadas ativado, o Azure File Sync suporta a ser ativada no volume do Windows eliminação de duplicados de dados de servidor.

### <a name="distributed-file-system-dfs"></a>Sistema de ficheiros distribuído (DFS)
O Azure File Sync suporta a interoperabilidade com espaços de nomes do DFS (DFS-N) e a replicação de DFS (DFS-R).

**Espaços de nomes do DFS (DFS-N)**: O Azure File Sync é totalmente suportado em servidores do DFS-N. Pode instalar o agente do Azure File Sync num ou mais membros do DFS-N para sincronizar dados entre os pontos de extremidade do servidor e o ponto final da cloud. Para obter mais informações, consulte [descrição geral de espaços de nomes DFS](https://docs.microsoft.com/windows-server/storage/dfs-namespaces/dfs-overview).
 
**A replicação de DFS (DFS-R)**: Como o DFS-R e o Azure File Sync são as duas soluções de replicação, na maioria dos casos, é recomendável substituir o DFS-R com o Azure File Sync. Existem vários cenários em que gostaria de utilizar o DFS-R e o Azure File Sync em conjunto:

- Estiver a migrar de uma implementação de DFS-R para uma implementação do Azure File Sync. Para obter mais informações, consulte [migrar uma implementação de replicação de DFS (DFS-R) para o Azure File Sync](storage-sync-files-deployment-guide.md#migrate-a-dfs-replication-dfs-r-deployment-to-azure-file-sync).
- Nem todos os servidor no local que necessita de uma cópia dos seus dados de ficheiro pode estar ligado diretamente à internet.
- Servidores de filiais consolidar os dados num servidor de hub individual, para que gostaria de utilizar o Azure File Sync.

Para o Azure File Sync e DFS-R trabalhar lado a lado:

1. Tem de camadas da cloud do Azure File Sync ser desativado em volumes com pastas replicada no DFS-R.
2. Pontos finais do servidor não devem ser configurados em pastas só de leitura de replicação de DFS-R.

Para obter mais informações, consulte [descrição geral da replicação de DFS](https://technet.microsoft.com/library/jj127250).

### <a name="sysprep"></a>Sysprep
Com o sysprep num servidor que tem instalado o agente de sincronização de ficheiros do Azure não é suportado e pode levar a resultados inesperados. Registo de servidor e a instalação do agente deve ocorrer depois de implementar a imagem do servidor e concluir a mini-configuração de sysprep.

### <a name="windows-search"></a>Windows Search
Se cloud disposição em camadas estiver ativada um ponto de final de servidor, os ficheiros que são dispostos em camadas ignorados e não indexados por Windows Search. Ficheiros em camadas não são indexados corretamente.

### <a name="antivirus-solutions"></a>Soluções antivírus
Como o antivírus funciona através da análise de ficheiros para o código malicioso conhecido, um produto antivírus pode causar a remoção de ficheiros em camadas. Nas versões 4.0 e acima do agente do Azure File Sync, ficheiros em camadas de ter o conjunto FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS de atributo seguro do Windows. Recomendamos que o serviço de consultoria com o seu fornecedor de software para saber como configurar a sua solução para ignorar a leitura de ficheiros com esse conjunto de atributo (muitos fazem-lo automaticamente).

Soluções da Microsoft internas antivírus, o Windows Defender e System Center Endpoint Protection (SCEP), o ambos ignorar automaticamente de ficheiros de leitura com esse atributo seja definido. Temos testado-los e identificou um problema menor: ao adicionar um servidor a um grupo de sincronização existente, arquivos de tamanho inferior de 800 bytes são recuperados (transferido) no novo servidor. Estes ficheiros permanecerão no novo servidor e não pode ser camados, uma vez que estas não cumprem o requisito de tamanho de camadas (> 64kb).

### <a name="backup-solutions"></a>Soluções de cópia de segurança
Como as soluções antivírus, soluções de cópia de segurança podem fazer com que a remoção de ficheiros em camadas. Recomendamos que utilize uma solução de cópia de segurança na cloud para criar cópias de segurança da partilha de ficheiros do Azure em vez de um produto de cópia de segurança no local.

Se estiver a utilizar uma solução de cópia de segurança no local, as cópias de segurança devem ser efetuadas num servidor no grupo de sincronização que tem na cloud em camadas desativado. Ao executar um restauro, utilize as opções de restauro de nível de volume ou ao nível do ficheiro. Ficheiros restaurados com a opção de restauro ao nível do ficheiro serão sincronizados para todos os pontos finais no grupo de sincronização e os arquivos existentes serão substituídos com a versão restaurada a partir de cópia de segurança.  Restauros ao nível do volume não irão substituir as versões mais recentes do ficheiro na partilha de ficheiros do Azure ou outros pontos de extremidade do servidor.

> [!Note]  
> Restauração bare-metal (BMR) pode causar resultados inesperados e não é atualmente suportada.

> [!Note]  
> Instantâneos do VSS (incluindo a guia Previous Versions) não são atualmente suportados em volumes que tem na cloud em camadas ativado. Se cloud em camadas estiver ativada, utilize os instantâneos de partilha de ficheiros do Azure para restaurar um ficheiro de cópia de segurança.

### <a name="encryption-solutions"></a>Soluções de encriptação
Suporte para soluções de encriptação depende de como eles são implementados. O Azure File Sync é conhecido por trabalhar com:

- Criptografia de disco BitLocker
- Azure Information Protection, o Azure Rights Management Services (Azure RMS) e o Active Directory RMS

O Azure File Sync é conhecido não para trabalhar com:

- NTFS encriptados sistema de ficheiros (EFS)

Em geral, Azure File Sync deve suportar a interoperabilidade com soluções de encriptação que estejam abaixo de sistema de arquivos, como o BitLocker e com soluções que são implementadas no formato de ficheiro, como o Azure Information Protection. Não foi efetuada nenhuma interoperabilidade especial para soluções que estejam acima do sistema de arquivos (como o EFS de NTFS).

### <a name="other-hierarchical-storage-management-hsm-solutions"></a>Outras soluções de gestão de armazenamento hierárquico (HSM)
Não existem outras soluções HSM devem ser utilizadas com o Azure File Sync.

## <a name="region-availability"></a>Disponibilidade de região
O Azure File Sync está disponível apenas nas seguintes regiões:

| Região | Localização do Datacenter |
|--------|---------------------|
| Leste da Austrália | Nova Gales do Sul |
| Sudeste da Austrália | Victoria |
| Sul do Brasil | São Paulo Estado |
| Canadá Central | Toronto |
| Leste do Canadá | Cidade do Quebeque |
| Índia Central | Pune |
| EUA Central | Iowa |
| Ásia Oriental | RAE de Hong Kong |
| EUA Leste | Virgínia |
| E.U.A. Leste 2 | Virgínia |
| Leste do Japão | Tóquio, Saitama |
| Oeste do Japão | Osaka |
| EUA Centro-Norte | Illinois |
| Europa do Norte | Irlanda |
| EUA Centro-Sul | Texas |
| Sul da Índia | Chennai |
| Sudeste Asiático | Singapura |
| Reino Unido Sul | Londres |
| Reino Unido Oeste | Cardiff |
| Europa Ocidental | Países Baixos |
| EUA Oeste | Califórnia |

O Azure File Sync suporta a sincronização apenas com uma partilha de ficheiros do Azure que está na mesma região que o serviço de sincronização de armazenamento.

### <a name="azure-disaster-recovery"></a>Recuperação após desastre do Azure
Para proteger contra a perda de uma região do Azure, Azure File Sync integra-se com o [redundância de armazenamento georredundante](../common/storage-redundancy-grs.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) opção (GRS). Armazenamento GRS funciona utilizando a replicação de bloco assíncrono entre o armazenamento na região primária, com a qual normalmente interage, e o armazenamento na região secundária associada. Em caso de desastre que faz com que uma região do Azure para go temporariamente offline ou permanentemente, a Microsoft irá armazenamento de ativação pós-falha para a região emparelhado. 

> [!Warning]  
> Se estiver a utilizar a partilha de ficheiros do Azure como um ponto final da cloud numa conta de armazenamento GRS, não deve iniciar a ativação pós-falha de conta de armazenamento. Se o fizer, sincronização causa para parar o trabalho e Maio também causará perda de dados inesperado no caso de ficheiros recentemente em camadas. No caso de perda de uma região do Azure, a Microsoft irá acionar a ativação pós-falha a conta de armazenamento de forma que seja compatível com o Azure File Sync.

Para suportar a integração de ativação pós-falha entre o armazenamento georredundante e o Azure File Sync, todas as regiões do Azure File Sync são associadas uma região secundária que corresponde à região secundária utilizada pelo armazenamento. Estes pares são os seguintes:

| Região primária      | Região emparelhada      |
|---------------------|--------------------|
| Leste da Austrália      | Sudeste da Austrália |
| Sudeste da Austrália | Leste da Austrália     |
| Canadá Central      | Leste do Canadá        |
| Leste do Canadá         | Canadá Central     |
| Índia Central       | Sul da Índia        |
| EUA Central          | EUA Leste 2          |
| Ásia Oriental           | Sudeste Asiático     |
| EUA Leste             | EUA Oeste            |
| EUA Leste 2           | EUA Central         |
| Europa do Norte        | Europa Ocidental        |
| EUA Centro-Norte    | EUA Centro-Sul   |
| Sul da Índia         | Índia Central      |
| Sudeste Asiático      | Ásia Oriental          |
| Reino Unido Sul            | Reino Unido Oeste            |
| Reino Unido Oeste             | Reino Unido Sul           |
| Europa Ocidental         | Europa do Norte       |
| EUA Oeste             | EUA Leste            |

## <a name="azure-file-sync-agent-update-policy"></a>Política de atualização do agente do Azure File Sync
[!INCLUDE [storage-sync-files-agent-update-policy](../../../includes/storage-sync-files-agent-update-policy.md)]

## <a name="next-steps"></a>Passos Seguintes
* [Considere as definições de proxy e firewall](storage-sync-files-firewall-and-proxy.md)
* [Planear uma implementação dos Ficheiros do Azure](storage-files-planning.md)
* [Implementar os ficheiros do Azure](storage-files-deployment-guide.md)
* [Implementar Azure File Sync](storage-sync-files-deployment-guide.md)
* [Monitorizar a sincronização de ficheiros do Azure](storage-sync-files-monitoring.md)
