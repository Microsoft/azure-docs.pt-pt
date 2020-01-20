---
title: Descrição geral da conta de armazenamento
titleSuffix: Azure Storage
description: Entenda as opções para criar e usar uma conta de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 5034aaaee335bbd87e7ea42b448e4e8fbf6aacca
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76274540"
---
# <a name="storage-account-overview"></a>Descrição geral da conta de armazenamento

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do armazenamento do Azure: BLOBs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do armazenamento do Azure que podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. Os dados em sua conta de armazenamento do Azure são duráveis e altamente disponíveis, seguros e amplamente escalonáveis.

Para saber como criar uma conta de armazenamento do Azure, consulte [criar uma conta de armazenamento](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Contas de armazenamento para fins gerais v2

As contas de armazenamento de uso geral v2 dão suporte aos recursos mais recentes do armazenamento do Azure e incorporam toda a funcionalidade das contas de armazenamento de blob v1 e de uso geral. As contas v2 de uso geral fornecem os preços de capacidade por gigabyte mais baixos para o armazenamento do Azure, bem como preços de transação competitivos do setor. As contas de armazenamento de uso geral v2 dão suporte a estes serviços de armazenamento do Azure:

- BLOBs (todos os tipos: bloco, acrescentar, página)
- Ger2 do Data Lake
- Ficheiros
- Discos
- Filas
- Tabelas

> [!NOTE]
> A Microsoft recomenda usar uma conta de armazenamento de uso geral v2 para a maioria dos cenários. Você pode atualizar facilmente uma conta de armazenamento de blob v1 ou de uso geral para uma conta v2 de finalidade geral sem tempo de inatividade e sem a necessidade de copiar dados.
>
> Para obter mais informações sobre como atualizar para uma conta v2 de uso geral, consulte [atualizar para uma conta de armazenamento de uso geral v2](storage-account-upgrade.md).

As contas de armazenamento de uso geral v2 oferecem várias camadas de acesso para armazenar dados com base em seus padrões de uso. Para obter mais informações, consulte [camadas de acesso para dados de blob de blocos](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Contas de armazenamento para fins gerais v1

As contas de armazenamento v1 de uso geral fornecem acesso a todos os serviços de armazenamento do Azure, mas podem não ter os recursos mais recentes ou os preços mais baixos por gigabyte. As contas de armazenamento v1 de uso geral dão suporte a estes serviços de armazenamento do Azure:

- BLOBs (todos os tipos)
- Ficheiros
- Discos
- Filas
- Tabelas

Você deve usar contas de uso geral v2 na maioria dos casos. Você pode usar contas v1 de uso geral para estes cenários:

- Seus aplicativos exigem o modelo de implantação clássico do Azure. Contas de uso geral V2 e contas de armazenamento de BLOBs dão suporte apenas ao modelo de implantação Azure Resource Manager.

- Seus aplicativos têm uso intensivo de transações ou usam uma largura de banda de replicação geográfica significativa, mas não exigem grande capacidade. Nesse caso, a finalidade geral v1 pode ser a opção mais econômica.

- Você usa uma versão da [API REST dos serviços de armazenamento](https://msdn.microsoft.com/library/azure/dd894041.aspx) que é anterior à 2014-02-14 ou uma biblioteca de cliente com uma versão inferior a 4. x. Não é possível atualizar seu aplicativo.

### <a name="blockblobstorage-accounts"></a>Contas do BlockBlobStorage

Uma conta do BlockBlobStorage é uma conta de armazenamento especializada no nível de desempenho premium para armazenar dados de objeto não estruturado como BLOBs de blocos ou BLOBs de acréscimo. Em comparação com contas de uso geral V2 e BlobStorage, as contas BlockBlobStorage fornecem latência baixa, consistente e taxas de transações mais altas.

As contas do BlockBlobStorage atualmente não dão suporte a camadas para camadas de acesso frequente, esporádica ou de arquivamento. Esse tipo de conta de armazenamento não dá suporte a blobs de página, tabelas ou filas.

### <a name="filestorage-accounts"></a>Contas de armazenamento de

Uma conta de armazenamento de arquivo é uma conta de armazenamento especializada usada para armazenar e criar compartilhamentos de arquivos premium. Esse tipo de conta de armazenamento dá suporte a arquivos, mas não a blobs de blocos, blobs de acréscimo, blobs de páginas, tabelas ou filas.

As contas de armazenamento de fileoferecem características de desempenho exclusivas, como a intermitência de IOPS. Para obter mais informações sobre essas características, consulte a seção [níveis de desempenho de compartilhamento de arquivos](../files/storage-files-planning.md#file-share-performance-tiers) do guia de planejamento de arquivos.

## <a name="naming-storage-accounts"></a>Nomeando contas de armazenamento

Para atribuir um nome à sua conta de armazenamento, mantenha estas regras em mente:

- Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
- O nome da sua conta do Storage tem de ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="performance-tiers"></a>Escalões de desempenho

Dependendo do tipo de conta de armazenamento que você criar, você pode escolher entre as camadas de desempenho Standard e Premium.

### <a name="general-purpose-storage-accounts"></a>Contas de armazenamento para fins gerais

As contas de armazenamento de uso geral podem ser configuradas para qualquer um dos seguintes níveis de desempenho:

- Um nível de desempenho padrão para armazenar blobs, arquivos, tabelas, filas e discos de máquina virtual do Azure. Para obter mais informações sobre metas de escalabilidade para contas de armazenamento padrão, consulte [metas de escalabilidade para contas de armazenamento padrão](scalability-targets-standard-account.md).
- Um nível de desempenho premium para armazenar discos de máquina virtual não gerenciados. A Microsoft recomenda o uso de discos gerenciados com máquinas virtuais do Azure em vez de discos não gerenciados. Para obter mais informações sobre metas de escalabilidade para o nível de desempenho premium, consulte [metas de escalabilidade para contas de armazenamento de blobs de páginas Premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Contas de armazenamento BlockBlobStorage

As contas de armazenamento BlockBlobStorage fornecem um nível de desempenho premium para armazenar blobs de bloco e blobs de acréscimo. Para obter mais informações, consulte [metas de escalabilidade para contas de armazenamento de blob de blocos Premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Contas de armazenamento de armazenamento em

As contas de armazenamento de armazenamento de arquivo fornecem um nível de desempenho premium para compartilhamentos de arquivos do Azure. Para obter mais informações, consulte [escalabilidade e metas de desempenho dos arquivos do Azure](../files/storage-files-scale-targets.md).

## <a name="access-tiers-for-block-blob-data"></a>Camadas de acesso para dados de blob de blocos

O armazenamento do Azure fornece opções diferentes para acessar dados de blob de blocos com base em padrões de uso. Cada camada de acesso no armazenamento do Azure é otimizada para um padrão específico de uso de dados. Ao selecionar a camada de acesso certa para suas necessidades, você pode armazenar seus dados de blob de blocos da maneira mais econômica.

As camadas de acesso disponíveis são:

- A camada de acesso **quente** . Essa camada é otimizada para acesso frequente de objetos na conta de armazenamento. O acesso a dados na camada quente é mais econômico, enquanto os custos de armazenamento são mais altos. As novas contas de armazenamento são criadas na camada quente por padrão.
- A camada de acesso **fria** . Essa camada é otimizada para armazenar grandes quantidades de dados acessados com pouca frequência e armazenados por pelo menos 30 dias. O armazenamento de dados na camada fria é mais econômico, mas acessar esses dados pode ser mais caro do que acessar dados na camada quente.
- A camada de **arquivo morto** . Essa camada está disponível apenas para BLOBs de blocos individuais e blobs de acréscimo. A camada de arquivamento é otimizada para dados que podem tolerar várias horas de latência de recuperação e que permanecerão na camada de arquivo por pelo menos 180 dias. A camada de arquivamento é a opção mais econômica para armazenar dados. No entanto, acessar esses dados é mais caro do que acessar dados em camadas quentes ou frias.

Se houver uma alteração no padrão de uso de seus dados, você poderá alternar entre essas camadas de acesso a qualquer momento. Para obter mais informações sobre as camadas de acesso, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Alterar a camada de acesso para uma conta de armazenamento existente ou blob pode resultar em encargos adicionais. Para obter mais informações, consulte a [seção de cobrança da conta de armazenamento](#storage-account-billing).

## <a name="replication"></a>Replicação

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Para obter mais informações sobre replicação de armazenamento, consulte [replicação de armazenamento do Azure](storage-redundancy.md).

## <a name="encryption"></a>Encriptação

Todos os dados em sua conta de armazenamento são criptografados no lado do serviço. Para obter mais informações sobre criptografia, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Pontos finais da conta do Storage

Uma conta de armazenamento fornece um namespace exclusivo no Azure para seus dados. Cada objeto que você armazena no armazenamento do Azure tem um endereço que inclui seu nome de conta exclusivo. A combinação do nome da conta e do ponto de extremidade do serviço de armazenamento do Azure forma os pontos de extremidades para sua conta de armazenamento.

Por exemplo, se sua conta de armazenamento de uso geral for denominada *mystorageaccount*, os pontos de extremidade padrão para essa conta serão:

- Armazenamento de BLOBs: `https://*mystorageaccount*.blob.core.windows.net`
- Armazenamento de tabelas: `https://*mystorageaccount*.table.core.windows.net`
- Armazenamento de filas: `https://*mystorageaccount*.queue.core.windows.net`
- Arquivos do Azure: `https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> O bloco BLOB e as contas de armazenamento de blob expõem apenas o ponto de extremidade do serviço BLOB.

Construa a URL para acessar um objeto em uma conta de armazenamento acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Você também pode configurar sua conta de armazenamento para usar um domínio personalizado para BLOBs. Para obter mais informações, consulte [configurar um nome de domínio personalizado para sua conta de armazenamento do Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controlar o acesso aos dados da conta

Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta. Você tem controle sobre quem pode acessar seus dados e quais permissões eles têm.

Todas as solicitações feitas em sua conta de armazenamento devem ser autorizadas. No nível do serviço, a solicitação deve incluir um cabeçalho de *autorização* válido. Especificamente, esse cabeçalho inclui todas as informações necessárias para que o serviço valide a solicitação antes de executá-la.

Você pode conceder acesso aos dados em sua conta de armazenamento usando qualquer uma das seguintes abordagens:

- **Azure Active Directory:** Use as credenciais do Azure Active Directory (AD do Azure) para autenticar um usuário, grupo ou outra identidade para acesso a dados de BLOB e de fila. Se a autenticação de uma identidade for bem-sucedida, o Azure AD retornará um token a ser usado na autorização da solicitação para o armazenamento de BLOBs do Azure ou armazenamento de filas. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure usando o Azure Active Directory](storage-auth-aad.md).
- **Autorização de chave compartilhada:** Use sua chave de acesso da conta de armazenamento para construir uma cadeia de conexão que seu aplicativo usa no tempo de execução para acessar o armazenamento do Azure. Os valores na cadeia de conexão são usados para construir o cabeçalho de *autorização* que é passado para o armazenamento do Azure. Para obter mais informações, consulte [Configurar cadeias de conexão de armazenamento do Azure](storage-configure-connection-string.md).
- **Assinatura de acesso compartilhado:** Use uma assinatura de acesso compartilhado para delegar acesso a recursos em sua conta de armazenamento, se você não estiver usando a autorização do Azure AD. Uma assinatura de acesso compartilhado é um token que encapsula todas as informações necessárias para autorizar uma solicitação para o armazenamento do Azure na URL. Você pode especificar o recurso de armazenamento, as permissões concedidas e o intervalo sobre o qual as permissões são válidas como parte da assinatura de acesso compartilhado. Para obter mais informações, consulte [usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).

> [!NOTE]
> Autenticar usuários ou aplicativos usando credenciais do Azure AD fornece segurança superior e facilidade de uso em relação a outros meios de autorização. Embora você possa continuar a usar a autorização de chave compartilhada com seus aplicativos, usar o Azure AD evita a necessidade de armazenar a chave de acesso da conta com seu código. Você também pode continuar a usar SAS (assinaturas de acesso compartilhado) para conceder acesso refinado aos recursos em sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerenciar tokens SAS ou se preocupar com a revogação de uma SAS comprometida.
>
> A Microsoft recomenda usar a autorização do Azure AD para seus aplicativos de BLOB e fila de armazenamento do Azure quando possível.

## <a name="copying-data-into-a-storage-account"></a>Copiando dados em uma conta de armazenamento

A Microsoft fornece utilitários e bibliotecas para importar seus dados de dispositivos de armazenamento locais ou provedores de armazenamento em nuvem de terceiros. A solução que você usa depende da quantidade de dados que você está transferindo.

Quando você atualiza para uma conta v2 de uso geral de uma conta de armazenamento de blob v1 ou de uso geral, seus dados são migrados automaticamente. A Microsoft recomenda esse caminho para atualizar sua conta. No entanto, se você decidir mover dados de uma conta de uso geral v1 para uma conta de armazenamento de BLOBs, você migrará os dados manualmente, usando as ferramentas e bibliotecas descritas abaixo.

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Você pode usar o AzCopy para copiar dados em uma conta de armazenamento de blobs de uma conta de armazenamento de uso geral existente ou para carregar dados de dispositivos de armazenamento locais. Para obter mais informações, veja [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de Movimento de Dados

A biblioteca de movimento de dados do Armazenamento do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples, semelhantes às de AzCopy. Você pode usar a biblioteca de movimentação de dados para aproveitar os recursos do AzCopy nativamente. Para obter mais informações, consulte [biblioteca de movimentação de dados do armazenamento do Azure para .net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Você pode criar um aplicativo personalizado para migrar seus dados de uma conta de armazenamento v1 de uso geral para uma conta de armazenamento de BLOBs. Use uma das bibliotecas de cliente do Azure ou a API REST dos serviços de armazenamento do Azure. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para obter mais informações sobre a API REST do armazenamento do Azure, consulte [referência da API REST dos serviços de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação com o blob. Se copiar um blob com encriptação do lado do cliente, certifique-se de que a operação de cópia preserva os metadados do blob e, especialmente, os metadados relacionados com a encriptação. Se copiar um blob sem os metadados de encriptação, não poderá obter novamente o conteúdo do mesmo. Para obter mais informações acerca dos metadados relacionados com a encriptação, veja [Encriptação do Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Faturação da conta do Storage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](storage-account-create.md)
- [Criar uma conta de armazenamento de blob de blocos](../blobs/storage-blob-create-account-block-blob.md)
