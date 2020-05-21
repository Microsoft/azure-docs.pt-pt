---
title: Armazenamento e movimento de dados para renderização
description: Conheça as várias opções de armazenamento e movimento de dados para renderizar cargas de trabalho de ficheiros de ativos e de saída.
services: batch
ms.service: batch
author: mscurrell
ms.author: markscu
ms.date: 08/02/2018
ms.topic: how-to
ms.openlocfilehash: dcb9d43b228428379414ca5d7688cff709a9959e
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726422"
---
# <a name="storage-and-data-movement-options-for-rendering-asset-and-output-files"></a>Opções de armazenamento e movimento de dados para renderização de ficheiros de ativos e de saída

Existem várias opções para disponibilizar a cena e os ficheiros de ativos para as aplicações de renderização nos VMs da piscina:

* [Armazenamento de bolhas azure:](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
  * Os ficheiros de cena e de ativos são enviados para o armazenamento de blob de um sistema de ficheiros local. Quando a aplicação é executada por uma tarefa, os ficheiros necessários são copiados do armazenamento de blob para o VM para que possam ser acedidos através da aplicação de renderização. Os ficheiros de saída são escritos pela aplicação de renderização no disco VM e depois copiados para o armazenamento de bolhas.  Se necessário, os ficheiros de saída podem ser descarregados do armazenamento blob para um sistema de ficheiros local.
  * O armazenamento de blob azure é uma opção simples e rentável para projetos menores.  Como todos os ficheiros de ativos são necessários em cada pool VM, então uma vez que o número e tamanho dos ficheiros de ativos aumenta o cuidado deve ser tomado para garantir que as transferências de ficheiros são o mais eficientes possível.  
* Armazenamento azure como sistema de ficheiros utilizando [blobfuse:](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
  * Para os VMs Linux, uma conta de armazenamento pode ser exposta e usada como um sistema de ficheiros quando o controlador do sistema de ficheiros virtual blobfuse é utilizado.
  * Esta opção tem a vantagem de ser muito rentável, uma vez que não são necessários VMs para o sistema de ficheiros, além de que o cache de blobfuse nos VMs evita transferências repetidas dos mesmos ficheiros para vários trabalhos e tarefas.  O movimento de dados também é simples, uma vez que os ficheiros são simplesmente bolhas e APIs padrão e ferramentas, como a azcopy, podem ser usadas para copiar ficheiros entre um sistema de ficheiros no local e armazenamento Azure.
* Sistema de ficheiros ou partilha de ficheiros:
  * Dependendo do sistema operativo VM e dos requisitos de desempenho/escala, as opções incluem [ficheiros Azure,](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)utilizando um VM com discos ligados para NFS, utilizando vários VMs com discos anexados para um sistema de ficheiros distribuído como o GlusterFS, ou utilizando uma oferta de terceiros.
  * [A Avere Systems](https://www.averesystems.com/) faz agora parte da Microsoft e terá soluções num futuro próximo que são ideais para renderização de grande escala e de alto desempenho.  A solução Avere permitirá a criação de uma cache NFS ou SMB baseada em Azure que funcione em conjunto com o armazenamento de blob ou com dispositivos NAS no local.
  * Com um sistema de ficheiros, os ficheiros podem ser lidos ou escritos diretamente para o sistema de ficheiros ou podem ser copiados entre o sistema de ficheiros e os VMs da piscina.
  * Um sistema de ficheiros partilhados permite a utilização de um grande número de ativos partilhados entre projetos e postos de trabalho, com tarefas de renderização apenas aceder ao que é necessário.

## <a name="using-azure-blob-storage"></a>Usando o armazenamento de blob Azure

Deve ser utilizada uma conta de armazenamento blob ou uma conta de armazenamento v2 de uso geral.  Estes dois tipos de conta de armazenamento podem ser configurados com limites significativamente mais elevados em comparação com uma conta de armazenamento v1 de propósito geral, conforme detalhado [neste post](https://azure.microsoft.com/blog/announcing-larger-higher-scale-storage-accounts/)de blog .  Quando configurados, os limites mais elevados permitirão um desempenho e escalabilidade muito melhores, especialmente quando há muitos VMs de piscina a aceder à conta de armazenamento.

### <a name="copying-files-between-client-and-blob-storage"></a>Copiar ficheiros entre o armazenamento de cliente e blob

Para copiar ficheiros de e para o armazenamento do Azure, podem ser utilizados vários mecanismos, incluindo a API blob de armazenamento, a Biblioteca de Movimentos de Dados de [Armazenamento Azure,](https://github.com/Azure/azure-storage-net-data-movement)a ferramenta de linha de comando azcopy para [Windows](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy) ou [Linux,](https://docs.microsoft.com/azure/storage/common/storage-use-azcopy-linux) [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/)e [Azure Batch Explorer.](https://azure.github.io/BatchExplorer/)

Por exemplo, utilizando azcopy, todos os ativos de uma pasta podem ser transferidos da seguinte forma:


`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /Y`

Para copiar apenas ficheiros modificados, o parâmetro /XO pode ser utilizado:

`azcopy /source:. /dest:https://account.blob.core.windows.net/rendering/project /destsas:"?st=2018-03-30T16%3A26%3A00Z&se=2020-03-31T16%3A26%3A00Z&sp=rwdl&sv=2017-04-17&sr=c&sig=sig" /XO /Y`

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar ficheiros de ativos de entrada do armazenamento de blob para VMs de piscina de lote

Existem algumas abordagens diferentes para copiar ficheiros com a melhor abordagem determinada pelo tamanho dos ativos de trabalho.
A abordagem mais simples é copiar todos os ficheiros de ativos para os VMs da piscina para cada trabalho:

* Quando existem ficheiros exclusivos de um trabalho, mas são necessários para todas as tarefas de um trabalho, então uma tarefa de [preparação](https://docs.microsoft.com/rest/api/batchservice/job/add#jobpreparationtask) de emprego pode ser especificada para copiar todos os ficheiros.  A tarefa de preparação de emprego é executada uma vez quando a primeira tarefa de trabalho é executada num VM, mas não é executada novamente para tarefas de trabalho subsequentes.
* Deve ser especificada uma tarefa de [libertação](https://docs.microsoft.com/rest/api/batchservice/job/add#jobreleasetask) de postos de trabalho para remover os ficheiros por trabalho uma vez concluído o trabalho; isto evitará que o disco VM seja preenchido por todos os ficheiros de ativos de trabalho.
* Quando existem múltiplos empregos usando os mesmos ativos, com apenas alterações incrementais nos ativos para cada trabalho, então todos os ficheiros de ativos ainda são copiados, mesmo que apenas um subconjunto foi atualizado.  Isto seria ineficiente quando há muitos ficheiros de ativos grandes.

Quando os ficheiros de ativos são reutilizados entre empregos, com apenas alterações incrementais entre empregos, então uma abordagem mais eficiente, mas ligeiramente mais envolvida, é armazenar ativos na pasta partilhada no VM e sincronizar ficheiros alterados.

* A tarefa de preparação do trabalho executaria a cópia utilizando a azcopy com o parâmetro /XO para a pasta partilhada VM especificada por AZ_BATCH_NODE_SHARED_DIR variável ambiental.  Isto só copiará ficheiros alterados para cada VM.
* O pensamento terá de ser dado ao tamanho de todos os ativos para garantir que se encaixarão na unidade temporária dos VMs da piscina.

O Azure Batch tem suporte incorporado para copiar ficheiros entre uma conta de armazenamento e VMs de piscina de lote.  Os ficheiros de recursos de [tarefacopiam ficheiros](https://docs.microsoft.com/rest/api/batchservice/job/add#resourcefile) de armazenamento para pool VMs e podem ser especificados para a tarefa de preparação do trabalho.  Infelizmente, quando existem centenas de ficheiros, é possível atingir um limite e as tarefas falharem.  Quando há um grande número de ativos, recomenda-se a utilização da linha de comando de azcopy na tarefa de preparação de trabalho, que pode usar wildcards e não tem limite.

### <a name="copying-output-files-to-blob-storage-from-batch-pool-vms"></a>Copiar ficheiros de saída para armazenamento de blob a partir de VMs de piscina de lote

[Os ficheiros](https://docs.microsoft.com/rest/api/batchservice/task/add#outputfile) de saída podem ser utilizados ficheiros de cópia saem de um VM de piscina para armazenamento.  Um ou mais ficheiros podem ser copiados do VM para uma conta de armazenamento especificada uma vez concluída a tarefa.  A saída renderizada deve ser copiada, mas também pode ser desejável armazenar ficheiros de registo.

## <a name="using-a-blobfuse-virtual-file-system-for-linux-vm-pools"></a>Usando um sistema de ficheiros virtual blobfuse para piscinas De VM Linux

Blobfuse é um controlador de sistema de ficheiros virtual para o Armazenamento De Blob Azure, que permite aceder a ficheiros armazenados como blobs numa conta de Armazenamento através do sistema de ficheiros Linux.

Os nódosos de piscina podem montar o sistema de ficheiros quando iniciados ou o monte pode acontecer como parte de uma tarefa de preparação de trabalho – tarefa que só é executada quando a primeira tarefa de um trabalho funciona num nó.  A blobfuse pode ser configurada para alavancar tanto um ramdisk como o SSD local vMs para o cache de ficheiros, o que aumentará significativamente o desempenho se várias tarefas num nó acederem a alguns dos mesmos ficheiros.

[Os modelos](https://github.com/Azure/BatchExplorer-data/tree/master/ncj/vray/render-linux-with-blobfuse-mount) de amostra estão disponíveis para executar renderizações de V-Ray autónomas usando um sistema de ficheiros blobfuse e podem ser usados como base para modelos para outras aplicações.

### <a name="accessing-files"></a>Aceder a ficheiros

As tarefas de trabalho especificam caminhos para ficheiros de entrada e ficheiros de saída utilizando o sistema de ficheiros montado.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar ficheiros de ativos de entrada do armazenamento de blob para VMs de piscina de lote

Como os ficheiros são simplesmente bolhas no Armazenamento Azure, então as APIs, ferramentas e UIs padrão podem ser usadas para copiar ficheiros entre um sistema de ficheiros no local e armazenamento de bolhas; por exemplo, azcopy, Storage Explorer, Batch Explorer, etc.

## <a name="using-azure-files-with-windows-vms"></a>Utilização de ficheiros Azure com VMs windows

[O Azure Files](https://docs.microsoft.com/azure/storage/files/storage-files-introduction) oferece partilhas de ficheiros totalmente geridas na nuvem que são acessíveis através do protocolo SMB.  O Azure Files baseia-se no armazenamento de blob Azure; é [rentável](https://azure.microsoft.com/pricing/details/storage/files/) e pode ser configurado com replicação de dados para outra região tão redundante a nível global.  Os alvos de escala devem ser [revistos](https://docs.microsoft.com/azure/storage/files/storage-files-scale-targets#azure-files-scale-targets) para determinar se os Ficheiros Azure devem ser utilizados dado o tamanho do pool previsto e o número de ficheiros de ativos.

Há uma [publicação de blog](https://blogs.msdn.microsoft.com/windowsazurestorage/2014/05/26/persisting-connections-to-microsoft-azure-files/) e [documentação](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows) que cobre como montar uma partilha de Ficheiros Azure.

### <a name="mounting-an-azure-files-share"></a>Montagem de uma partilha de Ficheiros Azure

Para utilizar em Lote, é necessário realizar uma operação de montagem sempre que uma tarefa em execução, uma vez que não é possível persistir a ligação entre tarefas.  A maneira mais fácil de o fazer é usar a cmdkey para persistir credenciais utilizando a tarefa de início na configuração da piscina e, em seguida, montar a parte antes de cada tarefa.

Exemplo de utilização de cmdkey num modelo de piscina (escapado para utilização no ficheiro JSON) – note que ao separar a chamada cmdkey da chamada de utilização da rede, o contexto do utilizador para a tarefa de início deve ser o mesmo que o utilizado para executar as tarefas:

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

Exemplo da linha de comando de tarefas:
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

As tarefas de trabalho especificam caminhos para ficheiros de entrada e ficheiros de saída utilizando o sistema de ficheiros montado, utilizando uma unidade mapeada ou um caminho unc.

### <a name="copying-input-asset-files-from-blob-storage-to-batch-pool-vms"></a>Copiar ficheiros de ativos de entrada do armazenamento de blob para VMs de piscina de lote

Os Ficheiros Azure são suportados por todas as principais APIs e ferramentas que têm suporte de armazenamento Azure; por exemplo, azcopy, Azure CLI, Storage Explorer, Azure PowerShell, Batch Explorer, etc.

[O Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) está disponível para sincronizar automaticamente ficheiros entre um sistema de ficheiros no local e uma partilha de Ficheiros Azure.

## <a name="next-steps"></a>Próximos passos

Para obter mais informações sobre as opções de armazenamento consulte a documentação aprofundada:

* [Armazenamento de blob azure](https://docs.microsoft.com/azure/storage/blobs/storage-blobs-introduction)
* [Blobfuse](https://docs.microsoft.com/azure/storage/blobs/storage-how-to-mount-container-linux)
* [Ficheiros do Azure](https://docs.microsoft.com/azure/storage/files/storage-files-introduction)
