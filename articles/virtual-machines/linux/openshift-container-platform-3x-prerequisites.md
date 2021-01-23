---
title: Plataforma de contentores OpenShift 3.11 em pré-requisitos Azure
description: Pré-requisitos para implantar a Plataforma de Contentores OpenShift 3.11 em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 51f6a2ac4f524ac2a504fb8e0c3dd90ec25c9f93
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/23/2021
ms.locfileid: "98734735"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Pré-requisitos comuns para a implantação da Plataforma de Contentores OpenShift 3.11 em Azure

Este artigo descreve pré-requisitos comuns para a implantação da Plataforma de Contentores OpenShift ou OKD em Azure.

A instalação do OpenShift utiliza livros de reprodução Ansible. A Ansible utiliza a Secure Shell (SSH) para ligar a todos os anfitriões do cluster para completar os passos de instalação.

Quando o ansível faz a ligação SSH aos anfitriões remotos, não pode introduzir uma palavra-passe. Por esta razão, a chave privada não pode ter uma palavra-passe (palavra-passe) associada a ela ou falha na implementação.

Como as máquinas virtuais (VMs) são implantadas através dos modelos Azure Resource Manager, a mesma chave pública é usada para o acesso a todos os VMs. A chave privada correspondente deve estar no VM que executa todos os livros de jogadas também. Para realizar esta ação de forma segura, um cofre de chave Azure é usado para passar a chave privada para o VM.

Se houver necessidade de armazenamento persistente para os recipientes, então são necessários volumes persistentes. O OpenShift suporta discos rígidos virtuais (VHDs) virtuais (VHDs) para volumes persistentes, mas o Azure tem primeiro de ser configurado como provedor de nuvem.

Neste modelo, OpenShift:

- Cria um objeto VHD numa conta de armazenamento Azure ou num disco gerido.
- Monta o VHD num VM e forma o volume.
- Monta o volume na cápsula.

Para que esta configuração funcione, o OpenShift necessita de permissões para executar estas tarefas em Azure. Para este fim é utilizado um diretor de serviço. O diretor de serviço é uma conta de segurança no Azure Ative Directory que é concedida permissões aos recursos.

O diretor de serviço precisa de ter acesso às contas de armazenamento e VMs que compõem o cluster. Se todos os recursos de cluster OpenShift forem implantados num único grupo de recursos, o principal de serviço pode ser autorizado a esse grupo de recursos.

Este guia descreve como criar os artefactos associados aos pré-requisitos.

> [!div class="checklist"]
> * Crie um cofre chave para gerir as chaves SSH para o cluster OpenShift.
> * Crie um principal de serviço para uso pelo Azure Cloud Provider.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão na sua subscrição Azure com o comando [de login az](/cli/azure/reference-index) e siga as instruções no ecrã, ou clique em **Experimentá-lo** para usar Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Deve usar um grupo de recursos dedicado para hospedar o cofre de chaves. Este grupo é separado do grupo de recursos no qual os recursos de cluster OpenShift se implantam.

O exemplo a seguir cria um grupo de recursos chamado *keyvaultrg* na localização *este:*

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Crie um cofre chave para armazenar as teclas SSH para o cluster com o [comando de criação de chave az.](/cli/azure/keyvault) O nome do cofre de chaves deve ser globalmente único e deve ser ativado para a implementação do modelo ou a implementação falhará com o erro "KeyVaultParameterReferenceReretrieveFailed".

O exemplo a seguir cria um cofre-chave denominado *keyvault* no grupo de recursos *keyvaultrg:*

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
É necessária uma chave SSH para garantir o acesso ao cluster OpenShift. Criar um par de chaves SSH utilizando o `ssh-keygen` comando (em Linux ou macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O seu par de chaves SSH não pode ter uma palavra-passe / palavra-passe.

Para obter mais informações sobre as teclas SSH no Windows, consulte [como criar as teclas SSH no Windows](./ssh-from-windows.md). Certifique-se de exportar a chave privada no formato OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Guarde a chave privada SSH no Cofre da Chave Azure
A implementação OpenShift utiliza a chave SSH criada para garantir o acesso ao mestre OpenShift. Para ativar a implantação para recuperar de forma segura a chave SSH, guarde a chave no Cofre de Chaves utilizando o seguinte comando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço 
O OpenShift comunica com o Azure utilizando um nome de utilizador e senha ou um principal de serviço. Um diretor de serviço Azure é uma identidade de segurança que pode usar com apps, serviços e ferramentas de automação como o OpenShift. Você controla e define as permissões sobre quais as operações que o diretor de serviço pode realizar em Azure. É melhor estender as permissões do principal serviço a grupos de recursos específicos em vez de toda a subscrição.

Crie um diretor de serviço com [a ad sp create-for-rbac](/cli/azure/ad/sp) e produza as credenciais de que o OpenShift precisa.

O exemplo a seguir cria um principal de serviço e atribui-lhe permissões de contribuinte a um grupo de recursos chamado *openshiftrg*.

Em primeiro lugar, crie o grupo de recursos chamado *openshiftrg:*

```azurecli
az group create -l eastus -n openshiftrg
```

Criar o principal do serviço:

```azurecli
az group show --name openshiftrg --query id
```

Salve a saída do comando e use no lugar de $scope no próximo comando

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Tome nota da propriedade appId e senha devolvida do comando:

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
 > Certifique-se de que escreve a palavra-passe segura, uma vez que não será possível recuperar novamente esta palavra-passe.

Para obter mais informações sobre os principais serviços, consulte [Criar um diretor de serviço Azure com a Azure CLI.](/cli/azure/create-an-azure-service-principal-azure-cli)

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Pré-requisitos aplicáveis apenas ao modelo de Gestor de Recursos

Os segredos terão de ser criados para a chave privada **SSH (sshPrivateKey),** Azure AD cliente secret **(aadClientSecret),** palavra-passe de administração OpenShift **(palavra de aberturaPassword)** e palavra-passe ou chave de ativação do Red Hat Subscription Manager **(rhsmPasswordOrActivationKey).**  Além disso, se forem utilizados certificados TLS/SSL personalizados, terão de ser criados seis segredos adicionais - **ficheiro de encaminhamento,** **ficheiro de encaminhamento,** **ficheiro de encaminhamento,** **mastercafile,** **mastercerte** e **masterkeyfile**.  Estes parâmetros serão explicados mais detalhadamente.

O modelo refere nomes secretos específicos, pelo que **deve** utilizar os nomes arrojados listados acima (sensíveis a casos).

### <a name="custom-certificates"></a>Certificados Personalizados

Por predefinição, o modelo irá implantar um cluster OpenShift utilizando certificados auto-assinados para a consola web OpenShift e o domínio de encaminhamento. Se pretender utilizar certificados TLS/SSL personalizados, desaprova 'encaminhamentoCertType' para 'personalizado' e 'masterCertType' para 'personalizado'.  Você precisará dos ficheiros CA, Cert e Key em formato .pem para os certificados.  É possível utilizar certificados personalizados para um, mas não para o outro.

Tens de guardar estes ficheiros em segredos do Key Vault.  Use o mesmo Cofre de Chaves que o usado para a chave privada.  Em vez de exigir 6 entradas adicionais para os nomes secretos, o modelo é codificado para usar nomes secretos específicos para cada um dos ficheiros de certificadoS TLS/SSL.  Armazenar os dados do certificado utilizando as informações a partir da tabela seguinte.

| Nome secreto      | Ficheiro de certificado   |
|------------------|--------------------|
| mastercafile     | arquivo ca mestre     |
| mastercertfile   | arquivo mestre CERT   |
| masterkeyfile    | arquivo chave mestre    |
| ficheiro de encaminhamento    | encaminhamento arquivo CA    |
| encaminhamento de certificados  | encaminhamento CERT  |
| ficheiro de encaminhamento   | ficheiro chave de encaminhamento   |

Crie os segredos usando o Azure CLI. Abaixo está um exemplo.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Próximos passos

Este artigo abordou os seguintes tópicos:
> [!div class="checklist"]
> * Crie um cofre chave para gerir as chaves SSH para o cluster OpenShift.
> * Crie um principal de serviço para utilização pelo Azure Cloud Solution Provider.

Em seguida, implementar um cluster OpenShift:

- [Implementar plataforma de recipientes de openshift](./openshift-container-platform-3x.md)
- [Implementar plataforma de recipientes abertos Self-Managed oferta de mercado](./openshift-container-platform-3x-marketplace-self-managed.md)
