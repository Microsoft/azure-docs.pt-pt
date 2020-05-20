---
title: Utilize GitOps para uma configuração de cluster ativada por Arco Azul (Pré-visualização)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utilize GitOps para uma configuração de cluster ativada por Arco Azul (Pré-visualização)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, contentores
ms.openlocfilehash: e945a1d39edb6dad43e66ac492eb1e5c36ff58e1
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83684209"
---
# <a name="use-gitops-for-an-azure-arc-enabled--configuration-preview"></a>Utilize GitOps para uma configuração ativada por Arco Azul (Pré-visualização)

Esta arquitetura usa um fluxo de trabalho GitOps para configurar o cluster e implementar aplicações. A configuração é descrita declarativamente em ficheiros .yaml e armazenada em Git. Um agente observa o repo git para alterações e aplica-as.  O mesmo agente também assegura periodicamente que o estado de agrupamento corresponde ao estado declarado no repo git e devolve o cluster ao estado pretendido se ocorrerem alterações não geridas.

A ligação entre o seu cluster e um ou mais repositórios Git é rastreada no Azure Resource Manager como um recurso de `sourceControlConfiguration` extensão. As propriedades de recursos representam onde e como os `sourceControlConfiguration` recursos kubernetes devem fluir de Git para o seu cluster. Os `sourceControlConfiguration` dados são armazenados encriptados em repouso numa base de dados da CosmosDb para garantir a confidencialidade dos dados.

O Azure Arc permitiu que kubernetes `config-agent` funcionassem no seu cluster é responsável por observar recursos novos ou `sourceControlConfiguration` atualizados e orquestra adicionar, atualizar ou remover automaticamente as ligações de repo Git.

Os mesmos padrões podem ser usados para gerir uma coleção maior de clusters, que podem ser implantados em ambientes heterogéneos. Por exemplo, pode ter um repositório que define a configuração de base para a sua organização e aplique isso a dezenas de clusters Kubernetes de uma só vez.

O repositório Git pode conter quaisquer recursos Kubernetes válidos, incluindo Espaços nomes, ConfigMaps, Implementações, DaemonSets, etc.  Também pode conter gráficos Helm para aplicações de implementação. Um conjunto comum de cenários inclui a definição de uma configuração de base para a sua organização, que pode incluir funções e encadernações Comuns rBAC, agentes de monitorização ou registo, ou serviços em todo o cluster.

Este guia de arranque irá acompanhá-lo através da aplicação de um conjunto de configurações com âmbito de administração de cluster.

## <a name="create-a-configuration"></a>Criar uma configuração

- Exemplo de repositório:<https://github.com/slack/cluster-config>

O repositório de exemplo é estruturado em torno da persona de um operador de cluster que gostaria de fornecer alguns espaços de nome, implementar uma carga de trabalho comum, e fornecer alguma configuração específica da equipa. A utilização deste repositório cria os seguintes recursos no seu cluster:

Espaços de **nome:** `cluster-config` , `team-a` `team-b` 
 **implantação:** `cluster-config/azure-vote` 
 **ConfigMap:**`team-a/endpoints`

As `config-agent` sondagens Azure para novas ou atualizadas a `sourceControlConfiguration` cada 30 segundos.  Este é o tempo máximo que levará para que o `config-agent` pick-up uma configuração nova ou atualizada.
Se estiver a associar um repositório privado, certifique-se de que também completa os passos na [configuração Apply a partir de um repositório git privado](https://github.com/Azure/azure-arc-kubernetes-preview/blob/master/docs/use-gitops-in-connected-cluster.md#apply-configuration-from-a-private-git-repository)

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Utilizando a extensão Azure CLI para, vamos ligar o `k8sconfiguration` nosso cluster conectado a um [repositório git exemplo](https://github.com/Azure/arc-k8s-demo). Daremos a esta configuração um `cluster-config` nome, instruímos o agente a implantar o operador no `cluster-config` espaço de nome, e daremos `cluster-admin` permissões ao operador.

```console
az k8sconfiguration create \
    --name cluster-config \
    --cluster-name AzureArcTest1 --resource-group AzureArcTest \
    --operator-instance-name cluster-config --operator-namespace cluster-config \
    --repository-url https://github.com/Azure/arc-k8s-demo \
    --scope cluster --cluster-type connectedClusters
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

#### <a name="repository-url-parameter"></a>parâmetro de url de repositório

Aqui estão os cenários suportados para o valor do parâmetro -repositório-url.

| Cenário | Formato | Descrição |
| ------------- | ------------- | ------------- |
| Private GitHub repo - SSH | git@github.com:username/repo | Par de tecladoS SSH gerado pelo Flux.  O utilizador deve adicionar a chave pública à conta GitHub como Chave de Implementação. |
| Repo gitHub público | `http://github.com/username/repo`ou git://github.com/username/repo   | Repo git público  |

Estes cenários são suportados pelo Flux, mas não por fonteControlConfiguration ainda. 

| Cenário | Formato | Descrição |
| ------------- | ------------- | ------------- |
| Private GitHub repo - HTTPS | `https://github.com/username/repo` | O fluxo não gera teclado SSH.  [Instruções](https://docs.fluxcd.io/en/1.17.0/guides/use-git-https.html) |
| Anfitrião privado git | user@githost:path/to/repo | [Instruções](https://docs.fluxcd.io/en/1.18.0/guides/use-private-git-host.html) |
| Private GitHub repo - SSH (traga a sua própria chave) | git@github.com:username/repo | [Use o seu próprio teclado SSH](https://docs.fluxcd.io/en/1.17.0/guides/provide-own-ssh-key.html) |


#### <a name="additional-parameters"></a>Parâmetros adicionais

Para personalizar a criação de configuração, aqui estão alguns parâmetros adicionais:

`--enable-helm-operator`: Interruptor *opcional* para ativar suporte para implementações de gráficos Helm. Esta configuração está desativada, por predefinição.

`--helm-operator-chart-values`: Valores de gráfico *opcionais* para o operador Helm (se ativado).  Por exemplo, '-set helm.versions=v3'.

`--helm-operator-chart-version`: Versão *opcional* do gráfico para o operador Helm (se ativado). Predefinição: '0.6.0'.

`--operator-namespace`: Nome *opcional* para o espaço de nome do operador. Padrão: 'padrão'

`--operator-params`: Parâmetros *opcionais* para o operador. Deve ser dada em orçamentos únicos. Por exemplo, ```--operator-params='--git-readonly --git-path=releases/prod' ```

Opções suportadas em --operador-params

| Opção | Descrição |
| ------------- | ------------- |
| -git-branch  | Ramo de git repo para usar para manifestos Kubernetes. Padrão é 'mestre'. |
| --git-path  | Caminho relativo dentro do repo Git para O Fluxo para localizar kubernetes manifesta-se. |
| --git-readonly | Git repo será considerado apenas leitura; O Fluxo não vai tentar escrever-lhe. |
| --manifesta-geração  | Se ativado, o Flux procurará .flux.yaml e executará kustomize ou outros geradores manifestos. |
| --git-poll-intervalo  | Período em que votar git repo para novos compromissos. Predefinição é '5m' (5 minutos). |
| --sync-lixo-recolha  | Se ativado, o Flux eliminará os recursos que criou, mas já não estão presentes em Git. |
| --git-label  | Etiqueta para acompanhar o progresso da sincronização, usada para marcar o ramo Git.  Padrão é 'flux-sync'. |
| --git-user  | Nome de utilizador para git commit. |
| --git-e-e-mail  | E-mail para usar para se comprometer. |

* Se '--git-user' ou '-git-email' não estiverem definidos (o que significa que não quer que o Flux escreva para o repo), então --git-read apenas será automaticamente definido (se ainda não o tiver definido).

* Se ativar o Operador Helmoperator é verdadeiro, então as cordas operatorInstanceName + operatorNamespace não podem exceder 47 caracteres combinados.  Se não aderir a este limite, terá este erro:

```console
{"OperatorMessage":"Error: {failed to install chart from path [helm-operator] for release [<operatorInstanceName>-helm-<operatorNamespace>]: err [release name \"<operatorInstanceName>-helm-<operatorNamespace>\" exceeds max length of 53]} occurred while doing the operation : {Installing the operator} on the config","ClusterState":"Installing the operator"}
```

Para mais informações consulte a [documentação do Flux](https://aka.ms/FluxcdReadme).

## <a name="validate-the-sourcecontrolconfiguration"></a>Validar a configuração de controlo de origem

A utilização do Azure CLI valida que o `sourceControlConfiguration` foi criado com sucesso.

```console
az k8sconfiguration show --resource-group AzureArcTest --name cluster-config --cluster-name AzureArcTest1 --cluster-type connectedClusters
```

Note que o recurso é atualizado com o estado de `sourceControlConfiguration` conformidade, mensagens e informações de depuração.

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
{
  "complianceStatus": {
    "complianceState": "Compliant",
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

Quando o `sourceControlConfiguration` é criado, algumas coisas acontecem sob o capuz:

1. O Azure Arc monitoriza o Gestor de `config-agent` Recursos Azure para configurações novas ou atualizadas ( `Microsoft.KubernetesConfiguration/sourceControlConfiguration` )
1. `config-agent`nota a nova `Pending` configuração
1. `config-agent`lê as propriedades de configuração e prepara-se para implementar uma instância gerida de`flux`
    * `config-agent`cria o espaço de nome de destino
    * `config-agent`prepara uma Conta de Serviço Kubernetes com a permissão `cluster` (ou `namespace` âmbito adequado)
    * `config-agent`implementa uma instância de`flux`
    * `flux`gera uma chave SSH e regista a chave pública
1. `config-agent`relatórios estado de volta para o`sourceControlConfiguration`

Enquanto o processo de provisionamento acontece, o `sourceControlConfiguration` irá mover-se através de algumas mudanças de Estado. Monitorize o progresso com o `az k8sconfiguration show ...` comando acima:

1. `complianceStatus` -> `Pending`: representa os estados iniciais e em curso
1. `complianceStatus` -> `Compliant`: foi capaz de `config-agent` configurar com sucesso o cluster e implantar `flux` sem erros
1. `complianceStatus` -> `Noncompliant`: encontrou uma implementação de `config-agent` `flux` erro, os detalhes devem estar disponíveis no corpo de `complianceStatus.message` resposta

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório git privado

Se estiver a usar um repo git privado, então precisa de realizar mais uma tarefa para fechar o loop: precisa adicionar a chave pública que foi gerada como `flux` chave **de implantação** no repo.

**Obtenha a chave pública usando o Azure CLI**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Obtenha a chave pública do portal Azure**

1. No portal Azure, navegue para o recurso de cluster conectado.
2. Na página de recursos, selecione "Configurações" e consulte a lista de configurações para este cluster.
3. Selecione a configuração que utiliza o repositório git privado.
4. Na janela de contexto que abre, na parte inferior da janela, copia a chave pública do **repositório.**

**Adicione a chave pública como chave de implantação para o git repo**

1. Abra o GitHub, navegue até ao garfo, às **Definições,** em seguida, para **implementar chaves**
2. Clique na **chave de implementação adicionar**
3. Fornecer um Título
4. Verificar **Permitir o acesso à escrita**
5. Colar a chave pública (menos quaisquer aspas circundantes)
6. Clique na **chave Adicionar**

Consulte os médicos gitHub para obter mais informações sobre como gerir as chaves de implementação.

**Se estiver a utilizar um repositório Azure DevOps, adicione a chave às suas teclas SSH**

1. Em **Definições** do Utilizador no direito superior (ao lado da imagem de perfil), clique em **teclas públicas SSH**
1. Selecione **+ Nova Chave**
1. Forneça um nome
1. Colar a chave pública sem qualquer citação circundante
1. Clique em **Adicionar**

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração kubernetes

Depois de `config-agent` ter instalado a `flux` instância, os recursos retidos no repositório git devem começar a fluir para o cluster. Verifique se foram criados espaços de nome, implantações e recursos:

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

Podemos ver que, e espaços de `team-a` nome foram `team-b` `itops` `cluster-config` criados.

O `flux` operador foi implantado para o espaço de `cluster-config` nome, conforme dirigido pelo `sourceControlConfig` nosso:

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

## <a name="delete-a-configuration"></a>Eliminar uma configuração

Pode eliminar `sourceControlConfiguration` um portal Azure CLI ou Azure.  Após iniciar o comando de eliminação, o `sourceControlConfiguration` recurso será eliminado imediatamente em Azure, mas pode demorar até 1 hora para a eliminação total dos objetos associados do cluster (temos um item de atraso para encurtar isto). Se o foi criado com o espaço de nome, esse espaço de `sourceControlConfiguration` nome não será eliminado do cluster (para evitar quebrar quaisquer outros recursos que possam ter sido criados nesse espaço de nome).

Note que quaisquer alterações ao cluster resultantes de implantações do repo git rastreado não são eliminadas quando o `sourceControlConfiguration` é eliminado.

```console
az k8sconfiguration delete --name '<config name>' -g '<resource group name>' --cluster-name '<cluster name>' --cluster-type connectedClusters
```

**Saída:**

```console
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
```

## <a name="next-steps"></a>Passos seguintes

- [Use GitOps com Helm para configuração de cluster](./use-gitops-with-helm.md)
- [Use a Política Azure para governar a configuração do cluster](./use-azure-policy.md)
