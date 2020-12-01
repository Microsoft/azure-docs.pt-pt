---
title: Encrypt Azure Data Factory com chave gerida pelo cliente
description: Aumentar a segurança da Data Factory com a sua própria chave (BYOK)
services: data-factory
documentationcenter: ''
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: f1a7bffc05d83b30fe9e5bcd6e17bf6bc0192e1d
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348947"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Encrypt Azure Data Factory com chaves geridas pelo cliente

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory encripta dados em repouso, incluindo definições de entidades e quaisquer dados em cache enquanto as execuções estão em andamento. Por padrão, os dados são encriptados com uma chave gerida aleatoriamente pela Microsoft que é atribuída exclusivamente à sua fábrica de dados. Para obter garantias de segurança adicionais, pode agora ativar Bring Your Own Key (BYOK) com a funcionalidade de chaves geridas pelo cliente na Azure Data Factory. Quando especifica uma chave gerida pelo cliente, a Data Factory utiliza __a__ chave do sistema de fábrica e a CMK para encriptar os dados do cliente. Faltar qualquer um dos dois resultaria em Negação de Acesso a dados e fábrica.

O Azure Key Vault é necessário para armazenar chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O cofre-chave e a Fábrica de Dados devem estar no mesmo inquilino do Azure Ative Directory (Azure AD) e na mesma região, mas podem estar em subscrições diferentes. Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../key-vault/general/overview.md)

> [!NOTE]
> Uma chave gerida pelo cliente só pode ser configurada numa fábrica de dados vazia. A fábrica de dados não pode conter quaisquer recursos, tais como serviços ligados, oleodutos e fluxos de dados. Recomenda-se que a chave gerida pelo cliente seja gerida pelo cliente logo após a criação da fábrica.

## <a name="about-customer-managed-keys"></a>Sobre chaves geridas pelo cliente

O seguinte diagrama mostra como a Data Factory utiliza o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

  ![Diagrama mostrando como as chaves geridas pelo cliente funcionam na Azure Data Factory](media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png)

A lista seguinte explica os passos numerados no diagrama:

1. Um administrador do Azure Key Vault concede permissões a chaves de encriptação da identidade gerida associada à Fábrica de Dados
1. Um administrador da Data Factory permite a funcionalidade chave gerida pelo cliente na fábrica
1. A Data Factory utiliza a identidade gerida que está associada à fábrica para autenticar o acesso ao Cofre de Chaves Azure via Azure Ative Directory
1. Data Factory envolve a chave de encriptação da fábrica com a chave do cliente no Cofre da Chave Azure
1. Para operações de leitura/escrita, a Data Factory envia pedidos ao Cofre da Chave Azure para desembrulhar a chave de encriptação da conta para realizar operações de encriptação e desencriptação

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Pré-requisitos - configurar cofre de chaves Azure e gerar chaves

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Ativar a eliminação suave e não purgar no cofre da chave Azure

A utilização de chaves geridas pelo cliente com a Data Factory requer duas propriedades a serem definidas no Cofre da Chave, __Eliminação Suave__ e __Não Purga__. Estas propriedades podem ser ativadas utilizando o PowerShell ou o Azure CLI num cofre de chaves novo ou existente. Para saber como ativar estas propriedades num cofre de chaves existente, consulte as secções intituladas _Permitir a eliminação suave_ e permitir a _proteção contra purgas_ num dos seguintes artigos:

- [Como utilizar soft-delete com PowerShell](../key-vault/general/key-vault-recovery.md)
- [Como usar soft-delete com CLI](../key-vault/general/key-vault-recovery.md)

Se estiver a criar um novo Cofre de Chaves Azure através do portal Azure, __o Soft Delete__ e o Não __Purga__ pode ser ativado da seguinte forma:

  ![Screenshot enable Soft Delete and Purpur Protection após a criação do Cofre chave](media/enable-customer-managed-key/01-enable-purge-protection.png)

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Grant Data Factory acesso ao Cofre de Chaves Azure

Certifique-se de que a Azure Key Vault e a Azure Data Factory estão no mesmo inquilino do Azure Ative Directory (Azure AD) e na _mesma região._ A partir do controlo de acesso do Azure Key Vault, conceder a Identidade de Serviço Gerido (MSI) da fábrica de dados seguintes permissões: _Obter_, _Desembrulhar a Chave_ e A Chave de _Embrulho_. Estas permissões são necessárias para permitir chaves geridas pelo cliente na Data Factory.

  ![Screenshot permite o acesso da fábrica de dados ao cofre de chaves](media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png)

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Gerar ou carregar a chave gerida pelo cliente para o Cofre da Chave Azure

Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. Apenas as teclas RSA de 2048 são suportadas com encriptação data Factory. Para mais informações, consulte [sobre chaves, segredos e certificados.](../key-vault/general/about-keys-secrets-certificates.md)

  ![Screenshot Gerar Chave Gerida pelo Cliente](media/enable-customer-managed-key/03-create-key.png)

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

1. Certifique-se de que a Fábrica de Dados está vazia. A fábrica de dados não pode conter quaisquer recursos, tais como serviços ligados, oleodutos e fluxos de dados. Por enquanto, a utilização da chave gerida pelo cliente para uma fábrica não vazia resultará num erro.

1. Para localizar a chave URI no portal Azure, navegue até ao Cofre da Chave Azure e selecione a definição de Chaves. Selecione a tecla procurada e, em seguida, clique na chave para ver as suas versões. Selecione uma versão chave para ver as definições

1. Copiar o valor do campo identificador-chave, que fornece o URI

    ![Screenshot Obter Key URI do Key Vault](media/enable-customer-managed-key/04-get-key-identifier.png)

1. Lançar portal Azure Data Factory, e usando a barra de navegação à esquerda, saltar para o Portal de Gestão de Fábricas de Dados

1. Clique no ícone da __chave manged cliente__

    ![Screenshot Enable Customer Managed Key in Data Factory](media/enable-customer-managed-key/05-customer-managed-key-configuration.png)

1. Introduza o URI para a chave gerida pelo cliente que copiou antes

1. Clique __em Guardar__ e a encriptação da chave manged pelo cliente está ativada para a Data Factory

## <a name="update-key-version"></a>Atualizar versão chave

Quando criar uma nova versão de uma chave, atualize a fábrica de dados para utilizar a nova versão. Siga passos semelhantes descritos na secção _Enable Customer-Managed Keys_, incluindo:

1. Localize o URI para a nova versão chave através do Portal Azure Key Vault

1. Navegue para a definição __de chave gerida pelo cliente__

1. Substitua e cole no URI para a nova chave

1. Click __Save__ and Data Factory irá agora encriptar com a nova versão chave

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para a encriptação da Data Factory, tem de atualizar manualmente as definições na Data Factory. Siga passos semelhantes descritos na secção _Enable Customer-Managed Keys_, incluindo:

1. Localize o URI para a nova chave através do Portal do Cofre da Chave Azure

1. Navegue para a definição __da chave manged do cliente__

1. Substitua e cole no URI para a nova chave

1. Click __Save__ and Data Factory irá agora encriptar com a nova chave

## <a name="disable-customer-managed-keys"></a>Desativar chaves de Customer-Managed

Por design, uma vez ativada a função-chave gerida pelo cliente, não é possível remover o passo de segurança extra. Esperamos sempre que um cliente tenha a chave para encriptar a fábrica e os dados.

## <a name="next-steps"></a>Passos seguintes

Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.