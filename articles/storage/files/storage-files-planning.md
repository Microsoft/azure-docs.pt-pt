---
title: Planejando uma implantação de arquivos do Azure | Microsoft Docs
description: Saiba o que deve ser considerado ao planejar uma implantação de arquivos do Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: b77d6fe03a051c019519f195d55cdeb00fb9afb2
ms.sourcegitcommit: 67e9f4cc16f2cc6d8de99239b56cb87f3e9bff41
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/31/2020
ms.locfileid: "76906265"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planear uma implementação dos Ficheiros do Azure

[Os ficheiros do Azure](storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo SMB padrão do setor. Como os arquivos do Azure são totalmente gerenciados, implantá-los em cenários de produção é muito mais fácil do que implantar e gerenciar um servidor de arquivos ou um dispositivo NAS. Este artigo aborda os tópicos a serem considerados ao implantar um compartilhamento de arquivos do Azure para uso em produção em sua organização.

## <a name="management-concepts"></a>Conceitos de gerenciamento

 O diagrama a seguir ilustra as construções de gerenciamento de arquivos do Azure:

![Estrutura de Ficheiros](./media/storage-files-introduction/files-concepts.png)

* **Conta de Armazenamento**: todos os acessos ao Armazenamento do Azure são feitos através de uma conta de armazenamento. Consulte [escalabilidade e metas de desempenho para contas de armazenamento Standard](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter detalhes sobre a capacidade da conta de armazenamento.

* **Partilha:** uma partilha do Armazenamento de Ficheiros é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e diretórios têm de ser criados numa partilha principal. Uma conta pode conter um número ilimitado de compartilhamentos e um compartilhamento pode armazenar um número ilimitado de arquivos, até a capacidade total do compartilhamento de arquivos. A capacidade total para compartilhamentos de arquivos Premium e Standard é de 100 TiB.

* **Diretório**: uma hierarquia opcional de diretórios.

* **Ficheiro**: um ficheiro na partilha. Um arquivo pode ter até 1 TiB de tamanho.

* **Formato de URL**: para solicitações a um compartilhamento de arquivos do Azure feito com o protocolo REST de arquivo, os arquivos são endereçáveis usando o seguinte formato de URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Método de acesso a dados

Os arquivos do Azure oferecem dois métodos de acesso a dados internos e convenientes que você pode usar separadamente, ou em combinação entre si, para acessar seus dados:

1. **Acesso direto à nuvem**: qualquer compartilhamento de arquivos do Azure pode ser montado pelo [Windows](storage-how-to-use-files-windows.md), [MacOS](storage-how-to-use-files-mac.md)e/ou [Linux](storage-how-to-use-files-linux.md) com o protocolo SMB (Server Message Block) padrão do setor ou por meio da API REST do arquivo. Com o SMB, leituras e gravações em arquivos no compartilhamento são feitas diretamente no compartilhamento de arquivos no Azure. Para montar por uma VM no Azure, o cliente SMB no sistema operacional deve dar suporte a pelo menos SMB 2,1. Para montar localmente, como na estação de trabalho de um usuário, o cliente SMB com suporte da estação de trabalho deve dar suporte a pelo menos SMB 3,0 (com criptografia). Além do SMB, novos aplicativos ou serviços podem acessar diretamente o compartilhamento de arquivos por meio de REST de arquivo, que fornece uma interface de programação de aplicativo fácil e escalonável para desenvolvimento de software.
2. **Sincronização de arquivos do Azure**: com sincronização de arquivos do Azure, os compartilhamentos podem ser replicados para servidores Windows locais ou no Azure. Os usuários acessarão o compartilhamento de arquivos por meio do Windows Server, por exemplo, por meio de um compartilhamento SMB ou NFS. Isso é útil para cenários em que os dados serão acessados e modificados longe de um datacenter do Azure, como em um cenário de filial. Os dados podem ser replicados entre vários pontos de extremidade do Windows Server, como entre várias filiais. Por fim, os dados podem estar em camadas para os arquivos do Azure, de modo que todos os dados ainda sejam acessíveis por meio do servidor, mas o servidor não tem uma cópia completa dos dados. Em vez disso, os dados são rechamados diretamente quando abertos pelo usuário.

A tabela a seguir ilustra como os usuários e aplicativos podem acessar o compartilhamento de arquivos do Azure:

| | Acesso direto à nuvem | Azure File Sync |
|------------------------|------------|-----------------|
| Quais protocolos você precisa usar? | Os arquivos do Azure dão suporte a SMB 2,1, SMB 3,0 e API REST de arquivo. | Acesse o compartilhamento de arquivos do Azure por meio de qualquer protocolo com suporte no Windows Server (SMB, NFS, FTPS, etc.) |  
| Onde você está executando sua carga de trabalho? | **No Azure**: os arquivos do Azure oferecem acesso direto aos seus dados. | **No local com rede lenta**: os clientes Windows, Linux e MacOS podem montar um compartilhamento de arquivos local do Windows como um cache rápido do seu compartilhamento de arquivos do Azure. |
| De que nível de ACLs você precisa? | Nível de compartilhamento e arquivo. | Compartilhamento, arquivo e nível de usuário. |

## <a name="data-security"></a>Segurança dos dados

Os arquivos do Azure têm várias opções internas para garantir a segurança dos dados:

* Suporte para criptografia em ambos os protocolos sobretransmitidos: criptografia SMB 3,0 e REST de arquivo por HTTPS. Por padrão: 
    * Os clientes que dão suporte à criptografia SMB 3,0 enviam e recebem dados por um canal criptografado.
    * Os clientes que não dão suporte a SMB 3,0 com criptografia podem se comunicar dentro do datacenter sobre SMB 2,1 ou SMB 3,0 sem criptografia. Os clientes SMB não têm permissão para comunicar entre datacenters via SMB 2,1 ou SMB 3,0 sem criptografia.
    * Os clientes podem se comunicar sobre o REST de arquivo com HTTP ou HTTPS.
* Criptografia em repouso ([Azure criptografia do serviço de armazenamento](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): criptografia do serviço de armazenamento (SSE) está habilitada para todas as contas de armazenamento. Os dados em repouso são criptografados com chaves totalmente gerenciadas. A criptografia em repouso não aumenta os custos de armazenamento nem reduz o desempenho. 
* Requisito opcional de dados criptografados em trânsito: quando selecionado, os arquivos do Azure rejeitam o acesso aos dados por canais não criptografados. Especificamente, somente HTTPS e SMB 3,0 com conexões de criptografia são permitidos.

    > [!Important]  
    > Exigir a transferência segura de dados fará com que clientes SMB mais antigos não sejam capazes de se comunicar com o SMB 3,0 com criptografia para falhar. Para obter mais informações, consulte [montar no Windows](storage-how-to-use-files-windows.md), [montar no Linux](storage-how-to-use-files-linux.md)e [montar no MacOS](storage-how-to-use-files-mac.md).

Para obter segurança máxima, é altamente recomendável sempre habilitar a criptografia em repouso e habilitar a criptografia de dados em trânsito sempre que você estiver usando clientes modernos para acessar seus dados. Por exemplo, se você precisar montar um compartilhamento em uma VM do Windows Server 2008 R2, que dá suporte apenas ao SMB 2,1, será necessário permitir o tráfego não criptografado para sua conta de armazenamento, pois o SMB 2,1 não oferece suporte à criptografia.

Se você estiver usando Sincronização de Arquivos do Azure para acessar o compartilhamento de arquivos do Azure, sempre usaremos HTTPS e SMB 3,0 com criptografia para sincronizar seus dados com os servidores do Windows, independentemente de você precisar de criptografia de dados em repouso.

## <a name="file-share-performance-tiers"></a>Níveis de desempenho de compartilhamento de arquivos

Os arquivos do Azure oferecem dois níveis de desempenho: Standard e Premium.

### <a name="standard-file-shares"></a>Compartilhamentos de arquivos padrão

Os compartilhamentos de arquivos padrão são apoiados por HDDs (unidades de disco rígido). Os compartilhamentos de arquivos padrão fornecem um desempenho confiável para cargas de trabalho de e/s que são menos sensíveis à variabilidade de desempenho, como compartilhamentos de arquivos de uso geral e ambientes de desenvolvimento/teste. Os compartilhamentos de arquivos padrão só estão disponíveis em um modelo de cobrança pago conforme o uso.

> [!IMPORTANT]
> Se você quiser usar compartilhamentos de arquivos maiores que 5 TiB, consulte a seção [integração a compartilhamentos de arquivos maiores (camada Standard)](#onboard-to-larger-file-shares-standard-tier) para obter as etapas para carregar, bem como a disponibilidade e as restrições regionais.

### <a name="premium-file-shares"></a>Compartilhamentos de arquivos Premium

Os compartilhamentos de arquivos Premium são apoiados por SSDs (unidades de estado sólido). Os compartilhamentos de arquivos Premium fornecem alto desempenho e baixa latência consistentes em milissegundos de dígito único para a maioria das operações de e/s, para cargas de trabalho com uso intensivo de e/s. Isso os torna adequados para uma ampla variedade de cargas de trabalho, como bancos de dados, Hospedagem de site e ambientes de desenvolvimento. Os compartilhamentos de arquivos premium estão disponíveis apenas em um modelo de cobrança provisionado. Os compartilhamentos de arquivos Premium usam um modelo de implantação separado dos compartilhamentos de arquivos padrão.

O backup do Azure está disponível para compartilhamentos de arquivos Premium e o serviço kubernetes do Azure dá suporte a compartilhamentos de arquivos Premium na versão 1,13 e superior.

Se você quiser saber como criar um compartilhamento de arquivos premium, consulte nosso artigo sobre o assunto: [como criar uma conta de armazenamento de arquivos premium do Azure](storage-how-to-create-premium-fileshare.md).

No momento, não é possível converter diretamente entre um compartilhamento de arquivos padrão e um compartilhamento de arquivos premium. Se desejar alternar para qualquer camada, você deverá criar um novo compartilhamento de arquivos nessa camada e copiar manualmente os dados do compartilhamento original para o novo compartilhamento que você criou. Você pode fazer isso usando qualquer uma das ferramentas de cópia com suporte do Azure files, como Robocopy ou AzCopy.

> [!IMPORTANT]
> Os compartilhamentos de arquivos premium estão disponíveis com o LRS na maioria das regiões que oferecem contas de armazenamento e com o ZRS em um subconjunto menor de regiões. Para descobrir se os compartilhamentos de arquivos premium estão disponíveis atualmente em sua região, confira a página [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para o Azure. Para descobrir quais regiões dão suporte a ZRS, consulte [cobertura de suporte e disponibilidade regional](../common/storage-redundancy-zrs.md#support-coverage-and-regional-availability).
>
> Para nos ajudar a priorizar novas regiões e recursos da camada Premium, preencha esta [pesquisa](https://aka.ms/pfsfeedback).

#### <a name="provisioned-shares"></a>Compartilhamentos provisionados

Os compartilhamentos de arquivos Premium são provisionados com base em uma taxa fixa de GiB/IOPS/taxa de transferência. Para cada GiB provisionado, o compartilhamento emitirá uma taxa de transferência de IOPS e 0,1 MiB/s até os limites máximos por compartilhamento. O provisionamento mínimo permitido é de 100 GiB com IOPS/taxa de transferência mín.

Em uma base de melhor esforço, todos os compartilhamentos podem disparar até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo do tamanho do compartilhamento. Novos compartilhamentos começam com o crédito intermitente completo com base na capacidade provisionada.

Os compartilhamentos devem ser provisionados em incrementos de 1 GiB. O tamanho mínimo é 100 GiB, o próximo tamanho é 101 GiB e assim por diante.

> [!TIP]
> IOPS de linha de base = 1 * GiB provisionado. (Até um máximo de 100.000 IOPS).
>
> Limite de intermitência = 3 * IOPS de linha de base. (Até um máximo de 100.000 IOPS).
>
> taxa de egresso = 60 MiB/s + 0, 6 * provisionamento GiB
>
> taxa de entrada = 40 MiB/s + 0, 4 * provisionamento GiB

O tamanho do compartilhamento provisionado é especificado por cota de compartilhamento. A cota de compartilhamento pode ser aumentada a qualquer momento, mas só pode ser reduzida após 24 horas desde o último aumento. Depois de aguardar 24 horas sem um aumento de cota, você pode diminuir a cota de compartilhamento quantas vezes desejar, até aumentá-la novamente. As alterações de escala de taxa de transferência/IOPS entrarão em vigor em alguns minutos após a alteração do tamanho.

É possível diminuir o tamanho do compartilhamento provisionado abaixo do GiB usado. Se você fizer isso, não perderá dados, mas ainda será cobrado pelo tamanho usado e receberá o desempenho (IOPS de linha de base, taxa de transferência e IOPS de intermitência) do compartilhamento provisionado, não o tamanho usado.

A tabela a seguir ilustra alguns exemplos dessas fórmulas para os tamanhos de compartilhamento provisionados:

|Capacidade (GiB) | IOPS de linha de base | IOPS de intermitência | Saída (MiB/s) | Entrada (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1.500   | 90   | 60   |
|1,024       | 1,024   | Até 3.072   | 122   | 81   |
|5\.120       | 5\.120   | Até 15.360  | 368   | 245   |
|10.240      | 10.240  | Até 30.720  | 675 | 450   |
|33.792      | 33.792  | Até 100.000 | 2\.088 | 1\.392   |
|51.200      | 51.200  | Até 100.000 | 3\.132 | 2\.088   |
|102.400     | 100.000 | Até 100.000 | 6\.204 | 4\.136   |

> [!NOTE]
> O desempenho dos compartilhamentos de arquivos está sujeito aos limites de rede da máquina, largura de banda de rede disponível, tamanhos de e/s, paralelismo, entre muitos outros fatores. Por exemplo, com base em testes internos com 8 tamanhos de IO de leitura/escrita KiB, uma única máquina virtual do Windows, *Standard F16s_v2*, ligada à partilha de ficheiros premium sobre SMB poderia atingir IOPS de leitura de 20K e 15K escrever IOPS. Com tamanhos de e/s de leitura/gravação de MiB 512, a mesma VM pode atingir a saída de 1,1 GiB/s e a taxa de transferência de entrada de 370 MiB/s. Para obter a escala de desempenho máxima, distribua a carga entre várias VMs. Consulte o [Guia de solução de problemas](storage-troubleshooting-files-performance.md) para alguns problemas comuns de desempenho e soluções alternativas.

#### <a name="bursting"></a>Bursting

Os compartilhamentos de arquivos Premium podem aumentar seu IOPS até um fator de três. A intermitência é automatizada e opera com base em um sistema de crédito. A intermitência funciona em uma base de melhor esforço e o limite de intermitência não é uma garantia, os compartilhamentos de arquivos podem *aumentar até* o limite.

Os créditos se acumulam em um Bucket de intermitência sempre que o tráfego para o compartilhamento de arquivos está abaixo do IOPS de linha de base. Por exemplo, um compartilhamento de GiB 100 tem 100 IOPS de linha de base. Se o tráfego real no compartilhamento era de 40 IOPS para um intervalo específico de 1 segundo, o IOPS de 60 não utilizado será creditado em um Bucket de intermitência. Esses créditos serão então usados mais tarde, quando as operações excederem o IOPs de linha de base.

> [!TIP]
> Tamanho do Bucket de intermitência = IOPS de linha de base * 2 * 3600.

Sempre que um compartilhamento exceder o IOPS de linha de base e tiver créditos em um Bucket de intermitência, ele será rompido. Os compartilhamentos podem continuar a aumentar, contanto que os créditos permaneçam, embora os compartilhamentos menores que 50 TiB permanecerão apenas no limite de intermitência de até uma hora. Compartilhamentos maiores que 50 TiB podem, tecnicamente, exceder esse limite de hora, até duas horas, mas isso se baseia no número de créditos de intermitência acumulados. Cada e/s além do IOPS de linha de base consome um crédito e quando todos os créditos são consumidos, o compartilhamento retornaria ao IOPS de linha de base

Os créditos de compartilhamento têm três Estados:

- A acumulação, quando o compartilhamento de arquivos está usando menos do que o IOPS de linha de base.
- Recusando, quando o compartilhamento de arquivos estiver intermitente.
- Constante restante, quando não há créditos ou IOPS de linha de base em uso.

Novos compartilhamentos de arquivos começam com o número total de créditos em seu Bucket de intermitência. Os créditos de intermitência não serão acumulados se o IOPS de compartilhamento cair abaixo do IOPS de linha de base devido à limitação pelo servidor.

## <a name="file-share-redundancy"></a>Redundância de compartilhamento de arquivos

Compartilhamentos padrão do Azure files oferece suporte a quatro opções de redundância de dados: LRS (armazenamento com redundância local), ZRS (armazenamento com redundância de zona), GRS (armazenamento com redundância geográfica) e GZRS (armazenamento com redundância de zona geográfica) (versão prévia).

Os compartilhamentos Premium dos arquivos do Azure dão suporte a LRS e ZRS, o ZRS está disponível atualmente em um subconjunto menor de regiões.

As seções a seguir descrevem as diferenças entre as diferentes opções de redundância:

### <a name="locally-redundant-storage"></a>Armazenamento localmente redundante

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Armazenamento georredundante

> [!Warning]  
> Se você estiver usando o compartilhamento de arquivos do Azure como um ponto de extremidade de nuvem em uma conta de armazenamento GRS, não deverá iniciar o failover da conta de armazenamento. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão. No caso de perda de uma região do Azure, a Microsoft disparará o failover da conta de armazenamento de forma que seja compatível com Sincronização de Arquivos do Azure.

O armazenamento geo-redundante (GRS) foi concebido para fornecer pelo menos 99.99999999999% (16 9's) durabilidade de objetos ao longo de um determinado ano, replicando os seus dados para uma região secundária que está a centenas de milhas da região primária. Se sua conta de armazenamento tiver o GRS habilitado, seus dados serão duráveis mesmo no caso de uma interrupção regional completa ou um desastre no qual a região primária não seja recuperável.

Se você optar pelo armazenamento com redundância geográfica com acesso de leitura (RA-GRS), deverá saber que o arquivo do Azure não oferece suporte ao armazenamento com redundância geográfica com acesso de leitura (RA-GRS) em qualquer região no momento. Os compartilhamentos de arquivos na conta de armazenamento RA-GRS funcionam como no GRS contas e são cobrados preços GRS.

O GRS Replica seus dados para outro data center em uma região secundária, mas esses dados estarão disponíveis para serem lidos somente se a Microsoft iniciar um failover da região primária para a secundária.

Para uma conta de armazenamento com GRS habilitado, todos os dados são replicados primeiro com LRS (armazenamento com redundância local). Uma atualização é confirmada primeiro no local principal e replicada usando LRS. Em seguida, a atualização é replicada assincronamente para a região secundária usando GRS. Quando os dados são gravados no local secundário, eles também são replicados nesse local usando LRS.

As regiões primária e secundária gerenciam réplicas em domínios de falha e domínios de atualização separados em uma unidade de escala de armazenamento. A unidade de escala de armazenamento é a unidade de replicação básica no datacenter. A replicação nesse nível é fornecida por LRS; para obter mais informações, consulte [LRS (armazenamento com redundância local): redundância de dados de baixo custo para o armazenamento do Azure](../common/storage-redundancy-lrs.md).

Tenha esses pontos em mente ao decidir qual opção de replicação usar:

* O armazenamento com redundância de zona geográfica (GZRS) (visualização) fornece alta disponibilidade junto com a durabilidade máxima, replicando os dados de forma síncrona em três zonas de disponibilidade do Azure e, em seguida, replicando os dados de maneira assíncrona para a região secundária. Você também pode habilitar o acesso de leitura para a região secundária. A GZRS foi concebida para fornecer pelo menos 99.9999999999999999999999999% (16 9's) durabilidade de objetos durante um determinado ano. Para obter mais informações sobre o GZRS, consulte [armazenamento com redundância de zona geográfica para alta disponibilidade e durabilidade máxima (versão prévia)](../common/storage-redundancy-gzrs.md).
* O ZRS (armazenamento com redundância de zona) fornece alta disponibilidade com replicação síncrona e pode ser uma opção melhor para alguns cenários do que GRS. Para obter mais informações sobre o ZRS, consulte [ZRS](../common/storage-redundancy-zrs.md).
* A replicação assíncrona envolve um atraso desde o momento em que os dados são gravados na região primária, até quando eles são replicados para a região secundária. No caso de um desastre regional, as alterações que ainda não foram replicadas para a região secundária poderão ser perdidas se esses dados não puderem ser recuperados da região primária.
* Com o GRS, a réplica não está disponível para acesso de leitura ou gravação, a menos que a Microsoft inicie um failover para a região secundária. No caso de um failover, você terá acesso de leitura e gravação a esses dados após a conclusão do failover. Para obter mais informações, consulte [diretrizes de recuperação de desastre](../common/storage-disaster-recovery-guidance.md).

## <a name="onboard-to-larger-file-shares-standard-tier"></a>Integração com compartilhamentos de arquivos maiores (camada Standard)

Esta seção se aplica somente aos compartilhamentos de arquivos padrão. Todos os compartilhamentos de arquivos premium estão disponíveis com capacidade de 100 TiB.

### <a name="restrictions"></a>Restrições

- A conversão de conta de LRS/ZRS para GRS/GZRS não será possível para nenhuma conta de armazenamento com grandes compartilhamentos de arquivos habilitados.

### <a name="regional-availability"></a>Disponibilidade regional

As ações de ficheiros standard com limite de capacidade de 100 TiB estão disponíveis globalmente em todas as regiões do Azure -

- LRS: Todas as regiões, exceto a África do Sul Norte e África do Sul Oeste.
   - As nuvens nacionais (Governo, Alemanha, China) são suportadas através da PowerShell e da Azure Command Line Interface (CLI). Sem suporte do portal. 
   - Leste dos EUA, LESTE DOS EUA, Europa Ocidental: Todas as novas contas são apoiadas. Um pequeno número de contas existentes não concluiu o processo de atualização. Você pode verificar se suas contas de armazenamento existentes concluíram o processo de atualização ao tentar [habilitar grandes compartilhamentos de arquivos](storage-files-how-to-create-large-file-share.md).

- ZRS: Todas as regiões, exceto japão Leste, Norte da Europa, África do Sul Norte.
- GRS/GZRS: Não suportado.

### <a name="enable-and-create-larger-file-shares"></a>Habilitar e criar compartilhamentos de arquivos maiores

Para começar a usar compartilhamentos de arquivos maiores, consulte nosso artigo [como habilitar e criar grandes compartilhamentos de arquivos](storage-files-how-to-create-large-file-share.md).

## <a name="data-growth-pattern"></a>Padrão de crescimento de dados

Hoje, o tamanho máximo de um compartilhamento de arquivos do Azure é de 100 TiB. Devido a essa limitação atual, você deve considerar o crescimento de dados esperado ao implantar um compartilhamento de arquivos do Azure.

É possível sincronizar vários compartilhamentos de arquivos do Azure com um único servidor de arquivos do Windows com o Sincronização de Arquivos do Azure. Isso permite que você garanta que os compartilhamentos de arquivos grandes e antigos que você pode ter no local possam ser colocados em Sincronização de Arquivos do Azure. Para obter mais informações, consulte [planejando uma implantação de sincronização de arquivos do Azure](storage-files-planning.md).

## <a name="data-transfer-method"></a>Método de transferência de dados

Há muitas opções fáceis para transferir dados em massa de um compartilhamento de arquivos existente, como um compartilhamento de arquivos local, em arquivos do Azure. Alguns daqueles populares incluem (lista não exaustiva):

* **[Sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)** : como parte de uma primeira sincronização entre um compartilhamento de arquivos do Azure (um "ponto de extremidade de nuvem") e um namespace de diretório do Windows (um "ponto de extremidade do servidor"), sincronização de arquivos do Azure replicará todos os dados do compartilhamento de arquivos existente para os arquivos do Azure.
* **[Importação/exportação do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : o serviço de importação/exportação do Azure permite que você transfira com segurança grandes quantidades de dados para um compartilhamento de arquivos do Azure enviando unidades de disco rígido para um datacenter do Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy é uma ferramenta de cópia bem conhecida que é fornecida com o Windows e o Windows Server. O Robocopy pode ser usado para transferir dados para arquivos do Azure, montando o compartilhamento de arquivos localmente e, em seguida, usando o local montado como o destino no comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : o AzCopy é um utilitário de linha de comando projetado para copiar dados de e para arquivos do Azure, bem como o armazenamento de BLOBs do Azure, usando comandos simples com desempenho ideal.

## <a name="next-steps"></a>Passos seguintes
* [Planejando uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
* [Implantando arquivos do Azure](storage-files-deployment-guide.md)
* [Implantando Sincronização de Arquivos do Azure](storage-sync-files-deployment-guide.md)
