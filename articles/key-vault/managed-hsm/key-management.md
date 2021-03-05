---
title: Gerir chaves num HSM gerido - Azure Key Vault | Microsoft Docs
description: Utilize este artigo para gerir as chaves num HSM gerido
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8d0cbd35b53bc8460ac8a19e5197d1f560657263
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212047"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Gerir um HSM gerido utilizando o Azure CLI

> [!NOTE]
> Key Vault suporta dois tipos de recursos: cofres e HSMs geridos. Este artigo é sobre **O HSM Gerido.** Se quiser aprender a gerir um cofre, consulte [o Key Vault utilizando o Azure CLI](../general/manage-with-cli2.md).

Para uma visão geral do HSM gerido, consulte [o que é gerido HSM?](overview.md)

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

Para completar os passos deste artigo, tem de ter os seguintes itens:

* Uma subscrição ao Microsoft Azure. Se não tiver uma, pode inscrever-se numa [avaliação gratuita](https://azure.microsoft.com/pricing/free-trial).
* A versão Azure CLI 2.12.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Instalar a CLI do Azure]( /cli/azure/install-azure-cli).
* Um HSM gerido na sua subscrição. Consulte [Quickstart: Provisão e ativação de um HSM gerido utilizando o Azure CLI](quick-create-cli.md) para provisões e ativar um HSM gerido.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Para iniciar seduca em Azure usando o CLI pode escrever:

```azurecli
az login
```

Para obter mais informações sobre as opções de login através do CLI, consulte [o login com o Azure CLI](/cli/azure/authenticate-azure-cli)

> [!NOTE]
> Todos os comandos abaixo mostram dois métodos de utilização. Um usando **parâmetros de nome hsm** e **--nome** (para nome-chave) e outro usando **parâmetros de identificação --id** onde você pode especificar url inteiro, incluindo incluindo o nome-chave, se apropriado. Este último método é útil quando o chamador (um utilizador ou uma aplicação) não tem acesso lido no plano de controlo e apenas acesso restrito no plano de dados.

## <a name="create-an-hsm-key"></a>Criar uma chave HSM

Use `az keyvault key create` o comando para criar uma chave.

### <a name="create-an-rsa-key"></a>Criar uma chave RSA

O exemplo abaixo mostra como criar uma chave **RSA** de 3072 bits que será usada apenas para **wrapKey, desembrulhar** operações (--ops). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Nota: a `get` operação apenas devolve a chave pública e os atributos-chave. Não devolve a chave privada (no caso da chave assimétrica, nem o material chave (no caso da chave simétrica).

### <a name="create-an-ec-key"></a>Criar uma chave CE

O exemplo abaixo mostra como criar uma chave **CE** com curva P-256 que será usada apenas para **sinalização e verificação** de operações (-ops) e tem duas etiquetas, **uso** e **appname**. As tags ajudam a adicionar metadados adicionais à chave para rastreio e gestão.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Criar uma chave simétrica de 256 bits

O exemplo abaixo mostra como criar uma chave **simétrica** de 256 bits que será usada apenas para **encriptar e desencriptar** operações (--ops).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Ver atributos-chave e tags

Utilize `az keyvault key show` o comando para visualizar atributos, versões e tags para uma chave.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Chaves da lista

Utilize `az keyvault key list` o comando para listar todas as chaves dentro de um HSM gerido.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Eliminar uma chave

Utilize `az keyvault key delete` o comando para eliminar uma chave de um HSM gerido. Note que a eliminação suave está sempre acesa. Por conseguinte, uma chave eliminada permanecerá em estado de suprido e poderá ser recuperada até que o número de dias de retenção tenha passado quando a chave for purgada (permanentemente eliminada) sem possibilidade de recuperação.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Lista de chaves eliminadas

Utilize `az keyvault key list-deleted` o comando para listar todas as chaves em estado eliminado no seu HSM gerido.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Recuperar (nãodelete) uma chave eliminada

Utilize `az keyvault key list-deleted` o comando para listar todas as chaves em estado eliminado no seu HSM gerido. Se precisar de recuperar (desdelete) uma chave utilizando o parâmetro de id enquanto recupera uma chave eliminada, deve ter em conta o `recoveryId` valor da chave eliminada obtida a partir do `az keyvault key list-deleted` comando.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Purgue (apagar permanentemente) uma chave

Utilize `az keyvault key purge` o comando para limpar (eliminar permanentemente) uma chave.

> [!NOTE]
> Se o HSM gerido tiver uma proteção de purga ativada, não será permitida a operação de purga. A chave será automaticamente purgada quando o período de retenção tiver passado.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Criar uma única cópia de segurança da chave

Use `az keyvault key backup` para criar uma cópia de segurança. O ficheiro de backup é uma bolha encriptada criptograficamente ligada ao domínio de segurança da fonte HSM. Só pode ser restaurado em HSMs que partilham o mesmo domínio de segurança. Leia mais sobre [o Domínio de Segurança.](security-domain.md)

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Restaurar uma única chave a partir de backup

Use `az keyvault key restore` para restaurar uma única chave. A fonte HSM onde a cópia de segurança foi criada deve partilhar o mesmo domínio de segurança que o HSM alvo onde a chave está a ser restaurada.

> [!NOTE]
> A restauração não terá sucesso se existir uma chave com o mesmo nome em estado ativo ou eliminado.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Importar uma chave de um ficheiro

Utilize `az keyvault key import` o comando para importar uma chave (apenas RSA e CE) de um ficheiro. O ficheiro de certificado deve ter chave privada e deve utilizar codificação PEM (tal como definido nos RFCs [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423), [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Para importar uma chave do seu HSM no local para o HSM gerido, consulte [as chaves protegidas pelo Import HSM para o Gerido HSM (BYOK)](hsm-protected-keys-byok.md)

## <a name="next-steps"></a>Passos seguintes

- Para obter uma referência completa do Azure CLI para comandos de cofre de chaves, consulte [a referência CLI do Cofre-Chave](/cli/azure/keyvault).
- Para obter referências de programação, consulte [o guia do desenvolvedor do Azure Key Vault](../general/developers-guide.md)
- Saiba mais sobre [gestão de funções gerida do HSM](role-management.md)
- Saiba mais sobre [as melhores práticas geridas do HSM](best-practices.md)
