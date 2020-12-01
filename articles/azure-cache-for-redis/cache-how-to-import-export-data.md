---
title: Dados de importação e exportação em Azure Cache para Redis
description: Saiba como importar e exportar dados de e para o armazenamento de bolhas com o seu Azure Cache premium para instâncias Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 9ee3b447b2b5f6dfa8972749c3c46ae01f79bfdc
ms.sourcegitcommit: 4295037553d1e407edeb719a3699f0567ebf4293
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96327513"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Dados de importação e exportação em Azure Cache para Redis
Import/Export é uma operação de gestão de dados Azure Cache para Redis, que permite importar dados para a Azure Cache para Redis ou exportar dados da Azure Cache para Redis importando e exportando uma cache Azure para Redis Database (RDB) de uma cache premium para uma bolha numa Conta de Armazenamento Azure.

- **Exporte** - pode exportar o seu Azure Cache para fotos RDB redis para uma Page Blob.
- **Import** - pode importar o seu Azure Cache para imagens Redis RDB de uma Mancha de Página ou de uma Blob de Bloco.

A Importação/Exportação permite-lhe migrar entre diferentes Cache Azure para instâncias Redis ou povoar a cache com dados antes de usar.

Este artigo fornece um guia para importar e exportar dados com a Azure Cache para redis e fornece as respostas às perguntas comumente colocadas.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches [de nível Premium.](cache-overview.md#service-tiers)
>
>

## <a name="import"></a>Importar
A importação pode ser usada para trazer ficheiros RDB compatíveis com Redis de qualquer servidor Redis em qualquer nuvem ou ambiente, incluindo Redis em execução em Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outros. Importar dados é uma maneira fácil de criar uma cache com dados pré-povoados. Durante o processo de importação, a Azure Cache for Redis carrega os ficheiros RDB do armazenamento Azure na memória e, em seguida, insere as chaves na cache.

> [!NOTE]
> Antes de iniciar a operação de importação, certifique-se de que o seu ficheiro ou ficheiros Redis Database (RDB) são enviados para a página ou bloquear bolhas no armazenamento Azure, na mesma região e subscrição que o seu Azure Cache para a instância Redis. Para obter mais informações, consulte [Começar com o armazenamento Azure Blob](../storage/blobs/storage-quickstart-blobs-dotnet.md). Se exportar o seu ficheiro RDB utilizando a função [Azure Cache for Redis Export,](#export) o seu ficheiro RDB já está armazenado numa bolha de página e está pronto para ser importado.
>
>

1. Para importar uma ou mais bolhas de cache exportadas, [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal Azure e clique em Importar **dados** de importação a partir do **menu Recursos.**

    ![Importar dados](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Clique **em Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados a importar.

    ![Escolha a conta de armazenamento](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Clique no recipiente que contém os dados para importar.

    ![Escolha o recipiente](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Selecione uma ou mais bolhas para importar clicando na área à esquerda do nome blob e, em seguida, clique em **Select**.

    ![Escolha bolhas](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Clique **em Importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > A cache não é acessível por clientes cache durante o processo de importação, e quaisquer dados existentes na cache são eliminados.
   >
   >

    ![Importar](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Pode acompanhar o progresso da operação de importação seguindo as notificações do portal Azure, ou visualizando os eventos no registo de [auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Progresso das importações](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportar
A exportação permite exportar os dados armazenados em Cache Azure para Redis para redis ficheiros RDB compatíveis. Pode utilizar esta funcionalidade para mover dados de um Cache Azure para redis para outro ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário no VM que acolhe a Cache Azure para a instância do servidor Redis, e o ficheiro é enviado para a conta de armazenamento designada. Quando a operação de exportação estiver concluída com um estado de sucesso ou de insucesso, o ficheiro temporário é suprimido.

1. Para exportar o conteúdo atual da cache para armazenamento, [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal Azure e clique em Ler os dados de **Exportação** do **menu Recursos.**

    ![No painel de navegação para contoso5premium, destaca-se a opção de dados de exportação na lista da Administração.](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Clique **em Escolher o Recipiente de Armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar na mesma subscrição e região que o seu cache.

   > [!IMPORTANT]
   > As exportações funcionam com bolhas de página, que são suportadas por contas de armazenamento clássicas e gestoras de recursos, mas não são suportadas pelas contas de armazenamento blob neste momento. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
   >

    ![Conta de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Escolha o recipiente blob desejado e clique em **Select**. Para utilizar um novo recipiente, clique em **Adicionar Recipiente** para o adicionar primeiro e, em seguida, selecione-o da lista.

    ![Em Recipientes para contoso55, destaca-se a opção + Contentor. Há um contentor na lista, cachesaves, e é selecionado e destacado. A opção Seleção é selecionada e destacada.](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Digite um **prefixo de nome Blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo do nome blob é utilizado para pré-fixar os nomes dos ficheiros gerados por esta operação de exportação.

    ![Exportar](./media/cache-how-to-import-export-data/cache-export-data.png)

    Pode acompanhar o progresso da operação de exportação seguindo as notificações do portal Azure, ou visualizando os eventos no registo de [auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Dados de exportação completos](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    As caches permanecem disponíveis para utilização durante o processo de exportação.

## <a name="importexport-faq"></a>Importação/Exportação de FAQ
Esta secção contém perguntas frequentes sobre a funcionalidade Importação/Exportação.

* [Que níveis de preços podem utilizar Import/Export?](#what-pricing-tiers-can-use-importexport)
* [Posso importar dados de qualquer servidor do Redis?](#can-i-import-data-from-any-redis-server)
* [Que versões RDB posso importar?](#what-rdb-versions-can-i-import)
* [O meu cache está disponível durante uma operação de importação/exportação?](#is-my-cache-available-during-an-importexport-operation)
* [Posso utilizar importe/exportação com cluster Redis?](#can-i-use-importexport-with-redis-cluster)
* [Como funciona a Importação/Exportação com uma definição de bases de dados personalizadas?](#how-does-importexport-work-with-a-custom-databases-setting)
* [Como é que a importação/exportação é diferente da persistência de Redis?](#how-is-importexport-different-from-redis-persistence)
* [Posso automatizar a Importação/Exportação utilizando o PowerShell, o CLI ou outros clientes de gestão?](#can-i-automate-importexport-using-powershell-cli-or-other-management-clients)
* [Recebi um erro de tempo limite durante a minha operação de importação/exportação. O que é que isso significa?](#i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean)
* [Tive um erro ao exportar os meus dados para o Azure Blob Storage. O que aconteceu?](#i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened)

### <a name="what-pricing-tiers-can-use-importexport"></a>Que níveis de preços podem utilizar Import/Export?
A importação/exportação só está disponível no nível de preços premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Posso importar dados de qualquer servidor do Redis?
Sim, além de importar dados exportados do Azure Cache para casos Redis, pode importar ficheiros RDB de qualquer servidor Redis em qualquer nuvem ou ambiente, como Linux, Windows ou fornecedores de nuvem como a Amazon Web Services. Para isso, faça o upload do ficheiro RDB do servidor Redis desejado para uma página ou bloco de bolhas numa Conta de Armazenamento Azure e, em seguida, importe-o para o seu Azure Cache premium para a instância Redis. Por exemplo, pode querer exportar os dados da sua cache de produção e importá-lo para uma cache usada como parte de um ambiente de preparação para testes ou migração.

> [!IMPORTANT]
> Para importar com sucesso dados exportados de servidores Redis que não o Azure Cache para Redis quando utilizar uma bolha de página, o tamanho da mancha de página deve ser alinhado num limite de byte 512. Para o código de amostra para efetuar o preenchimento de byte necessário, consulte [o upload da página da amostra](https://github.com/JimRoberts-MS/SamplePageBlobUpload).
>
>

### <a name="what-rdb-versions-can-i-import"></a>Que versões RDB posso importar?

A azure Cache para Redis suporta a importação de RDB através da versão RDB 7.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>O meu cache está disponível durante uma operação de importação/exportação?
* **Exportação** - Caches permanecem disponíveis e pode continuar a utilizar o seu cache durante uma operação de exportação.
* **Importação** - Caches ficam indisponíveis quando uma operação de importação começa e ficam disponíveis para utilização quando a operação de importação estiver concluída.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Posso utilizar importe/exportação com cluster Redis?
Sim, e pode importar/exportar entre uma cache agrupada e uma cache não agrupada. Uma vez que o cluster Redis [só suporta a base de dados 0,](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)quaisquer dados em bases de dados que não 0 não são importados. Quando os dados de cache agrupados são importados, as chaves são redistribuídas entre os fragmentos do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como funciona a Importação/Exportação com uma definição de bases de dados personalizadas?
Alguns níveis de preços têm [diferentes limites de bases de dados,](cache-configure.md#databases)por isso existem algumas considerações ao importar se configurar um valor personalizado para a configuração durante a `databases` criação de cache.

* Ao importar para um nível de preços com um limite inferior `databases` ao nível a partir do qual exportou:
  * Se estiver a utilizar o número padrão de `databases` , que é 16 para todos os níveis de preços, nenhum dado é perdido.
  * Se estiver a utilizar um número personalizado `databases` que se enquadra nos limites para o nível para o qual está a importar, não são perdidos dados.
  * Se os seus dados exportados contiverem dados numa base de dados que exceda os limites do novo nível, os dados dessas bases de dados mais elevadas não são importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Como é que a importação/exportação é diferente da persistência de Redis?
A azure Cache para persistência de Redis permite-lhe persistir dados armazenados em Redis para Azure Storage. Quando a persistência é configurada, a Cache Azure para Redis persiste uma imagem da Cache Azure para Redis num formato binário Redis para o disco com base numa frequência de backup configurável. Se ocorrer um evento catastrófico que desative a cache primária e réplica, os dados da cache são restaurados automaticamente usando o instantâneo mais recente. Para obter mais informações, consulte [Como configurar a persistência de dados para uma Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).

A Importação/Exportação permite-lhe trazer dados ou exportar da Azure Cache para a Redis. Não configura a cópia de segurança e restaura usando a persistência do Redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Posso automatizar a Importação/Exportação utilizando o PowerShell, o CLI ou outros clientes de gestão?
Sim, para instruções powerShell ver [Importar um Cache Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) e [exportar uma Cache Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo limite durante a minha operação de importação/exportação. O que é que isso significa?
Se permanecer na lâmina **de dados de importação** ou **exportação** por mais de 15 minutos antes de iniciar a operação, receberá um erro com uma mensagem de erro semelhante ao seguinte exemplo:

```output
The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.
```

Para resolver este problema, inicie a operação de importação ou exportação antes de decorridos 15 minutos.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Tive um erro ao exportar os meus dados para o Azure Blob Storage. O que aconteceu?
A exportação funciona apenas com ficheiros RDB armazenados como bolhas de página. Outros tipos de bolhas não são suportados atualmente, incluindo contas de armazenamento Blob com camadas quentes e frescas. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Passos seguintes
Saiba mais sobre o Azure Cache para funcionalidades redis.

* [Cache Azure para os níveis de serviço redis](cache-overview.md#service-tiers)