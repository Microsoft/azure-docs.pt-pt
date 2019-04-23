---
title: OpenShift em pré-requisitos do Azure | Documentos da Microsoft
description: Pré-requisitos para implementar o OpenShift no Azure.
services: virtual-machines-linux
documentationcenter: virtual-machines
author: haroldwongms
manager: mdotson
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 04/19/2019
ms.author: haroldw
ms.openlocfilehash: d8a9b82e51c837af6343ddf851545d02299aa527
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/22/2019
ms.locfileid: "60001654"
---
# <a name="common-prerequisites-for-deploying-openshift-in-azure"></a>Pré-requisitos comuns para implementar o OpenShift no Azure

Este artigo descreve os pré-requisitos comuns para implementar o OpenShift Container Platform ou OKD no Azure.

A instalação do OpenShift utiliza playbooks do Ansible. Ansible utiliza Secure Shell (SSH) para ligar a todos os anfitriões de cluster para concluir os passos de instalação.

Quando o ansible faz a ligação de SSH para hosts remotos, ele não é possível introduzir uma palavra-passe. Por esse motivo, a chave privada não pode ter uma palavra-passe (frase de acesso) associada a ele ou falha de implementação.

Uma vez que as máquinas virtuais (VMs) implementar através de modelos Azure Resource Manager, a mesma chave pública é utilizada para acesso a todas as VMs. A chave privada correspondente tem de ser na VM que executa todos os playbooks também. Para efetuar esta ação de forma segura, um cofre de chave do Azure é utilizado para passar a chave privada para a VM.

Se for necessário para armazenamento persistente para contentores, volumes persistentes são necessários. OpenShift suporta discos rígidos virtuais (VHDs) do Azure para volumes persistentes, mas tem de ser configurado primeiro Azure como o fornecedor de cloud.

Nesse modelo, o OpenShift:

- Cria um objeto VHD numa conta de armazenamento do Azure ou um disco gerido.
- Monta o VHD para uma VM e formata o volume.
- Monta o volume para o pod.

Para esta configuração funcione, OpenShift necessita de permissões para executar essas tarefas no Azure. Um principal de serviço é utilizado para esta finalidade. O principal de serviço é uma conta de segurança no Azure Active Directory que são concedidas permissões para recursos.

O principal de serviço tem de ter acesso às contas de armazenamento e as VMs que compõem o cluster. Se todos os recursos de cluster do OpenShift implementar um grupo de recursos, o principal de serviço pode ser concedido permissões para esse grupo de recursos.

Este guia descreve como criar os artefactos associados com os pré-requisitos.

> [!div class="checklist"]
> * Crie um cofre de chaves para gerir as chaves SSH para o cluster do OpenShift.
> * Crie um principal de serviço para utilização pelo fornecedor de nuvem do Azure.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure 
Inicie sessão na sua subscrição do Azure com o [início de sessão az](/cli/azure/reference-index) comando e siga na tela direções, ou clique em **experimentá-la** para utilizar o Cloud Shell.

```azurecli 
az login
```
## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Crie um grupo de recursos com o comando [az group create](/cli/azure/group). Um grupo de recursos do Azure é um contentor lógico no qual os recursos do Azure são implementados e geridos. Deve utilizar um grupo de recursos dedicado para alojar o Cofre de chaves. Este grupo é separado do grupo de recursos no qual implementar os recursos do cluster OpenShift.

O exemplo seguinte cria um grupo de recursos chamado *keyvaultrg* no *eastus* localização:

```azurecli 
az group create --name keyvaultrg --location eastus
```

## <a name="create-a-key-vault"></a>Criar um cofre de chaves
Criar um cofre de chaves para armazenar as chaves SSH para o cluster com o [az keyvault criar](/cli/azure/keyvault) comando. O nome do Cofre de chaves tem de ser globalmente exclusivo.

O exemplo seguinte cria um cofre de chaves com o nome *keyvault* no *keyvaultrg* grupo de recursos:

```azurecli 
az keyvault create --resource-group keyvaultrg --name keyvault \
       --enabled-for-template-deployment true \
       --location eastus
```

## <a name="create-an-ssh-key"></a>Criar uma chave SSH 
Uma chave SSH, é necessário para proteger o acesso ao OpenShift cluster. Criar um par de chaves SSH com o `ssh-keygen` comando (no Linux ou macOS):
 
 ```bash
ssh-keygen -f ~/.ssh/openshift_rsa -t rsa -N ''
```

> [!NOTE]
> O par de chaves de SSH não pode ter uma palavra-passe / frase de acesso.

Para obter mais informações sobre chaves SSH no Windows, consulte [chaves de como criar SSH no Windows](/azure/virtual-machines/linux/ssh-from-windows). Certifique-se de que exportar a chave privada no formato OpenSSH.

## <a name="store-the-ssh-private-key-in-azure-key-vault"></a>Store a chave privada SSH no Azure Key Vault
A implementação do OpenShift utiliza a chave SSH criado para proteger o acesso ao mestre de OpenShift. Para ativar a implementação obter a chave SSH de forma segura, armazene a chave no Cofre de chaves utilizando o seguinte comando:

```azurecli
az keyvault secret set --vault-name keyvault --name keysecret --file ~/.ssh/openshift_rsa
```

## <a name="create-a-service-principal"></a>Criar um principal de serviço 
OpenShift comunica com o Azure através de um nome de utilizador e palavra-passe ou um principal de serviço. Um principal de serviço do Azure é uma identidade de segurança que pode utilizar com aplicações, serviços e ferramentas de automatização, como o OpenShift. Pode controlar e define as permissões em relação às quais operações o principal de serviço pode executar no Azure. É melhor definir o âmbito as permissões de serviço principal para grupos de recursos específicos, em vez da subscrição completa.

Criar um serviço principal com [az ad sp create-for-rbac](/cli/azure/ad/sp) e as credenciais que precisa de OpenShift de saída.

O exemplo seguinte cria um serviço principal e o atribui permissões de contribuinte a um grupo de recursos com o nome openshiftrg.

Primeiro, crie o grupo de recursos com o nome openshiftrg:

```azurecli
az group create -l eastus -n openshiftrg
```

Crie principal de serviço:

```azurecli
scope=`az group show --name openshiftrg --query id`
az ad sp create-for-rbac --name openshiftsp \
      --role Contributor --password {Strong Password} \
      --scopes $scope
```
Se estiver a utilizar o Windows, execute ```az group show --name openshiftrg --query id``` e usar o resultado em vez de $scope.

Tome nota da propriedade appId devolvida do comando:
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
 > Certifique-se de que criar uma palavra-passe segura. Siga as instruções em [Azure AD password rules and restrictions](/azure/active-directory/active-directory-passwords-policy) (Regras e limitações de palavras-passe do Azure AD).

Para obter mais informações sobre principais de serviço, consulte [criar um Azure principal de serviço com a CLI do Azure](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli?view=azure-cli-latest).

## <a name="prerequisites-applicable-only-to-resource-manager-template"></a>Pré-requisitos aplicáveis apenas ao modelo do Resource Manager

Segredos tem de ser criado para a chave privada SSH (**sshPrivateKey**), segredo do cliente do Azure AD (**aadClientSecret**), palavra-passe de administrador do OpenShift (**openshiftPassword** ) e a chave de palavra-passe ou a ativação do Gestor de subscrições do Red Hat (**rhsmPasswordOrActivationKey**).  Além disso, se forem utilizados certificados SSL personalizados, em seguida, seis segredos adicionais serão necessário criar - **routingcafile**, **routingcertfile**, **routingkeyfile**, **mastercafile**, **mastercertfile**, e **masterkeyfile**.  Estes parâmetros serão explicados mais detalhadamente.

O modelo faz referência específicos nomes secretos por isso, **tem** utilize os nomes de negrito listados acima (sensível a maiúsculas e minúsculas).

### <a name="custom-certificates"></a>Certificados personalizados

Por predefinição, o modelo irá implementar um cluster de OpenShift com certificados autoassinados para a consola web do OpenShift e o domínio de encaminhamento. Se quiser utilizar certificados SSL personalizados, defina 'routingCertType' para 'custom' e 'masterCertType' para 'custom'.  Terá dos ficheiros de AC, certificado e chave no formato. pem para os certificados.  É possível utilizar certificados personalizados para uma, mas não na outra.

Precisará armazenar esses arquivos em segredos do Key Vault.  Utilize o mesmo Cofre de chaves que utilizou para a chave privada.  Em vez de exigir 6 entradas adicionais para os nomes secretos, o modelo está hard-coded para utilizar os nomes secretos específicos para cada um dos ficheiros de certificado SSL.  Store os dados de certificado utilizando as informações da tabela seguinte.

| Nome do segredo      | Ficheiro de certificado   |
|------------------|--------------------|
| mastercafile     | ficheiro de AC principal     |
| mastercertfile   | ficheiro de certificado principal   |
| masterkeyfile    | ficheiro de chave mestra    |
| routingcafile    | ficheiro de AC de encaminhamento    |
| routingcertfile  | Encaminhamento ficheiro de certificado  |
| routingkeyfile   | ficheiro da chave de encaminhamento   |

Crie os segredos com a CLI do Azure. Segue-se um exemplo.

```bash
az keyvault secret set --vault-name KeyVaultName -n mastercafile --file ~/certificates/masterca.pem
```

## <a name="next-steps"></a>Passos Seguintes

Este artigo abordou os seguintes tópicos:
> [!div class="checklist"]
> * Crie um cofre de chaves para gerir as chaves SSH para o cluster do OpenShift.
> * Crie um principal de serviço para utilização pelo fornecedor de soluções de Cloud do Azure.

Em seguida, implemente um cluster do OpenShift:

- [Implementar o OpenShift Container Platform](./openshift-container-platform.md)
- [Implementar o OpenShift Container Platform gestão automática centrada no Marketplace oferta](./openshift-marketplace-self-managed.md)