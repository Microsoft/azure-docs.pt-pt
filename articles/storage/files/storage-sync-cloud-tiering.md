---
title: Compreender o Azure File Sync Cloud Tiering Microsoft Docs
description: Leia sobre o tiering de nuvem, uma funcionalidade opcional de Sincronização de Ficheiros Azure. Os ficheiros acedidos frequentemente são cached localmente no servidor; outros são hierarquizados para Ficheiros Azure.
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 06/15/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: e5aafaa02f503582bd0050f8a6389d78b52eaa76
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939158"
---
# <a name="cloud-tiering-overview"></a>Visão geral do tiering da nuvem
O tiering em nuvem é uma funcionalidade opcional do Azure File Sync, no qual os ficheiros frequentemente acedidos são cached localmente no servidor, enquanto todos os outros ficheiros são hierárquicos para Ficheiros Azure baseados em definições de política. Quando um ficheiro é hierarquizado, o filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) substitui o ficheiro localmente por um ponteiro ou ponto de reparse. O ponto de reparse representa um URL para o ficheiro em Ficheiros Azure. Um ficheiro hierárquico tem o atributo "offline" e o atributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definido no NTFS para que as aplicações de terceiros possam identificar de forma segura ficheiros hierárquicos.
 
Quando um utilizador abre um ficheiro hierárquico, o Azure File Sync recorda perfeitamente os dados do ficheiro a partir de Ficheiros Azure sem que o utilizador precise de saber que o ficheiro está armazenado no Azure. 
 
 > [!Important]  
 > O tiering em nuvem não é suportado no volume do sistema Windows.
    
 > [!Important]  
 > Para recordar ficheiros que tenham sido nivelados, a largura de banda da rede deve ser de pelo menos 1 Mbps. Se a largura de banda da rede for inferior a 1 Mbps, os ficheiros podem não conseguir ser recolhidos com um erro de tempo limite.

## <a name="cloud-tiering-faq"></a>FaQ de tiering de nuvem

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Como funciona o nível das nuvens?
O filtro do sistema Azure File Sync constrói um "mapa de calor" do seu espaço de identificação em cada ponto final do servidor. Monitoriza os acessos (operações de leitura e escrita) ao longo do tempo e, em seguida, com base tanto na frequência como na recessão do acesso, atribui uma pontuação de calor a cada ficheiro. Um ficheiro frequentemente acedido que foi recentemente aberto será considerado quente, enquanto um ficheiro que mal é tocado e não foi acedido há algum tempo será considerado legal. Quando o volume de ficheiros de um servidor exceder o limiar de espaço livre de volume definido, irá nivelar os ficheiros mais frescos para Azure Files até que a sua percentagem de espaço livre seja satisfeita.

Além disso, pode especificar uma política de data em cada ponto final do servidor que irá nivelar quaisquer ficheiros não acedidos dentro de um número especificado de dias, independentemente da capacidade de armazenamento local disponível. Esta é uma boa escolha para libertar proativamente o espaço do disco local se você sabe que os ficheiros nesse ponto final do servidor não precisam de ser mantidos localmente para além de uma certa idade. Isso liberta capacidade valiosa de disco local para outros pontos finais no mesmo volume, para cache mais dos seus ficheiros.

O mapa de calor de nível de nuvem é essencialmente uma lista ordenada de todos os ficheiros que estão sincronizados e estão num local que tem o tiering de nuvem ativado. Para determinar a posição relativa de um ficheiro individual nesse mapa térmico, o sistema utiliza o máximo de qualquer um dos seguintes cartões temporais, por esta ordem: MAX (Última Hora de Acesso, Última Hora Modificada, Tempo de Criação). Normalmente, o último tempo de acesso é rastreado e disponível. No entanto, quando um novo ponto final do servidor é criado, com o tiering da nuvem ativado, então inicialmente não passou tempo suficiente para observar o acesso ao ficheiro. Na ausência de um último tempo de acesso, o último tempo modificado é utilizado para avaliar a posição relativa no mapa de calor. O mesmo recuo é aplicável à política de datas. Sem uma última hora de acesso, a política de datas atuará no último tempo modificado. Se isso não estiver disponível, irá recorrer ao tempo de criação de um ficheiro. Com o tempo, o sistema observará cada vez mais pedidos de acesso a ficheiros e pivô para utilizar predominantemente o último tempo de acesso auto-rastreado.

O tiering em nuvem não depende da funcionalidade NTFS para rastrear a última vez de acesso. Esta funcionalidade NTFS está desligada por padrão e devido a considerações de desempenho, não recomendamos que ative manualmente esta funcionalidade. As faixas de nivelamento da nuvem último tempo de acesso separada e eficientemente.

<a id="tiering-minimum-file-size"></a>
### <a name="what-is-the-minimum-file-size-for-a-file-to-tier"></a>Qual é o tamanho mínimo do ficheiro para um ficheiro de nível?

Para as versões 9 e mais recentes, o tamanho mínimo do ficheiro para um ficheiro é baseado no tamanho do cluster do sistema de ficheiros. O tamanho mínimo do ficheiro elegível para o tiering da nuvem é calculado em 2x o tamanho do cluster e no mínimo 8 KB. A tabela a seguir ilustra os tamanhos mínimos de ficheiro que podem ser nivelados, com base no tamanho do cluster de volume:

|Tamanho do cluster de volume (Bytes) |Arquivos deste tamanho ou maior podem ser hierarquizados  |
|----------------------------|---------|
|4 KB ou menor (4096)      | 8 KB    |
|8 KB (8192)                 | 16 KB   |
|16 KB (16384)               | 32 KB   |
|32 KB (32768)               | 64 KB   |
|64 KB (65536)    | 128 KB  |

Os tamanhos do cluster até 64 KB são atualmente suportados mas, para tamanhos maiores, o nível de nuvem não funciona.

Todos os sistemas de ficheiros utilizados pelo Windows, organizem o seu disco rígido com base no tamanho do cluster (também conhecido como tamanho da unidade de atribuição). O tamanho do cluster representa a menor quantidade de espaço em disco que pode ser usado para segurar um ficheiro. Quando os tamanhos dos ficheiros não saem para um múltiplo do tamanho do cluster, deve ser usado espaço adicional para segurar o ficheiro - até ao próximo múltiplo do tamanho do cluster.

O Azure File Sync é suportado em volumes NTFS com Windows Server 2012 R2 e mais recentes. A tabela seguinte descreve os tamanhos de cluster padrão quando cria um novo volume NTFS. 

|Tamanho do volume    |Windows Server 2012R2 e mais recente |
|---------------|---------------|
|7 MB - 16 TB   | 4 KB          |
|16TB - 32 TB   | 8 KB          |
|32TB - 64 TB   | 16 KB         |
|64TB - 128 TB  | 32 KB         |
|128TB - 256 TB | 64 KB         |
|> 256 TB       | Não suportado |

É possível que, após a criação do volume, formatesse manualmente o volume com um tamanho de cluster diferente. Se o seu volume provém de uma versão mais antiga do Windows, os tamanhos de cluster padrão também podem ser diferentes. [Este artigo tem mais detalhes sobre os tamanhos de cluster padrão.](https://support.microsoft.com/help/140365/default-cluster-size-for-ntfs-fat-and-exfat) Mesmo que escolha um tamanho de cluster inferior a 4 KB, um limite de 8 KB como o menor tamanho de ficheiro que pode ser nivelado, ainda se aplica. (Mesmo que tecnicamente o tamanho do cluster de 2x equivalesse a menos de 8 KB.)

A razão para o mínimo absoluto encontra-se na forma como o NTFS armazena ficheiros extremamente pequenos - 1 KB a 4 ficheiros de tamanho KB. Dependendo de outros parâmetros do volume, é possível que os pequenos ficheiros não sejam armazenados num cluster no disco. É possivelmente mais eficiente armazenar tais ficheiros diretamente na Master File Table do volume ou "MFT record". O ponto de reparse de nivelamento de nuvens é sempre armazenado no disco e ocupa exatamente um cluster. A nivelação de ficheiros tão pequenos pode acabar sem poupanças de espaço. Casos extremos podem até acabar por usar mais espaço com o tiering de nuvens ativado. Para evitar isso, o menor tamanho de um ficheiro que o nível da nuvem irá tiering, é de 8 KB em um tamanho de cluster de 4 KB ou menor.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Como funciona a política de arrumo de espaço livre no volume?
O espaço livre de volume é a quantidade de espaço livre que deseja reservar no volume em que está localizado um ponto final do servidor. Por exemplo, se o espaço livre de volume estiver definido para 20% num volume que tenha um ponto final de servidor, até 80% do espaço de volume será ocupado pelos ficheiros mais recentemente acedidos, com quaisquer ficheiros restantes que não se encaixem neste espaço tiered até Azure. O espaço livre de volume aplica-se ao nível do volume e não ao nível de diretórios individuais ou grupos de sincronização. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Como funciona a política de arrumo de espaço livre no volume no que se refere a novos pontos finais do servidor?
Quando um ponto final do servidor é recentemente a provisionado e ligado a uma partilha de ficheiros Azure, o servidor primeiro puxará para baixo o espaço de nome e, em seguida, irá retirar os ficheiros reais até atingir o seu limiar de espaço livre de volume. Este processo também é conhecido como rápida recuperação de desastres ou rápida restauração do espaço de nome.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Como é que o espaço livre de volume é interpretado quando tenho vários pontos finais do servidor num volume?
Quando há mais de um ponto final do servidor num volume, o limiar de espaço livre de volume efetivo é o maior espaço livre de volume especificado em qualquer ponto final do servidor nesse volume. Os ficheiros serão nivelados de acordo com os seus padrões de utilização, independentemente do ponto final do servidor a que pertencem. Por exemplo, se tiver dois pontos finais de servidor num volume, Endpoint1 e Endpoint2, onde o Endpoint1 tem um limiar de espaço livre de volume de 25% e o Endpoint2 tem um limiar de espaço livre de volume de 50%, o limiar de espaço livre de volume para ambos os pontos finais do servidor será de 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Como funciona a política de camadas de dados em conjunto com a política de arrumo de espaço livre no volume? 
Ao ativar o nível da nuvem num ponto final do servidor, define uma política de espaço livre de volume. Tem sempre precedência sobre quaisquer outras políticas, incluindo a política de datas. Opcionalmente, pode ativar uma política de data para cada ponto final do servidor nesse volume. Esta política gere que apenas os ficheiros acedidos (isto é, lidos ou escritos) dentro do intervalo de dias que esta política descreve serão mantidos locais. Os ficheiros não acedidos com o número de dias especificados serão hierárquicos. 

Cloud Tiering utiliza o último tempo de acesso para determinar quais os ficheiros que devem ser nivelados. O controlador de filtro de camadas de nuvem (storagesync.sys) rastreia a última hora de acesso e regista a informação na loja de calor de nivelamento de nuvens. Pode recuperar a loja de calor e guardá-la num ficheiro CSV utilizando um cmdlet PowerShell local do servidor.

```powershell
# There is a single heat store for files on a volume / server endpoint / individual file.
# The heat store can get very large. If you only need to retrieve the "coolest" number of items, use -Limit and a number

# Import the PS module:
Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'

# VOLUME FREE SPACE: To get the order in which files will be tiered using the volume free space policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrder

# DATE POLICY: To get the order in which files will be tiered using the date policy:
Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName LastAccessTimeWithSyncAndTieringOrderV2

# Find the heat store information for a particular file:
Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'
```

> [!IMPORTANT]
> O último timetamp não é uma propriedade rastreada pelo NTFS e, portanto, não é visível por padrão no File Explorer. Não utilize o último aviso de tempo modificado num ficheiro para verificar se a política de data funciona como esperado. Esta estamp só escreve, não lê. Utilize o cmdlet mostrado para obter o último tempo de acesso para esta avaliação.

> [!WARNING]
> Não ligue a funcionalidade NTFS de rastrear o último teste de tempos acedidos para ficheiros e pastas. Esta funcionalidade está desligada por padrão porque tem um grande impacto de desempenho. O Azure File Sync rastreará os tempos de última hora acedidos de forma automática e muito eficiente e não utilizará esta funcionalidade NTFS.

Tenha em mente que a política de espaço livre de volume tem sempre precedência, e quando não há espaço livre suficiente no volume para reter tantos dias de ficheiros como descrito pela política de data, o Azure File Sync continuará a nivelar os ficheiros mais frios até que a percentagem de espaço livre de volume seja satisfeita.

Por exemplo, digamos que tem uma política de tiering baseada em datas de 60 dias e uma política de espaço livre de volume de 20%. Se depois de aplicar a política de data, houver menos de 20% de espaço livre no volume, a política de espaço livre de volume entrará em vigor e anulará a política de data. Isto resultará na divisão de mais ficheiros, de modo a que a quantidade de dados mantidos no servidor possa ser reduzida de 60 dias de dados para 45 dias. Inversamente, esta política forçará o tiering de ficheiros que ficam fora do seu intervalo de tempo mesmo que não tenha atingido o seu limiar de espaço livre – por isso, um ficheiro com 61 dias será tiered mesmo que o seu volume esteja vazio.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Como posso determinar a quantidade adequada de espaço livre no volume?
A quantidade de dados que deve manter local é determinada por alguns fatores: a sua largura de banda, o padrão de acesso do seu conjunto de dados e o seu orçamento. Se tiver uma ligação de baixa largura de banda, é melhor manter mais dos seus dados locais para garantir que existe um atraso mínimo para os seus utilizadores. Caso contrário, pode baseá-la na taxa de churn durante um determinado período. Por exemplo, se souber que cerca de 10% do conjunto de dados de 1 TB altera ou é acedido ativamente todos os meses, então pode querer manter 100 GB locais para que não esteja frequentemente a recordar ficheiros. Se o seu volume for de 2TB, então vai querer manter 5% (ou 100 GB) local, o que significa que os restantes 95% são a sua percentagem de espaço livre de volume. No entanto, recomendamos que adicione um tampão para ter em conta períodos de maior agitação – ou seja, começando com uma percentagem de espaço livre de volume mais baixa e, em seguida, ajustando-o se necessário mais tarde. 

Manter mais dados locais significa custos de saída mais baixos, uma vez que menos ficheiros serão recolhidos do Azure, mas também requer que mantenha uma maior quantidade de armazenamento no local, o que vem a seu custo próprio. Uma vez implementada uma instância do Azure File Sync, pode olhar para a saída da sua conta de armazenamento para avaliar aproximadamente se as definições de espaço livre de volume são adequadas para a sua utilização. Assumindo que a conta de armazenamento contém apenas o seu Azure File Sync Cloud Endpoint (isto é, a sua parte de sincronização), então a saída alta significa que muitos ficheiros estão a ser recolhidos da nuvem, e deve considerar aumentar a cache local.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Adicionei um novo ponto final do servidor. Quanto tempo até os meus ficheiros neste nível de servidor?

Se os ficheiros precisam ou não de ser nivelados por políticas definidas é avaliado uma vez por hora. Pode encontrar duas situações quando um novo ponto final do servidor é criado:

1. Quando adiciona um novo ponto final do servidor, muitas vezes existem ficheiros nessa localização do servidor. Têm de ser carregados primeiro, antes que o nível das nuvens possa começar. A política de espaço livre de volume não começará o seu trabalho até que o carregamento inicial de todos os ficheiros tenha terminado. No entanto, a política de datas opcionais começará a funcionar numa base de ficheiro individual, assim que um ficheiro tiver sido carregado. O intervalo de uma hora também se aplica aqui. 
2. Quando adiciona um novo ponto final do servidor, é possível que conecte uma localização vazia do servidor a uma partilha de ficheiros Azure com os seus dados nele. Seja para um segundo servidor ou durante uma situação de recuperação de desastres. Se optar por descarregar o espaço de nomes e recordar o conteúdo durante o download inicial para o seu servidor, depois de o espaço de nome sair, os ficheiros serão recolhidos com base na última marca de tempo modificada. Apenas quantos ficheiros serão recolhidos como adequados dentro da política de espaço livre de volume e da política de data opcional.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Como posso saber se um ficheiro foi divulgado?
Existem várias formas de verificar se um ficheiro foi ligado à sua partilha de ficheiros Azure:
    
   *  **Verifique os atributos do ficheiro no ficheiro.**
     Clique com o direito num ficheiro, vá a **Detalhes**e, em seguida, desloque-se para a propriedade **Atributos.** Um ficheiro hierárquico tem os seguintes atributos definidos:     
        
        | Carta de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | A | Arquivo | Indica que o ficheiro deve ser apoiado por software de backup. Este atributo é sempre definido, independentemente de o ficheiro ser hierárquico ou armazenado totalmente no disco. |
        | P | Arquivo escasso | Indica que o ficheiro é um ficheiro escasso. Um ficheiro escasso é um tipo de ficheiro especializado que o NTFS oferece para uma utilização eficiente quando o ficheiro na corrente do disco está praticamente vazio. O Azure File Sync utiliza ficheiros escassos porque um ficheiro é totalmente hierarquizado ou parcialmente recolhido. Num ficheiro totalmente hierárquico, o fluxo de ficheiros é armazenado na nuvem. Num ficheiro parcialmente recolhido, essa parte do ficheiro já está no disco. Se um ficheiro for totalmente recolhido para o disco, o Azure File Sync converte-o de um ficheiro escasso para um ficheiro regular. Este atributo só está definido no Windows Server 2016 ou mais antigo.|
        | M | Lembre-se no acesso aos dados | Indica que os dados do ficheiro não estão totalmente presentes no armazenamento local. A leitura do ficheiro fará com que pelo menos parte do conteúdo do ficheiro seja recolhido a partir de uma partilha de ficheiros Azure à qual o ponto final do servidor está ligado. Este atributo só está definido no Windows Server 2019. |
        | L | Ponto de reanálise | Indica que o ficheiro tem um ponto de reparse. Um ponto de reparse é um ponteiro especial para utilização por um filtro de sistema de ficheiros. O Azure File Sync utiliza pontos de re-reserva para definir para o filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) a localização da nuvem onde o ficheiro está armazenado. Isto suporta um acesso sem emenda. Os utilizadores não precisarão de saber que o Azure File Sync está a ser utilizado ou como obter acesso ao ficheiro na sua partilha de ficheiros Azure. Quando um ficheiro é totalmente recolhido, o Azure File Sync remove o ponto de reparse do ficheiro. |
        | O | Offline | Indica que parte ou todo o conteúdo do ficheiro não é armazenado no disco. Quando um ficheiro é totalmente recolhido, o Azure File Sync remove este atributo. |

        ![A caixa de diálogo Properties para um ficheiro, com o separador Detalhes selecionado](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Pode ver os atributos de todos os ficheiros numa pasta adicionando o campo **Atributos** à exibição da tabela do Explorador de Ficheiros. Para isso, clique com o botão direito numa coluna existente (por exemplo, **Tamanho),** selecione **Mais**e, em seguida, selecione **Atributos** da lista de drop-down.
        
   * **Utilize `fsutil` para verificar se há pontos de reparse num ficheiro.**
       Como descrito na opção anterior, um ficheiro hierárquico tem sempre um conjunto de pontos de reparse. Um ponteiro de reparse é um ponteiro especial para o filtro do sistema de ficheiros Azure File Sync (StorageSync.sys). Para verificar se um ficheiro tem um ponto de reparse, numa janela de comando elevada ou powerShell, verifique o `fsutil` utilitário:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o ficheiro tiver um ponto de reparse, pode esperar ver **o Valor da Etiqueta Reparse: 0x8000001e**. Este valor hexadecimal é o valor de ponto de reparse que é propriedade da Azure File Sync. A saída também contém os dados de reparse que representam o caminho para o seu ficheiro na sua partilha de ficheiros Azure.

        > [!WARNING]  
        > O `fsutil reparsepoint` comando de utilidade também tem a capacidade de apagar um ponto de reparse. Não execute este comando a menos que a equipa de engenharia Azure File Sync lhe peça. Executar este comando pode resultar em perda de dados. 

<a id="afs-recall-file"></a>
### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Um ficheiro que quero usar foi hierarquizado. Como posso lembrar-me do ficheiro para o disco para o usar localmente?
A maneira mais fácil de chamar um ficheiro para o disco é abrir o ficheiro. O filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) descarrega perfeitamente o ficheiro da sua partilha de ficheiros Azure sem qualquer trabalho da sua parte. Para tipos de ficheiros que podem ser parcialmente lidos, tais como ficheiros multimédia ou .zip, a abertura de um ficheiro não descarrega todo o ficheiro.

Também pode utilizar o PowerShell para forçar a retirada de um ficheiro. Esta opção pode ser útil se pretender relembr em vários ficheiros de uma só vez, como todos os ficheiros de uma pasta. Abra uma sessão PowerShell para o nó do servidor onde o Azure File Sync está instalado e, em seguida, executar os seguintes comandos PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parâmetros opcionais:
* `-Order CloudTieringPolicy` recordará primeiro os ficheiros modificados ou acedidos mais recentemente e é permitido pela atual política de tiering. 
    * Se a política de espaço livre de volume estiver configurada, os ficheiros serão recolhidos até que a definição da política de espaço livre de volume seja alcançada. Por exemplo, se a definição da política livre de volume for de 20%, a recuperação irá parar quando o espaço livre de volume chegar aos 20%.  
    * Se a política de espaço e data livre de volume estiver configurada, os ficheiros serão recolhidos até que a definição da política de definição de política de volume livre ou de data seja alcançada. Por exemplo, se a definição de política livre de volume for de 20% e a política de data for de 7 dias, a chamada para quando o espaço livre de volume atingir 20% ou todos os ficheiros acedidos ou modificados dentro de 7 dias são locais.
* `-ThreadCount` determina quantos ficheiros podem ser recolhidos em paralelo.
* `-PerFileRetryCount`determina quantas vezes uma recuperação será tentada de um ficheiro que está atualmente bloqueado.
* `-PerFileRetryDelaySeconds`determina o tempo em segundos entre a tentativa de recuperação e deve ser sempre utilizado em combinação com o parâmetro anterior.

Exemplo:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
> - O Invoke-StorageSyncFileRecall cmdlet também pode ser usado para melhorar o desempenho do download de ficheiros ao adicionar um novo ponto final do servidor a um grupo de sincronização existente.  
>- Se o volume local que hospeda o servidor não tiver espaço livre suficiente para recordar todos os dados hierárquicos, o `Invoke-StorageSyncFileRecall` cmdlet falha.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Por que o tamanho na propriedade *do disco* para um ficheiro corresponde à propriedade *Size* depois de usar Azure File Sync? 
O Windows File Explorer expõe duas propriedades para representar o tamanho de um ficheiro: **Tamanho** e **tamanho no disco.** Estas propriedades diferem subtilmente no significado. **O tamanho** representa o tamanho completo do ficheiro. **O tamanho do disco** representa o tamanho do fluxo de ficheiros que está armazenado no disco. Os valores destas propriedades podem diferir por uma variedade de razões, tais como compressão, utilização de Deduplicação de Dados ou tiering em nuvem com Azure File Sync. Se um ficheiro for dimensionado para uma partilha de ficheiros Azure, o tamanho do disco é zero, porque o fluxo de ficheiros é armazenado na sua partilha de ficheiros Azure, e não no disco. Também é possível que um ficheiro seja parcialmente hierarquizado (ou parcialmente recolhido). Num ficheiro parcialmente hierárquico, parte do ficheiro está no disco. Isto pode ocorrer quando os ficheiros são parcialmente lidos por aplicações como jogadores multimédia ou serviços de zíper. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Como posso forçar um ficheiro ou diretório a ser hierárquico?

> [!NOTE]
> Quando seleciona um diretório a ser hierarquizado, apenas os ficheiros atualmente no diretório estão nivelados. Quaisquer ficheiros criados após esse tempo não são automaticamente nivelados.

Quando a função de tiering de nuvem está ativada, o tiering de nuvem tiering automaticamente camadas de ficheiros com base no último acesso e modifica os tempos para obter a percentagem de espaço livre de volume especificada no ponto final da nuvem. Às vezes, porém, talvez queira forçar manualmente um ficheiro a nivelar. Isto pode ser útil se guardar um ficheiro grande que não pretende utilizar novamente durante muito tempo, e pretende que o espaço livre no seu volume agora seja utilizado para outros ficheiros e pastas. Pode forçar o tiering utilizando os seguintes comandos PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Porque é que os meus ficheiros hierárquicos não mostram miniaturas ou pré-visualizações no Windows Explorer?
Para ficheiros hierárquicos, as miniaturas e as pré-visualizações não serão visíveis no ponto final do servidor. Este comportamento é esperado uma vez que a funcionalidade cache de miniatura no Windows ignora intencionalmente ficheiros de leitura com o atributo offline. Com o Cloud Tiering ativado, a leitura através de ficheiros hierárquicos faria com que fossem descarregados (recordados).

Este comportamento não é específico do Azure File Sync, o Windows Explorer apresenta um "X cinzento" para quaisquer ficheiros que tenham o conjunto de atributos offline. Verá o ícone X ao aceder a ficheiros sobre SMB. Para uma explicação detalhada deste comportamento, consulte [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)

<a id="afs-tiering-disabled"></a>
### <a name="i-have-cloud-tiering-disabled-why-are-there-tiered-files-in-the-server-endpoint-location"></a>Tenho camadas de nuvem desativadas, por que há ficheiros hierárquicos na localização do ponto final do servidor?

Existem duas razões pelas quais os ficheiros hierárquicos podem existir na localização do ponto final do servidor:

- Ao adicionar um novo ponto final do servidor a um grupo de sincronização existente, os metadados são primeiro sincronizados no servidor e os ficheiros são depois descarregados para o servidor em segundo plano. Os ficheiros serão apresentados como hierárquicos até serem descarregados localmente. Para melhorar o desempenho do download de ficheiros ao adicionar um novo servidor a um grupo de sincronização, utilize o cmdlet [Invoke-StorageSyncFileRecall.](storage-sync-cloud-tiering.md#afs-recall-file)

- Se o tiering da nuvem foi ativado no ponto final do servidor e depois desativado, os ficheiros permanecerão nivelados até serem acedidos.


## <a name="next-steps"></a>Passos Seguintes
* [Planeamento para uma implementação de sincronização de ficheiros Azure](storage-sync-files-planning.md)
