---
title: 'Tutorial: Implementar CI/CD com GitOps utilizando clusters Kubernetes ativados pelo Arco Azure'
description: Este tutorial caminha através da criação de uma solução ci/CD usando GitOps com arco Azure habilitado clusters Kubernetes. Para uma abordagem conceptual deste fluxo de trabalho, consulte o fluxo de trabalho CI/CD utilizando o artigo de GitOps - Azure Arc.
author: tcare
ms.author: tcare
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/03/2021
ms.custom: template-tutorial
ms.openlocfilehash: f720cc196f4034d29ec1d628e28d3534b10f3e41
ms.sourcegitcommit: a8ff4f9f69332eef9c75093fd56a9aae2fe65122
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/24/2021
ms.locfileid: "105025820"
---
# <a name="tutorial-implement-cicd-with-gitops-using-azure-arc-enabled-kubernetes-clusters"></a>Tutorial: Implementar CI/CD com GitOps utilizando clusters Kubernetes ativados pelo Arco Azure


Neste tutorial, irá configurar uma solução ci/CD utilizando gitOps com clusters Kubernetes habilitados para o Arco Azure. Utilizando a aplicação Azure Vote, a amostra irá:

> [!div class="checklist"]
> * Crie um aglomerado de Kubernetes ativado pelo Arco Azure.
> * Ligue a sua aplicação e os repos do GitOps ao Azure Repos.
> * Importar gasodutos CI/CD.
> * Ligue o registo do seu contentor Azure (ACR) a Azure DevOps e Kubernetes.
> * Criar grupos variáveis ambientais.
> * Desdobre os `dev` ambientes e `stage` os ambientes.
> * Teste os ambientes de aplicação.

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="before-you-begin"></a>Antes de começar

Este tutorial pressupõe familiaridade com Azure DevOps, Azure Repos e Pipelines, e Azure CLI.

* Inscreva-se nos [Serviços Azure DevOps](https://dev.azure.com/).
* Complete o [tutorial anterior](./tutorial-use-gitops-connected-cluster.md) para aprender a implementar GitOps para o seu ambiente CI/CD.
* Compreenda os [benefícios e arquitetura](./conceptual-configurations.md) desta característica.
* Verifique se tem:
  * Um [arco Azure conectado permitiu que kubernetes cluster](./quickstart-connect-cluster.md#connect-an-existing-kubernetes-cluster) chamado **arco-cicd-cluster**.
  * Um registo de contentores Azure conectado (ACR) com [integração AKS](../../aks/cluster-container-registry-integration.md) ou [autenticação de cluster não-AKS](../../container-registry/container-registry-auth-kubernetes.md).
  * Permissões "Build Admin" e "Project Admin" para [Azure Repos](/azure/devops/repos/get-started/what-is-repos) e [Azure Pipelines](/azure/devops/pipelines/get-started/pipelines-get-started).
* Instale as seguintes extensões CLI ativadas por Kubernetes de versões >= 1.0.0:

  ```azurecli
  az extension add --name connectedk8s
  az extension add --name k8s-configuration
  ```
  * Para atualizar estas extensões para a versão mais recente, execute os seguintes comandos:

    ```azurecli
    az extension update --name connectedk8s
    az extension update --name k8s-configuration
    ```

## <a name="import-application-and-gitops-repos-into-azure-repos"></a>Pedido de importação e GitOps repousa em Azure Repos

Importe um [repo de aplicação](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#application-repo) e um [GitOps repo](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd#gitops-repo) em Azure Repos. Para este tutorial, utilize os seguintes repos de exemplo:

* **rede de ar-cicd-demo-src**
   * URL: https://github.com/Azure/arc-cicd-demo-src
   * Contém o exemplo Azure Vote App que irá implementar usando GitOps.
* **arc-cicd-demo-gitops** GitOps repo
   * URL: https://github.com/Azure/arc-cicd-demo-gitops
   * Funciona como base para os seus recursos de cluster que abrigam a App Azure Vote.

Saiba mais sobre [a importação de repos de Git.](/azure/devops/repos/git/import-git-repository)

>[!NOTE]
> Importar e usar dois repositórios separados para aplicação e repos GitOps pode melhorar a segurança e a simplicidade. A aplicação e as permissões e visibilidade dos repositórios do GitOps podem ser afinadas individualmente.
> Por exemplo, o administrador de cluster pode não encontrar as alterações no código de aplicação relevantes para o estado desejado do cluster. Inversamente, um desenvolvedor de aplicações não precisa de conhecer os parâmetros específicos para cada ambiente - um conjunto de valores de teste que fornecem cobertura para os parâmetros pode ser suficiente.

## <a name="connect-the-gitops-repo"></a>Ligue o repo GitOps

Para implementar continuamente a sua aplicação, ligue o repo da aplicação ao seu cluster utilizando GitOps. O seu **repo gitOps de demo-de-demo-cíd-gitops** contém os recursos básicos para pôr a sua app a funcionar no seu **cluster arc-cicd.**

O repo GitOps inicial contém apenas um [manifesto](https://github.com/Azure/arc-cicd-demo-gitops/blob/master/arc-cicd-cluster/manifests/namespaces.yml) que cria os espaços **de nomes dev** e **estágio** correspondentes aos ambientes de implantação.

A ligação GitOps que cria será automaticamente:
* Sincronia os manifestos no diretório manifesto.
* Atualize o estado do cluster.

O fluxo de trabalho ci/CD irá povoar o diretório manifesto com manifestos extra para implantar a app.


1. [Crie uma nova ligação GitOps](./tutorial-use-gitops-connected-cluster.md) ao seu **recém-importado arc-cicd-demo-gitops** repo em Azure Repos.

   ```azurecli
   az k8sconfiguration create \
      --name cluster-config \
      --cluster-name arc-cicd-cluster \
      --resource-group myResourceGroup \
      --operator-instance-name cluster-config \
      --operator-namespace cluster-config \
      --repository-url https://dev.azure.com/<Your organization>/arc-cicd-demo-gitops \
      --https-user <Azure Repos username> \
      --https-key <Azure Repos PAT token> \
      --scope cluster \
      --cluster-type connectedClusters \
      --operator-params='--git-readonly --git-path=arc-cicd-cluster/manifests'
   ```

1. Certifique-se de que o Flux *utiliza apenas* o `arc-cicd-cluster/manifests` diretório como caminho base. Defina o caminho utilizando o seguinte parâmetro do operador:

   `--git-path=arc-cicd-cluster/manifests`

   > [!NOTE]
   > Se estiver a utilizar uma cadeia de ligação HTTPS e tiver problemas de ligação, certifique-se de que omite o prefixo do nome de utilizador no URL. Por exemplo, `https://alice@dev.azure.com/contoso/arc-cicd-demo-gitops` deve ter `alice@` sido removido. Em `--https-user` vez disso, o utilizador especifica, por `--https-user alice` exemplo.

1. Verifique o estado da implantação no portal Azure.
   * Se for bem sucedido, verá ambos `dev` e `stage` espaços de nomes criados no seu cluster.

## <a name="import-the-cicd-pipelines"></a>Importar os gasodutos CI/CD

Agora que sincronizou uma ligação gitOps, terá de importar os oleodutos CI/CD que criam os manifestos.

O repo de aplicação contém uma `.pipeline` pasta com os oleodutos que utilizará para PRs, CI e CD. Importar e renomear os três oleodutos previstos no repo da amostra:

| Nome do ficheiro do pipeline | Description |
| ------------- | ------------- |
| [`.pipelines/az-vote-pr-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-pr-pipeline.yaml)  | O pipeline de pr da aplicação, denominado **arc-cicd-demo-src PR** |
| [`.pipelines/az-vote-ci-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-ci-pipeline.yaml) | O pipeline ci da aplicação, denominado **arc-cicd-demo-src CI** |
| [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml) | O pipeline de CD da aplicação, denominado **CD arc-cicd-demo-src** |



## <a name="connect-your-acr"></a>Ligue o seu ACR
Tanto os seus oleodutos como o cluster estarão a utilizar a ACR para armazenar e recuperar imagens do Docker.

### <a name="connect-acr-to-azure-devops"></a>Ligue a ACR a Azure DevOps
Durante o processo de IC, irá colocar os seus recipientes de inscrição num registo. Comece por criar uma ligação de serviço Azure:

1. Em Azure DevOps, abra a página **de ligações** de serviço a partir da página de definições do projeto. Na TFS, abra a página **Serviço a** partir do ícone de **definições** na barra de menu superior.
2. Escolha **+ Nova ligação de serviço** e selecione o tipo de ligação de serviço de que necessita.
3. Preencha os parâmetros para a ligação de serviço. Para este tutorial:
   * Nomeie a ligação de serviço **arc-demo-acr**. 
   * Selecione **o myResourceGroup** como grupo de recursos.
4. Selecione a **permissão de acesso grant a todos os oleodutos**. 
   * Esta opção autoriza ficheiros de gasodutos YAML para ligações de serviço. 
5. Escolha **OK** para criar a ligação.

### <a name="connect-acr-to-kubernetes"></a>Ligar ACR a Kubernetes
Ative o seu cluster Kubernetes para extrair imagens do seu ACR. Se for privado, será necessária autenticação.

#### <a name="connect-acr-to-existing-aks-clusters"></a>Ligue o ACR aos clusters AKS existentes

Integre um ACR existente com clusters AKS existentes utilizando o seguinte comando:

```azurecli
az aks update -n arc-cicd-cluster -g myResourceGroup --attach-acr arc-demo-acr
```

#### <a name="create-an-image-pull-secret"></a>Criar um segredo de puxar a imagem

Para ligar os clusters não-AKS e locais ao seu ACR, crie um segredo de imagem. Kubernetes usa segredos de puxar imagem para armazenar informações necessárias para autenticar o seu registo.

Crie um segredo de imagem com o seguinte `kubectl` comando. Repita para os `dev` `stage` espaços e para nomes.
```console
kubectl create secret docker-registry <secret-name> \
    --namespace <namespace> \
    --docker-server=<container-registry-name>.azurecr.io \
    --docker-username=<service-principal-ID> \
    --docker-password=<service-principal-password>
```

> [!TIP]
> Para evitar ter de definir um imagePullSecret para cada Pod, considere adicionar a imagemPullSecret à conta de Serviço nos `dev` espaços e `stage` nomes. Consulte o [tutorial de Kubernetes](https://kubernetes.io/docs/tasks/configure-pod-container/configure-service-account/#add-imagepullsecrets-to-a-service-account) para obter mais informações.

## <a name="create-environment-variable-groups"></a>Criar grupos variáveis ambientais

### <a name="app-repo-variable-group"></a>Grupo variável de repo app
[Criar um grupo variável](/azure/devops/pipelines/library/variable-groups) chamado **az-vote-app-dev**. Defina os seguintes valores:

| Variável | Valor |
| -------- | ----- |
| AZ_ACR_NAME | (a sua acr, por exemplo. azurearctest.azurecr.io) |
| AZURE_SUBSCRIPTION | (a sua Conexão de Serviço Azure, que deve ser **arc-demo-acr** de início no tutorial) |
| AZURE_VOTE_IMAGE_REPO | O caminho completo para a App Azure Vote Repo, por exemplo, azurearctest.azurecr.io/azvote |
| ENVIRONMENT_NAME | Dev |
| MANIFESTS_BRANCH | `master` |
| MANIFESTS_REPO | A cadeia de conexão Git para o seu repo GitOps |
| PAT | Um [token PAT criado](/azure/devops/organizations/accounts/use-personal-access-tokens-to-authenticate#create-a-pat) com permissões de fonte de leitura/escrita. Guarde-o para usar mais tarde ao criar o `stage` grupo variável. |
| SRC_FOLDER | `azure-vote` | 
| TARGET_CLUSTER | `arc-cicd-cluster` |
| TARGET_NAMESPACE | `dev` |

> [!IMPORTANT]
> Marque o seu PAT como um tipo secreto. Nas suas aplicações, considere ligar segredos de um [Azure KeyVault.](/azure/devops/pipelines/library/variable-groups#link-secrets-from-an-azure-key-vault)
>
### <a name="stage-environment-variable-group"></a>Grupo variável de ambiente de palco

1. Clone o grupo variável **az-vote-app-dev.**
1. Mude o nome para **fase az-vote-app.**
1. Assegurar os seguintes valores para as variáveis correspondentes:

| Variável | Valor |
| -------- | ----- |
| ENVIRONMENT_NAME | Fase |
| TARGET_NAMESPACE | `stage` |

Agora estás pronto para ir para os `dev` `stage` ambientes e ambientes.

## <a name="deploy-the-dev-environment-for-the-first-time"></a>Implementar o ambiente dev pela primeira vez
Com os oleodutos de IA e CD criados, execute o pipeline ci para implementar a app pela primeira vez.

### <a name="ci-pipeline"></a>Gasoduto CI

Durante o curso inicial do gasoduto CI, poderá obter um erro de autorização de recursos na leitura do nome de ligação de serviço.
1. Verifique se a variável a ser acedida é AZURE_SUBSCRIPTION.
1. Autorize a utilização.
1. Reencaúndio do oleoduto.

O gasoduto CI:
* Garante que a alteração da aplicação passa por todos os controlos de qualidade automatizados para implantação.
* Faz qualquer validação extra que não possa ser concluída no oleoduto de relações públicas.
    * Específico do GitOps, o oleoduto também publica os artefactos para o compromisso que será implantado pelo pipeline de CD.
* Verifica se a imagem do Docker mudou e a nova imagem é empurrada.

### <a name="cd-pipeline"></a>Oleoduto de CD
O gasoduto CI bem sucedido aciona o gasoduto de CD para completar o processo de implantação. Irás implantar-te para cada ambiente gradualmente.

> [!TIP]
> Se o gasoduto de CD não acionar automaticamente:
> 1. Verifique se o nome corresponde ao gatilho do ramo em [`.pipelines/az-vote-cd-pipeline.yaml`](https://github.com/Azure/arc-cicd-demo-src/blob/master/.pipelines/az-vote-cd-pipeline.yaml)
>    * Deverá ser `arc-cicd-demo-src CI`.
> 1. Reencamúndir do oleoduto ci.

Uma vez gerado o modelo e as alterações manifestas no repo GitOps, o pipeline de CD criará um compromisso, empurrará-o e criará um PR para aprovação.
1. Abra a ligação de relações públicas dada na saída da `Create PR` tarefa.
1. Verifique as alterações ao repo GitOps. Deverá ver:
   * Alterações no modelo de leme de alto nível.
   * Kubernetes de baixo nível manifesta-se que mostram as alterações subjacentes ao estado desejado. O fluxo implanta estes manifestos.
1. Se tudo parecer bem, aprove e complete o pr.

1. Após alguns minutos, o Flux apanha a mudança e inicia a implantação.
1. Encaminhe a porta localmente utilizando `kubectl` e certifique-se de que a aplicação funciona corretamente utilizando:

   `kubectl port-forward -n dev svc/azure-vote-front 8080:80`

1. Veja a aplicação Azure Vote no seu navegador em `http://localhost:8080/` .

1. Vote nos seus favoritos e prepare-se para fazer algumas alterações na aplicação.

## <a name="set-up-environment-approvals"></a>Configurar aprovações ambientais
Após a implementação da aplicação, você pode não só fazer alterações ao código ou modelos, mas também pode involuntariamente colocar o cluster em um mau estado.

Se o ambiente dev revelar uma rutura após a implantação, impeça-o de ir para ambientes posteriores utilizando aprovações ambientais.

1. No seu projeto Azure DevOps, vá para o ambiente que precisa de ser protegido.
1. Navegue para **aprovações e verificações** para o recurso.
1. Selecione **Criar**.
1. Forneça os aprovadores e uma mensagem opcional.
1. **Selecione Criar** novamente para completar a adição da verificação de aprovação manual.

Para mais detalhes, consulte o tutorial [de aprovação e verificações.](/azure/devops/pipelines/process/approvals)

Da próxima vez que o gasoduto de CD funcionar, o gasoduto fará uma pausa após a criação do GitOps PR. Verifique se a alteração foi sincronizada corretamente e passa a funcionalidade básica. Aprove a verificação do gasoduto para permitir que a mudança flua para o ambiente seguinte.

## <a name="make-an-application-change"></a>Fazer uma alteração de candidatura

Com este conjunto de modelos e manifestos de base que representam o estado no cluster, você fará uma pequena alteração na app.

1. No **arc-cicd-demo-src** repo, edite [`azure-vote/src/azure-vote-front/config_file.cfg`](https://github.com/Azure/arc-cicd-demo-src/blob/master/azure-vote/src/azure-vote-front/config_file.cfg) o ficheiro.

2. Uma vez que "Cats vs Dogs" não está a receber votos suficientes, altere-o para "Tabs vs Spaces" para aumentar a contagem de votos.

3. Cometa a mudança num novo ramo, empurre-o e crie um pedido de atração.
   * Este é o fluxo típico do desenvolvedor que iniciará o ciclo de vida CI/CD.

## <a name="pr-validation-pipeline"></a>Gasoduto de validação de RP

O oleoduto de Relações Públicas é a primeira linha de defesa contra uma mudança defeituosa. Os controlos de qualidade de código de aplicação habituais incluem o linco e a análise estática. Do ponto de vista do GitOps, também é necessário garantir a mesma qualidade para a infraestrutura resultante a ser implantada.

Os gráficos Dockerfile e Helm da aplicação podem usar o linculamento de forma semelhante à aplicação.

Erros encontrados durante a gama de linças a partir de:
* Ficheiros YAML mal formatados, para
* Sugestões de boas práticas, como definir os limites de CPU e Memória para a sua aplicação.

> [!NOTE]
> Para obter a melhor cobertura do Helm linting em uma aplicação real, você precisará substituir valores que são razoavelmente semelhantes aos usados em um ambiente real.

Os erros encontrados durante a execução do gasoduto aparecem na secção de resultados do ensaio da execução. A partir daqui, pode:
* Acompanhe as estatísticas úteis sobre os tipos de erros.
* Encontre o primeiro compromisso em que foram detetados.
* Empilhar ligações de estilo de traço às secções de código que causaram o erro.

Uma vez terminada a execução do gasoduto, assegurou a qualidade do código de aplicação e o modelo que o irá implantar. Agora pode aprovar e completar o Pr. O CI voltará a funcionar, regenerando os modelos e manifestos, antes de acionar o pipeline de CD.

> [!TIP]
> Num ambiente real, não se esqueça de definir políticas de filial para garantir que o PR passa os seus cheques de qualidade. Para obter mais informações, consulte o artigo [de política de ramo definido.](/azure/devops/repos/git/branch-policies)

## <a name="cd-process-approvals"></a>Aprovações de processos de CD

Um gasoduto CI bem sucedido aciona o gasoduto de CD para completar o processo de implantação. À semelhança da primeira vez que puder o pipeline de CD, irá implantar-se gradualmente em cada ambiente. Desta vez, o oleoduto requer que aprove cada ambiente de implantação.

1. Aprove a implantação para o `dev` ambiente.
1. Uma vez gerado o modelo e as alterações manifestas no repo GitOps, o pipeline de CD criará um compromisso, empurrará-o e criará um PR para aprovação.
1. Abra a ligação de relações públicas dada na tarefa.
1. Verifique as alterações ao repo GitOps. Deverá ver:
   * Alterações no modelo de leme de alto nível.
   * Kubernetes de baixo nível manifesta-se que mostram as alterações subjacentes ao estado desejado.
1. Se tudo parecer bem, aprove e complete o pr.
1. Aguarde pela conclusão da implementação.
1. Como teste básico de fumo, navegue na página de aplicação e verifique se a aplicação de voto apresenta agora Separadores vs Espaços.
   * Encaminhe a porta localmente utilizando `kubectl` e certifique-se de que a aplicação funciona corretamente utilizando: `kubectl port-forward -n dev svc/azure-vote-front 8080:80`
   * Veja a aplicação Azure Vote no seu navegador http://localhost:8080/ e verifique se as escolhas de voto mudaram para Separadores vs Espaços. 
1. Repita os passos 1-7 para o `stage` ambiente.

A sua implantação está completa. Isto termina com o fluxo de trabalho CI/CD.

## <a name="clean-up-resources"></a>Limpar os recursos

Se não continuar a utilizar esta aplicação, elimine quaisquer recursos com os seguintes passos:

1. Eliminar a ligação de configuração Azure Arc GitOps:
   ```azurecli
   az k8sconfiguration delete \
   --name cluster-config \
   --cluster-name arc-cicd-cluster \
   --resource-group myResourceGroup \
   --cluster-type connectedClusters
   ```

2. Remova o `dev` espaço de nome:
   * `kubectl delete namespace dev`

3. Remova o `stage` espaço de nome:
   * `kubectl delete namespace stage`

## <a name="next-steps"></a>Passos seguintes

Neste tutorial, você criou um fluxo de trabalho ci/CD completo que implementa DevOps do desenvolvimento de aplicações através da implementação. As alterações à aplicação desencadeiam automaticamente a validação e implementação, fechadas por aprovações manuais.

Avance para o nosso artigo conceptual para saber mais sobre GitOps e configurações com Azure Arc habilitado Kubernetes.

> [!div class="nextstepaction"]
> [Fluxo de trabalho CI/CD utilizando GitOps - Azure Arc habilitado Kubernetes](https://docs.microsoft.com/azure/azure-arc/kubernetes/conceptual-gitops-cicd)