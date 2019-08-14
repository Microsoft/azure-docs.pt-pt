---
title: Gerenciar Azure Key Vault usando a CLI-Azure Key Vault | Microsoft Docs
description: Use este artigo para automatizar tarefas comuns no Key Vault usando o CLI do Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.openlocfilehash: 952126b76b00ff472181c72b1fd7b1a0e1b14e40
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976353"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Gerenciar Key Vault usando o CLI do Azure 

Este artigo aborda como começar a trabalhar com Azure Key Vault usando o CLI do Azure.  Você pode ver informações sobre:

- Como criar um contêiner protegido (um cofre) no Azure
- Adicionando uma chave, um segredo ou um certificado ao cofre de chaves
- Registrando um aplicativo com o Azure Active Directory
- Autorizando um aplicativo para usar uma chave ou segredo
- Definindo políticas de acesso avançado do cofre de chaves
- Trabalhando com módulos de segurança de hardware (HSMs)
- Excluindo o cofre de chaves e chaves e segredos associados
- Diversos comandos da interface de linha de comando de plataforma cruzada do Azure


O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Este artigo não inclui instruções sobre como escrever o aplicativo do Azure que uma das etapas inclui, que mostra como autorizar um aplicativo a usar uma chave ou um segredo no cofre de chaves.
>

Para obter uma visão geral do Azure Key Vault, consulte [o que é Azure Key Vault?](key-vault-whatis.md)
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para usar os comandos CLI do Azure neste artigo, você deve ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interface de linha de comando do Azure versão 2,0 ou posterior. Para instalar a versão mais recente, consulte [instalar o CLI do Azure](/cli/azure/install-azure-cli).
* Um aplicativo que será configurado para usar a chave ou senha que você cria neste artigo. Uma aplicação de exemplo está disponível a partir do [Centro de Transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame incluído.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obtendo ajuda com a interface de linha de comando de plataforma cruzada do Azure

Este artigo pressupõe que você esteja familiarizado com a interface de linha de comando (bash, terminal, prompt de comando).

O parâmetro--Help ou-h pode ser usado para exibir a ajuda para comandos específicos. Como alternativa, o formato da ajuda do Azure [comando] [opções] também pode ser usado. Em caso de dúvida sobre os parâmetros necessários para um comando, consulte a ajuda. Por exemplo, todos os comandos a seguir retornam as mesmas informações:

```azurecli
az account set --help
az account set -h
```

Você também pode ler os artigos a seguir para se familiarizar com Azure Resource Manager na interface de linha de comando de plataforma cruzada do Azure:

* [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
* [Introdução ao CLI do Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Como criar um contêiner protegido (um cofre) no Azure

Os cofres são contêineres protegidos apoiados por módulos de segurança de hardware. Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres de chaves também controlam e registam o acesso a tudo o que está armazenado nos mesmos. O Azure Key Vault pode processar pedidos e renovações de certificados Transport Layer Security (TLS), proporcionando as funcionalidades inerentes a uma solução de gestão de ciclo de vida de certificados robusta. Nas próximas etapas, você criará um cofre.

### <a name="connect-to-your-subscriptions"></a>Estabelecer a ligação às suas subscrições

Para entrar de forma interativa, use o seguinte comando:

```azurecli
az login
```
Para entrar usando uma conta organizacional, você pode passar seu nome de usuário e senha.

```azurecli
az login -u username@domain.com -p password
```

Se você tiver mais de uma assinatura e precisar especificar qual usar, digite o seguinte para ver as assinaturas da sua conta:

```azurecli
az account list
```

Especifique uma assinatura com o parâmetro de assinatura.

```azurecli
az account set --subscription <subscription name or ID>
```

Para obter mais informações sobre como configurar a interface de linha de comando de plataforma cruzada do Azure, consulte [instalar CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Crie um novo grupo de recursos

Ao usar Azure Resource Manager, todos os recursos relacionados são criados dentro de um grupo de recursos. Você pode criar um cofre de chaves em um grupo de recursos existente. Se você quiser usar um novo grupo de recursos, poderá criar um novo.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é o local. Para obter uma lista de todos os tipos de locais possíveis:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registrar o provedor de recursos de Key Vault

 Você pode ver o erro "a assinatura não está registrada para usar o namespace ' Microsoft. keyvault '" ao tentar criar um novo cofre de chaves. Se essa mensagem for exibida, verifique se Key Vault provedor de recursos está registrado em sua assinatura. Esta é uma operação única para cada subscrição.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Use o `az keyvault create` comando para criar um cofre de chaves. Esse script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome de cofre de chaves e a localização geográfica.

Para criar um novo cofre com o nome **ContosoKeyVault**, no grupo de recursos **ContosoResourceGroup**, que reside no local **Ásia Oriental** , digite: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

A saída desse comando mostra as propriedades do cofre de chaves que você criou. As duas propriedades mais importantes são:

* **nome**: No exemplo, o nome é ContosoKeyVault. Você usará esse nome para outros comandos de Key Vault.
* **vaultUri**: No exemplo, o URI é https://contosokeyvault.vault.azure.net. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

A sua conta do Azure pode agora realizar quaisquer operações neste cofre de chaves. Até o momento, ninguém mais está autorizado.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Adicionando uma chave, um segredo ou um certificado ao cofre de chaves

Se você quiser Azure Key Vault criar uma chave protegida por software para você, use o `az key create` comando.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Se você tiver uma chave existente em um arquivo. PEM, poderá carregá-la no Azure Key Vault. Você pode optar por proteger a chave com software ou HSM. Este exemplo importa a chave do arquivo. pem e a protege com o software, usando a senha "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Agora você pode fazer referência à chave que você criou ou carregou em Azure Key Vault, usando seu URI. Use **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para sempre obter a versão atual. Use https://[nome-do-cofre-de-compartimento]. Vault. Azure. net/Keys/[KeyName]/[chave-UNIQUE-ID] para obter essa versão específica. Por exemplo, **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** . 

Adicione um segredo ao cofre, que é uma senha chamada SQLPassword, e que tem o valor de "hVFkk965BuUv" para os cofres de chaves do Azure. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Referencie essa senha usando seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para sempre obter a versão atual e https://[keyvault-nome]. Vault. Azure. net/Secret/[nome-do-segredo]/[segredo-exclusivo-ID] para obter essa versão específica. Por exemplo, **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** .

Importe um certificado para o cofre usando um. PEM ou. pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Vamos exibir a chave, o segredo ou o certificado que você criou:

* Para exibir suas chaves, digite: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Para exibir seus segredos, digite: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Para exibir certificados, digite: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registrando um aplicativo com o Azure Active Directory

Geralmente, este passo deve ser realizado por um programador, num computador à parte. Ele não é específico para Azure Key Vault, mas está incluído aqui, para conscientização. Para concluir o registro do aplicativo, sua conta, o cofre e o aplicativo precisam estar no mesmo diretório do Azure.

As aplicações que utilizam um cofre de chaves devem ser autenticadas através de um token do Azure Active Directory.  O proprietário do aplicativo deve registrá-lo em Azure Active Directory primeiro. No final do registo, o proprietário da aplicação obtém os seguintes valores:

- Uma **ID de aplicativo** (também conhecida como ID do cliente do AAD ou AppID)
- **Chave de autenticação** (também conhecida como segredo partilhado). 

Para obter um token, a aplicação deve apresentar ambos os valores ao Azure Active Directory. A maneira como um aplicativo é configurado para obter um token dependerá do aplicativo. Na [aplicação de exemplo do Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário da aplicação define estes valores no ficheiro app.config.

Para obter etapas detalhadas sobre como registrar um aplicativo com o Azure Active Directory você deve examinar os artigos intitulados [integrando aplicativos com Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [usar o portal para criar um aplicativo e um serviço do Azure Active Directory entidade que pode acessar recursos](../active-directory/develop/howto-create-service-principal-portal.md)e [criar uma entidade de serviço do Azure com o CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Para registrar um aplicativo no Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorizando um aplicativo para usar uma chave ou segredo

Para autorizar o aplicativo a acessar a chave ou o segredo no cofre, use `az keyvault set-policy` o comando.

Por exemplo, se o nome do cofre for ContosoKeyVault, o aplicativo tiver uma appID de 8f8c4bbd-485B-45FD-98f7-ec6300b7b4ed e você quiser autorizar o aplicativo a descriptografar e assinar com chaves em seu cofre, use o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Para autorizar o mesmo aplicativo a ler segredos em seu cofre, digite o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a>Definindo políticas de acesso avançado do cofre de chaves

Use a [atualização AZ keyvault](/cli/azure/keyvault#az-keyvault-update) para habilitar políticas avançadas para o cofre de chaves.

 Habilitar Key Vault para implantação: Permite que as máquinas virtuais recuperem certificados armazenados como segredos do cofre.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Habilitar Key Vault para criptografia de disco: Necessário ao usar o cofre para a criptografia de disco do Azure.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Habilitar Key Vault para implantação de modelo: Permite que o Gerenciador de recursos recupere segredos do cofre.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Trabalhando com módulos de segurança de hardware (HSMs)

Para garantia adicional, você pode importar ou gerar chaves de HSMs (módulos de segurança de hardware) que nunca deixam o limite do HSM. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Se esse requisito não se aplicar a você, pule esta seção e vá para excluir o cofre de chaves e as chaves e os segredos associados.

Para criar essas chaves protegidas por HSM, você deve ter uma assinatura de cofre que ofereça suporte a chaves protegidas por HSM.

Ao criar o keyvault, adicione o parâmetro ' SKU ':

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Neste cofre, pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e chaves protegidas por HSM. Para criar uma chave protegida por HSM, defina o parâmetro de destino como ' HSM ':

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Você pode usar o comando a seguir para importar uma chave de um arquivo. pem em seu computador. Este comando importa a chave os HSMs no serviço Cofre de Chaves:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

O comando seguinte importa um pacote "traga a sua própria chave" (BYOK). Isto permite-lhe gerar a chave no seu HSM local e transferi-la para HSMs no serviço Cofre de Chaves, sem que a chave saia do limite HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Para obter instruções mais detalhadas sobre como gerar esse pacote BYOK, consulte [como usar chaves protegidas por HSM com Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Excluindo o cofre de chaves e chaves e segredos associados

Se você não precisar mais do cofre de chaves e de suas chaves ou segredos, poderá excluir o cofre de chaves usando `az keyvault delete` o comando:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Em alternativa, pode eliminar um grupo de recursos completo do Azure, que inclui o Cofre de Chaves e quaisquer outros recursos que incluiu nesse grupo:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Diversos comandos da interface de linha de comando de plataforma cruzada do Azure

Outros comandos que podem ser úteis para gerenciar Azure Key Vault.

Este comando lista uma exibição tabular de todas as chaves e propriedades selecionadas:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Este comando exibe uma lista completa de propriedades para a chave especificada:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Este comando lista uma exibição tabular de todos os nomes de segredo e propriedades selecionadas:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Aqui está um exemplo de como remover uma chave específica:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Veja um exemplo de como remover um segredo específico:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência completa de CLI do Azure para comandos do Key Vault, consulte [referência da CLI do Key Vault](/cli/azure/keyvault).

- Para referências de programação, consulte [o guia do desenvolvedor do Azure Key Vault](key-vault-developers-guide.md)

- Para obter informações sobre Azure Key Vault e HSMs, consulte [como usar chaves protegidas por HSM com Azure Key Vault](key-vault-hsm-protected-keys.md).
