---
title: Implementar configurações com o GitOps em clusters do Kubernetes preparados para o Azure Arc (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 02/17/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utilize GitOps para configurar um aglomerado de Kubernetes (Preview)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, AKS, contentores
ms.openlocfilehash: b30ecde0e128a955967638828dcb7bec008205ea
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2021
ms.locfileid: "100652484"
---
# <a name="deploy-configurations-using-gitops-on-an-arc-enabled-kubernetes-cluster-preview"></a>Implementar configurações usando GitOps num cluster Kubernetes ativado por Arco (Pré-visualização)

Este artigo demonstra a aplicação de configurações num aglomerado de Kubernetes ativado pelo Arco Azure. Para uma abordagem conceptual deste processo, consulte as [Configurações e GitOps - Azure Arc habilitado artigo de Kubernetes](./conceptual-configurations.md).

## <a name="before-you-begin"></a>Antes de começar

* Verifique se tem um aglomerado ligado a Kubernetes ativado por Azure Arc. Se precisar de um cluster ligado, consulte o arranque rápido do [cluster de kubernetes ativado por Um Arco Azure.](./connect-cluster.md)

* Reveja as [Configurações e GitOps com o artigo Da Arc for Kubernetes](./conceptual-configurations.md) para entender os benefícios e arquitetura desta funcionalidade.

## <a name="create-a-configuration"></a>Criar uma configuração

O [repositório de exemplo](https://github.com/Azure/arc-k8s-demo) utilizado neste artigo é estruturado em torno da persona de um operador de cluster que gostaria de fornecer alguns espaços de nome, implementar uma carga de trabalho comum, e fornecer alguma configuração específica da equipa. A utilização deste repositório cria os seguintes recursos no seu cluster:

* **Espaços de nome:** `cluster-config` `team-a` , `team-b`
* **Implantação:**`cluster-config/azure-vote`
* **ConfigMap:**`team-a/endpoints`

As `config-agent` sondagens Azure para novos ou atualizados `sourceControlConfiguration` . Esta tarefa levará até 30 segundos.

Se estiver a associar um repositório privado ao `sourceControlConfiguration` , complete os passos na [configuração Apply a partir de um repositório privado de Git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Utilize a extensão Azure CLI `k8sconfiguration` para ligar um cluster ligado ao [repositório de git exemplo](https://github.com/Azure/arc-k8s-demo). 
1. Nomeie esta configuração `cluster-config` .
1. Instrua o agente a colocar o operador no espaço de `cluster-config` nomes.
1. Dê permissão ao `cluster-admin` operador.

```azurecli
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

#### <a name="use-a-public-git-repository"></a>Use um repositório de Git público

| Parâmetro | Formato |
| ------------- | ------------- |
| `--repository-url` | http[s]://server/repo[.git] ou git://server/repo[.git]

#### <a name="use-a-private-git-repository-with-ssh-and-flux-created-keys"></a>Use um repositório privado de Git com teclas SSH e Flux criadas

| Parâmetro | Formato | Notas
| ------------- | ------------- | ------------- |
| `--repository-url` | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir `user@`

> [!NOTE]
> A chave pública gerada pelo Flux deve ser adicionada à conta de utilizador no seu prestador de serviços Git. Se a chave for adicionada ao repositório em vez da conta de utilizador, `git@` utilize-a no lugar `user@` do URL. Salte para a configuração Aplicar a partir de uma secção [privada do repositório git](#apply-configuration-from-a-private-git-repository) para mais detalhes.

#### <a name="use-a-private-git-repository-with-ssh-and-user-provided-keys"></a>Use um repositório privado de Git com SSH e chaves fornecidas pelo utilizador

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir `user@` |
| `--ssh-private-key` | chave codificada base64 em [formato PEM](https://aka.ms/PEMformat) | Fornecer chave diretamente |
| `--ssh-private-key-file` | caminho completo para arquivo local | Fornecer caminho completo para o arquivo local que contém a chave de formato PEM

> [!NOTE]
> Forneça a sua própria chave privada diretamente ou num ficheiro. A chave deve estar no [formato PEM](https://aka.ms/PEMformat) e terminar com newline (\n).  A chave pública associada deve ser adicionada à conta de utilizador no seu prestador de serviços Git. Se a chave for adicionada ao repositório em vez da conta de utilizador, `git@` utilize-a em vez de `user@` . Salte para a configuração Aplicar a partir de uma secção [privada do repositório git](#apply-configuration-from-a-private-git-repository) para mais detalhes.

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Use um anfitrião Git privado com SSH e anfitriões conhecidos fornecidos pelo utilizador

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| `--repository-url`  | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir `user@` |
| `--ssh-known-hosts` | base64-codificado | Fornecer conteúdo conhecido dos anfitriões diretamente |
| `--ssh-known-hosts-file` | caminho completo para arquivo local | Fornecer conteúdo conhecido dos anfitriões em um arquivo local |

> [!NOTE]
> Para autenticar o repositório git antes de estabelecer a ligação SSH, o operador do Fluxo mantém uma lista de anfitriões git comuns no seu ficheiro de anfitriões conhecido. Se estiver a utilizar um repositório git incomum ou o seu próprio anfitrião Git, poderá ter de fornecer a chave hospedeira para garantir que o Flux possa identificar o seu repo. Pode fornecer o seu conteúdo known_hosts diretamente ou num ficheiro. Utilize as [especificações do formato de conteúdo known_hosts](https://aka.ms/KnownHostsFormat) em conjunto com um dos cenários-chave SSH acima descritos ao fornecer o seu próprio conteúdo.

#### <a name="use-a-private-git-repository-with-https"></a>Use um repositório privado de Git com HTTPS

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| `--repository-url` | https://server/repo[.git] | HTTPS com auth básico |
| `--https-user` | cru ou base64 codificado | Nome de utilizador HTTPS |
| `--https-key` | cru ou base64 codificado | Token de acesso pessoal https ou senha

> [!NOTE]
> HTTPS Helm auth de lançamento privado é suportado apenas com a versão 1.2.0+ do gráfico do operador Helm (padrão).
> HTTPS Helm lança auth privada não é suportado atualmente para clusters geridos pelos Serviços Azure Kubernetes.
> Se precisar do Flux para aceder ao repositório git através do seu representante, então terá de atualizar os agentes do Azure Arc com as definições de procuração. Para obter mais informações, consulte [Connect utilizando um servidor de procuração de saída.](./connect-cluster.md#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Parâmetros adicionais

Personalize a configuração com os seguintes parâmetros opcionais:

| Parâmetro | Descrição |
| ------------- | ------------- |
| `--enable-helm-operator`| Altere para ativar o suporte para implementações de gráficos Helm. |
| `--helm-operator-params` | Valores de gráfico para o operador helm (se ativado). Por exemplo, `--set helm.versions=v3`. |
| `--helm-operator-version` | Versão de gráfico para operador helm (se ativado). Use a versão 1.2.0+. Predefinição: '1.2.0'. |
| `--operator-namespace` | Nome para o espaço de nome do operador. Predefinição: 'predefinição'. 23 caracteres. |
| `--operator-params` | Parâmetros para o operador. Deve ser dado dentro de cotações únicas. Por exemplo, ```--operator-params='--git-readonly --sync-garbage-collection --git-branch=main' ``` 

##### <a name="options-supported-in----operator-params"></a>Opções suportadas  `--operator-params` em:

| Opção | Descrição |
| ------------- | ------------- |
| `--git-branch`  | Ramo do repositório de Git para usar para manifestos de Kubernetes. O padrão é "mestre". Os repositórios mais recentes têm o nome de root `main` branch, caso em que precisa de `--git-branch=main` definir. |
| `--git-path`  | Caminho relativo dentro do repositório de Git para o Flux localizar manifestos de Kubernetes. |
| `--git-readonly` | O repositório de Git será considerado apenas para leitura; O Fluxo não vai tentar escrever-lhe. |
| `--manifest-generation`  | Se ativado, o Flux procurará .flux.yaml e executará Kustomize ou outros geradores manifestos. |
| `--git-poll-interval`  | Período para sondagem do repositório de Git para novos compromissos. O padrão é `5m` (5 minutos). |
| `--sync-garbage-collection`  | Se ativado, o Flux apagará os recursos que criou, mas já não está presente no Git. |
| `--git-label`  | Etiqueta para acompanhar o progresso da sincronização. Costumava marcar o ramo git.  A predefinição é `flux-sync`. |
| `--git-user`  | Nome de utilizador para git cometer. |
| `--git-email`  | E-mail para usar para git cometer. 

Se não quiser que o Flux escreva para o repositório e `--git-user` `--git-email` ou não esteja definido, então `--git-readonly` será automaticamente definido.

Para mais informações, consulte a [documentação do Fluxo.](https://aka.ms/FluxcdReadme)

> [!TIP]
> Pode criar um `sourceControlConfiguration` no portal Azure no separador **GitOps** do Arco Azure ativado pelo recurso Kubernetes.

## <a name="validate-the-sourcecontrolconfiguration"></a>Validar a fonteControlConfiguration

Utilize o CLI Azure para validar que o `sourceControlConfiguration` foi criado com sucesso.

```azurecli
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

O `sourceControlConfiguration` recurso será atualizado com o estado de conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
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

Quando um `sourceControlConfiguration` é criado ou atualizado, algumas coisas acontecem sob o capot:

1. O Azure Arc `config-agent` monitoriza o Azure Resource Manager para configurações novas ou atualizadas `Microsoft.KubernetesConfiguration/sourceControlConfigurations` e nota a nova `Pending` configuração.
1. Lê `config-agent` as propriedades de configuração e cria o espaço de nome de destino.
1. O Arco Azure `controller-manager` prepara uma Conta de Serviço Kubernetes com a permissão (ou âmbito) adequada `cluster` `namespace` e, em seguida, implementa um exemplo de `flux` .
1. Se utilizar a opção de SSH com teclas geradas pelo Fluxox, `flux` gera uma chave SSH e regista a chave pública.
1. O `config-agent` estado dos relatórios volta ao recurso em `sourceControlConfiguration` Azure.

Enquanto o processo de provisionamento acontece, o `sourceControlConfiguration` irá mover-se através de algumas mudanças de estado. Monitorize o progresso com o `az k8sconfiguration show ...` comando acima:

| Mudança de palco | Description |
| ------------- | ------------- |
| `complianceStatus`-> `Pending` | Representa os estados iniciais e em curso. |
| `complianceStatus` -> `Installed`  | `config-agent` foi capaz de configurar com sucesso o cluster e implementar `flux` sem erros. |
| `complianceStatus` -> `Failed` | `config-agent` encontrou um erro de `flux` implantação, os detalhes devem estar disponíveis no corpo de `complianceStatus.message` resposta. |

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório privado de Git

Se estiver a usar um repositório privado de Git, precisa de configurar a chave pública SSH no seu repositório. A chave pública SSH será a que o Flux gera ou a que fornece. Pode configurar a chave pública no repositório específico de Git ou no utilizador Git que tenha acesso ao repositório. 

### <a name="get-your-own-public-key"></a>Obtenha a sua própria chave pública

Se gerou as suas próprias chaves SSH, então já tem as chaves privadas e públicas.

#### <a name="get-the-public-key-using-azure-cli"></a>Obtenha a chave pública usando Azure CLI 

O seguinte é útil se o Flux gerar as teclas.

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --cluster-type connectedClusters --query 'repositoryPublicKey' 
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

#### <a name="get-the-public-key-from-the-azure-portal"></a>Obtenha a chave pública do portal Azure

O seguinte é útil se o Flux gerar as teclas.

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

**Saída:**

```console
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

O `flux` operador foi implantado para o espaço de `cluster-config` nome, como dirigido pelo `sourceControlConfig` nosso:

```console
kubectl -n cluster-config get deploy  -o wide
```

**Saída:**

```console
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

## <a name="delete-a-configuration"></a>Excluir uma configuração

Elimine uma `sourceControlConfiguration` utilização do portal Azure CLI ou Azure.  Depois de iniciar o comando de eliminação, o `sourceControlConfiguration` recurso será eliminado imediatamente em Azure. A eliminação total dos objetos associados do cluster deve ocorrer dentro de 10 minutos. Se o `sourceControlConfiguration` estado está em estado de falha quando removido, a eliminação total dos objetos associados pode demorar até uma hora.

> [!NOTE]
> Depois de criado um `sourceControlConfiguration` `namespace` com âmbito, os utilizadores com `edit` ligação de papel no espaço de nome podem implementar cargas de trabalho neste espaço de nome. Quando isto `sourceControlConfiguration` com o âmbito do espaço de nome é eliminado, o espaço de nome é deixado intacto e não será eliminado para evitar quebrar essas outras cargas de trabalho. Se necessário, pode eliminar este espaço de nome manualmente com `kubectl` .  
> Quaisquer alterações ao cluster que foram o resultado de implantações do repositório de Git rastreado não são eliminadas quando o `sourceControlConfiguration` é eliminado.

```azurecli
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Passos seguintes

- [Use o leme com configuração de controlo de origem](./use-gitops-with-helm.md)
- [Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)
