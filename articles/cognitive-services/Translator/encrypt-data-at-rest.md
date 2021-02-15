---
title: Encriptação do tradutor de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft permite-lhe gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para tradutor, e como ativar e gerir a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ff7b9f86cebc3c2479105d2a52aa92a265f8a1b3
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/15/2021
ms.locfileid: "100524521"
---
# <a name="translator-encryption-of-data-at-rest"></a>Encriptação do tradutor de dados em repouso

O tradutor encripta automaticamente os seus dados, que faz o upload para construir modelos de tradução personalizados, quando este é persistido na nuvem, ajudando a cumprir os seus objetivos de segurança organizacional e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação AES de [140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o [FIPS.](https://en.wikipedia.org/wiki/FIPS_140-2) A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os dados estão protegidos por predefinição e não precisa de modificar o código ou as aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Se estiver a utilizar um nível de preços que suporta teclas geridas pelo Cliente, pode ver as definições de encriptação do seu recurso na secção de **Encriptação** do [portal Azure](https://portal.azure.com), como mostra a imagem seguinte.

![Ver definições de encriptação](../media/cognitive-services-encryption/encryptionblade.png)

Para subscrições que suportam apenas as chaves de encriptação geridas pela Microsoft, não terá uma secção **de Encriptação.**

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com o Azure Key Vault

Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também a opção de gerir a sua subscrição com as suas próprias chaves chamadas chaves geridas pelo cliente (CMK). A CMK oferece uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados. Se a CMK estiver configurada para a sua subscrição, é fornecida uma dupla encriptação, que oferece uma segunda camada de proteção, permitindo ao mesmo tempo controlar a chave de encriptação através do seu Cofre de Chave Azure.

> [!IMPORTANT]
> As chaves geridas pelo cliente estão disponíveis para todos os níveis de preços para o serviço Tradutor. Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie o [Formulário de Pedido de Chave Customer-Managed Tradutor,](https://aka.ms/cogsvc-cmk) demorará aproximadamente 3 a 5 dias úteis para ouvir o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização da CMK com o serviço Tradutor, terá de criar um novo recurso Tradutor. Uma vez criado o seu recurso Tradutor, pode utilizar o Cofre da Chave Azure para configurar a sua identidade gerida.

Siga estes passos para permitir chaves geridas pelo cliente para o Tradutor:

1. Crie o seu novo Tradutor regional ou recurso de Serviços Cognitivos regionais. Isto não funcionará com um recurso global.
2. Identidade Gerida Ativada no portal Azure e adicione as informações chave geridas pelo cliente.
3. Crie um novo espaço de trabalho no Custom Tradutor e associe esta informação de subscrição.

### <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Tem de utilizar o Azure Key Vault para armazenar as chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso de Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Azure Key Vault, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md)

Um novo recurso de Serviços Cognitivos é sempre encriptado usando as teclas geridas pela Microsoft. Não é possível ativar chaves geridas pelo cliente no momento em que o recurso é criado. As chaves geridas pelo cliente são armazenadas no Cofre da Chave Azure, e o cofre-chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave à identidade gerida que está associada ao recurso Serviços Cognitivos. A identidade gerida está disponível assim que o recurso é criado.

Para aprender a usar chaves geridas pelo cliente com cofre de chaves Azure para encriptação de serviços cognitivos, consulte:

- [Configure as chaves geridas pelo cliente com a encriptação Key Vault for Cognitive Services a partir do portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Ativar as chaves geridas pelo cliente também permitirá um sistema atribuído à identidade gerida, uma característica do Azure AD. Uma vez ativada a identidade gerida do sistema, este recurso será registado no Azure Ative Directory. Após a sua inscrição, a identidade gerida terá acesso ao Cofre-Chave selecionado durante a configuração da chave gerida pelo cliente. Pode saber mais sobre [identidades geridas.](../../active-directory/managed-identities-azure-resources/overview.md)

> [!IMPORTANT]
> Se desativar as identidades geridas do sistema, o acesso ao cofre da chave será removido e quaisquer dados encriptados com as teclas do cliente deixarão de estar acessíveis. Quaisquer funcionalidades dependentes destes dados deixarão de funcionar. Quaisquer modelos que tenha implementado também não serão utilizados. Todos os dados enviados serão eliminados do Custom Tradutor. Se as identidades geridas forem re-activadas, não reiparceremos automaticamente o modelo para si.

> [!IMPORTANT]
> As identidades geridas não suportam atualmente cenários de diretórios cruzados. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o recurso de um diretório AD Azure para outro, a identidade gerida associada ao recurso não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a transferência de uma subscrição entre diretórios AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre da Chave Azure

Para ativar as chaves geridas pelo cliente, tem de utilizar um Cofre de Chaves Azure para guardar as suas chaves. Tem de ativar as propriedades **Soft Delete** e **Não Purgar** no cofre da chave.

Apenas as chaves RSA do tamanho 2048 são suportadas com encriptação dos Serviços Cognitivos. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](../../key-vault/general/about-keys-secrets-certificates.md).

> [!NOTE]
> Se todo o cofre de chaves for eliminado, os seus dados deixarão de ser apresentados e todos os seus modelos ficarão desprotendo-se. Todos os dados enviados serão eliminados do Custom Tradutor. 

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](/cli/azure/keyvault). A revogação do acesso bloqueia eficazmente o acesso a todos os dados do recurso Serviços Cognitivos e os seus modelos não serão desprovidos, uma vez que a chave de encriptação é inacessível pelos Serviços Cognitivos. Todos os dados carregados também serão eliminados do Custom Tradutor.


## <a name="next-steps"></a>Passos seguintes

* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)
