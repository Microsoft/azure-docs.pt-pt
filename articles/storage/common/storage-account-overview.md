---
title: Descrição geral da conta de armazenamento
titleSuffix: Azure Storage
description: Saiba mais sobre os diferentes tipos de contas de armazenamento no Azure Storage. Rever nomeação de conta, níveis de desempenho, níveis de acesso, redundância, encriptação, pontos finais e muito mais.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 03/09/2021
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 24d955b0d1c53f57f5927f9e893b6ecd75fb3ca8
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561896"
---
# <a name="storage-account-overview"></a>Descrição geral da conta de armazenamento

Uma conta de armazenamento Azure contém todos os seus objetos de dados de armazenamento Azure: bolhas, ficheiros, filas, tabelas e discos. A conta de armazenamento fornece um espaço de nome único para os seus dados de Armazenamento Azure que estão acessíveis a partir de qualquer parte do mundo em HTTP ou HTTPS. Os dados na sua conta de armazenamento Azure são duráveis e altamente disponíveis, seguros e massivamente escaláveis.

Para saber como criar uma conta de armazenamento do Azure, veja [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

O Azure Storage oferece vários tipos de contas de armazenamento. Cada tipo suporta diferentes funcionalidades e tem o seu próprio modelo de preços. Considere estas diferenças antes de criar uma conta de armazenamento para determinar o tipo de conta que é melhor para as suas aplicações. Os tipos de contas de armazenamento são:

- **Contas v2 para fins gerais**: Tipo de conta de armazenamento básico para bolhas, ficheiros, filas e tabelas. Recomendado para a maioria dos cenários usando O Armazenamento Azure.
- **Contas v1 para fins gerais**: Tipo de conta legado para bolhas, ficheiros, filas e tabelas. Utilize, em vez disso, as contas v2 de uso geral, sempre que possível.
- **Contas BlockBlobStorage**: Contas de armazenamento com características de desempenho premium para bolhas de blocos e bolhas de apêndice. Recomendado para cenários com altas taxas de transações, ou cenários que usem objetos menores ou exijam uma latência de armazenamento consistentemente baixa.
- **Contas de arquivamento de ficheiros**: Contas de armazenamento apenas de ficheiros com características de desempenho premium. Recomendado para aplicações de escala de empresa ou de alto desempenho.
- **BlobStorage accounts**: Legacy Blob-only storage accounts. Utilize, em vez disso, as contas v2 de uso geral, sempre que possível.

A tabela a seguir descreve os tipos de contas de armazenamento, os serviços que suportam e os modelos de implantação suportados para cada tipo de conta:

| Tipo de conta de armazenamento | Serviços suportados | Opções de redundância | Modelo de implantação<sup>1</sup> |
|--|--|--|--|
| V2 de uso geral | Blob, Arquivo, Fila, Mesa, Disco e Data Lake Gen2<sup>2</sup> | LRS, GRS, RA-GRS, ZRS, GZRS, RA-GZRS<sup>3</sup> | Resource Manager |
| V1 de uso geral | Blob, Arquivo, Fila, Tabela e Disco | LRS, GRS, RA-GRS | Gestor de Recursos, Clássico |
| BlockBlobStorage | Blob (blobs de bloco e apenas bolhas de apêndice) | LRS, ZRS<sup>3</sup> | Resource Manager |
| Arquitorage de arquivos | Apenas arquivar | LRS, ZRS<sup>3</sup> | Resource Manager |
| BlobStorage | Blob (blobs de bloco e apenas bolhas de apêndice) | LRS, GRS, RA-GRS | Resource Manager |

<sup>1</sup> Recomenda-se a utilização do modelo de implementação do Gestor de Recursos Azure. As contas de armazenamento que utilizam o modelo de implementação clássico ainda podem ser criadas em alguns locais, e as contas clássicas existentes continuam a ser suportadas. Para obter mais informações, consulte [Azure Resource Manager vs. design clássico: Compreenda os modelos de implantação e o estado dos seus recursos.](../../azure-resource-manager/management/deployment-models.md)

<sup>2</sup> Azure Data Lake Storage Gen2 é um conjunto de capacidades dedicadas à análise de big data, construídas no armazenamento de Azure Blob. Data Lake Storage Gen2 só é suportado em contas de armazenamento V2 de uso geral com espaço hierárquico habilitado. Para obter mais informações sobre data lake storage gen2, consulte [Introdução ao Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md).

<sup>3</sup> O armazenamento redundante de zonas (ZRS) e o armazenamento redundante em zonas geo-zonas (GZRS/RA-GZRS) só estão disponíveis para as contas V2, BlockBlobStorage e FileStorage em determinadas regiões. Para obter mais informações sobre as opções de redundância do [Azure Storage, consulte a redundância do Azure Storage](storage-redundancy.md).

### <a name="storage-account-redundancy"></a>Redundância da conta de armazenamento

As opções de despedimento de uma conta de armazenamento incluem:

- **Armazenamento localmente redundante (LRS)**: Uma estratégia simples e de redundância de baixo custo. Os dados são copiados sincronizadamente três vezes num único local físico na região primária.
- **Armazenamento redundante de zona (ZRS)**: Redundância para cenários que exijam elevada disponibilidade. Os dados são copiados sincronizadamente em três zonas de disponibilidade de Azure na região primária.
- **Armazenamento geo-redundante (GRS)**: Redundância regional transfronteiriça para proteger contra interrupções regionais. Os dados são copiados sincronizadamente três vezes na região primária, depois copiados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento geo-redundante de acesso à leitura (RA-GRS).
- **Armazenamento de zonas-redundantes (GZRS)**: Redundância para cenários que exijam elevada disponibilidade e durabilidade máxima. Os dados são copiados sincronizadamente em três zonas de disponibilidade de Azure na região primária, depois copiados assíncronamente para a região secundária. Para ler o acesso aos dados na região secundária, permita o armazenamento de zonas de acesso à leitura redundantes (RA-GZRS).

Para obter mais informações sobre as opções de despedimento no Azure Storage, consulte [a redundância do Azure Storage](storage-redundancy.md).

### <a name="general-purpose-v2-accounts"></a>Contas de armazenamento para fins gerais v2

As contas de armazenamento V2 de uso geral suportam as mais recentes funcionalidades de Armazenamento Azure e incorporam toda a funcionalidade das contas de armazenamento V1 e Blob para fins gerais. As contas v2 de uso geral oferecem os preços mais baixos de capacidade por gigabyte para o Azure Storage, bem como os preços de transação competitivos da indústria. As contas de armazenamento v2 para fins gerais suportam estes serviços de Armazenamento Azure:

- Blobs (todos os tipos: Bloco, Apêndice, Página)
- Data Lake Gen2
- Ficheiros
- Discos
- Filas
- Tabelas

> [!NOTE]
> A Microsoft recomenda a utilização de uma conta de armazenamento v2 para a maioria dos cenários. Pode facilmente atualizar uma conta de armazenamento v1 ou Blob para uma conta v2 de uso geral sem tempo de inatividade e sem necessidade de copiar dados. No entanto, a atualização não pode ser desfeita.
>
> Para obter mais informações sobre a atualização para uma conta V2 para fins gerais, consulte [a atualização para uma conta de armazenamento V2 para fins gerais](storage-account-upgrade.md).

As contas de armazenamento v2 de uso geral oferecem vários níveis de acesso para armazenar dados com base nos seus padrões de utilização. Para obter mais informações, consulte [os níveis de acesso para obter dados de blob de blocos](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Contas de armazenamento para fins gerais v1

As contas de armazenamento V1 de uso geral fornecem acesso a todos os serviços de Armazenamento Azure, mas podem não ter as funcionalidades mais recentes ou os preços mais baixos por gigabyte. As contas de armazenamento v1 para fins gerais suportam estes serviços de Armazenamento Azure:

- Bolhas (todos os tipos)
- Ficheiros
- Discos
- Filas
- Tabelas

Pode utilizar contas v1 para estes cenários:

- As suas aplicações requerem o modelo de implementação clássico do Azure. As contas v2 de uso geral e as contas de armazenamento Blob suportam apenas o modelo de implementação do Gestor de Recursos Azure.

- As suas aplicações são intensivas em transações ou utilizam largura de banda de geo-replicação significativa, mas não requerem grande capacidade. Neste caso, o v1 de uso geral pode ser a escolha mais económica.

- Utiliza uma versão do Serviço de [Armazenamento REST API](/rest/api/storageservices/Versioning-for-the-Azure-Storage-Services) que é anterior a 2014-02-14 ou uma biblioteca de clientes com uma versão inferior a 4.x. Não pode atualizar a sua candidatura.

> [!NOTE]
> Embora a Microsoft recomende contas v2 para a maioria dos cenários, a Microsoft continuará a suportar contas v1 de uso geral para clientes novos e já existentes. Pode criar contas de armazenamento v1 para fins gerais em novas regiões sempre que o Azure Storage estiver disponível nessas regiões. A Microsoft não tem atualmente um plano para deprecatar o suporte para contas V1 de uso geral e fornecerá pelo menos um ano de antecedência antes de deprecá-lo. A Microsoft continuará a fornecer atualizações de segurança para as contas V1 de uso geral, mas não é esperado um novo desenvolvimento de funcionalidades para este tipo de conta.
>
> A partir de 1 de outubro de 2020, os preços das contas v1 para fins gerais nas novas regiões de Armazenamento Azure equivalem a preços para as contas v2 para fins gerais nessas regiões. Os preços nas regiões de armazenamento Azure existentes não mudaram. Para obter detalhes sobre preços para contas v1 para fins gerais numa região específica, consulte a página de preços do Azure Storage. Escolha a sua região e, em seguida, ao lado **das ofertas de preços,** selecione **Outros**.

### <a name="blockblobstorage-accounts"></a>Contas blockBlobStorage

Uma conta BlockBlobStorage é uma conta de armazenamento especializada no nível de desempenho premium para armazenar dados de objetos não estruturados como bolhas de bloco ou bolhas de apêndice. Em comparação com as contas v2 e BlobStorage de fins gerais, as contas blockBlobStorage fornecem baixas, consistentes taxas de latência e taxas de transação mais elevadas.

As contas blockBlobStorage não suportam atualmente tiering para camadas de acesso quentes, frescos ou de arquivo. Este tipo de conta de armazenamento não suporta bolhas de página, tabelas ou filas.

### <a name="filestorage-accounts"></a>Contas de arquivamento de ficheiros

Uma conta FileStorage é uma conta de armazenamento especializada usada para armazenar e criar ações de ficheiros premium. Este tipo de conta de armazenamento suporta ficheiros, mas não bloqueia bolhas, bolhas de apêndice, bolhas de página, tabelas ou filas.

As contas FileStorage oferecem características únicas dedicadas ao desempenho, tais como o rebentamento do IOPS. Para obter mais informações sobre estas características, consulte a secção [de níveis](../files/storage-files-planning.md#storage-tiers) de armazenamento de partilha de ficheiros do guia de planeamento de ficheiros.

## <a name="naming-storage-accounts"></a>Nomear contas de armazenamento

Para atribuir um nome à sua conta de armazenamento, mantenha estas regras em mente:

- Os nomes das contas de armazenamento têm de ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
- O nome da sua conta do Storage tem de ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="performance-tiers"></a>Escalões de desempenho

Dependendo do tipo de conta de armazenamento que cria, pode escolher entre os níveis de desempenho standard e premium. O quadro seguinte resume quais os níveis de desempenho disponíveis para que tipo de conta de armazenamento.

| Tipo de conta de armazenamento | Níveis de desempenho suportados |
|--|--|
| V2 de uso geral | Standard, Premium<sup>1</sup> |
| V1 de uso geral | Standard, Premium<sup>1</sup> |
| BlockBlobStorage | Premium |
| Arquitorage de arquivos | Premium |
| BlobStorage | Standard |

<sup>1</sup> O desempenho premium para as contas v2 e v1 de uso geral está disponível apenas para o disco e para a página blob. O desempenho premium para blobs de bloco ou apêndice só está disponível nas contas BlockBlobStorage. O desempenho premium dos ficheiros só está disponível nas contas FileStorage.

### <a name="general-purpose-storage-accounts"></a>Contas de armazenamento para fins gerais

As contas de armazenamento para fins gerais podem ser configuradas para qualquer um dos seguintes níveis de desempenho:

- Um nível de desempenho padrão para armazenar bolhas, ficheiros, tabelas, filas e discos de máquina virtual Azure. Para obter mais informações sobre os objetivos de escalabilidade das contas de armazenamento padrão, consulte [os objetivos de escalabilidade para contas de armazenamento padrão.](scalability-targets-standard-account.md)
- Um nível de desempenho premium para armazenar discos de máquinas virtuais não geridos. A Microsoft recomenda a utilização de discos geridos com máquinas virtuais Azure em vez de discos não geridos. Para obter mais informações sobre os objetivos de escalabilidade para o nível de desempenho premium, consulte [os objetivos de escalabilidade para contas de armazenamento de blob de página premium](../blobs/scalability-targets-premium-page-blobs.md).

### <a name="blockblobstorage-storage-accounts"></a>Contas de armazenamento BlockBlobStorage

As contas de armazenamento BlockBlobStorage fornecem um nível de desempenho premium para armazenar bolhas de blocos e bolhas de apêndice. Para obter mais informações, consulte [os objetivos de Escalaability para contas de armazenamento de blob de bloco premium](../blobs/scalability-targets-premium-block-blobs.md).

### <a name="filestorage-storage-accounts"></a>Contas de armazenamento de arquiteação de ficheiros

As contas de armazenamento fileStorage fornecem um nível de desempenho premium para ações de ficheiros Azure. Para obter mais informações, consulte [os objetivos de escalabilidade e desempenho dos Ficheiros Azure.](../files/storage-files-scale-targets.md)

## <a name="access-tiers-for-block-blob-data"></a>Níveis de acesso para dados de blob de bloco

O Azure Storage fornece diferentes opções para aceder a dados de blob de blocos baseados em padrões de utilização. Cada nível de acesso no Azure Storage é otimizado para um padrão particular de utilização de dados. Ao selecionar o nível de acesso certo para as suas necessidades, pode armazenar os seus dados blob de bloco da forma mais rentável.

Os níveis de acesso disponíveis são:

- O nível de acesso **quente.** Este nível é otimizado para o acesso frequente de objetos na conta de armazenamento. O acesso aos dados no nível quente é mais rentável, enquanto os custos de armazenamento são mais elevados. Novas contas de armazenamento são criadas no nível quente por defeito.
- O nível de acesso **cool.** Este nível é otimizado para armazenar grandes quantidades de dados que são pouco acessados e armazenados durante pelo menos 30 dias. Armazenar dados no nível cool é mais rentável, mas aceder a esses dados pode ser mais caro do que aceder a dados no nível quente.
- A camada **Arquivo**. Este nível está disponível apenas para bolhas de bloco individuais. O nível de arquivo está otimizado para dados que podem tolerar várias horas de latência de recuperação e que permanecerão no nível de arquivo por pelo menos 180 dias. O nível de arquivo é a opção mais rentável para armazenar dados. No entanto, aceder a esses dados é mais caro do que aceder a dados nos níveis quentes ou frescos.

Se houver uma alteração no padrão de utilização dos seus dados, pode alternar entre estes níveis de acesso a qualquer momento. Para obter mais informações sobre os níveis de acesso, consulte [o armazenamento Azure Blob: níveis de acesso quentes, frescos e de arquivo.](../blobs/storage-blob-storage-tiers.md)

A tabela que se segue mostra quais os níveis de acesso disponíveis para bolhas em cada tipo de conta de armazenamento.

| Tipo de conta de armazenamento | Níveis de acesso suportados |
|--|--|
| V2 de uso geral | Quente, Fresco, Arquivo<sup>1</sup> |
| V1 de uso geral | N/D |
| BlockBlobStorage | N/D |
| Arquitorage de arquivos | N/D |
| BlobStorage | Quente, Fresco, Arquivo<sup>1</sup> |

<sup>1</sup> Arquivar o armazenamento e o nível de nível de bolhas apenas suportam bolhas de bloco. O nível Archive está disponível apenas ao nível de uma bolha individual, não ao nível da conta de armazenamento. Para mais informações, consulte [os níveis de acesso para armazenamento Azure Blob - quente, fresco e arquivo](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> A alteração do nível de acesso para uma conta de armazenamento ou bolha existente pode resultar em custos adicionais. Para obter mais informações, consulte [a faturação da conta de armazenamento.](#storage-account-billing)

## <a name="encryption"></a>Encriptação

Todos os dados da sua conta de armazenamento estão encriptados no lado do serviço. Para obter mais informações sobre encriptação, consulte [a Encriptação do Serviço de Armazenamento Azure para obter dados em repouso](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Pontos finais da conta do Storage

Uma conta de armazenamento fornece um espaço de nome único em Azure para os seus dados. Cada objeto que armazena no Azure Storage tem um endereço que inclui o nome da sua conta única. A combinação do nome da conta e do ponto final do serviço Azure Storage forma os pontos finais da sua conta de armazenamento.

A tabela que se segue lista os pontos finais de cada um dos serviços de Armazenamento Azure.

| Serviço de armazenamento | Ponto final |
|--|--|
| Armazenamento de blobs | `https://<storage-account>.blob.core.windows.net` |
| Armazenamento do Azure Data Lake Ger2 | `https://<storage-account>.dfs.core.windows.net` |
| Ficheiros do Azure | `https://<storage-account>.file.core.windows.net` |
| Armazenamento de filas | `https://<storage-account>.queue.core.windows.net` |
| Table Storage | `https://<storage-account>.table.core.windows.net` |

> [!NOTE]
> As contas de armazenamento de blocos e blob expõem apenas o ponto final do serviço Blob.

Construa o URL para aceder a um objeto numa conta de armazenamento, anexando a localização do objeto na conta de armazenamento para o ponto final. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também pode configurar a sua conta de armazenamento para usar um domínio personalizado para bolhas. Para mais informações, consulte [configurar um nome de domínio personalizado para a sua conta de Armazenamento Azure.](../blobs/storage-custom-domain-name.md)  

## <a name="control-access-to-account-data"></a>Controlar o acesso aos dados da conta

Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta. Tem controlo sobre quem pode aceder aos seus dados e quais as permissões que têm.

Todos os pedidos feitos contra a sua conta de armazenamento devem ser autorizados. Ao nível do serviço, o pedido deve incluir um *cabeçalho de autorização* válido. Especificamente, este cabeçalho inclui todas as informações necessárias para que o serviço valide o pedido antes de executá-lo.

Pode conceder acesso aos dados na sua conta de armazenamento utilizando qualquer uma das seguintes abordagens:

- **Diretório Ativo Azure:** Utilize credenciais do Azure Ative Directory (Azure AD) para autenticar um utilizador, grupo ou outra identidade para acesso a dados de blob e fila. Se a autenticação de uma identidade for bem sucedida, então a Azure AD devolve um símbolo para usar ao autorizar o pedido ao armazenamento de Azure Blob ou ao armazenamento da fila. Para mais informações, consulte [autenticar o acesso ao Azure Storage utilizando o Azure Ative Directory](storage-auth-aad.md).
- **Autorização de chave partilhada:** Utilize a chave de acesso à sua conta de armazenamento para construir uma cadeia de ligação que a sua aplicação utiliza em tempo de execução para aceder ao Azure Storage. Os valores da cadeia de ligação são utilizados para construir o *cabeçalho de autorização* que é passado para o Azure Storage. Para obter mais informações, consulte as cordas de [conexão Configure Azure Storage](storage-configure-connection-string.md).
- **Assinatura de acesso partilhado:** Uma assinatura de acesso partilhado (SAS) é um símbolo que permite o acesso delegado aos recursos na sua conta de armazenamento. O token SAS encapsula todas as informações necessárias para autorizar um pedido de armazenamento Azure no URL. Quando criar um SAS, pode especificar quais as permissões que o SAS concede a um recurso e o intervalo sobre o qual as permissões são válidas. Um token SAS pode ser assinado com credenciais AZure AD ou com Chave Partilhada. Para obter mais informações, consulte [Grant acesso limitado aos recursos de Armazenamento Azure usando assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).

> [!NOTE]
> Autenticar utilizadores ou aplicações utilizando credenciais Azure AD proporciona uma segurança superior e facilidade de utilização sobre outros meios de autorização. Embora possa continuar a utilizar a autorização da Chave Partilhada com as suas aplicações, a utilização do Azure AD contorna a necessidade de armazenar a chave de acesso à sua conta com o seu código. Também pode continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder acesso a recursos finos na sua conta de armazenamento, mas a Azure AD oferece capacidades semelhantes sem a necessidade de gerir fichas SAS ou se preocupar em revogar um SAS comprometido.
>
> A Microsoft recomenda a utilização da autorização AD AD para as suas aplicações de armazenamento Azure, quando possível.

## <a name="copying-data-into-a-storage-account"></a>Copiar dados numa conta de armazenamento

A Microsoft fornece utilitários e bibliotecas para importar os seus dados de dispositivos de armazenamento no local ou fornecedores de armazenamento em nuvem de terceiros. A solução que usa depende da quantidade de dados que está a transferir.

Quando faz o upgrade para uma conta V2 de uso geral a partir de uma conta de armazenamento v1 ou Blob para fins gerais, os seus dados são automaticamente migrados. A Microsoft recomenda esta via para a atualização da sua conta. No entanto, se decidir transferir dados de uma conta V1 para uma conta de armazenamento Blob, migrará os seus dados manualmente, utilizando as ferramentas e bibliotecas descritas abaixo.

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Pode utilizar o AzCopy para copiar dados numa conta de armazenamento Blob a partir de uma conta de armazenamento de fim geral existente, ou para enviar dados de dispositivos de armazenamento no local. Para obter mais informações, veja [Transferir dados com o Utilitário de Linha de Comandos AzCopy](./storage-use-azcopy-v10.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de Movimento de Dados

A biblioteca de movimento de dados do Armazenamento do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples, semelhantes às de AzCopy. Pode utilizar a biblioteca de movimentos de dados para tirar partido das funcionalidades AzCopy de forma nativa. Para mais informações, consulte [a Biblioteca de Movimentos de Dados de Armazenamento de Azure para .NET](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Pode criar uma aplicação personalizada para migrar os seus dados de uma conta de armazenamento V1 para uma conta de armazenamento Blob. Utilize uma das bibliotecas de clientes Azure ou os serviços de armazenamento Azure REST API. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para obter mais informações sobre a Azure Storage REST API, consulte [Azure Storage Services REST API Reference](/rest/api/storageservices/).

> [!IMPORTANT]
> Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação com o blob. Se copiar um blob com encriptação do lado do cliente, certifique-se de que a operação de cópia preserva os metadados do blob e, especialmente, os metadados relacionados com a encriptação. Se copiar um blob sem os metadados de encriptação, não poderá obter novamente o conteúdo do mesmo. Para obter mais informações acerca dos metadados relacionados com a encriptação, veja [Encriptação do Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

## <a name="storage-account-billing"></a>Faturação da conta do Storage

Faturas de Armazenamento Azure com base no uso da sua conta de armazenamento. Todos os objetos numa conta do Storage são faturados em conjunto como um grupo. Os custos de armazenagem são calculados de acordo com os seguintes fatores:

- **Região** refere-se à região geográfica em que a sua conta está sediada.
- **O tipo de conta** refere-se ao tipo de conta de armazenamento que está a utilizar.
- **O nível de acesso** refere-se ao padrão de utilização de dados especificado para a sua conta de armazenamento v2 ou Blob para fins gerais.
- **A capacidade** refere-se à quantidade de loteamento da sua conta de armazenamento que está a usar para armazenar dados.
- **A replicação** determina quantas cópias dos seus dados são mantidas ao mesmo tempo e em que locais.
- **As transações** referem-se a todas as operações de leitura e escrita para o Azure Storage.
- **A saída de dados** refere-se a quaisquer dados transferidos de uma região de Azure. Quando os dados na sua conta de armazenamento são acedidos por uma aplicação que não está a funcionar na mesma região, é cobrado por saída de dados. Para obter informações sobre a utilização de grupos de recursos para agrupar os seus dados e serviços na mesma região para limitar as taxas de saída, veja [o que é um grupo de recursos Azure?](/azure/cloud-adoption-framework/govern/resource-consistency/resource-access-management#what-is-an-azure-resource-group)

A página [Preços de Armazenamento do Azure](https://azure.microsoft.com/pricing/details/storage/) fornece informações detalhadas sobre os preços com base na capacidade de armazenamento, replicação e transações. A página [Detalhes de Preços das Transferências de Dados](https://azure.microsoft.com/pricing/details/data-transfers/) fornece informações detalhadas sobre os preços de saída de dados. Pode utilizar a [Calculadora de Preços do Storage do Azure](https://azure.microsoft.com/pricing/calculator/?scenario=data-management) para o ajudar a avaliar os custos.

[!INCLUDE [cost-management-horizontal](../../../includes/cost-management-horizontal.md)]

## <a name="next-steps"></a>Passos seguintes

- [Criar uma conta de armazenamento](storage-account-create.md)
- [Criar uma conta de armazenamento de blob de blocos](../blobs/storage-blob-create-account-block-blob.md)
- [Upgrade para uma conta de armazenamento v2 para fins gerais](storage-account-upgrade.md)
- [Recuperar uma conta de armazenamento eliminada](storage-account-recover.md)