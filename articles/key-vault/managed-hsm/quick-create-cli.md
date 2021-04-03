---
title: Quickstart - Provisão e ativação de um HSM gerido Azure
description: Quickstart mostrando como provisões e ativar um HSM gerido usando O Azure CLI
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: quickstart
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 86d0a336a7d3f5d12ed8e53de802616f839f9eba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "91756825"
---
# <a name="quickstart-provision-and-activate-a-managed-hsm-using-azure-cli"></a>Quickstart: Provisão e ativação de um HSM gerido utilizando O Azure CLI

Azure Key Vault Managed HSM é um serviço de nuvem totalmente gerido, altamente disponível, de inquilino único, que permite proteger chaves criptográficas para as suas aplicações em nuvem, utilizando HSMs validados **FIPS 140-2 Nível 3.** Para obter mais informações sobre o HSM gerido, poderá rever a [Visão Geral](overview.md). 

Neste arranque rápido, cria-se e ativa um HSM gerido com O Azure CLI. Depois de concluir este passo, irá armazenar um segredo.

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

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é um contentor lógico no qual os recursos do Azure são implementados e geridos. O exemplo a seguir cria um grupo de recursos chamado *ContosoResourceGroup* na localização *eastus2.*

```azurecli-interactive
az group create --name "ContosoResourceGroup" --location eastus2
```

## <a name="create-a-managed-hsm"></a>Criar um HSM gerido

Criar um HSM gerido é um processo de dois passos:
1. Fornecimento de um recurso Gerido HSM.
1. Ative o seu HSM gerido descarregando o domínio de segurança.

### <a name="provision-a-managed-hsm"></a>Provisão de um HSM gerido

Utilize o `az keyvault create` comando para criar um HSM gerido. Este script tem três parâmetros obrigatórios: um nome de grupo de recursos, um nome HSM e a localização geográfica.

Tem de fornecer as seguintes entradas para criar um recurso Gerido HSM:
- Um grupo de recursos onde será colocado na sua subscrição.
- Local azul.
- Uma lista de administradores iniciais.

O exemplo abaixo cria um HSM chamado **ContosoMHSM,** no grupo de recursos  **ContosoResourceGroup,** residente na **localização East US 2,** com **o atual assinado no utilizador** como o único administrador.

```azurecli-interactive
oid=$(az ad signed-in-user show --query objectId -o tsv)
az keyvault create --hsm-name "ContosoMHSM" --resource-group "ContosoResourceGroup" --location "East US 2" --administrators $oid
```

> [!NOTE]
> Criar comando pode demorar alguns minutos. Assim que voltar com sucesso, está pronto para ativar o seu HSM.

A saída deste comando mostra propriedades do HSM gerido que criou. As duas propriedades mais importantes são:

* **nome**: No exemplo, o nome é ContosoMHSM. Vais usar este nome para outros comandos do Key Vault.
* **hsmUri**: No exemplo, o URI é ' https://contosohsm.managedhsm.azure.net .' As aplicações que utilizam o seu HSM através da sua API REST devem utilizar este URI.

A sua conta Azure está agora autorizada a realizar quaisquer operações neste HSM gerido. Até agora, ninguém mais está autorizado.

### <a name="activate-your-managed-hsm"></a>Ativar o seu HSM gerido

Todos os comandos do plano de dados são desativados até que o HSM seja ativado. Não será capaz de criar chaves ou atribuir funções. Apenas os administradores designados que foram designados durante o comando de criação podem ativar o HSM. Para ativar o HSM tem de descarregar o [Domínio de Segurança](security-domain.md).

Para ativar o seu HSM, precisa de:
- Mínimos 3 pares de chaves RSA (máximo 10)
- Especificar o número mínimo de chaves necessárias para desencriptar o domínio de segurança (quórum)

Para ativar o HSM, envia pelo menos 3 (máximo 10) chaves públicas RSA para o HSM. O HSM encripta o domínio de segurança com estas chaves e envia-o de volta. Uma vez que este download de domínio de segurança esteja concluído com sucesso, o seu HSM está pronto a ser utilizado. Também precisa de especificar o quórum, que é o número mínimo de chaves privadas necessárias para desencriptar o domínio de segurança.

O exemplo abaixo mostra como usar  `openssl` para gerar 3 certificado auto-assinado.

```azurecli-interactive
openssl req -newkey rsa:2048 -nodes -keyout cert_0.key -x509 -days 365 -out cert_0.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_1.key -x509 -days 365 -out cert_1.cer
openssl req -newkey rsa:2048 -nodes -keyout cert_2.key -x509 -days 365 -out cert_2.cer
```

> [!IMPORTANT]
> Crie e guarde os pares de chaves RSA e o ficheiro de domínio de segurança gerados neste passo de forma segura.

Utilize o `az keyvault security-domain download` comando para descarregar o domínio de segurança e ativar o seu HSM gerido. O exemplo abaixo, utiliza 3 pares de chaves RSA (apenas são necessárias chaves públicas para este comando) e define o quórum para 2.

```azurecli-interactive
az keyvault security-domain download --hsm-name ContosoMHSM --sd-wrapping-keys ./certs/cert_0.cer ./certs/cert_1.cer ./certs/cert_2.cer --sd-quorum 2 --security-domain-file ContosoMHSM-SD.json
```

Por favor, guarde o ficheiro de domínio de segurança e os pares de chaves RSA de forma segura. Você precisará deles para recuperação de desastres ou para criar outro HSM gerido que partilhe o mesmo domínio de segurança, para que possam partilhar chaves.

Depois de descarregar com sucesso o domínio de segurança, o seu HSM estará em estado ativo e pronto para utilizar.

## <a name="clean-up-resources"></a>Limpar os recursos

Outros inícios rápidos e tutoriais desta coleção têm por base este início rápido. Se quiser continuar a trabalhar com os inícios rápidos e tutoriais subsequentes, pode manter estes recursos.

Quando já não forem necessários, pode utilizar o comando [az group delete](/cli/azure/group) para remover o grupo de recursos e todos os recursos relacionados. Pode eliminar os recursos da seguinte forma:

```azurecli-interactive
az group delete --name ContosoResourceGroup
```

## <a name="next-steps"></a>Passos seguintes

Neste arranque rápido criaste um Cofre-Chave e guardaste um segredo nele. Para saber mais sobre o Key Vault e como integrá-lo com as suas aplicações, continue para os artigos abaixo.

- Leia uma [visão geral do HSM gerido](overview.md)
- Saiba mais [sobre gerir chaves num HSM gerido](key-management.md)
- Rever [as melhores práticas do HSM gerido](best-practices.md)
