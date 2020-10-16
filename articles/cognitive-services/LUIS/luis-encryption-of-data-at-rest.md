---
title: Encriptação do serviço de compreensão de linguagem de dados em repouso
titleSuffix: Azure Cognitive Services
description: A Microsoft oferece chaves de encriptação geridas pela Microsoft e também permite gerir as suas subscrições de Serviços Cognitivos com as suas próprias chaves, chamadas teclas geridas pelo cliente (CMK). Este artigo cobre a encriptação de dados em repouso para a Compreensão da Linguagem (LUIS), e como ativar e gerir a CMK.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 08/28/2020
ms.author: egeaney
ms.openlocfilehash: ce6561652801d52e5600ddc63e573070281da3f2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "89078134"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Encriptação do serviço de compreensão de linguagem de dados em repouso

O serviço de Compreensão de Idiomas encripta automaticamente os seus dados quando estes são persistidos na nuvem. A encriptação do serviço Language Understanding protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação AES de [140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o [FIPS.](https://en.wikipedia.org/wiki/FIPS_140-2) A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os dados estão protegidos por predefinição e não precisa de modificar o código ou as aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também a opção de gerir a sua subscrição com as suas próprias chaves chamadas chaves geridas pelo cliente (CMK). A CMK oferece uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com o Azure Key Vault

Existe também uma opção para gerir a sua subscrição com as suas próprias chaves. As teclas geridas pelo cliente (CMK), também conhecidas como Bring your own key (BYOK), oferecem uma maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados.

Tem de utilizar o Azure Key Vault para armazenar as chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso de Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Azure Key Vault, veja [o que é Azure Key Vault?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

### <a name="customer-managed-keys-for-language-understanding"></a>Chaves geridas pelo cliente para a compreensão de linguagem

Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie o [Formulário de Pedido chave do Serviço LUIS Customer-Managed](https://aka.ms/cogsvc-cmk). Levará aproximadamente 3 a 5 dias úteis para ouvir de volta o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização de CMK com LUIS, terá de criar um novo recurso de Compreensão linguística a partir do portal Azure e selecionar e0 como O Nível de Preços. O novo SKU funcionará da mesma forma que o F0 SKU que já está disponível, exceto para CMK. Os utilizadores não poderão fazer upgrade do F0 para o novo E0 SKU.

![Imagem de assinatura LUIS](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="limitations"></a>Limitações

Existem algumas limitações ao utilizar o nível E0 com aplicações existentes/previamente criadas:

* A migração para um recurso E0 será bloqueada. Os utilizadores só poderão migrar as suas apps para recursos F0. Depois de migrar um recurso existente para F0, pode criar um novo recurso no nível E0. Saiba mais sobre [migração aqui.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)  
* A deslocação de aplicações para ou a partir de um recurso E0 será bloqueada. Um trabalho em torno desta limitação é exportar a sua aplicação existente, e importá-la como um recurso E0.
* A função de verificação de feitiços Bing não é suportada.
* O tráfego de utilizador final de registo é desativado se a sua aplicação for E0.
* A capacidade de escorvação do Discurso do serviço Azure Bot não é suportada para aplicações no nível E0. Esta funcionalidade está disponível através do Serviço Azure Bot, que não suporta a CMK.
* A capacidade de escorva da fala do portal requer armazenamento Azure Blob. Para mais informações, consulte [o seu próprio armazenamento.](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)

### <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Um novo recurso de Serviços Cognitivos é sempre encriptado usando as teclas geridas pela Microsoft. Não é possível ativar chaves geridas pelo cliente no momento em que o recurso é criado. As chaves geridas pelo cliente são armazenadas no Cofre da Chave Azure, e o cofre-chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave à identidade gerida que está associada ao recurso Serviços Cognitivos. A identidade gerida só está disponível após a criação do recurso utilizando o Preço Tier para CMK.

Para aprender a usar chaves geridas pelo cliente com cofre de chaves Azure para encriptação de serviços cognitivos, consulte:

- [Configure as chaves geridas pelo cliente com a encriptação Key Vault for Cognitive Services a partir do portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Ativar as chaves geridas pelo cliente também permitirá um sistema atribuído à identidade gerida, uma característica do Azure AD. Uma vez ativada a identidade gerida do sistema, este recurso será registado no Azure Ative Directory. Após a sua inscrição, a identidade gerida terá acesso ao Cofre-Chave selecionado durante a configuração da chave gerida pelo cliente. Pode saber mais sobre [identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Se desativar as identidades geridas do sistema, o acesso ao cofre da chave será removido e quaisquer dados encriptados com as teclas do cliente deixarão de estar acessíveis. Quaisquer funcionalidades dependentes destes dados deixarão de funcionar.

> [!IMPORTANT]
> As identidades geridas não suportam atualmente cenários de diretórios cruzados. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o recurso de um diretório AD Azure para outro, a identidade gerida associada ao recurso não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a transferência de uma subscrição entre diretórios AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre da Chave Azure

Para ativar as chaves geridas pelo cliente, tem de utilizar um Cofre de Chaves Azure para guardar as suas chaves. Tem de ativar as propriedades **Soft Delete** e **Não Purgar** no cofre da chave.

Apenas as chaves RSA do tamanho 2048 são suportadas com encriptação dos Serviços Cognitivos. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

### <a name="rotate-customer-managed-keys"></a>Rode as teclas geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a tecla estiver rodada, deve atualizar o recurso Serviços Cognitivos para utilizar o novo URI da chave. Para saber como atualizar o recurso para utilizar uma nova versão da chave no portal Azure, consulte a secção intitulada **Update the key version** in [Configure customer-managed for Cognitive Services utilizando o portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md).

Rodar a tecla não desencadeia a reencriminação de dados no recurso. Não é necessária nenhuma outra ação por parte do utilizador.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). A revogação do acesso bloqueia eficazmente o acesso a todos os dados do recurso Serviços Cognitivos, uma vez que a chave de encriptação é inacessível pelos Serviços Cognitivos.

## <a name="next-steps"></a>Passos seguintes

* [Formulário de pedido chave Customer-Managed serviço LUIS](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
