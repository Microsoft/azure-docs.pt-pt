---
title: QnA Maker encriptação de dados em repouso
titleSuffix: Azure Cognitive Services
description: QnA Maker encriptação de dados em repouso .
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 99c21ece202f8d9867045d506574dd7718bd455e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83653658"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>QnA Maker encriptação de dados em repouso

O QnA Maker encripta automaticamente os seus dados quando é persistido na nuvem, ajudando a cumprir os seus objetivos de segurança organizacional e conformidade.

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Por padrão, a sua subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também uma opção para gerir a sua subscrição com as suas próprias chaves. As chaves geridas pelo cliente (CMK) oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.

A QnA Maker utiliza suporte CMK da pesquisa Azure. Você precisa criar [CMK em Pesquisa Azure usando cofre de chave Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Esta instância Azure deve ser associada ao serviço QnA Maker para torná-lo ativado pela CMK.

> [!IMPORTANT]
> O seu recurso de serviço Azure Search deve ter sido criado depois de janeiro de 2019 e não pode estar no nível gratuito (partilhado). Não existe suporte para configurar as chaves geridas pelo cliente no portal Azure.

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

O serviço QnA Maker utiliza CMK do serviço De Pesquisa Azure. Siga estes passos para permitir a CMKs:

1. Crie uma nova instância de Pesquisa Azure e ative os pré-requisitos mencionados nos [pré-requisitos-chave geridos pelo cliente para a Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Ver definições de encriptação](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando cria um recurso QnA Maker, está automaticamente associado a uma instância de Pesquisa Azure. Isto não pode ser utilizado com CMK. Para utilizar a CMK, terá de associar a sua recém-criada instância de Pesquisa Azure que foi criada no passo 1. Especificamente, terá de atualizar o `AzureSearchAdminKey` e no seu recurso `AzureSearchName` QnA Maker.

   ![Ver definições de encriptação](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Em seguida, criar uma nova definição de aplicação:
   * **Nome**: Definir isto para`CustomerManagedEncryptionKeyUrl`
   * **Valor**: Este é o valor que obteve no Passo 1 ao criar a sua instância de Pesquisa Azure.

   ![Ver definições de encriptação](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Quando terminar, reinicie o tempo de execução. Agora o seu serviço QnA Maker está ativado pela CMK.

## <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão disponíveis em todas as regiões de Pesquisa Azure.

## <a name="encryption-of-data-in-transit"></a>Encriptação de dados em trânsito

O portal QnA Maker funciona no navegador do utilizador. Cada ação desencadeia uma chamada direta para a respetiva API do serviço cognitivo. Assim, a QnA Maker está em conformidade com os dados em trânsito.
No entanto, como o serviço de portal QnA Maker está hospedado no Oeste-EUA, ainda não é ideal para clientes não americanos. 

## <a name="next-steps"></a>Passos seguintes

* [Encriptação em Pesquisa Azure usando CMKs no cofre de chaves Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Encriptação de dados inativa](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
