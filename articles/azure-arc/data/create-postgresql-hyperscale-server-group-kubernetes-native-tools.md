---
title: Criar um grupo de servidores de hiperescala PostgreSQL usando ferramentas Kubernetes
description: Criar um grupo de servidores de hiperescala PostgreSQL usando ferramentas Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: fd1b74d33793c06e586a92cc8b2e8d2d36f4827a
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102519967"
---
# <a name="create-a-postgresql-hyperscale-server-group-using-kubernetes-tools"></a>Criar um grupo de servidores de hiperescala PostgreSQL usando ferramentas Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Já deveria ter criado um controlador de [dados Azure Arc.](./create-data-controller.md)

Para criar um grupo de servidores de hiperescala PostgreSQL utilizando ferramentas Kubernetes, terá de instalar as ferramentas Kubernetes.  Os exemplos deste artigo serão `kubectl` usados , mas abordagens semelhantes podem ser usadas com outras ferramentas Kubernetes, como o dashboard Kubernetes, `oc` ou se você estiver familiarizado com essas `helm` ferramentas e Kubernetes yaml/json.

[Instale a ferramenta kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Descrição geral

Para criar um grupo de servidores de hiperescala PostgreSQL, é necessário criar um segredo de Kubernetes para armazenar o seu login e palavra-passe do administrador de postgresql e uma fonte personalizada do grupo de servidores PostgreSQL Hyperscale com base nas definições de recursos personalizados postgresql-12 ou postgresql-11.

## <a name="create-a-yaml-file"></a>Criar um ficheiro yaml

Pode utilizar o ficheiro [yaml do modelo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/postgresql.yaml) como ponto de partida para criar o seu próprio ficheiro yaml do grupo de servidores pós-escala pós-SQL.  Faça o download deste ficheiro para o seu computador local e abra-o num editor de texto.  É útil usar um editor de texto como [o VS Code](https://code.visualstudio.com/download) que suporta o realce de sintaxe e o lintamento para ficheiros yaml.

Este é um ficheiro yaml exemplo:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
kind: Secret
metadata:
  name: pg1-login-secret
type: Opaque
---
apiVersion: arcdata.microsoft.com/v1alpha1
kind: postgresql-12
metadata:
  generation: 1
  name: pg1
spec:
  engine:
    extensions:
    - name: citus
  scale:
    shards: 3
  scheduling:
    default:
      resources:
        limits:
          cpu: "4"
          memory: 4Gi
        requests:
          cpu: "1"
          memory: 2Gi
  service:
    type: LoadBalancer
  storage:
    backups:
      className: default
      size: 5Gi
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Personalizar o login e a palavra-passe.
Um segredo de Kubernetes é armazenado como uma cadeia codificada base64 - uma para o nome de utilizador e outra para a senha.  Terá de codificar um login e palavra-passe de administrador e colocá-los no local reservado `data.password` e `data.username` .  Não inclua os `<` `>` símbolos e símbolos fornecidos no modelo.

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

### <a name="customizing-the-name"></a>Personalizar o nome

O modelo tem um valor de 'pg1' para o atributo de nome.  Podes mudar isto, mas devem ser os caracteres que seguem os padrões de nomeação dns.  Também deve mudar o nome do segredo para combinar.  Por exemplo, se alterar o nome do grupo de servidores PostgreSQL Hyperscale para 'pg2', tem de alterar o nome do segredo de 'pg1-login-secret' para 'pg2-login-secret'

### <a name="customizing-the-engine-version"></a>Personalizar a versão do motor

Pode alterar a versão do motor para ser pós-11 ou pós-12 editando o `kind` atributo.

### <a name="customizing-the-resource-requirements"></a>Personalização dos requisitos de recursos

Pode alterar os requisitos de recursos - os limites e pedidos fundamentais da RAM - conforme necessário.  

> [!NOTE]
> Pode saber mais sobre a [governação dos recursos de Kubernetes.](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)

Requisitos para limites de recursos e pedidos:
- O valor-limite dos núcleos é **necessário** para efeitos de faturação.
- O resto dos pedidos e limites de recursos são opcionais.
- O limite de núcleos e o pedido devem ser um valor inteiro positivo, se especificado.
- O mínimo de 1 núcleo é necessário para o pedido de núcleos, se especificado.
- O formato de valor de memória segue a notação kubernetes.  

### <a name="customizing-service-type"></a>Tipo de serviço de personalização

O tipo de serviço pode ser alterado para NodePort, se desejar.  Um número de porta aleatório será atribuído.

### <a name="customizing-storage"></a>Personalização do armazenamento

Pode personalizar as aulas de armazenamento para o armazenamento que combine com o seu ambiente.  Se não tiver a certeza de que aulas de armazenamento estão disponíveis, pode executar o comando `kubectl get storageclass` para as visualizar.  O modelo tem um valor padrão de 'predefinição'.  Isto significa que existe uma classe de armazenamento _chamada_ "padrão" não que exista uma classe de armazenamento que _é_ o padrão.  Também pode alterar opcionalmente o tamanho do seu armazenamento.  Pode ler mais sobre [a configuração do armazenamento.](./storage-configuration.md)

## <a name="creating-the-postgresql-hyperscale-server-group"></a>Criar o grupo de servidores de hiperescala PostgreSQL

Agora que personalizou o ficheiro yaml do grupo de servidores de hiperescala PostgreSQL, pode criar o grupo de servidores de hiperescala PostgreSQL executando o seguinte comando:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\postgres.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitorização do estado de criação

A criação do grupo de servidores de hiperescala PostgreSQL levará alguns minutos a ser concluída. Pode monitorizar o progresso noutra janela do terminal com os seguintes comandos:

> [!NOTE]
>  O exemplo comanda abaixo assumir que criou um grupo de servidores de hiperescala PostgreSQL chamado 'pg1' e kubernetes com o nome 'arc'.  Se utilizar um nome diferente de nomespace/nome do grupo de servidores de hiperescala PostgreSQL, pode substituir 'arc' e 'pg1' com os seus nomes.

```console
kubectl get postgresql-12/pg1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Também pode verificar o estado de criação de qualquer cápsula em particular executando um comando como abaixo.  Isto é especialmente útil para resolver problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/pg1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemas de criação de resolução de problemas

Se encontrar algum problema com a criação, consulte o [guia de resolução de problemas.](troubleshoot-guide.md)