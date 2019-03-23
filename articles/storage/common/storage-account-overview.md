---
title: Descrição geral da conta de armazenamento do Azure | Documentos da Microsoft
description: Compreenda as opções para criar e utilizar uma conta de armazenamento do Azure.
services: storage
author: tamram
ms.service: storage
ms.topic: article
ms.date: 03/06/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: ca71fdc8074e56adc8595ee905d5b1db3b60cef1
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371815"
---
# <a name="azure-storage-account-overview"></a>Descrição geral da conta de armazenamento do Azure

Uma conta de armazenamento do Azure contém todos os seus objetos de dados do armazenamento do Azure: blobs, ficheiros, filas, tabelas e discos. Dados na sua conta de armazenamento do Azure são durável e elevada disponibilidade, seguro, dimensionável em massa e acessível a partir de qualquer lugar no mundo através de HTTP ou HTTPS. 

Para saber como criar uma conta de armazenamento do Azure, veja [criar uma conta de armazenamento](storage-quickstart-create-account.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

### <a name="general-purpose-v2-accounts"></a>Contas de armazenamento para fins gerais v2

Contas de armazenamento para fins gerais v2 de suportar as funcionalidades de armazenamento do Azure mais recente e incorporam toda a funcionalidade de fins gerais v1 e contas de armazenamento de Blobs. Contas para fins gerais v2 fornecem o mais baixo por gigabyte os preços de capacidade do armazenamento do Azure, bem como competitiva em termos da indústria para transações. Contas de armazenamento para fins gerais v2 suportam estes serviços de armazenamento do Azure:

- BLOBs (todos os tipos: Bloquear, acrescentar, página)
- Ficheiros
- Discos
- Filas
- Tabelas

> [!NOTE]
> A Microsoft recomenda utilizar uma conta de armazenamento para fins gerais v2 na maioria dos cenários. Pode facilmente atualizar uma para fins gerais v1 ou a conta de armazenamento de BLOBs para uma conta para fins gerais v2, sem períodos de indisponibilidade e sem a necessidade de copiar dados.
>
> Para obter mais informações sobre como atualizar para uma conta para fins gerais v2, consulte [atualizar para uma conta de armazenamento para fins gerais v2](storage-account-upgrade.md).

Contas de armazenamento para fins gerais v2 oferecem várias camadas de acesso para armazenar dados com base nos seus padrões de utilização. Para obter mais informações, consulte [camadas de acesso para o bloco de dados de BLOBs](#access-tiers-for-block-blob-data).

### <a name="general-purpose-v1-accounts"></a>Contas de armazenamento para fins gerais v1

Contas de fins gerais v1 fornecem acesso a todos os serviços de armazenamento do Azure, mas podem não ter as funcionalidades mais recentes ou o mais baixos por gigabyte pelas operações de preços. Contas de armazenamento para fins gerais v1 suportam estes serviços de armazenamento do Azure:

- BLOBs (todos os tipos)
- Ficheiros
- Discos
- Filas
- Tabelas

Embora as contas para fins gerais v2 são recomendadas na maioria dos casos, contas de fins gerais v1 são mais adequadas para estes cenários: 

* As aplicações requerem o modelo de implementação clássica do Azure. Contas para fins gerais v2 e contas de armazenamento de BLOBs suportam apenas o modelo de implementação Azure Resource Manager.

* As aplicações são intensidade de transações ou utilizam largura de banda de replicação geográfica significativa, mas não necessitam de grande capacidade. Neste caso, para fins gerais v1 pode ser a opção mais económica.

* Utiliza uma versão da [API REST dos Serviços do Storage](https://msdn.microsoft.com/library/azure/dd894041.aspx) anterior a 14/02/2014 ou uma biblioteca de cliente com uma versão inferior à 4.x e não pode atualizar a sua aplicação.

### <a name="block-blob-storage-accounts"></a>Contas de armazenamento de BLOBs de bloco

Uma conta de armazenamento de BLOBs de bloco é uma conta de armazenamento especializada para armazenar dados de objetos não estruturados como blobs de blocos ou blobs de acréscimo. Contas de armazenamento de BLOBs de bloco oferecem várias camadas de acesso para armazenar dados com base nos seus padrões de utilização. Para obter mais informações, consulte [camadas de acesso para o bloco de dados de BLOBs](#access-tiers-for-block-blob-data).

## <a name="naming-storage-accounts"></a>Nomenclatura de contas de armazenamento

Para atribuir um nome à sua conta de armazenamento, mantenha estas regras em mente:

- Os nomes das contas do Storage devem ter entre 3 e 24 carateres de comprimento e apenas podem conter números e letras minúsculas.
- O nome da sua conta do Storage tem de ser exclusivo no Azure. Duas contas de armazenamento não podem ter o mesmo nome.

## <a name="performance-tiers"></a>Escalões de desempenho

Contas de armazenamento para fins gerais podem ser configuradas para qualquer um dos seguintes escalões de desempenho:

* Um escalão de desempenho standard para armazenar blobs, ficheiros, tabelas, filas e discos de máquina virtual do Azure.
* Um escalão de desempenho premium para armazenar apenas os discos de máquinas de virtuais não geridos.

## <a name="access-tiers-for-block-blob-data"></a>Escalões de acesso para os dados de BLOBs de bloco

Armazenamento do Azure oferece opções diferentes para aceder aos dados de blob de blocos com base nos padrões de utilização. Cada escalão de acesso no armazenamento do Azure está otimizado para um determinado padrão de utilização de dados. Ao selecionar a camada de acesso corretas para as suas necessidades, pode armazenar seus dados de BLOBs de bloco da maneira mais econômica.

Os escalões de acesso disponíveis são:

* O **frequente** camada de acesso, o que está otimizada para acesso frequente de objetos na conta de armazenamento. Aceder aos dados na camada frequente é mais econômica, enquanto os custos de armazenamento são um pouco mais. Novas contas de armazenamento são criadas de acesso frequente escalão por predefinição.
* O **esporádico** camada de acesso, o que está otimizada para armazenar grandes quantidades de dados que são acedidos com pouca frequência e armazenados durante, pelo menos, 30 dias. Armazenamento de dados na camada de acesso esporádica é mais rentável, mas o acesso aos dados pode ser um pouco mais dispendioso do que a aceder aos dados na camada frequente.
* O **arquivo** escalão, o que está disponível apenas para blobs de blocos individuais. A camada de arquivo é otimizada para dados que podem tolerar várias horas de latência de obtenção e irão permanecer na camada de arquivo por, pelo menos, 180 dias. A camada de arquivo é a opção mais económica para armazenar dados, mas o acesso aos dados é mais caro que acedem aos dados na escalões de acesso esporádicas ou frequente. 

Se houver uma alteração no padrão de utilização dos seus dados, pode alternar entre estas camadas de acesso em qualquer altura. Para obter mais informações sobre as camadas de acesso, consulte [armazenamento de Blobs do Azure: frequente, esporádico e de arquivo acesso](../blobs/storage-blob-storage-tiers.md).

> [!IMPORTANT]
> A alteração da camada de acesso para uma conta de armazenamento existente ou um blob poderá resultar em encargos adicionais. Para obter mais informações, consulte a [conta de armazenamento de faturação secção](#storage-account-billing).

## <a name="replication"></a>Replicação

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Para obter mais informações sobre a replicação de armazenamento, consulte [replicação de armazenamento do Azure](storage-redundancy.md).

## <a name="encryption"></a>Encriptação

Todos os dados na sua conta de armazenamento são encriptados no lado do serviço. Para obter mais informações sobre a encriptação, consulte [encriptação do serviço de armazenamento do Azure para dados Inativos](storage-service-encryption.md).

## <a name="storage-account-endpoints"></a>Pontos finais da conta do Storage

Uma conta de armazenamento fornece um espaço de nomes exclusivo no Azure para os seus dados. Cada objeto que armazena no armazenamento do Azure tem um endereço que inclui o seu nome de conta exclusivo. A combinação do nome da conta e o ponto de extremidade do serviço de armazenamento do Azure forma os pontos finais para a sua conta de armazenamento.

Por exemplo, se a sua conta de armazenamento para fins gerais com o nome *mystorageaccount*, em seguida, os pontos de extremidade padrão para essa conta são:

* Armazenamento de BLOBs: http://*mystorageaccount*. blob.core.windows.net
* Armazenamento de tabelas: http://*mystorageaccount*. table.core.windows.net
* Armazenamento de filas: http://*mystorageaccount*. queue.core.windows.net
* Os ficheiros do Azure: http://*mystorageaccount*. file.core.windows.net

> [!NOTE]
> Conta do Blob storage expõe apenas o Blob ponto final de serviço.

O URL para aceder a um objeto numa conta de armazenamento é construído ao acrescentar a localização do objeto na conta de armazenamento para o ponto final. Por exemplo, um endereço de blob pode ter este formato: http://*mystorageaccount*.blob.core.windows.net/*mycontainer*/*myblob*.

Também pode configurar a sua conta de armazenamento para utilizar um domínio personalizado para blobs. Para obter mais informações, consulte [configurar um nome de domínio personalizado para a sua conta de armazenamento do Azure](../blobs/storage-custom-domain-name.md).  

## <a name="control-access-to-account-data"></a>Controlar o acesso a dados da conta

Por predefinição, os dados na sua conta só estão disponíveis para si, o proprietário da conta. Tem controle sobre quem pode aceder aos seus dados e quais as permissões que têm.

Todos os pedidos efetuados em relação a sua conta de armazenamento tem de ser autorizado. Ao nível do serviço, o pedido tem de incluir um válido *autorização* cabeçalho, que inclui todas as informações necessárias para o serviço validar o pedido antes de executá-la.

Pode conceder acesso aos dados na sua conta de armazenamento através de qualquer uma das seguintes abordagens:

- **Azure Active Directory:** Utilize as credenciais do Azure Active Directory (Azure AD) para autenticar um utilizador, grupo ou outra identidade de acesso a dados de BLOBs e filas. Se a autenticação de uma identidade é efetuada com êxito, o AD do Azure devolve um token para utilizar em autorizar a solicitação para o armazenamento de Blobs do Azure ou o armazenamento de filas. Para obter mais informações, consulte [autenticar o acesso ao armazenamento do Azure com o Azure Active Directory](storage-auth-aad.md).
- **Autorização de chave partilhada:** Utilize a chave de acesso da conta de armazenamento para construir uma cadeia de ligação que a aplicação utiliza no tempo de execução para aceder ao armazenamento do Azure. Os valores na cadeia de ligação são utilizados para construir a *autorização* cabeçalho que é passado para o armazenamento do Azure. Para obter mais informações, consulte [cadeias de ligação de configurar o armazenamento do Azure](storage-configure-connection-string.md).
- **Assinatura de acesso partilhado:** Utilize assinaturas de acesso partilhado para delegar acesso a recursos na sua conta de armazenamento, se não estiver a utilizar autenticação do Azure AD. Uma assinatura de acesso partilhado é um token que encapsula todas as informações necessárias para autorizar um pedido ao armazenamento do Azure no URL. Pode especificar o recurso de armazenamento, as permissões concedidas e o intervalo durante o qual as permissões são válidas como parte da assinatura de acesso partilhado. Para obter mais informações, consulte [Using partilhado assinaturas de acesso (SAS)](storage-dotnet-shared-access-signature-part-1.md).

> [!NOTE]
> Autenticação de utilizadores ou aplicações que utilizam as credenciais do Azure AD fornece segurança superior e a facilidade de utilização ao longo de outros meios de autorização. Enquanto pode continuar a utilizar a autorização de chave partilhada com as suas aplicações, utilizar o Azure AD evita a necessidade de armazenar a chave de acesso da conta com o seu código. Também pode continuar a utilizar assinaturas de acesso partilhado (SAS) para conceder um acesso otimizado aos recursos na sua conta de armazenamento, mas o Azure AD oferece recursos semelhantes sem a necessidade de gerir os tokens de SAS ou se preocupar sobre revogar uma SAS comprometida. 
>
> A Microsoft recomenda utilizar autenticação do Azure AD para o armazenamento do Azure BLOBs e filas aplicações sempre que possível.

## <a name="copying-data-into-a-storage-account"></a>Copiar dados para uma conta de armazenamento

A Microsoft fornece utilitários e bibliotecas para importar os dados a partir de dispositivos de armazenamento no local ou fornecedores de armazenamento na cloud de terceiros. Que solução que utiliza depende a quantidade de dados que estiver a transferir. 

Quando atualizar para uma conta de um para fins gerais v1 para fins gerais v2 ou a conta de armazenamento de BLOBs, os seus dados é automaticamente migrados. A Microsoft recomenda este caminho para atualizar a sua conta. No entanto, se optar por mover dados de uma conta para fins gerais v1 para uma conta de armazenamento de BLOBs, em seguida, terá de migrar os seus dados manualmente, usando as ferramentas e bibliotecas descritas abaixo. 

### <a name="azcopy"></a>AzCopy

O AzCopy é um utilitário de linha de comandos do Windows concebido para copiar dados de e para o Storage do Azure com um elevado desempenho. Pode utilizar o AzCopy para copiar dados para uma conta de armazenamento de BLOBs a partir de uma conta de armazenamento para fins gerais existentes ou para carregar dados a partir de dispositivos de armazenamento no local. Para obter mais informações, veja [Transferir dados com o Utilitário de Linha de Comandos AzCopy](../common/storage-use-azcopy.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="data-movement-library"></a>Biblioteca de Movimento de Dados

A biblioteca de movimento de dados do Armazenamento do Azure para .NET baseia-se na arquitetura de movimento de dados central do AzCopy. A biblioteca foi concebida para operações de transferência de dados de elevado desempenho, fiáveis e simples, semelhantes às de AzCopy. Pode utilizá-la para tirar partido das funcionalidades fornecidas pelo AzCopy na sua aplicação de forma nativa, sem ter de executar e monitorizar instâncias externas do AzCopy. Para obter mais informações, veja [Biblioteca de Movimento de Dados do Armazenamento do Azure para .Net](https://github.com/Azure/azure-storage-net-data-movement)

### <a name="rest-api-or-client-library"></a>API REST ou biblioteca de cliente

Pode criar uma aplicação personalizada para migrar os dados para uma conta do Blob Storage utilizando uma das bibliotecas de cliente do Azure ou a API REST dos serviços do Storage do Azure. O Storage do Azure fornece bibliotecas de cliente avançadas para várias linguagens e plataformas como .NET, Java, C++, Node.JS, PHP, Ruby e Python. As bibliotecas de cliente oferecem funcionalidades avançadas, tais como lógica de repetição, registo e carregamentos paralelos. Também pode desenvolver diretamente na API REST, que pode ser chamada por qualquer linguagem que efetue pedidos HTTP/HTTPS.

Para obter mais informações sobre a API de REST de armazenamento do Azure, consulte [referência da API do REST do Azure Storage Services](https://docs.microsoft.com/rest/api/storageservices/). 

> [!IMPORTANT]
> Os blobs encriptados através de encriptação do lado do cliente armazenam os metadados relacionados com a encriptação com o blob. Se copiar um blob com encriptação do lado do cliente, certifique-se de que a operação de cópia preserva os metadados do blob e, especialmente, os metadados relacionados com a encriptação. Se copiar um blob sem os metadados de encriptação, não poderá obter novamente o conteúdo do mesmo. Para obter mais informações acerca dos metadados relacionados com a encriptação, veja [Encriptação do Lado do Cliente do Armazenamento do Azure](../common/storage-client-side-encryption.md?toc=%2fazure%2fstorage%2fblobs%2ftoc.json).

### <a name="azure-importexport-service"></a>Serviço Importar/Exportar do Azure

Se tiver uma grande quantidade de dados a importar para a sua conta de armazenamento, considere o serviço importar/exportar do Azure. O serviço importar/exportar é utilizado para importar com segurança grandes quantidades de dados para o armazenamento de Blobs do Azure e ficheiros do Azure ao envio de unidades de disco num Datacenter do Azure. 

O serviço importar/exportar também pode ser utilizado para transferir dados do armazenamento de Blobs do Azure para unidades de disco e disponibilize aos seus sites no local. Dados a partir de uma ou mais unidades de disco podem ser importados ou ficheiros do Azure ou de armazenamento de Blobs do Azure. Para obter mais informações, consulte [o que é o serviço importar/exportar do Azure?](https://docs.microsoft.com/azure/storage/common/storage-import-export-service).

## <a name="storage-account-billing"></a>Faturação da conta do Storage

[!INCLUDE [storage-account-billing-include](../../../includes/storage-account-billing-include.md)]

## <a name="next-steps"></a>Passos Seguintes

* Para saber como criar uma conta de armazenamento do Azure, veja [criar uma conta de armazenamento](storage-quickstart-create-account.md).
* Para gerir ou eliminar uma conta de armazenamento existente, consulte [gerir as contas de armazenamento](storage-account-manage.md).
