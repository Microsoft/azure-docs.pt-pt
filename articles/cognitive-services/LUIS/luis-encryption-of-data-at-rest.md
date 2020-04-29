---
title: Encriptação do serviço de compreensão de idiomas de dados em repouso
titleSuffix: Azure Cognitive Services
description: Encriptação do serviço de compreensão de idiomas de dados em repouso.
author: erindormier
manager: venkyv
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 03/13/2020
ms.author: egeaney
ms.openlocfilehash: 59e066974f690bda2384504cc27af5aa94b7b75b
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "79372339"
---
# <a name="language-understanding-service-encryption-of-data-at-rest"></a>Encriptação do serviço de compreensão de idiomas de dados em repouso

O serviço de Compreensão de Idiomas encripta automaticamente os seus dados quando é persistido na nuvem. A encriptação do serviço De Compreensão de Idiomas protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e conformidade.

## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos

Os dados são encriptados e desencriptados utilizando encriptação [AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) compatível com [140-2](https://en.wikipedia.org/wiki/FIPS_140-2) bits. A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os seus dados são seguros por padrão e não precisa de modificar o seu código ou aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão da chave de encriptação

Por padrão, a sua subscrição utiliza chaves de encriptação geridas pela Microsoft. Existe também uma opção para gerir a sua subscrição com as suas próprias chaves. As chaves geridas pelo cliente (CMK), oferecem maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.

## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com o Azure Key Vault

Existe também uma opção para gerir a sua subscrição com as suas próprias chaves. As chaves geridas pelo cliente (CMK), também conhecidacomo Bring your own key (BYOK), oferecem maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os seus dados.

Você deve usar o Cofre chave Azure para armazenar as suas chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso Dos Serviços Cognitivos e o cofre-chave devem estar na mesma região e no mesmo inquilino azure Ative Directory (Azure AD), mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre de Chaves Azure?](https://docs.microsoft.com/azure/key-vault/key-vault-overview)

### <a name="customer-managed-keys-for-language-understanding"></a>Chaves geridas pelo cliente para compreensão de linguagem

Para solicitar a capacidade de utilizar as chaves geridas pelo cliente, preencha e envie o Formulário de [Pedido de Chave Gerido](https://aka.ms/cogsvc-cmk)pelo Cliente gerido pelo cliente do serviço LUIS . Levará aproximadamente 3-5 dias úteis para voltar a ouvir o estado do seu pedido. Dependendo da procura, pode ser colocado numa fila e aprovado à medida que o espaço se torna disponível. Uma vez aprovado para utilizar cmk com LUIS, você precisará criar um novo recurso de Compreensão de Linguagem a partir do portal Azure e selecionar E0 como o Nível de Preços. O novo SKU funcionará da mesma forma que o F0 SKU que já está disponível, exceto para a CMK. Os utilizadores não poderão fazer o upgrade do F0 para o novo E0 SKU.

Os recursos E0 só estão disponíveis para o serviço de autoria e que o nível E0 será inicialmente apoiado apenas na Região Dos Estados Ocidentais.

![Imagem de subscrição luis](../media/cognitive-services-encryption/luis-subscription.png)

### <a name="regional-availability"></a>Disponibilidade regional

As chaves geridas pelo cliente estão atualmente disponíveis na região **oeste dos EUA.**

### <a name="limitations"></a>Limitações

Existem algumas limitações ao utilizar o nível E0 com aplicações já criadas/anteriormente criadas:

* A migração para um recurso E0 será bloqueada. Os utilizadores só poderão migrar as suas apps para os recursos F0. Depois de migrar um recurso existente para F0, pode criar um novo recurso no nível E0. Saiba mais sobre [a migração aqui.](https://docs.microsoft.com/azure/cognitive-services/luis/luis-migration-authoring)  
* As aplicações de deslocação de ou para um recurso E0 serão bloqueadas. Um trabalho em torno desta limitação é exportar a sua aplicação existente, e importá-la como um recurso E0.
* A funcionalidade de verificação bing spell não é suportada.
* O tráfego de utilizador final de registo é desativado se a sua aplicação for E0.
* A capacidade de escoramento de Discursos do serviço Azure Bot não é suportada para aplicações no nível E0. Esta funcionalidade está disponível através do Serviço Bot Azure, que não suporta a CMK.
* A capacidade de escoramento de discursos do portal requer armazenamento de Blob Azure. Para mais informações, [consulte o seu próprio armazenamento.](../Speech-Service/speech-encryption-of-data-at-rest.md#bring-your-own-storage-byos-for-customization-and-logging)

### <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

Um novo recurso dos Serviços Cognitivos é sempre encriptado utilizando chaves geridas pela Microsoft. Não é possível ativar as chaves geridas pelo cliente no momento em que o recurso é criado. As chaves geridas pelo cliente são armazenadas no Cofre chave Azure, e o cofre chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave para a identidade gerida que está associada ao recurso dos Serviços Cognitivos. A identidade gerida só está disponível após a criação do recurso utilizando o Nível de Preços para A CMK.

Para aprender a usar chaves geridas pelo cliente com o Cofre chave Azure para encriptação de Serviços Cognitivos, consulte:

- [Configure chaves geridas pelo cliente com chave vault para encriptação de serviços cognitivos do portal Azure](../Encryption/cognitive-services-encryption-keys-portal.md)

Ativar as chaves geridas pelo cliente também permitirá um sistema de identidade gerida atribuída, uma funcionalidade da Azure AD. Uma vez ativada a identidade gerida do sistema atribuída, este recurso será registado no Azure Ative Directory. Depois de registada, a identidade gerida terá acesso ao Cofre chave selecionado durante a configuração da chave gerida pelo cliente. Pode saber mais sobre [identidades geridas.](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/overview)

> [!IMPORTANT]
> Se desativar as identidades geridas atribuídas ao sistema, o acesso ao cofre da chave será removido e quaisquer dados encriptados com as teclas do cliente deixarão de estar acessíveis. Quaisquer funcionalidades dependentes destes dados deixarão de funcionar.

> [!IMPORTANT]
> As identidades geridas não suportam atualmente cenários transversais. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída sob as capas. Se posteriormente deslocar a subscrição, o grupo de recursos ou o recurso de um diretório Azure AD para outro, a identidade gerida associada ao recurso não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a Transferência de uma subscrição entre diretórios da AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/known-issues#transferring-a-subscription-between-azure-ad-directories).  

### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Armazenar chaves geridas pelo cliente no Cofre de Chaves Azure

Para ativar as chaves geridas pelo cliente, tem de utilizar um Cofre de Chave Azure para armazenar as suas chaves. Deve ativar as propriedades **Soft Delete** e **Não purgar** as propriedades no cofre da chave.

Apenas as chaves RSA do tamanho 2048 são suportadas com encriptação dos Serviços Cognitivos. Para mais informações sobre as chaves, consulte **as chaves key vault** em [chaves, segredos e certificados do Cofre chave Azure.](https://docs.microsoft.com/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys)

### <a name="rotate-customer-managed-keys"></a>Rode as chaves geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a chave estiver rodada, deve atualizar o recurso dos Serviços Cognitivos para utilizar a nova chave URI. Para aprender a atualizar o recurso para utilizar uma nova versão da chave no portal Azure, consulte a secção intitulada **Atualizar a versão chave** em Chaves geridas pelo cliente para [serviços cognitivos](../Encryption/cognitive-services-encryption-keys-portal.md)através do portal Azure .

Rodar a tecla não desencadeia a reencriptação de dados no recurso. Não são necessárias mais medidas por parte do utilizador.

### <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para mais informações, consulte [o Cofre de Chaves Azure PowerShell](https://docs.microsoft.com/powershell/module/az.keyvault//) ou o Cofre de Chaves [Azure CLI](https://docs.microsoft.com/cli/azure/keyvault). Revogar o acesso bloqueia efetivamente o acesso a todos os dados do recurso Serviços Cognitivos, uma vez que a chave de encriptação é inacessível pelos Serviços Cognitivos.

## <a name="next-steps"></a>Passos seguintes

* [Formulário de pedido de chave gerido pelo cliente gerido pelo cliente](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Cofre de Chaves Azure](https://docs.microsoft.com/azure/key-vault/key-vault-overview)
