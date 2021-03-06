---
title: Encrypt Azure Data Factory com chave gerida pelo cliente
description: Aumentar a segurança da Data Factory com a sua própria chave (BYOK)
author: chez-charlie
ms.service: data-factory
ms.topic: quickstart
ms.date: 05/08/2020
ms.author: chez
ms.reviewer: mariozi
ms.openlocfilehash: a18d06e3a0324889a4cb9936fb339fd9d8f9b816
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/02/2021
ms.locfileid: "106222704"
---
# <a name="encrypt-azure-data-factory-with-customer-managed-keys"></a>Encrypt Azure Data Factory com chaves geridas pelo cliente

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

A Azure Data Factory encripta dados em repouso, incluindo definições de entidades e quaisquer dados em cache enquanto as execuções estão em andamento. Por padrão, os dados são encriptados com uma chave gerida aleatoriamente pela Microsoft que é atribuída exclusivamente à sua fábrica de dados. Para obter garantias de segurança extra, pode agora ativar Bring Your Own Key (BYOK) com a funcionalidade de chaves geridas pelo cliente na Azure Data Factory. Quando especifica uma chave gerida pelo cliente, a Data Factory utiliza __a__ chave do sistema de fábrica e a CMK para encriptar os dados do cliente. Faltar qualquer um dos dois resultaria em Negação de Acesso a dados e fábrica.

O Azure Key Vault é necessário para armazenar chaves geridas pelo cliente. Pode criar as suas próprias chaves e armazená-las num cofre de chaves, ou pode usar as APIs do Cofre de Chaves Azure para gerar chaves. O cofre-chave e a Fábrica de Dados devem estar no mesmo inquilino do Azure Ative Directory (Azure AD) e na mesma região, mas podem estar em subscrições diferentes. Para mais informações sobre o Azure Key Vault, veja [o que é o Cofre da Chave Azure?](../key-vault/general/overview.md)

## <a name="about-customer-managed-keys"></a>Sobre chaves geridas pelo cliente

O seguinte diagrama mostra como a Data Factory utiliza o Azure Ative Directory e o Azure Key Vault para fazer pedidos utilizando a chave gerida pelo cliente:

  :::image type="content" source="media/enable-customer-managed-key/encryption-customer-managed-keys-diagram.png" alt-text="Diagrama mostrando como as chaves geridas pelo cliente funcionam na Azure Data Factory.":::

A lista seguinte explica os passos numerados no diagrama:

1. Um administrador do Azure Key Vault concede permissões a chaves de encriptação da identidade gerida associada à Fábrica de Dados
1. Um administrador da Data Factory permite a funcionalidade chave gerida pelo cliente na fábrica
1. A Data Factory utiliza a identidade gerida que está associada à fábrica para autenticar o acesso ao Cofre de Chaves Azure via Azure Ative Directory
1. Data Factory envolve a chave de encriptação da fábrica com a chave do cliente no Cofre da Chave Azure
1. Para operações de leitura/escrita, a Data Factory envia pedidos ao Cofre da Chave Azure para desembrulhar a chave de encriptação da conta para realizar operações de encriptação e desencriptação

Existem duas formas de adicionar encriptação da Chave Gerida pelo Cliente às fábricas de dados. Um é durante o tempo de criação de fábrica no portal Azure, e o outro é a criação pós-fábrica, na UI da Fábrica de Dados.

## <a name="prerequisites---configure-azure-key-vault-and-generate-keys"></a>Pré-requisitos - configurar cofre de chaves Azure e gerar chaves

### <a name="enable-soft-delete-and-do-not-purge-on-azure-key-vault"></a>Ativar a eliminação suave e não purgar no cofre da chave Azure

A utilização de chaves geridas pelo cliente com a Data Factory requer duas propriedades a serem definidas no Cofre da Chave, __Eliminação Suave__ e __Não Purga__. Estas propriedades podem ser ativadas utilizando o PowerShell ou o Azure CLI num cofre de chaves novo ou existente. Para aprender como ativar estas propriedades num cofre de chaves existente, consulte [a gestão de recuperação do Cofre da Chave Azure com proteção de eliminação e purga suave](../key-vault/general/key-vault-recovery.md)

Se estiver a criar um novo Cofre de Chaves Azure através do portal Azure, __o Soft Delete__ e o Não __Purga__ pode ser ativado da seguinte forma:

  :::image type="content" source="media/enable-customer-managed-key/01-enable-purge-protection.png" alt-text="Screenshot mostrando como permitir a proteção de eliminação e purga suave após a criação do Key Vault.":::

### <a name="grant-data-factory-access-to-azure-key-vault"></a>Grant Data Factory acesso ao Cofre de Chaves Azure

Certifique-se de que a Azure Key Vault e a Azure Data Factory estão no mesmo inquilino do Azure Ative Directory (Azure AD) e na _mesma região._ A partir do controlo de acesso a Azure Key Vault, conceda a fábrica de dados seguintes permissões: _Obter,_ _Desembrulhar a Chave_ e _a Chave de Embrulho_. Estas permissões são necessárias para permitir chaves geridas pelo cliente na Data Factory.

* Se pretender adicionar encriptação de chave gerida pelo cliente [após a criação da fábrica na Data Factory UI,](#post-factory-creation-in-data-factory-ui)certifique-se de que a identidade de serviço gerida da data factory (MSI) tem as três permissões para Key Vault
* Se pretender adicionar encriptação de chave gerida pelo cliente durante o [tempo de criação da fábrica no portal Azure,](#during-factory-creation-in-azure-portal)certifique-se de que a identidade gerida atribuída pelo utilizador (UA-MI) tem as três permissões para Key Vault

  :::image type="content" source="media/enable-customer-managed-key/02-access-policy-factory-managed-identities.png" alt-text="Screenshot mostrando como ativar o acesso da fábrica de dados ao cofre de chaves.":::

### <a name="generate-or-upload-customer-managed-key-to-azure-key-vault"></a>Gerar ou carregar a chave gerida pelo cliente para o Cofre da Chave Azure

Podes criar as tuas próprias chaves e guardá-las num cofre. Ou pode usar as APIs do Cofre da Chave Azure para gerar chaves. Apenas as teclas RSA de 2048 são suportadas com encriptação data Factory. Para mais informações, consulte [sobre chaves, segredos e certificados.](../key-vault/general/about-keys-secrets-certificates.md)

  :::image type="content" source="media/enable-customer-managed-key/03-create-key.png" alt-text="Screenshot mostrando como gerar Customer-Managed Chave.":::

## <a name="enable-customer-managed-keys"></a>Ativar chaves geridas pelo cliente

### <a name="post-factory-creation-in-data-factory-ui"></a>Criação de fábrica de pós-fábrica na UI da Fábrica de Dados

Esta secção percorre o processo para adicionar encriptação de chave gerida pelo cliente na UI da Data Factory, _após a_ criação da fábrica.

> [!NOTE]
> Uma chave gerida pelo cliente só pode ser configurada numa fábrica de dados vazia. A fábrica de dados não pode conter quaisquer recursos, tais como serviços ligados, oleodutos e fluxos de dados. Recomenda-se que a chave gerida pelo cliente seja gerida pelo cliente logo após a criação da fábrica.

> [!IMPORTANT]
> Esta abordagem não funciona com fábricas geridas por rede virtual. Por favor, considere a [rota alternativa,](#during-factory-creation-in-azure-portal)se quiser encriptar tais fábricas.

1. Certifique-se de que a identidade de serviço gerido da fábrica de dados (MSI) tem permissões _de Obter,_ _Desembrulhar a Chave_ e Embrulhar a _Chave_ para o Cofre de Chaves.

1. Certifique-se de que a Fábrica de Dados está vazia. A fábrica de dados não pode conter quaisquer recursos, tais como serviços ligados, oleodutos e fluxos de dados. Por enquanto, a utilização da chave gerida pelo cliente para uma fábrica não vazia resultará num erro.

1. Para localizar a chave URI no portal Azure, navegue até ao Cofre da Chave Azure e selecione a definição de Chaves. Selecione a tecla procurada e, em seguida, selecione a chave para visualizar as suas versões. Selecione uma versão chave para ver as definições

1. Copie o valor do campo key identifier, que fornece o Screenshot URI :::image type="content" source="media/enable-customer-managed-key/04-get-key-identifier.png" alt-text="de obter URI chave do Key Vault.":::

1. Lançar portal Azure Data Factory, e usando a barra de navegação à esquerda, saltar para o Portal de Gestão de Fábricas de Dados

1. Clique no ícone da __chave manged cliente__ :::image type="content" source="media/enable-customer-managed-key/05-customer-managed-key-configuration.png" alt-text="Screenshot como ativar a chave gerida pelo cliente na UI da Fábrica de Dados.":::

1. Introduza o URI para a chave gerida pelo cliente que copiou antes

1. Clique __em Guardar__ e a encriptação da chave manged pelo cliente está ativada para a Data Factory

### <a name="during-factory-creation-in-azure-portal"></a>Durante a criação de fábrica no portal Azure

Esta secção percorre etapas para adicionar encriptação de chave gerida pelo cliente no portal Azure, _durante_ a implantação da fábrica.

Para encriptar a fábrica, a Data Factory precisa primeiro de recuperar a chave gerida pelo cliente a partir do Key Vault. Uma vez que a implantação da fábrica ainda está em curso, a Identidade de Serviço Gerido (MSI) ainda não está disponível para autenticar com o Key Vault. Como tal, para utilizar esta abordagem, o cliente precisa de atribuir uma identidade gerida (UA-MI) atribuída ao grupo de dados. Assumimos os papéis definidos no UA-MI e autenticaremos com o Key Vault.

Para saber mais sobre a identidade gerida atribuída pelo utilizador, consulte os tipos de [identidade geridos](../active-directory/managed-identities-azure-resources/overview.md#managed-identity-types) e [a atribuição de funções para a identidade gerida atribuída pelo utilizador.](../active-directory/managed-identities-azure-resources/how-to-manage-ua-identity-portal.md)

1. Certifique-se de que a identidade gerida atribuída pelo utilizador (UA-MI) tem permissões _de chave_ de _desembrulhamento_ e _de embrulho_ para o Cofre de Chaves

1. Em __separador Advanced,__ verifique a caixa para ativar a _encriptação utilizando uma chave gerida pelo cliente_ Screenshot do 
   :::image type="content" source="media/enable-customer-managed-key/06-user-assigned-managed-identity.png" alt-text="separador Advanced para a experiência de criação de fábrica de dados no portal Azure.":::

1. Forneça o url para a chave gerida pelo cliente armazenada no Cofre de Chaves

1. Selecione um utilizador apropriado de identidade gerida atribuída para autenticar com Cofre de Chaves

1. Continue com a implantação da fábrica

## <a name="update-key-version"></a>Atualizar versão chave

Quando criar uma nova versão de uma chave, atualize a fábrica de dados para utilizar a nova versão. Siga passos semelhantes descritos na secção [Data Factory UI,](#post-factory-creation-in-data-factory-ui)incluindo:

1. Localize o URI para a nova versão chave através do Portal Azure Key Vault

1. Navegue para a definição __de chave gerida pelo cliente__

1. Substitua e cole no URI para a nova chave

1. Click __Save__ and Data Factory irá agora encriptar com a nova versão chave

## <a name="use-a-different-key"></a>Use uma chave diferente

Para alterar a chave utilizada para a encriptação da Data Factory, tem de atualizar manualmente as definições na Data Factory. Siga passos semelhantes descritos na secção [Data Factory UI,](#post-factory-creation-in-data-factory-ui)incluindo:

1. Localize o URI para a nova chave através do Portal do Cofre da Chave Azure

1. Navegue para a definição __da chave manged do cliente__

1. Substitua e cole no URI para a nova chave

1. Click __Save__ and Data Factory irá agora encriptar com a nova chave

## <a name="disable-customer-managed-keys"></a>Desativar as chaves geridas pelo cliente

Por design, uma vez ativada a função-chave gerida pelo cliente, não é possível remover o passo de segurança extra. Esperamos sempre que um cliente tenha a chave para encriptar a fábrica e os dados.

## <a name="customer-managed-key-and-continuous-integration-and-continuous-deployment"></a>Chave gerida pelo cliente e integração contínua e implementação contínua

Por predefinição, a configuração CMK não está incluída no modelo de gestor de recursos Azure (ARM) de fábrica. Para incluir as definições de encriptação geridas pelo cliente no modelo ARM para integração contínua (CI/CD):

1. Certifique-se de que a fábrica está no modo Git
1. Navegue para o portal de gestão - secção chave gerida pelo cliente
1. Verifique Incluir na opção _de modelo ARM_

  :::image type="content" source="media/enable-customer-managed-key/07-include-in-template.png" alt-text="Screenshot de incluir a definição de chave gerida pelo cliente no modelo ARM.":::

As seguintes definições serão adicionadas no modelo ARM. Estas propriedades podem ser parametrizadas em gasodutos de integração contínua e entrega, editando a configuração de [parâmetros do Gestor de Recursos Azure](continuous-integration-deployment.md#use-custom-parameters-with-the-resource-manager-template)

  :::image type="content" source="media/enable-customer-managed-key/08-template-with-customer-managed-key.png" alt-text="Screenshot de incluir a definição de chave gerida pelo cliente no modelo Azure Resource Manager.":::

> [!NOTE]
> A adição da definição de encriptação aos modelos ARM adiciona uma definição de nível de fábrica que irá sobrepor-se a outras definições de nível de fábrica, tais como configurações de git, em outros ambientes. Se tiver estas definições ativadas num ambiente elevado como UAT ou PROD, consulte os [Parâmetros Globais em CI/CD](author-global-parameters.md#cicd).

## <a name="next-steps"></a>Passos seguintes

Leia os [tutoriais](tutorial-copy-data-dot-net.md) para saber como utilizar o Data Factory em mais cenários.
