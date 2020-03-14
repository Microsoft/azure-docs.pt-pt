---
title: Definir variáveis ambientais em instância de recipiente
description: Saiba como definir variáveis ambientais nos recipientes que executa em Instâncias de Contentores Azure
ms.topic: article
ms.date: 04/17/2019
ms.openlocfilehash: c3c76ba0c6131a8ab3de68c13c9dfddaf7e8749a
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79247231"
---
# <a name="set-environment-variables-in-container-instances"></a>Definir variáveis ambientais em instâncias de contentores

A definição de variáveis ambientais nos casos do seu recipiente permite-lhe fornecer uma configuração dinâmica da aplicação ou script executada pelo recipiente. Isto é semelhante ao argumento `--env` linha de comando para `docker run`. 

Para definir variáveis ambientais num recipiente, especifique-as quando criar uma instância de contentores. Este artigo mostra exemplos de definição de variáveis ambientais quando se inicia um contentor com o [Azure CLI,](#azure-cli-example) [Azure PowerShell](#azure-powershell-example)e o [portal Azure](#azure-portal-example). 

Por exemplo, se executar a imagem do recipiente [aci-wordcount][aci-wordcount] da Microsoft, pode modificar o seu comportamento especificando as seguintes variáveis ambientais:

*NumWords*: O número de palavras enviadas para O DSTOUT.

*MinLength*: O número mínimo de caracteres numa palavra para ser contado. Um número maior ignora palavras comuns como "de" e "o".

Se precisa de passar segredos como variáveis ambientais, o Azure Container Instances suporta [valores seguros](#secure-values) tanto para os recipientes Windows como para os linux.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="azure-cli-example"></a>Exemplo azure CLI

Para ver a saída padrão do recipiente [aci-wordcount, execute-o][aci-wordcount] primeiro com este [recipiente az criar][az-container-create] comando (sem variáveis ambientais especificadas):

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer1 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure
```

Para modificar a saída, inicie um segundo recipiente com o argumento `--environment-variables` adicionado, especificando valores para as variáveis *NumWords* e *MinLength.* (Este exemplo assume que está a executar o CLI numa concha bash ou azure Cloud Shell. Se utilizar o Pedido de Comando do Windows, especifique as variáveis com orçamentos duplos, como `--environment-variables "NumWords"="5" "MinLength"="8"`.)

```azurecli-interactive
az container create \
    --resource-group myResourceGroup \
    --name mycontainer2 \
    --image mcr.microsoft.com/azuredocs/aci-wordcount:latest \
    --restart-policy OnFailure \
    --environment-variables 'NumWords'='5' 'MinLength'='8'
```

Uma vez que o estado de ambos os recipientes mostre como *Terminado (use* [az container show][az-container-show] to check state), exiba os seus troncos com troncos de recipiente [az][az-container-logs] para ver a saída.

```azurecli-interactive
az container logs --resource-group myResourceGroup --name mycontainer1
az container logs --resource-group myResourceGroup --name mycontainer2
```

A saída dos recipientes mostra como modificou o comportamento do segundo contentor, definindo variáveis ambientais.

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

## <a name="azure-powershell-example"></a>Exemplo azure PowerShell

Definir variáveis ambientais no PowerShell é semelhante ao CLI, mas utiliza o argumento `-EnvironmentVariable` linha de comando.

Em primeiro lugar, lance o recipiente [aci-wordcount][aci-wordcount] na sua configuração padrão com este comando [New-AzContainerGroup:][new-Azcontainergroup]

```azurepowershell-interactive
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer1 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest
```

Agora execute o seguinte comando [do New-AzContainerGroup.][new-Azcontainergroup] Este especifica as variáveis ambientais *NumWords* e *MinLength* após a povoação de uma variável matriz, `envVars`:

```azurepowershell-interactive
$envVars = @{'NumWords'='5';'MinLength'='8'}
New-AzContainerGroup `
    -ResourceGroupName myResourceGroup `
    -Name mycontainer2 `
    -Image mcr.microsoft.com/azuredocs/aci-wordcount:latest `
    -RestartPolicy OnFailure `
    -EnvironmentVariable $envVars
```

Uma vez *terminada* a emissão do estado de ambos os recipientes (utilize [o Get-AzContainerInstanceLog][azure-instance-log] para verificar o estado), puxe os seus registos com o comando [Get-AzContainerInstanceLog.][azure-instance-log]

```azurepowershell-interactive
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer1
Get-AzContainerInstanceLog -ResourceGroupName myResourceGroup -ContainerGroupName mycontainer2
```

A saída para cada recipiente mostra como modificou o script executado pelo recipiente, definindo variáveis ambientais.

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

Para definir variáveis ambientais quando iniciar um recipiente no portal Azure, especifique-as na página **Avançada** quando criar o recipiente.

1. Na página **Avançada,** delineie a **política de Reiniciar** para O *Fracasso*
2. Nas **variáveis Ambiente,** insira `NumWords` com um valor de `5` para a primeira variável, e introduza `MinLength` com um valor de `8` para a segunda variável. 
1. Selecione **Review + crie** para verificar e, em seguida, implante o recipiente.

![Página do portal mostrando variável ambiente Ativar botões e caixas de texto][portal-env-vars-01]

Para ver os registos do recipiente, em **Definições** selecione **Recipientes,** em seguida, **regista**. Semelhante à saída mostrada nas secções cli e PowerShell anteriores, pode ver como o comportamento do script foi modificado pelas variáveis ambientais. Apenas são apresentadas cinco palavras, cada uma com um comprimento mínimo de oito caracteres.

![Portal mostrando saída de registo de contentores][portal-env-vars-02]

## <a name="secure-values"></a>Valores seguros

Os objetos com valores seguros destinam-se a guardar informações sensíveis, como senhas ou chaves para a sua aplicação. A utilização de valores seguros para variáveis ambientais é simultaneamente mais segura e flexível do que incluê-la à imagem do seu recipiente. Outra opção é utilizar volumes secretos, descritos no [Monte um volume secreto em Instâncias de Contentores Azure.](container-instances-volume-secret.md)

Variáveis ambientais com valores seguros não são visíveis nas propriedades do seu recipiente - os seus valores só podem ser acedidos a partir do interior do recipiente. Por exemplo, as propriedades do contentor vistas no portal Azure ou no Azure CLI exibem apenas o nome de uma variável segura, e não o seu valor.

Detete uma variável ambiente segura especificando a propriedade `secureValue` em vez da `value` regular para o tipo da variável. As duas variáveis definidas no Seguinte YAML demonstram os dois tipos variáveis.

### <a name="yaml-deployment"></a>Implantação yAML

Crie um ficheiro `secure-env.yaml` com o seguinte corte.

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

Executar o seguinte comando para implantar o grupo de contentores com o YAML (ajuste o nome do grupo de recursos conforme necessário):

```azurecli-interactive
az container create --resource-group myResourceGroup --file secure-env.yaml
```

### <a name="verify-environment-variables"></a>Verificar variáveis ambientais

Executar o [recipiente az mostrar][az-container-show] comando para consultar as variáveis ambientais do seu recipiente:

```azurecli-interactive
az container show --resource-group myResourceGroup --name securetest --query 'containers[].environmentVariables'
```

A resposta json mostra tanto a chave e o valor da variável ambiental insegura, mas apenas o nome da variável ambiente segura:

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

Com o comando executivo do [contentor az,][az-container-exec] que permite executar um comando num recipiente de corrida, pode verificar se a variável ambiente segura foi definida. Executar o seguinte comando para iniciar uma sessão de bash interativa no recipiente:

```azurecli-interactive
az container exec --resource-group myResourceGroup --name securetest --exec-command "/bin/bash"
```

Uma vez aberta uma concha interativa dentro do recipiente, pode aceder ao valor da variável `SECRET`:

```console
root@caas-ef3ee231482549629ac8a40c0d3807fd-3881559887-5374l:/# echo $SECRET
my-secret-value
```

## <a name="next-steps"></a>Passos Seguintes

Cenários baseados em tarefas, como o processamento de lotes um grande conjunto de dados com vários contentores, podem beneficiar de variáveis ambientais personalizadas no tempo de execução. Para obter mais informações sobre a execução de contentores baseados em [tarefas, consulte executar tarefas contentorizadas com políticas](container-instances-restart-policy.md)de reinício .

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
