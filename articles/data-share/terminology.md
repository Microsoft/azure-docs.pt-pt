---
title: Terminologia de pré-visualização de partilha de dados do Azure
description: Terminologia de pré-visualização de partilha de dados do Azure
author: joannapea
ms.service: data-share
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: joanpo
ms.openlocfilehash: 4e7db84666b9d3786c3fc25e3653d24d0b95f2e4
ms.sourcegitcommit: 1572b615c8f863be4986c23ea2ff7642b02bc605
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/10/2019
ms.locfileid: "67789231"
---
# <a name="azure-data-share-preview-concepts"></a>Conceitos de pré-visualização de partilha de dados do Azure 

Pré-visualização de partilha de dados do Azure introduz alguma terminologia nova relacionadas com a partilha de dados. Este artigo explica que alguns usados com freqüência termos que poderá ver utilizados em todo o serviço. 

## <a name="data-provider"></a>Fornecedor de dados

Um provedor de dados é a organização que está a partilhar dados com seus consumidores. Normalmente, o provedor de dados pode ser um proprietário ou um curador dos dados. Provedores de dados pretendem partilhar dados de diversos tipos. Alguns exemplos de dados que um provedor de dados pode partilhar incluem dados não processados, por exemplo, o ponto de vendas ou dados de séries de tempo. Um provedor de dados talvez também queira partilhar dados de pré-processados, organizados que já contém informações e análises. 

## <a name="data-consumer"></a>Consumidor de dados 

Um consumidor de dados é a organização que está a receber dados de um fornecedor de dados. O consumidor de dados pode ser que desejam associar os dados compartilhados com seus próprios dados para derivar informações. Em alguns casos, o consumidor de dados pode receber dados que já foram processados. 

## <a name="data-share"></a>Partilha de dados

Uma partilha de dados é um grupo de conjuntos de dados que são partilhados como uma única entidade. Conjuntos de dados podem ser a partir de um número de origens de dados do Azure que são suportadas por partilha de dados do Azure. Atualmente, a partilha de dados do Azure suporta o armazenamento de Blobs do Azure e Azure Data Lake Store. 

## <a name="share-subscription"></a>Subscrição de partilha 

Se um consumidor de dados aceita um convite de partilha de dados de um fornecedor de dados, é criada uma subscrição de partilhar. Provedores de dados podem ver as subscrições do Active Directory partilha ao navegar para **enviados partilhas** nos seus dados do Azure partilhar conta e selecionando **partilhar subscrições**.

Um consumidor de dados pode verificar se eles têm uma subscrição de partilha de Active Directory ao navegar para o **recebido partilhas** e visualizar o estado dos seus compartilhamentos recebidos. 

## <a name="snapshot"></a>Instantâneo

Um instantâneo pode ser criado por um consumidor de dados quando aceitam um convite de partilha de dados. Quando aceitam um convite, eles podem disparar um instantâneo dos dados partilhados com eles. O instantâneo é uma cópia dos dados no ponto no tempo que o consumidor de dados gerado o instantâneo. 

Existem dois tipos de instantâneos – completos e incrementais. Um instantâneo completo contém todos os dados dentro da partilha de dados. Um instantâneo incremental contém todos os dados que tem sido atualizado/adicionado, uma vez que o último instantâneo foi acionado. 

## <a name="snapshot-settings-in-azure-data-share"></a>Definições de instantâneos de partilha de dados do Azure
 
Um provedor de dados pode ativar uma definição do instantâneo para uma partilha de dados. Esta definição permite que os consumidores de dados receber atualizações incrementais, à medida que ocorrem. Esta definição deve ser ativada se o fornecedor de dados gostaria de ter seus consumidores de dados para receber atualizações aos dados que foi partilhados. 

Se esta definição permite a um provedor de dados, é possível selecionar um intervalo de periodicidade. O intervalo de periodicidade pode ser por hora ou diariamente. 

Um consumidor de dados tem a opção de optar ativamente por esta agenda de instantâneos para receber atualizações incrementais, que inclui todos os dados que tem sido alterada desde que gerados pela primeira vez um novo instantâneo. 

## <a name="invitation"></a>Convite

Um provedor de dados pode convidar vários destinatários à sua partilha de dados. Ela poderá fazer isso adicionando destinatários para a partilha de dados. Convites também podem ser adicionados depois de criar uma partilha de dados. 

Um provedor de dados pode eliminar um convite após ter sido enviada. Tenha em atenção que se um provedor de dados elimina um convite após ele ter sido aceite, o consumidor de dados pode ainda ter uma subscrição de partilha de Active Directory. Se o fornecedor de dados elimina um convite e ainda não foram aceites, o consumidor de dados não será capaz de aceitá-lo. 

## <a name="recipient"></a>Destinatário

Um destinatário é alguém que recebe um convite para uma partilha de dados. Normalmente, um provedor de dados irá adicionar destinatários à partilha de dados que criaram. Assim que o destinatário de um convite aceita o convite, eles tornam-se um consumidor de dados.  

## <a name="next-steps"></a>Passos seguintes

Para saber como começar a partilha de dados, avance para o [partilhar os seus dados](share-your-data.md) tutorial.

