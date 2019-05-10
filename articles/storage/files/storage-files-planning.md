---
title: Planear uma implementação de ficheiros do Azure | Documentos da Microsoft
description: Saiba o que considerar quando planear uma implementação de ficheiros do Azure.
services: storage
author: roygara
ms.service: storage
ms.topic: article
ms.date: 04/25/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 82c0aa53fa8905e0e58cb784a478ade474ec5601
ms.sourcegitcommit: 2ce4f275bc45ef1fb061932634ac0cf04183f181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/07/2019
ms.locfileid: "65232773"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planear uma implementação dos Ficheiros do Azure

[Os ficheiros do Azure](storage-files-introduction.md) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo SMB padrão do setor. Porque os ficheiros do Azure totalmente gerido, implantá-lo em cenários de produção é muito mais fácil do que implementar e gerir um servidor de ficheiros ou um dispositivo. Este artigo aborda os tópicos a ter em consideração quando implementar uma partilha de ficheiros do Azure para utilização em produção na sua organização.

## <a name="management-concepts"></a>Conceitos de gestão

 O diagrama seguinte ilustra as construções de gestão de ficheiros do Azure:

![Estrutura de Ficheiros](./media/storage-files-introduction/files-concepts.png)

* **Conta de armazenamento**: Todos os acessos ao armazenamento do Azure é feito através de uma conta de armazenamento. Veja [Metas de Escalabilidade e Desempenho](../common/storage-scalability-targets.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) para obter detalhes sobre a capacidade das contas de armazenamento.

* **Partilha**: Uma partilha de armazenamento de ficheiros é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e diretórios têm de ser criados numa partilha principal. Uma conta pode conter um número ilimitado de partilhas e uma partilha pode armazenar um número ilimitado de ficheiros, até a capacidade de total de 5 TiB de partilha de ficheiros.

* **Diretório**: Uma hierarquia opcional de diretórios.

* **Ficheiro**: Um ficheiro na partilha. Um ficheiro pode ter até 1 TiB de tamanho.

* **Formato de URL**: Para os pedidos para uma partilha de ficheiros do Azure feitas com o protocolo de REST de ficheiros, os ficheiros são endereçáveis através do formato de URL seguinte:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Método de acesso de dados

Ficheiros do Azure oferecem duas, incorporados e convenientes de acesso a dados métodos que pode utilizar em separado, ou em combinação entre si, para aceder aos seus dados:

1. **Cloud acesso direto**: Qualquer partilha de ficheiros do Azure pode ser montada por [Windows](storage-how-to-use-files-windows.md), [macOS](storage-how-to-use-files-mac.md), e/ou [Linux](storage-how-to-use-files-linux.md) com o protocolo de bloco de mensagem de servidor (SMB) padrão do setor ou através da API de REST de ficheiros. Com o SMB, leituras e escritas de ficheiros na partilha são feitas diretamente na partilha de ficheiros no Azure. Para montar por uma VM no Azure, o cliente SMB no SO tem de suportar, pelo menos, SMB 2.1. Montar no local, como na estação de trabalho de um utilizador, o cliente do SMB suportado pela estação de trabalho tem de suportar, pelo menos, SMB 3.0 (com criptografia). Além de SMB, novos aplicativos ou serviços podem aceder diretamente a partilha de ficheiros através de REST de ficheiros, que fornece uma interface de programação de aplicativo de fácil e escalonável para o desenvolvimento de software.
2. **Azure File Sync**: Com o Azure File Sync, as partilhas podem ser replicadas para servidores do Windows no local ou no Azure. Os utilizadores seriam aceder a partilha de ficheiros através do Windows Server, tal como através de uma partilha SMB ou NFS. Isto é útil para cenários em que dados serão acedidos e modificados distantes de um datacenter do Azure, tal como num cenário de filiais. Dados podem ser replicados entre vários pontos de extremidade do Windows Server, tal como entre várias filiais. Por fim, dados podem ser colocado em camadas para ficheiros do Azure, que todos os dados são continua acessível através do servidor, mas o servidor não tem uma cópia completa dos dados. Em vez disso, dados de forma totalmente integrada são recuperados quando aberto pelo seu utilizador.

A tabela a seguir ilustra como os utilizadores e as aplicações podem aceder a partilha de ficheiros do Azure:

| | Acesso direto à cloud | Azure File Sync |
|------------------------|------------|-----------------|
| Quais protocolos precisar de utilizar? | Os ficheiros do Azure suporta o SMB 2.1 e API REST de ficheiros SMB 3.0. | Aceder à sua partilha de ficheiros do Azure através de qualquer protocolo suportado no Windows Server (SMB, NFS, FTPS, etc.) |  
| Onde está a executar a sua carga de trabalho? | **No Azure**: Os ficheiros do Azure oferece acesso direto aos seus dados. | **No local com rede lenta**: Clientes Windows, Linux e macOS podem montar uma partilha de ficheiros do Windows no local como um cache rápido da sua partilha de ficheiros do Azure. |
| O nível de ACLs precisa? | Nível de partilha e o ficheiro. | Nível de partilha de ficheiros e utilizador. |

## <a name="data-security"></a>Segurança de dados

Os ficheiros do Azure tem várias opções incorporadas para garantir a segurança de dados:

* Suporte para a encriptação em ambos os protocolos de over-the-wire: Encriptação SMB 3.0 e REST de ficheiros através de HTTPS. Por predefinição: 
    * Os clientes que suportam encriptação SMB 3.0 enviar e recebem dados através de um canal criptografado.
    * Clientes que não suportem SMB 3.0 com a encriptação podem comunicar intra-datacenter através de SMB 2.1 ou SMB 3.0 sem encriptação. Os clientes do SMB não são permitidos para comunicar o Centro de dados inter através de SMB 2.1 ou SMB 3.0 sem encriptação.
    * Os clientes podem comunicar através do REST de ficheiros com HTTP ou HTTPS.
* Encriptação em repouso ([encriptação do serviço de armazenamento do Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Encriptação de serviço de armazenamento (SSE) está ativado para todas as contas de armazenamento. Dados Inativos são encriptados com chaves geridas pelo totalmente. Encriptação em repouso não aumentar os custos de armazenamento ou reduzir o desempenho. 
* Requisito opcional de dados encriptados em trânsito: quando selecionada, ficheiros do Azure rejeita o acesso os dados através de canais não encriptadas. Especificamente, são permitidas apenas HTTPS e SMB 3.0 com ligações de encriptação.

    > [!Important]  
    > Exigir que a transferência segura de dados fará com que os clientes SMB antigos não capazes de comunicar com o SMB 3.0 com a encriptação efetuar a ativação. Para obter mais informações, consulte [montar no Windows](storage-how-to-use-files-windows.md), [montar no Linux](storage-how-to-use-files-linux.md), e [montar em macOS](storage-how-to-use-files-mac.md).

Para segurança máxima, é altamente recomendável sempre ativar ambas as encriptação inativa e ativar a encriptação de dados em trânsito, sempre que estiver a utilizar clientes modernos para aceder aos seus dados. Por exemplo, se precisa montar uma partilha numa VM Windows Server 2008 R2, que só suporta o SMB 2.1, terá de permitir o tráfego não criptografado para a sua conta de armazenamento, uma vez que o SMB 2.1 não suporta a encriptação.

Se estiver a utilizar o Azure File Sync para aceder a partilha de ficheiros do Azure, sempre Utilizamos HTTPS e SMB 3.0 com a encriptação para sincronizar os dados para os seus servidores do Windows, independentemente de se necessita de encriptação de dados em repouso.

## <a name="file-share-performance-tiers"></a>Escalões de desempenho de partilha de ficheiros

Os ficheiros do Azure oferece dois escalões de desempenho: standard e premium.

* **Partilhas de ficheiros padrão** são apoiados por rotação unidades de disco de rígido (HDDs) que apresentam um desempenho fiável para cargas de trabalho de e/s que são menos sensíveis à variabilidade de desempenho, como partilhas de ficheiros para fins gerais e ambientes de desenvolvimento/teste. Partilhas de ficheiros padrão só estão disponíveis num modelo de faturação pay as you go.
* **As partilhas de ficheiros (pré-visualização) Premium** são apoiados por discos Estado sólidos (SSDs) que fornecem consistente de alto desempenho e baixa latência, em milissegundos de dígito na maioria das operações de e/s, para mais cargas de trabalho de e/s intensiva. Isso as torna adequadas para uma grande variedade de cargas de trabalho, como bases de dados, alojamento de web sites, ambientes de desenvolvimento, etc. Partilhas de ficheiros de Premium só estão disponíveis num modelo de faturação aprovisionado. Partilhas de ficheiros de Premium utilizam um modelo de implantação separado de partilhas de ficheiros padrão.

Cópia de segurança do Azure está disponível para partilhas de ficheiros de premium e o serviço Kubernetes do Azure suporta partilhas de ficheiros de premium na versão 1.13 e acima.

Se gostaria de saber como criar uma partilha de ficheiros de premium, consulte nosso artigo sobre o assunto: [Como criar uma conta de armazenamento de ficheiros do Azure premium](storage-how-to-create-premium-fileshare.md).

Atualmente, não é possível converter diretamente entre uma partilha de ficheiros e uma partilha de ficheiros de premium. Se gostaria de mudar para o escalão, tem de criar uma nova partilha de ficheiros nesse escalão e copiar os dados manualmente a partilha original de para a nova partilha que criou. Pode fazê-lo a utilizar as ferramentas de cópia de ficheiros do Azure suportados, por exemplo, o AzCopy.

> [!IMPORTANT]
> Partilhas de ficheiros de Premium ainda estão em pré-visualização, só estão disponíveis com o LRS e estão disponíveis na maioria das regiões que oferecem as contas de armazenamento. Para saber se as partilhas de ficheiros do premium estão atualmente disponíveis na sua região, consulte a [produtos disponíveis por região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) página para o Azure.

### <a name="provisioned-shares"></a>Partilhas aprovisionadas

Partilhas de ficheiros de Premium (pré-visualização) são aprovisionadas com base numa proporção de GiB/IOPS/débito fixa. Para cada GiB aprovisionado, a partilha será emitida um IOPS e o débito de MiB/s de 0,1 até aos limites máximos por partilha. O mínimo de aprovisionamento são 100 GiB com o mínimo de IOPS/débito.

Na base de melhor esforço, todas as partilhas podem ultrapassar os limites até três IOPS por GiB de armazenamento aprovisionado durante 60 minutos ou mais consoante o tamanho da partilha. Novas partilhas de começam com o crédito de rajada completo com base na capacidade aprovisionada.

Partilhas de tem de ser aprovisionadas em incrementos de 1 GiB. Tamanho mínimo é de 100 GiB, o próximo tamanho é 101 GiB e assim por diante.

> [!TIP]
> Linha de base de IOPS = 1 * aprovisionado GiB. (Até um máximo de 100 000 IOPS).
>
> Limite de rajada = 3 * IOPS da linha de base. (Até um máximo de 100 000 IOPS).
>
> taxa de saída = 60 MiB/s + 0.06 * aprovisionado GiB
>
> taxa de entrada = 40 MiB/s + 0.04 * aprovisionado GiB

Tamanho da partilha pode ser aumentado em qualquer altura, mas pode ser reduzido apenas após 24 horas desde o último aumento. Após uma espera de 24 horas, sem um aumento de tamanho, pode diminuir o tamanho da partilha de quantas vezes desejar, até que aumentá-la novamente. Alterações de dimensionamento IOPS/débito entrarão em vigor dentro de alguns minutos após a alteração de tamanho.

É possível diminuir o tamanho da partilha aprovisionado do abaixo sua GiB utilizado. Se fizer isso, não perderá dados mas, ainda será cobrado para o tamanho utilizado e receber o desempenho (linha de base de IOPS, débito e IOPS de rajada) da partilha aprovisionado, não o tamanho utilizado.

A tabela seguinte ilustra alguns exemplos destes viramos para os tamanhos de partilha aprovisionado:

|Capacidade (GiB) | Linha de base de IOPS | Períodos de rajada de IOPS | Saída (MiB/s) | Entrada (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1500   | 90   | 60   |
|1,024       | 1,024   | Até 3,072   | 122   | 81   |
|5,120       | 5,120   | Até 15.360  | 368   | 245   |
|10,240      | 10,240  | Até 30,720  | 675 | 450   |
|33,792      | 33,792  | Até 100 000 | 2,088 | 1,392   |
|51,200      | 51,200  | Até 100 000 | 3,132 | 2,088   |
|102,400     | 100,000 | Até 100 000 | 6,204 | 4,136   |

### <a name="bursting"></a>Extrapolação

Partilhas de ficheiros de Premium podem ultrapassar os limites seu IOPS até um fator de três. Extrapolação é automatizado e funciona com base num sistema de crédito. Funciona numa base de melhor esforço e o limite de rajada de segurança não é uma garantia, podem períodos de rajada de partilhas de ficheiros *até* o limite.

Créditos acumular-se num bucket de rajada sempre que o tráfego para a partilha de ficheiros está abaixo da linha de base IOPS. Por exemplo, uma partilha de GiB 100 tem a linha de base de 100 IOPS. Se o tráfego real na partilha foi 40 IOPS para um intervalo de 1 segundo específico, o IOPS de não utilizado 60 é creditado um bucket de rajada. Estes créditos, em seguida, serão utilizados mais tarde quando operações iria exceder a linha de base IOPs.

> [!TIP]
> Tamanho do registo de rajada = IOPS de linha de base * 2 * 3600.

Sempre que uma partilha excede a linha de base IOPS e tem créditos num bucket de rajada, irá expandir. Partilhas podem continuar a expandir, desde que os créditos são restantes, embora partilhas menores do que 50 TiB apenas permanecerá atingiu o limite de rajada para até uma hora. Partilhas de maiores do que 50 TiB tecnicamente podem exceder este limite de uma hora, se a duas horas, mas isso é com base no número de créditos de rajada acumulados. Cada e/s para além de linha de base de IOPS consome uma unidade de crédito e assim que todos os créditos são consumidos a partilha irá devolver a linha de base IOPS.

Os créditos de partilha têm três Estados:

- A acumular, quando a partilha de ficheiros está a utilizar menos do que a linha de base IOPS.
- Recusar, quando a partilha de ficheiros é bursting.
- IOPS restantes constante, quando existem sem créditos ou de uma linha de base estão em utilização.

Novo início de partilhas de ficheiros com o número total de créditos no respetivo registo de rajada. Os créditos de rajada não irão ser acumulados se a partilha de IOPS são inferiores a linha de base de IOPS devido à limitação pelo servidor.

## <a name="file-share-redundancy"></a>Redundância de partilha de ficheiros

Partilhas de padrão de ficheiros do Azure suporta três opções de redundância de dados: armazenamento localmente redundante (LRS), o armazenamento com redundância de zona (ZRS) e o armazenamento georredundante (GRS).

Premium de ficheiros do Azure partilha só suporta o armazenamento localmente redundante (LRS).

As secções seguintes descrevem as diferenças entre as opções de redundância diferentes:

### <a name="locally-redundant-storage"></a>Armazenamento localmente redundante

[!INCLUDE [storage-common-redundancy-LRS](../../../includes/storage-common-redundancy-LRS.md)]

### <a name="zone-redundant-storage"></a>Armazenamento com redundância de zona

[!INCLUDE [storage-common-redundancy-ZRS](../../../includes/storage-common-redundancy-ZRS.md)]

### <a name="geo-redundant-storage"></a>Armazenamento georredundante

> [!Warning]  
> Se estiver a utilizar a partilha de ficheiros do Azure como um ponto final da cloud numa conta de armazenamento GRS, não deve iniciar a ativação pós-falha de conta de armazenamento. Se o fizer, sincronização causa para parar o trabalho e Maio também causará perda de dados inesperado no caso de ficheiros recentemente em camadas. No caso de perda de uma região do Azure, a Microsoft irá acionar a ativação pós-falha a conta de armazenamento de forma que seja compatível com o Azure File Sync.

Armazenamento georredundante (GRS) foi concebido para proporcionar, pelo menos, 99,99999999999999% (16 9 s) a durabilidade dos objetos ao longo de um determinado ano ao replicar os seus dados para uma região secundária que está situada a centenas de quilómetros de distância da região primária. Se a sua conta de armazenamento tiver GRS ativada, em seguida, seus dados são duráveis mesmo em caso de uma falha regional completa ou um desastre no qual a região primária não é recuperável.

Se optar por armazenamento georredundante com acesso de leitura (RA-GRS), deve saber que ficheiros do Azure não oferece suporte a armazenamento georredundante com acesso de leitura (RA-GRS) em qualquer região neste momento. Partilhas de ficheiros na conta de armazenamento RA-GRS funcionam como fariam em contas GRS e são cobrados aos preço do GRS.

GRS replica os dados para outro centro de dados numa região secundária, mas que os dados estão disponíveis para ser só de leitura se a Microsoft iniciará uma ativação pós-falha dos principais para a região secundária.

Para uma conta de armazenamento com GRS ativada, todos os dados pela primeira vez é replicado com armazenamento localmente redundante (LRS). Uma atualização em primeiro lugar tem o compromisso para a localização primária e replicados utiliza o LRS. A atualização, em seguida, é replicada de forma assíncrona para a região secundária com GRS. Quando os dados são escritos para a localização secundária, também são replicado dentro desse local utiliza o LRS.

Regiões primárias e secundárias gerir réplicas em domínios de falha e domínios dentro de uma unidade de escala de armazenamento de atualização. A unidade de escala de armazenamento é a unidade de replicação básica no Centro de dados. A replicação a este nível é fornecida pela LRS; Para obter mais informações, consulte [armazenamento localmente redundante (LRS): Redundância de dados de baixo custo do armazenamento do Azure](../common/storage-redundancy-lrs.md).

Tenha em consideração esses pontos ao decidir que opção de replicação a utilizar:

* Armazenamento com redundância de zona (ZRS) fornece elevada disponibilidade com a replicação síncrona e pode ser uma opção melhor para alguns cenários que o GRS. Para obter mais informações sobre ZRS, veja [ZRS](../common/storage-redundancy-zrs.md).
* Replicação assíncrona envolve um atraso a partir do momento em que dados são escritos para a região primária, quando ele é replicado para a região secundária. Em caso de desastre regional, as alterações que ainda não tiverem sido replicadas para a região secundária podem ser perdidas se esses dados não podem ser recuperados da região primária.
* Com a GRS, a réplica não está disponível para leitura ou acesso de escrita, a menos que a Microsoft inicie uma ativação pós-falha para a região secundária. No caso de uma ativação pós-falha, vai ter de leitura e escrita acesso a esses dados após a ativação pós-falha foi concluída. Para obter mais informações, consulte [orientações sobre a recuperação após desastre](../common/storage-disaster-recovery-guidance.md).

## <a name="data-growth-pattern"></a>Padrão de crescimento de dados

Hoje em dia, o tamanho máximo para uma partilha de ficheiros do Azure é de 5 TiB (TiB 100 para partilhas de ficheiros de premium, que estão em pré-visualização pública). Por causa da limitação atual, deve considerar o crescimento de dados esperado durante a implantação de uma partilha de ficheiros do Azure.

É possível sincronizar as partilhas de ficheiros do Azure vários para um único servidor de ficheiros do Windows com o Azure File Sync. Isto permite-lhe garantir que as partilhas de ficheiros de grande, mais antigos que podem ter no local podem ser colocadas em Azure File Sync. Para obter mais informações, consulte [planear uma implementação de sincronização de ficheiros do Azure](storage-files-planning.md).

## <a name="data-transfer-method"></a>Método de transferência de dados

Existem muitas opções fácil em massa de transferência de dados a partir de um ficheiro existente partilharem, tais como uma partilha de ficheiros no local, para ficheiros do Azure. Algumas aplicações populares incluem (lista parcial):

* **Azure File Sync**: Como parte da primeira sincronização entre uma partilha de ficheiros do Azure (uma "ponto final da Cloud") e um espaço de nomes de diretório Windows (um "servidor de ponto final"), o Azure File Sync irá replicar todos os dados da partilha de ficheiros existentes para ficheiros do Azure.
* **[Importar/exportar do Azure](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)**: O serviço importar/exportar do Azure permite-lhe transferir de forma segura grandes quantidades de dados para uma partilha de ficheiros do Azure envie unidades de disco rígido num Datacenter do Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)**: O Robocopy é uma ferramenta de cópia bem conhecido que acompanha o Windows e Windows Server. Robocopy pode ser utilizado para transferir dados para ficheiros do Azure ao montar a partilha de ficheiros localmente e, em seguida, utilizar a localização de montado como o destino no comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json#upload-files-to-an-azure-file-share)**: O AzCopy é um utilitário de linha de comandos concebido para copiar dados de e para ficheiros do Azure, bem como armazenamento de Blobs do Azure, utilizando os comandos simples com um desempenho ideal. O AzCopy é disponível para Windows e Linux.

## <a name="next-steps"></a>Passos Seguintes
* [Planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md)
* [Implementar os ficheiros do Azure](storage-files-deployment-guide.md)
* [Implementar a sincronização de ficheiros do Azure](storage-sync-files-deployment-guide.md)
