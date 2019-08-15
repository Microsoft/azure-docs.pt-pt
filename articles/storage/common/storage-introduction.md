---
title: Introdução ao Armazenamento do Azure - Armazenamento da cloud no Azure | Microsoft Docs
description: O Armazenamento do Azure é a solução de armazenamento da cloud da Microsoft. O Armazenamento do Azure oferece armazenamento de objetos de dados altamente disponível, seguro, durável, extremamente dimensionável e redundante.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 04c49b78d948884a9bcccfa949518d25a3dac9c1
ms.sourcegitcommit: 5b76581fa8b5eaebcb06d7604a40672e7b557348
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68986576"
---
# <a name="introduction-to-azure-storage"></a>Introdução ao Armazenamento do Azure

O Armazenamento do Azure é a solução de armazenamento da cloud da Microsoft para cenários de armazenamento de dados modernos. O Armazenamento do Azure oferece um arquivo de objetos extremamente dimensionável para objetos de dados, um serviço de sistema de ficheiros para a cloud, um arquivo de mensagens para mensagens fiáveis e um arquivo de NoSQL. O Armazenamento do Azure é:

- **Durável e de elevada disponibilidade.** A redundância garante que os dados estão seguros em caso de falhas de hardware transitórias. Também pode optar por replicar dados em centros de dados ou regiões geográficas para obter proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada.
- **Proteger.** Todos os dados escritos no Armazenamento do Azure são encriptado pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.
- **Escalável.** O Armazenamento do Azure foi criado para ser extremamente dimensionável para satisfazer as necessidades de armazenamento e desempenho de dados das aplicações atuais. 
- **Gerido.** O Microsoft Azure lida com manutenção de hardware, atualizações e problemas críticos para você.
- **Acessível.** Os dados no Armazenamento do Azure são acessíveis a partir de qualquer local no mundo através de HTTP ou HTTPS. A Microsoft fornece bibliotecas de cliente para o armazenamento do Azure em uma variedade de linguagens, incluindo .NET, Java, Node. js, Python, PHP, Ruby, Go e outros, bem como uma API REST madura. O Armazenamento do Azure suporta scripting no Azure PowerShell ou na CLI do Azure. E o portal do Azure e o Explorador de Armazenamento do Azure oferecem soluções visuais simples para trabalhar com os seus dados.  

## <a name="azure-storage-services"></a>Serviços do Armazenamento do Azure

O Armazenamento do Azure inclui estes serviços de dados:

- [BLOBs do Azure](../blobs/storage-blobs-introduction.md): Um armazenamento de objetos altamente escalonável para texto e dados binários.
- [Arquivos do Azure](../files/storage-files-introduction.md): Compartilhamentos de arquivos gerenciados para implantações locais ou na nuvem.
- [Filas do Azure](../queues/storage-queues-introduction.md): Um repositório de mensagens para mensagens confiáveis entre componentes do aplicativo. 
- [Tabelas do Azure](../tables/table-storage-overview.md): Um repositório NoSQL para armazenamento sem esquema de dados estruturados.

Cada serviço é acedido através de uma conta de armazenamento. Para começar a utilizar, veja [Criar uma conta de armazenamento](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Armazenamento de blobs

O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar grandes quantidades de dados não estruturados, como dados de texto ou binários. 

O armazenamento de blobs é ideal para:

- Entrega de imagens ou documentos diretamente a um browser.
- Armazenamento de ficheiros para acesso distribuído.
- Transmissão de áudio e vídeo.
- Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
- Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os Objetos no Armazenamento de blobs podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS. Os utilizadores ou aplicações de cliente podem aceder a blobs através de URLs, à [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), ao [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), à [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou a uma biblioteca de cliente do Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para muitas linguagens, incluindo [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Para obter mais informações sobre o armazenamento de BLOBs, consulte [introdução ao armazenamento de BLOBs](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Ficheiros do Azure

O serviço [Ficheiros do Azure](../files/storage-files-introduction.md) permite configurar partilhas de ficheiros de rede de elevada disponibilidade que podem ser acedidas através do protocolo SMB (Server Message Block) padrão. Isto significa que múltiplas VMs podem partilhar os mesmos ficheiros com acesso de leitura e de escrita. Também pode ler os ficheiros através da interface REST ou das bibliotecas de cliente de armazenamento.

A única coisa que distingue os Ficheiros do Azure dos ficheiros numa partilha de ficheiros empresarial é o facto de o utilizador poder aceder aos ficheiros a partir de qualquer parte do mundo através de um URL que aponta para o ficheiro e inclui um token de assinatura de acesso partilhado (SAS). Pode gerar tokens SAS; estes permitem o acesso específico a um recurso privado durante um período de tempo específico.

As partilhas de ficheiros podem ser utilizadas para inúmeros cenários comuns:

- Muitas aplicações no local utilizam partilhas de ficheiros. Esta funcionalidade facilita a migração dessas aplicações que partilham dados no Azure. Se montar a partilha de ficheiros na mesma letra de unidade utilizada pela aplicação no local, a parte da sua aplicação que acede à partilha de ficheiros deve funcionar com alterações mínimas, se existirem.

- Os ficheiros de configuração podem ser armazenados numa partilha de ficheiros e acedidos a partir de múltiplas VMs. As ferramentas e os utilitários utilizados pelos múltiplos programadores num grupo podem ser armazenados numa partilha de ficheiros, o que garante que todos podem encontrá-los e que utilizam a mesma versão.

- Os registos de diagnóstico, métricas e informações de falha são apenas três exemplos de dados que podem ser escritos numa partilha de ficheiros e processados ou analisados mais tarde.

Neste momento, a autenticação baseada no Active Directory e as listas de controlo de acesso (ACLs) não são suportadas, mas vão ser futuramente. As credenciais da conta de armazenamento são utilizadas para fornecer à autenticação o acesso à partilha de ficheiros. Isto significa que qualquer pessoa com a partilha montada terá total acesso de leitura/escrita à partilha.

Para obter mais informações sobre o serviço Ficheiros do Azure, veja [Introdução ao serviço Ficheiros do Azure](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Armazenamento de filas

O serviço Filas do Azure é utilizado para armazenar e obter mensagens. As mensagens das filas podem ter até 64 KB de tamanho, ao passo que as filas podem conter milhões de mensagens. Geralmente, as filas são utilizadas para armazenar listas de mensagens que vão ser processadas de forma assíncrona.

Por exemplo, imaginemos que quer dar aos seus clientes a capacidade de carregar imagens e que quer igualmente criar miniaturas de cada uma delas. Pode fazer com que os clientes esperem até criar as miniaturas enquanto carregam as imagens. Uma alternativa é utilizar uma fila. Quando o cliente concluir o carregamento, grave uma mensagem na fila. Em seguida, crie um função das Função do Azure para obter a mensagem da fila e criar as miniaturas. Cada uma das partes envolvidas neste processamento pode ser dimensionada à parte, dando-lhe mais controlo para a otimizar para a sua utilização.

Para obter mais informações sobre o serviço Filas do Azure, veja [Introdução ao serviço Filas do Azure](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

O armazenamento de Tabelas do Azure faz agora parte do Azure Cosmos DB. Para ver a documentação do armazenamento de Tabelas do Azure, veja a [Descrição Geral do Armazenamento de Tabelas do Azure](../tables/table-storage-overview.md). Para além do serviço de armazenamento de Tabelas do Azure já existente, há uma nova oferta de API de Tabela do Azure Cosmos DB que disponibiliza tabelas otimizadas para débito, distribuição global e índices secundários automáticos. Para saber mais e experimentar a nova experiência premium, veja [API de Tabelas do Azure Cosmos DB](https://aka.ms/premiumtables).

Para obter mais informações sobre o Armazenamento de tabelas, veja [Descrição geral do Armazenamento de tabelas do Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Armazenamento em disco

Um disco gerenciado do Azure é um VHD (disco rígido virtual). Você pode considerar isso como um disco físico em um servidor local, mas virtualizado. Os Azure Managed disks são armazenados como BLOBs de páginas, que são um objeto de armazenamento de e/s aleatório no Azure. Chamamos um disco gerenciado "Managed" porque ele é uma abstração em blobs de página, contêineres de BLOB e contas de armazenamento do Azure. Com os discos gerenciados, tudo o que você precisa fazer é provisionar o disco e o Azure cuida do restante.

Para obter mais informações sobre discos gerenciados, consulte [introdução aos Azure Managed disks](../../virtual-machines/windows/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Para obter mais informações sobre tipos de conta de armazenamento, consulte [visão geral da conta de armazenamento do Azure](storage-account-overview.md).

## <a name="securing-access-to-storage-accounts"></a>Protegendo o acesso a contas de armazenamento

Cada solicitação para o armazenamento do Azure deve ser autorizada. O armazenamento do Azure dá suporte aos seguintes métodos de autorização:

- **Integração do Azure Active Directory (Azure AD) para dados de BLOB e fila.** O armazenamento do Azure dá suporte à autenticação e autorização com o Azure AD para os serviços BLOB e fila por meio do controle de acesso baseado em função (RBAC). A autorização de solicitações com o Azure AD é recomendada para segurança superior e facilidade de uso. Para obter mais informações, consulte autorizar o [acesso a BLOBs e filas do Azure usando o Azure Active Directory](storage-auth-aad.md).
- **Autorização do Azure AD sobre SMB para arquivos do Azure (versão prévia).** Os arquivos do Azure oferecem suporte à autorização baseada em identidade sobre SMB (protocolo de mensagem de servidor) por meio de Azure Active Directory Domain Services. Suas VMs (máquinas virtuais) do Windows ingressadas no domínio podem acessar compartilhamentos de arquivos do Azure usando as credenciais do Azure AD. Para obter mais informações, consulte [visão geral da autorização de Azure Active Directory sobre SMB para arquivos do Azure (versão prévia)](../files/storage-files-active-directory-overview.md).
- **Autorização com chave compartilhada.** Os serviços Azure Storage Blob, fila e tabela e arquivos do Azure dão suporte à autorização com chave compartilhada. um cliente que usa a autorização de chave compartilhada passa um cabeçalho com cada solicitação que é assinada usando a chave de acesso da conta de armazenamento. Para obter mais informações, consulte [autorizar com chave compartilhada](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autorização usando SAS (assinaturas de acesso compartilhado).** Uma SAS (assinatura de acesso compartilhado) é uma cadeia de caracteres que contém um token de segurança que pode ser anexado ao URI para um recurso de armazenamento. O token de segurança encapsula restrições como permissões e o intervalo de acesso. Para obter mais informações, consulte [usando SAS (assinaturas de acesso compartilhado)](storage-sas-overview.md).
- **Acesso anônimo a contêineres e blobs.** Um contêiner e seus BLOBs podem estar publicamente disponíveis. Quando você especifica que um contêiner ou BLOB é público, qualquer pessoa pode lê-lo anonimamente; nenhuma autenticação é necessária. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

## <a name="encryption"></a>Encriptação

Estão disponíveis dois tipos básicos de encriptação para os serviços de Armazenamento. Para obter mais informações sobre segurança e encriptação, veja o [Guia de segurança do Armazenamento do Azure](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Encriptação inativa

A criptografia de armazenamento do Azure protege e protege seus dados para atender aos compromissos de segurança e conformidade da organização. O armazenamento do Azure criptografa automaticamente todos os dados antes de persistir para a conta de armazenamento e descriptografá-los antes da recuperação. Os processos de criptografia, descriptografia e gerenciamento de chaves são totalmente transparentes para os usuários. Os clientes também podem optar por gerenciar suas próprias chaves usando Azure Key Vault. Para obter mais informações, consulte [criptografia de armazenamento do Azure para dados em repouso](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

As bibliotecas de cliente de armazenamento do Azure fornecem métodos para criptografar dados da biblioteca de cliente antes de enviá-los pela rede e descriptografar a resposta. Os dados criptografados por meio da criptografia do lado do cliente também são criptografados em repouso pelo armazenamento do Azure. Para obter mais informações sobre criptografia do lado do cliente, consulte [criptografia do lado do cliente com .net para o armazenamento do Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundância

Para garantir que os seus dados são duráveis, o Armazenamento do Azure replica várias cópias dos mesmos. Ao configurar sua conta de armazenamento, você seleciona uma opção de redundância.

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Para obter mais informações sobre a recuperação de desastre, consulte [recuperação de desastre e failover de conta de armazenamento (versão prévia) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferir dados de e para o Armazenamento do Azure

Você tem várias opções para mover dados para dentro ou fora do armazenamento do Azure. A opção que você escolher dependerá do tamanho do seu conjunto de seus conjuntos de largura de banda e da sua rede. Para obter mais informações, consulte [escolher uma solução do Azure para transferência de dados](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Preços

Para obter informações detalhadas sobre os preços do Armazenamento do Azure, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>APIs de armazenamento, bibliotecas e ferramentas

É possível aceder aos recursos do Storage do Azure por qualquer idioma que consiga efetuar pedidos de HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam muitos aspetos do trabalho com o Storage do Azure ao processar detalhes como a invocação síncrona e assíncrona, a criação de batches de operações, a gestão de exceções, as tentativas automáticas, o comportamento operacional, etc. Atualmente, as bibliotecas estão disponíveis para os seguintes idiomas e plataformas, com outros no pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>API de dados do Armazenamento do Azure e referências da biblioteca

- [API REST do armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/)
- [Biblioteca de cliente de armazenamento do Azure para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Biblioteca de cliente de armazenamento do Azure para Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Biblioteca de cliente de armazenamento do Azure para node. js](https://docs.microsoft.com/javascript/api/azure-storage)
- [Biblioteca de cliente de armazenamento do Azure para Python](https://github.com/Azure/azure-storage-python)
- [Biblioteca de cliente de armazenamento do Azure para PHP](https://github.com/Azure/azure-storage-php)
- [Biblioteca de cliente de armazenamento do Azure para Ruby](https://github.com/Azure/azure-storage-ruby)
- [Biblioteca de cliente de armazenamento do Azure paraC++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>API de gestão do Armazenamento do Azure e referências da biblioteca

- [API REST do Fornecedor de Recursos de Armazenamento](https://docs.microsoft.com/rest/api/storagerp/)
- [Biblioteca de Clientes do Fornecedor de Recursos de Armazenamento para o .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [API REST da Gestão de Serviços de Armazenamento (Clássica)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>API de movimentação do Armazenamento do Azure e referências da biblioteca

- [API REST do Serviço de Importação/Exportação de Armazenamento](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Biblioteca de Clientes do Movimento de Dados de Armazenamento para o .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários

- [Cmdlets do Azure PowerShell para Armazenamento](https://docs.microsoft.com/powershell/module/az.storage)
- [Cmdlets da CLI do Azure para Armazenamento](https://docs.microsoft.com/cli/azure/storage)
- [Utilitário da Linha de Comandos do AzCopy](https://aka.ms/downloadazcopy)
- O [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
- [Ferramentas de Cliente do Armazenamento do Azure](../storage-explorers.md)
- [Ferramentas de Programação do Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Passos seguintes

Para começar a trabalhar rapidamente com o Armazenamento do Azure, veja [Criar uma conta de armazenamento](storage-quickstart-create-account.md).
