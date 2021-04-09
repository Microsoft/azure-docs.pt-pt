---
title: Criar um controlador de dados utilizando ferramentas Kubernetes
description: Criar um controlador de dados utilizando ferramentas Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 03/02/2021
ms.topic: how-to
ms.openlocfilehash: 500587dc6564aa55eb430365eb67bb958bbd2482
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/30/2021
ms.locfileid: "102519984"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Criar controlador de dados Azure Arc utilizando ferramentas Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Reveja o tópico [Criar o controlador de dados Azure Arc](create-data-controller.md) para obter informações gerais.

Para criar o Controlador de dados do Arco Azure utilizando ferramentas Kubernetes, terá de instalar as ferramentas Kubernetes.  Os exemplos deste artigo serão `kubectl` usados , mas abordagens semelhantes podem ser usadas com outras ferramentas Kubernetes, como o dashboard Kubernetes, `oc` ou se você estiver familiarizado com essas `helm` ferramentas e Kubernetes yaml/json.

[Instale a ferramenta kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Algumas das etapas para criar o controlador de dados Azure Arc que são indicados abaixo requerem permissões de administrador de cluster kubernetes.  Se não for um administrador de cluster Kubernetes, terá de pedir ao administrador de cluster kubernetes que execute estes passos em seu nome.

### <a name="cleanup-from-past-installations"></a>Limpeza de instalações passadas

Se instalou o controlador de dados Azure Arc no passado, no mesmo cluster e eliminou o controlador de dados do Arco Azure utilizando o `azdata arc dc delete` comando, pode haver alguns objetos de nível de cluster que ainda teriam de ser eliminados. Executar os seguintes comandos para eliminar os objetos de nível de cluster do controlador de dados do Azure Arc:

```console
# Cleanup azure arc data service artifacts
kubectl delete crd datacontrollers.arcdata.microsoft.com 
kubectl delete crd sqlmanagedinstances.sql.arcdata.microsoft.com 
kubectl delete crd postgresql-11s.arcdata.microsoft.com 
kubectl delete crd postgresql-12s.arcdata.microsoft.com
```

## <a name="overview"></a>Descrição Geral

A criação do controlador de dados Azure Arc tem os seguintes passos de alto nível:
1. Crie as definições de recursos personalizados para o controlador de dados Arc, a instância gerida Azure SQL e a Hiperescala PostgreSQL. **[Requer permissões de administrador de cluster kubernetes]**
2. Crie um espaço de nome no qual o controlador de dados será criado. **[Requer permissões de administrador de cluster kubernetes]**
3. Crie o serviço bootstrapper incluindo o conjunto de réplicas, conta de serviço, função e encadernação de funções.
4. Crie um segredo para o nome de utilizador e palavra-passe do administrador do controlador de dados.
5. Crie o controlador de dados.
   
## <a name="create-the-custom-resource-definitions"></a>Criar as definições de recursos personalizados

Executar o seguinte comando para criar as definições de recursos personalizados.  **[Requer permissões de administrador de cluster kubernetes]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Criar um espaço de nomes no qual o controlador de dados será criado

Executar um comando semelhante ao seguinte para criar um novo espaço de nome dedicado no qual o controlador de dados será criado.  Neste exemplo e no restante dos exemplos deste artigo, será usado um nome de espaço de `arc` nome. Se optar por usar um nome diferente, use o mesmo nome ao longo.

```console
kubectl create namespace arc
```

Se outras pessoas estiverem a usar este espaço de nome que não são administradores de clusters, recomendamos a criação de um papel de administrador do espaço de nome e a concessão desse papel a esses utilizadores através de uma função vinculativa.  O administrador do espaço de nome deve ter permissões completas no espaço de nomes.  Mais informações serão fornecidas mais tarde sobre como fornecer um acesso mais granular baseado em funções aos utilizadores.

## <a name="create-the-bootstrapper-service"></a>Crie o serviço bootstrapper

O serviço bootstrapper lida com pedidos de entrada para criar, editar e eliminar recursos personalizados, tais como um controlador de dados, exemplo gerido pelo SQL ou o grupo de servidores de hiperescala PostgreSQL.

Executar o seguinte comando para criar um serviço de botas, uma conta de serviço para o serviço de botas, e uma função e encadernação de papel para a conta de serviço bootstrapper.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Verifique se o casulo de botas está em funcionamento utilizando o seguinte comando.  Pode ser necessário executá-lo algumas vezes até que o estado mude para `Running` .

```console
kubectl get pod --namespace arc
```

O ficheiro do modelo bootstrapper.yaml não pode puxar a imagem do recipiente do bootstrapper a partir do Registo do Contentor da Microsoft (MCR).  Se o seu ambiente não tiver acesso direto ao Registo de Contentores da Microsoft, pode fazer o seguinte:
- Siga os passos para [retirar as imagens do contentor do Registo do Contentor da Microsoft e empurre-as para um registo privado de contentores](offline-deployment.md).
- [Crie um segredo de imagem](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) para o seu registo privado de contentores.
- Adicione um segredo de imagem ao recipiente de botas. Veja o exemplo abaixo.
- Altere a localização da imagem para a imagem do bootstrapper. Veja o exemplo abaixo.

O exemplo abaixo pressupõe que criou um nome secreto de puxar imagem `regcred` como indicado na documentação de Kubernetes.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-dec-2020  <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Criar um segredo para o administrador do controlador de dados

O nome de utilizador e palavra-passe do administrador de dados é utilizado para autenticar a API do controlador de dados para executar funções administrativas.  Escolha uma senha segura e partilhe-a apenas com aqueles que precisam de ter privilégios de administrador de cluster.

Um segredo de Kubernetes é armazenado como uma cadeia codificada base64 - uma para o nome de utilizador e outra para a senha.

Pode utilizar uma ferramenta online para codificar o nome de utilizador e palavra-passe pretendidos ou pode utilizar ferramentas CLI construídas dependendo da sua plataforma.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

Uma vez codificado o nome de utilizador e a palavra-passe, pode criar um ficheiro baseado no [ficheiro do modelo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/controller-login-secret.yaml) e substituir o nome de utilizador e os valores da palavra-passe pelos seus próprios valores.

Em seguida, executar o seguinte comando para criar o segredo.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Criar o controlador de dados

Agora está pronto para criar o próprio controlador de dados.

Em primeiro lugar, crie uma cópia do [ficheiro de modelo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/data-controller.yaml) localmente no seu computador para que possa modificar algumas das definições.

Editar o seguinte conforme necessário:

**Necessário**
- **localização**: Altere esta para ser a localização Azure onde os _metadados_ sobre o controlador de dados serão armazenados.  Pode ver a lista de localizações disponíveis do Azure no artigo [de visão geral do controlador de dados](create-data-controller.md) da create.
- **resourceGroup**: o grupo de recursos Azure onde pretende criar o recurso Azure do controlador de dados no Azure Resource Manager.  Normalmente, este grupo de recursos já deveria existir, mas não é necessário até ao momento em que você envia os dados para Azure.
- **subscrição**: a assinatura Azure GUID para a subscrição em que pretende criar os recursos Azure.

**RECOMENDADO PARA REVER E POSSIVELMENTE ALTERAR INCUMPRIMENTOS**
- **armazenamento.. nome classe**: a classe de armazenamento a utilizar para os dados do controlador de dados e ficheiros de registo.  Se não tiver a certeza das aulas de armazenamento disponíveis no seu cluster Kubernetes, pode executar o seguinte comando: `kubectl get storageclass` .  O padrão é `default` que pressupõe que existe uma classe de armazenamento que existe e é nomeado `default` não que haja uma classe de armazenamento que _seja_ o padrão.  Nota: Existem duas definições de nome de classe a definir para a classe de armazenamento desejada - uma para dados e outra para registos.
- **serviçoType**: Altere o tipo de serviço para `NodePort` se não estiver a utilizar um LoadBalancer.  Nota: Existem duas definições de tipo de serviço que precisam de ser alteradas.
- Na plataforma de contentores Azure Red Hat OpenShift ou Red Hat OpenShift, deve aplicar a restrição de contexto de segurança antes de criar o controlador de dados. Siga as instruções na [Aplicação de uma restrição de contexto de segurança para serviços de dados ativados pelo Azure Arc no OpenShift](how-to-apply-security-context-constraint.md).
- **Segurança** Para a plataforma de contentores OpenShift ou Red Hat OpenShift do chapéu vermelho, substitua as `security:` definições pelos seguintes valores no ficheiro yaml do controlador de dados. 

```yml
  security:
    allowDumps: true
    allowNodeMetricsCollection: false
    allowPodMetricsCollection: false
    allowRunAsRoot: false
```

**OPCIONAL**
- **nome**: O nome predefinido do controlador de dados é `arc` , mas pode alterá-lo se quiser.
- **nome do display**: Desa esta medida para o mesmo valor que o atributo de nome na parte superior do ficheiro.
- **registo**: O registo do contentor da Microsoft é o predefinido.  Se estiver a retirar as imagens do Registo do Contentor da Microsoft e [a empurrá-las para um registo privado de contentores,](offline-deployment.md)insira aqui o endereço IP ou o nome DNS do seu registo.
- **estivadorRegistry**: A imagem puxa segredo para usar para retirar as imagens de um registo de contentores privados, se necessário.
- **repositório**: O repositório predefinido no registo do contentor da Microsoft é `arcdata` .  Se estiver a utilizar um registo de contentores privados, insira o caminho que contém as imagens do contentor do arco Azure.
- **imageTag**: a marca de versão mais recente está incumprida no modelo, mas pode alterá-la se quiser utilizar uma versão mais antiga.

O exemplo a seguir mostra um ficheiro yaml de controlador de dados completo. Atualize o exemplo para o seu ambiente, com base nos seus requisitos e nas informações acima.

```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: arc-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-dec-2020 
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Guarde o ficheiro editado no seu computador local e execute o seguinte comando para criar o controlador de dados:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Monitorização do estado de criação

A criação do controlador levará alguns minutos a ser concluída. Pode monitorizar o progresso noutra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo abaixo assumem que criou um controlador de dados e espaço de nome Kubernetes com o nome `arc` .  Se usou um nome diferente de espaço de nome/controlador de dados, pode substituir `arc` pelo seu nome.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Também pode verificar o estado de criação de qualquer cápsula em particular executando um comando como abaixo.  Isto é especialmente útil para resolver problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

A extensão Azure Arc para Azure Data Studio fornece um caderno para o acompanhar através da experiência de como configurar o Azure Arc permitiu que kubernetes e configurar para monitorizar um repositório de git que contém uma amostra sql managed instance yaml file. Quando tudo estiver ligado, um novo SQL Managed Instance será implantado no seu cluster Kubernetes.

Consulte a **Implementação de uma Instância Gerida SQL utilizando o Azure Arc ativado kubernetes e** o caderno Flux na extensão Azure Arc para O Azure Data Studio.

## <a name="troubleshooting-creation-problems"></a>Problemas de criação de resolução de problemas

Se encontrar algum problema com a criação, consulte o [guia de resolução de problemas.](troubleshoot-guide.md)

## <a name="next-steps"></a>Passos seguintes

- [Crie um exemplo gerido pelo SQL utilizando ferramentas nativas de Kubernetes](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Criar um grupo de servidores de hiperescala PostgreSQL usando ferramentas nativas de Kubernetes](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
