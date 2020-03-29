---
title: Utilize o portal Azure para configurar chaves geridas pelo cliente
titleSuffix: Cognitive Services
description: Aprenda a usar o portal Azure para configurar chaves geridas pelo cliente com o Cofre chave Azure. As chaves geridas pelo cliente permitem criar, rodar, desativar e revogar os controlos de acesso.
services: cognitive-services
author: erindormier
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2020
ms.author: egeaney
ms.openlocfilehash: 22bd3afcf30b8b8ebce18b22d5419d49ec8c3b4b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "80053613"
---
# <a name="configure-customer-managed-keys-with-azure-key-vault-by-using-the-azure-portal"></a>Configure as chaves geridas pelo cliente com o Cofre chave Azure utilizando o portal Azure

Você deve usar o Cofre chave Azure para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso Dos Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Este artigo mostra como configurar um Cofre chave Azure com chaves geridas pelo cliente usando o [portal Azure](https://portal.azure.com/). Para aprender a criar um cofre chave utilizando o portal Azure, consulte [Quickstart: set and retrieve a secret from Azure Key Vault utilizando o portal Azure](../../key-vault/quick-create-portal.md).

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

A utilização de chaves geridas pelo cliente requer que sejam definidas duas propriedades no cofre da chave, **Soft Delete** e **Não Purgar**. Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando powerShell ou Azure CLI em um cofre de chave novo ou existente.

> [!IMPORTANT]
> Se não tiver as propriedades **Soft Delete** e **Não Purgar** as propriedades ativadas e eliminar a sua chave, não conseguirá recuperar os dados no seu recurso do Serviço Cognitivo.

Para aprender a ativar estas propriedades num cofre chave existente, consulte as secções intituladas **Enableing soft-delete** e **Enableing Purge Protection** num dos seguintes artigos:

- [Como utilizar soft-delete com powerShell](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-powershell).
- Como utilizar o [soft-delete com o CLI](https://docs.microsoft.com/azure/key-vault/key-vault-soft-delete-cli).

Apenas as chaves RSA do tamanho 2048 são suportadas com encriptação de Armazenamento Azure. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para o seu recurso de Serviços Cognitivos.
1. Na lâmina **Definições** para o seu recurso Serviços Cognitivos, clique em **Encriptação**. Selecione a opção **Chaves Geridas** pelo Cliente, como mostra a seguinte figura.

    ![Screenshot mostrando como selecionar Chaves Geridas pelo Cliente](../media/cognitive-services-encryption/selectcmk.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de ativar as chaves geridas pelo cliente, terá a oportunidade de especificar uma chave para se associar ao recurso dos Serviços Cognitivos.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como URI

Para especificar uma chave como URI, siga estes passos:

1. Para localizar a chave URI no portal Azure, navegue até ao seu cofre chave e selecione a definição de **Keys.** Selecione a tecla desejada e, em seguida, clique na tecla para visualizar as suas versões. Selecione uma versão chave para visualizar as definições para esta versão.
1. Copie o valor do campo **chave identificador,** que fornece o URI.

    ![Screenshot mostrando chave do cofre chave URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nas definições **de Encriptação** para a sua conta de armazenamento, escolha a **opção Enter key URI.**
1. Colhe o URI que copiou no campo **Key URI.**

   ![Screenshot mostrando como entrar na chave URI](../media/cognitive-services-encryption/ssecmk2.png)

1. Especifique a subscrição que contém o cofre da chave.
1. Guarde as alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre chave

Para especificar uma chave de um cofre chave, certifique-se primeiro de que tem um cofre chave que contém uma chave. Para especificar uma chave de um cofre chave, siga estes passos:

1. Escolha o Select a partir da opção **Key Vault.**
1. Selecione o cofre da chave que contém a chave que pretende utilizar.
1. Selecione a chave do cofre da chave.

   ![Screenshot mostrando opção chave gerida pelo cliente](../media/cognitive-services-encryption/ssecmk3.png)

1. Guarde as alterações.

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, atualize o recurso dos Serviços Cognitivos para utilizar a nova versão. Siga estes passos.

1. Navegue para o seu recurso de Serviços Cognitivos e exiba as definições **de Encriptação.**
1. Introduza o URI para a nova versão chave. Alternadamente, pode selecionar o cofre da chave e a chave novamente para atualizar a versão.
1. Guarde as alterações.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação, siga estes passos:

1. Navegue para o seu recurso de Serviços Cognitivos e exiba as definições **de Encriptação.**
1. Introduza o URI para a nova chave. Alternadamente, pode selecionar o cofre da chave e escolher uma nova chave.
1. Guarde as alterações.

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as chaves geridas pelo cliente, o seu recurso Deserviços Cognitivos é então encriptado com chaves geridas pela Microsoft. Para desativar as chaves geridas pelo cliente, siga estes passos:

1. Navegue para o seu recurso de Serviços Cognitivos e exiba as definições **de Encriptação.**
1. Desmarque a caixa de verificação ao lado da definição **da sua própria tecla.**

## <a name="next-steps"></a>Passos seguintes

* [O que é o Cofre chave Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
* [Formulário de pedido de chave gerido pelo cliente de serviços cognitivos](https://aka.ms/cogsvc-cmk)
* [Encriptação de dados dos Serviços Faciais em repouso](../Face/face-encryption-of-data-at-rest.md)
* [QnA Maker encriptação de dados em repouso](../QnAMaker/qna-maker-encryption-of-data-at-rest.md)
* [Encriptação do serviço de compreensão de idiomas de dados em repouso](../LUIS/luis-encryption-of-data-at-rest.md)
* [Encriptação moderadora de conteúdo de dados em repouso](../Content-Moderator/content-moderator-encryption-of-data-at-rest.md)
* [Encriptação de dados de tradutor em repouso](../translator/translator-encryption-of-data-at-rest.md)
* [Encriptação personalizada de dados em repouso](../personalizer/personalizer-encryption-of-data-at-rest.md)
