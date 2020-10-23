---
title: Tutorial - Conecte-se a um aglomerado Azure Red Hat OpenShift 4
description: Saiba como ligar um cluster Microsoft Azure Red Hat OpenShift
author: sakthi-vetrivel
ms.author: suvetriv
ms.topic: tutorial
ms.service: container-service
ms.date: 04/24/2020
ms.openlocfilehash: 132a9b7ad3eef17f4ca27fb76a0c05f94c82fd37
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92428278"
---
# <a name="tutorial-connect-to-an-azure-red-hat-openshift-4-cluster"></a>Tutorial: Ligue-se a um aglomerado Azure Red Hat OpenShift 4

Neste tutorial, parte dois de três, você vai ligar-se a um cluster Azure Red Hat OpenShift executando OpenShift 4 como o utilizador kubeadmin através da consola web OpenShift. Saiba como:
> [!div class="checklist"]
> * Obtenha `kubeadmin` credenciais para o seu cluster
> * Instale o CLI OpenShift
> * Ligue-se a um cluster Azure Red Hat OpenShift utilizando o CLI OpenShift

## <a name="before-you-begin"></a>Antes de começar

Em tutoriais anteriores, foi criado um cluster Azure Red Hat OpenShift. Se não tiver feito estes passos, e gostaria de seguir em frente, comece com [Tutorial 1 - Crie um Azure Red Hat OpenShift 4 Cluster.](tutorial-create-cluster.md)

Se optar por instalar e utilizar o CLI localmente, este tutorial requer que esteja a executar a versão Azure CLI 2.6.0 ou posterior. Executar `az --version` para localizar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="connect-to-the-cluster"></a>Ligar ao cluster

Pode iniciar sessão no cluster utilizando o `kubeadmin` utilizador.  Executar o seguinte comando para encontrar a senha para o `kubeadmin` utilizador.

```azurecli-interactive
az aro list-credentials \
  --name $CLUSTER \
  --resource-group $RESOURCEGROUP
```

A saída de exemplo a seguir mostra em que palavra-passe estará `kubeadminPassword` .

```json
{
  "kubeadminPassword": "<generated password>",
  "kubeadminUsername": "kubeadmin"
}
```

Pode encontrar o URL da consola de cluster executando o seguinte comando, que será semelhante `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .

```azurecli-interactive
 az aro show \
    --name $CLUSTER \
    --resource-group $RESOURCEGROUP \
    --query "consoleProfile.url" -o tsv
```

Lance o URL da consola num browser e faça login usando as `kubeadmin` credenciais.

![Ecrã de login Azure Red Hat OpenShift](media/aro4-login.png)

## <a name="install-the-openshift-cli"></a>Instale o CLI OpenShift

Uma vez iniciado sessão na Consola Web OpenShift, clique no **?** no topo direito e, em seguida, em **Ferramentas de Linha de Comando**. Descarregue o desbloqueio apropriado para a sua máquina.

![Screenshot que realça a opção Ferramentas linha de comando na lista quando selecionar o ? .](media/aro4-download-cli.png)

Também pode descarregar a versão mais recente do CLI apropriada para a sua máquina a partir de <https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/> .

Se estiver a executar os comandos no Azure Cloud Shell, descarregue o mais recente OpenShift 4 CLI para Linux.

```azurecli-interactive
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

## <a name="connect-using-the-openshift-cli"></a>Conecte-se usando o CLI OpenShift

Recupere o endereço do servidor API.

```azurecli-interactive
apiServer=$(az aro show -g $RESOURCEGROUP -n $CLUSTER --query apiserverProfile.url -o tsv)
```

Faça login no servidor API do cluster OpenShift utilizando o seguinte comando. **\<kubeadmin password>** Substitua-a pela palavra-passe que acabou de recuperar.

```azurecli-interactive
oc login $apiServer -u kubeadmin -p <kubeadmin password>
```

## <a name="next-steps"></a>Passos seguintes

Nesta parte do tutorial, ficou a saber como:
> [!div class="checklist"]
> * Obtenha `kubeadmin` credenciais para o seu cluster
> * Instale o CLI OpenShift
> * Ligue-se a um cluster Azure Red Hat OpenShift utilizando o CLI OpenShift

Avance para o tutorial seguinte:
> [!div class="nextstepaction"]
> [Eliminar um cluster do Azure Red Hat OpenShift](tutorial-delete-cluster.md)