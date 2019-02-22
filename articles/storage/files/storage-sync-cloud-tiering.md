---
title: Noções básicas sobre o Azure File Sync em camada de Cloud | Documentos da Microsoft
description: Saiba mais sobre a funcionalidade do Azure File Sync em camada de Cloud
services: storage
author: sikoo
ms.service: storage
ms.topic: article
ms.date: 09/21/2018
ms.author: sikoo
ms.subservice: files
ms.openlocfilehash: fe363bd6d16d7beea1c8f1e6ec17710975a80924
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/21/2019
ms.locfileid: "56652565"
---
# <a name="cloud-tiering-overview"></a>Descrição geral de camadas da cloud
Na cloud em camadas são uma funcionalidade opcional do Azure File Sync em que frequentemente ficheiros acedidos são colocadas em cache localmente no servidor, enquanto todos os outros ficheiros são dispostos em camadas para ficheiros do Azure com base nas definições de política. Quando um ficheiro é em camadas, o filtro de sistema de ficheiros do Azure File Sync (StorageSync.sys) substitui o ficheiro localmente com um ponteiro, ou um ponto de reanálise. O ponto de reanálise representa um URL para o ficheiro nos ficheiros do Azure. Um ficheiro em camadas tem o atributo "offline" e o atributo FILE_ATTRIBUTE_RECALL_ON_DATA_ACCESS definido no NTFS para que aplicativos de terceiros com segurança podem identificar os ficheiros em camadas.
 
Quando um utilizador abre um ficheiro em camadas, o Azure File Sync solicitar forma totalmente integrada os dados de arquivo de ficheiros do Azure sem que o utilizador necessidade de saber que o arquivo é realmente armazenado no Azure. 
 
 > [!Important]  
 > Na cloud em camadas não é suportada para pontos de extremidade nos volumes de sistema do Windows server e apenas superiores a 64 KiB de tamanho de ficheiros podem ser colocado em camadas para ficheiros do Azure.
    
O Azure File Sync não suporta a disposição em camadas arquivos menores do que 64 KiB como a sobrecarga de desempenho de disposição em camadas e recupera um desses arquivos pequenos seria superam a economia de espaço.

 > [!Important]  
 > Recuperar ficheiros que tenham sido em camadas, a largura de banda de rede deve ser, pelo menos, 1 Mbps. Se a largura de banda de rede for inferior a 1 Mbps, arquivos podem não conseguir Lembre-se com um erro de tempo limite.

## <a name="cloud-tiering-faq"></a>FAQ de camadas da cloud

<a id="afs-cloud-tiering"></a>
### <a name="how-does-cloud-tiering-work"></a>Como trabalho camado da cloud?
O filtro de sistema de sincronização de ficheiros do Azure baseia-se um "mapa térmico" do espaço de nomes em cada ponto de final do servidor. Monitoriza acessos (leitura e operações de escrita) ao longo do tempo e, em seguida, com base na frequência e recency de acesso, atribui um térmico classificar a cada arquivo. Um arquivo acedido com frequência, que foi aberto recentemente será considerado como acesso frequente, ao passo que um ficheiro que é muito pouco tocadas e não tiver sido acedido por algum tempo será considerado muito legal. Quando o volume de ficheiros num servidor excede o limiar de espaço livre de volume que definir, ele será da camada os ficheiros mais interessantes para ficheiros do Azure até que a percentagem de espaço livre é cumprida.

Nas versões 4.0 e superior do agente do Azure File Sync, pode adicionalmente especificar uma política de data cada ponto de final de servidor que irá camada todos os arquivos não acessados ou modificados dentro de um número especificado de dias.

<a id="afs-volume-free-space"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work"></a>Como funciona a política de camadas de espaço livre de volume?
Espaço livre do volume é a quantidade de espaço livre que pretende reservar no volume onde se localiza um ponto final do servidor. Por exemplo, se o espaço livre do volume está definido como 20% num volume que tem um ponto final de servidor, cópia de segurança para 80% do espaço de volume irá ser ocupada pelos ficheiros acedidos mais recentemente, com todos os ficheiros restantes que não se encaixa nessa área em camadas no Azure. Espaço livre do volume aplica-se ao nível do volume, em vez de no nível de diretórios individuais ou grupos de sincronização. 

<a id="volume-free-space-fastdr"></a>
### <a name="how-does-the-volume-free-space-tiering-policy-work-with-regards-to-new-server-endpoints"></a>Como funciona a política de camadas de espaço livre de volume com a respeito de novos pontos de extremidade do servidor?
Quando um ponto de final de servidor recentemente é aprovisionado e está ligado a uma partilha de ficheiros do Azure, o servidor irá importar primeiro o espaço de nomes e, em seguida, irá fazer com os arquivos reais até atingir o limite de espaço livre do volume. Este processo também é conhecido como recuperação após desastre rápida ou de restauro rápido do espaço de nomes.

<a id="afs-effective-vfs"></a>
### <a name="how-is-volume-free-space-interpreted-when-i-have-multiple-server-endpoints-on-a-volume"></a>Como é que o espaço livre do volume interpretado quando tenho vários pontos de extremidade do servidor num volume?
Quando existe mais do que um ponto final de servidor num volume, o limite de espaço livre de volume em vigor é o maior espaço livre de volume especificado em qualquer ponto final do servidor nesse volume. Ficheiros serão dispostos em camadas, de acordo com seus padrões de utilização, independentemente de qual ponto final do servidor ao qual pertencem. Por exemplo, se tiver dois pontos de extremidade do servidor num volume, Endpoint1 e Endpoint2, onde Endpoint1 tem um limiar de espaço livre do volume de 25% e Endpoint2 tem um limiar de espaço livre do volume de 50%, o limite de espaço livre de volume para os dois pontos finais do servidor será 50%. 

<a id="date-tiering-policy"></a>
### <a name="how-does-the-date-tiering-policy-work-in-conjunction-with-the-volume-free-space-tiering-policy"></a>Como a política de camadas de data funciona em conjunto com o espaço livre de volume em camadas política? 
Ao ativar a colocação em camadas de um ponto final do servidor de nuvem, definir uma política de espaço livre de volume. Ele sempre terão precedência sobre quaisquer outras diretivas, incluindo a política de data. Opcionalmente, pode ativar uma data de política para cada ponto final do servidor em que volume, o que significa que apenas os ficheiros acedidos (isto é, de leitura ou escrita) dentro do intervalo de dias que esta política descreve será mantido local, com todos os ficheiros mais obsoletos em camadas. Lembre-se de que a política de espaço livre de volume sempre tem precedência e quando não existir espaço livre suficiente no volume para reter os dias de ficheiros, tal como descrito pela política de data, o Azure File Sync irá continuar a disposição em camadas os ficheiros mais frio até que o volume gratuito percentagem de espaço é cumprida.

Por exemplo, digamos que tem uma política de disposição em camadas baseado na data de 60 dias e uma política de espaço livre do volume de 20%. Se, depois de aplicar a política de data, há menos de 20% de espaço livre no volume, a política de espaço livre de volume irá surgir e substituir a política de data. Isso resultará em mais ficheiros em camadas, o que está a ser, de modo a que a quantidade de dados encontram-se no servidor de pode ser reduzida de 60 dias de dados para 45 dias. Por outro lado, esta política irá forçar a criação de camadas de ficheiros que estão fora do intervalo de tempo, mesmo que não atingiu o limiar de espaço livre – para que um ficheiro que é 61 dias será colocado em camadas, mesmo que o volume está vazio.

<a id="volume-free-space-guidelines"></a>
### <a name="how-do-i-determine-the-appropriate-amount-of-volume-free-space"></a>Como posso determinar a quantidade adequada de espaço livre do volume?
A quantidade de dados que deve ter locais é determinada pelo alguns fatores: a largura de banda, o padrão de acesso do seu conjunto de dados e o seu orçamento. Se tiver uma ligação de largura de banda baixa, convém manter mais dos seus dados locais para garantir que existe um atraso mínimo para os seus utilizadores. Caso contrário, pode baseá-la na taxa de abandono durante um determinado período. Por exemplo, se souber que cerca de 10% das suas alterações de conjunto de dados de 1 TB ou são ativamente acedidos a todos os meses,, em seguida, pode querer manter 100 GB local por isso, é não com freqüência recupera um ficheiros. Se o volume é de 2TB, então vai querer manter 5% (ou 100 GB) local, o que significa que os restantes 95% é a percentagem de espaço livre de volume. No entanto, recomendamos que adicione uma memória intermédia para levar em conta por períodos de alterações a superior – em outras palavras, começando com uma menor porcentagem de espaço livre de volume e, em seguida, ajustar-se for necessário mais tarde. 

Manter mais dados local significa que a redução dos custos de saída como serão possível resgatar os menos ficheiros do Azure, mas também requer uma quantidade maior de armazenamento no local, que tem seu próprio custo de manter. Assim que tiver uma instância do Azure File Sync implementado, pode examinar a saída de sua conta de armazenamento para aproximadamente avaliar se as configurações de espaço livre do volume são adequadas para a sua utilização. Supondo que a conta de armazenamento contém apenas o Azure ficheiro sincronização de ponto final da Cloud (ou seja, sua partilha de sincronização), em seguida, saída alta significa que muitos ficheiros estão a ser recuperados da cloud e deve considerar aumentar seu cache local.

<a id="how-long-until-my-files-tier"></a>
### <a name="ive-added-a-new-server-endpoint-how-long-until-my-files-on-this-server-tier"></a>Eu adicionei um novo ponto de final do servidor. O período de tempo até que meus arquivos este escalão de servidor?
Nas versões 4.0 e acima do agente do Azure File Sync, uma vez que os ficheiros foram carregados para a partilha de ficheiros do Azure, eles serão dispostos em camadas, de acordo com as políticas de logo que o próximo camadas sessão é executado, que ocorre uma vez por hora. Em agentes mais antigos, a disposição em camadas pode demorar até 24 horas a acontecer.

<a id="is-my-file-tiered"></a>
### <a name="how-can-i-tell-whether-a-file-has-been-tiered"></a>Como posso saber se um ficheiro tem sido em camadas?
Existem várias formas para verificar se um ficheiro tem sido em camadas para a partilha de ficheiros do Azure:
    
   *  **Verifique os atributos de ficheiro no ficheiro.**
     Com o botão direito num arquivo, aceda a **detalhes**e, em seguida, desloque para baixo até o **atributos** propriedade. Um ficheiro em camadas tem os seguintes atributos definido:     
        
        | Letra de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | A | Arquivo | Indica que o ficheiro deve ser feito pelo software de cópia de segurança. Este atributo é sempre definido, independentemente se o ficheiro está em camadas ou totalmente armazenado no disco. |
        | P | Ficheiro disperso | Indica que o ficheiro é um ficheiro disperso. Um ficheiro disperso é um tipo especializado de arquivos NTFS oferece para uma utilização eficaz quando o ficheiro no fluxo de disco é maioritariamente vazio. O Azure File Sync utiliza arquivos esparsos, porque um ficheiro é totalmente em camadas ou parcialmente recolhido. Num arquivo totalmente em camadas, a sequência de ficheiros é armazenada na cloud. Num arquivo parcialmente recolhido, que parte do arquivo já está no disco. Se um ficheiro é totalmente recuperados para o disco, Azure File Sync converte-o de um ficheiro disperso regulárním souborem. |
        | L | Ponto de reanálise | Indica que o ficheiro tem um ponto de reanálise. Um ponto de reanálise é um ponteiro especial para utilização por um filtro de sistema de ficheiros. O Azure File Sync utiliza pontos de reanálise para definir para o filtro de sistema de ficheiros do Azure File Sync (StorageSync.sys) a localização de cloud em que o ficheiro está armazenado. Isto suporta acesso totalmente integrado. Os utilizadores não precisam de saber que o Azure File Sync está sendo usado ou como obter acesso ao ficheiro na partilha de ficheiros do Azure. Quando um ficheiro é totalmente recuperado, o Azure File Sync remove o ponto de reanálise do ficheiro. |
        | O | Offline | Indica que alguns ou todos os conteúdos do ficheiro não são armazenados no disco. Quando um ficheiro é totalmente recuperado, o Azure File Sync remove este atributo. |

        ![A caixa de diálogo de propriedades de um arquivo, com o separador de detalhes selecionado](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
        Pode ver os atributos para todos os ficheiros numa pasta ao adicionar o **atributos** campo para a exibição de tabela do Explorador de ficheiros. Para tal, faça duplo clique numa coluna existente (por exemplo, **tamanho**), selecione **mais**e, em seguida, selecione **atributos** na lista pendente.
        
   * **Utilize `fsutil` para verificar a existência de pontos de reanálise num ficheiro.**
       Conforme descrito na opção anterior, um ficheiro em camadas sempre tem um conjunto de ponto de reanálise. Um ponteiro de reanálise é um ponteiro especial para o filtro de sistema de ficheiros do Azure File Sync (StorageSync.sys). Para verificar se um ficheiro tiver um ponto de reanálise, numa janela de Prompt de comando ou do PowerShell elevada, execute o `fsutil` utilitário:
    
        ```PowerShell
        fsutil reparsepoint query <your-file-name>
        ```

        Se o ficheiro tiver um ponto de reanálise, pode esperar ver **reanálise valor de etiqueta: 0x8000001e**. Este valor hexadecimal é o valor do ponto de reanálise que é propriedade de sincronização de ficheiros do Azure. A saída também contém os dados de reanálise que representa o caminho para o ficheiro na partilha de ficheiros do Azure.

        > [!WARNING]  
        > O `fsutil reparsepoint` comando utilitário também tem a capacidade de eliminar um ponto de reanálise. Não execute este comando, a menos que a equipa de engenharia do Azure File Sync pede para. Executar este comando pode resultar na perda de dados. 

<a id="afs-recall-file"></a>

### <a name="a-file-i-want-to-use-has-been-tiered-how-can-i-recall-the-file-to-disk-to-use-it-locally"></a>Um ficheiro que pretende utilizar tem sido em camadas. Como posso recuperar o arquivo em disco para utilizá-lo localmente?
A maneira mais fácil de recuperar um arquivo em disco é abrir o ficheiro. O filtro de sistema de ficheiros do Azure File Sync (StorageSync.sys) transfere o ficheiro de forma totalmente integrada a partir da partilha de ficheiros do Azure sem qualquer trabalho de sua parte. Para tipos de ficheiro que podem ser parcialmente leitura, tais como ficheiros de multimédia ou. zip, abrir um ficheiro não é transferido o arquivo inteiro.

Também pode utilizar o PowerShell para forçar um ficheiro a ser recolhido. Esta opção poderá ser útil se quiser Lembre-se vários ficheiros ao mesmo tempo, por exemplo, todos os ficheiros numa pasta. Abra uma sessão do PowerShell para o nó de servidor em que o Azure File Sync está instalado e, em seguida, execute os seguintes comandos do PowerShell:
    
    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncFileRecall -Path <file-or-directory-to-be-recalled>
    ```

<a id="sizeondisk-versus-size"></a>
### <a name="why-doesnt-the-size-on-disk-property-for-a-file-match-the-size-property-after-using-azure-file-sync"></a>Por que não a *tamanho no disco* propriedade uma correspondência de ficheiro para o *tamanho* propriedade depois de utilizar o Azure File Sync? 
Explorador de ficheiros do Windows expõe duas propriedades para representar o tamanho de um ficheiro: **Tamanho** e **tamanho no disco**. Estas propriedades uma diferença sutil na significado. **Tamanho** representa o tamanho total do ficheiro. **Tamanho no disco** representa o tamanho do fluxo de ficheiros que está armazenado no disco. Os valores para estas propriedades podem ser diferentes para diversas razões, tais como compressão, utilize da eliminação de duplicados de dados ou na cloud em camadas com o Azure File Sync. Se um ficheiro é em camadas para uma partilha de ficheiros do Azure, o tamanho no disco for zero, uma vez que a sequência de ficheiros é armazenada na partilha de ficheiros do Azure e não no disco. Também é possível para um arquivo seja parcialmente em camadas (ou parcialmente recolhido). Num arquivo parcialmente em camadas, parte do arquivo é efetuada no disco. Esta situação pode ocorrer quando os ficheiros parcialmente são lidas pelas aplicações, como leitores de multimídia ou zip utilitários. 

<a id="afs-force-tiering"></a>
### <a name="how-do-i-force-a-file-or-directory-to-be-tiered"></a>Como posso forçar um ficheiro ou diretório para ser colocado em camadas?
Quando a funcionalidade de camadas na cloud está ativada, disposição em camadas automaticamente os ficheiros de camadas de cloud com base no último acesso e modificar vezes para alcançar a percentagem de espaço livre de volume especificada no ponto final da cloud. Às vezes, no entanto, pode querer forçar manualmente um ficheiro a uma camada. Isso pode ser útil se salvar um arquivo grande que não pretende utilizar novamente durante muito tempo e pretende que o espaço livre no volume dos seus agora a utilizar para outros ficheiros e pastas. Pode forçar a disposição em camadas utilizando os seguintes comandos do PowerShell:

    ```PowerShell
    Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
    Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
    ```

## <a name="next-steps"></a>Próximos Passos
* [Planear uma implementação de sincronização de ficheiros do Azure](storage-sync-files-planning.md)
