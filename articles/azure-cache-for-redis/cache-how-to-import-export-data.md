---
title: Importar e exportar dados no cache do Azure para Redis
description: Saiba como importar e exportar dados de e para o armazenamento de BLOBs com seu cache do Azure Premium para instâncias de Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 29ad5ca6c9058b88a539c7a3bb8ace4d9a65083a
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/24/2020
ms.locfileid: "76714519"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importar e exportar dados no cache do Azure para Redis
A importação/exportação é um cache do Azure para a operação de gerenciamento de dados Redis, que permite que você importe dados para o cache do Azure para Redis ou exporte dados do cache do Azure para Redis importando e exportando um instantâneo do RDB (cache do Azure para o Redis de armazenamento) de um cache Premium para um blob em uma conta de repositório do Azure.

- **Exportação** - pode exportar o seu Cache Azure para fotos Redis RDB para uma Página Blob.
- **Importar** - pode importar o seu Cache Azure para fotos Redis RDB a partir de uma Blob page ou de um Block Blob.

A importação/exportação permite que você migre entre diferentes cache do Azure para instâncias Redis ou preencha o cache com os dados antes de usá-los.

Este artigo fornece um guia para importar e exportar dados com o cache do Azure para Redis e fornece as respostas para as perguntas mais frequentes.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches [de nível premium.](cache-premium-tier-intro.md)
>
>

## <a name="import"></a>Importar
A importação pode ser usada para colocar arquivos de RDB compatíveis com Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer provedor de nuvem, como Amazon Web Services e outros. A importação de dados é uma maneira fácil de criar um cache com dados preenchidos previamente. Durante o processo de importação, o cache do Azure para Redis carrega os arquivos de RDB do armazenamento do Azure na memória e, em seguida, insere as chaves no cache.

> [!NOTE]
> Antes de iniciar a operação de importação, verifique se os arquivos RDB (banco de dados Redis) estão carregados em blobs de página ou de bloco no armazenamento do Azure, na mesma região e assinatura que o cache do Azure para a instância Redis. Para mais informações, consulte [Começar com o armazenamento Azure Blob](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Se exportou o seu ficheiro RDB utilizando a funcionalidade [Azure Cache for Redis Export,](#export) o seu ficheiro RDB já está armazenado numa página blob e está pronto para importar.
>
>

1. Para importar uma ou mais bolhas de cache exportadas, [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal Azure e clique em importar **dados** do **menu Recurso**.

    ![Importar dados](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Clique **em Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados para importar.

    ![Escolha a conta de armazenamento](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Clique no contêiner que contém os dados a serem importados.

    ![Escolher contêiner](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Selecione uma ou mais bolhas para importar clicando na área à esquerda do nome blob e, em seguida, clique em **Select**.

    ![Escolher BLOBs](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Clique em **Importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > O cache não é acessível por clientes de cache durante o processo de importação e todos os dados existentes no cache são excluídos.
   >
   >

    ![Importar](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Pode acompanhar o progresso da operação de importação seguindo as notificações do portal Azure ou visualizando os acontecimentos no [registo de auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Progresso da importação](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportar
A exportação permite exportar os dados armazenados no cache do Azure para Redis para arquivos RDB compatíveis com o Redis. Você pode usar esse recurso para mover dados de um cache do Azure para instância Redis para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda o cache do Azure para a instância do servidor Redis e o arquivo é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com um status de êxito ou falha, o arquivo temporário será excluído.

1. Para exportar o conteúdo atual da cache para armazenamento, [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal Azure e clique em exportar **dados** do **menu Recurso**.

    ![Escolher contêiner de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Clique **em Escolher o Recipiente de Armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar na mesma assinatura e região que o cache.

   > [!IMPORTANT]
   > A exportação funciona com blobs de páginas, que têm suporte em contas de armazenamento clássicas e do Resource Manager, mas que não têm suporte nas contas de armazenamento de BLOBs no momento. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
   >

    ![Conta de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Escolha o recipiente de bolhas desejado e clique **em Selecionar**. Para utilizar um recipiente novo, clique em **Adicionar recipiente** para o adicionar primeiro e, em seguida, selecioná-lo a partir da lista.

    ![Escolher contêiner de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Digite um prefixo de **nome Blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo do nome do blob é usado para prefixar os nomes dos arquivos gerados por essa operação de exportação.

    ![Exportar](./media/cache-how-to-import-export-data/cache-export-data.png)

    Pode acompanhar o progresso da operação de exportação seguindo as notificações do portal Azure, ou visualizando os eventos no [registo de auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Exportação de dados concluída](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Os caches permanecem disponíveis para uso durante o processo de exportação.

## <a name="importexport-faq"></a>Perguntas frequentes sobre importação/exportação
Esta seção contém perguntas frequentes sobre o recurso de importação/exportação.

* [Que níveis de preços podem utilizar import/exportação?](#what-pricing-tiers-can-use-importexport)
* [Posso importar dados de qualquer servidor redis?](#can-i-import-data-from-any-redis-server)
* [Que versões RDB posso importar?](#what-rdb-versions-can-i-import)
* [A minha cache está disponível durante uma operação de importação/exportação?](#is-my-cache-available-during-an-importexport-operation)
* [Posso usar import/exportação com cluster Redis?](#can-i-use-importexport-with-redis-cluster)
* [Como funciona a Importação/Exportação com uma definição de bases de dados personalizada?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Como é que a Importação/Exportação é diferente da persistência do Redis?](#how-is-importexport-different-from-redis-persistence)
* [Posso automatizar a Importação/Exportação utilizando powerShell, CLI ou outros clientes de gestão?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Recebi um erro de tempo durante a minha operação de importação/exportação. O que é que isso significa?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Tive um erro ao exportar os meus dados para o Armazém Azure Blob. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Quais tipos de preço podem usar a importação/exportação?
A importação/exportação está disponível apenas no tipo de preço premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Posso importar dados de qualquer servidor Redis?
Sim, além de importar dados exportados do cache do Azure para instâncias Redis, você pode importar arquivos RDB de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, como Linux, Windows ou provedores de nuvem, como o Amazon Web Services. Para fazer isso, carregue o arquivo RDB do servidor Redis desejado em um blob de página ou de bloco em uma conta de armazenamento do Azure e importe-o para o cache do Azure Premium para a instância Redis. Por exemplo, talvez você queira exportar os dados do cache de produção e importá-los em um cache usado como parte de um ambiente de preparo para teste ou migração.

> [!IMPORTANT]
> Para importar com êxito os dados exportados de servidores Redis que não sejam o cache do Azure para Redis ao usar um blob de páginas, o tamanho do blob de páginas deve ser alinhado em um limite de 512 bytes. Para que o código da amostra efetue qualquer preenchimento de byte necessário, consulte o upload da amostra de [blob](https://github.com/JimRoberts-MS/SamplePageBlobUpload)da página .
>
>

### <a name="what-rdb-versions-can-i-import"></a>Quais versões do RDB posso importar?

O cache do Azure para Redis dá suporte à importação de RDB por meio do RDB versão 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>O cache está disponível durante uma operação de importação/exportação?
* **Exportação** - Os caches permanecem disponíveis e pode continuar a utilizar o seu cache durante uma operação de exportação.
* **Importação** - Os caches ficam indisponíveis quando uma operação de importação começa e ficam disponíveis para utilização quando a operação de importação estiver concluída.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Posso usar a importação/exportação com o cluster Redis?
Sim, e você pode importar/exportar entre um cache clusterizado e um cache não clusterizado. Uma vez que o cluster Redis apenas suporta a [base de dados 0,](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)quaisquer dados em bases de dados que não 0 não são importados. Quando os dados de cache clusterizados são importados, as chaves são redistribuídas entre os fragmentos do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como a importação/exportação funciona com uma configuração de bancos de dados personalizados?
Alguns níveis de preços têm [diferentes limites](cache-configure.md#databases)de bases de dados, pelo que existem algumas considerações ao importar se configurar um valor personalizado para a configuração `databases` durante a criação de cache.

* Ao importar para um nível de preços com um limite de `databases` inferior ao de onde exportou:
  * Se estiver a utilizar o número padrão de `databases`, que é 16 para todos os níveis de preços, não se perdem dados.
  * Se estiver a utilizar um número personalizado de `databases` que se insere dentro dos limites para o nível a que está a importar, não se perdem dados.
  * Se os dados exportados contiverem dados em um banco de dado que exceda os limites da nova camada, os dados desses bancos mais altos não serão importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Como a importação/exportação é diferente da persistência do Redis?
O cache do Azure para persistência de Redis permite que você persista os dados armazenados no Redis no armazenamento do Azure. Quando a persistência é configurada, o cache do Azure para Redis persiste um instantâneo do cache do Azure para Redis em um formato binário Redis para o disco com base em uma frequência de backup configurável. Se ocorrer um evento catastrófico que desabilite o cache primário e de réplica, os dados de cache serão restaurados automaticamente usando o instantâneo mais recente. Para mais informações, consulte [Como configurar a persistência de dados para um Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).

A importação/exportação permite que você coloque dados ou exporte-os do cache do Azure para Redis. Ele não configura o backup e a restauração usando a persistência Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Posso automatizar a importação/exportação usando o PowerShell, a CLI ou outros clientes de gerenciamento?
Sim, para instruções da PowerShell ver [importar um Cache Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) e [exportar um Azure Cache para Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo limite durante minha operação de importação/exportação. O que isso significa?
Se permanecer na lâmina de **dados de importação** ou **exportação** por mais de 15 minutos antes de começar a operação, receberá um erro com uma mensagem de erro semelhante ao seguinte exemplo:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver isso, inicie a operação de importação ou exportação antes de 15 minutos.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Recebi um erro ao exportar meus dados para o armazenamento de BLOBs do Azure. O que aconteceu?
A exportação funciona somente com arquivos RDB armazenados como BLOBs de páginas. Atualmente, não há suporte para outros tipos de BLOB, incluindo contas de armazenamento de BLOBs com camadas quentes e frias. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Passos Seguintes
Saiba como usar mais recursos de cache Premium.

* [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md)
