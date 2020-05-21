---
title: Encrypt Azure Data Factory com chave gerida pelo cliente
description: Melhore a segurança da fábrica de dados com a Bring Your Own Key (BYOK)
services: data-factory
documentationcenter: ''
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: fa1ce8516223b725c1efcb7e27d4726bbadfe62e
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/19/2020
ms.locfileid: "83655046"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Encrypt Azure Data Factory com chaves geridas pelo cliente

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory encripta dados em repouso, incluindo definições de entidades, quaisquer dados em cache durante as execuções estão em andamento, e dados cached para Data Preview. Por padrão, os dados são encriptados com uma chave criada aleatoriamente pela Microsoft que é exclusivamente atribuída à sua fábrica de dados. Para garantias de segurança adicionais, pode agora ativar a Sua Própria Chave (BYOK) com a funcionalidade de chaves gerida pelo cliente na Azure Data Factory. Quando especifica uma chave gerida pelo cliente, a Data Factory utiliza tanto a chave do sistema de fábrica __como__ a CMK para encriptar os dados dos clientes. Faltar qualquer um dos dois resultaria na Negação do Acesso aos dados e à fábrica.

O Cofre de Chaves Azure é necessário para armazenar chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O cofre chave e a Fábrica de Dados devem estar no mesmo inquilino azure Ative Directory (Azure AD) e na mesma região, mas podem estar em diferentes subscrições. Para mais informações sobre o Cofre de Chaves Azure, veja [o que é o Cofre chave Azure?](../key-vault/general/overview.md)

> [!NOTE]
> Uma chave gerida pelo cliente só pode ser configurada numa Fábrica de dados vazios. A fábrica de dados não pode conter recursos como serviços ligados, oleodutos e fluxos de dados. Recomenda-se que permita a chave gerida pelo cliente logo após a criação da fábrica.

## <a name="about-customer-managed-keys"></a>Sobre chaves geridas pelo cliente

O diagrama seguinte mostra como a Data Factory utiliza o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

  ![Diagrama mostrando como as chaves geridas pelo cliente funcionam na Fábrica de Dados Azure](media/quickstart-enable-customer-managed-key/encryption-customer-managed-keys-diagram.png)

A lista que se segue explica os passos numerados no diagrama:

1. Um administrador da Azure Key Vault concede permissões para encriptar chaves da identidade gerida que está associada à Fábrica de Dados
1. Um administrador da Data Factory permite a funcionalidade chave gerida pelo cliente na fábrica
1. Data Factory usa a identidade gerida que está associada à fábrica para autenticar o acesso ao Cofre chave Azure via Diretório Ativo Azure
1. Data Factory embrulha a chave de encriptação da fábrica com a chave do cliente no Cofre de Chaves Azure
1. Para operações de leitura/escrita, data Factory envia pedidos para Azure Key Vault para desembrulhar a chave de encriptação da conta para realizar operações de encriptação e desencriptação

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Pré-requisitos - configure o Cofre chave Azure e gere chaves

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Ativar soft delete e não purgar no cofre da chave azure

A utilização de chaves geridas pelo cliente com data factory requer duas propriedades a definir no Cofre de Chaves, __Soft Delete__ e __Não Purgar__. Estas propriedades podem ser ativadas utilizando powerShell ou Azure CLI num cofre de chaves novo ou existente. Para aprender a ativar estas propriedades num cofre chave existente, consulte as secções intituladas _Enableing soft-delete_ e _Enableing Purge Protection_ num dos seguintes artigos:

- [Como utilizar soft-delete com powerShell](../key-vault/general/soft-delete-powershell.md)
- [Como utilizar soft-delete com CLI](../key-vault/general/soft-delete-cli.md)

Se estiver a criar um novo Cofre chave Azure através do portal Azure, __o Soft Delete__ e o Não __Purga__ podem ser ativados da seguinte forma:

  ![Screenshot Enable Soft Delete and Purge Protection após a criação de cofre chave](media/quickstart-enable-customer-managed-key/01-enable-purge-protection.png)

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Grant Data Factory acesso ao Cofre chave Azure

Certifique-se de que o Azure Key Vault e a Azure Data Factory estão no mesmo inquilino azure Ative Directory (Azure AD) e na _mesma região._ A partir do controlo de acesso ao Cofre de Chaves Azure, conceder a identidade de serviço gerida (MSI) da fábrica de dados seguindo permissões: _Obter,_ _Chave de desembrulhar_e _Chave de Embrulho_. Estas permissões são necessárias para ativar as chaves geridas pelo cliente na Data Factory.

  ![Screenshot ativar acesso de fábrica de dados ao cofre chave](media/quickstart-enable-customer-managed-key/02-access-policy-factory-msi.png)

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Gere ou carregue a chave gerida pelo cliente para o Cofre chave Azure

Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. Apenas as teclas RSA de 2048 bits são suportadas com encriptação data Factory. Para mais informações, consulte [chaves, segredos e certificados.](../key-vault/general/about-keys-secrets-certificates.md)

  ![Screenshot Gerachave gerida pelo cliente](media/quickstart-enable-customer-managed-key/03-create-key.png)

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

1. Certifique-se de que a Fábrica de Dados está vazia. A fábrica de dados não pode conter recursos como serviços ligados, oleodutos e fluxos de dados. Por enquanto, a implantação da chave gerida pelo cliente para uma fábrica não vazia resultará num erro.

1. Para localizar a chave URI no portal Azure, navegue até ao Cofre de Chaves Azure e selecione a definição de Teclas. Selecione a tecla procurada e, em seguida, clique na tecla para visualizar as suas versões. Selecione uma versão chave para visualizar as definições

1. Copiar o valor do campo chave identificador, que fornece o URI

    ![Screenshot Obter chave URI do cofre chave](media/quickstart-enable-customer-managed-key/04-get-key-uri.png)

1. Lançar portal Azure Data Factory, e usando a barra de navegação à esquerda, salte para data Factory Home Page

1. Clique no ícone __da chave manhosa do Cliente__

    ![Screenshot ativar chave gerida pelo cliente na fábrica de dados](media/quickstart-enable-customer-managed-key/05-cmk-configuration.png)

1. Introduza o URI para a chave gerida pelo cliente que copiou antes

1. Clique em __Guardar__ e a encriptação da chave com o homem do cliente está ativada para data factory

## <a name="update-key-version"></a>Versão de chave de atualização

Quando criar uma nova versão de uma chave, atualize a fábrica de dados para utilizar a nova versão. Siga passos semelhantes, conforme descrito na secção _Ativar chaves geridas pelo cliente,_ incluindo:

1. Localize o URI para a nova versão chave através do Portal do Cofre de Chaves Azure

1. Navegue para a definição __de chave gerida pelo Cliente__

1. Substitua e cola no URI para a nova chave

1. Clique em __Guardar__ e Data Factory irá agora encriptar com a nova versão chave

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para encriptação data Factory, tem de atualizar manualmente as definições na Data Factory. Siga passos semelhantes, conforme descrito na secção _Ativar chaves geridas pelo cliente,_ incluindo:

1. Localize o URI para a nova chave através do Portal do Cofre de Chaves Azure

1. Navegue para a definição de __chave manhosa do Cliente__

1. Substitua e cola no URI para a nova chave

1. Clique em __Guardar__ e Data Factory irá agora encriptar com a nova chave

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Por design, uma vez ativada a função chave gerida pelo cliente, não pode remover o passo de segurança extra. Esperamos sempre que um cliente forneceu a chave para encriptar a fábrica e os dados.

## <a name="next-steps"></a>Próximos passos

Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
