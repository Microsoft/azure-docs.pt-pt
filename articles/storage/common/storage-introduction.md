---
title: Introdução ao Armazenamento do Azure - Armazenamento da cloud no Azure | Microsoft Docs
description: A plataforma de armazenamento Azure core é a solução de armazenamento em nuvem da Microsoft. O Armazenamento do Azure oferece armazenamento de objetos de dados altamente disponível, seguro, durável, extremamente dimensionável e redundante.
services: storage
author: tamram
ms.service: storage
ms.topic: conceptual
ms.date: 04/08/2020
ms.author: tamram
ms.subservice: common
ms.openlocfilehash: cd45a393081d52985d99fac7ea5dccc5bd2a2712
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/15/2020
ms.locfileid: "92079229"
---
# <a name="introduction-to-the-core-azure-storage-services"></a>Introdução aos principais serviços de armazenamento Azure

A plataforma Azure Storage é a solução de armazenamento em nuvem da Microsoft para cenários modernos de armazenamento de dados. Os serviços de armazenamento de núcleo oferecem uma loja de objetos massivamente escalável para objetos de dados, armazenamento de discos para máquinas virtuais Azure (VMs), um serviço de sistema de ficheiros para a nuvem, uma loja de mensagens para mensagens confiáveis e uma loja NoSQL. Os serviços são:

- **Durável e de elevada disponibilidade.** A redundância garante que os dados estão seguros em caso de falhas de hardware transitórias. Também pode optar por replicar dados em centros de dados ou regiões geográficas para obter proteção adicional contra catástrofes locais ou desastres naturais. Os dados replicados desta forma permanecem altamente disponíveis no caso de uma falha inesperada.
- **Está seguro.** Todos os dados escritos numa conta de armazenamento Azure são encriptados pelo serviço. O Armazenamento do Azure oferece-lhe controlo detalhado sobre quem tem acesso aos seus dados.
- **Escalável.** O Armazenamento do Azure foi criado para ser extremamente dimensionável para satisfazer as necessidades de armazenamento e desempenho de dados das aplicações atuais.
- **Gerido.** A Azure lida com manutenção de hardware, atualizações e problemas críticos para si.
- **Acessível.** Os dados no Armazenamento do Azure são acessíveis a partir de qualquer local no mundo através de HTTP ou HTTPS. A Microsoft fornece bibliotecas de clientes para armazenamento Azure em uma variedade de idiomas, incluindo .NET, Java, Node.js, Python, PHP, Ruby, Go, e outros, bem como uma API de REST madura. O Armazenamento do Azure suporta scripting no Azure PowerShell ou na CLI do Azure. E o portal do Azure e o Explorador de Armazenamento do Azure oferecem soluções visuais simples para trabalhar com os seus dados.  

## <a name="core-storage-services"></a>Serviços de armazenamento de núcleo

A plataforma Azure Storage inclui os seguintes serviços de dados:

- [Blobs do Azure](../blobs/storage-blobs-introduction.md): um arquivo de objetos extremamente dimensionável para texto e dados binários. Também inclui suporte para análise de big data através da Data Lake Storage Gen2.
- [Ficheiros Azure](../files/storage-files-introduction.md): Partilhas de ficheiros geridas para implementações em nuvem ou no local.
- [Filas do Azure](../queues/storage-queues-introduction.md): arquivo de mensagens para mensagens fiáveis entre componentes da aplicação.
- [Tabelas do Azure](../tables/table-storage-overview.md): um arquivo do NoSQL para armazenamento sem esquemas de dados estruturados.
- [Discos Azure](../../virtual-machines/managed-disks-overview.md): Volumes de armazenamento ao nível do bloco para VMs Azure.

Cada serviço é acedido através de uma conta de armazenamento. Para começar a utilizar, veja [Criar uma conta de armazenamento](storage-account-create.md).

## <a name="example-scenarios"></a>Cenários de exemplo

A tabela seguinte compara Ficheiros, Blobs, Discos, Filas e Tabelas, e mostra cenários de exemplo para cada um.

| Funcionalidade | Descrição | Quando utilizar |
|--------------|-------------|-------------|
| **Ficheiros do Azure** |Oferece partilhas de ficheiros em nuvem totalmente geridas que pode aceder a partir de qualquer lugar através do protocolo padrão do Bloco de Mensagens do Servidor (SMB) da indústria.<br><br>Pode montar partilhas de ficheiros Azure a partir de implementações de Windows, Linux e macOS no local. | Pretende "levantar e deslocar" uma aplicação para a nuvem que já utiliza as APIs do sistema de ficheiros nativo para partilhar dados entre ele e outras aplicações em execução no Azure.<br/><br/>Pretende substituir ou complementar servidores de ficheiros no local ou dispositivos NAS.<br><br> Pretende armazenar ferramentas de desenvolvimento e depuragem que precisam de ser acedidas a partir de muitas máquinas virtuais. |
| **Blobs do Azure** | Permite que os dados não estruturados sejam armazenados e acedidos a uma escala massiva em blobs de blocos.<br/><br/>Também suporta [a Azure Data Lake Storage Gen2](../blobs/data-lake-storage-introduction.md) para soluções de análise de big data da empresa. | Pretende que a sua aplicação suporte cenários de streaming e acesso aleatório.<br/><br/>Pretende aceder aos dados da aplicação a partir de qualquer lugar.<br/><br/>Você quer construir um lago de dados da empresa em Azure e realizar grandes análises de dados. |
| **Discos do Azure** | Permite que os dados sejam armazenados e acedidos persistentemente a partir de um disco rígido virtual anexado. | Pretende -se que "levante e desloque" aplicações que utilizem APIs do sistema de ficheiros nativos para ler e escrever dados para discos persistentes.<br/><br/>Pretende armazenar dados que não sejam necessários para serem acedidos a partir de fora da máquina virtual à qual o disco está ligado. |
| **Filas do Azure** | Permite a fila de mensagens assíncronos entre os componentes da aplicação. | Pretende dissociar os componentes da aplicação e utilizar mensagens assíncronos para comunicar entre eles.<br><br>Para obter orientações sobre quando usar o armazenamento da fila contra as filas de Service Bus, consulte [as filas de armazenamento e as filas de Service Bus - comparadas e contrastadas](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted). |
| **Tabelas do Azure** | Permitir-lhe armazenar dados NoSQL estruturados na nuvem, fornecendo uma loja chave/atributo com um design sem esquemas. | Pretende armazenar conjuntos de dados flexíveis, como dados de utilizador para aplicações web, livros de endereços, informações de dispositivos ou outros tipos de metadados que o seu serviço necessita. <br/><br/>Para obter orientações sobre quando utilizar o armazenamento de mesa contra a API da tabela AZure Cosmos DB, consulte [Desenvolver-se com Azure Cosmos DB Table API e Azure Table storage](../../cosmos-db/table-support.md). |

## <a name="blob-storage"></a>Armazenamento de blobs

O Armazenamento de blobs do Azure é a solução de armazenamento de objetos da Microsoft para a cloud. O Armazenamento de blobs está otimizado para armazenar grandes quantidades de dados não estruturados, como dados de texto ou binários.

O armazenamento de blobs é ideal para:

- Entrega de imagens ou documentos diretamente a um browser.
- Armazenamento de ficheiros para acesso distribuído.
- Transmissão de áudio e vídeo.
- Armazenamento de dados de cópia de segurança e restauro, recuperação após desastre e arquivo.
- Armazenamento de dados para análise por um serviço no local ou alojado no Azure.

Os Objetos no Armazenamento de blobs podem ser acedidos em qualquer local no mundo através de HTTP ou HTTPS. Os utilizadores ou aplicações de cliente podem aceder a blobs através de URLs, à [API REST do Armazenamento do Azure](https://docs.microsoft.com/rest/api/storageservices/blob-service-rest-api), ao [Azure PowerShell](https://docs.microsoft.com/powershell/module/azure.storage), à [CLI do Azure](https://docs.microsoft.com/cli/azure/storage) ou a uma biblioteca de cliente do Armazenamento do Azure. As bibliotecas de clientes de armazenamento estão disponíveis para muitas linguagens, incluindo [.NET](/dotnet/api/overview/azure/storage?view=azure-dotnet), [Java](https://docs.microsoft.com/java/api/overview/azure/storage), [Node.js](https://azure.github.io/azure-storage-node), [Python](https://azure-storage.readthedocs.io/), [PHP](https://azure.github.io/azure-storage-php/) e [Ruby](https://azure.github.io/azure-storage-ruby).

Para obter mais informações sobre o armazenamento blob, consulte [Introdução ao armazenamento blob](../blobs/storage-blobs-introduction.md).

## <a name="azure-files"></a>Ficheiros do Azure

[O Azure Files](../files/storage-files-introduction.md) permite-lhe configurar ações de ficheiros de rede altamente disponíveis que podem ser acedidas utilizando o protocolo padrão do Bloco de Mensagens do Servidor (SMB). Isto significa que múltiplas VMs podem partilhar os mesmos ficheiros com acesso de leitura e de escrita. Também pode ler os ficheiros com a interface REST ou as bibliotecas de cliente de armazenamento.

A única coisa que distingue os Ficheiros do Azure dos ficheiros numa partilha de ficheiros empresarial é o facto de o utilizador poder aceder aos ficheiros a partir de qualquer parte do mundo através de um URL que aponta para o ficheiro e inclui um token de assinatura de acesso partilhado (SAS). Pode gerar tokens SAS; estes permitem o acesso específico a um recurso privado durante um período de tempo específico.

As partilhas de ficheiros podem ser utilizadas para inúmeros cenários comuns:

- Muitas aplicações no local utilizam partilhas de ficheiros. Esta funcionalidade facilita a migração dessas aplicações que partilham dados no Azure. Se montar a partilha de ficheiros na mesma letra de unidade utilizada pela aplicação no local, a parte da sua aplicação que acede à partilha de ficheiros deve funcionar com alterações mínimas, se existirem.

- Os ficheiros de configuração podem ser armazenados numa partilha de ficheiros e acedidos a partir de múltiplas VMs. As ferramentas e os utilitários utilizados pelos múltiplos programadores num grupo podem ser armazenados numa partilha de ficheiros, o que garante que todos podem encontrá-los e que utilizam a mesma versão.

- Registos de recursos, métricas e despejos de colisão são apenas três exemplos de dados que podem ser escritos para uma partilha de ficheiros e processados ou analisados mais tarde.

Para obter mais informações sobre o serviço Ficheiros do Azure, veja [Introdução ao serviço Ficheiros do Azure](../files/storage-files-introduction.md).

Algumas funcionalidades de SMB não são aplicáveis à nuvem. Para obter mais informações, consulte [funcionalidades não suportadas pelo serviço Azure File](/rest/api/storageservices/features-not-supported-by-the-azure-file-service).

## <a name="queue-storage"></a>Armazenamento de filas

O serviço Fila do Azure é utilizado para armazenar e obter mensagens. As mensagens das filas podem ter até 64 KB de tamanho, ao passo que as filas podem conter milhões de mensagens. Geralmente, as filas são utilizadas para armazenar listas de mensagens que serão processadas de forma assíncrona.

Por exemplo, imaginemos que quer dar aos seus clientes a capacidade de carregar imagens e que quer igualmente criar miniaturas de cada uma delas. Pode fazer com que os clientes esperem até criar as miniaturas enquanto carregam as imagens. Uma alternativa é utilizar uma fila. Quando o cliente terminar o upload, escreva uma mensagem para a fila. Em seguida, crie um função das Função do Azure para obter a mensagem da fila e criar as miniaturas. Cada uma das partes envolvidas neste processamento pode ser dimensionada à parte, dando-lhe mais controlo para a otimizar para a sua utilização.

Para obter mais informações sobre o serviço Filas do Azure, veja [Introdução ao serviço Filas do Azure](../queues/storage-queues-introduction.md).

## <a name="table-storage"></a>Table Storage

O armazenamento de Tabelas do Azure faz agora parte do Azure Cosmos DB. Para ver a documentação do armazenamento de Tabelas do Azure, veja a [Descrição Geral do Armazenamento de Tabelas do Azure](../tables/table-storage-overview.md). Para além do serviço de armazenamento de Tabelas do Azure já existente, há uma nova oferta de API de Tabela do Azure Cosmos DB que disponibiliza tabelas otimizadas para débito, distribuição global e índices secundários automáticos. Para saber mais e experimentar a nova experiência premium, consulte [a Azure Cosmos DB Table API](https://aka.ms/premiumtables).

Para obter mais informações sobre o Armazenamento de tabelas, veja [Descrição geral do Armazenamento de tabelas do Azure](../tables/table-storage-overview.md).

## <a name="disk-storage"></a>Armazenamento em disco

Um disco gerido aZure é um disco rígido virtual (VHD). Pode pensar nisso como um disco físico num servidor no local, mas virtualizado. Os discos geridos pelo Azure são armazenados como bolhas de página, que são um objeto de armazenamento IO aleatório em Azure. Chamamos um disco gerido 'gerido' porque é uma abstração sobre bolhas de página, recipientes blob e contas de armazenamento Azure. Com discos geridos, tudo o que tens de fazer é providenciar o disco, e o Azure trata do resto.

Para obter mais informações sobre discos geridos, consulte [discos geridos Introdução ao Azure](../../virtual-machines/managed-disks-overview.md).

## <a name="types-of-storage-accounts"></a>Tipos de contas de armazenamento

O Azure Storage oferece vários tipos de contas de armazenamento. Cada tipo suporta diferentes funcionalidades e tem o seu próprio modelo de preços. Para obter mais informações sobre tipos de conta de armazenamento, consulte [a visão geral da conta de armazenamento Azure](storage-account-overview.md).

## <a name="secure-access-to-storage-accounts"></a>Acesso seguro às contas de armazenamento

Todos os pedidos ao Azure Storage devem ser autorizados. O Azure Storage suporta os seguintes métodos de autorização:

- **Integração do Azure Ative Directory (Azure AD) para dados de bolhas e filas.** O Azure Storage suporta a autenticação e autorização com a Azure AD para os serviços Blob e Queue via Azure role-based access control (Azure RBAC). É recomendada a autorização de pedidos com Azure AD para uma segurança superior e facilidade de utilização. Para obter mais informações, consulte [Autoriza o acesso a blobs e filas Azure usando o Azure Ative Directory](storage-auth-aad.md).
- **Autorização Azure AD sobre SMB para Ficheiros Azure.** O Azure Files suporta a autorização baseada na identidade sobre o SMB (Server Message Block) através dos Serviços de Domínio do Diretório Ativo Azure (Azure AD DS) ou nos locais Serviços de Domínio do Diretório Ativo (pré-visualização). Os seus VMs windows unidos ao domínio podem aceder a ações de ficheiros Azure usando credenciais Azure AD. Para obter mais informações, consulte [o suporte de autenticação baseado em identidade do Azure Files para aceder](../files/storage-files-active-directory-overview.md) e planear uma [implementação de Ficheiros Azure](../files/storage-files-planning.md#identity).
- **Autorização com Chave Partilhada.** A Azure Storage Blob, Ficheiros, Fila e Serviços de Tabela apoiam a autorização com Chave Partilhada. Um cliente que utilize a autorização Da Chave Partilhada passa um cabeçalho com cada pedido assinado usando a chave de acesso à conta de armazenamento. Para mais informações, consulte [Autorizar com chave partilhada.](https://docs.microsoft.com/rest/api/storageservices/authorize-with-shared-key)
- **Autorização utilizando assinaturas de acesso partilhado (SAS).** Uma assinatura de acesso partilhado (SAS) é uma cadeia que contém um símbolo de segurança que pode ser anexado ao URI para um recurso de armazenamento. O símbolo de segurança encapsula constrangimentos como permissões e o intervalo de acesso. Para obter mais informações, consulte [utilizar assinaturas de acesso partilhado (SAS)](storage-sas-overview.md).
- **Acesso anónimo a contentores e bolhas.** Um recipiente e as suas bolhas podem estar disponíveis ao público. Quando se especifica que um recipiente ou bolha é público, qualquer pessoa pode lê-lo anonimamente; não é necessária autenticação. Para obter mais informações, veja [Manage anonymous read access to containers and blobs](../blobs/storage-manage-access-to-resources.md) (Gerir o acesso de leitura anónima a contentores e blobs).

## <a name="encryption"></a>Encriptação

Existem dois tipos básicos de encriptação disponíveis para os serviços de armazenamento principal. Para obter mais informações sobre segurança e encriptação, veja o [Guia de segurança do Armazenamento do Azure](../blobs/security-recommendations.md).

### <a name="encryption-at-rest"></a>Encriptação de dados inativos

A encriptação Azure Storage protege e salvaguarda os seus dados para cumprir os seus compromissos de segurança organizacional e conformidade. O Azure Storage encripta automaticamente todos os dados antes de persistir na conta de armazenamento e desencripta-os antes da recuperação. A encriptação, desencriptação e os principais processos de gestão são transparentes para os utilizadores. Os clientes também podem optar por gerir as suas próprias chaves usando o Azure Key Vault. Para obter mais informações, consulte [a encriptação do Azure Storage para obter dados em repouso](storage-service-encryption.md).

### <a name="client-side-encryption"></a>Encriptação do lado do cliente

As bibliotecas de clientes do Azure Storage fornecem métodos para encriptar dados da biblioteca do cliente antes de enviá-los através do fio e desencriptar a resposta. Os dados encriptados através da encriptação do lado do cliente também são encriptados em repouso pelo Azure Storage. Para obter mais informações sobre encriptação do lado do cliente, consulte [a encriptação do lado do cliente com .NET para armazenamento Azure](storage-client-side-encryption.md).

## <a name="redundancy"></a>Redundância

Para garantir que os seus dados são duráveis, o Azure Storage armazena várias cópias dos seus dados. Ao configurar a sua conta de armazenamento, selecione uma opção de redundância. Para obter mais informações, veja [Redundância do Armazenamento do Microsoft Azure](/azure/storage/common/storage-redundancy?toc=/azure/storage/blobs/toc.json).

## <a name="transfer-data-to-and-from-azure-storage"></a>Transferir dados de e para o Azure Storage

Tem várias opções para mover dados para dentro ou para fora do Azure Storage. Qual a opção que escolher depende do tamanho do seu conjunto de dados e da largura de banda da sua rede. Para mais informações, consulte [Escolha uma solução Azure para transferência de dados.](storage-choose-data-transfer-solution.md)

## <a name="pricing"></a>Preços

Ao tomar decisões sobre como os seus dados são armazenados e acedidos, deve também considerar os custos envolvidos. Para mais informações, consulte [os preços de armazenamento Azure.](https://azure.microsoft.com/pricing/details/storage/)

## <a name="storage-apis-libraries-and-tools"></a>APIs de armazenamento, bibliotecas e ferramentas

Pode aceder a recursos numa conta de armazenamento por qualquer idioma que possa fazer pedidos HTTP/HTTPS. Além disso, os principais serviços de Armazenamento Azure oferecem bibliotecas de programação para várias línguas populares. Estas bibliotecas simplificam muitos aspetos do trabalho com o Storage do Azure ao processar detalhes como a invocação síncrona e assíncrona, a criação de batches de operações, a gestão de exceções, as tentativas automáticas, o comportamento operacional, etc. Atualmente, as bibliotecas estão disponíveis para os seguintes idiomas e plataformas, com outros no pipeline:

### <a name="azure-storage-data-api-and-library-references"></a>API de dados do Armazenamento do Azure e referências da biblioteca

- [Azure Storage REST API](https://docs.microsoft.com/rest/api/storageservices/) (API REST do Armazenamento do Azure)
- [Biblioteca de clientes Azure Storage para .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage)
- [Biblioteca de clientes Azure Storage para Java/Android](https://docs.microsoft.com/java/api/overview/azure/storage)
- [Biblioteca de clientes Azure Storage para Node.js](https://docs.microsoft.com/javascript/api/overview/azure/storage-overview)
- [Biblioteca de clientes Azure Storage para Python](https://github.com/Azure/azure-storage-python)
- [Biblioteca de clientes Azure Storage para PHP](https://github.com/Azure/azure-storage-php)
- [Biblioteca de clientes Azure Storage para Ruby](https://github.com/Azure/azure-storage-ruby)
- [Biblioteca de clientes Azure Storage para C++](https://github.com/Azure/azure-storage-cpp)

### <a name="azure-storage-management-api-and-library-references"></a>API de gestão do Armazenamento do Azure e referências da biblioteca

- [API REST do Fornecedor de Recursos de Armazenamento](https://docs.microsoft.com/rest/api/storagerp/)
- [Biblioteca de Clientes do Fornecedor de Recursos do Storage para o .NET](https://docs.microsoft.com/dotnet/api/overview/azure/storage/management)
- [API REST da Gestão de Serviços do Storage (Clássica)](https://msdn.microsoft.com/library/azure/ee460790.aspx)

### <a name="azure-storage-data-movement-api-and-library-references"></a>API de movimentação do Armazenamento do Azure e referências da biblioteca

- [API REST do Serviço de Importação/Exportação do Storage](https://docs.microsoft.com/rest/api/storageimportexport/)
- [Biblioteca de Clientes do Movimento de Dados do Storage para o .NET](/dotnet/api/microsoft.azure.storage.datamovement)

### <a name="tools-and-utilities"></a>Ferramentas e utilitários

- [Cmdlets do Azure PowerShell para Armazenamento](https://docs.microsoft.com/powershell/module/az.storage)
- [Cmdlets da CLI do Azure para Armazenamento](https://docs.microsoft.com/cli/azure/storage)
- [Utilitário da Linha de Comandos do AzCopy](https://aka.ms/downloadazcopy)
- O [Explorador de Armazenamento do Azure](https://azure.microsoft.com/features/storage-explorer/) é uma aplicação autónoma e gratuita da Microsoft, que lhe permite trabalhar visualmente com dados do Armazenamento do Azure no Windows, macOS e Linux.
- [Modelos de gestor de recursos Azure para armazenamento Azure](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Storage)

## <a name="next-steps"></a>Passos seguintes

Para se levantar e funcionar com os principais serviços de Armazenamento Azure, consulte [Criar uma conta de armazenamento](storage-account-create.md).
