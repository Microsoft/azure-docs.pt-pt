---
title: Importar e exportar dados no cache do Azure para Redis
description: Saiba como importar e exportar dados de e para o armazenamento de BLOBs com seu cache do Azure Premium para instâncias de Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: df8b65fcb4dd2b9622716052be2df59cbfa5424a
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/15/2020
ms.locfileid: "75971823"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importar e exportar dados no cache do Azure para Redis
A importação/exportação é um cache do Azure para a operação de gerenciamento de dados Redis, que permite que você importe dados para o cache do Azure para Redis ou exporte dados do cache do Azure para Redis importando e exportando um instantâneo do RDB (cache do Azure para o Redis de armazenamento) de um cache Premium para um blob em uma conta de repositório do Azure.

- **Exportar** – você pode exportar o cache do Azure para instantâneos do RDB Redis para um blob de páginas.
- **Importar** – você pode importar o cache do Azure para instantâneos do RDB Redis de um blob de páginas ou de um blob de blocos.

A importação/exportação permite que você migre entre diferentes cache do Azure para instâncias Redis ou preencha o cache com os dados antes de usá-los.

Este artigo fornece um guia para importar e exportar dados com o cache do Azure para Redis e fornece as respostas para as perguntas mais frequentes.

> [!IMPORTANT]
> A importação/exportação está em visualização e só está disponível para caches da [camada Premium](cache-premium-tier-intro.md) .
>
>

## <a name="import"></a>Importar
A importação pode ser usada para colocar arquivos de RDB compatíveis com Redis de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, incluindo Redis em execução no Linux, Windows ou qualquer provedor de nuvem, como Amazon Web Services e outros. A importação de dados é uma maneira fácil de criar um cache com dados preenchidos previamente. Durante o processo de importação, o cache do Azure para Redis carrega os arquivos de RDB do armazenamento do Azure na memória e, em seguida, insere as chaves no cache.

> [!NOTE]
> Antes de iniciar a operação de importação, verifique se os arquivos RDB (banco de dados Redis) estão carregados em blobs de página ou de bloco no armazenamento do Azure, na mesma região e assinatura que o cache do Azure para a instância Redis. Para obter mais informações, consulte Introdução [ao armazenamento de BLOBs do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Se você exportou o arquivo RDB usando o recurso de [exportação do cache do Azure para Redis](#export) , o arquivo RDB já estará armazenado em um blob de páginas e estará pronto para importação.
>
>

1. Para importar um ou mais blobs de cache exportados, [navegue até o cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal do Azure e clique em **importar dados** no **menu de recursos**.

    ![Importar dados](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Clique em **escolher BLOB (s)** e selecione a conta de armazenamento que contém os dados a serem importados.

    ![Escolher conta de armazenamento](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Clique no contêiner que contém os dados a serem importados.

    ![Escolher contêiner](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Selecione um ou mais BLOBs para importar clicando na área à esquerda do nome do blob e, em seguida, clique em **selecionar**.

    ![Escolher BLOBs](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Clique em **importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > O cache não é acessível por clientes de cache durante o processo de importação e todos os dados existentes no cache são excluídos.
   >
   >

    ![Importar](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Você pode monitorar o progresso da operação de importação seguindo as notificações do portal do Azure ou exibindo os eventos no [log de auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Progresso da importação](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportar
A exportação permite exportar os dados armazenados no cache do Azure para Redis para arquivos RDB compatíveis com o Redis. Você pode usar esse recurso para mover dados de um cache do Azure para instância Redis para outro ou para outro servidor Redis. Durante o processo de exportação, um arquivo temporário é criado na VM que hospeda o cache do Azure para a instância do servidor Redis e o arquivo é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com um status de êxito ou falha, o arquivo temporário será excluído.

1. Para exportar o conteúdo atual do cache para o armazenamento, [navegue até o cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal do Azure e clique em **exportar dados** no **menu de recursos**.

    ![Escolher contêiner de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Clique em **escolher contêiner de armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar na mesma assinatura e região que o cache.

   > [!IMPORTANT]
   > A exportação funciona com blobs de páginas, que têm suporte em contas de armazenamento clássicas e do Resource Manager, mas que não têm suporte nas contas de armazenamento de BLOBs no momento. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
   >

    ![Conta de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Escolha o contêiner de BLOBs desejado e clique em **selecionar**. Para usar um novo contêiner, clique em **Adicionar contêiner** para adicioná-lo primeiro e, em seguida, selecione-o na lista.

    ![Escolher contêiner de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Digite um **prefixo de nome de blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo do nome do blob é usado para prefixar os nomes dos arquivos gerados por essa operação de exportação.

    ![Exportar](./media/cache-how-to-import-export-data/cache-export-data.png)

    Você pode monitorar o progresso da operação de exportação seguindo as notificações do portal do Azure ou exibindo os eventos no [log de auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Exportação de dados concluída](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Os caches permanecem disponíveis para uso durante o processo de exportação.

## <a name="importexport-faq"></a>Perguntas frequentes sobre importação/exportação
Esta seção contém perguntas frequentes sobre o recurso de importação/exportação.

* [Quais tipos de preço podem usar a importação/exportação?](#what-pricing-tiers-can-use-importexport)
* [Posso importar dados de qualquer servidor Redis?](#can-i-import-data-from-any-redis-server)
* [Quais versões do RDB posso importar?](#what-rdb-versions-can-i-import)
* [O cache está disponível durante uma operação de importação/exportação?](#is-my-cache-available-during-an-importexport-operation)
* [Posso usar a importação/exportação com o cluster Redis?](#can-i-use-importexport-with-redis-cluster)
* [Como a importação/exportação funciona com uma configuração de bancos de dados personalizados?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Como a importação/exportação é diferente da persistência do Redis?](#how-is-importexport-different-from-redis-persistence)
* [Posso automatizar a importação/exportação usando o PowerShell, a CLI ou outros clientes de gerenciamento?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Recebi um erro de tempo limite durante minha operação de importação/exportação. O que isso significa?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Recebi um erro ao exportar meus dados para o armazenamento de BLOBs do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Quais tipos de preço podem usar a importação/exportação?
A importação/exportação está disponível apenas no tipo de preço premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Posso importar dados de qualquer servidor Redis?
Sim, além de importar dados exportados do cache do Azure para instâncias Redis, você pode importar arquivos RDB de qualquer servidor Redis em execução em qualquer nuvem ou ambiente, como Linux, Windows ou provedores de nuvem, como o Amazon Web Services. Para fazer isso, carregue o arquivo RDB do servidor Redis desejado em um blob de página ou de bloco em uma conta de armazenamento do Azure e importe-o para o cache do Azure Premium para a instância Redis. Por exemplo, talvez você queira exportar os dados do cache de produção e importá-los em um cache usado como parte de um ambiente de preparo para teste ou migração.

> [!IMPORTANT]
> Para importar com êxito os dados exportados de servidores Redis que não sejam o cache do Azure para Redis ao usar um blob de páginas, o tamanho do blob de páginas deve ser alinhado em um limite de 512 bytes. Para obter um exemplo de código para executar qualquer preenchimento de byte necessário, consulte [upload de blob de página de exemplo](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Quais versões do RDB posso importar?

O cache do Azure para Redis dá suporte à importação de RDB por meio do RDB versão 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>O cache está disponível durante uma operação de importação/exportação?
* Os caches de **exportação** permanecem disponíveis e você pode continuar a usar o cache durante uma operação de exportação.
* **Importar** – os caches tornam-se indisponíveis quando uma operação de importação é iniciada e ficam disponíveis para uso quando a operação de importação é concluída.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Posso usar a importação/exportação com o cluster Redis?
Sim, e você pode importar/exportar entre um cache clusterizado e um cache não clusterizado. Como o cluster Redis [dá suporte apenas ao banco](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)de dados 0, todos os bancos de dado que não sejam 0 não são importados. Quando os dados de cache clusterizados são importados, as chaves são redistribuídas entre os fragmentos do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como a importação/exportação funciona com uma configuração de bancos de dados personalizados?
Alguns tipos de preço têm [limites de bancos de dados](cache-configure.md#databases)diferentes, portanto, há algumas considerações ao importar se você configurou um valor personalizado para a configuração de `databases` durante a criação do cache.

* Ao importar para um tipo de preço com um limite de `databases` menor do que a camada da qual você exportou:
  * Se você estiver usando o número padrão de `databases`, que é 16 para todos os tipos de preço, nenhum dado será perdido.
  * Se você estiver usando um número personalizado de `databases` que esteja dentro dos limites da camada para a qual você está importando, nenhum dado será perdido.
  * Se os dados exportados contiverem dados em um banco de dado que exceda os limites da nova camada, os dados desses bancos mais altos não serão importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Como a importação/exportação é diferente da persistência do Redis?
O cache do Azure para persistência de Redis permite que você persista os dados armazenados no Redis no armazenamento do Azure. Quando a persistência é configurada, o cache do Azure para Redis persiste um instantâneo do cache do Azure para Redis em um formato binário Redis para o disco com base em uma frequência de backup configurável. Se ocorrer um evento catastrófico que desabilite o cache primário e de réplica, os dados de cache serão restaurados automaticamente usando o instantâneo mais recente. Para obter mais informações, consulte [como configurar a persistência de dados para um cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).

A importação/exportação permite que você coloque dados ou exporte-os do cache do Azure para Redis. Ele não configura o backup e a restauração usando a persistência Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Posso automatizar a importação/exportação usando o PowerShell, a CLI ou outros clientes de gerenciamento?
Sim, para obter instruções do PowerShell, consulte [importar um cache do Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) e [exportar um cache do Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo limite durante minha operação de importação/exportação. O que isso significa?
Se você permanecer na folha **importar dados** ou **exportar dados** por mais de 15 minutos antes de iniciar a operação, você receberá um erro com uma mensagem de erro semelhante ao exemplo a seguir:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver isso, inicie a operação de importação ou exportação antes de 15 minutos.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Recebi um erro ao exportar meus dados para o armazenamento de BLOBs do Azure. O que aconteceu?
A exportação funciona somente com arquivos RDB armazenados como BLOBs de páginas. Atualmente, não há suporte para outros tipos de BLOB, incluindo contas de armazenamento de BLOBs com camadas quentes e frias. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais recursos de cache Premium.

* [Introdução ao cache do Azure para a camada Premium do Redis](cache-premium-tier-intro.md)
