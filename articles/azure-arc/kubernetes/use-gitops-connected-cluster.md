---
title: Implementar configurações usando GitOps em Arc ativado cluster Kubernetes (Preview)
services: azure-arc
ms.service: azure-arc
ms.date: 05/19/2020
ms.topic: article
author: mlearned
ms.author: mlearned
description: Utilize GitOps para configurar um cluster Kubernetes ativado pelo Arco Azure (Pré-visualização)
keywords: GitOps, Kubernetes, K8s, Azure, Arc, Azure Kubernetes Service, AKS, contentores
ms.openlocfilehash: 85771824a6cecd10346937220e400028a4570377
ms.sourcegitcommit: ad677fdb81f1a2a83ce72fa4f8a3a871f712599f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/17/2020
ms.locfileid: "97653457"
---
# <a name="deploy-configurations-using-gitops-on-arc-enabled-kubernetes-cluster-preview"></a>Implementar configurações usando GitOps em Arc ativado cluster Kubernetes (Preview)

GitOps, no que diz respeito a Kubernetes, é a prática de declarar o estado desejado da configuração de Kubernetes (implementações, espaços de nome, etc.) num repositório de Git seguido de uma colocação baseada em sondagens e puxar estas configurações para o cluster usando um operador. Este documento abrange a configuração de tais fluxos de trabalho em agrupamentos de Kubernetes habilitados pelo Arco Azure.

A ligação entre o seu cluster e um repositório Git é criada no Azure Resource Manager como um `Microsoft.KubernetesConfiguration/sourceControlConfigurations` recurso de extensão. As `sourceControlConfiguration` propriedades dos recursos representam onde e como os recursos de Kubernetes devem fluir de Git para o seu cluster. Os `sourceControlConfiguration` dados são armazenados encriptados em repouso numa base de dados DB da Azure Cosmos para garantir a confidencialidade dos dados.

A `config-agent` execução no seu cluster é responsável por observar os recursos de extensão novos ou `sourceControlConfiguration` atualizados no Arco Azure, permitindo que o recurso Kubernetes, para a implementação de um operador de Fluxo para vigiar o repositório git para cada `sourceControlConfiguration` um , e aplicar quaisquer atualizações feitas a qualquer `sourceControlConfiguration` . É possível criar múltiplos `sourceControlConfiguration` recursos no mesmo Arco Azure que permitiu que o cluster Kubernetes alcançasse o multi-arrendamento. Pode criar cada `sourceControlConfiguration` um com um âmbito diferente para limitar as `namespace` implementações dentro dos respetivos espaços de nome.

O repositório git pode conter manifestos de formato YAML que descrevem quaisquer recursos válidos de Kubernetes, incluindo Namespaces, ConfigMaps, Deployments, DaemonSets, etc.  Também pode conter gráficos helm para a implementação de aplicações. Um conjunto comum de cenários inclui a definição de uma configuração de base para a sua organização, que pode incluir papéis e encadernações comuns, agentes de monitorização ou de exploração madeireira, ou serviços em todo o cluster.

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
Se estiver a associar um repositório privado ao `sourceControlConfiguration` , certifique-se de que também completa os passos na [configuração Apply a partir de um repositório privado de Git](#apply-configuration-from-a-private-git-repository).

### <a name="using-azure-cli"></a>Utilizar a CLI do Azure

Utilize a extensão Azure CLI `k8sconfiguration` para ligar um cluster ligado ao [repositório de git exemplo](https://github.com/Azure/arc-k8s-demo). Daremos a esta configuração um `cluster-config` nome, instruíremos o agente a implantar o operador no `cluster-config` espaço de nomes e dar-lhe permissões do `cluster-admin` operador.

```console
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

#### <a name="use-a-public-git-repo"></a>Use um repo git público

| Parâmetro | Formato |
| ------------- | ------------- |
| --repositório-url | http[s]://server/repo[.git] ou git://server/repo[.git]

#### <a name="use-a-private-git-repo-with-ssh-and-flux-created-keys"></a>Use um repo Git privado com teclas criadas por SSH e Fluxo

| Parâmetro | Formato | Notas
| ------------- | ------------- | ------------- |
| --repositório-url | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir por `user@`

> [!NOTE]
> A chave pública gerada pelo Flux deve ser adicionada à conta de utilizador no seu prestador de serviços Git. Se a chave for adicionada ao repo em vez de > a conta do utilizador, `git@` utilize-a no lugar `user@` do URL. [Ver mais detalhes](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-repo-with-ssh-and-user-provided-keys"></a>Use um repo Git privado com SSH e chaves fornecidas pelo utilizador

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| --repositório-url  | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir por `user@` |
| --ssh-private-key | chave codificada base64 em [formato PEM](https://aka.ms/PEMformat) | Fornecer chave diretamente |
| --ssh-private-key-file | caminho completo para arquivo local | Fornecer caminho completo para o arquivo local que contém a chave de formato PEM

> [!NOTE]
> Forneça a sua própria chave privada diretamente ou num ficheiro. A chave deve estar no [formato PEM](https://aka.ms/PEMformat) e terminar com newline (\n).  A chave pública associada deve ser adicionada à conta de utilizador no seu prestador de serviços Git. Se a chave for adicionada ao repo em vez da conta do utilizador, `git@` utilize-a em vez de `user@` . [Ver mais detalhes](#apply-configuration-from-a-private-git-repository)

#### <a name="use-a-private-git-host-with-ssh-and-user-provided-known-hosts"></a>Use um anfitrião Git privado com SSH e anfitriões conhecidos fornecidos pelo utilizador

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| --repositório-url  | ssh://user@server/repo[.git] ou user@server:repo [.git] | `git@` pode substituir por `user@` |
| --ssh-conhecidos-anfitriões | base64-codificado | conteúdo conhecido anfitriões fornecido diretamente |
| --ssh-conhecido-arquivo de anfitriões | caminho completo para arquivo local | conteúdo conhecido dos anfitriões fornecido em um arquivo local

> [!NOTE]
> O operador flux mantém uma lista de anfitriões git comuns no seu ficheiro anfitrião conhecido, a fim de autenticar o repo Git antes de estabelecer a ligação SSH. Se estiver a usar um repo Git incomum ou o seu próprio hospedeiro Git, poderá ter de fornecer a chave hospedeira para garantir que o Flux possa identificar o seu repo. Pode fornecer o conteúdo dos anfitriões conhecidos diretamente ou num ficheiro. [Ver especificação de formato de conteúdo dos anfitriões conhecidos.](https://aka.ms/KnownHostsFormat)
> Pode usá-lo em conjunto com um dos cenários-chave SSH acima descritos.

#### <a name="use-a-private-git-repo-with-https"></a>Use um git repo privado com HTTPS

| Parâmetro | Formato | Notas |
| ------------- | ------------- | ------------- |
| --repositório-url | https://server/repo[.git] | HTTPS com auth básico |
| --https-utilizador | cru ou base64 codificado | Nome de utilizador HTTPS |
| --https-chave | cru ou base64 codificado | Token de acesso pessoal https ou senha

> [!NOTE]
> HTTPS Helm auth privado é suportado apenas com a versão do gráfico do operador Helm >= 1.2.0.  A versão 1.2.0 é utilizada por predefinição.
> HTTPS Helm lança auth privada não é suportado atualmente para clusters geridos Azure Kubernetes Services.
> Se precisar de Fluxo para aceder ao Git repo através do seu representante, então terá de atualizar os agentes do Azure Arc com as definições de procuração. [Mais informações](https://docs.microsoft.com/azure/azure-arc/kubernetes/connect-cluster#connect-using-an-outbound-proxy-server)

#### <a name="additional-parameters"></a>Parâmetros adicionais

Para personalizar a configuração, aqui estão mais parâmetros que pode utilizar:

`--enable-helm-operator` : *Comutador opcional* para permitir o suporte para implementações de gráficos Helm.

`--helm-operator-params` : *Valores de* gráfico opcionais para o operador Helm (se ativado).  Por exemplo, '-set helm.versions=v3'.

`--helm-operator-chart-version` : *Versão de* gráfico opcional para o operador Helm (se ativado). Predefinição: '1.2.0'.

`--operator-namespace` : *Nome opcional* para o espaço de nome do operador. Predefinição: 'predefinição'

`--operator-params` : *Parâmetros opcionais* para o operador. Deve ser dado dentro de cotações únicas. Por exemplo, ```--operator-params='--git-readonly --git-path=releases --sync-garbage-collection' ```

Opções suportadas em --params do operador

| Opção | Descrição |
| ------------- | ------------- |
| --git-branch  | Ramo de Git repo para usar para manifestos kubernetes. O padrão é "mestre". |
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
> É possível criar uma fonteControlConfiguration no portal Azure no separador **GitOps** do Arco Azure ativado recurso Kubernetes.

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

Quando o `sourceControlConfiguration` é criado, algumas coisas acontecem sob o capot:

1. O Azure Arc `config-agent` monitoriza o Azure Resource Manager para configurações novas ou atualizadas ( `Microsoft.KubernetesConfiguration/sourceControlConfigurations`
1. `config-agent` nota a nova `Pending` configuração
1. `config-agent` lê as propriedades de configuração e prepara-se para implementar uma instância gerida de `flux`
    * `config-agent` cria o espaço de nome de destino
    * `config-agent` prepara uma Conta de Serviço Kubernetes com a permissão `cluster` (ou `namespace` âmbito) apropriado
    * `config-agent` implementa uma instância de `flux`
    * `flux` gera uma chave SSH e regista a chave pública (se utilizar a opção de SSH com teclas geradas pelo fluxo)
1. `config-agent` reporta estado de volta ao `sourceControlConfiguration` recurso em Azure

Enquanto o processo de provisionamento acontece, o `sourceControlConfiguration` irá mover-se através de algumas mudanças de estado. Monitorize o progresso com o `az k8sconfiguration show ...` comando acima:

1. `complianceStatus` -> `Pending`: representa os estados iniciais e em curso
1. `complianceStatus` -> `Installed`: `config-agent` foi capaz de configurar com sucesso o cluster e implementar sem `flux` erro
1. `complianceStatus` -> `Failed`: `config-agent` encontrou um erro de implantação, os detalhes devem estar `flux` disponíveis no organismo de `complianceStatus.message` resposta

## <a name="apply-configuration-from-a-private-git-repository"></a>Aplicar a configuração de um repositório privado de Git

Se você está usando um git repo privado, então você precisa configurar a chave pública SSH no seu repo. Você pode configurar a chave pública no repo Git ou no utilizador Git que tem acesso ao repo. A chave pública SSH será a que fornece ou a que o Flux gera.

**Obtenha a sua própria chave pública**

Se gerou as suas próprias chaves SSH, então já tem as chaves privadas e públicas.

**Obtenha a chave pública usando Azure CLI (útil se o Fluxo gerar as teclas)**

```console
$ az k8sconfiguration show --resource-group <resource group name> --cluster-name <connected cluster name> --name <configuration name> --query 'repositoryPublicKey'
Command group 'k8sconfiguration' is in preview. It may be changed/removed in a future release.
"ssh-rsa AAAAB3NzaC1yc2EAAAADAQABAREDACTED"
```

**Obtenha a chave pública do portal Azure (útil se o Fluxo gerar as teclas)**

1. No portal Azure, navegue para o recurso de cluster conectado.
2. Na página de recursos, selecione "GitOps" e consulte a lista de configurações deste cluster.
3. Selecione a configuração que utiliza o repositório privado de Git.
4. Na janela de contexto que se abre, na parte inferior da janela, copie a **chave pública do Repositório.**

Se estiver a utilizar o GitHub, utilize uma das seguintes 2 opções:

**Opção 1: Adicione a chave pública à sua conta de utilizador (aplica-se a todos os repos da sua conta)**

1. Abra o GitHub, clique no ícone de perfil no canto superior direito da página.
2. Clique em **Definições**
3. Clique nas **teclas SSH e GPG**
4. Clique na **chave SSH nova**
5. Fornecer um título
6. Cole a chave pública (menos as aspas circundantes)
7. Clique na **tecla Add SSH**

**Opção 2: Adicione a chave pública como chave de implantação para o repo Git (aplica-se apenas a este repo)**

1. Abra o GitHub, navegue para o seu repo, para **Definições,** em seguida, para **implementar teclas**
2. Clique na **tecla de implementação de adicionar**
3. Fornecer um título
4. Verificar **Permitir o acesso de escrita**
5. Cole a chave pública (menos as aspas circundantes)
6. Clique na **tecla Adicionar**

**Se estiver a utilizar um repositório Azure DevOps, adicione a chave às suas teclas SSH**

1. Nas **Definições do Utilizador** no topo direito (ao lado da imagem de perfil), clique nas **teclas públicas SSH**
1. Selecione  **+ Nova Tecla**
1. Fornecer um nome
1. Cole a chave pública sem quaisquer cotações circundantes
1. Clique **em Adicionar**

## <a name="validate-the-kubernetes-configuration"></a>Validar a configuração de Kubernetes

Depois de `config-agent` instalado o `flux` caso, os recursos retidos no repositório de Git devem começar a fluir para o cluster. Verifique se os espaços de nome, implementações e recursos foram criados:

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

Elimine uma `sourceControlConfiguration` utilização do portal Azure CLI ou Azure.  Depois de iniciar o comando de eliminação, o `sourceControlConfiguration` recurso será eliminado imediatamente em Azure, e a eliminação total dos objetos associados do cluster deverá ocorrer dentro de 10 minutos.  Se o `sourceControlConfiguration` estado está em estado de falha quando é eliminado, a eliminação total dos objetos associados pode demorar até uma hora.

> [!NOTE]
> Depois de ser criada uma fonteControlConfiguration com âmbito de espaço de nome, é possível que os utilizadores com `edit` ligação de funções no espaço de nomes implementem cargas de trabalho neste espaço de nome. Quando isto `sourceControlConfiguration` com o âmbito do espaço de nome é eliminado, o espaço de nome é deixado intacto e não será eliminado para evitar quebrar essas outras cargas de trabalho.  Se necessário, pode eliminar este espaço de nome manualmente com kubectl.
> Quaisquer alterações ao cluster que foram o resultado de implementações do repo Git rastreado não são eliminadas quando a `sourceControlConfiguration` placa é eliminada.

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
