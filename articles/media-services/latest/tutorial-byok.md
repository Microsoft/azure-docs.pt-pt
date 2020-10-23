---
title: Chaves geridas pelo cliente ou traga a sua própria chave (BYOK) com serviços de mídia
description: Este é um tutorial para usar chaves geridas pelo cliente com uma conta de armazenamento de Serviços de Mídia
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c26da9d888bbcdf72c052fa4bd7fcdf443a2d8f7
ms.sourcegitcommit: ce8eecb3e966c08ae368fafb69eaeb00e76da57e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/21/2020
ms.locfileid: "92326152"
---
# <a name="tutorial-use-customer-managed-keys-or-bring-your-own-key-byok-with-media-services"></a>Tutorial: Use chaves geridas pelo cliente ou traga a sua própria chave (BYOK) com serviços de mídia

Com a API 2020-05-01, pode utilizar uma chave RSA gerida por um cliente com uma conta de Serviços de Comunicação que tenha uma identidade gerida pelo sistema.  Este tutorial inclui uma coleção e ambiente do Carteiro para enviar pedidos de REST aos serviços da Azure.  Serviços utilizados:

- Inscrição de diretório ativo Azure para Carteiro
- Microsoft Graph API
- Storage do Azure
- Azure Key Vault
- Serviços de Multimédia

Neste tutorial, aprenderá a usar o Carteiro para:

> [!div class="checklist"]
> * Obtenha fichas para uso com os serviços Azure.
> * Criar um grupo de recursos e uma conta de armazenamento.
> * Criar uma conta de Serviços de Comunicação com uma identidade gerida pelo sistema
> * Crie um Cofre-Chave para armazenar uma chave RSA (gerida pelo cliente) para utilização com a conta de armazenamento.
> * Atualize a conta de Serviços de Mídia para utilizar a chave RSA com a conta de armazenamento.
> * Use variáveis no Carteiro.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

- Registe um rescaminante de serviço com as permissões apropriadas.
- Instalar [carteiro](https://www.postman.com).
- Descarregue a coleção do Carteiro para este tutorial na [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registar um diretor de serviço com as permissões necessárias

[Criar um principal de serviço.](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)  Consulte [a Opção Dois](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal#authentication-two-options) para obter o segredo principal do serviço.  Note o segredo em algum lugar, uma vez que você deixa a página secreta no portal, será inacessível.

O diretor de serviço necessita das seguintes permissões para executar as tarefas neste tutorial.

![as permissões necessárias para o diretor de serviço](./media/tutorial-byok/service-principal-permissions-1.png)

### <a name="install-postman"></a>Instalar Carteiro

Se ainda não instalou o Carteiro para uso com o Azure, pode obtê-lo [a postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Descarregue e importe a coleção

Descarregue a coleção do Carteiro para este tutorial na [Azure Samples: media-services-customer-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok)

## <a name="installation-of-collection-and-environment"></a>Instalação de recolha e ambiente

1. O Carteiro.
1. Selecione **Import** (Importar).
1. Selecione **carregar ficheiros**.
1. Navegue para onde guardou os ficheiros de recolha e ambiente.
1. Selecione o ficheiro de recolha e ambiente.
1. Selecione **Abrir**.  (Verá um aviso de que os ficheiros não serão importados como API, mas como coleções.  Isto está bom.  É o que quer.)
1. Esta coleção vai agora aparecer nas suas Coleções como BYOK.
1. As variáveis ambientais aparecerão agora nos seus Ambientes.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Compreenda os pedidos da API rest na coleção

A coleção fornece os seguintes pedidos de API REST. Os pedidos devem ser enviados na sequência fornecida, uma vez que a maioria deles tem scripts de teste que criam dinamicamente variáveis globais para o próximo (ou subsequente) pedido na sequência. Não é necessário criar manualmente as variáveis globais.

No Carteiro, verás estas variáveis contidas dentro `{{ }}` dos parênteses.  Por exemplo, `{{bearerToken}}`.

1. Get AAD token - o teste define o portador variável *globalToken*
2. Get Graph token - o teste define o gráfico variável *globalToken*
3. Obter Detalhes Principais do Serviço - o teste define o serviço variável *globalPrincipalObjectId*
4. Criar uma conta de armazenamento - o teste define o armazenamento variável *globalAccountId*
5. Criar uma Conta de Serviços de Mídia com uma Identidade Gerida do Sistema - o teste define o *principalid* variável global
6. Criar um Cofre-Chave, permitindo o acesso ao principal de serviço - o teste define a chave variável *globalVaultId*
7. Obtenha um token Key Vault - o teste define a chave variável *globalVaultToken*
8. Crie a chave RSA no cofre-chave - o teste define *o keyId* variável global
9. Atualize a conta de Serviços de Mídia para utilizar a chave com a conta de armazenamento - não existe um script de teste para este pedido.

## <a name="define-environment-variables"></a>Definir variáveis ambientais

1. Mude para o ambiente que descarregou selecionando a lista de suspensos de ambientes.
1. Estabeleça as variáveis ambientais no Carteiro. Também são usadas como variáveis contidas dentro `{{ }}` dos parênteses.  Por exemplo, `{{tenantId}}`.

    * *tenantId* = o seu ID inquilino
    * *servicePrincipalId* = o ID do principal serviço que estabelece com o seu método favorito: portal, CLI, e assim por diante.
    * *servicePrincipalSecret* = o segredo criado para o diretor de serviço
    * *subscrição* = o seu ID de subscrição
    * *storageName* = o nome que pretende dar ao seu armazenamento
    * *nome de conta* = o nome da conta do Serviço de Mídia que pretende utilizar
    * *keyVaultName* = o nome do Cofre de Chaves que pretende utilizar
    * *recursoLocation* = central (Ou onde quer colocar os seus recursos.  Esta coleção só foi testada com central.)
    * *grupo de recursos* = o nome do grupo de recursos

    As seguintes variáveis são padrão para trabalhar com recursos Azure, por isso não há necessidade de alterá-las.

    * *armaresource* = `https://management.core.windows.net`
    * *graphResource* = `https://graph.windows.net/`
    * *keyVaultResource* = `https://vault.azure.net`
    * *armEndpoint* = `management.azure.com`
    * *graphEndpoint* = `graph.windows.net`
    * *aadEndpoint* = `login.microsoftonline.com`
    * *keyVaultDomainS suléix* = `vault.azure.net`

## <a name="send-the-requests"></a>Enviar os pedidos

Uma vez definidas as variáveis ambientais, pode executar os pedidos um de cada vez na sequência acima, ou usar o corredor do Carteiro para executar a coleção.

## <a name="change-the-key"></a>Mude a chave

Os serviços de comunicação social detetarão automaticamente quando a chave foi alterada.  Para testar isto, crie outra versão chave para a mesma tecla. Os Serviços de Comunicação Devem detetar esta chave em menos de 15 minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a usar os recursos que criou e **não quer continuar a ser cobrado,** apague-os.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo artigo para aprender a...
> [!div class="nextstepaction"]
> [Codificar um ficheiro remoto com base no URL e transmitir o vídeo com REST](stream-files-tutorial-with-rest.md)
