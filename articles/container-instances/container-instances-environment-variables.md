---
title: Definir variáveis de ambiente em instâncias de contêiner do Azure
description: Saiba como definir variáveis de ambiente nos contêineres que você executa em instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 04/17/2019
ms.author: danlep
ms.openlocfilehash: 9cd62c378270da31079a38f89b040985105a4218
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68326041"
---
# <a name="set-environment-variables-in-container-instances"></a>Definir variáveis de ambiente em instâncias de contêiner

Definir variáveis de ambiente em suas instâncias de contêiner permite que você forneça a configuração dinâmica do aplicativo ou script executado pelo contêiner. Isso é semelhante ao `--env` argumento de linha de comando para. `docker run` 

Para definir variáveis de ambiente em um contêiner, especifique-as ao criar uma instância de contêiner. Este artigo mostra exemplos de como definir variáveis de ambiente ao iniciar um contêiner com o [CLI do Azure](#azure-cli-example), [Azure PowerShell](#azure-powershell-example)e o [portal do Azure](#azure-portal-example). 

Por exemplo, se você executar a imagem de contêiner do Microsoft [ACI-WordCount][aci-wordcount] , poderá modificar seu comportamento especificando as seguintes variáveis de ambiente:

*NumWords*: O número de palavras enviadas para STDOUT.

*MinLength*: O número mínimo de caracteres em uma palavra para ser contado. Um número mais alto ignora palavras comuns como "of" e "The".

Se você precisar passar segredos como variáveis de ambiente, as instâncias de contêiner do Azure oferecerão suporte a [valores seguros](#secure-values) para contêineres do Windows e do Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Exemplo de CLI do Azure

Para ver a saída padrão do contêiner [ACI-WordCount][aci-wordcount] , execute-o primeiro com este comando [AZ container Create][az-container-create] (nenhuma variável de ambiente especificada):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Para modificar a saída, inicie um segundo contêiner com o `--environment-variables` argumento adicionado, especificando valores para as variáveis *NumWords* e *minLength* . (Este exemplo pressupõe que você está executando a CLI em um shell bash ou Azure Cloud Shell. Se você usar o prompt de comando do Windows, especifique as variáveis com aspas duplas, `--environment-variables "NumWords"="5" "MinLength"="8"`como.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Quando o estado dos dois contêineres aparecer como encerrado (use [AZ container show][az-container-show] para verificar o estado), exiba seus logs com [AZ container logs][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A saída dos contêineres mostra como você modificou o comportamento de script do segundo contêiner Definindo variáveis de ambiente.

```console
azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

azureuser@Azure:~$ az container logs --resource-group myResourceGroup --name mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Exemplo de Azure PowerShell

A definição de variáveis de ambiente no PowerShell é semelhante à CLI, mas `-EnvironmentVariable` usa o argumento de linha de comando.

Primeiro, inicie o contêiner [ACI-WordCount][aci-wordcount] em sua configuração padrão com esse comando [New-AzContainerGroup][new-Azcontainergroup] :

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Agora, execute o seguinte comando [New-AzContainerGroup][new-Azcontainergroup] . Essa especifica as variáveis de ambiente *NumWords* e *minLength* depois de preencher uma variável de `envVars`matriz,:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Depois que o estado dos dois contêineres for encerrado (use [Get-AzContainerInstanceLog][azure-instance-log] para verificar o estado), faça pull de seus logs com o comando [Get-AzContainerInstanceLog][azure-instance-log] .

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

A saída para cada contêiner mostra como você modificou o script executado pelo contêiner Definindo variáveis de ambiente.

```console
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
[('the', 990),
 ('and', 702),
 ('of', 628),
 ('to', 610),
 ('I', 544),
 ('you', 495),
 ('a', 453),
 ('my', 441),
 ('in', 399),
 ('HAMLET', 386)]

Azure:\
PS Azure:\> Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]

Azure:\
```

## <a name="azure-portal-example"></a>Exemplo de portal do Azure

Para definir variáveis de ambiente ao iniciar um contêiner no portal do Azure, especifique-os na página **avançado** ao criar o contêiner.

1. Na página **avançado** , defina a **política** de reinicialização como *em caso de falha*
2. Em **variáveis de ambiente**, `NumWords` Insira com um valor `5` de para a primeira variável e digite `MinLength` com um valor de `8` para a segunda variável. 
1. Selecione **examinar + criar** para verificar e implantar o contêiner.

![Página do portal mostrando as caixas de texto e botão habilitar variável de ambiente][portal-env-vars-01]

Para exibir os logs do contêiner, em **configurações** ,selecione contêineres e **logs**. Semelhante à saída mostrada nas seções anterior da CLI e do PowerShell, você pode ver como o comportamento do script foi modificado pelas variáveis de ambiente. Somente cinco palavras são exibidas, cada uma com um comprimento mínimo de oito caracteres.

![Portal mostrando a saída do log de contêiner][portal-env-vars-02]

## <a name="secure-values"></a>Valores seguros

Objetos com valores seguros destinam-se a manter informações confidenciais, como senhas ou chaves para seu aplicativo. Usar valores seguros para variáveis de ambiente é mais seguro e mais flexível do que incluí-lo na imagem do contêiner. Outra opção é usar volumes secretos, descritos em [montar um volume secreto em instâncias de contêiner do Azure](container-instances-volume-secret.md).

Variáveis de ambiente com valores seguros não são visíveis nas propriedades do contêiner – seus valores podem ser acessados somente de dentro do contêiner. Por exemplo, as propriedades de contêiner exibidas no portal do Azure ou CLI do Azure exibem apenas o nome de uma variável segura, não seu valor.

Defina uma variável de ambiente segura especificando a `secureValue` Propriedade em vez de regular `value` para o tipo da variável. As duas variáveis definidas no YAML a seguir demonstram os dois tipos de variáveis.

### <a name="yaml-deployment"></a>Implantação do YAML

Crie um `secure-env.yaml` arquivo com o trecho a seguir.

```yaml
apiVersion: 2018-10-01
location: eastus
name: securetest
properties:
  containers:
  - name: mycontainer
    properties:
      environmentVariables:
        - name: 'NOTSECRET'
          value: 'my-exposed-value'
        - name: 'SECRET'
          secureValue: 'my-secret-value'
      image: nginx
      ports: []
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  osType: Linux
  restartPolicy: Always
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

Execute o comando a seguir para implantar o grupo de contêineres com YAML (ajuste o nome do grupo de recursos conforme necessário):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verificar variáveis de ambiente

Execute o comando [AZ container show][az-container-show] para consultar as variáveis de ambiente do contêiner:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A resposta JSON mostra a chave e o valor da variável de ambiente inseguro, mas apenas o nome da variável de ambiente segura:

```json
[
  [
    {
      "name": "NOTSECRET",
      "secureValue": null,
      "value": "my-exposed-value"
    },
    {
      "name": "SECRET",
      "secureValue": null,
      "value": null
    }
  ]
]
```

Com o comando [AZ container exec][az-container-exec] , que permite executar um comando em um contêiner em execução, você pode verificar se a variável de ambiente segura foi definida. Execute o seguinte comando para iniciar uma sessão de bash interativa no contêiner:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Depois de abrir um shell interativo dentro do contêiner, você pode acessar o `SECRET` valor da variável:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como processamento em lote de um grande conjunto de grandes com vários contêineres, podem se beneficiar de variáveis de ambiente personalizadas em tempo de execução. Para obter mais informações sobre a execução de contêineres baseados em tarefas, consulte [executar tarefas em contêineres com políticas](container-instances-restart-policy.md)de reinicialização.

<!-- IMAGES -->
[portal-env-vars-01]: ./media/container-instances-environment-variables/portal-env-vars-01.png
[portal-env-vars-02]: ./media/container-instances-environment-variables/portal-env-vars-02.png

<!-- LINKS - External -->
[aci-wordcount]: https://hub.docker.com/_/microsoft-azuredocs-aci-wordcount

<!-- LINKS Internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-exec]: /cli/azure/container#az-container-exec
[az-container-logs]: /cli/azure/container#az-container-logs
[az-container-show]: /cli/azure/container#az-container-show
[azure-cli-install]: /cli/azure/
[azure-instance-log]: /powershell/module/az.containerinstance/get-azcontainerinstancelog
[azure-powershell-install]: /powershell/azure/install-Az-ps
[new-Azcontainergroup]: /powershell/module/az.containerinstance/new-azcontainergroup
[portal]: https://portal.azure.com
