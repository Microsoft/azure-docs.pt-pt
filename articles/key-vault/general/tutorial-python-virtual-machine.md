---
title: Tutorial - Use Azure Key Vault com uma máquina virtual em Python Microsoft Docs
description: Neste tutorial, você configura uma máquina virtual uma aplicação Python para ler um segredo do seu cofre chave.
services: key-vault
author: msmbaldwin
ms.service: key-vault
ms.subservice: general
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: mbaldwin
ms.custom: mvc, devx-track-python, devx-track-azurecli
ms.openlocfilehash: 89118f13bc009ce60d4fd1c82dfe7688bf1e551b
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/27/2020
ms.locfileid: "92741265"
---
# <a name="tutorial-use-azure-key-vault-with-a-virtual-machine-in-python"></a>Tutorial: Use a adóia da chave Azure com uma máquina virtual em Python

O Azure Key Vault ajuda-o a proteger chaves, segredos e certificados, tais como chaves API e cadeias de ligação de bases de dados.

Neste tutorial, você criou uma aplicação Python para ler informações do Azure Key Vault usando identidades geridas para recursos Azure. Saiba como:

> [!div class="checklist"]
> * Criar um cofre de chaves
> * Armazenar um segredo em Key Vault
> * Criar uma máquina virtual Azure Linux
> * Ativar uma [identidade gerida](../../active-directory/managed-identities-azure-resources/overview.md) para a máquina virtual
> * Conceda as permissões necessárias para a aplicação da consola para ler dados do Key Vault
> * Recupere um segredo do Cofre de Chaves

Antes de começar, leia [os conceitos básicos do Key Vault.](basic-concepts.md) 

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Pré-requisitos

Para Windows, Mac e Linux:
  * [Rio Git](https://git-scm.com/downloads)
  * Este tutorial requer que você gere o Azure CLI localmente. Deve ter a versão Azure CLI 2.0.4 ou posteriormente instalada. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar a CLI, veja [Instalar a CLI 2.0 do Azure](/cli/azure/install-azure-cli).

## <a name="log-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar sessão no Azure com a CLI do Azure, introduza:

```azurecli
az login
```

## <a name="create-a-resource-group-and-key-vault"></a>Criar um grupo de recursos e cofre chave

[!INCLUDE [Create a resource group and key vault](../../../includes/key-vault-rg-kv-creation.md)]

## <a name="populate-your-key-vault-with-a-secret"></a>Povoe o seu cofre com um segredo

[!INCLUDE [Create a secret](../../../includes/key-vault-create-secret.md)]

## <a name="create-a-virtual-machine"></a>Criar uma máquina virtual

Criar um VM chamado **myVM** utilizando um dos seguintes métodos:

| Linux | Windows |
|--|--|
| [CLI do Azure](../../virtual-machines/linux/quick-create-cli.md) | [CLI do Azure](../../virtual-machines/windows/quick-create-cli.md) |
| [PowerShell](../../virtual-machines/linux/quick-create-powershell.md) | [PowerShell](../../virtual-machines/windows/quick-create-powershell.md) |
| [Portal do Azure](../../virtual-machines/linux/quick-create-portal.md) | [O portal do Azure](../../virtual-machines/windows/quick-create-portal.md) |

Para criar um Linux VM utilizando o CLI Azure, utilize o comando [az vm create.](/cli/azure/vm)  O exemplo a seguir adiciona uma conta de utilizador *denominada azureuser* . O `--generate-ssh-keys` parâmetro é utilizado para gerar automaticamente uma chave SSH e colocá-la no local da chave predefinido *(~/.ssh).* 

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVM \
  --image UbuntuLTS \
  --admin-username azureuser \
  --generate-ssh-keys
```

Note o valor da `publicIpAddress` saída.

## <a name="assign-an-identity-to-the-vm"></a>Atribuir uma identidade ao VM

Criar uma identidade atribuída ao sistema para a máquina virtual utilizando o comando de atribuição de identidade Azure CLI [vm:](/cli/azure/vm/identity?view=azure-cli-latest#az-vm-identity-assign)

```azurecli
az vm identity assign --name "myVM" --resource-group "myResourceGroup"
```

Note a identidade atribuída ao sistema que é apresentada no seguinte código. A saída do comando anterior seria: 

```output
{
  "systemAssignedIdentity": "xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx",
  "userAssignedIdentities": {}
}
```

## <a name="assign-permissions-to-the-vm-identity"></a>Atribuir permissões à identidade VM

Agora pode atribuir as permissões de identidade previamente criadas para o seu cofre de chaves executando o seguinte comando:

```azurecli
az keyvault set-policy --name "<your-unique-keyvault-name>" --object-id "<systemAssignedIdentity>" --secret-permissions get list
```

## <a name="log-in-to-the-vm"></a>Faça login no VM

Para iniciar seduca na máquina virtual, siga as instruções em [Connect e inscreva-se numa máquina virtual Azure que executa o Linux](../../virtual-machines/linux/login-using-aad.md) ou [Connect e inscreva-se numa máquina virtual Azure que executa o Windows](../../virtual-machines/windows/connect-logon.md).


Para iniciar sessão num Linux VM, pode utilizar o comando ssh com o <publicIpAddress> " dado no passo de máquina [virtual:](#create-a-virtual-machine)

```terminal
ssh azureuser@<PublicIpAddress>
```

## <a name="install-python-libraries-on-the-vm"></a>Instalar bibliotecas Python no VM

Na máquina virtual, instale as duas bibliotecas Python que vamos usar no nosso script Python: `azure-keyvault-secrets` e `azure.identity` .  

Num Linux VM, por exemplo, pode instalá-los `pip3` utilizando:

```bash
pip3 install azure-keyvault-secrets

pip3 install azure.identity
```

## <a name="create-and-edit-the-sample-python-script"></a>Criar e editar a amostra python script

Na máquina virtual, crie um ficheiro Python chamado **sample.py** . Edite o ficheiro para conter o seguinte código, substituindo "<o seu nome único de chave->" com o nome do seu cofre chave:

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import DefaultAzureCredential

keyVaultName = "<your-unique-keyvault-name>"
KVUri = f"https://{keyVaultName}.vault.azure.net"
secretName = "mySecret"

credential = DefaultAzureCredential()
client = SecretClient(vault_url=KVUri, credential=credential)
retrieved_secret = client.get_secret(secretName)

print(f"The value of secret '{secretName}' in '{keyVaultName}' is: '{retrieved_secret.value}'")
```

## <a name="run-the-sample-python-app"></a>Executar a amostra python app

Por último, corra **sample.py.** Se tudo correu bem, deve devolver o valor do seu segredo:

```bash
python3 sample.py

The value of secret 'mySecret' in '<your-unique-keyvault-name>' is: 'Success!'
```

## <a name="clean-up-resources"></a>Limpar os recursos

Quando já não forem necessários, elimine a máquina virtual e o cofre da chave.  Pode fazê-lo rapidamente, simplesmente eliminando o grupo de recursos a que pertencem:

```azurecli
az group delete -g myResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

[Cofre chave Azure REST API](https://docs.microsoft.com/rest/api/keyvault/)
