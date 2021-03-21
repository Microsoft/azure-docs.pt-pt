---
title: Encriptação de serviço de serviço de comandos personalizados em repouso
titleSuffix: Azure Cognitive Services
description: Comandos personalizados encriptam dados em repouso.
services: cognitive-services
author: singhsaumya
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2020
ms.author: sausin
ms.openlocfilehash: 89d7a6f8beb004f57a00dfe75e4cc387c8591b1e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716589"
---
# <a name="custom-commands-encryption-of-data-at-rest"></a>Encriptação de Comandos Personalizados de dados inativos

Os Comandos Personalizados encriptam automaticamente os seus dados quando estes são persistidos na nuvem. A encriptação do serviço Comandos Personalizados protege os seus dados e ajuda-o a cumprir os seus compromissos de segurança organizacional e de conformidade.

> [!NOTE]
> O serviço De Comandos Personalizados não permite automaticamente encriptação para os recursos LUIS associados à sua aplicação. Se necessário, deve ativar a encriptação do seu recurso LUIS a partir [daqui.](../luis/encrypt-data-at-rest.md)

## <a name="about-cognitive-services-encryption"></a>Sobre a encriptação dos Serviços Cognitivos
Os dados são encriptados e desencriptados utilizando encriptação AES de [140-2](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) em conformidade com o [FIPS.](https://en.wikipedia.org/wiki/FIPS_140-2) A encriptação e a desencriptação são transparentes, o que significa que a encriptação e o acesso são geridos para si. Os dados estão protegidos por predefinição e não precisa de modificar o código ou as aplicações para tirar partido da encriptação.

## <a name="about-encryption-key-management"></a>Sobre a gestão de chaves de encriptação

Quando utilizar Comandos Personalizados, o serviço de fala armazenará os seguintes dados na nuvem:
* Configuração JSON por trás da aplicação Comandos Personalizados
* A chave de autoria e previsão do LUIS

Por predefinição, a subscrição utiliza chaves de encriptação geridas pela Microsoft. No entanto, também pode gerir a subscrição com as suas próprias chaves de encriptação. As chaves geridas pelo cliente (CMK), também conhecidas como bring your own key (BYOK), oferecem maior flexibilidade para criar, rodar, desativar e revogar os controlos de acesso. Também pode auditar as chaves de encriptação utilizadas para proteger os dados.


> [!IMPORTANT]
> As chaves geridas pelo cliente só são recursos disponíveis criados após 27 de junho de 2020. Para utilizar a CMK com serviços de fala, terá de criar um novo recurso de Discurso. Uma vez criado o recurso, pode utilizar o Azure Key Vault para configurar a sua identidade gerida.

Para solicitar a capacidade de utilizar chaves geridas pelo cliente, preencha e envie Customer-Managed Formulário de Pedido chave. Levará aproximadamente 3 a 5 dias úteis para ouvir de volta o estado do seu pedido. Dependendo da procura, você pode ser colocado em uma fila e aprovado à medida que o espaço fica disponível. Uma vez aprovado para a utilização de CMK com Serviços de Fala, terá de criar um novo recurso de Discurso a partir do portal Azure.
   > [!NOTE]
   > **As teclas geridas pelo cliente (CMK) são suportadas apenas para Comandos Personalizados.**
   >
   >  **A Voz Personalizada e a Voz Personalizada ainda suportam apenas o seu próprio armazenamento (BYOS).**  [Saiba mais](speech-encryption-of-data-at-rest.md)
   >
   > Se estiver a utilizar o recurso de fala dado para aceder a estes serviços, as necessidades de conformidade devem ser satisfeitas configurando explicitamente o BYOS.


## <a name="customer-managed-keys-with-azure-key-vault"></a>Chaves geridas pelo cliente com o Azure Key Vault

Tem de utilizar o Cofre da Chave Azure para armazenar chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O recurso Speech e o cofre-chave devem estar na mesma região e no mesmo inquilino do Azure Ative Directory (Azure AD), mas podem estar em subscrições diferentes. Para mais informações sobre o Azure Key Vault, veja [o que é Azure Key Vault?](../../key-vault/general/overview.md)

Quando um novo recurso de Discurso é criado e usado para a aplicação de Comandos Personalizados - os dados são sempre encriptados usando as teclas geridas pela Microsoft. Não é possível ativar chaves geridas pelo cliente no momento em que o recurso é criado. As chaves geridas pelo cliente são armazenadas no Cofre da Chave Azure, e o cofre-chave deve ser aprovisionado com políticas de acesso que concedem permissões-chave à identidade gerida que está associada ao recurso Serviços Cognitivos. A identidade gerida só está disponível após a criação do recurso utilizando o Nível de Preços necessário para a CMK.

Ativar as chaves geridas pelo cliente também permitirá um sistema de [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md)atribuída , uma característica do Azure AD. Uma vez ativada a identidade gerida do sistema, este recurso será registado no Azure Ative Directory. Após a sua inscrição, a identidade gerida terá acesso ao Cofre-Chave selecionado durante a configuração da chave gerida pelo cliente. 

> [!IMPORTANT]
> Se desativar as identidades geridas do sistema, o acesso ao cofre da chave será removido e quaisquer dados encriptados com as teclas do cliente deixarão de estar acessíveis. Quaisquer funcionalidades dependentes destes dados deixarão de funcionar.

> [!IMPORTANT]
> As identidades geridas não suportam atualmente cenários de diretórios cruzados. Quando configura as chaves geridas pelo cliente no portal Azure, uma identidade gerida é automaticamente atribuída sob as capas. Se posteriormente mover a subscrição, o grupo de recursos ou o recurso de um diretório AD Azure para outro, a identidade gerida associada ao recurso não é transferida para o novo inquilino, pelo que as chaves geridas pelo cliente podem deixar de funcionar. Para obter mais informações, consulte **a transferência de uma subscrição entre diretórios AD Azure** em [FAQs e questões conhecidas com identidades geridas para recursos Azure](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).  

## <a name="configure-azure-key-vault"></a>Configurar o Azure Key Vault

A utilização de chaves geridas pelo cliente requer que sejam definidas duas propriedades no cofre da chave, **Soft Delete** e **Não Purpuria**. Estas propriedades não são ativadas por padrão, mas podem ser ativadas usando o PowerShell ou o Azure CLI num cofre de chaves novo ou existente.

> [!IMPORTANT]
> Se não tiver as propriedades **Soft Delete** e **Não Purgar** ativadas e eliminar a sua chave, não poderá recuperar os dados no seu recurso Serviço Cognitivo.

Para saber como ativar estas propriedades num cofre de chaves existente, consulte as secções intituladas **Permitir a eliminação suave** e permitir a **proteção contra purgas** num dos seguintes artigos:

- [Como utilizar o soft-delete com PowerShell](../../key-vault/general/key-vault-recovery.md).
- [Como utilizar o soft-delete com CLI](../../key-vault/general/key-vault-recovery.md).

Apenas as teclas RSA do tamanho 2048 são suportadas com encriptação de Armazenamento Azure. Para obter mais informações sobre as chaves, consulte **as chaves do Cofre chave** em chaves [Azure Key Vault, segredos e certificados](../../key-vault/general/about-keys-secrets-certificates.md).

## <a name="enable-customer-managed-keys-for-your-speech-resource"></a>Ativar as chaves geridas pelo cliente para o seu recurso Speech

Para ativar as chaves geridas pelo cliente no portal Azure, siga estes passos:

1. Navegue para o seu recurso Speech.
1. Na lâmina **Definições** para o seu recurso De discurso, clique em **Encriptação**. Selecione a opção **Chaves Geridas pelo Cliente,** como mostra a seguinte figura.

 ![Screenshot mostrando como selecionar Chaves Geridas pelo Cliente](media/custom-commands/select-cmk.png)

## <a name="specify-a-key"></a>Especificar uma chave

Depois de ativar as chaves geridas pelo cliente, terá a oportunidade de especificar uma chave para associar ao recurso Serviços Cognitivos.

### <a name="specify-a-key-as-a-uri"></a>Especifique uma chave como um URI

Para especificar uma chave como URI, siga estes passos:

1. Para localizar a chave URI no portal Azure, navegue até ao cofre da chave e selecione a definição **de Chaves.** Selecione a tecla desejada e, em seguida, clique na chave para visualizar as suas versões. Selecione uma versão chave para visualizar as definições para esta versão.
1. Copie o valor do campo **identificador chave,** que fornece o URI.

    ![Screenshot mostrando chave de cofre chave URI](../media/cognitive-services-encryption/key-uri-portal.png)

1. Nas definições **de Encriptação** para o seu serviço Speech, escolha a opção **URI da chave entrar.**
1. Cole o URI que copiou para o campo **Key URI.**

1. Especifique a subscrição que contém o cofre de chaves.
1. Guarde as alterações.

### <a name="specify-a-key-from-a-key-vault"></a>Especifique uma chave de um cofre chave

Para especificar uma chave de um cofre de chaves, certifique-se primeiro de que tem um cofre chave que contém uma chave. Para especificar uma chave de um cofre de chaves, siga estes passos:

1. Escolha a **opção Selecionar a partir da** opção Key Vault.
1. Selecione o cofre de chaves que contém a chave que pretende utilizar.
1. Selecione a chave do cofre da chave.

   ![Screenshot mostrando a opção chave gerida pelo cliente](media/custom-commands/configure-cmk-fromKV.png)

1. Guarde as alterações.

## <a name="update-the-key-version"></a>Atualizar a versão chave

Quando criar uma nova versão de uma chave, atualize o recurso Speech para utilizar a nova versão. Siga estes passos:

1. Navegue para o seu recurso De discurso e apresente as definições **de Encriptação.**
1. Introduza o URI para a nova versão chave. Em alternativa, pode selecionar o cofre da chave e a chave novamente para atualizar a versão.
1. Guarde as alterações.

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação, siga estes passos:

1. Navegue para o seu recurso De discurso e apresente as definições **de Encriptação.**
1. Insira o URI para a nova chave. Alternadamente, pode selecionar o cofre da chave e escolher uma nova chave.
1. Guarde as alterações.

## <a name="rotate-customer-managed-keys"></a>Rode as teclas geridas pelo cliente

Pode rodar uma chave gerida pelo cliente no Cofre de Chaves Azure de acordo com as suas políticas de conformidade. Quando a tecla estiver rodada, tem de atualizar o recurso Speech para utilizar o novo URI da tecla. Para saber como atualizar o recurso para utilizar uma nova versão da chave no portal Azure, consulte [a versão chave](#update-the-key-version).

Rodar a tecla não desencadeia a reencriminação de dados no recurso. Não é necessária nenhuma outra ação por parte do utilizador.

## <a name="revoke-access-to-customer-managed-keys"></a>Revogar o acesso às chaves geridas pelo cliente

Para revogar o acesso às chaves geridas pelo cliente, utilize o PowerShell ou o Azure CLI. Para obter mais informações, consulte [Azure Key Vault PowerShell](/powershell/module/az.keyvault//) ou [Azure Key Vault CLI](/cli/azure/keyvault). A revogação do acesso bloqueia eficazmente o acesso a todos os dados do recurso Serviços Cognitivos, uma vez que a chave de encriptação é inacessível pelos Serviços Cognitivos.

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Quando desativa as teclas geridas pelo cliente, o seu recurso Speech é então encriptado com teclas geridas pela Microsoft. Para desativar as chaves geridas pelo cliente, siga estes passos:

1. Navegue para o seu recurso De discurso e apresente as definições **de Encriptação.**
1. Desmarcar a caixa de verificação ao lado da definição **de teclas.**

## <a name="next-steps"></a>Passos seguintes

* [Formulário de pedido chave Customer-Managed de discurso](https://aka.ms/cogsvc-cmk)
* [Saiba mais sobre o Azure Key Vault](../../key-vault/general/overview.md)
* [O que são identidades geridas](../../active-directory/managed-identities-azure-resources/overview.md)