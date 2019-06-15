---
title: Gerir o Azure Key Vault com a CLI - Azure Key Vault | Documentos da Microsoft
description: Utilize este artigo para automatizar tarefas comuns no Key Vault com a CLI do Azure
services: key-vault
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.topic: conceptual
ms.date: 01/10/2019
ms.author: barclayn
ms.openlocfilehash: d7d76458601b2afecafc1313e334215bf08b6545
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713849"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Gerir o Key Vault com a CLI do Azure 

Este artigo aborda como começar a trabalhar com o Azure Key Vault com a CLI do Azure.  Pode ver informações sobre:

- Como criar um contentor protegido (cofre) no Azure
- Adicionar um certificado, chave ou segredo ao Cofre de chaves
- Registo de aplicações no Azure Active Directory
- Autorizar uma aplicação para utilizar uma chave ou segredo
- Cofre de chaves de configuração avançada de políticas de acesso
- Trabalhar com módulos de segurança de Hardware (HSMs)
- A eliminar o Cofre de chaves e as chaves associadas e segredos
- Comandos de diversos Interface de linha de comandos para várias plataformas do Azure


O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Este artigo não inclui instruções sobre como escrever a aplicação do Azure que um dos passos inclui, que mostra como autorizar uma aplicação para utilizar uma chave ou segredo no Cofre de chaves.
>

Para uma descrição geral do Cofre de chaves do Azure, consulte [o que é o Azure Key Vault?](key-vault-whatis.md)
Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os comandos da CLI do Azure neste artigo, tem de ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Interface de linha de comandos do Azure versão 2.0 ou posterior. Para instalar a versão mais recente, consulte [instalar a CLI do Azure](/cli/azure/install-azure-cli).
* Um aplicativo que será configurado para utilizar a chave ou palavra-passe que criou neste artigo. Uma aplicação de exemplo está disponível a partir do [Centro de Transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o arquivo Leiame incluído.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obter ajuda com Interface de linha de comandos de várias plataformas do Azure

Este artigo pressupõe que esteja familiarizado com a interface de linha de comandos (Bash, Terminal, linha de comandos).

O-- parâmetro de ajuda ou -h pode ser utilizado para ver a ajuda para comandos específicos. Em alternativa, o Azure ajuda também pode ser usado muito o formato [opções] [comando]. Em caso de dúvida sobre os parâmetros necessários para um comando, consulte a ajuda. Por exemplo, os comandos seguintes para todos os devolvem as mesmas informações:

```azurecli
az account set --help
az account set -h
```

Pode ainda ler os artigos seguintes para se familiarizar com o Azure Resource Manager na Interface de linha de comandos de várias plataformas do Azure:

* [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
* [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Como criar um contentor protegido (cofre) no Azure

Cofres são protegidos por módulos de segurança de hardware de segurança de contentores. Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres de chaves também controlam e registam o acesso a tudo o que está armazenado nos mesmos. O Azure Key Vault pode processar pedidos e renovações de certificados Transport Layer Security (TLS), proporcionando as funcionalidades inerentes a uma solução de gestão de ciclo de vida de certificados robusta. Os passos seguintes, irá criar um cofre.

### <a name="connect-to-your-subscriptions"></a>Estabelecer a ligação às suas subscrições

Para iniciar sessão interativamente, utilize o seguinte comando:

```azurecli
az login
```
Para iniciar sessão com uma conta institucional, pode transmitir o seu nome de utilizador e palavra-passe.

```azurecli
az login -u username@domain.com -p password
```

Se tiver mais de uma assinatura e tem de especificar quais pode ser utilizado, escreva o seguinte para ver as subscrições da sua conta:

```azurecli
az account list
```

Especifica uma subscrição com o parâmetro de subscrição.

```azurecli
az account set --subscription <subscription name or ID>
```

Para obter mais informações sobre a configuração de Interface de linha de comandos do Azure para várias plataformas, consulte [instalar a CLI do Azure](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos

Ao utilizar o Azure Resource Manager, todos os recursos relacionados são criados dentro de um grupo de recursos. Pode criar um cofre de chaves num grupo de recursos existente. Se pretender utilizar um grupo de recursos, pode criar um novo.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é o local. Para obter uma lista de todos os locais possíveis tipo:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registar o fornecedor de recursos do Cofre de chaves

 Poderá ver o erro "a subscrição não está registada para utilizar o espaço de nomes 'Microsoft. keyvault'" quando tenta criar um novo cofre de chaves. Se esta mensagem for apresentada, certifique-se de que fornecedor de recursos do Cofre de chaves está registado na sua subscrição. Esta é uma operação única para cada subscrição.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Utilize o `az keyvault create` comando para criar um cofre de chaves. Este script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome de Cofre de chaves e a localização geográfica.

Para criar um novo cofre com o nome **ContosoKeyVault**, no grupo de recursos **ContosoResourceGroup**, que residem no **Ásia Oriental** localização, escreva: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

O resultado deste comando mostra as propriedades do Cofre de chaves que criou. As duas propriedades mais importantes são:

* **name**: No exemplo, o nome é ContosoKeyVault. Irá utilizar este nome para outros comandos do Key Vault.
* **vaultUri**: No exemplo, o URI é https://contosokeyvault.vault.azure.net. As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

A sua conta do Azure pode agora realizar quaisquer operações neste cofre de chaves. Até o momento, está autorizado mais ninguém.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Adicionar um certificado, chave ou segredo ao Cofre de chaves

Se pretender que o Azure Key Vault para criar uma chave protegida por software para si, utilize o `az key create` comando.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Se tiver uma chave existente num ficheiro. pem, pode carregá-lo para o Azure Key Vault. Pode optar por proteger a chave com o software ou o HSM. Neste exemplo, importam a chave do ficheiro. pem e protegê-lo com o software, usando a palavra-passe "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Agora, pode referenciar a chave que criou ou carregou para o Azure Key Vault, utilizando o respetivo URI. Uso **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey** para obter sempre a versão atual. Utilize https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] para obter esta versão específica. Por exemplo, **https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87** . 

Adicione um segredo ao cofre, que é uma palavra-passe designada SQLPassword e que tem o valor de "hVFkk965BuUv" para o Azure Key Vault. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Referenciar esta palavra-passe com o URI. Uso **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual e https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] para obter esta versão específica. Por exemplo, **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d** .

Importe um certificado para o Cofre através de uma. pem ou. pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Vamos ver a chave, o segredo ou o certificado que criou:

* Para ver as suas chaves, escreva: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Para ver os seus segredos, escreva: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Para ver os certificados, escreva: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registo de aplicações no Azure Active Directory

Geralmente, este passo deve ser realizado por um programador, num computador à parte. Ele não é específico para o Azure Key Vault, mas é incluído aqui, reconhecimento de. Para concluir o registo de aplicações, sua conta, o Cofre e a aplicação tem de estar no mesmo diretório do Azure.

As aplicações que utilizam um cofre de chaves devem ser autenticadas através de um token do Azure Active Directory.  O proprietário do aplicativo deve registrá-la no Azure Active Directory pela primeira vez. No final do registo, o proprietário da aplicação obtém os seguintes valores:

- Uma **ID da aplicação** (também conhecido como o ID de cliente do AAD ou appID)
- **Chave de autenticação** (também conhecida como segredo partilhado). 

Para obter um token, a aplicação deve apresentar ambos os valores ao Azure Active Directory. Como um aplicativo está configurado para obter um token irá depender do aplicativo. Na [aplicação de exemplo do Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário da aplicação define estes valores no ficheiro app.config.

Para obter passos detalhados no registo de aplicações no Azure Active Directory deve rever os artigos intitulados [integrar aplicações com o Azure Active Directory](../active-directory/develop/active-directory-integrating-applications.md), [utilize o portal para criar um Azure Active Principal de serviço que pode aceder aos recursos e aplicações do](../active-directory/develop/howto-create-service-principal-portal.md), e [criar um Azure principal de serviço com a CLI do Azure](/cli/azure/create-an-azure-service-principal-azure-cli).

Registar uma aplicação no Azure Active Directory:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorizar uma aplicação para utilizar uma chave ou segredo

Para autorizar a aplicação a aceder à chave ou segredo no cofre, utilize o `az keyvault set-policy` comando.

Por exemplo, se o nome do seu Cofre é ContosoKeyVault, o aplicativo tem um appID do 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed e pretende autorizar a aplicação a desencriptar e assinar com chaves no seu Cofre, utilize o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Para autorizar a mesma aplicação leia os segredos no cofre, escreva o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="bkmk_KVperCLI"></a> Cofre de chaves de configuração avançada de políticas de acesso

Uso [atualização do Cofre de chaves de az](/cli/azure/keyvault#az-keyvault-update) para ativar políticas avançadas para o Cofre de chaves.

 Ative o Key Vault para a implementação: Permite que as máquinas virtuais obter certificados armazenados como segredos do cofre.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Ative o Key Vault para encriptação de disco: É necessário quando utilizar o cofre para a encriptação de disco do Azure.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Ative o Key Vault para implementação do modelo: Permite que o Resource Manager para obter segredos a partir do cofre.

```azurecli 
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
 ```

## <a name="working-with-hardware-security-modules-hsms"></a>Trabalhar com módulos de segurança de Hardware (HSMs)

Para mais segurança, pode importar ou gerar chaves a partir de módulos de segurança de hardware (HSMs) que nunca deixam o limite do HSM. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Se este requisito não se aplica a si, ignore esta secção e aceda ao eliminar o Cofre de chaves e as chaves associadas e segredos.

Para criar estas chaves protegidas por HSM, tem de ter uma subscrição de cofre que suporta chaves protegidas por HSM.

Quando cria o Cofre de chaves, adicione o parâmetro de "sku":

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Neste cofre, pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e chaves protegidas por HSM. Para criar uma chave protegida por HSM, defina o parâmetro de destino para 'HSM':

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Pode utilizar o seguinte comando para importar uma chave a partir de um ficheiro. pem no seu computador. Este comando importa a chave os HSMs no serviço Cofre de Chaves:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

O comando seguinte importa um pacote "traga a sua própria chave" (BYOK). Isto permite-lhe gerar a chave no seu HSM local e transferi-la para HSMs no serviço Cofre de Chaves, sem que a chave saia do limite HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Para obter mais instruções sobre como gerar este pacote BYOK, consulte [como utilizar chaves de HSM-Protected com o Azure Key Vault](key-vault-hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>A eliminar o Cofre de chaves e as chaves associadas e segredos

Se já não precisar de Cofre de chaves e respetivas chaves ou segredos, pode eliminar o Cofre de chaves utilizando o `az keyvault delete` comando:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Em alternativa, pode eliminar um grupo de recursos completo do Azure, que inclui o Cofre de Chaves e quaisquer outros recursos que incluiu nesse grupo:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Comandos de diversos Interface de linha de comandos para várias plataformas do Azure

Outros comandos que poderão ser úteis para a gestão do Azure Key Vault.

Este comando lista uma tabela de apresentação de todas as chaves e propriedades selecionadas:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Este comando apresenta uma lista completa das propriedades para a chave especificada:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Este comando lista uma tabela de apresentação de todos os nomes secretos e propriedades selecionadas:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Eis um exemplo de como remover uma chave específica:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Eis um exemplo de como remover um segredo específico:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Passos Seguintes

- Para referência da CLI do Azure completa para comandos do Cofre de chaves, consulte [referência da CLI do Cofre de chave](/cli/azure/keyvault).

- Para referências de programação, consulte [guia para programadores do Azure Key Vault](key-vault-developers-guide.md)

- Para obter informações sobre o Azure Key Vault e HSMs, consulte [como utilizar chaves de HSM-Protected com o Azure Key Vault](key-vault-hsm-protected-keys.md).
