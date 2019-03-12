---
title: Movimento de dados e armazenamento para a composição - Azure Batch
description: Opções de movimento de dados e armazenamento para cargas de trabalho de composição
services: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 15f5fe6e74cbe8cbffff31114e8aa588ec4a7ebb
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538005"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opções de movimento de dados e armazenamento para o processamento de ficheiros ativos e de saída

Existem várias opções para tornar os arquivos de recursos e de cena disponíveis para os aplicativos de composição no pool de VMs:

* [Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Ficheiros de recursos e de cena são carregados para o blob storage a partir de um sistema de ficheiros local. Quando a aplicação é executada através de uma tarefa, os ficheiros necessários são copiados do armazenamento de BLOBs na VM para que possam ser acedidos pelo aplicativo de processamento. Os ficheiros de saída são escritos pela aplicação de composição para o disco VM e, em seguida, copiados para o armazenamento de Blobs.  Se necessário, os ficheiros de saída podem ser transferidos do armazenamento de BLOBs para um sistema de ficheiros local.
  * Armazenamento de Blobs do Azure é uma opção simples e económica para projetos menores.  Como ativo todos os ficheiros são necessárias em cada VM, do agrupamento, em seguida, quando aumenta o número e tamanho dos ficheiros de elemento necessidades de cuidado para garantir que as transferências de ficheiros são tão eficientes quanto possível.  
* O armazenamento do Azure como um sistema de arquivo usando [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Para VMs do Linux, uma conta de armazenamento pode ser exposta e utilizada como um sistema de ficheiros quando o driver de sistema de arquivos virtual blobfuse é utilizado.
  * Esta opção tem a vantagem de que é bastante rentável, como não existem VMs são necessárias para o sistema de arquivos, além de blobfuse colocação em cache nas VMs evita repetidas downloads dos mesmos arquivos várias trabalhos e tarefas.  Movimento de dados também é simples, como os ficheiros são simplesmente blobs e padrão de APIs e ferramentas, como o azcopy, podem ser usadas para copiar ficheiros entre um sistema de ficheiros no local e o armazenamento do Azure.
* Sistema de ficheiros ou partilha de ficheiros:
  * Dependendo do sistema de operativo da VM e os requisitos de dimensionamento do desempenho, em seguida, as opções incluem [ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), utilizar uma VM com discos anexados para NFS, usar várias VMs com discos anexados para um sistema de ficheiros distribuído como GlusterFS, ou utilizar uma oferta de terceiros.
  * [Sistemas de Avere](https://www.averesystems.com/) faz agora parte da Microsoft e terá em breve soluções que são ideais para a composição em grande escala, de alto desempenho.  A solução de Avere permitirá NFS baseada no Azure ou cache SMB seja criado que funciona em conjunto com o armazenamento de BLOBs ou com dispositivos no local.
  * Com um sistema de ficheiros, os ficheiros podem ser lidos ou gravados diretamente para o sistema de ficheiros ou podem ser copiados entre o sistema de ficheiros e o conjunto de VMs.
  * Um sistema de ficheiros partilhado permite que um grande número de recursos compartilhados entre projetos e tarefas a ser utilizado com o processamento de tarefas acessar apenas o que é necessário.

## <a name="using-azure-blob-storage"></a>Utilizar o armazenamento de Blobs do Azure

Deve ser utilizada uma conta de armazenamento de BLOBs ou uma conta de armazenamento para fins gerais v2.  Estes dois tipos de conta de armazenamento podem ser configurados com limites significativamente mais em comparação comparadas uma conta de armazenamento para fins gerais v1, conforme detalhado no [nesta mensagem de blogue](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Quando configurado, os limites superior permitirá muito melhor desempenho e escalabilidade, especialmente quando há várias VMs de conjunto acessar a conta de armazenamento.

### <a name="copying-files-between-client-and-blob-storage"></a>Copiar ficheiros entre o armazenamento de BLOBs e de cliente

Para copiar ficheiros para e do armazenamento do Azure, podem utilizar vários mecanismos incluindo o blob de armazenamento API, o [biblioteca de movimento de dados de armazenamento do Azure](https://github.com/Azure/azure-storage-net-data-movement), a ferramenta de linha de comandos do azcopy para [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Explorador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/), e [Explorador do Batch do Azure](https://azure.github.io/BatchExplorer/).

Por exemplo, utilizar o azcopy, todos os recursos numa pasta podem ser transferidos da seguinte forma:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Para copiar apenas os ficheiros modificados, o parâmetro /XO pode ser utilizado:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar os ficheiros de elemento de entrada do armazenamento de BLOBs para as VMs do conjunto de Batch

Existem duas abordagens diferentes para copiar ficheiros com a melhor abordagem determinada pelo tamanho dos ativos de tarefa.
A abordagem mais simples é copiar todos os arquivos de recurso para o conjunto de VMs para cada tarefa:

* Quando existirem ficheiros exclusivo a um trabalho, mas são necessários para todas as tarefas de uma tarefa, em seguida, um [tarefa de preparação](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) podem ser especificados para copiar todos os arquivos.  A tarefa de preparação da tarefa é executada uma vez, quando a primeira tarefa de tarefa é executada numa VM, mas não é executada novamente para a sequência de tarefas subsequentes.
* R [tarefa de libertação](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) deve ser especificado para remover os ficheiros de por tarefa depois de concluída a tarefa; isso evitará que o disco da VM obtenção preenchido por todos os ficheiros de elemento de tarefa.
* Quando existem várias tarefas usando os mesmo ativos, com apenas as alterações incrementais para os ativos de cada tarefa, em seguida, todos os ficheiros de elemento são ainda copiados, mesmo que apenas um subconjunto foram atualizadas.  Isso seria ineficiente quando existem muitos ficheiros de recursos grandes.

Quando os ficheiros de elemento são reutilizados entre tarefas, com alterações apenas incrementais entre tarefas, em seguida, uma abordagem mais eficiente, ligeiramente mais envolvida, mas é armazenar elementos na pasta partilhada na VM e sincronizar ficheiros alterados.

* A tarefa de preparação executará a cópia com o azcopy com o parâmetro /XO para a pasta compartilhada de VM especificada pela variável de ambiente de AZ_BATCH_NODE_SHARED_DIR.  Só isso irá copiar os ficheiros alterados para cada VM.
* Idéias terão-se obter o tamanho de todos os ativos para garantir que poderão caber numa unidade temporária do conjunto de VMs.

O Azure Batch tem suporte incorporado para copiar ficheiros entre uma conta de armazenamento e as VMs do conjunto de Batch.  Tarefa [arquivos de recursos](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) copiar ficheiros de armazenamento para as VMs do conjunto e pode ser especificado para a tarefa de preparação.  Infelizmente, se existirem centenas de arquivos é possível atingir um limite e a falha de tarefas.  Quando há um grande número de ativos é recomendado utilizar a linha de comandos do azcopy na tarefa de preparação, que pode utilizar carateres universais e tem sem limite.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Copiar ficheiros de saída para blob storage, as VMs do conjunto de Batch

[Ficheiros de saída](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) pode ser ficheiros de cópia utilizados de uma VM do agrupamento de armazenamento.  Um ou mais ficheiros podem ser copiados da VM para uma conta de armazenamento especificado depois de concluída a tarefa.  A saída renderizada deve ser copiada, mas também pode ser desejável para armazenar ficheiros de registo.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Utilizar um sistema de arquivos virtual blobfuse para conjuntos de VM do Linux

Blobfuse é um driver de sistema de arquivos virtual para armazenamento de Blobs do Azure, que permite-lhe aceder a ficheiros armazenados como blobs numa conta de armazenamento através do sistema de ficheiros do Linux.

Nós do conjunto podem montar o sistema de ficheiros quando iniciado ou a montagem pode acontecer como parte de uma tarefa de preparação de trabalho – uma tarefa que é executada apenas quando a primeira tarefa numa tarefa é executada num nó.  Blobfuse pode ser configurado para tirar partido de um ramdisk e SSD local para colocar em cache de ficheiros, o que irão aumentar o desempenho significativamente se várias tarefas num nó de aceder a alguns dos mesmos arquivos de VMs.

[Modelos de exemplo estão disponíveis](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) para executar autónomo V-Ray é processado com um sistema de ficheiros blobfuse e pode ser utilizado como base para modelos para outras aplicações.

### <a name="accessing-files"></a>Aceder a ficheiros

Tarefas de especificar caminhos para ficheiros de entrada e de ficheiros de saída usando o sistema de ficheiros instalado.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar os ficheiros de elemento de entrada do armazenamento de BLOBs para as VMs do conjunto de Batch

À medida que os arquivos são simplesmente blobs no armazenamento do Azure, em seguida, BLOBs padrão APIs, ferramentas e interfaces do usuário podem ser usados para copiar ficheiros entre no local system e blob de armazenamento de ficheiros; Por exemplo, o azcopy, Explorador de armazenamento, o Explorador do Batch, etc.

## <a name="using-azure-files-with-windows-vms"></a>Utilizar os ficheiros do Azure com VMs do Windows

[Os ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) oferece totalmente geridos partilhas de ficheiros na cloud que estão acessíveis através do protocolo SMB.  Os ficheiros do Azure baseia-se no armazenamento de Blobs do Azure; é [económico](https://azure.microsoft.com/pricing/details/storage/files/) e pode ser configurado com replicação de dados para outra região, por isso, globalmente redundante.  [Dimensionar destinos](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) devem ser revistas para determinar se os ficheiros do Azure deve ser utilizados na tendo em conta o tamanho do conjunto de previsão e o número de ficheiros de elemento.

Há uma [mensagem de blogue](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) e [documentação](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) que descreve como montar uma partilha de ficheiros do Azure.

### <a name="mounting-an-azure-files-share"></a>Montar uma partilha de ficheiros do Azure

Para utilizar em lote, uma operação de montagem tem de ser executada sempre que uma tarefa em execução como não é possível manter a conexão entre tarefas.  A maneira mais fácil de fazer isso é usar cmdkey para manter as credenciais a utilizar a tarefa de início na configuração do conjunto, em seguida, montar a partilha antes de cada tarefa.

Exemplo de cmdkey num modelo de agrupamento (escape para utilização num ficheiro JSON) – utilize tenha em atenção que ao separar a chamada de cmdkey da chamada a utilização de rede, o contexto de utilizador para a tarefa de início tem de ser iguais às utilizadas para executar as tarefas:

```
"startTask": {
  "commandLine": "cmdkey /add:storageaccountname.file.core.windows.net
    /user:AZURE\\markscuscusbatch /pass:storage_account_key",
  "userIdentity":{
    "autoUser": {
      "elevationLevel": "nonadmin",
      "scope": "pool"
    }
}
```

Linha de comandos de tarefas de trabalho de exemplo:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Aceder a ficheiros

Tarefas de especificar caminhos para ficheiros de entrada e de ficheiros de saída usando o sistema de ficheiros instalado, utilizando uma unidade mapeada ou caminho UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar os ficheiros de elemento de entrada do armazenamento de BLOBs para as VMs do conjunto de Batch

Os ficheiros do Azure são suportados por todas as principais APIs e ferramentas que têm o armazenamento do Azure suportam; Por exemplo, o azcopy, da CLI do Azure, o Explorador de armazenamento, o Azure PowerShell, o Explorador do Batch, etc.

[O Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) está disponível para sincronizar arquivos entre um sistema de ficheiros no local e uma partilha de ficheiros do Azure automaticamente.

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações sobre as opções de armazenamento, consulte a documentação detalhada sobre:

* [Armazenamento de Blobs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
