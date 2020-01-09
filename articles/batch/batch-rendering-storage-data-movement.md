---
title: Armazenamento e movimentação de dados para renderização-lote do Azure
description: Saiba mais sobre as várias opções de armazenamento e movimentação de dados para processar cargas de trabalho de ativos e arquivos de saída.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: conceptual
ms.openlocfilehash: 1076fc152ddf2c3a2d4f2346262ca90215d68ddf
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75390389"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opções de armazenamento e movimentação de dados para renderizar arquivos de ativos e de saída

Há várias opções para disponibilizar os arquivos de cena e de ativo para os aplicativos de renderização nas VMs do pool:

* [Armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction):
  * Os arquivos de cena e de ativo são carregados no armazenamento de blobs de um sistema de arquivos local. Quando o aplicativo é executado por uma tarefa, os arquivos necessários são copiados do armazenamento de BLOBs para a VM, para que possam ser acessados pelo aplicativo de renderização. Os arquivos de saída são gravados pelo aplicativo de renderização no disco da VM e, em seguida, copiados para o armazenamento de BLOBs.  Se necessário, os arquivos de saída podem ser baixados do armazenamento de BLOBs para um sistema de arquivos local.
  * O armazenamento de BLOBs do Azure é uma opção simples e econômica para projetos menores.  Como todos os arquivos de ativo são necessários em cada VM do pool, uma vez que o número e o tamanho dos arquivos de ativo aumentam o cuidado para garantir que as transferências de arquivo sejam as mais eficientes possíveis.  
* Armazenamento do Azure como um sistema de arquivos usando o [blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux):
  * Para VMs do Linux, uma conta de armazenamento pode ser exposta e usada como um sistema de arquivos quando o driver do sistema de arquivos virtual blobfuse é usado.
  * Essa opção tem a vantagem de ser muito econômica, uma vez que nenhuma VM é necessária para o sistema de arquivos, além do blobfuse Caching nas VMs evita downloads repetidos dos mesmos arquivos para vários trabalhos e tarefas.  A movimentação de dados também é simples, pois os arquivos são simplesmente BLOBs, e APIs e ferramentas padrão, como azcopy, podem ser usados para copiar o arquivo entre um sistema de arquivos local e o armazenamento do Azure.
* Sistema de arquivos ou compartilhamento de arquivos:
  * Dependendo do sistema operacional da VM e dos requisitos de desempenho/escala, as opções incluem [arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction), usando uma VM com discos anexados para NFS, usando várias VMs com discos anexados para um sistema de arquivos distribuído, como GlusterFS, ou usando uma oferta de terceiros.
  * Os [sistemas avere](https://www.averesystems.com/) agora fazem parte da Microsoft e terão soluções em um futuro próximo que são ideais para renderização em larga escala e de alto desempenho.  A solução avere permitirá que um cache NFS ou SMB baseado no Azure seja criado e funcione em conjunto com o armazenamento de BLOBs ou com dispositivos NAS locais.
  * Com um sistema de arquivos, os arquivos podem ser lidos ou gravados diretamente no sistema de arquivos ou podem ser copiados entre as VMs do sistema de arquivos e do pool.
  * Um sistema de arquivos compartilhado permite que um grande número de ativos compartilhados entre projetos e trabalhos seja utilizado, com tarefas de renderização acessando apenas o que é necessário.

## <a name="using-azure-blob-storage"></a>Usando o armazenamento de BLOBs do Azure

Uma conta de armazenamento de BLOBs ou uma conta de armazenamento de uso geral v2 deve ser usada.  Esses dois tipos de conta de armazenamento podem ser configurados com limites significativamente mais altos em comparação com uma conta de armazenamento v1 de uso geral, conforme detalhado nesta [postagem no blog](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Quando configurado, os limites mais altos permitirão um desempenho e escalabilidade muito melhores, especialmente quando houver muitas VMs de pool acessando a conta de armazenamento.

### <a name="copying-files-between-client-and-blob-storage"></a>Copiando arquivos entre o armazenamento de cliente e BLOB

Para copiar arquivos de e para o armazenamento do Azure, vários mecanismos podem ser usados, incluindo a API de armazenamento de BLOBs, a [biblioteca de movimentação de dados do armazenamento do Azure](https://github.com/Azure/azure-storage-net-data-movement), a ferramenta de linha de comando Azcopy para [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) ou [Linux](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux), [Gerenciador de armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/)e [Azure batch Explorer](https://azure.github.io/BatchExplorer/).

Por exemplo, usando azcopy, todos os ativos em uma pasta podem ser transferidos da seguinte maneira:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Para copiar somente os arquivos modificados, o parâmetro/XO pode ser usado:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiando arquivos de ativo de entrada do armazenamento de BLOBs para VMs do pool do lote

Há algumas abordagens diferentes para copiar arquivos com a melhor abordagem determinada pelo tamanho dos ativos de trabalho.
A abordagem mais simples é copiar todos os arquivos de ativo para as VMs do pool para cada trabalho:

* Quando há arquivos exclusivos a um trabalho, mas são necessários para todas as tarefas de um trabalho, uma tarefa de [preparação de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) pode ser especificada para copiar todos os arquivos.  A tarefa de preparação de trabalho é executada uma vez quando a primeira tarefa de trabalho é executada em uma VM, mas não é executada novamente para tarefas de trabalho subsequentes.
* Uma [tarefa de liberação de trabalho](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) deve ser especificada para remover os arquivos por trabalho depois que o trabalho for concluído; Isso evitará que o disco de VM seja preenchido por todos os arquivos de ativo de trabalho.
* Quando há vários trabalhos usando os mesmos ativos, com apenas alterações incrementais nos ativos de cada trabalho, todos os arquivos de ativo ainda são copiados, mesmo que apenas um subconjunto tenha sido atualizado.  Isso seria ineficiente quando há muitos arquivos de ativo grandes.

Quando os arquivos de ativo são reutilizados entre trabalhos, com apenas alterações incrementais entre trabalhos, uma abordagem mais eficiente, mas ligeiramente mais envolvida, é armazenar ativos na pasta compartilhada na VM e sincronizar arquivos alterados.

* A tarefa de preparação de trabalho executaria a cópia usando azcopy com o parâmetro/XO para a pasta compartilhada da VM especificada por AZ_BATCH_NODE_SHARED_DIR variável de ambiente.  Isso copiará somente os arquivos alterados para cada VM.
* Deve-se ter em consideração o tamanho de todos os ativos para garantir que eles caibam na unidade temporária das VMs do pool.

O lote do Azure tem suporte interno para copiar arquivos entre uma conta de armazenamento e VMs do pool do lote.  [Arquivos de recurso](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) de tarefa copiam arquivos do armazenamento para VMs de pool e podem ser especificados para a tarefa de preparação de trabalho.  Infelizmente, quando há centenas de arquivos, é possível atingir um limite e as tarefas falham.  Quando há um grande número de ativos, é recomendável usar a linha de comando azcopy na tarefa de preparação do trabalho, que pode usar curingas e sem limite.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Copiando arquivos de saída para o armazenamento de blobs de VMs do pool do lote

[Os arquivos de saída](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) podem ser usados para copiar arquivos de uma VM do pool para o armazenamento.  Um ou mais arquivos podem ser copiados da VM para uma conta de armazenamento especificada assim que a tarefa for concluída.  A saída renderizada deve ser copiada, mas também pode ser desejável armazenar arquivos de log.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Usando um sistema de arquivos virtual blobfuse para pools de VM Linux

Blobfuse é um driver de sistema de arquivos virtual para armazenamento de BLOBs do Azure, que permite que você acesse arquivos armazenados como BLOBs em uma conta de armazenamento por meio do sistema de arquivos do Linux.

Os nós de pool podem montar o sistema de arquivos quando iniciado ou a montagem pode ocorrer como parte de uma tarefa de preparação de trabalho – uma tarefa que só é executada quando a primeira tarefa em um trabalho é executada em um nó.  O Blobfuse pode ser configurado para aproveitar um ramdisk e o SSD local de VMs para cache de arquivos, o que aumentará significativamente o desempenho se várias tarefas em um nó acessarem alguns dos mesmos arquivos.

Os [modelos de exemplo estão disponíveis](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) para executar renderizações de V-Ray autônomos usando um sistema de arquivos blobfuse e podem ser usados como base para modelos para outros aplicativos.

### <a name="accessing-files"></a>Acessando arquivos

As tarefas de trabalho especificam caminhos para arquivos de entrada e arquivos de saída usando o sistema de arquivos montado.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiando arquivos de ativo de entrada do armazenamento de BLOBs para VMs do pool do lote

Como os arquivos são simplesmente BLOBs no armazenamento do Azure, as APIs de blob padrão, as ferramentas e as UIs podem ser usadas para copiar arquivos entre um sistema de arquivos local e o armazenamento de BLOBs; por exemplo, azcopy, Gerenciador de Armazenamento, Batch Explorer, etc.

## <a name="using-azure-files-with-windows-vms"></a>Usando os arquivos do Azure com VMs do Windows

Os [arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) oferecem compartilhamentos de arquivos totalmente gerenciados na nuvem que são acessíveis por meio do protocolo SMB.  Os arquivos do Azure baseiam-se no armazenamento de BLOBs do Azure; Ele é [econômico](https://azure.microsoft.com/pricing/details/storage/files/) e pode ser configurado com a replicação de dados para outra região, para que seja globalmente redundante.  Os [destinos de escala](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) devem ser revisados para determinar se os arquivos do Azure devem ser usados de acordo com o tamanho do pool de previsão e o número de arquivos de ativo.

Há uma [postagem no blog](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) e [documentação](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) que aborda como montar um compartilhamento de arquivos do Azure.

### <a name="mounting-an-azure-files-share"></a>Montando um compartilhamento de arquivos do Azure

Para usar no lote, uma operação de montagem precisa ser executada cada vez que uma tarefa é executada, pois não é possível manter a conexão entre as tarefas.  A maneira mais fácil de fazer isso é usar cmdkey para persistir as credenciais usando a tarefa inicial na configuração do pool e, em seguida, montar o compartilhamento antes de cada tarefa.

Exemplo de uso de cmdkey em um modelo de pool (escape para uso no arquivo JSON) – Observe que, ao separar a chamada cmdkey da chamada net use, o contexto do usuário para a tarefa inicial deve ser o mesmo usado para executar as tarefas:

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

Linha de comando da tarefa de trabalho de exemplo:
```
"commandLine":"net use S:
  \\\\storageaccountname.file.core.windows.net\\rendering &
3dsmaxcmdio.exe -v:5 -rfw:0 -10 -end:10
  -bitmapPath:\"s:\\3dsMax\\Dragon\\Assets\"
  -outputName:\"s:\\3dsMax\\Dragon\\RenderOutput\\dragon.jpg\"
  -w:1280 -h:720
  \"s:\\3dsMax\\Dragon\\Assets\\Dragon_Character_Rig.max\""
```

### <a name="accessing-files"></a>Acessando arquivos

As tarefas de trabalho especificam caminhos para arquivos de entrada e arquivos de saída usando o sistema de arquivos montado, usando uma unidade mapeada ou um caminho UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiando arquivos de ativo de entrada do armazenamento de BLOBs para VMs do pool do lote

Os arquivos do Azure têm suporte de todas as APIs e ferramentas principais que têm suporte ao armazenamento do Azure; por exemplo, azcopy, CLI do Azure, Gerenciador de Armazenamento, Azure PowerShell, Batch Explorer, etc.

[Sincronização de arquivos do Azure](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) está disponível para sincronizar automaticamente arquivos entre um sistema de arquivos local e um compartilhamento de arquivos do Azure.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as opções de armazenamento, consulte a documentação detalhada:

* [Armazenamento de BLOBs do Azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
