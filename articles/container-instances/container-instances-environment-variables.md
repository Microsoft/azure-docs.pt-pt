---
title: Definir variáveis ambientais em instância de contentor
description: Saiba como definir variáveis ambientais nos recipientes que corre em Instâncias de Contentores Azure
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: 92ae59f69b7cb43fee1d3ce8190a85fc20a11f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86169770"
---
# <a name="set-environment-variables-in-container-instances"></a>Definir variáveis ambientais em instâncias de contentores

Definir variáveis de ambiente nas suas instâncias de contentor permite-lhe oferecer configuração dinâmica da aplicação ou do script executado pelo contentor. Isto é semelhante ao `--env` argumento da linha de comando para `docker run` . 

Para definir variáveis ambientais num recipiente, especifique-as quando criar uma instância de recipiente. Este artigo mostra exemplos de definição de variáveis ambientais quando inicia um contentor com o [Azure CLI,](#azure-cli-example) [Azure PowerShell](#azure-powershell-example)e o [portal Azure](#azure-portal-example). 

Por exemplo, se executar a imagem do recipiente do [aci-wordcount][aci-wordcount] da Microsoft, pode modificar o seu comportamento especificando as seguintes variáveis ambientais:

*NumWords*: O número de palavras enviadas para STDOUT.

*MinLength*: O número mínimo de caracteres numa palavra para que seja contado. Um número mais alto ignora palavras comuns como "de" e "o".

Se precisar de passar segredos como variáveis ambientais, a Azure Container Instances suporta [valores seguros](#secure-values) tanto para os recipientes Windows como para o Linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Exemplo de Azure CLI

Para ver a saída padrão do recipiente [aci-wordcount, execute-o][aci-wordcount] primeiro com este [contentor az criar][az-container-create] comando (sem variáveis ambientais especificadas):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Para modificar a saída, inicie um segundo recipiente com o `--environment-variables` argumento adicionado, especificando valores para as variáveis *NumWords* e *MinLength.* (Este exemplo pressupõe que você está executando o CLI em uma concha bash ou Azure Cloud Shell. Se utilizar o Pedido de Comando do Windows, especifique as variáveis com cotações duplas, tais como `--environment-variables "NumWords"="5" "MinLength"="8"` .)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Uma vez que o estado de ambos os recipientes apareça como *Terminado* (use [az container show][az-container-show] para verificar o estado), exiba seus troncos com [registos de contentores az][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A saída dos recipientes mostra como modificou o comportamento do segundo recipiente definindo variáveis ambientais.

**mycontainer1**
```output
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
```

**mycontainer2**
```output
[('CLAUDIUS', 120),
 ('POLONIUS', 113),
 ('GERTRUDE', 82),
 ('ROSENCRANTZ', 69),
 ('GUILDENSTERN', 54)]
```

## <a name="azure-powershell-example"></a>Exemplo Azure PowerShell

Definir variáveis ambientais em PowerShell é semelhante ao CLI, mas usa o `-EnvironmentVariable` argumento da linha de comando.

Primeiro, lance o recipiente [aci-wordcount][aci-wordcount] na sua configuração padrão com este comando [New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Agora executar o seguinte comando [New-AzContainerGroup.][new-Azcontainergroup] Este especifica as *variáveis numwords* e *ambiente MinLength* após a povoação de uma variável de matriz, `envVars` :

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Uma vez terminado o estado de ambos os contentores *(utilize* [o Get-AzContainerInstanceLog][azure-instance-log] para verificar o estado), puxe os seus registos com o comando [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

A saída de cada recipiente mostra como modificou o script executado pelo recipiente definindo variáveis ambientais.

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

## <a name="azure-portal-example"></a>Exemplo do portal Azure

Para definir variáveis ambientais quando iniciar um recipiente no portal Azure, especifique-as na página **Advanced** quando criar o recipiente.

1. Na página **Avançada,** desaceia a **política de Reinício** para *o insucesso*
2. Em **Ambiente variáveis,** insira `NumWords` com um valor de primeira `5` variável, e introduza `MinLength` com um valor de segunda `8` variável. 
1. Selecione **Rever + criar** para verificar e, em seguida, implantar o recipiente.

![Página do portal mostrando variável ambiente Ative botão e caixas de texto][portal-env-vars-01]

Para visualizar os registos do contentor, em **Definições** selecione **Recipientes,** em seguida, **Regista .** Semelhante à saída mostrada nas secções anteriores do CLI e da PowerShell, pode ver como o comportamento do script foi modificado pelas variáveis ambientais. Apenas são exibidas cinco palavras, cada uma com um comprimento mínimo de oito caracteres.

![Portal mostrando saída de registo de contentores][portal-env-vars-02]

## <a name="secure-values"></a>Valores seguros

Os objetos com valores seguros destinam-se a conter informações sensíveis, como palavras-passe ou chaves para a sua aplicação. A utilização de valores seguros para variáveis ambientais é mais segura e flexível do que incluí-la à imagem do seu recipiente. Outra opção é utilizar volumes secretos, descritos no [Monte um volume secreto em Instâncias de Contentores Azure](container-instances-volume-secret.md).

Variáveis ambientais com valores seguros não são visíveis nas propriedades do seu contentor -- os seus valores só podem ser acedidos a partir de dentro do recipiente. Por exemplo, as propriedades do contentor visualizadas no portal Azure ou no CLI Azure exibem apenas o nome de uma variável segura, e não o seu valor.

Desaprovar uma variável de ambiente segura especificando a `secureValue` propriedade em vez do regular para o tipo da `value` variável. As duas variáveis definidas no YAML seguinte demonstram os dois tipos variáveis.

### <a name="yaml-deployment"></a>Implantação YAML

Crie um `secure-env.yaml` ficheiro com o seguinte corte.

```yaml
apiVersion: 2019-12-01
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

Executar o seguinte comando para implantar o grupo de contentores com YAML (ajustar o nome do grupo de recursos, se necessário):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verificar variáveis ambientais

Executar o comando [do contentor az][az-container-show] para consultar as variáveis ambientais do seu contentor:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A resposta JSON mostra a chave e o valor da variável do ambiente inseguro, mas apenas o nome da variável ambiente seguro:

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

Com o comando executivo do [contentor az,][az-container-exec] que permite executar um comando num recipiente em funcionamento, pode verificar se a variável ambiente segura foi definida. Executar o seguinte comando para iniciar uma sessão de festa interativa no recipiente:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Uma vez aberta uma concha interativa dentro do recipiente, pode aceder ao `SECRET` valor da variável:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Passos seguintes

Cenários baseados em tarefas, como o processamento de um grande conjunto de dados com vários contentores, podem beneficiar de variáveis de ambiente personalizados em tempo de execução. Para obter mais informações sobre a execução de recipientes baseados em tarefas, consulte [executar tarefas contentorizadas com políticas de reinício](container-instances-restart-policy.md).

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
