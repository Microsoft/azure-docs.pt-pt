---
title: Utilize chaves geridas pelo cliente para encriptar os seus dados de configuração
description: Criptografe os seus dados de configuração utilizando chaves geridas pelo cliente
author: AlexandraKemperMS
ms.author: alkemper
ms.date: 07/28/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 46a1e99a7bc75efa85fcb5eff649c14af5abd17b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "96930505"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Utilize chaves geridas pelo cliente para encriptar os dados de Configuração da Aplicação
A Azure App Configuration [encripta informações sensíveis em repouso](../security/fundamentals/encryption-atrest.md). A utilização de chaves geridas pelo cliente proporciona uma melhor proteção de dados, permitindo-lhe gerir as suas chaves de encriptação.  Quando a encriptação de chave gerida é utilizada, todas as informações sensíveis na Configuração da Aplicação são encriptadas com uma chave Azure Key Vault fornecida pelo utilizador.  Isto proporciona a capacidade de rodar a chave de encriptação a pedido.  Também fornece a capacidade de revogar o acesso da Azure App Configuration a informações confidenciais, revogando o acesso da instância de Configuração da Aplicação à chave.

## <a name="overview"></a>Descrição Geral 
A Azure App Configuration encripta informações sensíveis em repouso utilizando uma chave de encriptação AES de 256 bits fornecida pela Microsoft. Cada instância de Configuração de Aplicações tem a sua própria chave de encriptação gerida pelo serviço e usada para encriptar informações confidenciais. Informações sensíveis incluem os valores encontrados em pares de valor-chave.  Quando a capacidade de chave gerida pelo cliente está ativada, a Configuração de Aplicações utiliza uma identidade gerida atribuída à instância de Configuração da Aplicação para autenticar com o Azure Ative Directory. A identidade gerida chama então Azure Key Vault e envolve a chave de encriptação da configuração da aplicação. A chave de encriptação embrulhada é então armazenada e a chave de encriptação desembrulhada fica em cache na Configuração da Aplicação durante uma hora. A Configuração da Aplicação atualiza a versão desembrulhada da chave de encriptação da instância de configuração da aplicação de aplicação de hora a hora. Isto garante a disponibilidade em condições normais de funcionamento. 

>[!IMPORTANT]
> Se a identidade atribuída à instância de Configuração da Aplicação já não estiver autorizada a desembrulhar a chave de encriptação da instância, ou se a chave gerida for permanentemente eliminada, deixará de ser possível desencriptar informações sensíveis armazenadas na instância de Configuração da Aplicação. A utilização da função [soft delete](../key-vault/general/soft-delete-overview.md) do Azure Key Vault atenua a possibilidade de eliminar acidentalmente a sua chave de encriptação.

Quando os utilizadores ativam a capacidade de chave gerida pelo cliente na sua instância de Configuração de Aplicação Azure, controlam a capacidade do serviço de aceder às suas informações sensíveis. A chave gerida serve como uma chave de encriptação de raiz. Um utilizador pode revogar o acesso da sua instância de Configuração de Aplicações à sua chave gerida alterando a sua política de acesso ao cofre chave. Quando este acesso for revogado, a Configuração da Aplicação perderá a capacidade de desencriptar dados do utilizador dentro de uma hora. Neste momento, a instância de Configuração da Aplicação proibirá todas as tentativas de acesso. Esta situação é recuperável, concedendo mais uma vez o acesso do serviço à chave gerida.  Dentro de uma hora, a Configuração da Aplicação será capaz de desencriptar os dados dos utilizadores e operar em condições normais.

>[!NOTE]
>Todos os dados de Configuração de Aplicações Azure são armazenados até 24 horas numa cópia de segurança isolada. Isto inclui a chave de encriptação desembrulhada. Estes dados não estão imediatamente disponíveis para o serviço ou equipa de serviço. Em caso de restauro de emergência, a Configuração da Aplicação Azure revogue-se-á a partir dos dados-chave geridos.

## <a name="requirements"></a>Requisitos
São necessários os seguintes componentes para ativar com sucesso a capacidade de chave gerida pelo cliente para a Configuração da Aplicação Azure:
- Instância de configuração de aplicativo de nível padrão Azure
- Cofre de chaves Azure com funcionalidades de proteção de eliminação suave e proteção de purga ativadas
- Uma chave RSA ou RSA-HSM dentro do Cofre de Chaves
    - A chave não deve ser expirada, deve ser ativada, e deve ter capacidades de embrulho e desembrulhar

Uma vez configurados estes recursos, restam dois passos para permitir que a Configuração da Aplicação Azure utilize a chave do Cofre de Chaves:
1. Atribuir uma identidade gerida à instância de Configuração da Aplicação Azure
2. Conceda a identidade `GET` `WRAP` e `UNWRAP` permissões na política de acesso do Key Vault.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Ativar a encriptação de chaves gerida pelo cliente para a sua instância de Configuração de Aplicação Azure
Para começar, necessitará de uma configuração de configuração de aplicação Azure devidamente configurada. Se ainda não tiver uma instância de Configuração de Aplicação disponível, siga um destes quickstarts para configurar um:
- [Crie uma aplicação core ASP.NET com configuração de aplicação Azure](quickstart-aspnet-core-app.md)
- [Criar uma aplicação .NET Core com Configuração de Aplicações Azure](quickstart-dotnet-core-app.md)
- [Criar uma aplicação .NET Framework com Configuração de Aplicações Azure](quickstart-dotnet-app.md)
- [Criar uma aplicação Java Spring com Configuração de Aplicação Azure](quickstart-java-spring-app.md)

>[!TIP]
> O Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções da linha de comando neste artigo.  Tem ferramentas Azure comuns pré-instaladas, incluindo o .NET Core SDK. Se tiver iniciado sessão na sua subscrição Azure, lance a sua [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell [lendo a nossa documentação](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Criar e configurar um Cofre de Chaves Azure
1. Crie um cofre de chaves Azure utilizando o Azure CLI.  Note que ambos `vault-name` são fornecidos pelo utilizador e devem ser `resource-group-name` únicos.  Utilizamos `contoso-vault` e `contoso-resource-group` nestes exemplos.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Ativar a proteção contra a eliminação e a purga suaves para o cofre da chave. Substitua os nomes do Key Vault ( `contoso-vault` ) e do Grupo de Recursos ( ) `contoso-resource-group` criados no passo 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Crie uma chave key vault. Forneça um exclusivo `key-name` para esta chave e substitua os nomes do Cofre de Chaves ( ) criado no passo `contoso-vault` 1. Especifique se prefere `RSA` ou `RSA-HSM` encriptação.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    A saída deste comando mostra o ID chave ("kid") para a chave gerada.  Tome nota da chave ID para usar mais tarde neste exercício.  O ID chave tem o formulário: `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}` .  O ID chave tem três componentes importantes:
    1. Key Vault URI: 'https://{my key vault}.vault.azure.net
    1. Nome chave do cofre da chave: {Nome chave}
    1. Versão chave do Cofre chave: {Versão chave}

1. Crie um sistema atribuído à identidade gerida utilizando o CLI Azure, substituindo o nome da sua instância de Configuração de Aplicações e grupo de recursos utilizados nos passos anteriores. A identidade gerida será usada para aceder à chave gerida. Usamos `contoso-app-config` para ilustrar o nome de uma instância de configuração de aplicação:
    
    ```azurecli
    az appconfig identity assign --name contoso-app-config --resource-group contoso-resource-group --identities [system]
    ```
    
    A saída deste comando inclui o ID principal ("principalId") e o ID do inquilino ("tenandId") do sistema atribuído à identidade.  Estes IDs serão usados para conceder o acesso de identidade à chave gerida.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. A identidade gerida da instância de Configuração da Aplicação Azure necessita de acesso à chave para realizar a validação, encriptação e desencriptação da chave. O conjunto específico de ações a que necessita de acesso inclui: `GET` , e para as `WRAP` `UNWRAP` chaves.  A concessão do acesso requer a identificação principal da identidade gerida da instância de Configuração da Aplicação. Este valor foi obtido no escalão anterior. É mostrado abaixo como `contoso-principalId` . Conceder permissão à chave gerida utilizando a linha de comando:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Uma vez que a instância de Configuração da Aplicação Azure possa aceder à chave gerida, podemos ativar a capacidade de chave gerida pelo cliente no serviço usando o Azure CLI. Recorde as seguintes propriedades registadas durante os principais passos de criação: `key name` `key vault URI` .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

A sua instância de configuração da aplicação Azure está agora configurada para utilizar uma chave gerida pelo cliente armazenada no Cofre da Chave Azure.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, configuraste a tua instância de Configuração de Aplicação Azure para usar uma chave gerida pelo cliente para encriptação.  Saiba como [integrar o seu serviço com identidades geridas Azure.](howto-integrate-azure-managed-service-identity.md)
