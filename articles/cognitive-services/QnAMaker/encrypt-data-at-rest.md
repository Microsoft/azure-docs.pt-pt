---
title: Encriptação de dados do QnA Maker em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de encriptação geridas pela Microsoft e também permite gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para o QnA Maker, e como ativar e gerir a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: conceptual
ms.date: 11/09/2020
ms.author: egeaney
ms.openlocfilehash: 19dc0f3a676d5373b28e4b7055050477c426f847
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100524531"
---
# <a name="qna-maker-encryption-of-data-at-rest"></a>Encriptação de dados do QnA Maker em repouso

O QnA Maker encripta automaticamente os seus dados quando estes são persistidos na nuvem, ajudando a cumprir os seus objetivos de segurança organizacional e conformidade.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também a opção de gerir a sua subscrição com as suas próprias chaves chamadas chaves geridas pelo cliente (CMK). A CMK oferece uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados. Se a CMK estiver configurada para a sua subscrição, é fornecida uma dupla encriptação, que oferece uma segunda camada de proteção, permitindo ao mesmo tempo controlar a chave de encriptação através do seu Cofre de Chave Azure.

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

O QnA Maker utiliza o suporte cmk da pesquisa Azure. Configure [CMK em Azure Search usando Azure Key Vault](../../search/search-security-manage-encryption-keys.md). Esta instância Azure deve ser associada ao serviço QnA Maker para torná-lo cmk habilitado.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

O QnA Maker utiliza [o suporte cmk da pesquisa Azure,](../../search/search-security-manage-encryption-keys.md)e associa automaticamente a CMK fornecida para encriptar os dados armazenados no índice de pesquisa Azure.

---

> [!IMPORTANT]
> O seu recurso de serviço Azure Search deve ter sido criado após janeiro de 2019 e não pode estar no nível gratuito (partilhado). Não existe suporte para configurar chaves geridas pelo cliente no portal Azure.

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

O serviço QnA Maker utiliza CMK do serviço de Pesquisa Azure. Siga estes passos para permitir cmks:

# <a name="qna-maker-ga-stable-release"></a>[QnA Maker GA (lançamento estável)](#tab/v1)

1. Crie uma nova instância de Pesquisa de Azure e permita os pré-requisitos mencionados nos [pré-requisitos de chave geridos pelo cliente para a Pesquisa Cognitiva Azure](../../search/search-security-manage-encryption-keys.md#prerequisites).

   ![Ver definições de encriptação 1](../media/cognitive-services-encryption/qna-encryption-1.png)

2. Quando cria um recurso QnA Maker, está automaticamente associado a uma instância de Pesquisa Azure. Este caso não pode ser utilizado com CMK. Para utilizar a CMK, terá de associar o seu recém-criado exemplo de Azure Search que foi criado no passo 1. Especificamente, terá de atualizar o `AzureSearchAdminKey` e no seu recurso `AzureSearchName` QnA Maker.

   ![Ver definições de encriptação 2](../media/cognitive-services-encryption/qna-encryption-2.png)

3. Em seguida, crie uma nova definição de aplicação:
   * **Nome**: Definir para `CustomerManagedEncryptionKeyUrl`
   * **Valor**: Use o valor que obteve no Passo 1 ao criar a sua instância de Pesquisa Azure.

   ![Ver definições de encriptação 3](../media/cognitive-services-encryption/qna-encryption-3.png)

4. Quando terminar, reinicie o tempo de execução. Agora o seu serviço QnA Maker está habilitado para CMK.

# <a name="qna-maker-managed-preview-release"></a>[QnA Maker gerido (pré-visualização)](#tab/v2)

1.  Aceda ao separador **encriptação** do seu serviço gerido (Preview) do Seu QnA Maker.
2.  Selecione a opção **Chaves Geridas pelo Cliente.** Forneça os detalhes das [suas chaves geridas pelo cliente](../../storage/common/customer-managed-keys-configure-key-vault.md?tabs=portal) e clique em **Save**.

     :::image type="content" source="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png" alt-text="QnA Maker gerido (Preview) CMK definição" lightbox="../media/cognitive-services-encryption/qnamaker-v2-encryption-cmk.png":::

3.  Numa defesa bem sucedida, o CMK será utilizado para encriptar os dados armazenados no Índice de Pesquisa do Azure.

> [!IMPORTANT]
> Recomenda-se definir o seu CMK num serviço de Pesquisa Cognitiva Azure antes de serem criadas quaisquer bases de conhecimento. Se colocar a CMK num serviço QnA Maker com bases de conhecimento existentes, poderá perder o acesso a elas. Leia mais sobre [trabalhar com conteúdo encriptado](../../search/search-security-manage-encryption-keys.md#work-with-encrypted-content) na pesquisa cognitiva do Azure.

> [!NOTE]
> Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie o [Formulário de Pedido de Chave dos Serviços Cognitivos Customer-Managed](https://aka.ms/cogsvc-cmk).

---

## <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão disponíveis em todas as regiões de Pesquisa Azure.

## <a name="encryption-of-data-in-transit"></a>Encriptação de dados em trânsito

O portal QnA Maker funciona no navegador do utilizador. Cada ação desencadeia uma chamada direta para a API do serviço cognitivo respetivo. Assim, a QnA Maker está em conformidade com os dados em trânsito.
No entanto, como o serviço de portal QnA Maker está hospedado no West-US, ainda não é ideal para clientes não americanos. 

## <a name="next-steps"></a>Passos seguintes

* [Encriptação em Pesquisa de Azure usando CMKs no Cofre da Chave Azure](../../search/search-security-manage-encryption-keys.md)
* [Encriptação de dados inativos](../../security/fundamentals/encryption-atrest.md)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)