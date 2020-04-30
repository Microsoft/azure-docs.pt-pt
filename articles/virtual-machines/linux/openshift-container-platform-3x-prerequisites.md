---
title: Plataforma de contentores OpenShift 3.11 em pré-requisitos do Azure
description: Pré-requisitos para implantar a Plataforma de Contentores OpenShift 3.11 em Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.workload: infrastructure
ms.date: 10/23/2019
ms.author: haroldw
ms.openlocfilehash: 26b190515819378309c2b0705efdbc349ecccbe2
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81759503"
---
# <a name="common-prerequisites-for-deploying-openshift-container-platform-311-in-azure"></a>Pré-requisitos comuns para a implantação da Plataforma de Contentores OpenShift 3.11 em Azure

Este artigo descreve pré-requisitos comuns para a implementação da Plataforma de Contentores OpenShift ou OKD em Azure.

A instalação do OpenShift utiliza playbooks Ansible. O Ansible utiliza a Secure Shell (SSH) para ligar a todos os anfitriões do cluster para completar os passos de instalação.

Quando o ansível faz a ligação SSH aos anfitriões remotos, não pode introduzir uma senha. Por esta razão, a chave privada não pode ter uma palavra-passe (palavra-passe) associada a esta ou falha na implementação.

Como as máquinas virtuais (VMs) implantam através de modelos do Gestor de Recursos Azure, a mesma chave pública é usada para acesso a todos os VMs. A chave privada correspondente deve estar no VM que executa todos os playbooks também. Para realizar esta ação de forma segura, um cofre chave Azure é usado para passar a chave privada para o VM.

Se houver necessidade de armazenamento persistente para os recipientes, então são necessários volumes persistentes. O OpenShift suporta discos rígidos virtuais Azure (VHDs) para volumes persistentes, mas o Azure tem primeiro de ser configurado como fornecedor de nuvem.

Neste modelo, OpenShift:

- Cria um objeto VHD numa conta de armazenamento Azure ou num disco gerido.
- Monta o VHD num VM e formata o volume.
- Aumenta o volume para a cápsula.

Para que esta configuração funcione, o OpenShift necessita de permissões para executar estas tarefas no Azure. Para o efeito, é utilizado um diretor de serviço. O diretor de serviço é uma conta de segurança no Azure Ative Directory que é concedida permissões aos recursos.

O diretor de serviço precisa de ter acesso às contas de armazenamento e VMs que compõem o cluster. Se todos os recursos de cluster OpenShift se implantarem num único grupo de recursos, o diretor de serviço pode ser autorizado a esse grupo de recursos.

Este guia descreve como criar os artefactos associados aos pré-requisitos.

> [!div class="checklist"]
> * Crie um cofre chave para gerir as teclas SSH para o cluster OpenShift.
> * Crie um diretor de serviço para utilização pelo Azure Cloud Provider.

Se não tiver uma subscrição Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão na subscrição do Azure com o comando [de login az](/cli/azure/reference-index) e siga as instruções no ecrã ou clique em **experimentá-la** para utilizar a Cloud Shell.

```azurecli
az login
```

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Devias usar um grupo de recursos dedicado para alojar o cofre chave. Este grupo é separado do grupo de recursos em que os recursos de cluster OpenShift se implantam.

O exemplo seguinte cria um grupo de recursos chamado *keyvaultrg* na localização *oriental:*

```azurecli
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Crie um cofre chave para armazenar as chaves SSH para o cluster com o [cofre az criar](/cli/azure/keyvault) comando. O nome do cofre chave deve ser globalmente único e deve ser ativado para a implementação do modelo ou a implementação falhará com o erro "KeyVaultParameterReferenceRetrieveRetrieveFailed".

O exemplo seguinte cria um cofre de chave chamado *keyvault* no grupo de recursos *keyvaultrg:*

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
É necessária uma chave SSH para garantir o acesso ao cluster OpenShift. Crie um par de chaves `ssh-keygen` SSH utilizando o comando (em Linux ou macOS):
 
```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O seu par de chaves SSH não pode ter uma palavra-passe/palavra-passe.

Para obter mais informações sobre as teclas SSH no Windows, consulte [como criar chaves SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows). Certifique-se de exportar a chave privada em formato OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Armazenar a chave privada SSH no Cofre de Chaves Azure
A implementação OpenShift utiliza a chave SSH que criou para garantir o acesso ao master OpenShift. Para permitir que a implementação recupere de forma segura a chave SSH, guarde a chave no Cofre chave utilizando o seguinte comando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço 
O OpenShift comunica com o Azure utilizando um nome de utilizador e uma senha ou um diretor de serviço. Um diretor de serviço Azure é uma identidade de segurança que pode usar com apps, serviços e ferramentas de automação como o OpenShift. Controla e define as permissões sobre quais as operações que o diretor de serviço pode realizar em Azure. É melhor examinar as permissões do principal de serviço para grupos de recursos específicos em vez de toda a subscrição.

Crie um diretor de serviço com [az ad sp criar-para-rbac](/cli/azure/ad/sp) e obter as credenciais de que o OpenShift precisa.

O exemplo seguinte cria um diretor de serviço e atribui-lhe permissões contributivas a um grupo de recursos chamado *openshiftrg*.

Primeiro, crie o grupo de recursos chamado *openshiftrg:*

```azurecli
az group create -l eastus -n openshiftrg
```

Criar o diretor de serviço:

```azurecli
az group show --name openshiftrg --query id
```

Salve a saída do comando e use no lugar de $scope no próximo comando

```azurecli
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --scopes $scope \
```

Tome nota da propriedade appId e palavra-passe devolvida do comando:

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
 > Certifique-se de que anota a palavra-passe segura, uma vez que não será possível recuperar novamente esta palavra-passe.

Para obter mais informações sobre os principais de serviço, consulte Criar um diretor de [serviço Azure com o Azure CLI](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Pré-requisitos aplicáveis apenas ao modelo de Gestor de Recursos

Serão necessários segredos para a chave privada SSH **(sshPrivateKey),** azure AD client secret **(aadClientSecret),** palavra-passe de administração OpenShift **(openshiftPassword)** e senha de subscrição do Red Hat **(rhsmPasswordOrActivationKey).**  Além disso, se forem utilizados certificados TLS/SSL personalizados, terão de ser criados seis segredos adicionais - **routingcafile,** **routingcertfile,** **routingkeyfile,** **mastercafile,** **mastercertfile**e **masterkeyfile**.  Estes parâmetros serão explicados mais detalhadamente.

O modelo refere nomes secretos específicos, pelo que **deve** utilizar os nomes arrojados acima indicados (sensível ao caso).

### <a name="custom-certificates"></a>Certificados Personalizados

Por padrão, o modelo irá implantar um cluster OpenShift utilizando certificados auto-assinados para a consola web OpenShift e o domínio de encaminhamento. Se pretender utilizar certificados TLS/SSL personalizados, detete 'routingCertType' para 'custom' e 'masterCertType' para 'custom'.  Você precisará dos ficheiros CA, Cert e Key em formato .pem para os certificados.  É possível utilizar certificados personalizados para um, mas não para o outro.

Tens de guardar estes ficheiros em segredos do Cofre chave.  Use o mesmo cofre de chaves que o usado para a chave privada.  Em vez de exigir 6 entradas adicionais para os nomes secretos, o modelo é codificado para usar nomes secretos específicos para cada um dos ficheiros de certificadoS TLS/SSL.  Guarde os dados do certificado utilizando as informações da tabela seguinte.

| Nome Secreto      | Ficheiro de certificado   |
|------------------|--------------------|
| mastercafile     | arquivo ca mestre     |
| mestrecertfile   | arquivo cert mestre   |
| masterkeyfile    | arquivo chave mestre    |
| routingcafile    | ficheiro CA de encaminhamento    |
| routingcertfile  | ficheiro CERT de encaminhamento  |
| routingkeyfile   | ficheiro chave de encaminhamento   |

Crie os segredos usando o Azure CLI. Abaixo está um exemplo.

```azurecli
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Passos seguintes

Este artigo abordou os seguintes tópicos:
> [!div class="checklist"]
> * Crie um cofre chave para gerir as teclas SSH para o cluster OpenShift.
> * Crie um diretor de serviço para utilização pelo Fornecedor de Soluções De Nuvem Azure.

Em seguida, implementar um cluster OpenShift:

- [Implementar plataforma de contentores openshift](./openshift-container-platform-3x.md)
- [Implementar oferta de marketplace auto-gerida plataforma de contentores openshift](./openshift-container-platform-3x-marketplace-self-managed.md)
