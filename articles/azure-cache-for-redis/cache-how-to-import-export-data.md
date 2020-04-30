---
title: Dados de importação e exportação em Azure Cache para Redis
description: Saiba importar e exportar dados de e para o armazenamento de blob com o seu premium Azure Cache para instâncias Redis
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 07/31/2017
ms.author: yegu
ms.openlocfilehash: 29ad5ca6c9058b88a539c7a3bb8ace4d9a65083a
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79278093"
---
# <a name="import-and-export-data-in-azure-cache-for-redis"></a>Dados de importação e exportação em Azure Cache para Redis
Import/Export é um Cache Azure para a operação de gestão de dados Redis, que permite importar dados para Azure Cache para Redis ou exportar dados de Azure Cache para Redis, importando e exportando um cache azure para redis Database (RDB) de uma cache premium para uma bolha numa conta de armazenamento Azure.

- **Exportação** - pode exportar o seu Cache Azure para fotos Redis RDB para uma Página Blob.
- **Importar** - pode importar o seu Cache Azure para fotos Redis RDB a partir de uma Blob page ou de um Block Blob.

A importação/exportação permite-lhe migrar entre diferentes cache sinuosos para casos Redis ou povoar a cache com dados antes da utilização.

Este artigo fornece um guia para a importação e exportação de dados com o Azure Cache for Redis e fornece as respostas a perguntas comummente colocadas.

> [!IMPORTANT]
> A importação/exportação só está disponível para caches [de nível premium.](cache-premium-tier-intro.md)
>
>

## <a name="import"></a>Importar
A importação pode ser usada para trazer ficheiros RDB compatíveis com Redis de qualquer servidor Redis em qualquer nuvem ou ambiente, incluindo Redis em execução em Linux, Windows ou qualquer fornecedor de nuvem, como Amazon Web Services e outros. Importar dados é uma forma fácil de criar uma cache com dados pré-povoados. Durante o processo de importação, o Azure Cache for Redis carrega os ficheiros RDB do armazenamento Azure na memória e, em seguida, insere as teclas na cache.

> [!NOTE]
> Antes de iniciar a operação de importação, certifique-se de que o ficheiro ou ficheiros da Redis Database (RDB) são enviados para a página ou bloqueie bolhas no armazenamento do Azure, na mesma região e subscrição do seu Cache Azure para a redis. Para mais informações, consulte [Começar com o armazenamento Azure Blob](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Se exportou o seu ficheiro RDB utilizando a funcionalidade [Azure Cache for Redis Export,](#export) o seu ficheiro RDB já está armazenado numa página blob e está pronto para importar.
>
>

1. Para importar uma ou mais bolhas de cache exportadas, [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal Azure e clique em importar **dados** do **menu Recurso**.

    ![Importar dados](./media/cache-how-to-import-export-data/cache-import-data.png)
2. Clique **em Escolher Blob(s)** e selecione a conta de armazenamento que contém os dados para importar.

    ![Escolha a conta de armazenamento](./media/cache-how-to-import-export-data/cache-import-choose-storage-account.png)
3. Clique no recipiente que contém os dados para importar.

    ![Escolha o recipiente](./media/cache-how-to-import-export-data/cache-import-choose-container.png)
4. Selecione uma ou mais bolhas para importar clicando na área à esquerda do nome blob e, em seguida, clique em **Select**.

    ![Escolha bolhas](./media/cache-how-to-import-export-data/cache-import-choose-blobs.png)
5. Clique em **Importar** para iniciar o processo de importação.

   > [!IMPORTANT]
   > A cache não é acessível pelos clientes cache durante o processo de importação, e quaisquer dados existentes na cache são eliminados.
   >
   >

    ![Importar](./media/cache-how-to-import-export-data/cache-import-blobs.png)

    Pode acompanhar o progresso da operação de importação seguindo as notificações do portal Azure ou visualizando os acontecimentos no [registo de auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Progresso das importações](./media/cache-how-to-import-export-data/cache-import-data-import-complete.png)

## <a name="export"></a>Exportar
A exportação permite-lhe exportar os dados armazenados em Azure Cache para Redis para redis compatíveis com ficheiros RDB compatíveis. Pode utilizar esta funcionalidade para mover dados de um Azure Cache para redis para outro ou para outro servidor Redis. Durante o processo de exportação, é criado um ficheiro temporário no VM que acolhe a caixa Azure para a instância do servidor Redis, e o ficheiro é enviado para a conta de armazenamento designada. Quando a operação de exportação termina com um estatuto de sucesso ou falha, o ficheiro temporário é suprimido.

1. Para exportar o conteúdo atual da cache para armazenamento, [navegue até à sua cache](cache-configure.md#configure-azure-cache-for-redis-settings) no portal Azure e clique em exportar **dados** do **menu Recurso**.

    ![Escolha o recipiente de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-choose-storage-container.png)
2. Clique **em Escolher o Recipiente de Armazenamento** e selecione a conta de armazenamento desejada. A conta de armazenamento deve estar na mesma subscrição e região que a sua cache.

   > [!IMPORTANT]
   > As exportações funcionam com blobs de página, que são suportados tanto por contas de armazenamento clássicas como de Gestor de Recursos, mas não são suportadas por contas de armazenamento Blob neste momento. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).
   >

    ![Conta de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-choose-account.png)
3. Escolha o recipiente de bolhas desejado e clique **em Selecionar**. Para utilizar um recipiente novo, clique em **Adicionar recipiente** para o adicionar primeiro e, em seguida, selecioná-lo a partir da lista.

    ![Escolha o recipiente de armazenamento](./media/cache-how-to-import-export-data/cache-export-data-container.png)
4. Digite um prefixo de **nome Blob** e clique em **Exportar** para iniciar o processo de exportação. O prefixo de nome blob é usado para pré-fixar os nomes dos ficheiros gerados por esta operação de exportação.

    ![Exportar](./media/cache-how-to-import-export-data/cache-export-data.png)

    Pode acompanhar o progresso da operação de exportação seguindo as notificações do portal Azure, ou visualizando os eventos no [registo de auditoria](../azure-resource-manager/management/view-activity-logs.md).

    ![Dados de exportação completos](./media/cache-how-to-import-export-data/cache-export-data-export-complete.png)

    Os caches permanecem disponíveis para utilização durante o processo de exportação.

## <a name="importexport-faq"></a>FaQ de importação/exportação
Esta secção contém perguntas frequentes sobre a função import/exportação.

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

### <a name="what-pricing-tiers-can-use-importexport"></a>Que níveis de preços podem utilizar import/exportação?
A importação/exportação só está disponível no nível de preços premium.

### <a name="can-i-import-data-from-any-redis-server"></a>Posso importar dados de qualquer servidor redis?
Sim, além de importar dados exportados de Azure Cache para casos Redis, pode importar ficheiros RDB de qualquer servidor Redis em qualquer nuvem ou ambiente, como Linux, Windows ou fornecedores de nuvem como amazon Web Services. Para isso, faça o upload do ficheiro RDB do servidor Redis desejado para uma página ou bloqueie a bolha numa Conta de Armazenamento Azure e, em seguida, importe-o para o seu cofre Azure premium para a instância Redis. Por exemplo, pode querer exportar os dados da sua cache de produção e importá-lo para uma cache utilizada como parte de um ambiente de preparação para testes ou migração.

> [!IMPORTANT]
> Para importar com sucesso dados exportados de servidores Redis que não o Azure Cache para Redis quando utilizar uma bolha de página, o tamanho da página blob deve ser alinhado num limite de byte 512. Para que o código da amostra efetue qualquer preenchimento de byte necessário, consulte o upload da amostra de [blob](https://github.com/JimRoberts-MS/SamplePageBlobUpload)da página .
>
>

### <a name="what-rdb-versions-can-i-import"></a>Que versões RDB posso importar?

O Azure Cache for Redis suporta a importação de RDB através da versão 7 do RDB.

### <a name="is-my-cache-available-during-an-importexport-operation"></a>A minha cache está disponível durante uma operação de importação/exportação?
* **Exportação** - Os caches permanecem disponíveis e pode continuar a utilizar o seu cache durante uma operação de exportação.
* **Importação** - Os caches ficam indisponíveis quando uma operação de importação começa e ficam disponíveis para utilização quando a operação de importação estiver concluída.

### <a name="can-i-use-importexport-with-redis-cluster"></a>Posso usar import/exportação com cluster Redis?
Sim, e pode importar/exportar entre uma cache agrupada e uma cache não agrupada. Uma vez que o cluster Redis apenas suporta a [base de dados 0,](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)quaisquer dados em bases de dados que não 0 não são importados. Quando os dados de cache agrupados são importados, as chaves são redistribuídas entre os fragmentos do cluster.

### <a name="how-does-importexport-work-with-a-custom-databases-setting"></a>Como funciona a Importação/Exportação com uma definição de bases de dados personalizada?
Alguns níveis de preços têm [diferentes limites](cache-configure.md#databases)de bases de dados, pelo que existem algumas considerações ao importar se configurar um valor personalizado para a configuração durante a `databases` criação de cache.

* Ao importar para um nível `databases` de preços com um limite inferior ao do nível a partir do qual exportou:
  * Se estiver a utilizar `databases`o número padrão de , que é 16 para todos os níveis de preços, não se perdem dados.
  * Se estiver a utilizar `databases` um número personalizado que se insere dentro dos limites para o nível a que está a importar, não se perdem dados.
  * Se os seus dados exportados contiverem dados numa base de dados que exceda os limites do novo nível, os dados dessas bases de dados mais elevadas não são importados.

### <a name="how-is-importexport-different-from-redis-persistence"></a>Como é que a Importação/Exportação é diferente da persistência do Redis?
A persistência de Azure Cache para Redis permite-lhe persistir os dados armazenados em Redis para o Armazenamento Azure. Quando a persistência é configurada, o Cache Azure para Redis persiste uma imagem do Azure Cache para Redis num formato binário Redis para o disco com base numa frequência de backup configurável. Se ocorrer um evento catastrófico que desativa a cache primária e a réplica, os dados de cache são restaurados automaticamente utilizando o instantâneo mais recente. Para mais informações, consulte [Como configurar a persistência de dados para um Cache Premium Azure para Redis](cache-how-to-premium-persistence.md).

Importar/ Exportação permite-lhe trazer dados ou exportar de Azure Cache para Redis. Não configura a cópia de segurança e restaura utilizando a persistência redis.

### <a name="can-i-automate-importexport-using-powershell-cli-or-other-management-clients"></a>Posso automatizar a Importação/Exportação utilizando powerShell, CLI ou outros clientes de gestão?
Sim, para instruções da PowerShell ver [importar um Cache Azure para Redis](cache-how-to-manage-redis-cache-powershell.md#to-import-an-azure-cache-for-redis) e [exportar um Azure Cache para Redis](cache-how-to-manage-redis-cache-powershell.md#to-export-an-azure-cache-for-redis).

### <a name="i-received-a-timeout-error-during-my-importexport-operation-what-does-it-mean"></a>Recebi um erro de tempo durante a minha operação de importação/exportação. O que é que isso significa?
Se permanecer na lâmina de **dados de importação** ou **exportação** por mais de 15 minutos antes de começar a operação, receberá um erro com uma mensagem de erro semelhante ao seguinte exemplo:

    The request to import data into cache 'contoso55' failed with status 'error' and error 'One of the SAS URIs provided could not be used for the following reason: The SAS token end time (se) must be at least 1 hour from now and the start time (st), if given, must be at least 15 minutes in the past.

Para resolver esta questão, inicie a operação de importação ou exportação antes de decorridos 15 minutos.

### <a name="i-got-an-error-when-exporting-my-data-to-azure-blob-storage-what-happened"></a>Tive um erro ao exportar os meus dados para o Armazém Azure Blob. O que aconteceu?
A exportação funciona apenas com ficheiros RDB armazenados como bolhas de página. Outros tipos de blob não são atualmente suportados, incluindo contas de armazenamento Blob com camadas quentes e frescas. Para mais informações, veja [Visão geral de conta de armazenamento do Azure](../storage/common/storage-account-overview.md).

## <a name="next-steps"></a>Passos seguintes
Saiba como usar mais funcionalidades de cache premium.

* [Introdução ao Cache Azure para o nível Redis Premium](cache-premium-tier-intro.md)
