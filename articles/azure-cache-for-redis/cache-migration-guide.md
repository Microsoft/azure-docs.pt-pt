---
title: Migrar para a Cache do Azure para Redis
description: Saiba como migrar a sua cache existente para Azure Cache para Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/22/2020
ms.author: yegu
ms.openlocfilehash: d63cafb32dc1db0a901ed3e6004446b450db10c7
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102451528"
---
# <a name="migrate-to-azure-cache-for-redis"></a>Migrar para a Cache do Azure para Redis
Este artigo descreve uma série de abordagens para migrar uma cache Redis existente que corre no local ou em outro serviço de nuvem para Azure Cache para Redis.

## <a name="migration-scenarios"></a>Cenários de migração
Redis de código aberto pode funcionar em muitos ambientes computacional. Exemplos comuns incluem:

- No local - **Caches** Redis em execução em datacenters privados.
- **VMs baseados em nuvem** - Caches Redis em execução em VMs Azure, AWS EC2, e assim por diante.
- **Serviços de hospedagem** - Serviços de Redis geridos como a AWS ElastiCache.
- **Diferentes regiões** - Caches Redis localizados em outra região de Azure.

Se tiver tal cache, poderá movê-lo para Azure Cache para Redis com interrupção mínima ou tempo de inatividade.

## <a name="migration-options"></a>Opções de migração

Há diferentes maneiras de mudar de um cache para outro. Dependendo de onde está a sua cache e de como a sua aplicação interage com ela, um método será mais útil do que os outros. Algumas das estratégias de migração frequentemente utilizadas são detalhadas abaixo.

   | Opção       | Vantagens | Desvantagens |
   | ------------ | ---------- | ------------- |
   | Criar uma nova cache | Mais simples de implementar. | Precisa de repovoar dados para a nova cache, que pode não funcionar com muitas aplicações. |
   | Dados de exportação e importação através de ficheiro RDB | Compatível com qualquer cache Redis em geral. | Alguns dados podem ser perdidos, se forem escritos para a cache existente após a geração do ficheiro RDB. | 
   | Dados de dupla escrita para dois caches | Sem perda de dados ou tempo de inatividade. Operações ininterruptas da cache existente. Teste mais fácil da nova cache. | Precisa de dois caches por um longo período de tempo. | 
   | Migrar dados programáticamente | Controlo total sobre a forma como os dados são movidos. | Requer código personalizado. | 

### <a name="create-a-new-azure-cache-for-redis"></a>Criar um novo Azure Cache para Redis

Tecnicamente, esta abordagem não é uma migração. Se perder dados não é uma preocupação, a maneira mais fácil de se mudar para Azure Cache para Redis é criar cache e ligar a sua aplicação ao mesmo. Por exemplo, se usar o Redis como uma cache de registos de bases de dados, pode facilmente reconstruir a cache do zero.

As medidas gerais para implementar esta opção são:

1. Crie um novo Azure Cache para a instância Redis.

2. Atualize a sua aplicação para utilizar a nova instância.

3. Apague a velha instância do Redis.

### <a name="export-data-to-an-rdb-file-and-import-it-into-azure-cache-for-redis"></a>Dados de exportação para um ficheiro RDB e importá-lo em Azure Cache para Redis

O Redis de código aberto define um mecanismo padrão para tirar uma foto do conjunto de dados de memória de uma cache e guardá-lo para um ficheiro. Este ficheiro, chamado RDB, pode ser lido por outra cache redis. [A azure Cache para o nível premium Redis](cache-overview.md#service-tiers) suporta a importação de dados em uma instância de cache através de ficheiros RDB. Pode utilizar um ficheiro RDB para transferir dados de uma cache existente para Azure Cache para Redis.

> [!IMPORTANT]
> O formato de ficheiro RDB pode ser alterado entre as versões Redis e pode não manter a retrocompatibilidade. A versão Redis da cache de onde está a exportar deve ser igual ou inferior à versão fornecida pela Azure Cache para o Redis.
>

As medidas gerais para implementar esta opção são:

1. Crie uma nova Cache Azure para a instância Redis no nível premium que é do mesmo tamanho (ou maior que) a cache existente.

2. Guarde uma foto da cache redis existente. Pode [configurar o Redis para guardar instantâneos](https://redis.io/topics/persistence) periodicamente, ou executar o processo manualmente utilizando os comandos [SAVE](https://redis.io/commands/save) ou [BGSAVE.](https://redis.io/commands/bgsave) O ficheiro RDB é denominado "dump.rdb" por predefinição e ficará localizado no caminho especificado no ficheiro de configuração *redis.conf.*

    > [!NOTE]
    > Se estiver a migrar dados dentro da Cache Azure para Redis, consulte [estas instruções sobre como exportar um ficheiro RDB](cache-how-to-import-export-data.md) ou utilize o [cmdlet PowerShell Export.](/powershell/module/azurerm.rediscache/export-azurermrediscache)
    >

3. Copie o ficheiro RDB para uma conta de armazenamento Azure na região onde está localizado o seu novo cache. Pode utilizar o AzCopy para esta tarefa.

4. Importe o ficheiro RDB na nova cache utilizando estas [instruções de importação](cache-how-to-import-export-data.md) ou o [cmdlet PowerShell Import](/powershell/module/azurerm.rediscache/import-azurermrediscache).

5. Atualize a sua aplicação para utilizar a nova instância cache.

### <a name="write-to-two-redis-caches-simultaneously-during-migration-period"></a>Escreva para dois caches Redis simultaneamente durante o período de migração

Em vez de mover dados diretamente entre caches, pode usar a sua aplicação para escrever dados tanto para uma cache existente como para um novo que está a configurar. A aplicação ainda irá ler os dados da cache existente inicialmente. Quando a nova cache tiver os dados necessários, mude a aplicação para essa cache e retire a antiga. Digamos, por exemplo, que usa o Redis como loja de sessão e as sessões de aplicação são válidas por sete dias. Depois de escrever para os dois caches durante uma semana, você terá certeza que a nova cache contém todas as informações de sessão não expiradas. Pode confiar com segurança nele a partir desse ponto sem se preocupar com a perda de dados.

As medidas gerais para implementar esta opção são:

1. Crie uma nova Cache Azure para a instância Redis no nível premium que é do mesmo tamanho (ou maior que) a cache existente.

2. Modifique o código de aplicação para escrever tanto para as instâncias novas como para as instâncias originais.

3. Continuar a ler Dados da instância original até que a nova instância seja suficientemente preenchida com dados.

4. Atualizar o código de aplicação para ler e escrever apenas a partir da nova instância.

5. Apague a instância original.

### <a name="migrate-programmatically"></a>Migrar programáticamente

Pode criar um processo de migração personalizado lendo programáticamente dados a partir de uma cache existente e escrevendo-os em Azure Cache para Redis. Esta [ferramenta de código aberto](https://github.com/deepakverma/redis-copy) pode ser usada para copiar dados de uma Cache Azure para redis para outra. Esta ferramenta é útil para mover dados entre instâncias de cache em diferentes regiões de Cache Azure. Uma [versão compilada](https://github.com/deepakverma/redis-copy/releases/download/alpha/Release.zip) também está disponível. Também pode achar o código fonte um guia útil para escrever a sua própria ferramenta de migração.

> [!NOTE]
> Esta ferramenta não é oficialmente suportada pela Microsoft. 
>

As medidas gerais para implementar esta opção são:

1. Crie um VM na região onde se encontra a cache existente. Se o seu conjunto de dados for grande, escolha um VM relativamente potente para reduzir o tempo de cópia.

2. Crie um novo Azure Cache para a instância Redis.

3. Retire os dados da nova cache para garantir que está vazio. Este passo é necessário porque a própria ferramenta de cópia não substitui qualquer chave existente na cache alvo.

    > [!IMPORTANT]
    > Certifique-se de que não se lava a partir da cache de origem.
    >

4. Utilize uma aplicação como a ferramenta de código aberto acima para automatizar a cópia dos dados da cache de origem para o alvo. Lembre-se que o processo de cópia pode demorar algum tempo a ser concluído dependendo do tamanho do seu conjunto de dados.

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço redis](cache-overview.md#service-tiers)
* [Dados de importação](cache-how-to-import-export-data.md#import)