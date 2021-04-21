---
title: Como gerir ficheiros tiered Azure File Sync | Microsoft Docs
description: Dicas e comandos PowerShell para ajudá-lo a gerir ficheiros hierárquicos
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd740c773998450ef6e8bb95c4df3a1abadaceed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107797128"
---
# <a name="how-to-manage-tiered-files"></a>Como gerir ficheiros hierárquicos

Este artigo fornece orientações para utilizadores que tenham dúvidas relacionadas com a gestão de ficheiros hierárquicos. Para questões conceptuais sobre o tiering de nuvens, consulte [a Azure Files FAQ](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json).

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Como verificar se os seus ficheiros estão a ser hierarquizados

Se os ficheiros precisam ou não de ser nivelados por políticas definidas é avaliado uma vez por hora. Pode encontrar duas situações quando um novo ponto final do servidor é criado:

Quando adiciona pela primeira vez um novo ponto final do servidor, muitas vezes existem ficheiros nessa localização do servidor. Têm de ser carregados antes que o nível da nuvem possa começar. A política de espaço livre de volume não começará o seu trabalho até que o carregamento inicial de todos os ficheiros tenha terminado. No entanto, a política de datas opcionais começará a funcionar numa base de ficheiro individual, assim que um ficheiro tiver sido carregado. O intervalo de uma hora também se aplica aqui. 

Quando adiciona um novo ponto final do servidor, é possível que tenha ligado uma localização vazia do servidor a uma partilha de ficheiros Azure com os seus dados nele. Se optar por descarregar o espaço de nomes e recordar o conteúdo durante o download inicial para o seu servidor, depois de o espaço de nome sair, os ficheiros serão recolhidos com base no último timetamp modificado até que a política de espaço livre de volume e os limites de política de data opcional sejam atingidos.

Existem várias formas de verificar se um ficheiro foi ligado à sua partilha de ficheiros Azure:
    
   *  **Verifique os atributos do ficheiro no ficheiro.**
     Clique com o direito num ficheiro, vá a **Detalhes** e, em seguida, desloque-se para a propriedade **Atributos.** Um ficheiro hierárquico tem os seguintes atributos definidos:     
        
        | Carta de atributo | Atributo | Definição |
        |:----------------:|-----------|------------|
        | A | Arquivo | Indica que o ficheiro deve ser apoiado por software de backup. Este atributo é sempre definido, independentemente de o ficheiro ser hierárquico ou armazenado totalmente no disco. |
        | P | Arquivo escasso | Indica que o ficheiro é um ficheiro escasso. Um ficheiro escasso é um tipo de ficheiro especializado que o NTFS oferece para uma utilização eficiente quando o ficheiro na corrente do disco está praticamente vazio. O Azure File Sync utiliza ficheiros escassos porque um ficheiro é totalmente hierarquizado ou parcialmente recolhido. Num ficheiro totalmente hierárquico, o fluxo de ficheiros é armazenado na nuvem. Num ficheiro parcialmente recolhido, essa parte do ficheiro já está no disco. Isto pode ocorrer quando os ficheiros são parcialmente lidos por aplicações como jogadores multimédia ou serviços de zíper. Se um ficheiro for totalmente recolhido para o disco, o Azure File Sync converte-o de um ficheiro escasso para um ficheiro regular. Este atributo só está definido no Windows Server 2016 ou mais antigo.|
        | M | Lembre-se no acesso aos dados | Indica que os dados do ficheiro não estão totalmente presentes no armazenamento local. A leitura do ficheiro fará com que pelo menos parte do conteúdo do ficheiro seja recolhido a partir de uma partilha de ficheiros Azure à qual o ponto final do servidor está ligado. Este atributo só está definido no Windows Server 2019. |
        | L | Ponto de reanálise | Indica que o ficheiro tem um ponto de reparse. Um ponto de reparse é um ponteiro especial para utilização por um filtro de sistema de ficheiros. O Azure File Sync utiliza pontos de re-reserva para definir para o filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) a localização da nuvem onde o ficheiro está armazenado. Isto suporta um acesso sem emenda. Os utilizadores não precisarão de saber que o Azure File Sync está a ser utilizado ou como obter acesso ao ficheiro na sua partilha de ficheiros Azure. Quando um ficheiro é totalmente recolhido, o Azure File Sync remove o ponto de reparse do ficheiro. |
        | O | Offline | Indica que parte ou todo o conteúdo do ficheiro não é armazenado no disco. Quando um ficheiro é totalmente recolhido, o Azure File Sync remove este atributo. |

        ![A caixa de diálogo Properties para um ficheiro, com o separador Detalhes selecionado](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > Pode ver os atributos de todos os ficheiros numa pasta adicionando o campo **Atributos** à exibição da tabela do Explorador de Ficheiros. Para isso, clique com o botão direito numa coluna existente (por exemplo, **Tamanho),** selecione **Mais** e, em seguida, selecione **Atributos** da lista de drop-down.
        
        > [!NOTE]
        > Todos estes atributos serão visíveis para ficheiros parcialmente recolhidos também.
        
   * **Utilize `fsutil` para verificar se há pontos de reparse num ficheiro.**
       Como descrito na opção anterior, um ficheiro hierárquico tem sempre um conjunto de pontos de reparse. Um ponto de reparse permite ao controlador de filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) recuperar conteúdo de ações de ficheiros Azure que não são armazenadas localmente no servidor. 

       Para verificar se um ficheiro tem um ponto de reparse, numa janela de comando elevada ou powerShell, verifique o `fsutil` utilitário:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Se o ficheiro tiver um ponto de reparse, pode esperar ver **Reparse Tag Value: 0x8000001e**. Este valor hexadecimal é o valor de ponto de reparse que é propriedade da Azure File Sync. A saída também contém os dados de reparse que representam o caminho para o seu ficheiro na sua partilha de ficheiros Azure.
        
        > [!WARNING]  
        > O `fsutil reparsepoint` comando de utilidade também tem a capacidade de apagar um ponto de reparse. Não execute este comando a menos que a equipa de engenharia Azure File Sync lhe peça. Executar este comando pode resultar em perda de dados. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Como excluir aplicações do tiering de nuvem último rastreamento do tempo de acesso

Com a versão 11.1 do agente Azure File Sync, pode agora excluir aplicações do último rastreio de acesso. Quando uma aplicação acede a um ficheiro, a última hora de acesso ao ficheiro é atualizada na base de dados de tiering de nuvem. As aplicações que digitalizam o sistema de ficheiros como o antivírus fazem com que todos os ficheiros tenham a mesma última vez de acesso, o que tem impacto quando os ficheiros são nivelados.

Para excluir aplicações do rastreio do tempo de acesso do último, adicione o nome do processo à definição de registo HeatTrackingProcessNameExclusionList que está localizada sob HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Exemplo: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> Os processos de Deduplicação de Dados e Gestor de Recursos do Servidor de Ficheiros (FSRM) estão excluídos por padrão. As alterações à lista de exclusão do processo são honradas pelo sistema a cada 5 minutos.

## <a name="how-to-access-the-heat-store"></a>Como aceder à loja de calor

O tiering da nuvem utiliza a última hora de acesso e a frequência de acesso de um ficheiro para determinar quais os ficheiros que devem ser nivelados. O controlador de filtro de camadas de nuvem (storagesync.sys) rastreia a última hora de acesso e regista a informação na loja de calor de nivelamento de nuvens. Pode recuperar a loja de calor e guardá-la num ficheiro CSV utilizando um cmdlet PowerShell local do servidor.

Há uma única loja de calor para todos os ficheiros no mesmo volume. A loja de calor pode ficar muito grande. Se precisar apenas de recuperar o número "mais fixe" de itens, utilize -Limite e um número e considere também a filtragem por um sub-caminho vs. raiz de volume.

- Importar o módulo PowerShell:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- VOLUME ESPAÇO LIVRE: Para obter a ordem em que os ficheiros serão hierárquicos utilizando a política de espaço livre de volume:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- POLÍTICA DE DATA: Para obter a ordem em que os ficheiros serão hierárquicos usando a política de datas:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Encontre as informações da loja de calor para um ficheiro específico:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Consulte todos os ficheiros em ordem descendente pela última vez de acesso:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Consulte a ordem através da qual os ficheiros hierárquicos serão recolhidos por recordação de antecedentes ou recuperação a pedido através do PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Como forçar um ficheiro ou diretório a ser tiered

> [!NOTE]
> Quando seleciona um diretório a ser hierarquizado, apenas os ficheiros atualmente no diretório estão nivelados. Quaisquer ficheiros criados após esse tempo não são automaticamente nivelados.

Quando a função de tiering de nuvem está ativada, o tiering de nuvem tiering automaticamente camadas de ficheiros com base no último acesso e modifica os tempos para obter a percentagem de espaço livre de volume especificada no ponto final da nuvem. Às vezes, porém, talvez queira forçar manualmente um ficheiro a nivelar. Isto pode ser útil se guardar um ficheiro grande que não pretende utilizar novamente durante muito tempo, e pretende que o espaço livre no seu volume agora seja utilizado para outros ficheiros e pastas. Pode forçar o tiering utilizando os seguintes comandos PowerShell:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Como recordar um ficheiro hierárquico para o disco

A maneira mais fácil de chamar um ficheiro para o disco é abrir o ficheiro. O filtro do sistema de ficheiros Azure File Sync (StorageSync.sys) descarrega perfeitamente o ficheiro da sua partilha de ficheiros Azure sem qualquer trabalho da sua parte. Para tipos de ficheiros que podem ser parcialmente lidos ou transmitidos, como ficheiros multimédia ou .zip, simplesmente abrir um ficheiro não garante que todo o ficheiro seja descarregado.

Para garantir que um ficheiro é totalmente descarregado para o disco local, tem de utilizar o PowerShell para forçar um ficheiro a ser totalmente recolhido. Esta opção também pode ser útil se pretender relembr em vários ficheiros de uma só vez, como todos os ficheiros de uma pasta. Abra uma sessão PowerShell para o nó do servidor onde o Azure File Sync está instalado e, em seguida, executar os seguintes comandos PowerShell:
    
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
>- Se o volume local que hospeda o servidor não tiver espaço livre suficiente para recordar todos os dados hierárquicos, o `Invoke-StorageSyncFileRecall` cmdlet falha.  

> [!Note]
> Para recordar ficheiros que tenham sido nivelados, a largura de banda da rede deve ser de pelo menos 1 Mbps. Se a largura de banda da rede for inferior a 1 Mbps, os ficheiros podem não conseguir ser recolhidos com um erro de tempo limite.

## <a name="next-steps"></a>Passos seguintes

* [Perguntas mais frequentes (FAQ) sobre os Ficheiros do Azure](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)