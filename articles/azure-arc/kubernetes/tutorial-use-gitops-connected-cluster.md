---
title: 'Tutorial: Implementar configurações usando GitOps num arco Azure habilitado para o cluster Kubernetes'
description: Este tutorial demonstra a aplicação de configurações num arco Azure ativado pelo cluster Kubernetes. Para uma abordagem conceptual deste processo, consulte as Configurações e GitOps - Azure Arc habilitado para o artigo de Kubernetes.
author: shashankbarsin
ms.author: shasb
ms.service: azure-arc
ms.topic: tutorial
ms.date: 03/02/2021
ms.custom: template-tutorial
ms.openlocfilehash: 64299bd05e82cf6f5452cde3f3da5622eff25e56
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102121478"
---
# <a name="tutorial-deploy-configurations-using-gitops-on-an-azure-arc-enabled-kubernetes-cluster"></a>Tutorial: Implementar configurações usando GitOps num arco Azure habilitado para o cluster Kubernetes 

Neste tutorial, aplicará configurações usando GitOps num cluster Kubernetes ativado pelo Arco Azure. Vai aprender a:

> [!div class="checklist"]
> * Crie uma configuração num aglomerado de Kubernetes ativado por Azure Arc utilizando um repositório de Git exemplo.
> * Validar que a configuração foi criada com sucesso.
> * Aplique a configuração de um repositório privado de Git.
> * Valide a configuração de Kubernetes.

## <a name="prerequisites"></a>Pré-requisitos

- Uma conta Azure com uma subscrição ativa. [Crie uma conta gratuita.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Um arco Azure existente permitiu o agrupamento de kubernetes conectado.
    - Se ainda não ligou um cluster, caminhe pelo nosso [Connect a Azure Arc ativando o arranque rápido do cluster Kubernetes.](quickstart-connect-cluster.md)
- Uma compreensão dos benefícios e arquitetura desta característica. Ler mais em [Configurações e GitOps - Azure Arc habilitado artigo de Kubernetes](conceptual-configurations.md).

## <a name="create-a-configuration"></a>Criar uma configuração

O [repositório de exemplo](https://github.com/Azure/arc-k8s-demo) utilizado neste artigo é estruturado em torno da persona de um operador de cluster. Os manifestos neste repositório fornecem alguns espaços de nome, implementam cargas de trabalho e fornecem alguma configuração específica da equipa. A utilização deste repositório com o GitOps cria os seguintes recursos no seu cluster:

* Espaços de nome: `cluster-config` `team-a` , `team-b`
* Implantação: `cluster-config/azure-vote`
* ConfigMap: `team-a/endpoints`

As `config-agent` sondagens Azure para configurações novas ou atualizadas. Esta tarefa levará até 30 segundos.

Se estiver a associar um repositório privado à configuração, complete os passos abaixo na [configuração De aplicar a partir de um repositório privado de Git](#apply-configuration-from-a-private-git-repository).

## <a name="use-azure-cli"></a>Utilizar a CLI do Azure
Utilize a extensão Azure CLI `k8s-configuration` para ligar um cluster ligado ao [repositório de git exemplo](https://github.com/Azure/arc-k8s-demo). 
1. Nomeie esta configuração `cluster-config` .
1. Instrua o agente a colocar o operador no espaço de `cluster-config` nomes.
1. Dê permissão ao `cluster-admin` operador.

    ```azurecli
    az k8s-configuration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
    ```

    ```output
    {
      "complianceStatus": {
      "complianceState": "Pending",
      "lastConfigApplied": "0001-01-01T00:00:00",
      "message": "{\"OperatorMessage\":null,\"ClusterState\":null}",
      "messageLevel": "3"
      },
      "configurationProtectedSettings": {},
      "enableHelmOperator": false,
      "helmOperatorProperties": null,
      "id": "/subscriptions/<sub id>/resourceGroups/<group name>/providers/Microsoft.Kubernetes/connectedClusters/<cluster name>/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
      "name": "cluster-config",
      "operatorInstanceName": "cluster-config",
      "operatorNamespace": "cluster-config",
      "operatorParams": "--git-readonly",
      "operatorScope": "cluster",
      "operatorType": "Flux",
      "provisioningState": "Succeeded",
      "repositoryPublicKey": "",
      "repositoryUrl": "https://github.com/Azure/arc-k8s-demo",
      "resourceGroup": "MyRG",
      "sshKnownHostsContents": "",
      "systemData": {
        "createdAt": "2020-11-24T21:22:01.542801+00:00",
        "createdBy": null,
        "createdByType": null,
        "lastModifiedAt": "2020-11-24T21:22:01.542801+00:00",
        "lastModifiedBy": null,
        "lastModifiedByType": null
      },
      "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
      ```

### <a name="use-a-public-git-repository"></a>Use um repositório de Git público

| Parâmetro | Formato |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] ou git://server/repo[.git]

### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Use um repositório privado de Git com teclas SSH e Flux criadas

Adicione a chave pública gerada pelo Flux à conta de utilizador no seu prestador de serviços Git. Se a chave for adicionada ao repositório em vez da conta de utilizador, `git@` utilize-a no lugar `user@` do URL. 

Salte para a configuração Aplicar a partir de uma secção [privada do repositório git](#apply-configuration-from-a-private-git-repository) para mais detalhes.


| Parâmetro | Formato | Notas
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir `user@`

### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Use um repositório privado de Git com SSH e chaves fornecidas pelo utilizador

Forneça a sua própria chave privada diretamente ou num ficheiro. A chave deve estar no [formato PEM](https://aka.ms/PEMformat) e terminar com newline (\n). 

Adicione a chave pública associada à conta de utilizador no seu prestador de serviços Git. Se a chave for adicionada ao repositório em vez da conta de utilizador, `git@` utilize-a em vez de `user@` . 

Salte para a configuração Aplicar a partir de uma secção [privada do repositório git](#apply-configuration-from-a-private-git-repository) para mais detalhes.  

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir `user@` |
| `--ssh-private-key` | chave codificada base64 em [formato PEM](https://aka.ms/PEMformat) | Fornecer chave diretamente |
| `--ssh-private-key-file` | caminho completo para arquivo local | Fornecer caminho completo para o arquivo local que contém a chave de formato PEM

### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Use um anfitrião Git privado com SSH e anfitriões conhecidos fornecidos pelo utilizador

O operador do Flux mantém uma lista de anfitriões git comuns no seu ficheiro de anfitriões conhecido para autenticar o repositório git antes de estabelecer a ligação SSH. Se estiver a usar um repositório *git incomum* ou o seu próprio anfitrião Git, pode fornecer a chave de anfitrião para que o Flux possa identificar o seu repo. 

Tal como as chaves privadas, pode fornecer o seu conteúdo known_hosts diretamente ou num ficheiro. Ao fornecer o seu próprio conteúdo, utilize as [especificações do formato de conteúdo known_hosts,](https://aka.ms/KnownHostsFormat)juntamente com qualquer um dos cenários-chave SSH acima.

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir `user@` |
| `--ssh-known-hosts` | base64-codificado | Fornecer conteúdo conhecido dos anfitriões diretamente |
| `--ssh-known-hosts-file` | caminho completo para arquivo local | Fornecer conteúdo conhecido dos anfitriões em um arquivo local |

### <a name="use-a-private-git-repository-with-https"></a>Use um repositório privado de Git com HTTPS

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[.git] | HTTPS com auth básico |
| `--https-user` | cru ou base64 codificado | Nome de utilizador HTTPS |
| `--https-key` | cru ou base64 codificado | Token de acesso pessoal https ou senha

>[!NOTE]
>* A versão 1.2.0+ do gráfico do operador de leme suporta o auth privado de lançamento HTTPS Helm.
>* A versão HTTPS Helm não é suportada para clusters geridos pela AKS.
>* Se precisar do Flux para aceder ao repositório git através do seu representante, terá de atualizar os agentes do Azure Arc com as definições de procuração. Para obter mais informações, consulte [Connect utilizando um servidor de procuração de saída.](./connect-cluster.md#connect-using-an-outbound-proxy-server)


## <a name="additional-parameters"></a>Parâmetros adicionais

Personalize a configuração com os seguintes parâmetros opcionais:

| Parâmetro | Descrição |
| ------------- | ------------- |
| `--enable-helm-operator`| Altere para ativar o suporte para implementações de gráficos Helm. |
| `--helm-operator-params` | Valores de gráfico para o operador helm (se ativado). Por exemplo, `--set helm.versions=v3`. |
| `--helm-operator-chart-version` | Versão de gráfico para operador helm (se ativado). Use a versão 1.2.0+. Predefinição: '1.2.0'. |
| `--operator-namespace` | Nome para o espaço de nome do operador. Predefinição: 'predefinição'. 23 caracteres. |
| `--operator-params` | Parâmetros para o operador. Deve ser dado dentro de cotações únicas. Por exemplo, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main'``` 

### <a name="options-supported-in----operator-params"></a>Opções suportadas  `--operator-params` em:

| Opção | Descrição |
| ------------- | ------------- |
| `--git-branch`  | Ramo do repositório de Git para usar para manifestos de Kubernetes. O padrão é "mestre". Os repositórios mais recentes têm o nome de root `main` branch, caso em que precisa de `--git-branch=main` definir. |
| `--git-path`  | Caminho relativo dentro do repositório de Git para o Flux localizar manifestos de Kubernetes. |
| `--git-readonly` | O repositório de Git será considerado apenas para ler. O Fluxo não vai tentar escrever-lhe. |
| `--manifest-generation`  | Se ativado, o Flux procurará .flux.yaml e executará Kustomize ou outros geradores manifestos. |
| `--git-poll-interval`  | Período para sondagem do repositório de Git para novos compromissos. O padrão é `5m` (5 minutos). |
| `--sync-garbage-collection`  | Se ativado, o Flux apagará os recursos que criou, mas já não está presente no Git. |
| `--git-label`  | Etiqueta para acompanhar o progresso da sincronização. Costumava marcar o ramo git.  A predefinição é `flux-sync`. |
| `--git-user`  | Nome de utilizador para git cometer. |
| `--git-email`  | E-mail para usar para git cometer. 

Se não quiser que o Flux escreva para o repositório e `--git-user` ou `--git-email` não esteja definido, então `--git-readonly` será automaticamente definido.

Para mais informações, consulte a [documentação do Fluxo.](https://aka.ms/FluxcdReadme)

> [!TIP]
> Pode criar uma configuração no portal Azure no separador **GitOps** do recurso Kubernetes ativado pelo Arco Azure.

## <a name="validate-the-configuration"></a>Validar a configuração

Utilize o CLI Azure para validar que a configuração foi criada com sucesso.

```azurecli
az k8s-configuration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

O recurso de configuração será atualizado com o estado de conformidade, mensagens e informações de depuração.

```output
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2020-12-10T18:26:52.801000+00:00",
    "message": "...",
    "messageLevel": "Information"
  },
  "configurationProtectedSettings": {},
  "enableHelmOperator": false,
  "helmOperatorProperties": {
    "chartValues": "",
    "chartVersion": ""
  },
  "id": "/subscriptions/<sub id>/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
  "name": "cluster-config",
  "operatorInstanceName": "cluster-config",
  "operatorNamespace": "cluster-config",
  "operatorParams": "--git-readonly",
  "operatorScope": "cluster",
  "operatorType": "Flux",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": "...",
  "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git",
  "resourceGroup": "AzureArcTest",
  "sshKnownHostsContents": null,
  "systemData": {
    "createdAt": "2020-12-01T03:58:56.175674+00:00",
    "createdBy": null,
    "createdByType": null,
    "lastModifiedAt": "2020-12-10T18:30:56.881219+00:00",
    "lastModifiedBy": null,
    "lastModifiedByType": null
},
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Quando uma configuração é criada ou atualizada, algumas coisas acontecem:

1. O Azure Arc `config-agent` monitoriza o Azure Resource Manager para configurações novas ou atualizadas `Microsoft.KubernetesConfiguration/sourceControlConfigurations` e nota a nova `Pending` configuração.
1. Lê `config-agent` as propriedades de configuração e cria o espaço de nome de destino.
1. O Arco Azure `controller-manager` cria uma conta de serviço Kubernetes e mapeia-a para [ClusterRoleBinding ou RoleBinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/) para as permissões apropriadas `cluster` (ou `namespace` âmbito). Em seguida, implementa um exemplo de `flux` .
1. Se utilizar a opção de SSH com teclas geradas pelo Fluxox, `flux` gera uma chave SSH e regista a chave pública.
1. O `config-agent` estado dos relatórios volta ao recurso de configuração em Azure.

Enquanto o processo de provisionamento acontece, o recurso de configuração irá mover-se através de algumas alterações de estado. Monitorize o progresso com o `az k8s-configuration show ...` comando acima:

| Mudança de palco | Description |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Representa os estados iniciais e em curso. |
| `complianceStatus` -> `Installed`  | `config-agent` configurado com sucesso o cluster e implantado `flux` sem erros. |
| `complianceStatus` -> `Failed` | `config-agent` deparou-se com um erro de implantação `flux` . Os detalhes são fornecidos no `complianceStatus.message` corpo de resposta. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório privado de Git

Se estiver a usar um repositório privado de Git, precisa de configurar a chave pública SSH no seu repositório. Ou fornece ou o Flux gera a chave pública SSH. Pode configurar a chave pública no repositório específico de Git ou no utilizador Git que tenha acesso ao repositório. 

### <a name="get-your-own-public-key"></a>Obtenha a sua própria chave pública

Se gerou as suas próprias chaves SSH, então já tem as chaves privadas e públicas.

#### <a name="get-the-public-key-using-azure-cli"></a>Obtenha a chave pública usando Azure CLI 

Utilize o seguinte em Azure CLI se o Fluxo estiver a gerar as teclas.

```console
$ az k8s-configuration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Obtenha a chave pública do portal Azure

Caminhe pelo seguinte no portal Azure se o Flux estiver a gerar as teclas.

1. No portal Azure, navegue para o recurso de cluster conectado.
2. Na página de recursos, selecione "GitOps" e consulte a lista de configurações deste cluster.
3. Selecione a configuração que utiliza o repositório privado de Git.
4. Na janela de contexto que se abre, na parte inferior da janela, copie a **chave pública do Repositório.**

#### <a name="add-public-key-using-github"></a>Adicionar chave pública usando GitHub

Utilize uma das seguintes opções:

* Opção 1: Adicione a chave pública à sua conta de utilizador (aplica-se a todos os repositórios da sua conta):  
    1. Abra o GitHub e clique no ícone de perfil no canto superior direito da página.
    2. Clique em **Definições**.
    3. Clique nas **teclas SSH e GPG**.
    4. Clique na **tecla SSH nova.**
    5. Fornecer um título.
    6. Cole a chave pública sem citações circundantes.
    7. Clique na **tecla Add SSH**.

* Opção 2: Adicione a chave pública como chave de implantação do repositório Git (aplica-se apenas a este repositório):  
    1. Abra o GitHub e navegue até ao seu repositório.
    1. Clique em **Definições**.
    1. Clique nas **teclas 'Implementar'.**
    1. Clique na **tecla De implementação de adicionar**.
    1. Fornecer um título.
    1. Verifique **Deixe o acesso por escrito.**
    1. Cole a chave pública sem citações circundantes.
    1. Clique na **tecla Adicionar**.

#### <a name="add-public-key-using-an-azure-devops-repository"></a>Adicione a chave pública usando um repositório Azure DevOps

Utilize os seguintes passos para adicionar a chave às suas teclas SSH:

1. Nas **Definições do Utilizador** no topo direito (junto à imagem de perfil), clique nas **teclas públicas SSH**.
1. Selecione  **+ Nova Tecla**.
1. Fornecer um nome.
1. Cole a chave pública sem citações circundantes.
1. Clique em **Adicionar**.

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração de Kubernetes

Depois de `config-agent` instalado o `flux` caso, os recursos retidos no repositório de Git devem começar a fluir para o cluster. Verifique se os espaços de nome, implantações e recursos foram criados com o seguinte comando:

```console
kubectl get ns --show-labels
```

```output
NAME              STATUS   AGE    LABELS
azure-arc         Active   24h    <none>
cluster-config    Active   177m   <none>
default           Active   29h    <none>
itops             Active   177m   fluxcd.io/sync-gc-mark=sha256.9oYk8yEsRwWkR09n8eJCRNafckASgghAsUWgXWEQ9es,name=itops
kube-node-lease   Active   29h    <none>
kube-public       Active   29h    <none>
kube-system       Active   29h    <none>
team-a            Active   177m   fluxcd.io/sync-gc-mark=sha256.CS5boSi8kg_vyxfAeu7Das5harSy1i0gc2fodD7YDqA,name=team-a
team-b            Active   177m   fluxcd.io/sync-gc-mark=sha256.vF36thDIFnDDI2VEttBp5jgdxvEuaLmm7yT_cuA2UEw,name=team-b
```

Podemos ver `team-a` `team-b` que, e `itops` espaços de nome foram `cluster-config` criados.

O `flux` operador foi implantado para o espaço de `cluster-config` nome, conforme dirigido pelo recurso de configuração:

```console
kubectl -n cluster-config get deploy  -o wide
```

```output
NAME             READY   UP-TO-DATE   AVAILABLE   AGE   CONTAINERS   IMAGES                         SELECTOR
cluster-config   1/1     1            1           3h    flux         docker.io/fluxcd/flux:1.16.0   instanceName=cluster-config,name=flux
memcached        1/1     1            1           3h    memcached    memcached:1.5.15               name=memcached
```

## <a name="further-exploration"></a>Mais exploração

Pode explorar os outros recursos implantados como parte do repositório de configuração utilizando:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```
## <a name="clean-up-resources"></a>Limpar os recursos

Elimine uma configuração utilizando o portal Azure CLI ou Azure. Depois de executar o comando de eliminação, o recurso de configuração será eliminado imediatamente em Azure. A eliminação total dos objetos associados do cluster deve ocorrer dentro de 10 minutos. Se a configuração estiver num estado de falha quando removida, a supressão total dos objetos associados pode demorar até uma hora.

Quando uma configuração com `namespace` âmbito é eliminada, o espaço de nome não é eliminado pelo Azure Arc para evitar quebrar as cargas de trabalho existentes. Se necessário, pode eliminar este espaço de identificação `kubectl` manualmente.

```azurecli
az k8s-configuration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

> [!NOTE]
> Quaisquer alterações ao cluster que foram o resultado de implementações do repositório de Git rastreado não são eliminadas quando a configuração é eliminada.

## <a name="next-steps"></a>Passos seguintes

Avance para o próximo tutorial para aprender a implementar CI/CD com GitOps.
> [!div class="nextstepaction"]
> [Implementar CI/CD com GitOps](./tutorial-gitops-ci-cd.md)
