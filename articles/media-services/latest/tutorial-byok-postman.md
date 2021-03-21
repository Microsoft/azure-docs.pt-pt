---
title: Utilize chaves geridas pelo cliente ou BYOK REST API
description: Neste tutorial, utilize chaves geridas pelo cliente ou traga a sua própria chave (BYOK) com uma conta de armazenamento Azure Media Services.
author: IngridAtMicrosoft
ms.author: inhenkel
ms.service: media-services
ms.topic: tutorial
ms.date: 10/18/2020
ms.openlocfilehash: c8a5b682e2ac4879d2181bdb069cf554bad512d9
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "96498290"
---
# <a name="tutorial-use-customer-managed-keys-or-byok-with-media-services-rest-api"></a>Tutorial: Utilize chaves geridas pelo cliente ou BYOK com Serviços de Mídia REST API

Com a API 2020-05-01, pode utilizar uma chave RSA gerida pelo cliente com uma conta Azure Media Services que tem uma identidade gerida pelo sistema. Este tutorial inclui uma coleção e ambiente do Carteiro para enviar pedidos de REST aos serviços da Azure. Os serviços utilizados são:

- Registo de candidatura de Azure Ative (Azure AD) para Carteiro
- Microsoft Graph API
- Armazenamento do Azure
- Azure Key Vault
- Serviços de Multimédia do Azure

Neste tutorial, aprenderá a usar o Carteiro para:

> [!div class="checklist"]
> - Obtenha fichas para uso com os serviços Azure.
> - Criar um grupo de recursos e uma conta de armazenamento.
> - Criar uma conta de Serviços de Comunicação com uma identidade gerida pelo sistema.
> - Crie um cofre chave para armazenar uma chave RSA gerida pelo cliente.
> - Atualize a conta de Serviços de Mídia para utilizar a chave RSA com a conta de armazenamento.
> - Use variáveis no Carteiro.

Se não tiver uma subscrição do Azure, [crie uma conta de teste gratuita](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Pré-requisitos

1. Registe um rescaminante de serviço com as permissões apropriadas.
1. Instalar [carteiro](https://www.postman.com).
1. Descarregue a coleção do Carteiro para este tutorial na [Azure Samples: media-services-services-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

### <a name="register-a-service-principal-with-the-needed-permissions"></a>Registar um diretor de serviço com as permissões necessárias

1. [Criar um principal de serviço.](../../active-directory/develop/howto-create-service-principal-portal.md)
1. Ir para a [Opção 2: Criar um novo segredo de aplicação](../../active-directory/develop/howto-create-service-principal-portal.md#authentication-two-options) para obter o segredo principal do serviço.

   > [!IMPORTANT]
   >Copie e guarde o segredo para utilização posterior. Não pode aceder ao segredo depois de deixar a página secreta no portal.

1. Atribuir permissões ao principal de serviço, como mostra a seguinte imagem:

   :::image type="complex" source="./media/tutorial-byok/service-principal-permissions-1.png" alt-text="Screenshot mostrando as permissões necessárias para o diretor de serviço.":::
   As permissões são listadas por serviço, nome de permissão, tipo e, em seguida, descrição. Azure Key Vault: imitação do utilizador, delegado, acesso total ao Cofre da Chave Azure. Azure Service Management: personificação do utilizador, delegada, acesso Azure Service Management como utilizador da organização. Azure Storage: personificação do utilizador, delegado, acesso Azure Storage. Serviços de comunicação: personificação do utilizador, delegado, serviços de comunicação de acesso. Microsoft Graph: user.read, delediled, iniciar sessão e ler o perfil do utilizador.
   :::image-end:::

### <a name="install-postman"></a>Instalar Carteiro

Se ainda não instalou o Carteiro para uso com o Azure, pode obtê-lo [a postman.com](https://www.postman.com/).

### <a name="download-and-import-the-collection"></a>Descarregue e importe a coleção

Descarregue a coleção do Carteiro para este tutorial na [Azure Samples: media-services-services-managed-keys-byok](https://github.com/Azure-Samples/media-services-customer-managed-keys-byok).

## <a name="install-the-postman-collection-and-environment"></a>Instale a coleção e o ambiente do Carteiro

1. O Carteiro.
1. Selecione **Import** (Importar).
1. Selecione **carregar ficheiros**.
1. Vá para onde guardou os ficheiros de recolha e ambiente.
1. Selecione os ficheiros de recolha e ambiente.
1. Selecione **Abrir**. Um aviso parece dizer que os ficheiros não serão importados como API, mas como coleções. Este aviso está bom. É o que quer.

A coleção mostra agora nas suas coleções como BYOK. Além disso, as variáveis ambientais aparecem nos seus ambientes.

### <a name="understand-the-rest-api-requests-in-the-collection"></a>Compreenda os pedidos da API rest na coleção

A coleção fornece os seguintes pedidos de API REST.

> [!NOTE]
>
>- Os pedidos devem ser enviados na sequência fornecida.
>- A maioria dos pedidos tem scripts de teste que criam variáveis globais para o próximo pedido na sequência.
>- Não é preciso criar variáveis globais manualmente.

No Carteiro, verás estas variáveis contidas dentro dos parênteses. Por exemplo, `{{bearerToken}}`.

1. Obtenha um token AD Azure: O teste define o portador variável **globalToken**.
2. Obtenha um token Microsoft Graph: O teste define o gráfico variável **globalToken**.
3. Obtenha os principais detalhes do serviço: O teste define o serviço variável **globalPrincipalObjectId**.
4. Criar uma conta de armazenamento: O teste define o armazenamento variável **globalAccountId**.
5. Criar uma conta de Serviços de Mídia com uma identidade gerida pelo sistema: O teste define o **principal variável** global .
6. Criar um cofre-chave para garantir o acesso ao principal de serviço: O teste define a chave variável **globalVaultId**.
7. Obtenha um token Key Vault: O teste define a chave variável **globalVaultToken**.
8. Criar a chave RSA no cofre da chave: O teste define o **teclado** variável global .
9. Atualize a conta dos Serviços de Comunicação para utilizar a chave com a conta de armazenamento: Não existe um script de teste para este pedido.

## <a name="define-environment-variables"></a>Definir variáveis ambientais

1. Selecione a lista de drop-down do ambiente para mudar para o ambiente que descarregou.
1. Estabeleça as variáveis ambientais no Carteiro. Também são usadas como variáveis contidas dentro dos parênteses. Por exemplo, `{{tenantId}}`.

    - **tenantId**: A sua identificação de inquilino.
    - **serviçoPrincipalId**: O ID do principal serviço que estabelece com o seu método favorito, como portal ou CLI.
    - **servicePrincipalSecret**: O segredo criado para o diretor de serviço.
    - **subscrição**: O seu ID de subscrição.
    - **storageName**: O nome que pretende dar ao seu armazenamento.
    - **nome da conta**: O nome da conta dos Serviços de Comunicação Social que pretende utilizar.
    - **keyVaultName**: O nome do cofre de chaves que deseja utilizar.
    - **utilização de recursos**: A localização **CentralUs** ou onde pretende colocar os seus recursos. Esta coleção só foi testada com **a CentralUs.**
    - **Grupo de recursos**: O nome do grupo de recursos.

    As seguintes variáveis são padrão para trabalhar com recursos Azure. Então, não há necessidade de mudá-los.

    - **fonte de braço:**`https://management.core.windows.net`
    - **gráficoResource:**`https://graph.windows.net/`
    - **keyVaultResource:**`https://vault.azure.net`
    - **ponto de apoio:**`management.azure.com`
    - **graphEndpoint:**`graph.windows.net`
    - **aadEndpoint:**`login.microsoftonline.com`
    - **keyVaultDomainS sulé:**`vault.azure.net`

## <a name="send-the-requests"></a>Enviar os pedidos

Depois de definir as variáveis ambientais, pode executar os pedidos um de cada vez na sequência anterior. Ou podes usar o corredor do Carteiro para gerir a coleção.

## <a name="change-the-key"></a>Mude a chave

Os Serviços de Comunicação Social detetam automaticamente quando a chave é alterada. Crie outra versão chave para a mesma chave para testar este processo. Os Serviços de Comunicação Devem detetar a chave em menos de 15 minutos.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não vai continuar a usar os recursos que criou e *não quer continuar a ser cobrado,* apague-os.

## <a name="next-steps"></a>Passos seguintes

Vá ao próximo artigo para aprender a:
> [!div class="nextstepaction"]
> [Codificar um ficheiro remoto com base no URL e transmitir o vídeo com REST](stream-files-tutorial-with-rest.md)