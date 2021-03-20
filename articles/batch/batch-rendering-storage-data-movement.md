---
title: Armazenamento e movimento de dados para a renderização
description: Conheça as várias opções de armazenamento e movimento de dados para renderizar cargas de trabalho de ativos e ficheiros de saída.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: 55ec04df2a107dabfc72298bc8849c13f3a926e0
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "86147312"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opções de armazenamento e movimento de dados para renderização de ficheiros de ativos e de saída

Existem múltiplas opções para disponibilizar a cena e os ficheiros de ativos às aplicações de renderização nos VMs da piscina:

* [Armazenamento de bolhas Azure:](../storage/blobs/storage-blobs-introduction.md)
  * Os ficheiros de cena e de ativos são enviados para o armazenamento de blob a partir de um sistema de ficheiros local. Quando a aplicação é executada por uma tarefa, os ficheiros necessários são copiados do armazenamento de bolhas para o VM para que possam ser acedidos através da aplicação de renderização. Os ficheiros de saída são escritos pela aplicação de renderização do disco VM e depois copiados para o armazenamento de bolhas.  Se necessário, os ficheiros de saída podem ser descarregados do armazenamento de bolhas para um sistema de ficheiros local.
  * O armazenamento de blob azure é uma opção simples e rentável para projetos mais pequenos.  Como todos os ficheiros de ativos são necessários em cada VM do pool, então uma vez que o número e o tamanho dos ficheiros de ativos aumentam, é necessário ter cuidado para garantir que as transferências de ficheiros são o mais eficientes possível.  
* Armazenamento Azure como um sistema de ficheiros usando [blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md):
  * Para os VMs Linux, uma conta de armazenamento pode ser exposta e usada como um sistema de ficheiros quando o controlador do sistema de ficheiros virtuais blobfuse é utilizado.
  * Esta opção tem a vantagem de ser muito rentável, uma vez que não são necessários VMs para o sistema de ficheiros, além de que o blobfuse caching nos VMs evita transferências repetidas dos mesmos ficheiros para múltiplos trabalhos e tarefas.  O movimento de dados também é simples, uma vez que os ficheiros são simplesmente bolhas e APIs padrão e ferramentas, como a azcopia, podem ser usadas para copiar ficheiros entre um sistema de ficheiros no local e o armazenamento Azure.
* Sistema de ficheiros ou partilha de ficheiros:
  * Dependendo do sistema operativo VM e dos requisitos de desempenho/escala, as opções incluem [ficheiros Azure,](../storage/files/storage-files-introduction.md)utilizando um VM com discos anexados para NFS, utilizando vários VMs com discos anexados para um sistema de ficheiros distribuídos como o GlusterFS, ou utilizando uma oferta de terceiros.
  * [A Avere Systems](https://www.averesystems.com/) passa a fazer parte da Microsoft e terá soluções num futuro próximo que são ideais para renderização de grande escala e de alto desempenho.  A solução Avere permitirá criar uma cache NFS ou SMB baseada em Azure que funcione em conjunto com o armazenamento de bolhas ou com dispositivos NAS no local.
  * Com um sistema de ficheiros, os ficheiros podem ser lidos ou escritos diretamente no sistema de ficheiros ou podem ser copiados entre o sistema de ficheiros e os VMs da piscina.
  * Um sistema de ficheiros partilhados permite que um grande número de ativos partilhados entre projetos e empregos sejam utilizados, com tarefas de renderização apenas acedendo ao que é necessário.

## <a name="using-azure-blob-storage"></a>Usando o armazenamento de bolhas Azure

Deve ser utilizada uma conta de armazenamento blob ou uma conta de armazenamento v2 para fins gerais.  Estes dois tipos de conta de armazenamento podem ser configurados com limites significativamente mais elevados em comparação com uma conta de armazenamento v1 de uso geral, conforme detalhado [neste post de blog](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/).  Quando configurados, os limites mais elevados permitirão um desempenho e escalabilidade muito melhores, especialmente quando há muitos VMs de piscina que acedem à conta de armazenamento.

### <a name="copying-files-between-client-and-blob-storage"></a>Copiar ficheiros entre o armazenamento do cliente e do blob

Para copiar ficheiros de e para o armazenamento Azure, podem ser utilizados vários mecanismos, incluindo a bolha de armazenamento API, a [Biblioteca de Movimentos de Dados de Armazenamento Azure,](https://github.com/Azure/azure-storage-net-data-movement)a ferramenta de linha de comando azcopia para [Windows](../storage/common/storage-use-azcopy-v10.md) ou [Linux,](../storage/common/storage-use-azcopy-v10.md) [Azure Storage Explorer,](https://azure.microsoft.com/features/storage-explorer/)e [Azure Batch Explorer](https://azure.github.io/BatchExplorer/).

Por exemplo, usando a azcopia, todos os ativos de uma pasta podem ser transferidos da seguinte forma:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Para copiar apenas ficheiros modificados, o parâmetro /XO pode ser utilizado:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar ficheiros de ativos de entrada do armazenamento de bolhas para VMs de piscina de lote

Existem algumas abordagens diferentes para copiar ficheiros com a melhor abordagem determinada pelo tamanho dos ativos do trabalho.
A abordagem mais simples é copiar todos os ficheiros de ativos para os VMs da piscina para cada trabalho:

* Quando há ficheiros exclusivos de um trabalho, mas são necessários para todas as tarefas de um trabalho, então uma tarefa de [preparação](/rest/api/batchservice/job/add#jobpreparationtask) de trabalho pode ser especificada para copiar todos os ficheiros.  A tarefa de preparação do trabalho é executada uma vez quando a primeira tarefa de trabalho é executada num VM, mas não é novamente executada para tarefas de trabalho subsequentes.
* Deve ser especificada uma tarefa de [libertação](/rest/api/batchservice/job/add#jobreleasetask) de emprego para remover os ficheiros por trabalho uma vez concluído o trabalho; isto evitará que o disco VM seja preenchido por todos os ficheiros de ativos de trabalho.
* Quando há vários postos de trabalho usando os mesmos ativos, com apenas alterações incrementais nos ativos para cada trabalho, então todos os ficheiros de ativos ainda são copiados, mesmo que apenas um subconjunto tenha sido atualizado.  Isto seria ineficiente quando há muitos ficheiros de ativos grandes.

Quando os ficheiros de ativos são reutilizados entre postos de trabalho, com apenas mudanças incrementais entre postos de trabalho, então uma abordagem mais eficiente, mas ligeiramente mais envolvida, é armazenar ativos na pasta partilhada no VM e sincronizar ficheiros alterados.

* A tarefa de preparação do trabalho executaria a cópia utilizando a azcopia com o parâmetro /XO para a pasta partilhada VM especificada por AZ_BATCH_NODE_SHARED_DIR variável ambiental.  Isto só irá copiar ficheiros alterados para cada VM.
* O pensamento terá de ser dado ao tamanho de todos os ativos para garantir que se encaixarão na unidade temporária dos VMs da piscina.

O Azure Batch tem suporte incorporado para copiar ficheiros entre uma conta de armazenamento e VMs da piscina de lotes.  Os [ficheiros de recursos](/rest/api/batchservice/job/add#resourcefile) de tarefa copiam ficheiros do armazenamento para os VMs de piscina e podem ser especificados para a tarefa de preparação do trabalho.  Infelizmente, quando há centenas de ficheiros é possível atingir um limite e tarefas a falhar.  Quando há um grande número de ativos é recomendado usar a linha de comando da azcopia na tarefa de preparação de trabalho, que pode usar wildcards e não tem limite.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Copiar ficheiros de saída para o armazenamento de blob a partir de VMs de piscina batch

[Os ficheiros de saída](/rest/api/batchservice/task/add#outputfile) podem ser usados ficheiros de cópia de um VM de piscina para armazenamento.  Um ou mais ficheiros podem ser copiados do VM para uma conta de armazenamento especificada uma vez concluída a tarefa.  A saída renderizada deve ser copiada, mas também pode ser desejável armazenar ficheiros de registo.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Usando um sistema de ficheiros virtuais blobfuse para piscinas Linux VM

O Blobfuse é um controlador de sistema de ficheiros virtual para o Azure Blob Storage, que permite aceder a ficheiros armazenados como bolhas numa conta de Armazenamento através do sistema de ficheiros Linux.

Os nós de piscina podem montar o sistema de ficheiros quando iniciado ou o suporte pode acontecer como parte de uma tarefa de preparação de trabalho – uma tarefa que só é executada quando a primeira tarefa de um trabalho funciona num nó.  O Blobfuse pode ser configurado para alavancar tanto um ramdisk como o SSD local VMs para a caching de ficheiros, o que aumentará significativamente o desempenho se várias tarefas num nó acederem a alguns dos mesmos ficheiros.

[Os modelos de amostra estão disponíveis](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) para executar renderizações de V-Ray autónomas usando um sistema de ficheiros blobfuse e podem ser usados como base para modelos para outras aplicações.

### <a name="accessing-files"></a>Aceder a ficheiros

As tarefas de trabalho especificam caminhos para ficheiros de entrada e ficheiros de saída utilizando o sistema de ficheiros montado.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar ficheiros de ativos de entrada do armazenamento de bolhas para VMs de piscina de lote

Como os ficheiros são simplesmente bolhas no Azure Storage, em seguida, APIs blob padrão, ferramentas e UIs podem ser usados para copiar ficheiros entre um sistema de ficheiros no local e armazenamento de bolhas; por exemplo, azcopia, Explorador de Armazenamento, Explorador de Lotes, etc.

## <a name="using-azure-files-with-windows-vms"></a>Utilização de ficheiros Azure com VMs windows

[O Azure Files](../storage/files/storage-files-introduction.md) oferece ações de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo SMB.  A Azure Files baseia-se no armazenamento de blob Azure; é [rentável](https://azure.microsoft.com/pricing/details/storage/files/) e pode ser configurado com a replicação de dados para outra região tão globalmente redundante.  [Os alvos de escala](../storage/files/storage-files-scale-targets.md#azure-files-scale-targets) devem ser revistos para determinar se os Ficheiros Azure devem ser utilizados dado o tamanho da previsão de pool e o número de ficheiros de ativos.

Existe [documentação que](../storage/files/storage-how-to-use-files-windows.md) cobre como montar uma partilha de ficheiros Azure.

### <a name="mounting-an-azure-files-share"></a>Montagem de uma partilha de Ficheiros Azure

Para ser utilizada em Batch, é necessário executar uma operação de montagem sempre que uma tarefa em execução não seja possível persistir na ligação entre tarefas.  A maneira mais fácil de o fazer é usar o cmdkey para persistir nas credenciais usando a tarefa inicial na configuração da piscina e, em seguida, montar a partilha antes de cada tarefa.

Exemplo de utilização de cmdkey num modelo de piscina (escapada para utilização em ficheiro JSON) – note que ao separar a chamada cmdkey da chamada de utilização da rede, o contexto do utilizador para a tarefa inicial deve ser o mesmo que o utilizado para executar as tarefas:

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

Linha de comando de tarefa de trabalho exemplo:
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

As tarefas de trabalho especificam caminhos para ficheiros de entrada e ficheiros de saída utilizando o sistema de ficheiros montado, utilizando uma unidade mapeada ou um caminho UNC.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar ficheiros de ativos de entrada do armazenamento de bolhas para VMs de piscina de lote

Os Ficheiros Azure são suportados por todas as PRINCIPAis APIs e ferramentas que têm suporte a armazenamento Azure; por exemplo, azcopia, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer, etc.

[O Azure File Sync](../storage/files/storage-sync-files-planning.md) está disponível para sincronizar automaticamente ficheiros entre um sistema de ficheiros no local e uma partilha de Ficheiros Azure.

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações sobre as opções de armazenamento consulte a documentação aprofundada:

* [Armazenamento de blobs do Azure](../storage/blobs/storage-blobs-introduction.md)
* [Rio Blobfuse](../storage/blobs/storage-how-to-mount-container-linux.md)
* [Ficheiros do Azure](../storage/files/storage-files-introduction.md)
