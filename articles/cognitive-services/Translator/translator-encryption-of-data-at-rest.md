---
title: Encriptação de dados de tradutor em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação de dados de tradutor em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 44bb223dbc944be1b8769aa2572f1b88b916528b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372240"
---
# <a name="translator-encryption-of-data-at-rest"></a>Encriptação de dados de tradutor em repouso

O tradutor encripta automaticamente os seus dados, que envia para construir modelos de tradução personalizada, quando é persistido na nuvem, ajudando a cumprir os seus objetivos de segurança organizacional e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [140-2](https://en.wikipedia.org/wiki/FIPS_140-2) bits. A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os seus dados são seguros por padrão e não precisa de modificar o seu código ou aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Por padrão, a sua subscrição utiliza chaves de encriptação geridas pela Microsoft. Se estiver a utilizar um nível de preços que suporta as chaves geridas pelo Cliente, pode ver as definições de encriptação do seu recurso na secção de **encriptação** do [portal Azure,](https://portal.azure.com)como mostra a imagem seguinte.

![Ver definições de encriptação](../media/cognitive-services-encryption/encryptionblade.png)

Para subscrições que suportam apenas chaves de encriptação geridas pela Microsoft, não terá uma secção de **Encriptação.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com o Azure Key Vault

Existe também uma opção para gerir a sua subscrição com as suas próprias chaves. As chaves geridas pelo cliente (CMK), também conhecidacomo Bring your own key (BYOK), oferecem maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.

> [!IMPORTANT]
> As chaves geridas pelo cliente estão disponíveis para todos os níveis de preços para o serviço Tradutor. Para solicitar a capacidade de utilizar as chaves geridas pelo cliente, preencha e envie o Formulário de Pedido de Chave Gerido pelo Cliente do [Tradutor,](https://aka.ms/cogsvc-cmk) levará aproximadamente 3-5 dias úteis para voltar a ouvir o estado do seu pedido. Dependendo da procura, pode ser colocado numa fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para utilizar a CMK com o serviço Tradutor, terá de criar um novo recurso Tradutor. Assim que o seu recurso Tradutor for criado, pode utilizar o Cofre chave Azure para configurar a sua identidade gerida.

Siga estes passos para ativar as chaves geridas pelo cliente para o Tradutor:

1. Crie o seu novo texto de tradutor regional ou recurso regional de Serviços Cognitivos. Isto não funcionará com um recurso global.
2. Identidade Gerida Ativada no portal Azure e adicione informações chave geridas pelo cliente.
3. Crie um novo espaço de trabalho em Tradutor Personalizado e associe esta informação de subscrição.

[!INCLUDE [cognitive-services-cmk](../../../includes/cognitive-services-cmk-regions.md)]

### <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Você deve usar o Cofre chave Azure para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso Dos Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

Um novo recurso dos Serviços Cognitivos é sempre encriptado utilizando chaves geridas pela Microsoft. Não é possível ativar as chaves geridas pelo cliente no momento em que o recurso é criado. As chaves geridas pelo cliente são armazenadas no Cofre chave Azure, e o cofre chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave para a identidade gerida que está associada ao recurso dos Serviços Cognitivos. A identidade gerida está disponível assim que o recurso é criado.

Para aprender a usar chaves geridas pelo cliente com o Cofre chave Azure para encriptação de Serviços Cognitivos, consulte:

- [Configure chaves geridas pelo cliente com chave vault para encriptação de serviços cognitivos do portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Ativar as chaves geridas pelo cliente também permitirá um sistema de identidade gerida atribuída, uma funcionalidade da Azure AD. Uma vez ativada a identidade gerida do sistema atribuída, este recurso será registado no Azure Ative Directory. Depois de registada, a identidade gerida terá acesso ao Cofre chave selecionado durante a configuração da chave gerida pelo cliente. Pode saber mais sobre [identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Se desativar as identidades geridas atribuídas ao sistema, o acesso ao cofre da chave será removido e quaisquer dados encriptados com as teclas do cliente deixarão de estar acessíveis. Quaisquer funcionalidades dependentes destes dados deixarão de funcionar. Quaisquer modelos que tenha implantado também não serão implantados. Todos os dados enviados serão eliminados do Tradutor Personalizado. Se as identidades geridas forem reativadas, não recolocaremos automaticamente o modelo para si.

> [!IMPORTANT]
> As identidades geridas não suportam atualmente cenários transversais. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída sob as capas. Se posteriormente deslocar a subscrição, o grupo de recursos ou o recurso de um diretório Azure AD para outro, a identidade gerida associada ao recurso não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a Transferência de uma subscrição entre diretórios da AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre de Chaves Azure

Para ativar as chaves geridas pelo cliente, tem de utilizar um Cofre de Chave Azure para armazenar as suas chaves. Deve ativar as propriedades **Soft Delete** e **Não purgar** as propriedades no cofre da chave.

Apenas as chaves RSA do tamanho 2048 são suportadas com encriptação dos Serviços Cognitivos. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

> [!NOTE]
> Se todo o cofre da chave for eliminado, os seus dados deixarão de ser apresentados e todos os seus modelos ficarão desdistribuídos. Todos os dados enviados serão eliminados do Tradutor Personalizado. 

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para mais informações, consulte [o Cofre de Chaves Azure PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou o Cofre de Chaves [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os dados do recurso Serviços Cognitivos e os seus modelos não serão implantados, uma vez que a chave de encriptação é inacessível pelos Serviços Cognitivos. Todos os dados enviados também serão eliminados do Tradutor Personalizado.


## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
