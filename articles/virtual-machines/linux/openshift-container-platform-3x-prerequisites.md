---
title: Plataforma de contêiner OpenShift 3,11 em pré-requisitos do Azure
description: Pré-requisitos para implantar a plataforma de contêiner OpenShift 3,11 no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 069561c4bed55bf6021b594d693e076ef8d313bd
ms.sourcegitcommit: 49cf9786d3134517727ff1e656c4d8531bbbd332
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/13/2019
ms.locfileid: "74035464"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Pré-requisitos comuns para implantar a plataforma de contêiner do OpenShift 3,11 no Azure

Este artigo descreve os pré-requisitos comuns para implantar a plataforma de contêiner do OpenShift ou o OKD no Azure.

A instalação do OpenShift usa os guias estratégicos do Ansible. O Ansible usa Secure Shell (SSH) para se conectar a todos os hosts de cluster para concluir as etapas de instalação.

Quando o Ansible faz a conexão SSH com os hosts remotos, ele não pode inserir uma senha. Por esse motivo, a chave privada não pode ter uma senha (frase secreta) associada a ela ou a implantação falha.

Como as VMs (máquinas virtuais) são implantadas por meio de modelos de Azure Resource Manager, a mesma chave pública é usada para acesso a todas as VMs. A chave privada correspondente deve estar na VM que executa todos os guias estratégicos também. Para executar essa ação com segurança, um cofre de chaves do Azure é usado para passar a chave privada para a VM.

Se houver uma necessidade de armazenamento persistente para contêineres, os volumes persistentes serão necessários. O OpenShift dá suporte a VHDs (discos rígidos virtuais) do Azure para volumes persistentes, mas o Azure deve primeiro ser configurado como o provedor de nuvem.

Neste modelo, OpenShift:

- Cria um objeto VHD em uma conta de armazenamento do Azure ou em um disco gerenciado.
- Monta o VHD em uma VM e formata o volume.
- Monta o volume para o pod.

Para que essa configuração funcione, o OpenShift precisa de permissões para executar essas tarefas no Azure. Uma entidade de serviço é usada para essa finalidade. A entidade de serviço é uma conta de segurança no Azure Active Directory que recebe permissões para recursos.

A entidade de serviço precisa ter acesso às contas de armazenamento e às VMs que compõem o cluster. Se todos os recursos de cluster do OpenShift forem implantados em um único grupo de recursos, a entidade de serviço poderá receber permissões para esse grupo de recursos.

Este guia descreve como criar os artefatos associados aos pré-requisitos.

> [!div class="checklist"]
> * Crie um cofre de chaves para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma entidade de serviço para uso pelo provedor de nuvem do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Entre em sua assinatura do Azure com o comando [AZ login](/cli/azure/reference-index) e siga as instruções na tela ou clique em **experimente** para usar Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos:

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Você deve usar um grupo de recursos dedicado para hospedar o cofre de chaves. Esse grupo é separado do grupo de recursos no qual os recursos de cluster do OpenShift são implantados.

O exemplo a seguir cria um grupo de recursos chamado *keyvaultrg* no local *eastus* :

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Crie um cofre de chaves para armazenar as chaves SSH para o cluster com o comando [AZ keyvault Create](/cli/azure/keyvault) . O nome do cofre de chaves deve ser globalmente exclusivo e deve ser habilitado para implantação de modelo, ou a implantação falhará com o erro "KeyVaultParameterReferenceSecretRetrieveFailed".

O exemplo a seguir cria um cofre de chaves chamado *keyvault* no grupo de recursos *keyvaultrg* :

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
Uma chave SSH é necessária para proteger o acesso ao cluster OpenShift. Crie um par de chaves SSH usando o comando `ssh-keygen` (no Linux ou macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves SSH não pode ter uma senha/frase secreta.

Para obter mais informações sobre chaves SSH no Windows, consulte [como criar chaves SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows). Certifique-se de exportar a chave privada no formato OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Armazenar a chave privada SSH no Azure Key Vault
A implantação do OpenShift usa a chave SSH que você criou para proteger o acesso ao mestre do OpenShift. Para habilitar a implantação para recuperar com segurança a chave SSH, armazene a chave em Key Vault usando o seguinte comando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço 
O OpenShift se comunica com o Azure usando um nome de usuário e senha ou uma entidade de serviço. Uma entidade de serviço do Azure é uma identidade de segurança que você pode usar com aplicativos, serviços e ferramentas de automação como o OpenShift. Você controla e define as permissões para quais operações a entidade de serviço pode executar no Azure. É melhor fazer o escopo das permissões da entidade de serviço para grupos de recursos específicos em vez de toda a assinatura.

Crie uma entidade de serviço com [AZ ad SP Create-for-RBAC](/cli/azure/ad/sp) e gere as credenciais que o OpenShift precisa.

O exemplo a seguir cria uma entidade de serviço e atribui permissões de colaborador de ti a um grupo de recursos chamado openshiftrg.

Primeiro, crie o grupo de recursos chamado openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Criar entidade de serviço:

```azurecli
az group show --name openshiftrg --query id
```
Salvar a saída do comando e usar no lugar de $scope no próximo comando

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Anote a propriedade appId e a senha retornada do comando:
```json
{
  "appId": "11111111-abcd-1234-efgh-111111111111",
  "displayName": "openshiftsp",
  "name": "http://openshiftsp",
  "password": {Strong Password},
  "tenant": "XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX"
}
```
 > [!WARNING] 
 > Certifique-se de anotar a senha segura, pois não será possível recuperar essa senha novamente.

Para obter mais informações sobre entidades de serviço, consulte [criar uma entidade de serviço do Azure com CLI do Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Pré-requisitos aplicáveis somente ao modelo do Resource Manager

Os segredos precisarão ser criados para a chave privada SSH (**sshPrivateKey**), o segredo do cliente do Azure AD (**aadClientSecret**), a senha de administrador do OpenShift (**openshiftPassword**) e a senha ou a chave de ativação do Red Hat Subscription Manager (**rhsmPasswordOrActivationKey**).  Além disso, se forem usados certificados SSL personalizados, seis segredos adicionais precisarão ser criados- **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**e **masterkeyfile**.  Esses parâmetros serão explicados em mais detalhes.

O modelo referencia nomes de segredo específicos, portanto, você **deve** usar os nomes em negrito listados acima (diferencia maiúsculas de minúsculas).

### <a name="custom-certificates"></a>Certificados personalizados

Por padrão, o modelo implantará um cluster OpenShift usando certificados autoassinados para o console Web do OpenShift e o domínio de roteamento. Se você quiser usar certificados SSL personalizados, defina ' routingCertType ' como ' Custom ' e ' masterCertType ' como ' Custom '.  Você precisará da autoridade de certificação, do certificado e dos arquivos de chave no formato. pem para os certificados.  É possível usar certificados personalizados para um, mas não para o outro.

Você precisará armazenar esses arquivos em Key Vault segredos.  Use o mesmo Key Vault como aquele usado para a chave privada.  Em vez de exigir 6 entradas adicionais para os nomes de segredo, o modelo é embutido em código para usar nomes de segredo específicos para cada um dos arquivos de certificado SSL.  Armazene os dados do certificado usando as informações da tabela a seguir.

| Nome do segredo      | Arquivo de certificado   |
|------------------|--------------------|
| mastercafile     | arquivo de AC mestre     |
| mastercertfile   | arquivo de certificado mestre   |
| masterkeyfile    | arquivo de chave mestra    |
| routingcafile    | arquivo de AC de roteamento    |
| routingcertfile  | arquivo de certificado de roteamento  |
| routingkeyfile   | arquivo de chave de roteamento   |

Crie os segredos usando o CLI do Azure. Veja abaixo um exemplo.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Passos seguintes

Este artigo abordou os seguintes tópicos:
> [!div class="checklist"]
> * Crie um cofre de chaves para gerenciar chaves SSH para o cluster OpenShift.
> * Crie uma entidade de serviço para uso pelo provedor de soluções de nuvem do Azure.

Em seguida, implante um cluster OpenShift:

- [Implantar plataforma de contêiner OpenShift](./openshift-container-platform-3x.md)
- [Implantar a oferta do Marketplace autogerenciado da plataforma de contêiner do OpenShift](./openshift-container-platform-3x-marketplace-self-managed.md)
