---
title: Visão geral da conta de armazenamento do Azure | Microsoft Docs
description: Entenda as opções para criar e usar uma conta de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/07/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 67022c6bd9e237ce24d8e63285f7ebabadca87c6
ms.sourcegitcommit: 2d9a9079dd0a701b4bbe7289e8126a167cfcb450
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/29/2019
ms.locfileid: "71671318"
---
# <a name="azure-storage-account-overview"></a>Visão geral da conta de armazenamento do Azure

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do armazenamento do Azure: BLOBs, arquivos, filas, tabelas e discos. A conta de armazenamento fornece um namespace exclusivo para os dados do armazenamento do Azure que podem ser acessados de qualquer lugar do mundo por HTTP ou HTTPS. Os dados em sua conta de armazenamento do Azure são duráveis e altamente disponíveis, seguros e amplamente escalonáveis.

Para saber como criar uma conta de armazenamento do Azure, consulte [criar uma conta de armazenamento](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Contas de armazenamento para fins gerais v2

As contas de armazenamento de uso geral v2 dão suporte aos recursos mais recentes do armazenamento do Azure e incorporam toda a funcionalidade das contas de armazenamento de blob v1 e de uso geral. As contas v2 de uso geral fornecem os preços de capacidade por gigabyte mais baixos para o armazenamento do Azure, bem como preços de transação competitivos do setor. As contas de armazenamento de uso geral v2 dão suporte a estes serviços de armazenamento do Azure:

- BLOBs (todos os tipos: Bloco, acrescentar, página)
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

As contas de uso geral v1 fornecem acesso a todos os serviços de armazenamento do Azure, mas podem não ter os recursos mais recentes ou os preços mais baixos por gigabyte. As contas de armazenamento v1 de uso geral dão suporte a estes serviços de armazenamento do Azure:

- BLOBs (todos os tipos)
- Ficheiros
- Discos
- Filas
- Tabelas

Embora as contas v2 de uso geral sejam recomendadas na maioria dos casos, as contas v1 de uso geral são mais adequadas para esses cenários:

* Seus aplicativos exigem o modelo de implantação clássico do Azure. Contas de uso geral V2 e contas de armazenamento de BLOBs dão suporte apenas ao modelo de implantação Azure Resource Manager.

* Seus aplicativos têm uso intensivo de transações ou usam uma largura de banda de replicação geográfica significativa, mas não exigem grande capacidade. Nesse caso, a finalidade geral v1 pode ser a opção mais econômica.

* Utiliza uma versão da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior a 14/02/2014 ou uma biblioteca de cliente com uma versão inferior à 4.x e não pode atualizar a sua aplicação.

### <a name="block-blob-storage-accounts"></a>Bloquear contas de armazenamento de BLOBs

Uma conta de armazenamento de blobs de blocos é uma conta de armazenamento especializada para armazenar dados de objeto não estruturados como BLOBs de blocos. Esse tipo de conta de armazenamento dá suporte a blobs de bloco e blobs de acréscimo, mas não a blobs de páginas, tabelas ou filas.

Em comparação com contas de armazenamento de BLOBs V2 e de uso geral, as contas de armazenamento de blobs de blocos fornecem latência baixa e consistente e taxas de transações mais altas.

As contas de armazenamento de blobs de blocos atualmente não dão suporte a camadas para camadas de acesso frequente, esporádica ou de arquivamento.

### <a name="filestorage-storage-accounts"></a>Contas de armazenamento de armazenamento em

Uma conta de armazenamento de armazenamento de arquivo é uma conta de armazenamento especializada usada para armazenar e criar compartilhamentos de arquivos premium. As contas de armazenamento de armazenamento em fileoferecem características exclusivas de desempenho dedicadas, como a intermitência de IOPS. Para obter mais informações sobre essas características, consulte a seção [níveis de desempenho de compartilhamento de arquivos](../files/storage-files-planning.md#file-share-performance-tiers) do guia de planejamento de arquivos.

## <a name="naming-storage-accounts"></a>Nomeando contas de armazenamento

Para atribuir um nome à sua conta de armazenamento, mantenha estas regras em mente:

- Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
- O nome da sua conta do Storage tem de ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="performance-tiers"></a>Escalões de desempenho

As contas de armazenamento de uso geral podem ser configuradas para qualquer um dos seguintes níveis de desempenho:

* Um nível de desempenho padrão para armazenar blobs, arquivos, tabelas, filas e discos de máquina virtual do Azure.
* Um nível de desempenho premium para armazenar apenas discos de máquina virtual não gerenciados.

As contas de armazenamento de blobs de blocos fornecem um nível de desempenho premium para armazenar blobs de bloco e blobs de acréscimo.

As contas de armazenamento de armazenamento de arquivo fornecem um nível de desempenho premium para compartilhamentos de arquivos do Azure.

## <a name="access-tiers-for-block-blob-data"></a>Camadas de acesso para dados de blob de blocos

O armazenamento do Azure fornece opções diferentes para acessar dados de blob de blocos com base em padrões de uso. Cada camada de acesso no armazenamento do Azure é otimizada para um padrão específico de uso de dados. Ao selecionar a camada de acesso certa para suas necessidades, você pode armazenar seus dados de blob de blocos da maneira mais econômica.

As camadas de acesso disponíveis são:

* A camada de acesso **quente** , que é otimizada para acesso frequente de objetos na conta de armazenamento. O acesso a dados na camada quente é mais econômico, enquanto os custos de armazenamento são mais altos. As novas contas de armazenamento são criadas na camada quente por padrão.
* A camada de acesso **frio** , que é otimizada para armazenar grandes quantidades de dados acessados com pouca frequência e armazenados por pelo menos 30 dias. O armazenamento de dados na camada fria é mais econômico, mas acessar esses dados pode ser mais caro do que acessar dados na camada quente.
* A camada de **arquivo morto** , que está disponível somente para BLOBs de blocos individuais. A camada de arquivo é otimizada para dados que podem tolerar várias horas de latência de recuperação e permanecerão na camada de arquivo por pelo menos 180 dias. A camada de arquivamento é a opção mais econômica para armazenar dados, mas acessar esses dados é mais caro do que acessar dados em camadas quentes ou frias.

Se houver uma alteração no padrão de uso de seus dados, você poderá alternar entre essas camadas de acesso a qualquer momento. Para obter mais informações sobre as camadas de acesso, consulte [armazenamento de BLOBs do Azure: camadas de acesso quentes, frias e de arquivo](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> Alterar a camada de acesso para uma conta de armazenamento existente ou blob pode resultar em encargos adicionais. Para obter mais informações, consulte a [seção de cobrança da conta de armazenamento](#storage-account-billing).

## <a name="replication"></a>Replicação

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Para obter mais informações sobre replicação de armazenamento, consulte [replicação de armazenamento do Azure](storage-redundancy.md).

## <a name="encryption"></a>Encriptação

Todos os dados em sua conta de armazenamento são criptografados no lado do serviço. Para obter mais informações sobre criptografia, consulte [criptografia do serviço de armazenamento do Azure para dados em repouso](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Pontos finais da conta de armazenamento

Uma conta de armazenamento fornece um namespace exclusivo no Azure para seus dados. Cada objeto que você armazena no armazenamento do Azure tem um endereço que inclui seu nome de conta exclusivo. A combinação do nome da conta e do ponto de extremidade do serviço de armazenamento do Azure forma os pontos de extremidades para sua conta de armazenamento.

Por exemplo, se sua conta de armazenamento de uso geral for denominada *mystorageaccount*, os pontos de extremidade padrão para essa conta serão:

* Armazenamento de BLOBs: http://*mystorageaccount*. blob.Core.Windows.net
* Armazenamento de tabelas: http://*mystorageaccount*. Table.Core.Windows.net
* Armazenamento de filas: http://*mystorageaccount*. Queue.Core.Windows.net
* Arquivos do Azure: http://*mystorageaccount*. File.Core.Windows.net

> [!NOTE]
> O bloco BLOB e as contas de armazenamento de blob expõem apenas o ponto de extremidade do serviço BLOB.

A URL para acessar um objeto em uma conta de armazenamento é construída acrescentando o local do objeto na conta de armazenamento ao ponto de extremidade. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Você também pode configurar sua conta de armazenamento para usar um domínio personalizado para BLOBs. Para obter mais informações, consulte [configurar um nome de domínio personalizado para sua conta de armazenamento do Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controlar o acesso aos dados da conta

Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta. Você tem controle sobre quem pode acessar seus dados e quais permissões eles têm.

Todas as solicitações feitas em sua conta de armazenamento devem ser autorizadas. No nível do serviço, a solicitação deve incluir um cabeçalho de *autorização* válido, que inclui todas as informações necessárias para que o serviço valide a solicitação antes de executá-la.

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

Quando você atualiza para uma conta v2 de uso geral de uma conta de armazenamento de blob v1 ou de uso geral, seus dados são migrados automaticamente. A Microsoft recomenda esse caminho para atualizar sua conta. No entanto, se você decidir mover dados de uma conta de uso geral v1 para uma conta de armazenamento de BLOBs, precisará migrar seus dados manualmente, usando as ferramentas e bibliotecas descritas abaixo. 

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Você pode usar o AzCopy para copiar dados em uma conta de armazenamento de blobs de uma conta de armazenamento de uso geral existente ou para carregar dados de dispositivos de armazenamento locais. Para obter mais informações, veja [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de Movimento de Dados

A biblioteca de movimento de dados do Armazenamento do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples, semelhantes às de AzCopy. Pode utilizá-la para tirar partido das funcionalidades fornecidas pelo AzCopy na sua aplicação de forma nativa, sem ter de executar e monitorizar instâncias externas do AzCopy. Para obter mais informações, veja [Biblioteca de Movimento de Dados do Armazenamento do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Pode criar uma aplicação personalizada para migrar os dados para uma conta do Blob Storage utilizando uma das bibliotecas de cliente do Azure ou a API REST dos serviços do Storage do Azure. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para obter mais informações sobre a API REST do armazenamento do Azure, consulte [referência da API REST dos serviços de armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação com o blob. Se copiar um blob com encriptação do lado do cliente, certifique-se de que a operação de cópia preserva os metadados do blob e, especialmente, os metadados relacionados com a encriptação. Se copiar um blob sem os metadados de encriptação, não poderá obter novamente o conteúdo do mesmo. Para obter mais informações acerca dos metadados relacionados com a encriptação, veja [Encriptação do Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Serviço de importação/exportação do Azure

Se você tiver uma grande quantidade de dados para importar para sua conta de armazenamento, considere o serviço de importação/exportação do Azure. O serviço de importação/exportação é usado para importar com segurança grandes quantidades de dados para o armazenamento de BLOBs do Azure e os arquivos do Azure enviando unidades de disco para um datacenter do Azure. 

O serviço de importação/exportação também pode ser usado para transferir dados do armazenamento de BLOBs do Azure para as unidades de disco e enviar para seus sites locais. Os dados de uma ou mais unidades de disco podem ser importados para o armazenamento de BLOBs do Azure ou para os arquivos do Azure. Para obter mais informações, consulte [o que é o serviço de importação/exportação do Azure?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Faturação da conta do Storage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Passos seguintes

* Para saber como criar uma conta de armazenamento do Azure de uso geral, consulte [criar uma conta de armazenamento](storage-quickstart-create-account.md).
* Para saber como criar uma conta de armazenamento de blob de blocos, consulte [criar uma conta de armazenamento de blob de blocos](../blobs/storage-blob-create-account-block-blob.md).
* Para gerenciar ou excluir uma conta de armazenamento existente, consulte [gerenciar contas de armazenamento do Azure](storage-account-manage.md).
