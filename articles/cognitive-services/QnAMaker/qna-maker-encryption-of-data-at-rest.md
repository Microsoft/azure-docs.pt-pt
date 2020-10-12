---
title: Encriptação de dados do QnA Maker em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de encriptação geridas pela Microsoft e também permite gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para o QnA Maker, e como ativar e gerir a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: e744423e00377ef763824f6e39865e6b3e8ee475
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89073544"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>Encriptação de dados do QnA Maker em repouso

O QnA Maker encripta automaticamente os seus dados quando estes são persistidos na nuvem, ajudando a cumprir os seus objetivos de segurança organizacional e conformidade.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também a opção de gerir a sua subscrição com as suas próprias chaves chamadas chaves geridas pelo cliente (CMK). A CMK oferece uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados. Se a CMK estiver configurada para a sua subscrição, é fornecida uma dupla encriptação, que oferece uma segunda camada de proteção, permitindo ao mesmo tempo controlar a chave de encriptação através do seu Cofre de Chave Azure.

O QnA Maker utiliza o suporte cmk da pesquisa Azure. Você precisa criar [CMK em Azure Search usando Azure Key Vault](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys). Esta instância Azure deve ser associada ao serviço QnA Maker para torná-lo cmk habilitado.

> [!IMPORTANT]
> O seu recurso de serviço Azure Search deve ter sido criado após janeiro de 2019 e não pode estar no nível gratuito (partilhado). Não existe suporte para configurar chaves geridas pelo cliente no portal Azure.

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

O serviço QnA Maker utiliza CMK do serviço de Pesquisa Azure. Siga estes passos para permitir cmks:

1. Crie uma nova instância de Pesquisa de Azure e permita os pré-requisitos mencionados nos [pré-requisitos de chave geridos pelo cliente para a Pesquisa Cognitiva Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys#prerequisites).

   ![Ver definições de encriptação 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando cria um recurso QnA Maker, está automaticamente associado a uma instância de Pesquisa Azure. Isto não pode ser utilizado com CMK. Para utilizar a CMK, terá de associar o seu recém-criado exemplo de Azure Search que foi criado no passo 1. Especificamente, terá de atualizar o `AzureSearchAdminKey` e no seu recurso `AzureSearchName` QnA Maker.

   ![Ver definições de encriptação 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Em seguida, crie uma nova definição de aplicação:
   * **Nome**: Desemalte isto para `CustomerManagedEncryptionKeyUrl`
   * **Valor**: Este é o valor que obteve no Passo 1 ao criar a sua instância de Pesquisa Azure.

   ![Ver definições de encriptação 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Quando terminar, reinicie o tempo de execução. Agora o seu serviço QnA Maker está habilitado para CMK.

## <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão disponíveis em todas as regiões de Pesquisa Azure.

## <a name="encryption-of-data-in-transit"></a>Encriptação de dados em trânsito

O portal QnA Maker funciona no navegador do utilizador. Cada ação desencadeia uma chamada direta para a API do serviço cognitivo respetivo. Assim, a QnA Maker está em conformidade com os dados em trânsito.
No entanto, como o serviço de portal QnA Maker está hospedado no West-US, ainda não é ideal para clientes não americanos. 

## <a name="next-steps"></a>Passos seguintes

* [Encriptação em Pesquisa de Azure usando CMKs no Cofre da Chave Azure](https://docs.microsoft.com/azure/search/search-security-manage-encryption-keys)
* [Encriptação de dados inativos](https://docs.microsoft.com/azure/security/fundamentals/encryption-atrest)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)