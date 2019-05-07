---
title: Introdução ao Armazenamento do Azure - Armazenamento da cloud no Azure | Microsoft Docs
description: O Armazenamento do Azure é a solução de armazenamento da cloud da Microsoft. O Armazenamento do Azure oferece armazenamento de objetos de dados altamente disponível, seguro, durável, extremamente dimensionável e redundante.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 01/02/2019
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: 175cc03dac21280a5e3ef3ec130cb94bc800f540
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/06/2019
ms.locfileid: "65153909"
---
# <a name="introduction-to-azure-storage"></a>Introdução ao Armazenamento do Azure

O Armazenamento do Azure é a solução de armazenamento da cloud da Microsoft para cenários de armazenamento de dados modernos. O Armazenamento do Azure oferece um arquivo de objetos extremamente dimensionável para objetos de dados, um serviço de sistema de ficheiros para a cloud, um arquivo de mensagens para mensagens fiáveis e um arquivo de NoSQL. O Armazenamento do Azure é:

- **Durável e de elevada disponibilidade.** A redundância garante que os dados estão seguros em caso de falhas de hardware transitórias. Também pode optar por replicar dados em centros de dados ou regiões geográficas para obter proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada. 
- **Proteger.** Todos os dados escritos no Armazenamento do Azure são encriptado pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.
- **Escalável.** O Armazenamento do Azure foi criado para ser extremamente dimensionável para satisfazer as necessidades de armazenamento e desempenho de dados das aplicações atuais. 
- **Gerido.** Microsoft Azure trata a manutenção de hardware, atualizações e problemas críticos por si.
- **Acessível.** Os dados no Armazenamento do Azure são acessíveis a partir de qualquer local no mundo através de HTTP ou HTTPS. A Microsoft disponibiliza SDKs para o Armazenamento do Azure numa variedade de linguagens – .NET, Java, Node.js, Python, PHP, Ruby, Go e outros utilizadores – bem como uma API REST madura. O Armazenamento do Azure suporta scripting no Azure PowerShell ou na CLI do Azure. E o portal do Azure e o Explorador de Armazenamento do Azure oferecem soluções visuais simples para trabalhar com os seus dados.  

## <a name="azure-storage-services"></a>Serviços do Armazenamento do Azure

O Armazenamento do Azure inclui estes serviços de dados: 

- [Blobs do Azure](../blobs/storage-blobs-introduction.md): Um armazenamento de objetos altamente dimensionável para dados binários e de texto.
- [Os ficheiros do Azure](../files/storage-files-introduction.md): Ficheiros geridos partilham para a cloud ou implementações no local.
- [As filas do Azure](../queues/storage-queues-introduction.md): Um arquivo de mensagens para mensagens fiáveis entre componentes da aplicação. 
- [Tabelas do Azure](../tables/table-storage-overview.md): Um arquivo de NoSQL para armazenamento sem esquemas de dados estruturados.

Cada serviço é acedido através de uma conta de armazenamento. Para começar a utilizar, veja [Criar uma conta de armazenamento](storage-quickstart-create-account.md).

## <a name="blob-storage"></a>Armazenamento de blobs

O Armazenamento de Blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar grandes quantidades de dados não estruturados, como dados de texto ou binários. 

O armazenamento de blobs é ideal para:

* Entrega de imagens ou documentos diretamente a um browser.
* Armazenamento de ficheiros para acesso distribuído.
* Transmissão de áudio e vídeo.
* Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
* Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os Objetos no Armazenamento de blobs podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS. Os utilizadores ou aplicações de cliente podem aceder a blobs através de URLs, à [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), ao [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), à [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou a uma biblioteca de cliente do Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para muitas linguagens, incluindo [.NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/client), [Java](https://docs.microsoft.com/java/api/overview/azure/storage/client), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Para obter mais informações sobre o armazenamento de BLOBs, veja [introdução ao armazenamento de BLOBs](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Ficheiros do Azure
O serviço [Ficheiros do Azure](../files/storage-files-introduction.md) permite configurar partilhas de ficheiros de rede de elevada disponibilidade que podem ser acedidas através do protocolo SMB (Server Message Block) padrão. Isto significa que múltiplas VMs podem partilhar os mesmos ficheiros com acesso de leitura e de escrita. Também pode ler os ficheiros através da interface REST ou das bibliotecas de cliente de armazenamento.

A única coisa que distingue os Ficheiros do Azure dos ficheiros numa partilha de ficheiros empresarial é o facto de o utilizador poder aceder aos ficheiros a partir de qualquer parte do mundo através de um URL que aponta para o ficheiro e inclui um token de assinatura de acesso partilhado (SAS). Pode gerar tokens SAS; estes permitem o acesso específico a um recurso privado durante um período de tempo específico.

As partilhas de ficheiros podem ser utilizadas para inúmeros cenários comuns:

* Muitas aplicações no local utilizam partilhas de ficheiros. Esta funcionalidade facilita a migração dessas aplicações que partilham dados no Azure. Se montar a partilha de ficheiros na mesma letra de unidade utilizada pela aplicação no local, a parte da sua aplicação que acede à partilha de ficheiros deve funcionar com alterações mínimas, se existirem.

* Os ficheiros de configuração podem ser armazenados numa partilha de ficheiros e acedidos a partir de múltiplas VMs. As ferramentas e os utilitários utilizados pelos múltiplos programadores num grupo podem ser armazenados numa partilha de ficheiros, o que garante que todos podem encontrá-los e que utilizam a mesma versão.

* Os registos de diagnóstico, métricas e informações de falha são apenas três exemplos de dados que podem ser escritos numa partilha de ficheiros e processados ou analisados mais tarde.

Neste momento, a autenticação baseada no Active Directory e as listas de controlo de acesso (ACLs) não são suportadas, mas vão ser futuramente. As credenciais da conta de armazenamento são utilizadas para fornecer à autenticação o acesso à partilha de ficheiros. Isto significa que qualquer pessoa com a partilha montada terá total acesso de leitura/escrita à partilha.

Para obter mais informações sobre o serviço Ficheiros do Azure, veja [Introdução ao serviço Ficheiros do Azure](../files/storage-files-introduction.md).

## <a name="queue-storage"></a>Armazenamento de filas

O serviço Filas do Azure é utilizado para armazenar e obter mensagens. As mensagens das filas podem ter até 64 KB de tamanho, ao passo que as filas podem conter milhões de mensagens. Geralmente, as filas são utilizadas para armazenar listas de mensagens que vão ser processadas de forma assíncrona.

Por exemplo, imaginemos que quer dar aos seus clientes a capacidade de carregar imagens e que quer igualmente criar miniaturas de cada uma delas. Pode fazer com que os clientes esperem até criar as miniaturas enquanto carregam as imagens. Uma alternativa é utilizar uma fila. Quando o cliente concluir o carregamento, escreva uma mensagem para a fila. Em seguida, crie um função das Função do Azure para obter a mensagem da fila e criar as miniaturas. Cada uma das partes envolvidas neste processamento pode ser dimensionada à parte, dando-lhe mais controlo para a otimizar para a sua utilização.

Para obter mais informações sobre o serviço Filas do Azure, veja [Introdução ao serviço Filas do Azure](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

O armazenamento de Tabelas do Azure faz agora parte do Azure Cosmos DB. Para ver a documentação do armazenamento de Tabelas do Azure, veja a [Descrição Geral do Armazenamento de Tabelas do Azure](../tables/table-storage-overview.md). Para além do serviço de armazenamento de Tabelas do Azure já existente, há uma nova oferta de API de Tabela do Azure Cosmos DB que disponibiliza tabelas otimizadas para débito, distribuição global e índices secundários automáticos. Para saber mais e experimentar a nova experiência premium, veja [API de Tabelas do Azure Cosmos DB](https://aka.ms/premiumtables).

Para obter mais informações sobre o Armazenamento de tabelas, veja [Descrição geral do Armazenamento de tabelas do Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Armazenamento em disco

O Armazenamento do Azure também inclui capacidades de discos geridos e não geridos que as máquinas virtuais utilizam. Para obter mais informações sobre estas funcionalidades, veja a [documentação do Serviço de Computação](https://docs.microsoft.com/azure/#pivot=products&panel=Compute).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

[!INCLUDE [storage-account-types-include](../../../includes/storage-account-types-include.md)]

Para obter mais informações sobre os tipos de conta de armazenamento, consulte [descrição geral da conta de armazenamento do Azure](storage-account-overview.md). 

## <a name="securing-access-to-storage-accounts"></a>Protegendo o acesso a contas de armazenamento

Todos os pedidos ao armazenamento do Azure tem de ser autorizado. O armazenamento do Azure suporta os seguintes métodos de autorização:

- **Integração do Active Directory (Azure AD) do Azure para dados de BLOBs e filas.** O armazenamento do Azure suporta a autenticação e autorização com credenciais do Azure AD para os serviços de BLOBs e filas através do controlo de acesso baseado em funções (RBAC). Autorização de solicitações com o Azure AD é recomendada para maior segurança e facilidade de utilização. Para obter mais informações, consulte [autenticar o acesso ao Azure os blobs e filas com o Azure Active Directory](storage-auth-aad.md).
- **Azure autorização de AD através de SMB para ficheiros do Azure (pré-visualização).** Os ficheiros do Azure suporta a autorização baseada em identidade através de SMB (Server Message Block) através do Azure Active Directory Domain Services. Máquinas de virtuais de Windows (VMs) do seu associados a um domínio podem aceder a partilhas de ficheiros do Azure com credenciais do Azure AD. Para obter mais informações, consulte [autorização de descrição geral do Azure Active Directory através de SMB para ficheiros do Azure (pré-visualização)](../files/storage-files-active-directory-overview.md).
- **Autorização com a chave partilhada.** Os serviços do Azure Storage Blob, filas e tabelas e ficheiros do Azure suportam a autorização com o cliente Key.A partilhado com autorização passa um cabeçalho com cada solicitação que esteja assinada utilizando a chave de acesso da conta de armazenamento de chave partilhada. Para obter mais informações, consulte [autorizar com a chave partilhada](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key).
- **Autorização a utilizar, assinaturas de acesso (SAS) é partilhado.** Uma assinatura de acesso partilhado (SAS) é uma cadeia de caracteres que contém um token de segurança que pode ser anexado ao URI de um recurso de armazenamento. O token de segurança encapsula as restrições, como o intervalo de acesso e permissões. Para obter mais informações, consulte [usando partilhado assinaturas de acesso (SAS)](storage-dotnet-shared-access-signature-part-1.md).
- **Acesso anónimo a contentores e blobs.** Um contentor e respetivos blobs poderão estar publicamente disponíveis. Quando especifica que um contentor ou blob é público, qualquer pessoa pode lê-lo anonimamente; Não é necessária autenticação. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

## <a name="encryption"></a>Encriptação

Estão disponíveis dois tipos básicos de encriptação para os serviços de Armazenamento. Para obter mais informações sobre segurança e encriptação, veja o [Guia de segurança do Armazenamento do Azure](storage-security-guide.md).

### <a name="encryption-at-rest"></a>Encriptação inativa

A Encriptação do Serviço de Armazenamento do Azure (SEE) ajuda a proteger e salvaguardar os seus dados para cumprir as obrigações de conformidade e segurança da sua organização. Com esta funcionalidade, o Armazenamento do Azure encripta automaticamente os dados antes de continuar a armazenar e desencriptar antes da obtenção. A encriptação, a desencriptação e a gestão de chaves são completamente transparentes para os utilizadores.


O SSE encripta automaticamente dados em todos os escalões de desempenho (Standard e Premium), todos os modelos de implementação (Azure Resource Manager e Clássico) e todos os serviços de Armazenamento do Azure (Blob, Fila, Tabela e Ficheiro). O SSE não afeta o desempenho do Armazenamento do Azure.

Para obter mais informações sobre a encriptação de SSE inativa, veja [Encriptação do Serviço de Armazenamento do Azure para Dados Inativos](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

As bibliotecas de cliente de armazenamento têm métodos que pode chamar para encriptar os dados programaticamente antes de os enviar de forma automática do cliente para o Azure. São armazenados encriptados, o que significa que também estão encriptados quando estão inativos. Ao ler os dados novamente, desencripte as informações depois de as receber.

Para obter mais informações sobre a encriptação do lado do cliente, veja [Encriptação do Lado do Cliente com .NET para o Armazenamento do Microsoft Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundância

Para garantir que os seus dados são duráveis, o Armazenamento do Azure replica várias cópias dos mesmos. Quando configurar a sua conta de armazenamento, selecione uma opção de redundância. 

[!INCLUDE [storage-common-redundancy-options](../../../includes/storage-common-redundancy-options.md)]

Para obter mais informações sobre a recuperação após desastre, veja [desastre recuperação e o armazenamento de conta ativação pós-falha (pré-visualização) no armazenamento do Azure](storage-disaster-recovery-guidance.md).

## <a name="transferring-data-to-and-from-azure-storage"></a>Transferir dados de e para o Armazenamento do Azure

Tem várias opções para mover dados para dentro ou fora do armazenamento do Azure. Quais opções escolha depende do tamanho do seu conjunto de dados e a largura de banda de rede. Para obter mais informações, consulte [escolher uma solução do Azure para a transferência de dados](storage-choose-data-transfer-solution.md).

## <a name="pricing"></a>Preços

Para obter informações detalhadas sobre os preços do Armazenamento do Azure, veja a [página de preços](https://azure.microsoft.com/pricing/details/storage/blobs/).

## <a name="storage-apis-libraries-and-tools"></a>APIs de armazenamento, bibliotecas e ferramentas
É possível aceder aos recursos do Storage do Azure por qualquer idioma que consiga efetuar pedidos de HTTP/HTTPS. Além disso, o Storage do Azure oferece bibliotecas de programação para vários idiomas populares. Estas bibliotecas simplificam muitos aspetos do trabalho com o Storage do Azure ao processar detalhes como a invocação síncrona e assíncrona, a criação de batches de operações, a gestão de exceções, as tentativas automáticas, o comportamento operacional, etc. Atualmente, as bibliotecas estão disponíveis para os seguintes idiomas e plataformas, com outros no pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>API de dados do Armazenamento do Azure e referências da biblioteca
* [API REST dos Serviços de Armazenamento](https://docs.microsoft.com/rest/api/storageservices/)
* [Storage Client Library for .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage) (Biblioteca de Clientes de Armazenamento para .NET)
* [Biblioteca de Clientes de Armazenamento do Azure para Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
* [Biblioteca de Clientes de Armazenamento para Node.js](https://docs.microsoft.com/javascript/api/azure-storage)
* [Biblioteca de Clientes de Armazenamento para Python](https://github.com/Azure/azure-storage-python)
* [Biblioteca de Clientes de Armazenamento para PHP](https://github.com/Azure/azure-storage-php)
* [Biblioteca de Clientes de Armazenamento para Ruby](https://github.com/Azure/azure-storage-ruby)
* [Biblioteca de Clientes de Armazenamento para C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>API de gestão do Armazenamento do Azure e referências da biblioteca
* [API REST do Fornecedor de Recursos de Armazenamento](https://docs.microsoft.com/rest/api/storagerp/)
* [Biblioteca de Clientes do Fornecedor de Recursos de Armazenamento para o .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
* [API REST da Gestão de Serviços de Armazenamento (Clássica)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>API de movimentação do Armazenamento do Azure e referências da biblioteca
* [API REST do Serviço de Importação/Exportação de Armazenamento](https://docs.microsoft.com/rest/api/storageimportexport/)
* [Biblioteca de Clientes do Movimento de Dados de Armazenamento para o .NET](https://docs.microsoft.com/dotnet/api/microsoft.windowsazure.storage.datamovement)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários
* [Cmdlets do Azure PowerShell para Armazenamento](https://docs.microsoft.com/powershell/module/az.storage)
* [Cmdlets da CLI do Azure para Armazenamento](https://docs.microsoft.com/cli/azure/storage)
* [Utilitário da Linha de Comandos do AzCopy](https://aka.ms/downloadazcopy)
* O [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
* [Ferramentas de Cliente do Armazenamento do Azure](../storage-explorers.md)
* [Ferramentas de Programação do Azure](https://azure.microsoft.com/tools/)

## <a name="next-steps"></a>Passos seguintes

Para começar a trabalhar rapidamente com o Armazenamento do Azure, veja [Criar uma conta de armazenamento](storage-quickstart-create-account.md).
