---
title: Planeamento para uma implantação de Ficheiros Azure  Microsoft Docs
description: Saiba o que considerar ao planear uma implementação de Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 10/16/2019
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 9b71c4a5c0f245d9da97dc8f096d15c5386bf919
ms.sourcegitcommit: f97f086936f2c53f439e12ccace066fca53e8dc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2020
ms.locfileid: "77368612"
---
# <a name="planning-for-an-azure-files-deployment"></a>Planear uma implementação dos Ficheiros do Azure

[O Azure Files](storage-files-introduction.md) oferece partilhas de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo Padrão SMB da indústria. Uma vez que o Azure Files é totalmente gerido, implementá-lo em cenários de produção é muito mais fácil do que implementar e gerir um servidor de ficheiros ou dispositivo NAS. Este artigo aborda os tópicos a considerar ao implementar uma partilha de ficheiros Azure para uso de produção dentro da sua organização.

## <a name="management-concepts"></a>Conceitos de gestão

 O diagrama que se segue ilustra as construções de gestão de Ficheiros Azure:

![Estrutura de Ficheiros](./media/storage-files-introduction/files-concepts.png)

* **Conta de Armazenamento**: todos os acessos ao Armazenamento do Azure são feitos através de uma conta de armazenamento. Consulte [a escalabilidade e os alvos de desempenho para as contas padrão](../common/scalability-targets-standard-account.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json) de armazenamento para obter detalhes sobre a capacidade da conta de armazenamento.

* **Partilha:** uma partilha do Armazenamento de Ficheiros é uma partilha de ficheiros SMB no Azure. Todos os ficheiros e diretórios têm de ser criados numa partilha principal. Uma conta pode conter um número ilimitado de ações e uma ação pode armazenar um número ilimitado de ficheiros, até à capacidade total da parte do ficheiro. A capacidade total das ações de ficheiros premium e standard é de 100 TiB.

* **Diretório**: uma hierarquia opcional de diretórios.

* **Ficheiro**: um ficheiro na partilha. Um ficheiro pode ter até 1 TiB de tamanho.

* **Formato URL**: Para pedidos de uma partilha de ficheiros Azure feita com o protocolo FILE REST, os ficheiros são endereçados utilizando o seguinte formato URL:

    ```
    https://<storage account>.file.core.windows.net/<share>/<directory>/<file>
    ```

## <a name="data-access-method"></a>Método de acesso a dados

O Azure Files oferece dois métodos de acesso a dados incorporados e convenientes que pode utilizar separadamente, ou em combinação entre si, para aceder aos seus dados:

1. **Acesso direto à nuvem:** Qualquer partilha de ficheiros Azure pode ser montada pelo [Windows,](storage-how-to-use-files-windows.md) [macOS,](storage-how-to-use-files-mac.md)e/ou [Linux](storage-how-to-use-files-linux.md) com o protocolo padrão do Bloco de Mensagens do Servidor (SMB) da indústria ou através da API DO REST de Ficheiros. Com a SMB, as leituras e os escritos para ficheiros sobre a parte são feitos diretamente na parte do ficheiro no Azure. Para montar por um VM em Azure, o cliente SMB no SISTEMA deve suportar pelo menos SMB 2.1. Para montar no local, como na estação de trabalho de um utilizador, o cliente SMB suportado pela estação de trabalho deve suportar pelo menos SMB 3.0 (com encriptação). Além do SMB, novas aplicações ou serviços podem aceder diretamente à partilha de ficheiros através do File REST, que fornece uma interface de programação de aplicações fácil e escalável para desenvolvimento de software.
2. **Sincronização de ficheiros Azure**: Com o Azure File Sync, as ações podem ser replicadas para os Servidores Windows no local ou no Azure. Os seus utilizadores acederiam à partilha de ficheiros através do Windows Server, como por exemplo através de uma partilha De SMB ou NFS. Isto é útil para cenários em que os dados serão acedidos e modificados longe de um datacenter Azure, como num cenário de sucursal. Os dados podem ser replicados entre vários pontos finais do Windows Server, como entre várias agências. Por fim, os dados podem ser endereçados aos Ficheiros Azure, de modo a que todos os dados ainda estejam acessíveis através do Servidor, mas o Servidor não tem uma cópia completa dos dados. Pelo contrário, os dados são perfeitamente recolhidos quando abertos pelo utilizador.

A tabela que se segue ilustra como os seus utilizadores e aplicações podem aceder à sua partilha de ficheiros Azure:

| | Acesso direto à nuvem | Azure File Sync |
|------------------------|------------|-----------------|
| Que protocolos precisa usar? | Os Ficheiros Azure suportam SMB 2.1, SMB 3.0 e File REST API. | Aceda à sua partilha de ficheiros Azure através de qualquer protocolo suportado no Windows Server (SMB, NFS, FTPS, etc.) |  
| Onde está a trabalhar? | **In Azure**: Azure Files oferece acesso direto aos seus dados. | **No local com rede lenta**: Os clientes Windows, Linux e macOS podem montar uma partilha local no local do Windows File como uma cache rápida da sua partilha de ficheiros Azure. |
| Que nível de ACLs precisas? | Partilhar e arquivar nível. | Partilhar, arquivar e nível de utilizador. |

## <a name="data-security"></a>Segurança de dados

O Azure Files tem várias opções incorporadas para garantir a segurança dos dados:

* Suporte para encriptação em ambos os protocolos over-the-wire: Encriptação SMB 3.0 e File REST over HTTPS. Por defeito: 
    * Os clientes que suportam a encriptação SMB 3.0 enviam e recebem dados num canal encriptado.
    * Os clientes que não suportam sMB 3.0 com encriptação podem comunicar intra datacenter sobre SMB 2.1 ou SMB 3.0 sem encriptação. Os clientes SMB não estão autorizados a comunicar interdatacenter através de SMB 2.1 ou SMB 3.0 sem encriptação.
    * Os clientes podem comunicar através do File REST com HTTP ou HTTPS.
* Encriptação em repouso ( Encriptação do serviço de[armazenamento Azure](../common/storage-service-encryption.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)): Encriptação do serviço de armazenamento (SSE) está ativada para todas as contas de armazenamento. Os dados em repouso são encriptados com chaves totalmente geridas. A encriptação em repouso não aumenta os custos de armazenamento nem reduz o desempenho. 
* Requisito opcional de dados encriptados em trânsito: quando selecionados, o Azure Files rejeita o acesso aos dados por canais não encriptados. Especificamente, apenas são permitidas HTTPS e SMB 3.0 com ligações de encriptação.

    > [!Important]  
    > Exigir uma transferência segura de dados fará com que clientes SMB mais antigos não sejam capazes de comunicar com sMB 3.0 com encriptação a falhar. Para mais informações, consulte [Mount on Windows](storage-how-to-use-files-windows.md), Monte no [Linux](storage-how-to-use-files-linux.md)e [Monte no macOS.](storage-how-to-use-files-mac.md)

Para máxima segurança, recomendamos vivamente que permita sempre a encriptação em repouso e permitindo a encriptação de dados em trânsito sempre que estiver a usar clientes modernos para aceder aos seus dados. Por exemplo, se precisar de montar uma parte num VM R2 do Windows Server 2008, que apenas suporta SMB 2.1, tem de permitir o tráfego não encriptado na sua conta de armazenamento, uma vez que o SMB 2.1 não suporta encriptação.

Se estiver a utilizar o Azure File Sync para aceder à partilha de ficheiros Do Azure, utilizaremos sempre HTTPS e SMB 3.0 com encriptação para sincronizar os seus dados com os seus Servidores Windows, independentemente de necessitar de encriptação de dados em repouso.

## <a name="file-share-performance-tiers"></a>Níveis de desempenho de partilha de ficheiros

O Azure Files oferece dois níveis de desempenho: standard e premium.

### <a name="standard-file-shares"></a>Ações de ficheiros padrão

As ações padrão de ficheiro são apoiadas por discos rígidos (HDDs). As ações de ficheiros padrão proporcionam um desempenho fiável para cargas de trabalho IO menos sensíveis à variabilidade do desempenho, tais como partilhas de ficheiros de uso geral e ambientes de dev/teste. As ações padrão de ficheiro saem apenas disponíveis num modelo de faturação pay-as-you-go.

> [!IMPORTANT]
> Se pretender utilizar ações de ficheiros superiores a 5 TiB, consulte a secção [Onboard para maiores partilhas de ficheiros (nível padrão)](#onboard-to-larger-file-shares-standard-tier) para as medidas a bordo, bem como disponibilidade e restrições regionais.

### <a name="premium-file-shares"></a>Ações de ficheiropremium

As ações de ficheiros premium são apoiadas por unidades de estado sólido (SSDs). As ações de ficheiros premium proporcionam um desempenho consistente e baixa latência, dentro de milissegundos de um dígito para a maioria das operações io, para cargas de trabalho intensivas em OI. Isto torna-os adequados para uma grande variedade de cargas de trabalho, como bases de dados, hospedagem de sites e ambientes de desenvolvimento. As ações de ficheiropremium só estão disponíveis num modelo de faturação provisionado. As ações de ficheiropremium utilizam um modelo de implantação separado das ações padrão de ficheiros.

A Azure Backup está disponível para ações de ficheiropremium e o Serviço Azure Kubernetes suporta ações de ficheiropremium na versão 1.13 ou superior.

Se quiser aprender a criar uma partilha de ficheiros premium, consulte o nosso artigo sobre o assunto: Como criar uma conta de armazenamento de [ficheiros premium Azure](storage-how-to-create-premium-fileshare.md).

Atualmente, não é possível converter diretamente entre uma participação padrão de ficheiro e uma parte de ficheiro premium. Se quiser mudar para qualquer um dos níveis, tem de criar uma nova quota de ficheiro nesse nível e copiar manualmente os dados da sua parte original para a nova partilha que criou. Pode fazê-lo utilizando qualquer uma das ferramentas de cópia suportadas pelo Azure Files, como robocopy ou AzCopy.

> [!IMPORTANT]
> As ações de ficheiropremium estão disponíveis com lRS na maioria das regiões que oferecem contas de armazenamento e com ZRS num subconjunto menor de regiões. Para saber se as ações de ficheiropremium estão atualmente disponíveis na sua região, consulte os [produtos disponíveis por página da região](https://azure.microsoft.com/global-infrastructure/services/?products=storage) para o Azure. Para obter informações sobre regiões que apoiam o ZRS, consulte o [despedimento do Armazenamento Azure.](../common/storage-redundancy.md)
>
> Para nos ajudar a priorizar novas regiões e características de nível premium, preencha este [inquérito.](https://aka.ms/pfsfeedback)

#### <a name="provisioned-shares"></a>Ações provisionadas

As ações de ficheiropremium são provisionadas com base numa relação gib/iops/perput fixa. Para cada GiB previsto, a ação será emitida uma entrada IOPS e 0,1 MiB/s até aos limites máximos por ação. O fornecimento mínimo permitido é 100 GiB com min IOPS/entrada.

Numa melhor base de esforço, todas as ações podem rebentar até três IOPS por GiB de armazenamento provisionado por 60 minutos ou mais, dependendo da dimensão da parte. As novas ações começam com o crédito total rebentado com base na capacidade provisionada.

As ações devem ser aprovisionadas em incrementos de 1 GiB. O tamanho mínimo é 100 GiB, o próximo tamanho é 101 GiB e assim por diante.

> [!TIP]
> IOPS base = 1 * giB provisionado. (Até um máximo de 100.000 IOPS).
>
> Limite de rutura = 3 * IOPS de base. (Até um máximo de 100.000 IOPS).
>
> taxa de egress = 60 MiB/s + 0,06 * giB provisionado
>
> taxa de entrada = 40 MiB/s + 0,04 * giB provisionado

O tamanho das ações provisionadas é especificado por quota de ações. A quota de ações pode ser aumentada a qualquer momento, mas só pode ser diminuída após 24 horas desde o último aumento. Depois de esperar 24 horas sem um aumento de quota, pode diminuir a quota de partilha quantas vezes quiser, até que volte a aumentá-la. As alterações na escala IOPS/Deputadas serão eficazes dentro de alguns minutos após a alteração do tamanho.

É possível diminuir o tamanho da sua quota provisionada abaixo do Seu GiB usado. Se o fizer, não perderá dados, mas continuará a ser faturado pelo tamanho utilizado e receberá o desempenho (IOPS de base, entrada e iOPS rebentado) da parte provisionada, e não pelo tamanho utilizado.

O quadro que se segue ilustra alguns exemplos destas fórmulas para as dimensões das ações previstas:

|Capacidade (GiB) | IOPS de base | IOPS rebentando | Egress (MiB/s) | Ingress (MiB/s) |
|---------|---------|---------|---------|---------|
|100         | 100     | Até 300     | 66   | 44   |
|500         | 500     | Até 1.500   | 90   | 60   |
|1,024       | 1,024   | Até 3.072   | 122   | 81   |
|5,120       | 5,120   | Até 15.360  | 368   | 245   |
|10,240      | 10,240  | Até 30.720  | 675 | 450   |
|33,792      | 33,792  | Até 100.000 | 2,088 | 1,392   |
|51,200      | 51,200  | Até 100.000 | 3,132 | 2,088   |
|102,400     | 100 000 | Até 100.000 | 6,204 | 4,136   |

> [!NOTE]
> O desempenho das partilhas de ficheiros está sujeito a limites de rede de máquinas, largura de banda de rede disponível, tamanhos IO, paralelismo, entre muitos outros fatores. Por exemplo, com base em testes internos com 8 tamanhos de IO de leitura/escrita KiB, uma única máquina virtual do Windows, *Standard F16s_v2*, ligada à partilha de ficheiros premium sobre SMB poderia atingir IOPS de leitura de 20K e 15K escrever IOPS. Com 512 tamanhos de IO de leitura/escrita MiB, o mesmo VM poderia alcançar 1.1 GiB/s egress e 370 MiB/s entrada de entrada. Para atingir a escala máxima de desempenho, espalhe a carga por vários VMs. Consulte o guia de resolução de [problemas](storage-troubleshooting-files-performance.md) para alguns problemas comuns de desempenho e sobras.

#### <a name="bursting"></a>Rebentando

As ações de ficheiropremium podem rebentar o seu IOPS até um fator de três. A explosão é automatizada e funciona com base num sistema de crédito. A explosão funciona com o melhor esforço e o limite de rutura não é uma garantia, as ações de ficheiropodem rebentar *até* ao limite.

Os créditos acumulam-se num balde de rutura sempre que o tráfego para a sua parte de ficheiro saem abaixo do IOPS de base. Por exemplo, uma quota de 100 GiB tem 100 IOPS de base. Se o tráfego real na parte foi de 40 IOPS para um intervalo específico de 1 segundo, então os 60 IOPS não utilizados são creditados a um balde de rutura. Estes créditos serão então utilizados posteriormente quando as operações excederem os IOPs de base.

> [!TIP]
> Tamanho do balde de rutura = IOPS base * 2 * 3600.

Sempre que uma ação exceda o IOPS de base e tenha créditos num balde de rutura, rebenta. As ações podem continuar a rebentar enquanto os créditos permanecerem, embora as ações inferiores a 50 TiB só permaneçam no limite de rutura até uma hora. As ações superiores a 50 TiB podem tecnicamente ultrapassar este limite de uma hora, até duas horas, mas, isto baseia-se no número de créditos de rutura acumulados. Cada IO para além da linha de base IOPS consome um crédito e uma vez consumidos todos os créditos a parte regressaria ao IOPS de base.

Os créditos de ações têm três estados:

- Acumulação, quando a parte do ficheiro está a usar menos do que o IOPS de base.
- Em declínio, quando a parte do ficheiro está a rebentar.
- Permanecendo constante, quando não há créditos ou IOPS de base estão em uso.

As novas ações começam com o número total de créditos no balde de rutura. Os créditos de rebentamento não serão acumulados se o IOPS de ação ficar abaixo do IOPS de base devido à aceleração pelo servidor.

## <a name="file-share-redundancy"></a>Redundância de partilha de ficheiros

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Se optar pelo armazenamento georedundant de acesso de leitura (RA-GRS), deve saber que o Ficheiro Azure não suporta armazenamento geo-redundante de acesso de leitura (RA-GRS) em qualquer região neste momento. As ações de ficheiro na conta de armazenamento RA-GRS funcionam como fariam nas contas GRS e são cobradas preços GRS.

> [!Warning]  
> Se estiver a usar a sua quota de ficheiroS Azure como ponto final de nuvem numa conta de armazenamento GRS, não deve iniciar o failover da conta de armazenamento. Se a fizer, fará com que a sincronização deixe de funcionar e poderá também causar perdas de dados inesperadas em caso de ficheiros com novo escalão. Em caso de perda de uma região do Azure, a Microsoft irá desencadear a falha da conta de armazenamento de uma forma compatível com o Azure File Sync.

As ações premium da Azure Files suportam tanto o LRS como o ZRS, o ZRS está atualmente disponível num subconjunto menor de regiões.

## <a name="onboard-to-larger-file-shares-standard-tier"></a>A bordo de maiores quotas de ficheiros (nível padrão)

Esta secção aplica-se apenas às ações de ficheiros padrão. Todas as ações de ficheiropremium estão disponíveis com capacidade de 100 TiB.

### <a name="restrictions"></a>Restrições

- A conversão da conta LRS/ZRS para GRS/GZRS não será possível para qualquer conta de armazenamento com grandes ações de ficheiros ativadas.

### <a name="regional-availability"></a>Disponibilidade regional

As ações de ficheiros standard com limite de capacidade de 100 TiB estão disponíveis globalmente em todas as regiões do Azure -

- LRS: Todas as regiões, exceto a África do Sul Norte e África do Sul Oeste.
- ZRS: Todas as regiões, exceto japão Leste, Norte da Europa, África do Sul Norte.
- GRS/GZRS: Não suportado.

### <a name="enable-and-create-larger-file-shares"></a>Ativar e criar maiores partilhas de ficheiros

Para começar a usar maiores partilhas de ficheiros, consulte o nosso artigo [Como ativar e criar grandes partilhas](storage-files-how-to-create-large-file-share.md)de ficheiros .

## <a name="data-growth-pattern"></a>Padrão de crescimento de dados

Hoje, o tamanho máximo para uma partilha de ficheiros Azure é de 100 TiB. Devido a esta limitação atual, deve considerar o crescimento esperado dos dados ao implementar uma quota de ficheiros Azure.

É possível sincronizar várias partilhas de ficheiros Azure num único Servidor de Ficheiros Windows com O Sincronizado de Ficheiros Azure. Isto permite-lhe garantir que as partilhas de ficheiros mais antigas e grandes que possa ter no local podem ser trazidas para o Azure File Sync. Para mais informações, consulte [Planplan para uma implantação](storage-files-planning.md)de sincronização de ficheiros Azure .

## <a name="data-transfer-method"></a>Método de transferência de dados

Existem muitas opções fáceis de transferir dados a granel de uma parte de ficheiro existente, como uma partilha de ficheiros no local, para Ficheiros Azure. Alguns populares incluem (lista não exaustiva):

* **[Sincronização de ficheiros Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning)** : Como parte de uma primeira sincronização entre uma partilha de ficheiros Azure (um "Cloud Endpoint") e um espaço de nome de diretório Windows (um "Server Endpoint"), o Azure File Sync irá replicar todos os dados desde a partilha de ficheiros existente para ficheiros Azure.
* **[Azure Import/Export](../common/storage-import-export-service.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : O serviço de importação/exportação Azure permite-lhe transferir com segurança grandes quantidades de dados para uma parte de ficheiro Sutiã, enviando discos rígidos para um centro de dados Azure. 
* **[Robocopy](https://technet.microsoft.com/library/cc733145.aspx)** : Robocopy é uma ferramenta de cópia bem conhecida que envia com Windows e Windows Server. A robocópia pode ser usada para transferir dados para Ficheiros Azure, montando a parte do ficheiro localmente e, em seguida, usando a localização montada como destino no comando Robocopy.
* **[AzCopy](../common/storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2ffiles%2ftoc.json)** : AzCopy é um utilitário de linha de comando projetado para copiar dados de e para o Azure Files, bem como armazenamento Azure Blob, utilizando comandos simples com um desempenho ótimo.

## <a name="next-steps"></a>Passos seguintes
* [Planejamento de uma implantação de sincronização de ficheiros Azure](storage-sync-files-planning.md)
* [Implementação de ficheiros Azure](storage-files-deployment-guide.md)
* [Implementação de sincronização de ficheiros Azure](storage-sync-files-deployment-guide.md)
