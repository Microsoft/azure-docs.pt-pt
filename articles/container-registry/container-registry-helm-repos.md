---
title: Gráficos de leme da loja
description: Saiba como armazenar gráficos Helm para as suas aplicações Kubernetes usando repositórios no Registo de Contentores Azure
ms.topic: article
ms.date: 04/15/2021
ms.openlocfilehash: c7dcdf222e9628daedb7e1c3617efb0b9c7af185
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/20/2021
ms.locfileid: "107772384"
---
# <a name="push-and-pull-helm-charts-to-an-azure-container-registry"></a>Empurre e puxe as fichas de Helm para um registo de contentores Azure

Para gerir e implementar rapidamente aplicações para Kubernetes, pode utilizar o [gestor de pacotes Helm de código aberto.][helm] Com helm, os pacotes de aplicações são definidos como [gráficos,](https://helm.sh/docs/topics/charts/)que são recolhidos e armazenados num [repositório de gráficos Helm](https://helm.sh/docs/topics/chart_repository/).

Este artigo mostra-lhe como hospedar repositórios helm charts em um registo de contentores Azure, usando comandos Helm 3. Em muitos cenários, construiria e carregaria os seus próprios gráficos para as aplicações que desenvolve. Para obter mais informações sobre como construir os seus próprios gráficos helm, consulte o [Guia do Desenvolvedor de Modelos de Gráficos.][develop-helm-charts] Você também pode armazenar um gráfico helm existente a partir de outro repo Helm.

## <a name="helm-3-or-helm-2"></a>Leme 3 ou Helm 2?

Para armazenar, gerir e instalar gráficos Helm, utilize um cliente Helm e o Helm CLI. As principais versões do cliente Helm incluem Helm 3 e Helm 2. Para mais detalhes sobre as diferenças de versão, consulte a [versão FAQ](https://helm.sh/docs/faq/). 

O leme 3 deve ser utilizado para hospedar gráficos helm no registo de contentores Azure. Com Helm 3, tu:

* Pode criar um ou mais repositórios helm num registo de contentores Azure
* Armazenar gráficos Helm 3 num registo como [artefactos OCI](container-registry-image-formats.md#oci-artifacts). O Registo de Contentores Azure fornece suporte de GA para [artefactos OCI,](container-registry-oci-artifacts.md)incluindo gráficos helm.
* Autenticar com o seu registo utilizando o `helm registry login` comando.
* Use `helm chart` comandos no Helm CLI para empurrar, puxar e gerir gráficos de Helm em um registo
* Use `helm install` para instalar gráficos num cluster Kubernetes a partir de uma cache de repositório local.
> [!NOTE]
> A partir do Helm 3, os comandos [de leme az acr][az-acr-helm] para uso com o cliente Helm 2 estão a ser depreciados. Um pré-aviso mínimo de 3 meses será fornecido antes da remoção do comando. Se já implementou previamente os gráficos Helm 2, consulte [o Leme Migratório v2 para v3](https://helm.sh/docs/topics/v2_v3_migration/).

## <a name="prerequisites"></a>Pré-requisitos

São necessários os seguintes recursos para o cenário deste artigo:

- **Um registo de contentores Azure** na sua assinatura Azure. Se necessário, crie um registo utilizando o [portal Azure](container-registry-get-started-portal.md) ou o [Azure CLI](container-registry-get-started-azure-cli.md).
- **Versão do cliente helm 3.1.0 ou mais tarde** - Corra `helm version` para encontrar a sua versão atual. Para obter mais informações sobre como instalar e atualizar o Helm, consulte [instalar o Leme.][helm-install]
- **Um cluster Kubernetes** onde vai instalar um gráfico Helm. Se necessário, crie um [cluster de serviço Azure Kubernetes][aks-quickstart]. 
- **Versão Azure CLI 2.0.71 ou mais tarde** - Corra `az --version` para encontrar a versão. Se precisar de instalar ou atualizar, veja [Install Azure CLI (Instalar o Azure CLI)][azure-cli-install].

## <a name="enable-oci-support"></a>Ativar o suporte do OCI

Utilize o `helm version` comando para verificar se instalou o Helm 3:

```console
helm version
```

Desaprote a seguinte variável ambiental para permitir o suporte do OCI no cliente Helm 3. Atualmente, este apoio é experimental. 

```console
export HELM_EXPERIMENTAL_OCI=1
```

## <a name="create-a-sample-chart"></a>Criar um gráfico de amostras

Criar um gráfico de teste utilizando os seguintes comandos:

```console
mkdir helmtest

cd helmtest
helm create hello-world
```

Como exemplo básico, altere o diretório para a `templates` pasta e elimine primeiro os conteúdos lá:

```console
cd hello-world/templates
rm -rf *
```

Na `templates` pasta, crie um ficheiro chamado `configmap.yaml` , executando o seguinte comando:

```console
cat <<EOF > configmap.yaml
apiVersion: v1
kind: ConfigMap
metadata:
  name: hello-world-configmap
data:
  myvalue: "Hello World"
EOF
```

Para mais informações sobre a criação e execução deste exemplo, consulte ["Começar](https://helm.sh/docs/chart_template_guide/getting_started/) no Helm Docs".

## <a name="save-chart-to-local-registry-cache"></a>Guardar gráfico para cache de registo local

Mude o diretório para a `hello-world` subdiretório. Em seguida, corra `helm chart save` para guardar uma cópia do gráfico localmente e também crie um pseudónimo com o nome totalmente qualificado do registo (todos minúsculos) e o repositório e etiqueta alvo. 

No exemplo seguinte, o nome do registo é *mycontainerregistry*, o repo alvo é *olá-mundo*, e a etiqueta de gráfico-alvo é *v1*, mas valores substitutos para o seu ambiente:

```console
cd ..
helm chart save . hello-world:v1
helm chart save . mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Corra `helm chart list` para confirmar que guardou as fichas na cache de registo local. A saída é semelhante a:

```console
REF                                                      NAME            VERSION DIGEST  SIZE            CREATED
hello-world:v1                                           hello-world       0.1.0   5899db0 3.2 KiB        2 minutes 
mycontainerregistry.azurecr.io/helm/hello-world:v1       hello-world       0.1.0   5899db0 3.2 KiB        2 minutes
```

## <a name="authenticate-with-the-registry"></a>Autenticar com o registo

Executar o `helm registry login` comando no Helm 3 CLI para [autenticar com o registo](container-registry-authentication.md) usando credenciais apropriadas para o seu cenário.

Por exemplo, crie um diretor de serviço Azure Ative Directory [com permissões de puxar e empurrar](container-registry-auth-service-principal.md#create-a-service-principal) (papel AcrPush) para o registo. Em seguida, forneça as principais credenciais de serviço para `helm registry login` . O exemplo a seguir fornece a palavra-passe utilizando uma variável ambiental:

```console
echo $spPassword | helm registry login mycontainerregistry.azurecr.io \
  --username <service-principal-id> \
  --password-stdin
```

## <a name="push-chart-to-registry"></a>Gráfico de empurrar para o registo

Executar o `helm chart push` comando no Helm 3 CLI para empurrar a tabela para o repositório de alvos totalmente qualificado:

```console
helm chart push mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Após um impulso bem sucedido, a saída é semelhante a:

```output
The push refers to repository [mycontainerregistry.azurecr.io/helm/hello-world]
ref:     mycontainerregistry.azurecr.io/helm/hello-world:v1
digest:  5899db028dcf96aeaabdadfa5899db025899db025899db025899db025899db02
size:    3.2 KiB
name:    hello-world
version: 0.1.0
```

## <a name="list-charts-in-the-repository"></a>Gráficos de listas no repositório

Tal como acontece com as imagens armazenadas num registo de contentores Azure, pode utilizar [comandos de repositório az acr][az-acr-repository] para mostrar os repositórios que hospedam os seus gráficos, e etiquetas de gráficos e manifestos. 

Por exemplo, executar [az acr repositório mostrar][az-acr-repository-show] para ver as propriedades do repo que criou no passo anterior:

```azurecli
az acr repository show \
  --name mycontainerregistry \
  --repository helm/hello-world
```

A saída é semelhante a:

```output
{
  "changeableAttributes": {
    "deleteEnabled": true,
    "listEnabled": true,
    "readEnabled": true,
    "writeEnabled": true
  },
  "createdTime": "2020-03-20T18:11:37.6701689Z",
  "imageName": "helm/hello-world",
  "lastUpdateTime": "2020-03-20T18:11:37.7637082Z",
  "manifestCount": 1,
  "registry": "mycontainerregistry.azurecr.io",
  "tagCount": 1
}
```

Executar o comando [az acr repositório de manifestos][az-acr-repository-show-manifests] para ver detalhes do gráfico armazenado no repositório. Por exemplo:

```azurecli
az acr repository show-manifests \
  --name mycontainerregistry \
  --repository helm/hello-world --detail
```

A produção, abreviada neste exemplo, mostra um `configMediaType` `application/vnd.cncf.helm.config.v1+json` de:

```output
[
  {
    [...]
    "configMediaType": "application/vnd.cncf.helm.config.v1+json",
    "createdTime": "2020-03-20T18:11:37.7167893Z",
    "digest": "sha256:0c03b71c225c3ddff53660258ea16ca7412b53b1f6811bf769d8c85a1f0663ee",
    "imageSize": 3301,
    "lastUpdateTime": "2020-03-20T18:11:37.7167893Z",
    "mediaType": "application/vnd.oci.image.manifest.v1+json",
    "tags": [
      "v1"
    ]
```

## <a name="pull-chart-to-local-cache"></a>Puxe o gráfico para cache local

Para instalar um gráfico Helm para Kubernetes, o gráfico deve estar na cache local. Neste exemplo, primeira corrida `helm chart remove` para remover o gráfico local existente chamado `mycontainerregistry.azurecr.io/helm/hello-world:v1` :

```console
helm chart remove mycontainerregistry.azurecr.io/helm/hello-world:v1
```

Corra `helm chart pull` para descarregar o gráfico do registo de contentores Azure para a sua cache local:

```console
helm chart pull mycontainerregistry.azurecr.io/helm/hello-world:v1
```

## <a name="export-helm-chart"></a>Gráfico de leme de exportação

Para trabalhar mais com o gráfico, exporte-o para um diretório local utilizando `helm chart export` . Por exemplo, exporte o gráfico que puxou para o `install` diretório:

```console
helm chart export mycontainerregistry.azurecr.io/helm/hello-world:v1 \
  --destination ./install
```

Para ver informações sobre o gráfico exportado no repo, executar o `helm show chart` comando no diretório onde exportou o gráfico.

```console
cd install
helm show chart hello-world
```

Helm devolve informações detalhadas sobre a versão mais recente do seu gráfico, como mostrado na seguinte saída da amostra:

```output
apiVersion: v2
appVersion: 1.16.0
description: A Helm chart for Kubernetes
name: hello-world
type: application
version: 0.1.0    
```

## <a name="install-helm-chart"></a>Instalar gráfico de leme

Corra `helm install` para instalar o gráfico Helm que puxou para a cache local e exportado. Especifique um nome de libertação como *myhelmtest*, ou passe o `--generate-name` parâmetro. Por exemplo:

```console
helm install myhelmtest ./hello-world
```

A saída após a instalação de gráficos bem sucedida é semelhante a:

```console
NAME: myhelmtest
LAST DEPLOYED: Fri Mar 20 14:14:42 2020
NAMESPACE: default
STATUS: deployed
REVISION: 1
TEST SUITE: None
```

Para verificar a instalação, verifique o `helm get manifest` comando. 

```console
helm get manifest myhelmtest
```

O comando devolve os dados YAML no seu `configmap.yaml` ficheiro de modelo.

Corra `helm uninstall` para desinstalar a versão do gráfico no seu cluster:

```console
helm uninstall myhelmtest
```

## <a name="delete-chart-from-the-registry"></a>Eliminar gráfico do registo

Para eliminar um gráfico do registo do contentor, utilize o comando de eliminação do [repositório az acr.][az-acr-repository-delete] Executar o seguinte comando e confirmar a operação quando solicitado:

```azurecli
az acr repository delete --name mycontainerregistry --image helm/hello-world:v1
```

## <a name="next-steps"></a>Passos seguintes

* Para obter mais informações sobre como criar e implementar gráficos helm, consulte [gráficos de Helm em Desenvolvimento][develop-helm-charts].
* Saiba mais sobre a instalação de aplicações com o Helm in [Azure Kubernetes Service (AKS)](../aks/kubernetes-helm.md).
* Os gráficos de leme podem ser usados como parte do processo de construção do contentor. Para obter mais informações, consulte [utilizar as tarefas de registo do contentor Azure][acr-tasks].

<!-- LINKS - external -->
[helm]: https://helm.sh/
[helm-install]: https://helm.sh/docs/intro/install/
[develop-helm-charts]: https://helm.sh/docs/chart_template_guide/
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/

<!-- LINKS - internal -->
[azure-cli-install]: /cli/azure/install-azure-cli
[aks-quickstart]: ../aks/kubernetes-walkthrough.md
[acr-bestpractices]: container-registry-best-practices.md
[az-configure]: /cli/azure/reference-index#az_configure
[az-acr-login]: /cli/azure/acr#az_acr_login
[az-acr-helm]: /cli/azure/acr/helm
[az-acr-repository]: /cli/azure/acr/repository
[az-acr-repository-show]: /cli/azure/acr/repository#az_acr_repository_show
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-tags]: /cli/azure/acr/repository#az_acr_repository_show_tags
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[acr-tasks]: container-registry-tasks-overview.md
