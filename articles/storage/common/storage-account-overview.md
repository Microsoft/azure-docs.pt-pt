---
title: Descrição geral da conta de armazenamento
titleSuffix: Azure Storage
description: Compreenda as opções para criar e utilizar uma conta de Armazenamento Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/17/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 3694a1d04ec25f44cfcf9395bdd148e2fd3c0d9f
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "79371563"
---
# <a name="storage-account-overview"></a>Descrição geral da conta de armazenamento

Uma conta de armazenamento Azure contém todos os objetos de dados do Seu Armazenamento Azure: blobs, ficheiros, filas, mesas e discos. A conta de armazenamento fornece um espaço de nome único para os seus dados de Armazenamento Azure que é acessível a partir de qualquer parte do mundo em HTTP ou HTTPS. Os dados na sua conta de armazenamento Azure são duráveis e altamente disponíveis, seguros e massivamente escaláveis.

Para aprender a criar uma conta de armazenamento Azure, consulte Criar uma conta de [armazenamento.](storage-account-create.md)

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Contas de armazenamento para fins gerais v2

As contas de armazenamento v2 de uso geral suportam as mais recentes funcionalidades de Armazenamento Azure e incorporam toda a funcionalidade das contas de armazenamento v1 e Blob de uso geral. As contas v2 de uso geral fornecem os preços de capacidade mais baixos por gigabyte para o Armazenamento Azure, bem como os preços de transação competitivos da indústria. As contas de armazenamento v2 de uso geral suportam estes serviços de armazenamento Azure:

- Blobs (todos os tipos: Bloco, Apêndice, Página)
- Data Lake Gen2
- Ficheiros
- Discos
- Filas
- Tabelas

> [!NOTE]
> A Microsoft recomenda a utilização de uma conta de armazenamento v2 para a maioria dos cenários. Pode facilmente atualizar uma conta de armazenamento v1 ou Blob para uma conta v2 de uso geral sem tempo de inatividade e sem necessidade de copiar dados.
>
> Para obter mais informações sobre a atualização para uma conta v2 de uso geral, consulte o Upgrade para uma conta de [armazenamento v2](storage-account-upgrade.md)de uso geral .

As contas de armazenamento v2 de uso geral oferecem vários níveis de acesso para armazenar dados com base nos seus padrões de utilização. Para mais informações, consulte os níveis de [acesso para obter dados de blocos blob](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Contas de armazenamento para fins gerais v1

As contas de armazenamento v1 de uso geral fornecem acesso a todos os serviços de Armazenamento Azure, mas podem não ter as funcionalidades mais recentes ou os preços mais baixos por gigabyte. As contas de armazenamento v1 de uso geral suportam estes serviços de armazenamento Azure:

- Bolhas (todos os tipos)
- Ficheiros
- Discos
- Filas
- Tabelas

Na maioria dos casos, deve utilizar contas v2 de uso geral. Pode utilizar contas v1 de uso geral para estes cenários:

- As suas aplicações requerem o modelo de implantação clássico do Azure. As contas v2 de uso geral e as contas de armazenamento blob suportam apenas o modelo de implementação do Gestor de Recursos Azure.

- As suas aplicações são intensivas em transações ou utilizam largura de banda de geo-replicação significativa, mas não requerem grande capacidade. Neste caso, a v1 de uso geral pode ser a escolha mais económica.

- Utiliza uma versão da [Storage Services REST API](https://msdn.microsoft.com/library/azure/dd894041.aspx) que é mais cedo do que 2014-02-14 ou uma biblioteca de clientes com uma versão inferior a 4.x. Não pode atualizar a sua candidatura.

### <a name="blockblobstorage-accounts"></a>Contas BlockBlobStorage

Uma conta BlockBlobStorage é uma conta de armazenamento especializada no nível de desempenho premium para armazenar dados de objetos não estruturados como bolhas de bloco ou bolhas de apêndice. Em comparação com as contas v2 e BlobStorage de propósito geral, as contas BlockBlobStorage fornecem baixa, latência consistente e taxas de transação mais elevadas.

As contas BlockBlobStorage não suportam atualmente níveis de acesso quentes, frescos ou de arquivo. Este tipo de conta de armazenamento não suporta bolhas de página, mesas ou filas.

### <a name="filestorage-accounts"></a>Contas de FileStorage

Uma conta fileStorage é uma conta de armazenamento especializada usada para armazenar e criar ações de ficheiropremium. Este tipo de conta de armazenamento suporta ficheiros, mas não bloqueia bolhas, bolhas de apêndice, bolhas de página, mesas ou filas.

As contas de FileStorage oferecem características dedicadas ao desempenho únicas, como a explosão do IOPS. Para obter mais informações sobre estas características, consulte a secção de níveis de armazenamento de partilha de ficheiros do guia de planeamento de [Ficheiros.](../files/storage-files-planning.md#storage-tiers)

## <a name="naming-storage-accounts"></a>Contas de armazenamento de nomeação

Para atribuir um nome à sua conta de armazenamento, mantenha estas regras em mente:

- Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
- O nome da sua conta do Storage tem de ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="performance-tiers"></a>Escalões de desempenho

Dependendo do tipo de conta de armazenamento que cria, pode escolher entre os níveis de desempenho padrão e premium.

### <a name="general-purpose-storage-accounts"></a>Contas de armazenamento para fins gerais

As contas de armazenamento para fins gerais podem ser configuradas para qualquer um dos seguintes níveis de desempenho:

- Um nível de desempenho padrão para armazenar bolhas, ficheiros, mesas, filas e discos de máquinas virtuais Azure. Para obter mais informações sobre os objetivos de escalabilidade das contas de armazenamento padrão, consulte os objetivos de [escalabilidade para as contas de armazenamento padrão](scalability-targets-standard-account.md).
- Um nível de desempenho premium para armazenar discos de máquinas virtuais não geridos. A Microsoft recomenda a utilização de discos geridos com máquinas virtuais Azure em vez de discos não geridos. Para obter mais informações sobre os objetivos de escalabilidade para o nível de desempenho premium, consulte os objetivos de escalabilidade para contas de [armazenamento de blob](../blobs/scalability-targets-premium-page-blobs.md)de página premium .

### <a name="blockblobstorage-storage-accounts"></a>Contas de armazenamento BlockBlobStorage

As contas de armazenamento BlockBlobStorage fornecem um nível de desempenho premium para armazenar bolhas de blocos e bolhas de apêndice. Para obter mais informações, consulte os alvos de [escalabilidade para contas](../blobs/scalability-targets-premium-block-blobs.md)de armazenamento de blocos premium .

### <a name="filestorage-storage-accounts"></a>Contas de armazenamento de arquivos

As contas de armazenamento de fileStorage fornecem um nível de desempenho premium para as ações de ficheiros Azure. Para mais informações, consulte a escalabilidade e os alvos de desempenho do [Azure Files.](../files/storage-files-scale-targets.md)

## <a name="access-tiers-for-block-blob-data"></a>Níveis de acesso para dados de blocos blob

O Azure Storage oferece diferentes opções para aceder a dados blob de blocos com base em padrões de utilização. Cada nível de acesso no Armazenamento Azure é otimizado para um padrão particular de utilização de dados. Ao selecionar o nível de acesso certo para as suas necessidades, pode armazenar os seus dados blob de bloco da forma mais rentável.

Os níveis de acesso disponíveis são:

- O nível de acesso **quente.** Este nível está otimizado para o acesso frequente de objetos na conta de armazenamento. O acesso aos dados no nível quente é mais rentável, enquanto os custos de armazenamento são mais elevados. Novas contas de armazenamento são criadas no nível quente por padrão.
- O nível de acesso **Cool.** Este nível está otimizado para armazenar grandes quantidades de dados que são raramente acedidos e armazenados durante pelo menos 30 dias. Armazenar dados no nível cool é mais rentável, mas aceder a esses dados pode ser mais caro do que aceder a dados no nível quente.
- O nível de **arquivo.** Este nível está disponível apenas para bolhas de bloco individuais. O nível de arquivo está otimizado para dados que podem tolerar várias horas de latência de recuperação e que permanecerão no nível de arquivo por pelo menos 180 dias. O nível de arquivo é a opção mais rentável para armazenar dados. No entanto, aceder a esses dados é mais caro do que aceder a dados nos níveis quentes ou frescos.

Se houver uma alteração no padrão de utilização dos seus dados, pode alternar entre estes níveis de acesso a qualquer momento. Para obter mais informações sobre os níveis de acesso, consulte o [armazenamento do Azure Blob: hot, cool e archive access tiers](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> A alteração do nível de acesso para uma conta de armazenamento ou bolha existente pode resultar em encargos adicionais. Para mais informações, consulte a [secção de faturação](#storage-account-billing)da conta de armazenamento .

## <a name="redundancy"></a>Redundância

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

## <a name="encryption"></a>Encriptação

Todos os dados da sua conta de armazenamento estão encriptados no lado do serviço. Para obter mais informações sobre encriptação, consulte a Encriptação do Serviço de [Armazenamento Azure para obter dados em repouso](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Pontos finais da conta do Storage

Uma conta de armazenamento fornece um espaço de nome único em Azure para os seus dados. Cada objeto que armazena no Azure Storage tem um endereço que inclui o nome único da conta. A combinação do nome da conta e do ponto final do serviço de armazenamento Azure forma os pontos finais da sua conta de armazenamento.

Por exemplo, se a sua conta de armazenamento de uso geral for nomeada *minha conta*de armazenamento , então os pontos finais por defeito dessa conta são:

- Armazenamento de bolhas:`https://*mystorageaccount*.blob.core.windows.net`
- Armazenamento de mesa:`https://*mystorageaccount*.table.core.windows.net`
- Armazenamento de fila:`https://*mystorageaccount*.queue.core.windows.net`
- Ficheiros Azure:`https://*mystorageaccount*.file.core.windows.net`

> [!NOTE]
> As contas de armazenamento de blob e blob de blocos expõem apenas o ponto final do serviço Blob.

Construa o URL para aceder a um objeto numa conta de armazenamento, aperto ua,o local na conta de armazenamento até ao ponto final. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também pode configurar a sua conta de armazenamento para utilizar um domínio personalizado para bolhas. Para mais informações, consulte Configurar um nome de [domínio personalizado para a sua conta de Armazenamento Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controlar o acesso aos dados da conta

Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta. Tem controlo sobre quem pode aceder aos seus dados e que permissões têm.

Todos os pedidos feitos contra a sua conta de armazenamento devem ser autorizados. Ao nível do serviço, o pedido deve incluir um cabeçalho de *Autorização* válido. Especificamente, este cabeçalho inclui todas as informações necessárias para que o serviço valide o pedido antes de executá-lo.

Pode conceder acesso aos dados na sua conta de armazenamento utilizando qualquer uma das seguintes abordagens:

- **Diretório Ativo Azure:** Utilize credenciais de Diretório Ativo Azure (Azure AD) para autenticar um utilizador, grupo ou outra identidade para acesso a dados de blob e fila. Se a autenticação de uma identidade for bem sucedida, então a Azure AD devolve um símbolo a utilizar na autorização do pedido para armazenamento de Blob Azure ou armazenamento de fila. Para mais informações, consulte [O acesso authenticado ao Armazenamento Azure utilizando o Diretório Ativo Azure](storage-auth-aad.md).
- **Autorização de chave partilhada:** Utilize a chave de acesso à sua conta de armazenamento para construir uma cadeia de ligação que a sua aplicação utiliza no tempo de execução para aceder ao Armazenamento Azure. Os valores da cadeia de ligação são utilizados para construir o cabeçalho de *autorização* que é passado para o Armazenamento Azure. Para mais informações, consulte as cordas de ligação de [armazenamento Configure Azure](storage-configure-connection-string.md).
- **Assinatura de acesso partilhado:** Utilize uma assinatura de acesso partilhado para delegar o acesso aos recursos na sua conta de armazenamento, caso não esteja a utilizar a autorização da Azure AD. Uma assinatura de acesso partilhado é um símbolo que encapsula todas as informações necessárias para autorizar um pedido ao Armazenamento Azure no URL. Pode especificar o recurso de armazenamento, as permissões concedidas e o intervalo sobre o qual as permissões são válidas como parte da assinatura de acesso partilhado. Para mais informações, consulte [A utilização de assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).

> [!NOTE]
> Autenticar utilizadores ou aplicações utilizando credenciais De AD Azure proporciona uma segurança e facilidade de utilização superiores em outros meios de autorização. Embora possa continuar a utilizar a autorização de Chave Partilhada com as suas aplicações, a utilização do Azure AD contorna a necessidade de armazenar a chave de acesso à sua conta com o seu código. Também pode continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder acesso fino aos recursos na sua conta de armazenamento, mas a Azure AD oferece capacidades semelhantes sem a necessidade de gerir tokens SAS ou preocupar-se em revogar um SAS comprometido.
>
> A Microsoft recomenda a utilização da autorização da Azure AD para as suas aplicações de blob de armazenamento Azure e de fila, quando possível.

## <a name="copying-data-into-a-storage-account"></a>Copiar dados numa conta de armazenamento

A Microsoft fornece serviços e bibliotecas para importar os seus dados de dispositivos de armazenamento no local ou fornecedores de armazenamento em nuvem de terceiros. A solução que usas depende da quantidade de dados que estás a transferir.

Ao atualizar para uma conta v2 de propósito geral a partir de uma conta de armazenamento v1 ou Blob de uso geral, os seus dados são automaticamente migrados. A Microsoft recomenda esta via para atualizar a sua conta. No entanto, se decidir transferir dados de uma conta v1 de uso geral para uma conta de armazenamento Blob, então irá migrar os seus dados manualmente, utilizando as ferramentas e bibliotecas descritas abaixo.

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Pode utilizar o AzCopy para copiar dados numa conta de armazenamento Blob a partir de uma conta de armazenamento de uso geral existente, ou para fazer o upload de dados de dispositivos de armazenamento no local. Para obter mais informações, veja [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de Movimento de Dados

A biblioteca de movimento de dados do Armazenamento do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples, semelhantes às de AzCopy. Pode utilizar a biblioteca de movimentos de dados para tirar partido das funcionalidades AzCopy de forma nativa. Para mais informações, consulte a Biblioteca de Movimentos de Dados de [Armazenamento de Azure para .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Pode criar uma aplicação personalizada para migrar os seus dados de uma conta de armazenamento v1 de propósito geral para uma conta de armazenamento Blob. Utilize uma das bibliotecas de clientes Azure ou os serviços de armazenamento Azure REST API. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para mais informações sobre a API de Repouso de Armazenamento Azure, consulte [Azure Storage Services REST API Reference](https://docs.microsoft.com/rest/api/storageservices/).

> [!IMPORTANT]
> Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação com o blob. Se copiar um blob com encriptação do lado do cliente, certifique-se de que a operação de cópia preserva os metadados do blob e, especialmente, os metadados relacionados com a encriptação. Se copiar um blob sem os metadados de encriptação, não poderá obter novamente o conteúdo do mesmo. Para obter mais informações acerca dos metadados relacionados com a encriptação, veja [Encriptação do Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Faturação da conta do Storage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](storage-account-create.md)
- [Criar uma conta de armazenamento de blob de blocos](../blobs/storage-blob-create-account-block-blob.md)
