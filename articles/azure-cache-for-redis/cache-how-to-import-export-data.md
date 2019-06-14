---
title: Importar e exportar os dados na Cache do Azure de Redis | Documentos da Microsoft
description: Saiba como importar e exportar dados para e do armazenamento de Blobs com o premium Cache do Azure para instâncias de Redis
services: cache
documentationcenter: ''
author: yegu-ms
manager: jhubbard
editor: ''
ms.assetid: 4a68ac38-87af-4075-adab-569d37d7cc9e
ms.service: cache
ms.workload: tbd
ms.tgt_pltfrm: cache
ms.devlang: na
ms.topic: article
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: dfa8b47ced70386efa1daa44af318f1da55f49e1
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60542346"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Importar e exportar os dados na Cache do Azure de Redis
Importar/exportar é uma Cache do Azure para a operação de gestão dados de Redis, que permite-lhe importar dados para a Cache de Redis do Azure ou exportar dados a partir do Azure Cache de Redis por importar e exportar uma Cache do Azure para o instantâneo de base de dados de Redis (RDB) de uma cache premium para um BLOBs numa conta de armazenamento do Azure. 

- **Exportar** -é possível exportar sua Cache do Azure para instantâneos de Redis RDB para um Blob de página.
- **Importar** -é possível importar o Cache do Azure para instantâneos de Redis RDB de um Blob de página ou um Blob de blocos.

Importar/exportar permite-lhe migrar entre diferentes a Cache do Azure para instâncias de Redis ou povoar a cache com os dados antes de utilização.

Este artigo fornece um guia para importar e exportar dados com o Azure Cache de Redis e as respostas às perguntas mais frequentes.

> [!IMPORTANT]
> Importar/exportar está em pré-visualização e só está disponível para [escalão premium](cache-premium-tier-intro.md) coloca em cache.
>
>

## <a name="import"></a>Importar
Importação pode ser utilizada para juntar o ficheiros RDB compatíveis de Redis a partir de qualquer servidor Redis em execução em qualquer cloud ou o ambiente, incluindo o Redis em execução no Linux, Windows ou qualquer fornecedor de cloud, como Amazon Web Services e outras pessoas. A importação de dados é uma forma fácil de criar uma cache com dados preenchidos previamente. Durante o processo de importação, a Cache de Redis do Azure carrega os ficheiros RDB do armazenamento do Azure na memória e, em seguida, insere as chaves na cache.

> [!NOTE]
> Antes de iniciar a operação de importação, certifique-se de que o ficheiro de base de dados de Redis (RDB) ou ficheiros são carregados nos blobs de página ou bloco no armazenamento do Azure, na mesma região e subscrição como a sua Cache do Azure para a instância de Redis. Para obter mais informações, consulte [introdução ao armazenamento de Blobs do Azure](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Se exportou o ficheiro RDB com o [Cache do Azure para exportação de Redis](#export) funcionalidade, o seu ficheiro RDB já armazenado num blob de página e está pronto para importação.
>
>

1. Para importar um ou mais blobs de cache exportado [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal do Azure e clique em **importar dados** do **menu recursos**.

    ![Importar dados][cache-import-data]
2. Clique em **blob (s) Escolha** e selecione a conta de armazenamento que contém os dados a importar.

    ![Escolha a conta de armazenamento][cache-import-choose-storage-account]
3. Clique no contentor que contém os dados a importar.

    ![Escolher contentor][cache-import-choose-container]
4. Selecione um ou mais blobs para importar clicando a área à esquerda do nome do blob e, em seguida, clique em **selecione**.

    ![Escolher blobs][cache-import-choose-blobs]
5. Clique em **importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > A cache não está acessível aos clientes de cache durante o processo de importação e quaisquer dados existentes na cache são eliminados.
   >
   >

    ![Importar][cache-import-blobs]

    Pode monitorizar o progresso da operação de importação, seguindo as notificações do portal do Azure ou ao visualizar os eventos na [registo de auditoria](../azure-resource-manager/resource-group-audit.md).

    ![Progresso da importação][cache-import-data-import-complete]

## <a name="export"></a>Exportar
Exportação permite-lhe exportar os dados armazenados na Cache do Azure para ficheiros RDB compatíveis do Redis para Redis. Pode utilizar esta funcionalidade para mover dados de um Cache do Azure para a instância de Redis para outro ou para outro servidor de Redis. Durante o processo de exportação, um ficheiro temporário é criado na VM que aloja a Cache do Azure para a instância de servidor Redis e o ficheiro é carregado para a conta de armazenamento designada. Quando a operação de exportação for concluída com o estado de sucesso ou falha, o ficheiro temporário é eliminado.

1. Para exportar o conteúdo atual da cache para armazenamento, [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal do Azure e clique em **exportar dados** do **menu recursos**.

    ![Escolha o contentor de armazenamento][cache-export-data-choose-storage-container]
2. Clique em **escolha o contentor de armazenamento** e selecione a conta de armazenamento pretendido. A conta de armazenamento tem de ser na mesma subscrição e região como seu cache.

   > [!IMPORTANT]
   > Exporte funciona com blobs de páginas, que são suportados pelo clássico e contas de armazenamento do Resource Manager, mas, neste momento, não são suportadas por contas de armazenamento de Blobs. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
   >
   >

    ![Conta de armazenamento][cache-export-data-choose-account]
3. Escolha o contentor de BLOBs pretendido e clique em **selecione**. Para utilizar novos um contentor, clique em **adicionar contentor** para adicioná-lo primeiro e, em seguida, selecione-o na lista.

    ![Escolha o contentor de armazenamento][cache-export-data-container]
4. Introduza um **prefixo do nome do Blob** e clique em **exportar** para iniciar o processo de exportação. O prefixo de nome de blob é utilizado para o prefixo os nomes dos arquivos gerados por esta operação de exportação.

    ![Exportar][cache-export-data]

    Pode monitorizar o progresso da operação de exportação, seguindo as notificações do portal do Azure ou ao visualizar os eventos na [registo de auditoria](../azure-resource-manager/resource-group-audit.md).

    ![Dados de exportação concluída][cache-export-data-export-complete]

    Caches permanecem disponíveis para utilização durante o processo de exportação.

## <a name="importexport-faq"></a>FAQ de importação/exportação
Esta secção contém perguntas freqüentes sobre a funcionalidade de importação/exportação.

* [Os preços camadas podem utilizar a importação/exportação?](#what-pricing-tiers-can-use-importexport)
* [Pode importar dados de qualquer servidor Redis?](#can-i-import-data-from-any-redis-server)
* [Que versões RDB pode importar?](#what-rdb-versions-can-i-import)
* [Minha cache está disponível durante uma operação de importação/exportação?](#is-my-cache-available-during-an-importexport-operation)
* [Pode utilizar para importar/exportar com o cluster de Redis?](#can-i-use-importexport-with-redis-cluster)
* [Como funciona com uma definição de bancos de dados personalizados de importação/exportação?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Como importar/exportar é diferente da persistência de Redis?](#how-is-importexport-different-from-redis-persistence)
* [Pode automatizar com o PowerShell, CLI ou outros clientes de gestão de importação/exportação?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Recebi um erro de tempo limite durante minha operação de importação/exportação. O que significa?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Recebi um erro ao exportar os meus dados para armazenamento de Blobs do Azure. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Os preços camadas podem utilizar a importação/exportação?
Importar/exportar está disponível apenas no escalão de preços premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Pode importar dados de qualquer servidor Redis?
Sim, além de importar dados exportados a partir do Cache do Azure para instâncias de Redis, pode importar ficheiros RDB a partir de qualquer servidor Redis em execução em qualquer cloud ou o ambiente, como Linux, Windows, ou na cloud fornecedores, como o Amazon Web Services. Para fazer isso, carregue o ficheiro RDB do servidor Redis desejado num página ou blob de blocos numa conta de armazenamento do Azure e, em seguida, importe-o para o premium Cache do Azure para a instância de Redis. Por exemplo, poderá exportar os dados da sua cache de produção e importe-o para uma cache utilizada como parte de um ambiente de teste para testar ou de migração.

> [!IMPORTANT]
> Para importar com êxito os dados exportados a partir de servidores Redis que não seja o Cache do Azure para Redis quando utilizar um blob de página, o tamanho do blob de página deve obrigatoriamente estar alinhado num limite de 512 bytes. Para o código de exemplo executar qualquer preenchimento de byte necessárias, consulte [carregamento de BLOBs de página de exemplo](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
> 
> 

### <a name="what-rdb-versions-can-i-import"></a>Que versões RDB pode importar?

A Cache de Redis do Azure suporta a importação da RDB da cópia de segurança por meio da RDB da versão 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>Minha cache está disponível durante uma operação de importação/exportação?
* **Exportar** - Caches continuam disponíveis e pode continuar a utilizar a sua cache durante uma operação de exportação.
* **Importar** - Caches fiquem indisponíveis quando uma operação de importação é iniciado e se tornar disponível para utilização quando a operação de importação for concluída.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Pode utilizar para importar/exportar com o cluster de Redis?
Sim, e pode importar/exportar entre um cache em cluster e uma cache não-clusterizados. Desde o cluster de Redis [só suporta a base de dados 0](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering), todos os dados em bancos de dados diferente de 0 não estão a ser importados. Quando os dados em cache em cluster são importados, as chaves são redistribuídas entre as partições horizontais do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como funciona com uma definição de bancos de dados personalizados de importação/exportação?
Alguns escalões de preço possuem diferentes [limites de bases de dados](cache-configure.md#databases), por isso, existem algumas considerações ao importar se tiver configurado um valor personalizado para o `databases` definir durante a criação da cache.

* Ao importar para um escalão de preço com um menor `databases` limite que o escalão a partir do qual exportou:
  * Se estiver a utilizar o número predefinido de `databases`, que é 16 para todos os escalões de preço, nenhum dado seja perdido.
  * Se estiver a utilizar um número personalizado de `databases` esse cai dentro dos limites para a camada a que estiver a importar, nenhum dado seja perdido.
  * Se os dados exportados continha dados numa base de dados que exceda os limites da nova camada, os dados desses bancos de dados superior não são importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Como importar/exportar é diferente da persistência de Redis?
A Cache do Azure para a persistência de Redis permite-lhe manter os dados armazenados no Redis ao armazenamento do Azure. Quando a persistência é configurada, a Cache de Redis do Azure mantém um instantâneo da Cache do Azure para Redis num formato binário do Redis para o disco com base numa frequência de cópia de segurança configurável. Se um evento catastrófico ocorrer que desativa a cache principal e de réplica, os dados de cache são restaurados automaticamente usando o instantâneo mais recente. Para obter mais informações, consulte [como configurar a persistência de dados para uma Cache do Azure Premium para Redis](cache-how-to-premium-persistence.md).

Importação / exportação permite-lhe trazer dados para ou exportar a partir do Azure Cache de Redis. Não configurar cópia de segurança e restaurar com a persistência de Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Pode automatizar com o PowerShell, CLI ou outros clientes de gestão de importação/exportação?
Sim, para o PowerShell obter instruções, consulte [para importar uma Cache do Azure para Redis](cache-howto-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) e [para exportar uma Cache do Azure para Redis](cache-howto-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo limite durante minha operação de importação/exportação. O que significa?
Se permanecerem no **importar dados** ou **exportar dados** painel há mais do que 15 minutos antes de iniciar a operação, recebe um erro com uma mensagem de erro semelhante ao seguinte exemplo:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver este problema, a iniciar a importação ou a operação antes de 15 minutos de exportação expirou.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Recebi um erro ao exportar os meus dados para armazenamento de Blobs do Azure. O que aconteceu?
Exportação funciona apenas com os ficheiros RDB armazenados como blobs de páginas. Outros tipos de BLOBs não são suportados atualmente, o incluindo contas de armazenamento de Blobs com escalões de acesso frequentes e esporádico. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Passos Seguintes
Saiba como utilizar as funcionalidades de cache premium mais.

* [Introdução à Cache do Azure para o escalão Premium da Redis](cache-premium-tier-intro.md)    

<!-- IMAGES -->
[cache-settings-import-export-menu]: ./media/cache-how-to-import-export-data/cache-settings-import-export-menu.png
[cache-export-data-choose-account]: ./media/cache-how-to-import-export-data/cache-export-data-choose-account.png
[cache-export-data-choose-storage-container]: ./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png
[cache-export-data-container]: ./media/cache-how-to-import-export-data/cache-export-data-container.png
[cache-export-data-export-complete]: ./media/cache-how-to-import-export-data/cache-export-data-export-complete.png
[cache-export-data]: ./media/cache-how-to-import-export-data/cache-export-data.png
[cache-import-data]: ./media/cache-how-to-import-export-data/cache-import-data.png
[cache-import-choose-storage-account]: ./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png
[cache-import-choose-container]: ./media/cache-how-to-import-export-data/cache-import-choose-container.png
[cache-import-choose-blobs]: ./media/cache-how-to-import-export-data/cache-import-choose-blobs.png
[cache-import-blobs]: ./media/cache-how-to-import-export-data/cache-import-blobs.png
[cache-import-data-import-complete]: ./media/cache-how-to-import-export-data/cache-import-data-import-complete.png
