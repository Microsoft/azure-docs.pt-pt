---
title: Utilize chaves geridas pelo cliente para encriptar os seus dados de configuração
description: Criptografe os seus dados de configuração usando chaves geridas pelo cliente
author: lisaguthrie
ms.author: lcozzens
ms.date: 02/18/2020
ms.topic: conceptual
ms.service: azure-app-configuration
ms.openlocfilehash: 5749b2fc58c4e1c5c75142f85a5132946714e25b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "77472638"
---
# <a name="use-customer-managed-keys-to-encrypt-your-app-configuration-data"></a>Utilize chaves geridas pelo cliente para encriptar os dados de configuração da aplicação
A Configuração da Aplicação Azure [encripta informações confidenciais no repouso](../security/fundamentals/encryption-atrest.md). A utilização de chaves geridas pelo cliente fornece uma proteção de dados melhorada, permitindo-lhe gerir as suas chaves de encriptação.  Quando a encriptação de chaves gerida é utilizada, todas as informações sensíveis na Configuração da App são encriptadas com uma chave Azure Key Vault fornecida pelo utilizador.  Isto fornece a capacidade de rodar a chave de encriptação a pedido.  Também fornece a capacidade de revogar o acesso da Configuração de Aplicações Azure a informações sensíveis, revogando o acesso da instância de Configuração da Aplicação à chave.

## <a name="overview"></a>Descrição geral 
A Configuração de Aplicações Azure encripta informações confidenciais em repouso utilizando uma chave de encriptação AES de 256 bits fornecida pela Microsoft. Cada instância de Configuração de Aplicações tem a sua própria chave de encriptação gerida pelo serviço e usada para encriptar informações sensíveis. Informações sensíveis incluem os valores encontrados em pares de valor-chave.  Quando a capacidade de chave gerida pelo cliente está ativada, a Configuração da Aplicação utiliza uma identidade gerida atribuída à instância de Configuração de Aplicações para autenticar com o Diretório Ativo Azure. A identidade gerida chama então o Cofre chave azure e envolve a chave de encriptação da instância de configuração da aplicação. A chave de encriptação embrulhada é então armazenada e a chave de encriptação desembrulhada está em cache dentro da Configuração da App durante uma hora. A Configuração da Aplicação atualiza a versão desembrulhada da chave de encriptação da instância de configuração da aplicação de hora a hora. Isto garante disponibilidade em condições normais de funcionamento. 

>[!IMPORTANT]
> Se a identidade atribuída à instância de Configuração da Aplicação já não estiver autorizada a desembrulhar a chave de encriptação da instância, ou se a chave gerida for permanentemente eliminada, então deixará de ser possível desencriptar informações sensíveis armazenadas na App Exemplo de configuração. A utilização da função [de eliminação suave](../key-vault/key-vault-ovw-soft-delete.md) do Azure Key Vault atenua a possibilidade de eliminar acidentalmente a sua chave de encriptação.

Quando os utilizadores permitem que o cliente gere a capacidade de chave na sua instância de Configuração de Aplicações Azure, eles controlam a capacidade do serviço de aceder às suas informações sensíveis. A chave gerida serve como uma chave de encriptação de raiz. Um utilizador pode revogar o acesso da sua instância de Configuração de Aplicações à sua chave gerida alterando a sua política de acesso ao cofre chave. Quando este acesso for revogado, a Configuração da Aplicação perderá a capacidade de desencriptar os dados dos utilizadores dentro de uma hora. Neste ponto, a instância de Configuração da Aplicação proibirá todas as tentativas de acesso. Esta situação é recuperável, concedendo mais uma vez acesso ao serviço à chave gerida.  Dentro de uma hora, a Configuração da Aplicação será capaz de desencriptar os dados dos utilizadores e operar em condições normais.

>[!NOTE]
>Todos os dados de configuração de aplicações azure são armazenados por até 24 horas numa cópia de segurança isolada. Isto inclui a chave de encriptação desembrulhada. Estes dados não estão imediatamente disponíveis para o serviço ou equipa de serviço. Em caso de restauro de emergência, a Configuração da Aplicação Azure rerevogar-se-á dos dados-chave geridos.

## <a name="requirements"></a>Requisitos
Os seguintes componentes são necessários para ativar com sucesso a capacidade de chave gerida pelo cliente para a configuração da aplicação Azure:
- Padrão nível de configuração de aplicativos Azure
- Cofre chave Azure com funcionalidades de eliminação suave e proteção de purga ativadas
- Uma chave RSA ou RSA-HSM dentro do Cofre chave
    - A chave não deve ser expirada, deve ser ativada, e deve ter capacidades de embrulho e desembrulhar ativadas

Uma vez configurados estes recursos, restam dois passos para permitir que a Configuração da Aplicação Azure utilize a chave key vault:
1. Atribuir uma identidade gerida à instância de Configuração de Aplicações Azure
2. Conceda `GET`a `WRAP`identidade `UNWRAP` e permissões na política de acesso do Key Vault.

## <a name="enable-customer-managed-key-encryption-for-your-azure-app-configuration-instance"></a>Ativar encriptação de chaves gerida pelo cliente para a sua instância de configuração de aplicações Azure
Para começar, necessitará de uma configuração de aplicação Azure devidamente configurada. Se ainda não tiver uma instância de Configuração de Aplicações disponível, siga um destes quickstarts para configurar um:
- [Crie uma app ASP.NET Core com configuração de aplicações azure](quickstart-aspnet-core-app.md)
- [Crie uma aplicação .NET Core com configuração de aplicações azure](quickstart-dotnet-core-app.md)
- [Crie uma aplicação .NET Framework com configuração de aplicações azure](quickstart-dotnet-app.md)
- [Crie uma aplicação Java Spring com configuração de aplicações azure](quickstart-java-spring-app.md)

>[!TIP]
> O Azure Cloud Shell é uma concha interativa gratuita que pode usar para executar as instruções da linha de comando neste artigo.  Tem ferramentas azure comuns pré-instaladas, incluindo o .NET Core SDK. Se estiver ligado à sua subscrição Azure, lance o seu [Azure Cloud Shell](https://shell.azure.com) a partir de shell.azure.com.  Você pode saber mais sobre Azure Cloud Shell lendo a [nossa documentação](../cloud-shell/overview.md)

### <a name="create-and-configure-an-azure-key-vault"></a>Criar e configurar um cofre de chaves Azure
1. Crie um Cofre de Chave Azure utilizando o Azure CLI.  Tenha em `vault-name` anote que ambos e `resource-group-name` são fornecidos pelo utilizador e devem ser únicos.  Usamos `contoso-vault` e `contoso-resource-group` nestes exemplos.

    ```azurecli
    az keyvault create --name contoso-vault --resource-group contoso-resource-group
    ```
    
1. Ativar a eliminação suave e a proteção de purga para o Cofre-Chave. Substitua os nomes`contoso-vault`do Cofre-Chave`contoso-resource-group`( ) e do Grupo de Recursos ( ) criados no passo 1.

    ```azurecli
    az keyvault update --name contoso-vault --resource-group contoso-resource-group --enable-purge-protection --enable-soft-delete
    ```
    
1. Crie uma chave chave do cofre. Forneça um `key-name` exclusivo para esta chave, e substitua os nomes do Cofre chave (`contoso-vault`) criado no passo 1. Especifique `RSA` se `RSA-HSM` prefere ou encriptação.

    ```azurecli
    az keyvault key create --name key-name --kty {RSA or RSA-HSM} --vault-name contoso-vault
    ```
    
    A saída deste comando mostra o ID chave ("criança") para a chave gerada.  Tome nota da identificação chave para usar mais tarde neste exercício.  O ID chave tem `https://{my key vault}.vault.azure.net/keys/{key-name}/{Key version}`o formulário: .  O ID chave tem três componentes importantes:
    1. Key Vault URI: 'https://{my key vault}.vault.azure.net
    1. Nome chave do cofre: {Nome chave}
    1. Versão chave vault: {Versão chave}

1. Crie um sistema de identidade gerida com recurso ao Azure CLI, substituindo o nome da sua instância de Configuração de Aplicações e grupo de recursos utilizado nos passos anteriores. A identidade gerida será utilizada para aceder à chave gerida. Usamos `contoso-app-config` para ilustrar o nome de uma instância de configuração de aplicações:
    
    ```azurecli
    az appconfig identity assign --na1. me contoso-app-config --group contoso-resource-group --identities [system]
    ```
    
    A saída deste comando inclui o ID principal ("principalId") e o ID do inquilino ("tenandId") do sistema atribuído identidade.  Isto será utilizado para conceder o acesso de identidade à chave gerida.

    ```json
    {
    "principalId": {Principal Id},
    "tenantId": {Tenant Id},
    "type": "SystemAssigned",
    "userAssignedIdentities": null
    }
    ```

1. A identidade gerida da instância de Configuração de Aplicações Azure precisa de ter acesso à chave para realizar validação, encriptação e desencriptação de chaves. O conjunto específico de ações a `GET`que `WRAP`necessita `UNWRAP` de acesso inclui: , e para chaves.  A concessão do acesso requer o ID principal da identidade gerida da instância de configuração da aplicação. Este valor foi obtido no escalão anterior. É mostrado abaixo `contoso-principalId`como . Conceda permissão à chave gerida utilizando a linha de comando:

    ```azurecli
    az keyvault set-policy -n contoso-vault --object-id contoso-principalId --key-permissions get wrapKey unwrapKey
    ```

1. Uma vez que a instância de configuração de aplicações Azure pode aceder à chave gerida, podemos ativar a capacidade de chave gerida pelo cliente no serviço utilizando o Azure CLI. Lembre-se das seguintes propriedades `key name` `key vault URI`registadas durante os passos-chave da criação: .

    ```azurecli
    az appconfig update -g contoso-resource-group -n contoso-app-config --encryption-key-name key-name --encryption-key-version key-version --encryption-key-vault key-vault-Uri
    ```

A sua instância de configuração de aplicações Azure está agora configurada para utilizar uma chave gerida pelo cliente armazenada no Cofre de Chaves Azure.

## <a name="next-steps"></a>Passos Seguintes
Neste artigo, configurou a sua instância de Configuração de Aplicações Azure para utilizar uma chave gerida pelo cliente para encriptação.  Saiba como [integrar o seu serviço com identidades geridas azure.](howto-integrate-azure-managed-service-identity.md)