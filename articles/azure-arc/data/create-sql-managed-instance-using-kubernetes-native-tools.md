---
title: Crie um exemplo gerido pelo SQL utilizando ferramentas Kubernetes
description: Crie um exemplo gerido pelo SQL utilizando ferramentas Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 02/11/2021
ms.topic: how-to
ms.openlocfilehash: d23df80a3f80ed96779297bac12ef0ed8d2927d5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "101687928"
---
# <a name="create-azure-sql-managed-instance-using-kubernetes-tools"></a>Criar exemplo gerido pelo Azure SQL utilizando ferramentas Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Pré-requisitos

Já deveria ter criado um controlador de [dados Azure Arc.](./create-data-controller.md)

Para criar uma instância gerida pela SQL utilizando ferramentas Kubernetes, terá de instalar as ferramentas Kubernetes.  Os exemplos deste artigo serão `kubectl` usados , mas abordagens semelhantes podem ser usadas com outras ferramentas Kubernetes, como o dashboard Kubernetes, `oc` ou se você estiver familiarizado com essas `helm` ferramentas e Kubernetes yaml/json.

[Instale a ferramenta kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

## <a name="overview"></a>Descrição Geral

Para criar um exemplo gerido pela SQL, é necessário criar um segredo kubernetes para armazenar o login e a palavra-passe do administrador do sistema de forma segura e um recurso personalizado de instância gerido pela SQL com base na definição de recursos personalizados sqlmanagedinstance.

## <a name="create-a-yaml-file"></a>Criar um ficheiro yaml

Pode utilizar o ficheiro [yaml do modelo](https://raw.githubusercontent.com/microsoft/azure_arc/main/arc_data_services/deploy/yaml/sqlmi.yaml) como ponto de partida para criar o seu próprio ficheiro yaml de instância gerida por SQL personalizado.  Faça o download deste ficheiro para o seu computador local e abra-o num editor de texto.  É útil usar um editor de texto como [o VS Code](https://code.visualstudio.com/download) que suporta o realce de sintaxe e o lintamento para ficheiros yaml.

Este é um ficheiro yaml exemplo:

```yaml
apiVersion: v1
data:
  password: <your base64 encoded password>
  username: <your base64 encoded user name. 'sa' is not allowed>
kind: Secret
metadata:
  name: sql1-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
  name: sql1
spec:
  limits:
    memory: 4Gi
    vcores: "4"
  requests:
    memory: 2Gi
    vcores: "1"
  service:
    type: LoadBalancer
  storage:
    data:
      className: default
      size: 5Gi
    logs:
      className: default
      size: 1Gi
```

### <a name="customizing-the-login-and-password"></a>Personalizar o login e a palavra-passe

Um segredo de Kubernetes é armazenado como uma cadeia codificada base64 - uma para o nome de utilizador e outra para a senha.  Terá de codificar um login e palavra-passe de administrador de sistema e colocá-los no local reservado `data.password` e `data.username` .  Não inclua os `<` `>` símbolos e símbolos fornecidos no modelo.

> [!NOTE]
> Para uma segurança ótima, não é permitido utilizar o valor 'sa' para o início de sessão .
> Siga a política de complexidade da [palavra-passe.](/sql/relational-databases/security/password-policy#password-complexity)

Pode utilizar uma ferramenta online para codificar o nome de utilizador e palavra-passe pretendidos ou pode utilizar ferramentas CLI construídas dependendo da sua plataforma.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes('example'))

```

Linux/macOS

```console
echo -n '<your string to encode here>' | base64

#Example
# echo -n 'example' | base64
```

### <a name="customizing-the-name"></a>Personalizar o nome

O modelo tem um valor de 'sql1' para o atributo de nome.  Podes mudar isto, mas devem ser os caracteres que seguem os padrões de nomeação dns.  Também deve mudar o nome do segredo para combinar.  Por exemplo, se alterar o nome do sql gerido para 'sql2', deve alterar o nome do segredo de 'sql1-login-secret' para 'sql2-login-secret'

### <a name="customizing-the-resource-requirements"></a>Personalização dos requisitos de recursos

Pode alterar os requisitos de recursos - os limites e pedidos fundamentais da RAM - conforme necessário.  

> [!NOTE]
> Pode saber mais sobre a [governação dos recursos de Kubernetes.](https://kubernetes.io/docs/concepts/configuration/manage-resources-containers/#resource-units-in-kubernetes)

Requisitos para limites de recursos e pedidos:
- O valor-limite dos núcleos é **necessário** para efeitos de faturação.
- O resto dos pedidos e limites de recursos são opcionais.
- O limite de núcleos e o pedido devem ser um valor inteiro positivo, se especificado.
- O mínimo de 2 núcleos é necessário para o pedido de núcleos, se especificado.
- O formato de valor de memória segue a notação kubernetes.  
- É necessário um mínimo de 2Gi para pedido de memória, se especificado.
- Como orientação geral, deve ter 4GB de RAM para cada 1 núcleo para casos de utilização de produção.

### <a name="customizing-service-type"></a>Tipo de serviço de personalização

O tipo de serviço pode ser alterado para NodePort, se desejar.  Um número de porta aleatório será atribuído.

### <a name="customizing-storage"></a>Personalização do armazenamento

Pode personalizar as aulas de armazenamento para o armazenamento que combine com o seu ambiente.  Se não tiver a certeza de que aulas de armazenamento estão disponíveis, pode executar o comando `kubectl get storageclass` para as visualizar.  O modelo tem um valor padrão de 'predefinição'.  Isto significa que existe uma classe de armazenamento _chamada_ "padrão" não que exista uma classe de armazenamento que _é_ o padrão.  Também pode alterar opcionalmente o tamanho do seu armazenamento.  Pode ler mais sobre [a configuração do armazenamento.](./storage-configuration.md)

## <a name="creating-the-sql-managed-instance"></a>Criando o exemplo gerido pelo SQL

Agora que personalizou o ficheiro yaml de instância gerida pela SQL, pode criar a instância gerida pelo SQL executando o seguinte comando:

```console
kubectl create -n <your target namespace> -f <path to your yaml file>

#Example
#kubectl create -n arc -f C:\arc-data-services\sqlmi.yaml
```


## <a name="monitoring-the-creation-status"></a>Monitorização do estado de criação

A criação do exemplo gerido pelo SQL levará alguns minutos a ser concluída. Pode monitorizar o progresso noutra janela do terminal com os seguintes comandos:

> [!NOTE]
>  Os comandos de exemplo abaixo assumem que criou um SQL gerido exemplo chamado 'sql1' e Kubernetes namespace com o nome 'arc'.  Se usou um nome diferente de nomespace/SQL, pode substituir 'arc' e 'sqlmi' com os seus nomes.

```console
kubectl get sqlmi/sql1 --namespace arc
```

```console
kubectl get pods --namespace arc
```

Também pode verificar o estado de criação de qualquer cápsula em particular executando um comando como abaixo.  Isto é especialmente útil para resolver problemas.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/sql1-0 --namespace arc
```

## <a name="troubleshooting-creation-problems"></a>Problemas de criação de resolução de problemas

Se encontrar algum problema com a criação, consulte o [guia de resolução de problemas.](troubleshoot-guide.md)

## <a name="next-steps"></a>Passos seguintes

[Ligue ao Arco Azure ativado sql instância gerida](connect-managed-instance.md)
