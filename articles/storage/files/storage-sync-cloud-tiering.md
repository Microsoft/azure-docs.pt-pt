---
title: Entendendo Sincronização de Arquivos do Azure camadas de nuvem | Microsoft Docs
description: Saiba mais sobre a disposição em camadas da nuvem de recursos do Sincronização de Arquivos do Azure
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 36b09ce8ece010ff24345ddb96654f75542cc9a5
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098952"
---
# <a name="cloud-tiering-overview"></a>Visão geral de camadas de nuvem
A camada de nuvem é um recurso opcional do Sincronização de Arquivos do Azure em que os arquivos acessados com frequência são armazenados em cache localmente no servidor, enquanto todos os outros arquivos estão em camadas para os arquivos do Azure com base nas configurações de política. Quando um arquivo está em camadas, o filtro do sistema de arquivos Sincronização de Arquivos do Azure (StorageSync. sys) substitui o arquivo localmente por um ponteiro ou ponto de nova análise. O ponto de nova análise representa uma URL para o arquivo nos arquivos do Azure. Um arquivo em camadas tem o atributo "offline" e o atributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definidos no NTFS para que os aplicativos de terceiros possam identificar com segurança os arquivos em camadas.
 
Quando um usuário abre um arquivo em camadas, Sincronização de Arquivos do Azure rechama diretamente os dados de arquivo dos arquivos do Azure sem que o usuário precise saber que o arquivo está realmente armazenado no Azure. 
 
 > [!Important]  
 > Não há suporte para camadas de nuvem para pontos de extremidade de servidor nos volumes de sistema do Windows e somente arquivos maiores que 64 KiB podem ser em camadas para os arquivos do Azure.
    
O Sincronização de Arquivos do Azure não dá suporte a arquivos de camadas menores que 64 KiB, pois a sobrecarga de desempenho de camadas e o cancelamento desses arquivos pequenos superaria a economia de espaço.

 > [!Important]  
 > Para recuperar os arquivos que foram em camadas, a largura de banda da rede deve ser de pelo menos 1 Mbps. Se a largura de banda da rede for menor que 1 Mbps, os arquivos poderão falhar ao se recuperar com um erro de tempo limite.

## <a name="cloud-tiering-faq"></a>Perguntas frequentes sobre camadas de nuvem

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Como funciona a disposição em camadas de nuvem?
O filtro do sistema Sincronização de Arquivos do Azure cria um "calor" de seu namespace em cada ponto de extremidade do servidor. Ele monitora os acessos (operações de leitura e gravação) ao longo do tempo e, em seguida, com base na frequência e recency de acesso, atribui uma pontuação de calor a cada arquivo. Um arquivo acessado com frequência que foi aberto recentemente será considerado quente, enquanto que um arquivo que é mal tocado e não foi acessado por algum tempo será considerado legal. Quando o volume do arquivo em um servidor excede o limite de espaço livre do volume definido, ele hierarquizará os arquivos mais interessantes para os arquivos do Azure até que a porcentagem de espaço livre seja atendida.

Nas versões 4,0 e acima do agente de Sincronização de Arquivos do Azure, você também pode especificar uma política de data em cada ponto de extremidade do servidor que irá hierarquizar os arquivos não acessados ou modificados dentro de um número de dias especificado.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Como funciona a política de camadas de espaço livre do volume?
Espaço livre no volume é a quantidade de espaço livre que você deseja reservar no volume no qual um ponto de extremidade do servidor está localizado. Por exemplo, se o espaço livre do volume for definido como 20% em um volume que tenha um ponto de extremidade do servidor, até 80% do espaço do volume será ocupado pelos arquivos acessados mais recentemente, com os arquivos restantes que não se ajustam a esse espaço em camadas até o Azure. O espaço livre do volume se aplica no nível do volume, e não no nível de diretórios individuais ou grupos de sincronização. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Como a política de camadas de espaço livre do volume funciona com relação aos novos pontos de extremidade do servidor?
Quando um ponto de extremidade do servidor é provisionado recentemente e conectado a um compartilhamento de arquivos do Azure, o servidor primeiro recebe o namespace e, em seguida, efetua pull dos arquivos reais até atingir seu limite de espaço livre no volume. Esse processo também é conhecido como recuperação rápida de desastre ou restauração rápida de namespace.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Como o espaço livre no volume é interpretado quando tenho vários pontos de extremidade do servidor em um volume?
Quando há mais de um ponto de extremidade de servidor em um volume, o limite de espaço livre do volume efetivo é o maior espaço livre do volume especificado em qualquer ponto de extremidade do servidor nesse volume. Os arquivos serão em camadas de acordo com seus padrões de uso, independentemente do ponto de extremidade do servidor ao qual pertencem. Por exemplo, se você tiver dois pontos de extremidade de servidor em um volume, Endpoint1 e endpoint2, em que Endpoint1 tem um limite de espaço livre de volume de 25% e endpoint2 tem um limite de espaço livre em volume de 50%, o limite de espaço livre do volume para ambos os pontos de extremidade do servidor será 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Como a política de camadas de data funciona em conjunto com a política de camadas de espaço livre do volume? 
Ao habilitar a camada de nuvem em um ponto de extremidade do servidor, você define uma política de espaço livre no volume. Ele sempre tem precedência sobre quaisquer outras políticas, incluindo a política de data. Opcionalmente, você pode habilitar uma política de data para cada ponto de extremidade do servidor nesse volume, o que significa que somente os arquivos acessados (ou seja, lidos ou gravados) dentro do intervalo de dias que essa política descreve será mantida local, com todos os arquivos obsoletos em camadas. Lembre-se de que a política de espaço livre do volume sempre tem precedência e quando não há espaço livre suficiente no volume para reter o máximo de dias de arquivos, conforme descrito pela política de data, Sincronização de Arquivos do Azure continuará a colocar os arquivos mais frios em camadas até que o volume fique livre porcentagem de espaço atendida.

Por exemplo, digamos que você tenha uma política de camadas baseada em data de 60 dias e uma política de espaço livre em volume de 20%. Se, depois de aplicar a política de data, houver menos de 20% de espaço livre no volume, a política de espaço livre do volume será ativada e substituirá a política de data. Isso fará com que mais arquivos estejam em camadas, de modo que a quantidade de dados mantidos no servidor pode ser reduzida de 60 dias de dados para 45 dias. Por outro lado, essa política forçará a camada de arquivos que ficam fora do intervalo de tempo, mesmo que você não tenha atingido o limite de espaço livre – portanto, um arquivo com 61 dias de idade será colocado em camadas, mesmo que o volume esteja vazio.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Como fazer determinar a quantidade apropriada de espaço livre no volume?
A quantidade de dados que você deve manter local é determinada por alguns fatores: sua largura de banda, o padrão de acesso do seu conjunto e seu orçamento. Se você tiver uma conexão de baixa largura de banda, talvez queira manter mais de seus dados locais para garantir que haja um atraso mínimo para os usuários. Caso contrário, você pode baseá-lo na taxa de rotatividade durante um determinado período. Por exemplo, se você souber que cerca de 10% das suas alterações de conjunto de informações de 1 TB ou que são acessadas ativamente por mês, convém manter o local de 100 GB para que você não esteja rechamando os arquivos com frequência. Se o volume for de 2 TB, você desejará manter o local de 5% (ou 100 GB), o que significa que o percentual restante de 95% é a porcentagem de espaço livre do volume. No entanto, recomendamos que você adicione um buffer para considerar períodos de variação mais alta – em outras palavras, começando com uma porcentagem de espaço livre de volume inferior e, em seguida, ajustando-o se necessário mais tarde. 

Manter mais dados locais significa custos de egresso menores, pois menos arquivos serão rechamados do Azure, mas também exigirão que você mantenha uma quantidade maior de armazenamento local, que tem seu próprio custo. Depois de ter uma instância do Sincronização de Arquivos do Azure implantada, você pode examinar a saída da sua conta de armazenamento para medir aproximadamente se as configurações de espaço livre do volume são apropriadas para seu uso. Supondo que a conta de armazenamento contenha apenas seu ponto de extremidade de nuvem Sincronização de Arquivos do Azure (ou seja, seu compartilhamento de sincronização), a alta saída significa que muitos arquivos estão sendo recuperados da nuvem e você deve considerar aumentar o cache local.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Adicionei um novo ponto de extremidade do servidor. Quanto tempo até meus arquivos nesta camada de servidor?
Nas versões 4,0 e acima do agente de Sincronização de Arquivos do Azure, depois que os arquivos forem carregados no compartilhamento de arquivos do Azure, eles serão em camadas de acordo com suas políticas assim que a próxima sessão de camadas for executada, o que acontece uma vez por hora. Em agentes mais antigos, a camada pode levar até 24 horas para acontecer.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Como posso saber se um arquivo foi colocado em camadas?
Há várias maneiras de verificar se um arquivo foi colocado em camadas em seu compartilhamento de arquivos do Azure:
    
   *  **Verifique os atributos do arquivo no arquivo.**
     Clique com o botão direito do mouse em um arquivo, vá para **detalhes**e role para baixo até a propriedade **atributos** . Um arquivo em camadas tem os seguintes atributos definidos:     
        
        | Letra do atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | A | Arquivo | Indica que o backup do arquivo deve ser feito pelo software de backup. Esse atributo é sempre definido, independentemente de o arquivo estar em camadas ou armazenado totalmente no disco. |
        | P | Arquivo esparso | Indica que o arquivo é um arquivo esparso. Um arquivo esparso é um tipo especializado de arquivo que o NTFS oferece para uso eficiente quando o arquivo no fluxo de disco está praticamente vazio. Sincronização de Arquivos do Azure usa arquivos esparsos porque um arquivo é totalmente em camadas ou parcialmente rechamado. Em um arquivo totalmente em camadas, o fluxo de arquivos é armazenado na nuvem. Em um arquivo parcialmente rechamado, essa parte do arquivo já está no disco. Se um arquivo for totalmente rechamado para o disco, Sincronização de Arquivos do Azure o converterá de um arquivo esparso em um arquivo regular. |
        | B | Ponto de nova análise | Indica que o arquivo tem um ponto de nova análise. Um ponto de nova análise é um ponteiro especial para uso por um filtro do sistema de arquivos. Sincronização de Arquivos do Azure usa pontos de nova análise para definir para o filtro do sistema de arquivos Sincronização de Arquivos do Azure (StorageSync. sys) o local da nuvem onde o arquivo está armazenado. Isso dá suporte ao acesso contínuo. Os usuários não precisarão saber que Sincronização de Arquivos do Azure está sendo usado ou como obter acesso ao arquivo em seu compartilhamento de arquivos do Azure. Quando um arquivo é totalmente rechamado, Sincronização de Arquivos do Azure remove o ponto de nova análise do arquivo. |
        | O | Offline | Indica que parte ou todo o conteúdo do arquivo não está armazenado no disco. Quando um arquivo é totalmente rechamado, Sincronização de Arquivos do Azure remove esse atributo. |

        ![A caixa de diálogo Propriedades de um arquivo, com a guia detalhes selecionada](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Você pode ver os atributos de todos os arquivos em uma pasta adicionando o campo **atributos** à exibição de tabela do explorador de arquivos. Para fazer isso, clique com o botão direito do mouse em uma coluna existente (por exemplo, **tamanho**), selecione **mais**e, em seguida, selecione **atributos** na lista suspensa.
        
   * **Use `fsutil` para verificar se há pontos de nova análise em um arquivo.**
       Conforme descrito na opção anterior, um arquivo em camadas sempre tem um conjunto de pontos de nova análise. Um ponteiro de nova análise é um ponteiro especial para o Sincronização de Arquivos do Azure filtro do sistema de arquivos (StorageSync. sys). Para verificar se um arquivo tem um ponto de nova análise, em um prompt de comando com privilégios elevados ou na janela `fsutil` do PowerShell, execute o utilitário:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o arquivo tiver um ponto de nova análise, você poderá esperar ver **o valor da marca de nova análise: 0x8000001e**. Esse valor hexadecimal é o valor do ponto de nova análise que pertence ao Sincronização de Arquivos do Azure. A saída também contém os dados de nova análise que representam o caminho para o arquivo no compartilhamento de arquivos do Azure.

        > [!WARNING]  
        > O `fsutil reparsepoint` comando do utilitário também tem a capacidade de excluir um ponto de nova análise. Não execute esse comando a menos que a equipe de engenharia de Sincronização de Arquivos do Azure solicite a você. A execução desse comando pode resultar em perda de dados. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Um arquivo que desejo usar foi colocado em camadas. Como posso recuperar o arquivo no disco para usá-lo localmente?
A maneira mais fácil de lembrar um arquivo para o disco é abrir o arquivo. O filtro do sistema de arquivos Sincronização de Arquivos do Azure (StorageSync. sys) baixa diretamente o arquivo do compartilhamento de arquivos do Azure sem qualquer trabalho de sua parte. Para tipos de arquivo que podem ser parcialmente lidos, como arquivos multimídia ou. zip, abrir um arquivo não baixa o arquivo inteiro.

Você também pode usar o PowerShell para forçar a rechamada de um arquivo. Essa opção pode ser útil se você quiser rechamar vários arquivos de uma vez, como todos os arquivos em uma pasta. Abra uma sessão do PowerShell para o nó do servidor em que o Sincronização de Arquivos do Azure está instalado e execute os seguintes comandos do PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Por que o *tamanho na* Propriedade do disco para um arquivo corresponde à propriedade *size* depois de usar sincronização de arquivos do Azure? 
O explorador de arquivos do Windows expõe duas propriedades para representar o tamanho de um arquivo: **Tamanho** e **tamanho em disco**. Essas propriedades diferem sutilmente no significado. **Tamanho** representa o tamanho completo do arquivo. **Tamanho em disco** representa o tamanho do fluxo de arquivos armazenado no disco. Os valores dessas propriedades podem diferir por vários motivos, como compactação, uso de eliminação de duplicação de dados ou camadas de nuvem com Sincronização de Arquivos do Azure. Se um arquivo estiver em camadas para um compartilhamento de arquivos do Azure, o tamanho no disco será zero, pois o fluxo de arquivos será armazenado no compartilhamento de arquivos do Azure e não no disco. Também é possível que um arquivo seja parcialmente em camadas (ou parcialmente rechamado). Em um arquivo parcialmente em camadas, parte do arquivo está no disco. Isso pode ocorrer quando os arquivos são lidos parcialmente por aplicativos como players de multimídia ou utilitários de zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Como fazer forçar um arquivo ou diretório a ser colocado em camadas?
Quando o recurso de camadas de nuvem está habilitado, o nivelamento de nuvem automaticamente hierarquiza os arquivos com base no último acesso e modifica as horas para atingir a porcentagem de espaço livre do volume especificada no ponto de extremidade da nuvem. No entanto, às vezes, talvez você queira forçar manualmente um arquivo para uma camada. Isso pode ser útil se você salvar um arquivo grande que você não pretende usar novamente por muito tempo e deseja que o espaço livre em seu volume agora seja usado para outros arquivos e pastas. Você pode forçar o nivelamento usando os seguintes comandos do PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="next-steps"></a>Próximos Passos
* [Planejando uma implantação de Sincronização de Arquivos do Azure](storage-sync-files-planning.md)
