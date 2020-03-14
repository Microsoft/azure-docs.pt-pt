---
title: Compreender o Tiering de Nuvem de Sincronização de Ficheiros Azure  Microsoft Docs
description: Conheça a funcionalidade cloud tiering do Azure File Sync
author: roygara
ms.service: storage
ms.topic: conceptual
ms.date: 09/21/2018
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: fea9cebc5199fc7c1fc5c081aa45f08044c21e44
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79268096"
---
# <a name="cloud-tiering-overview"></a>Visão geral do tiering da nuvem
O tiering em nuvem é uma funcionalidade opcional do Azure File Sync, no qual os ficheiros frequentemente acedidos são protegidos localmente no servidor, enquanto todos os outros ficheiros são tiered para Ficheiros Azure com base em definições de política. Quando um ficheiro é nivelado, o filtro de ficheiroS De ficheiros Azure Sync (StorageSync.sys) substitui o ficheiro localmente por um ponteiro ou ponto de reparse. O ponto de reparse representa um URL para o ficheiro em Ficheiros Azure. Um ficheiro hierárquico tem tanto o atributo "offline" como o FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS atributo definido no NTFS para que as aplicações de terceiros possam identificar de forma segura ficheiros hierárquicos.
 
Quando um utilizador abre um ficheiro hierárquico, o Azure File Sync recorda sem problemas os dados de ficheiros do Azure Files sem que o utilizador precise de saber que o ficheiro está armazenado no Azure. 
 
 > [!Important]  
 > O tiering em nuvem não é suportado para pontos finais do servidor nos volumes do sistema Windows, e apenas ficheiros superiores a 64 KiB em tamanho podem ser tiered para Ficheiros Azure.
    
O Azure File Sync não suporta ficheiros de tiering inferiores a 64 KiB, uma vez que o desempenho do tiering e a recordação de ficheiros tão pequenos superariam a poupança de espaço.

 > [!Important]  
 > Para recordar os ficheiros que foram nividados, a largura de banda da rede deve ser de pelo menos 1 Mbps. Se a largura de banda da rede for inferior a 1 Mbps, os ficheiros podem não conseguir recuperar com um erro de tempo limite.

## <a name="cloud-tiering-faq"></a>FAQ de nível de nuvem

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Como funciona o tiering de nuvem?
O filtro De sincronização de ficheiros Azure constrói um "mapa de calor" do seu espaço de nome em cada ponto final do servidor. Monitoriza os acessos (ler e escrever operações) ao longo do tempo e, em seguida, com base tanto na frequência como na recessão de acesso, atribui uma pontuação de calor a cada ficheiro. Um ficheiro frequentemente acedido que foi recentemente aberto será considerado quente, enquanto um ficheiro que mal é tocado e que não foi acedido há algum tempo será considerado legal. Quando o volume de ficheiros de um servidor exceder o limiar de espaço livre de volume que definiu, irá tierar os ficheiros mais fixes para Ficheiros Azure até que a sua percentagem de espaço livre seja satisfeita.

Nas versões 4.0 e acima do agente Dosindo de Ficheiros Azure, pode especificar adicionalmente uma política de data em cada ponto final do servidor que irá indicar quaisquer ficheiros não acedidos ou modificados dentro de um número determinado de dias.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Como funciona a política de tiering de espaço livre de volume?
Espaço livre de volume é a quantidade de espaço livre que deseja reservar no volume em que está localizado um ponto final do servidor. Por exemplo, se o espaço livre de volume for definido para 20% num volume que tenha um ponto final do servidor, até 80% do espaço de volume será ocupado pelos ficheiros mais recentemente acedidos, com quaisquer ficheiros restantes que não se encaixem neste espaço tiered até Azure. O espaço livre de volume aplica-se ao nível do volume e não ao nível de diretórios individuais ou grupos de sincronização. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Como funciona a política de tiering de espaço livre de volume no que diz respeito a novos pontos finais do servidor?
Quando um ponto final do servidor for recentemente aprovisionado e ligado a uma partilha de ficheiros Azure, o servidor irá primeiro puxar para baixo o espaço de nome e, em seguida, puxaros para baixo os ficheiros reais até atingir o seu limiar de espaço livre de volume. Este processo também é conhecido como rápida recuperação de desastres ou restauro rápido do espaço de nome.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Como é interpretado o espaço livre de volume quando tenho vários pontos finais do servidor num volume?
Quando há mais de um ponto final do servidor num volume, o limiar de espaço livre de volume eficaz é o maior espaço livre de volume especificado em qualquer ponto final do servidor nesse volume. Os ficheiros serão nividamente de acordo com os seus padrões de utilização, independentemente do ponto final do servidor a que pertencem. Por exemplo, se tiver dois pontos finais do servidor num volume, Endpoint1 e Endpoint2, onde endpoint1 tem um limiar de espaço livre de volume de 25% e Endpoint2 tem um limiar de espaço livre de volume de 50%, o limiar de espaço livre de volume para ambos os pontos finais do servidor será de 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Como funciona a política de tiering de datas em conjunto com a política de tiering de espaço livre de volume? 
Ao ativar o tiering da nuvem num ponto final do servidor, definiu uma política de espaço livre de volume. Tem sempre precedência sobre quaisquer outras políticas, incluindo a política de datas. Opcionalmente, pode ativar uma política de data para cada ponto final do servidor nesse volume, o que significa que apenas os ficheiros acedidos (isto é, lidos ou escritos) dentro do intervalo de dias que esta política descreve serão mantidos locais, com quaisquer ficheiros mais estalados nidificados. Tenha em mente que a política de espaço livre de volume sempre tem precedência, e quando não há espaço livre suficiente no volume para reter tantos dias de ficheiros como descrito pela política de data, o Azure File Sync continuará a tiering os ficheiros mais frios até que o volume seja livre a percentagem de espaço é cumprida.

Por exemplo, digamos que tem uma política de tiering baseada em datas de 60 dias e uma política de espaço livre de volume de 20%. Se, após a aplicação da política de data, houver menos de 20% de espaço livre no volume, a política de espaço livre de volume sairá em vigor e anulará a política de data. Isto resultará na sua enumeração de mais ficheiros, de modo a que a quantidade de dados mantidos no servidor possa ser reduzida de 60 dias de dados para 45 dias. Inversamente, esta política vai forçar o tiering de ficheiros que caem fora do seu intervalo de tempo, mesmo que não tenha atingido o seu limite de espaço livre – por isso, um ficheiro com 61 dias será nivelado mesmo que o seu volume esteja vazio.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Como determino a quantidade adequada de espaço livre de volume?
A quantidade de dados que deve manter local é determinada por alguns fatores: a largura de banda, o padrão de acesso do seu conjunto de dados e o seu orçamento. Se tiver uma ligação de baixa largura de banda, poderá querer manter mais dos seus dados locais para garantir que há um mínimo de atraso para os seus utilizadores. Caso contrário, pode baseá-lo na taxa de churn durante um determinado período. Por exemplo, se souber que cerca de 10% dos seus 1 TB altera mato de dados ou é ativamente acedido todos os meses, então pode querer manter 100 GB locais para que não esteja a recordar frequentemente ficheiros. Se o seu volume for de 2TB, então você vai querer manter 5% (ou 100 GB) local, o que significa que os restantes 95% são a sua percentagem de espaço livre de volume. No entanto, recomendamos que adicione um tampão para explicar períodos de maior agitação – ou seja, começando com uma percentagem de espaço livre de menor volume e, em seguida, ajustando-o se necessário mais tarde. 

Manter mais dados locais significa custos de saída mais baixos, uma vez que menos ficheiros serão recolhidos do Azure, mas também exige que mantenha uma maior quantidade de armazenamento no local, o que vem a seu custo próprio. Uma vez implementado um exemplo de Sincronização de Ficheiros Azure, pode ver a saída da sua conta de armazenamento para avaliar aproximadamente se as definições de espaço sem volume são adequadas para a sua utilização. Assumindo que a conta de armazenamento contém apenas o seu Ponto Final de Nuvem de Sincronização de Ficheiros Azure (isto é, a sua parte de sincronização), então a alta saída significa que muitos ficheiros estão a ser recolhidos da nuvem, e deve considerar aumentar a sua cache local.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Adicionei um novo ponto final do servidor. Quanto tempo até os meus ficheiros neste servidor?
Nas versões 4.0 e superior esgote do agente Dosindo de Ficheiros Azure, uma vez que os seus ficheiros tenham sido enviados para a partilha de ficheiros Azure, serão nivelado de acordo com as suas políticas assim que a próxima sessão de tiering for publicada, o que acontece uma vez por hora. Em agentes mais velhos, o tiering pode levar até 24 horas para acontecer.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Como posso saber se um ficheiro foi niveto?
Existem várias formas de verificar se um ficheiro foi tiered para a sua parte de ficheiro Azure:
    
   *  **Verifique os atributos do ficheiro no ficheiro.**
     Clique à direita num ficheiro, vá a **Detalhes**e, em seguida, desloque-se para a propriedade **Atributos.** Um ficheiro hierárquico tem os seguintes atributos definidos:     
        
        | Carta de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | A | Arquivo | Indica que o ficheiro deve ser apoiado por um software de reserva. Este atributo é sempre definido, independentemente de o ficheiro estar nivelado ou armazenado totalmente no disco. |
        | P | Arquivo escasso | Indica que o ficheiro é um ficheiro escasso. Um ficheiro escasso é um tipo de ficheiro especializado que a NTFS oferece para uma utilização eficiente quando o ficheiro no fluxo de disco está praticamente vazio. O Azure File Sync utiliza ficheiros escassos porque um ficheiro é totalmente hierárquico ou parcialmente recuperado. Num ficheiro totalmente niveado, o fluxo de ficheiros é armazenado na nuvem. Num ficheiro parcialmente recordado, essa parte do ficheiro já está no disco. Se um ficheiro for totalmente recolhido para o disco, o Azure File Sync converte-o de um ficheiro escasso para um ficheiro regular. Este atributo só está definido no Windows Server 2016 e mais antigo.|
        | M | Recolha no acesso aos dados | Indica que os dados do ficheiro não estão totalmente presentes no armazenamento local. Ler o ficheiro fará com que pelo menos parte do conteúdo do ficheiro seja recolhido a partir de uma partilha de ficheiros Azure à qual o ponto final do servidor está ligado. Este atributo está apenas definido no Windows Server 2019. |
        | L | Ponto reparse | Indica que o ficheiro tem um ponto de reparse. Um ponto de reparse é um ponteiro especial para ser usado por um filtro de sistema de ficheiros. O Azure File Sync utiliza pontos reparse para definir para o filtro de ficheiros Do Ficheiro Azure Sync (StorageSync.sys) a localização da nuvem onde o ficheiro está armazenado. Isto suporta um acesso perfeito. Os utilizadores não precisarão de saber que o Azure File Sync está a ser utilizado ou como aceder ao ficheiro na sua partilha de ficheiros Azure. Quando um ficheiro é totalmente recuperado, o Azure File Sync remove o ponto de reparse do ficheiro. |
        | O | Banda | Indica que parte ou a toda a conteúdo do ficheiro não está armazenado no disco. Quando um ficheiro é totalmente recolhido, o Azure File Sync remove este atributo. |

        ![A caixa de diálogo Properties para um ficheiro, com o separador Detalhes selecionado](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Pode ver os atributos de todos os ficheiros numa pasta adicionando o campo **Atributos** ao ecrã da tabela do File Explorer. Para isso, clique à direita numa coluna existente (por exemplo, **Tamanho),** selecione **Mais**, e, em seguida, selecione **Atributos** da lista de drop-down.
        
   * **Utilize `fsutil` para verificar se há pontos de reparse num ficheiro.**
       Tal como descrito na opção anterior, um ficheiro hierárquico tem sempre um conjunto de pontos reparso. Um ponteiro reparse é um ponteiro especial para o filtro de ficheiros Do Ficheiro Sincron (StorageSync.sys). Para verificar se um ficheiro tem um ponto de reparso, numa janela elevada de Comando Prompt ou PowerShell, executar o utilitário `fsutil`:
    
        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o ficheiro tiver um ponto de reparse, pode esperar ver **Reparse Tag Value: 0x80000001e**. Este valor hexadecimal é o valor de ponto reparse que é propriedade da Azure File Sync. A saída também contém os dados reparsos que representam o caminho para o seu ficheiro na sua partilha de ficheiros Azure.

        > [!WARNING]  
        > O comando de utilidade `fsutil reparsepoint` também tem a capacidade de apagar um ponto de reparse. Não execute este comando a menos que a equipa de engenharia Azure File Sync lhe peça. Executar este comando pode resultar em perda de dados. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Um ficheiro que quero usar foi nivelamento. Como posso lembrar o ficheiro para o disco para usá-lo localmente?
A maneira mais fácil de recordar um ficheiro para o disco é abrir o ficheiro. O filtro de ficheiros Azure File Sync (StorageSync.sys) descarrega sem problemas o ficheiro a partir da sua partilha de ficheiros Azure sem qualquer trabalho da sua parte. Para tipos de ficheiros que possam ser parcialmente lidos, tais como ficheiros multimédia ou .zip, a abertura de um ficheiro não descarrega todo o ficheiro.

Também pode usar o PowerShell para forçar um ficheiro a ser recolhido. Esta opção pode ser útil se pretender recordar vários ficheiros ao mesmo tempo, como todos os ficheiros de uma pasta. Abra uma sessão PowerShell no nó do servidor onde o Azure File Sync está instalado e, em seguida, execute os seguintes comandos PowerShell:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Parâmetros opcionais:
* `-Order CloudTieringPolicy` recordará primeiro os ficheiros mais recentemente modificados.  
* `-ThreadCount` determina quantos ficheiros podem ser recolhidos em paralelo.
* `-PerFileRetryCount`determina com que frequência será tentada uma retirada de um ficheiro que está atualmente bloqueado.
* `-PerFileRetryDelaySeconds`determina o tempo em segundos entre a tentativa de relembrar as tentativas e deve ser sempre utilizado em combinação com o parâmetro anterior.

> [!Note]  
> Se o volume local que hospeda o servidor não tiver espaço livre suficiente para recordar todos os dados hierárquicos, o `Invoke-StorageSyncFileRecall` cmdlet falha.  

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Porque é que o tamanho na propriedade *do disco* para um ficheiro corresponde à propriedade *Size* depois de usar o Azure File Sync? 
O Windows File Explorer expõe duas propriedades para representar o tamanho de um ficheiro: **Tamanho** e **Tamanho no disco**. Estas propriedades diferem subtilmente no significado. **O tamanho** representa o tamanho total do ficheiro. **O tamanho do disco** representa o tamanho do fluxo de ficheiros que está armazenado no disco. Os valores destas propriedades podem diferir por uma variedade de razões, tais como compressão, utilização de Deduplicação de Dados ou tiering em nuvem com O Sincronizado de Ficheiros Azure. Se um ficheiro for tiered para uma partilha de ficheiros Azure, o tamanho do disco é zero, porque o fluxo de ficheiros é armazenado na sua partilha de ficheiros Azure, e não no disco. Também é possível que um ficheiro seja parcialmente nivelado (ou parcialmente recordado). Num ficheiro parcialmente hierárquico, parte do ficheiro está no disco. Isto pode ocorrer quando os ficheiros são parcialmente lidos por aplicações como jogadores multimédia ou utilitários zip. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Como posso forçar um ficheiro ou diretório a ser niveado?
Quando a função de tiering de nuvem está ativada, o tiering da nuvem automaticamente nidifica ficheiros com base no último acesso e modifica os tempos para atingir a percentagem de espaço livre de volume especificada no ponto final da nuvem. Às vezes, porém, é melhor forçar manualmente um ficheiro a nível. Isto pode ser útil se guardar um ficheiro grande que não pretende utilizar novamente durante muito tempo, e deseja que o espaço livre no seu volume agora seja utilizado para outros ficheiros e pastas. Pode forçar o tiering utilizando os seguintes comandos PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

<a id="afs-image-thumbnail"></a>
### <a name="why-are-my-tiered-files-not-showing-thumbnails-or-previews-in-windows-explorer"></a>Porque é que os meus ficheiros hierárquicos não mostram miniaturas ou pré-visualizações no Windows Explorer?
Para ficheiros, miniaturas e pré-visualizações não serão visíveis no ponto final do servidor. Este comportamento é esperado uma vez que a funcionalidade de cache de miniatura no Windows ignora intencionalmente ficheiros de leitura com o atributo offline. Com o Cloud Tiering ativado, a leitura através de ficheiros hierárquicos faria com que fossem descarregados (recordados).

Este comportamento não é específico do Azure File Sync, o Windows Explorer apresenta um "X cinzento" para quaisquer ficheiros que tenham o conjunto de atributooffline. Verá o ícone X ao aceder a ficheiros sobre SMB. Para uma explicação detalhada deste comportamento, consulte [https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105](https://blogs.msdn.microsoft.com/oldnewthing/20170503-00/?p=96105)


## <a name="next-steps"></a>Passos Seguintes
* [Planejamento de uma implantação de sincronização de ficheiros Azure](storage-sync-files-planning.md)
