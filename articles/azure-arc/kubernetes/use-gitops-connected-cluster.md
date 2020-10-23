---
title: Implementar configurações usando GitOps em Arc ativado cluster Kubernetes (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utilize GitOps para uma configuração de cluster ativada pelo Arco Azure (Pré-visualização)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, contentores
ms.openlocfilehash: 1a8839c2463494ba0e165bf9e1a5d22245fac8df
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/22/2020
ms.locfileid: "92371261"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Implementar configurações usando GitOps em Arc ativado cluster Kubernetes (Preview)

GitOps é a prática de declarar o estado desejado da configuração de Kubernetes (implementações, espaços de nome, e assim por diante) num repositório de Git seguido de uma votação e colocação baseada nestas configurações para o cluster usando um operador. Este documento abrange a configuração de tais fluxos de trabalho em agrupamentos de Kubernetes habilitados pelo Arco Azure.

A ligação entre o seu cluster e um ou mais repositórios Git é rastreada no Azure Resource Manager como um `sourceControlConfiguration` recurso de extensão. As `sourceControlConfiguration` propriedades dos recursos representam onde e como os recursos de Kubernetes devem fluir de Git para o seu cluster. Os `sourceControlConfiguration` dados são armazenados encriptados em repouso numa base de dados DB da Azure Cosmos para garantir a confidencialidade dos dados.

A `config-agent` execução no seu cluster é responsável por observar os recursos de extensão novos ou `sourceControlConfiguration` atualizados no Arco Azure, permitindo que o recurso Kubernetes, implantando um operador de fluxo para assistir ao repositório git, e propagando quaisquer atualizações feitas para o `sourceControlConfiguration` . É até possível criar múltiplos `sourceControlConfiguration` recursos com âmbito no mesmo Arco `namespace` Azure que permitiu que o cluster Kubernetes alcançasse o multi-arrendamento. Neste caso, cada operador só pode implementar configurações para o seu respetivo espaço de nome.

O repositório git pode conter quaisquer recursos válidos de Kubernetes, incluindo Namespaces, ConfigMaps, Deployments, DaemonSets, etc.  Também pode conter gráficos helm para a implementação de aplicações. Um conjunto comum de cenários inclui a definição de uma configuração de base para a sua organização, que pode incluir papéis e encadernações comuns, agentes de monitorização ou de exploração madeireira, ou serviços em todo o cluster.

O mesmo padrão pode ser usado para gerir uma maior coleção de aglomerados, que podem ser implantados em ambientes heterogéneos. Por exemplo, pode ter um repositório que define a configuração de base para a sua organização e aplicá-la a dezenas de clusters Kubernetes ao mesmo tempo. [A política Azure pode automatizar](use-azure-policy.md) a criação de um `sourceControlConfiguration` com um conjunto específico de parâmetros em todo o Arco Azure habilitado recursos Kubernetes sob um âmbito (subscrição ou grupo de recursos).

Este guia de arranque irá acompanhá-lo através da aplicação de um conjunto de configurações com âmbito de administração de cluster.

## <a name="before-you-begin"></a>Antes de começar

Este artigo pressupõe que você tem um Azure Arc existente habilitado cluster ligado Kubernetes. Se precisar de um cluster ligado, consulte o [arranque rápido](./connect-cluster.md)do cluster .

## <a name="create-a-configuration"></a>Criar uma configuração

O [repositório de exemplo](https://github.com/Azure/arc-k8s-demo) utilizado neste documento é estruturado em torno da persona de um operador de cluster que gostaria de fornecer alguns espaços de nome, implementar uma carga de trabalho comum, e fornecer alguma configuração específica da equipa. A utilização deste repositório cria os seguintes recursos no seu cluster:

**Espaços de nome:** `cluster-config` , , `team-a` `team-b` 
 **Implantação:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

As `config-agent` sondagens Azure para novas ou `sourceControlConfiguration` atualizadas a cada 30 segundos, que é o tempo máximo necessário `config-agent` para captar uma configuração nova ou atualizada.
Se estiver a associar um repositório privado ao `sourceControlConfiguration` , certifique-se de que também completa os passos na [configuração De aplicar a partir de um repositório privado](#apply-configuration-from-a-private-git-repository)de git .

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Utilizando a extensão Azure CLI para , vamos ligar o `k8sconfiguration` nosso cluster conectado a um [repositório de git exemplo](https://github.com/Azure/arc-k8s-demo). Daremos a esta configuração um `cluster-config` nome, instruíremos o agente a implantar o operador no `cluster-config` espaço de nomes e dar-lhe permissões do `cluster-admin` operador.

```console
az k8sconfiguration create --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --operator-instance-name cluster-config --operator-namespace cluster-config --repository-url https://github.com/Azure/arc-k8s-demo --scope cluster --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "ComplianceStatus": 1,
    "clientAppliedTime": null,
    "level": 3,
    "message": "{\"OperatorMessage\":null,\"ClusterState\":null}"
  },
  "configKind": "Git",
  "configName": "cluster-config",
  "configOperator": {
    "operatorInstanceName": "cluster-config",
    "operatorNamespace": "cluster-config",
    "operatorParams": "--git-readonly",
    "operatorScope": "cluster",
    "operatorType": "flux"
  },
  "configType": "",
  "id": null,
  "name": null,
  "operatorInstanceName": null,
  "operatorNamespace": null,
  "operatorParams": null,
  "operatorScope": null,
  "operatorType": null,
  "providerName": "ConnectedClusters",
  "provisioningState": "Succeeded",
  "repositoryPublicKey": null,
  "repositoryUrl": null,
  "sourceControlConfiguration": {
    "repositoryPublicKey": "",
    "repositoryUrl": "git://github.com/Azure/arc-k8s-demo.git"
  },
  "type": null
}
```

#### <a name="repository-url-parameter"></a>parâmetro repositório-url

Aqui estão os cenários suportados para o valor do parâmetro --repositório-url.

| Cenário | Formato | Descrição |
| ------------- | ------------- | ------------- |
| Repo de Git público | http[s]://server/repo.git ou git://server/repo.git   | Repo de Git público  |
| Repo Git privado – SSH – Teclas criadas pelo fluxo | ssh://[user@]servidor/repo.git ou [user@]servidor:repo.git | A chave pública gerada pelo Flux precisa de ser adicionada à conta de utilizador ou repo no seu prestador de serviços Git. Estão disponíveis mais detalhes [aqui](#apply-configuration-from-a-private-git-repository) |

Estes cenários são apoiados pelo Flux, mas ainda não pela fonteControlConfiguration.

| Cenário | Formato | Descrição |
| ------------- | ------------- | ------------- |
| Repo Git privado - HTTPS | https://server/repo.git | Em breve (irá suportar o nome de utilizador/palavra-passe, nome de utilizador/token, certificado) |
| Repo Git Privado - SSH – Chaves fornecidas pelo utilizador | ssh://[user@]servidor/repo.git ou [user@]servidor:repo.git | Em breve |
| Anfitrião privado git – SSH – known_hosts personalizado | ssh://[user@]servidor/repo.git ou [user@]servidor:repo.git | Em breve |

#### <a name="additional-parameters"></a>Parâmetros adicionais

Para personalizar a criação de configuração, aqui estão alguns parâmetros adicionais:

`--enable-helm-operator` : *Comutador opcional* para permitir o suporte para implementações de gráficos Helm.

`--helm-operator-chart-values` : *Valores de* gráfico opcionais para o operador Helm (se ativado).  Por exemplo, '-set helm.versions=v3'.

`--helm-operator-chart-version` : *Versão de* gráfico opcional para o operador Helm (se ativado). Predefinição: '0.6.0'.

`--operator-namespace` : *Nome opcional* para o espaço de nome do operador. Predefinição: 'predefinição'

`--operator-params` : *Parâmetros opcionais* para o operador. Deve ser dado dentro de cotações únicas. Por exemplo, ```--operator-params='--git-readonly --git-path=releases' ```

Opções suportadas em --params do operador

| Opção | Descrição |
| ------------- | ------------- |
| --git-branch  | Ramo de git repo para usar para manifestos kubernetes. O padrão é "mestre". |
| --git-caminho  | Caminho relativo dentro do git repo para o Flux localizar manifestos de Kubernetes. |
| --git-readonly | Git repo será considerado apenas para ler; O Fluxo não vai tentar escrever-lhe. |
| --geração manifesta  | Se ativado, o Flux procurará .flux.yaml e executará Kustomize ou outros geradores manifestos. |
| --git-poll-intervalo  | Período para a sondagem de Git repo para novos compromissos. O predefinição é '5m' (5 minutos). |
| --sync-recolha de lixo  | Se ativado, o Flux apagará os recursos que criou, mas já não está presente no Git. |
| --git-label  | Etiqueta para acompanhar o progresso sincronizado, usado para marcar o ramo Git.  O padrão é "fluxo-sincronização". |
| --git-utilizador  | Nome de utilizador para git cometer. |
| --git-e-mail  | E-mail para usar para git cometer. |

* Se não estiverem definidos '--git-user' ou '-git-email' (o que significa que não quer que o Flux escreva para o repo), então --git-readonly será automaticamente definido (se ainda não o tiver definido).

* Se ativar o Operador de Operações é verdadeiro, então as cordas DeinstanceName + operador não podem exceder 47 caracteres combinados.  Se não cumprir este limite, terá o seguinte erro:

   ```console
   {"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
   ```

Para mais informações, consulte [a documentação do Flux.](https://aka.ms/FluxcdReadme)

> [!TIP]
> É possível criar uma fonteControlConfiguration no portal Azure, bem como no separador **Configurações** do Arco Azure ativado pela lâmina de recurso Kubernetes.

## <a name="validate-the-sourcecontrolconfiguration"></a>Validar a fonteControlConfiguration

A utilização do CLI Azure valida que o `sourceControlConfiguration` foi criado com sucesso.

```console
az k8sconfiguration show --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

Note que o `sourceControlConfiguration` recurso é atualizado com o estado de conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Installed",
    "lastConfigApplied": "2019-12-05T05:34:41.481000",
    "message": "...",
    "messageLevel": "3"
  },
  "id": "/subscriptions/57ac26cf-a9f0-4908-b300-9a4e9a0fb205/resourceGroups/AzureArcTest/providers/Microsoft.Kubernetes/connectedClusters/AzureArcTest1/providers/Microsoft.KubernetesConfiguration/sourceControlConfigurations/cluster-config",
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
  "type": "Microsoft.KubernetesConfiguration/sourceControlConfigurations"
}
```

Quando o `sourceControlConfiguration` é criado, algumas coisas acontecem sob o capot:

1. O Azure Arc `config-agent` monitoriza o Azure Resource Manager para configurações novas ou atualizadas ( `Microsoft.KubernetesConfiguration/sourceControlConfiguration`
1. `config-agent` nota a nova `Pending` configuração
1. `config-agent` lê as propriedades de configuração e prepara-se para implementar uma instância gerida de `flux`
    * `config-agent` cria o espaço de nome de destino
    * `config-agent` prepara uma Conta de Serviço Kubernetes com a permissão `cluster` (ou `namespace` âmbito) apropriado
    * `config-agent` implementa uma instância de `flux`
    * `flux` gera uma chave SSH e regista a chave pública
1. `config-agent` reporta estado de volta para o `sourceControlConfiguration`

Enquanto o processo de provisionamento acontece, o `sourceControlConfiguration` irá mover-se através de algumas mudanças de estado. Monitorize o progresso com o `az k8sconfiguration show ...` comando acima:

1. `complianceStatus` -> `Pending`: representa os estados iniciais e em curso
1. `complianceStatus` -> `Installed`: `config-agent` foi capaz de configurar com sucesso o cluster e implementar sem `flux` erro
1. `complianceStatus` -> `Failed`: `config-agent` encontrou um erro de implantação, os detalhes devem estar `flux` disponíveis no organismo de `complianceStatus.message` resposta

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar configuração a partir de um repositório de git privado

Se estiver a utilizar um repo git privado, então precisa executar mais uma tarefa para fechar o loop: adicione a chave pública gerada por `flux` como chave **implementar** no repo.

**Obtenha a chave pública usando Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Obtenha a chave pública do portal Azure**

1. No portal Azure, navegue para o recurso de cluster conectado.
2. Na página de recursos, selecione "Configurações" e consulte a lista de configurações deste cluster.
3. Selecione a configuração que utiliza o repositório privado de Git.
4. Na janela de contexto que se abre, na parte inferior da janela, copie a **chave pública do Repositório.**

**Adicione a chave pública como chave de implantação para o repo git**

1. Abra o GitHub, navegue para o seu repo, para **Definições,** em seguida, para **implementar teclas**
2. Clique  **na tecla de implementação de adicionar**
3. Fornecer um título
4. Verificar **Permitir o acesso de escrita**
5. Cole a chave pública (menos as aspas circundantes)
6. Clique **na tecla Adicionar**

Consulte os docs do GitHub para obter mais informações sobre como gerir estas chaves.

**Se estiver a utilizar um repositório Azure DevOps, adicione a chave às suas teclas SSH**

1. Nas **Definições do Utilizador** no topo direito (ao lado da imagem de perfil), clique nas **teclas públicas SSH**
1. Selecione  **+ Nova Tecla**
1. Fornecer um nome
1. Cole a chave pública sem quaisquer cotações circundantes
1. Clique **em Adicionar**

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração de Kubernetes

Depois de `config-agent` instalado o `flux` caso, os recursos retidos no repositório de git devem começar a fluir para o cluster. Verifique se os espaços de nome, implementações e recursos foram criados:

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

Pode explorar os outros recursos implantados como parte do repositório de configuração:

```console
kubectl -n team-a get cm -o yaml
kubectl -n itops get all
```

## <a name="delete-a-configuration"></a>Excluir uma configuração

Elimine uma `sourceControlConfiguration` utilização do portal Azure CLI ou Azure.  Depois de iniciar o comando de eliminação, o `sourceControlConfiguration` recurso será eliminado imediatamente em Azure, mas pode demorar até 1 hora para a eliminação total dos objetos associados do cluster (temos um item de atraso para reduzir este desfasamento de tempo).

> [!NOTE]
> Depois de ser criada uma fonteControlConfiguration com âmbito de espaço de nome, é possível que os utilizadores com `edit` ligação de funções no espaço de nomes implementem cargas de trabalho neste espaço de nome. Quando isto `sourceControlConfiguration` com o âmbito do espaço de nome é eliminado, o espaço de nome é deixado intacto e não será eliminado para evitar quebrar essas outras cargas de trabalho.
> Quaisquer alterações ao cluster que foram o resultado de implementações do repo git rastreado não são eliminadas quando a `sourceControlConfiguration` placa é eliminada.

```console
az k8sconfiguration delete --name cluster-config --cluster-name AzureArcTest1 --resource-group AzureArcTest --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Passos seguintes

- [Use o leme com configuração de controlo de origem](./use-gitops-with-helm.md)
- [Use a política do Azure para governar a configuração do cluster](./use-azure-policy.md)
