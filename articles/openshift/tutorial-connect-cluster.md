---
title: Tutorial - Ligue-se a um cluster OpenShift 4 do chapéu vermelho azure
description: Saiba como ligar um cluster OpenShift do Microsoft Azure Red Hat
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: d7efe781f1ba2beb1fa7dd4fdaaad280fc789de2
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/29/2020
ms.locfileid: "82204387"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Ligue-se a um cluster OpenShift 4 do chapéu vermelho azure

Neste tutorial, parte dois de três, iráligar-se a um cluster OpenShift 4 do Chapéu Vermelho Azure que executa o OpenShift 4 como utilizador de kubeadmin através da consola web OpenShift. Saiba como:
> [!div class="checklist"]
> * Obtenha `kubeadmin` credenciais para o seu cluster
> * Instale o CLI OpenShift
> * Ligue-se a um cluster OpenShift do chapéu vermelho azure usando o CLI OpenShift

## <a name="before-you-begin"></a>Antes de começar

Em tutoriais anteriores, foi criado um cluster OpenShift do Chapéu Vermelho Azure. Se não fez estes passos, e gostaria de seguir em frente, comece com [tutorial 1 - Crie um Cluster Openshift 4 do Chapéu Vermelho Azure.](tutorial-create-cluster.md)

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.0.75 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Pode iniciar sessão no `kubeadmin` cluster utilizando o utilizador.  Executar o seguinte comando para `kubeadmin` encontrar a palavra-passe para o utilizador.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

A saída de exemplo seguinte `kubeadminPassword`mostra que a palavra-passe estará dentro .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Você pode encontrar o URL da consola cluster executando o seguinte comando, que será como`https://console-openshift-console.apps.<random>.<region>.aroapp.io/`

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Lance o URL da consola num `kubeadmin` browser e faça login utilizando as credenciais.

![Tela de login OpenShift do chapéu vermelho azure](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instale o CLI OpenShift

Assim que estiver ligado à Consola Web OpenShift, clique no **?** na parte superior direita e depois nas **Ferramentas da Linha de Comando**. Faça o download adequado à sua máquina.

![Tela de login OpenShift do chapéu vermelho azure](media/aro4-download-cli.png)

Também pode descarregar o mais recente lançamento do <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/>CLI apropriado para a sua máquina a partir de .

Se estiver a comandar os comandos da Azure Cloud Shell, descarregue o mais recente OpenShift 4 CLI para Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Conecte-se utilizando o CLI OpenShift

Recupere o endereço do servidor DaPI.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Inicie sessão no servidor API do cluster OpenShift utilizando o seguinte comando. Substitua a ** \<palavra-passe de kubeadmin>** pela palavra-passe que acabou de recuperar.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Obtenha `kubeadmin` credenciais para o seu cluster
> * Instale o CLI OpenShift
> * Ligue-se a um cluster OpenShift do chapéu vermelho azure usando o CLI OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)