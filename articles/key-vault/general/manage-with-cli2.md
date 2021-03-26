---
title: Gerir o cofre da chave Azure usando cli - Azure Key Vault | Microsoft Docs
description: Utilize este artigo para automatizar tarefas comuns no Cofre de Chaves utilizando o CLI Azure
services: key-vault
author: msmbaldwin
manager: rkarlin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: mbaldwin
ms.custom: devx-track-azurecli
ms.openlocfilehash: c084df60fa2293a614726be44669a1465b399d2c
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105557212"
---
# <a name="manage-key-vault-using-the-azure-cli"></a>Gerir o Cofre de Chaves utilizando o Azure CLI 

Este artigo abrange como começar a trabalhar com o Azure Key Vault usando o Azure CLI.  Pode ver informações sobre:

- Como criar um recipiente endurecido (um cofre) em Azure
- Adicionando uma chave, segredo ou certificado ao cofre chave
- Registar uma candidatura com o Azure Ative Directory
- Autorizar uma aplicação a usar uma chave ou segredo
- Definição de políticas avançadas de acesso ao cofre de chaves
- Trabalhar com módulos de segurança hardware (HSMs)
- Apagar o cofre de chaves e chaves e segredos associados
- Comandos de interface de linha de plataforma cruzada Azure diversos


O Cofre de Chaves do Azure chave está disponível na maior parte das regiões. Para obter mais informações, consulte a [página de preços do Cofre de Chaves](https://azure.microsoft.com/pricing/details/key-vault/).

> [!NOTE]
> Este artigo não inclui instruções sobre como escrever a aplicação Azure que um dos passos inclui, o que mostra como autorizar uma aplicação a usar uma chave ou segredo no cofre da chave.
>

Para uma visão geral do Azure Key Vault, veja [o que é Azure Key Vault?](overview.md) Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para utilizar os comandos Azure CLI neste artigo, tem de ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* Azure Command-Line Interface versão 2.0 ou posterior. Para instalar a versão mais recente, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).
* Uma aplicação que será configurada para usar a chave ou senha que cria neste artigo. Uma aplicação de exemplo está disponível a partir do [Centro de Transferências da Microsoft](https://www.microsoft.com/download/details.aspx?id=45343). Para obter instruções, consulte o ficheiro Readme incluído.

### <a name="getting-help-with-azure-cross-platform-command-line-interface"></a>Obter ajuda com Azure Cross-Platform Command-Line Interface

Este artigo pressupõe que está familiarizado com a interface de linha de comando (Bash, Terminal, Comando).

O parâmetro de ajuda ou -h pode ser usado para visualizar ajuda para comandos específicos. Alternadamente, o formato de ajuda do Azure [comando] também pode ser usado. Quando estiver em dúvida sobre os parâmetros necessários por um comando, consulte a ajuda. Por exemplo, todos os seguintes comandos devolvem as mesmas informações:

```azurecli
az account set --help
az account set -h
```

Também pode ler os seguintes artigos para se familiarizar com o Azure Resource Manager na Azure Cross-Platform Command-Line Interface:

* [Instalar a CLI do Azure](/cli/azure/install-azure-cli)
* [Introdução à CLI do Azure](/cli/azure/get-started-with-azure-cli)

## <a name="how-to-create-a-hardened-container-a-vault-in-azure"></a>Como criar um recipiente endurecido (um cofre) em Azure

Os cofres são contentores seguros apoiados por módulos de segurança de hardware. Os cofres centralizam o armazenamento dos segredos das aplicações, o que ajuda a reduzir as possibilidades de perda acidental de informações de segurança. Os cofres de chaves também controlam e registam o acesso a tudo o que está armazenado nos mesmos. O Azure Key Vault pode processar pedidos e renovações de certificados Transport Layer Security (TLS), proporcionando as funcionalidades inerentes a uma solução de gestão de ciclo de vida de certificados robusta. Nos próximos passos, vai criar um cofre.

### <a name="connect-to-your-subscriptions"></a>Estabelecer a ligação às suas subscrições

Para iniciar sinsutiltivamente, utilize o seguinte comando:

```azurecli
az login
```
Para iniciar sôms usando uma conta organizacional, pode passar no seu nome de utilizador e senha.

```azurecli
az login -u username@domain.com -p password
```

Se tiver mais do que uma subscrição e precisar de especificar qual usar, digite o seguinte para ver as subscrições da sua conta:

```azurecli
az account list
```

Especifique uma subscrição com o parâmetro de subscrição.

```azurecli
az account set --subscription <subscription name or ID>
```

Para obter mais informações sobre a configuração da Interface Command-Line da Plataforma Transversal Azure, consulte [instalar o Azure CLI](/cli/azure/install-azure-cli).

### <a name="create-a-new-resource-group"></a>Criar um novo grupo de recursos

Ao utilizar o Azure Resource Manager, todos os recursos relacionados são criados dentro de um grupo de recursos. Pode criar um cofre chave num grupo de recursos existente. Se quiser utilizar um novo grupo de recursos, pode criar um novo.

```azurecli
az group create -n "ContosoResourceGroup" -l "East Asia"
```

O primeiro parâmetro é o nome do grupo de recursos e o segundo parâmetro é a localização. Para obter uma lista de todos os locais possíveis tipo:

```azurecli
az account list-locations
``` 

### <a name="register-the-key-vault-resource-provider"></a>Registe o fornecedor de recursos Key Vault

 Pode ver o erro "A subscrição não está registada para utilizar o espaço de identificação 'Microsoft.KeyVault'" quando tentar criar um novo cofre de chaves. Se essa mensagem aparecer, certifique-se de que o fornecedor de recursos Key Vault está registado na sua subscrição. Esta é uma operação única para cada subscrição.

```azurecli
az provider register -n Microsoft.KeyVault
```

### <a name="create-a-key-vault"></a>Criar um cofre de chaves

Use o `az keyvault create` comando para criar um cofre de chaves. Este script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome de cofre chave e a localização geográfica.

Para criar um novo cofre com o nome **ContosoKeyVault,** no grupo de recursos  **ContosoResourceGroup,** residente na localização da **Ásia Oriental,** escreva: 

```azurecli
az keyvault create --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --location "East Asia"
```

A saída deste comando mostra propriedades do cofre chave que criaste. As duas propriedades mais importantes são:

* **nome**: No exemplo, o nome é ContosoKeyVault. Vais usar este nome para outros comandos do Key Vault.
* **vaultUri**: No exemplo, o URI é https://contosokeyvault.vault.azure.net . As aplicações que utilizam o cofre através da respetiva API têm de utilizar este URI.

A sua conta do Azure pode agora realizar quaisquer operações neste cofre de chaves. Até agora, ninguém mais está autorizado.

## <a name="adding-a-key-secret-or-certificate-to-the-key-vault"></a>Adicionando uma chave, segredo ou certificado ao cofre chave

Se quiser que o Azure Key Vault crie uma chave protegida por software para si, utilize o `az key create` comando.

```azurecli
az keyvault key create --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --protection software
```

Se tiver uma chave existente num ficheiro .pem, pode enviá-la para o Cofre da Chave Azure. Pode optar por proteger a chave com software ou HSM. Este exemplo importa a chave do ficheiro .pem e protege-a com software, utilizando a palavra-passe "hVFkk965BuUv":

```azurecli
az keyvault key import --vault-name "ContosoKeyVault" --name "ContosoFirstKey" --pem-file "./softkey.pem" --pem-password "hVFkk965BuUv" --protection software
```

Agora pode fazer referência à chave que criou ou carregou para o Azure Key Vault, utilizando o seu URI. Use `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey` para obter sempre a versão atual. Utilize https://[keyvault-name].vault.azure.net/keys/[keyname]/[key-unique-id] para obter esta versão específica. Por exemplo, `https://ContosoKeyVault.vault.azure.net/keys/ContosoFirstKey/cgacf4f763ar42ffb0a1gca546aygd87`. 

Adicione um segredo ao cofre, que é uma palavra-passe chamada SQLPassword, e que tem o valor de "hVFkk965BuUv" a Azure Key Vaults. 

```azurecli
az keyvault secret set --vault-name "ContosoKeyVault" --name "SQLPassword" --value "hVFkk965BuUv "
```

Consulte esta palavra-passe utilizando o seu URI. Use **https://ContosoVault.vault.azure.net/secrets/SQLPassword** para obter sempre a versão atual, e https://[keyvault-name].vault.azure.net/secret/[secret-name]/[secret-unique-id] para obter esta versão específica. Por exemplo, **https://ContosoVault.vault.azure.net/secrets/SQLPassword/90018dbb96a84117a0d2847ef8e7189d**.

Importe um certificado para o cofre usando um .pem ou .pfx.

```azurecli
az keyvault certificate import --vault-name "ContosoKeyVault" --file "c:\cert\cert.pfx" --name "ContosoCert" --password "hVFkk965BuUv"
```

Vamos ver a chave, segredo ou certificado que criou:

* Para ver as suas chaves, escreva: 

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

* Para ver os seus segredos, escreva: 

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

* Para visualizar certificados, digite: 

```azurecli
az keyvault certificate list --vault-name "ContosoKeyVault"
```

## <a name="registering-an-application-with-azure-active-directory"></a>Registar uma candidatura com o Azure Ative Directory

Geralmente, este passo deve ser realizado por um programador, num computador à parte. Não é específico do Azure Key Vault, mas está incluído aqui, para a consciencialização. Para completar o registo da aplicação, a sua conta, o cofre e a aplicação precisam estar no mesmo diretório Azure.

As aplicações que utilizam um cofre de chaves devem ser autenticadas através de um token do Azure Active Directory.  O proprietário do pedido deve registá-lo primeiro no Azure Ative Directy. No final do registo, o proprietário da aplicação obtém os seguintes valores:

- Um **ID de aplicação** (também conhecido como ID do Cliente AAD ou appID)
- **Chave de autenticação** (também conhecida como segredo partilhado). 

Para obter um token, a aplicação deve apresentar ambos os valores ao Azure Active Directory. A configuração de uma aplicação para obter um token dependerá da aplicação. Na [aplicação de exemplo do Key Vault](https://www.microsoft.com/download/details.aspx?id=45343), o proprietário da aplicação define estes valores no ficheiro app.config.

Para etapas detalhadas sobre o registo de uma aplicação com o Azure Ative Directory deve rever os artigos [intitulados Integrando aplicações com diretório ativo Azure,](../../active-directory/develop/quickstart-register-app.md) [Use portal para criar uma aplicação e um diretor de serviço azure ative que possa aceder a recursos,](../../active-directory/develop/howto-create-service-principal-portal.md)e Criar um diretor de serviço [Azure com o Azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

Para registar uma candidatura no Diretório Ativo Azure:

```azurecli
az ad sp create-for-rbac -n "MyApp" --password "hVFkk965BuUv" --skip-assignment
# If you don't specify a password, one will be created for you.
```

## <a name="authorizing-an-application-to-use-a-key-or-secret"></a>Autorizar uma aplicação a usar uma chave ou segredo

Para autorizar a aplicação a aceder à chave ou segredo no cofre, utilize o `az keyvault set-policy` comando.

Por exemplo, se o seu nome de cofre é ContosoKeyVault, a aplicação tem um appID de 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed, e pretende autorizar a aplicação para desencriptar e assinar com chaves no seu cofre, use o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --key-permissions decrypt sign
```

Para autorizar a mesma aplicação a ler segredos no seu cofre, digite o seguinte comando:

```azurecli
az keyvault set-policy --name "ContosoKeyVault" --spn 8f8c4bbd-485b-45fd-98f7-ec6300b7b4ed --secret-permissions get
```

## <a name="setting-key-vault-advanced-access-policies"></a><a name="bkmk_KVperCLI"></a> Definição de políticas avançadas de acesso ao cofre de chaves

Utilize [a atualização az keyvault](/cli/azure/keyvault#az-keyvault-update) para permitir políticas avançadas para o cofre da chave.

 Ativar o Cofre da Chave para implantação: Permite que as máquinas virtuais recuperem certificados armazenados como segredos do cofre.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-deployment "true"
 ```

Ativar o Cofre da Chave para encriptação do disco: Necessário ao utilizar o cofre para encriptação do disco Azure.

 ```azurecli
 az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-disk-encryption "true"
 ```  

Ativar o Cofre de Chaves para a implementação do modelo: Permite que o Gestor de Recursos recupere segredos do cofre.

```azurecli
az keyvault update --name "ContosoKeyVault" --resource-group "ContosoResourceGroup" --enabled-for-template-deployment "true"
```

## <a name="working-with-hardware-security-modules-hsms"></a>Trabalhar com módulos de segurança hardware (HSMs)

Para obter uma garantia adicional, pode importar ou gerar chaves de módulos de segurança de hardware (HSMs) que nunca saem do limite do HSM. Os HSMs têm a certificação FIPS 140-2 de nível 2 validada. Se este requisito não se aplica a si, ignore esta secção e aceda a Eliminar o cofre de chaves e as chaves e segredos associados.

Para criar estas chaves protegidas pelo HSM, a sua subscrição ao cofre deverá suportar chaves protegidas pelo HSM.

Quando criar o teclado, adicione o parâmetro 'sku':

```azurecli
az keyvault create --name "ContosoKeyVaultHSM" --resource-group "ContosoResourceGroup" --location "East Asia" --sku "Premium"
```

Neste cofre, pode adicionar chaves protegidas por software (conforme mostrado anteriormente) e chaves protegidas por HSM. Para criar uma chave protegida pelo HSM, desajuste o parâmetro destino para 'HSM':

```azurecli
az keyvault key create --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --protection "hsm"
```

Pode utilizar o seguinte comando para importar uma chave a partir de um ficheiro .pem no seu computador. Este comando importa a chave os HSMs no serviço Cofre de Chaves:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --pem-file "/.softkey.pem" --protection "hsm" --pem-password "PaSSWORD"
```

O próximo comando importa um pacote "traga a sua própria chave" (BYOK). Isto permite-lhe gerar a chave no seu HSM local e transferi-la para HSMs no serviço Cofre de Chaves, sem que a chave saia do limite HSM:

```azurecli
az keyvault key import --vault-name "ContosoKeyVaultHSM" --name "ContosoFirstHSMKey" --byok-file "./ITByok.byok" --protection "hsm"
```

Para obter instruções mais detalhadas sobre como gerar este pacote BYOK, consulte [Como utilizar chaves HSM-Protected com cofre de chave Azure](../keys/hsm-protected-keys.md).

## <a name="deleting-the-key-vault-and-associated-keys-and-secrets"></a>Apagar o cofre de chaves e chaves e segredos associados

Se já não precisar do cofre de chaves e das chaves ou segredos, pode apagar o cofre da chave utilizando o `az keyvault delete` comando:

```azurecli
az keyvault delete --name "ContosoKeyVault"
```

Em alternativa, pode eliminar um grupo de recursos completo do Azure, que inclui o Cofre de Chaves e quaisquer outros recursos que incluiu nesse grupo:

```azurecli
az group delete --name "ContosoResourceGroup"
```

## <a name="miscellaneous-azure-cross-platform-command-line-interface-commands"></a>Comandos de interface de linha de plataforma cruzada Azure diversos

Outros comandos que poderá achar úteis para gerir o Azure Key Vault.

Este comando lista um ecrã tabular de todas as teclas e propriedades selecionadas:

```azurecli
az keyvault key list --vault-name "ContosoKeyVault"
```

Este comando apresenta uma lista completa de propriedades para a chave especificada:

```azurecli
az keyvault key show --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Este comando lista uma exibição tabular de todos os nomes secretos e propriedades selecionadas:

```azurecli
az keyvault secret list --vault-name "ContosoKeyVault"
```

Aqui está um exemplo de como remover uma chave específica:

```azurecli
az keyvault key delete --vault-name "ContosoKeyVault" --name "ContosoFirstKey"
```

Aqui está um exemplo de como remover um segredo específico:

```azurecli
az keyvault secret delete --vault-name "ContosoKeyVault" --name "SQLPassword"
```

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência completa do Azure CLI para comandos de cofre de chaves, consulte [a referência CLI do Cofre-Chave](/cli/azure/keyvault).

- Para obter referências de programação, consulte [o guia do desenvolvedor do Azure Key Vault](developers-guide.md)

- Para obter informações sobre Azure Key Vault e HSMs, consulte [Como utilizar chaves de HSM-Protected com cofre de chaves Azure](../keys/hsm-protected-keys.md).
