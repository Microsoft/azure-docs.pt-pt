---
title: Terminologia do Azure Data Share
description: Conheça os termos comuns utilizados para descrever os recursos utilizados na Partilha de Dados do Azure (fornecedor de dados, consumidor de dados, partilha de dados, subscrição de partilha, instantâneo, convite, destinatário.)
ms.service: data-share
author: joannapea
ms.author: joanpo
ms.topic: conceptual
ms.date: 07/10/2019
ms.openlocfilehash: 33532380d8f98df44029eeea998130d1da5fdafd
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "73468551"
---
# <a name="azure-data-share-concepts"></a>Conceitos de partilha de dados azure 

A Azure Data Share introduz algumas novas terminologias relacionadas com a partilha de dados. Este artigo explica alguns termos frequentemente utilizados que pode ver usados em todo o serviço. 

## <a name="data-provider"></a>Fornecedor de dados

Um fornecedor de Dados é a organização que está a partilhar dados com os seus consumidores. Normalmente, o fornecedor de dados pode ser proprietário ou curador dos dados. Os fornecedores de dados querem partilhar dados de vários tipos. Alguns exemplos de dados que um fornecedor de dados pode querer partilhar incluem dados brutos, tais como dados de ponto de venda ou séries de tempo. Um fornecedor de dados também pode querer partilhar dados pré-processados e curados que já contenham análises e insights. 

## <a name="data-consumer"></a>Consumidor de dados 

Um consumidor de Dados é a organização que está a receber dados de um fornecedor de dados. O consumidor de dados pode querer juntar os dados partilhados com os seus próprios dados para obter insights. Em alguns casos, o consumidor de dados pode estar a receber dados que já foram tratados. 

## <a name="data-share"></a>Data Share

Uma partilha de dados é um grupo de conjuntos de dados que são partilhados como uma única entidade. Os conjuntos de dados podem ser de várias fontes de dados do Azure que são suportadas pela Azure Data Share. Atualmente, a Azure Data Share suporta o Armazenamento Azure Blob e a Azure Data Lake Store. 

## <a name="share-subscription"></a>Subscrição de partilha 

Uma Subscrição de Partilha é criada quando um consumidor de dados aceita um convite de partilha de dados de um fornecedor de dados. Os fornecedores de dados podem ver subscrições de ações ativas navegando para **Ações Enviadas** na sua conta De dados Azure e selecionando **Subscrições**de Partilha de Ações .

Um consumidor de dados pode verificar se tem uma subscrição de ações ativas navegando para **As Ações Recebidas** e visualizando o estado das suas ações recebidas. 

## <a name="snapshot"></a>Instantâneo

Um instantâneo pode ser criado por um consumidor de dados quando aceita um convite de partilha de dados. Quando aceitam um convite, podem desencadear uma imagem completa dos dados partilhados com eles. O instantâneo é uma cópia dos dados no momento em que o consumidor de dados gerou o instantâneo. 

Existem dois tipos de instantâneos - completos e incrementais. Um instantâneo completo contém todos os dados dentro da partilha de dados. Um instantâneo incremental contém todos os dados que foram atualizados/adicionados desde que o último instantâneo foi desencadeado. 

## <a name="snapshot-settings-in-azure-data-share"></a>Configurações instantâneas em Partilha de Dados Azure
 
Um fornecedor de dados pode ativar uma definição instantânea para uma partilha de dados. Esta definição permite que os consumidores de dados recebam atualizações incrementais à medida que ocorrem. Esta definição deve ser ativada se o fornecedor de dados quiser que os seus consumidores de dados recebam atualizações aos dados partilhados. 

Se um fornecedor de dados permitir esta definição, pode ser selecionado um intervalo de recorrência. O intervalo de recorrência pode ser de hora em hora ou diariamente. 

Um consumidor de dados tem a opção de optar por este calendário instantâneo para receber atualizações incrementais, o que inclui quaisquer dados que tenham mudado desde que geraram um novo instantâneo. 

## <a name="invitation"></a>Convite

Um fornecedor de dados pode convidar vários destinatários para a sua quota de dados. Podem fazê-lo adicionando destinatários à partilha de dados. Os convites também podem ser adicionados após a criação de uma partilha de dados. 

Um fornecedor de dados pode apagar um convite depois de ter sido enviado se não tiver sido aceite. Se o fornecedor de dados apagar um convite e ainda não tiver sido aceite, o consumidor de dados não poderá aceitá-lo. 

Os convites podem ser ressentidos até cinco vezes por dia. 

## <a name="recipient"></a>Destinatário

Um destinatário é alguém que recebe um convite para uma partilha de dados. Normalmente, um fornecedor de dados adicionará destinatários à partilha de dados que criam. Uma vez que o destinatário de um convite aceita o convite, eles tornam-se um consumidor de dados.  

## <a name="next-steps"></a>Passos seguintes

Para aprender a começar a partilhar dados, continue a partilhar o seu tutorial de [dados.](share-your-data.md)
