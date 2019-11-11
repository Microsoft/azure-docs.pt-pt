---
title: Solucionando problemas de instâncias de contêiner do Azure
description: Saiba como solucionar problemas com as instâncias de contêiner do Azure
services: container-instances
author: dlepow
manager: gwallace
ms.service: container-instances
ms.topic: article
ms.date: 09/25/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: 14745f79955a98727d6f55da4189212f2f18d9c0
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904402"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Solucionar problemas comuns em instâncias de contêiner do Azure

Este artigo mostra como solucionar problemas comuns de gerenciamento ou implantação de contêineres em instâncias de contêiner do Azure. Consulte também [perguntas frequentes](container-instances-faq.md).

Se precisar de suporte adicional, consulte Opções de **ajuda + suporte** disponíveis no [portal do Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problemas durante a implantação do grupo de contêineres
### <a name="naming-conventions"></a>Convenções de nomenclatura

Ao definir a especificação do contêiner, determinados parâmetros exigem a adesão às restrições de nomenclatura. Abaixo está uma tabela com requisitos específicos para propriedades do grupo de contêineres. Para obter mais informações sobre as convenções de nomenclatura do Azure, consulte [convenções de nomenclatura][azure-name-restrictions] no centro de arquitetura do Azure.

| Âmbito | Comprimento | Maiúsculas e Minúsculas | Carateres válidos | Padrão sugerido | Exemplo |
| --- | --- | --- | --- | --- | --- |
| Nome do grupo de contêineres | 1-64 |Não sensível a maiúsculas e minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nome do contentor | 1-64 |Não sensível a maiúsculas e minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Portas de contêiner | Entre 1 e 65535 |Número inteiro |Inteiro entre 1 e 65535 |`<port-number>` |`443` |
| Rótulo de nome DNS | 5-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caractere |`<name>` |`frontend-site1` |
| Variável de ambiente | 1-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico e sublinhado (_) em qualquer lugar, exceto o primeiro ou o último caractere |`<name>` |`MY_VARIABLE` |
| Nome do volume | 5-63 |Não sensível a maiúsculas e minúsculas |Letras minúsculas e números e hifens em qualquer lugar, exceto o primeiro ou o último caractere. Não pode conter dois hifens consecutivos. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>Não há suporte para a versão do so da imagem

Se você especificar uma imagem para a qual as instâncias de contêiner do Azure não dão suporte, um erro de `OsVersionNotSupported` será retornado. O erro é semelhante ao seguinte, em que `{0}` é o nome da imagem que você tentou implantar:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Esse erro é encontrado com mais frequência ao implantar imagens do Windows baseadas na versão do canal semestral 1709 ou 1803, que não tem suporte. Para imagens do Windows com suporte em instâncias de contêiner do Azure, consulte [perguntas](container-instances-faq.md#what-windows-base-os-images-are-supported)frequentes.

### <a name="unable-to-pull-image"></a>Não é possível efetuar pull da imagem

Se as instâncias de contêiner do Azure não puderem efetuar pull de sua imagem inicialmente, ela tentará novamente por um período de tempo. Se a operação de pull da imagem continuar falhando, o ACI eventualmente falhará na implantação e você poderá ver um erro de `Failed to pull image`.

Para resolver esse problema, exclua a instância de contêiner e tente a implantação novamente. Verifique se a imagem existe no registro e se você digitou o nome da imagem corretamente.

Se a imagem não puder ser retirada, eventos como os mostrados a seguir serão exibidos na saída de [AZ container show][az-container-show]:

```bash
"events": [
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "Pulling",
    "type": "Normal"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:19+00:00",
    "lastTimestamp": "2017-12-21T22:57:00+00:00",
    "message": "Failed to pull image \"mcr.microsoft.com/azuredocs/aci-hellowrld\": rpc error: code 2 desc Error: image t/aci-hellowrld:latest not found",
    "name": "Failed",
    "type": "Warning"
  },
  {
    "count": 3,
    "firstTimestamp": "2017-12-21T22:56:20+00:00",
    "lastTimestamp": "2017-12-21T22:57:16+00:00",
    "message": "Back-off pulling image \"mcr.microsoft.com/azuredocs/aci-hellowrld\"",
    "name": "BackOff",
    "type": "Normal"
  }
],
```
### <a name="resource-not-available-error"></a>Erro de recurso não disponível

Devido a uma variável de carga de recursos regionais no Azure, você pode receber o seguinte erro ao tentar implantar uma instância de contêiner:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Esse erro indica que, devido à carga pesada na região em que você está tentando implantar, os recursos especificados para o contêiner não podem ser alocados nesse momento. Use uma ou mais das etapas de mitigação a seguir para ajudar a resolver o problema.

* Verifique se as configurações de implantação de contêiner estão dentro dos parâmetros definidos em [disponibilidade de região para instâncias de contêiner do Azure](container-instances-region-availability.md)
* Especificar configurações menores de CPU e memória para o contêiner
* Implantar em uma região diferente do Azure
* Implantar em um momento posterior

## <a name="issues-during-container-group-runtime"></a>Problemas durante o tempo de execução do grupo de contêineres
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>O contêiner sai e reinicia continuamente (sem processo de execução demorada)

Os grupos de contêineres assumem como padrão uma [política de reinicialização](container-instances-restart-policy.md) **sempre**, portanto, os contêineres no grupo de contêineres sempre são reiniciados depois de executados até Talvez seja necessário alterar isso para **OnFailure** ou **nunca** se você pretender executar contêineres baseados em tarefas. Se você especificar **OnFailure** e ainda vir reinicializações contínuas, poderá haver um problema com o aplicativo ou script executado em seu contêiner.

Ao executar grupos de contêineres sem processos de execução longa, você poderá ver saídas repetidas e reinicializações com imagens como Ubuntu ou Alpine. A conexão via [exec](container-instances-exec.md) não funcionará, pois o contêiner não tem nenhum processo mantendo-o ativo. Para resolver esse problema, inclua um comando Iniciar, como o seguinte, com a implantação do grupo de contêineres para manter o contêiner em execução.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

A API de instâncias de contêiner e portal do Azure inclui uma propriedade `restartCount`. Para verificar o número de reinicializações de um contêiner, você pode usar o comando [AZ container show][az-container-show] no CLI do Azure. Na saída de exemplo a seguir (que foi truncada para fins de brevidade), você pode ver a propriedade `restartCount` no final da saída.

```json
...
 "events": [
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:06+00:00",
     "lastTimestamp": "2017-11-13T21:20:06+00:00",
     "message": "Pulling: pulling image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Pulled: Successfully pulled image \"myregistry.azurecr.io/aci-tutorial-app:v1\"",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Created: Created container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   },
   {
     "count": 1,
     "firstTimestamp": "2017-11-13T21:20:14+00:00",
     "lastTimestamp": "2017-11-13T21:20:14+00:00",
     "message": "Started: Started container with id bf25a6ac73a925687cafcec792c9e3723b0776f683d8d1402b20cc9fb5f66a10",
     "type": "Normal"
   }
 ],
 "previousState": null,
 "restartCount": 0
...
}
```

> [!NOTE]
> A maioria das imagens de contêiner para distribuições do Linux define um shell, como bash, como o comando padrão. Como um shell por si só não é um serviço de execução longa, esses contêineres são imediatamente encerrados e se encaixam em um loop de reinicialização quando configurados com a política padrão **Always** restart.

### <a name="container-takes-a-long-time-to-start"></a>O contêiner leva muito tempo para iniciar

Os três fatores principais que contribuem para o tempo de inicialização do contêiner em instâncias de contêiner do Azure são:

* [Tamanho da imagem](#image-size)
* [Local da imagem](#image-location)
* [Imagens armazenadas em cache](#cached-images)

As imagens do Windows têm [considerações adicionais](#cached-images).

#### <a name="image-size"></a>Tamanho da imagem

Se o contêiner levar muito tempo para iniciar, mas acabar com sucesso, comece examinando o tamanho da imagem de contêiner. Como as instâncias de contêiner do Azure recebem sua imagem de contêiner sob demanda, o tempo de inicialização que você vê está diretamente relacionado ao seu tamanho.

Você pode exibir o tamanho da sua imagem de contêiner usando o comando `docker images` na CLI do Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A chave para manter os tamanhos de imagem pequenos é garantir que a imagem final não contenha nada que não seja necessário no tempo de execução. Uma maneira de fazer isso é com [compilações de vários estágios][docker-multi-stage-builds]. Compilações de vários estágios facilitam a garantia de que a imagem final contenha apenas os artefatos de que você precisa para seu aplicativo, e não qualquer conteúdo extra necessário no momento da compilação.

#### <a name="image-location"></a>Local da imagem

Outra maneira de reduzir o impacto do pull da imagem no tempo de inicialização do contêiner é hospedar a imagem de contêiner no [registro de contêiner do Azure](/azure/container-registry/) na mesma região em que você pretende implantar as instâncias de contêiner. Isso reduz o caminho de rede que a imagem de contêiner precisa para viajar, reduzindo significativamente o tempo de download.

#### <a name="cached-images"></a>Imagens armazenadas em cache

As instâncias de contêiner do Azure usam um mecanismo de cache para ajudar a acelerar o tempo de inicialização do contêiner para imagens criadas em [imagens básicas](container-instances-faq.md#what-windows-base-os-images-are-supported)comuns do Windows, incluindo `nanoserver:1809`, `servercore:ltsc2019`e `servercore:1809`. As imagens do Linux geralmente usadas, como `ubuntu:1604` e `alpine:3.6`, também são armazenadas em cache. Para obter uma lista atualizada de imagens e marcas armazenadas em cache, use a API da [lista de imagens em cache][list-cached-images] .

> [!NOTE]
> O uso de imagens baseadas no Windows Server 2019 em instâncias de contêiner do Azure está em versão prévia.

#### <a name="windows-containers-slow-network-readiness"></a>Preparação de rede lenta de contêineres do Windows

Na criação inicial, os contêineres do Windows podem não ter nenhuma conectividade de entrada ou de saída por até 30 segundos (ou mais, em casos raros). Se o aplicativo de contêiner precisar de uma conexão com a Internet, adicione a lógica de atraso e repetição para permitir que 30 segundos estabeleçam conectividade com a Internet. Após a configuração inicial, a rede de contêiner deve retomar adequadamente.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Não é possível conectar-se à API do Docker subjacente ou executar contêineres privilegiados

As instâncias de contêiner do Azure não expõem o acesso direto à infraestrutura subjacente que hospeda grupos de contêineres. Isso inclui o acesso à API do Docker em execução no host do contêiner e na execução de contêineres privilegiados. Se você precisar de interação com o Docker, consulte a [documentação de referência REST](https://aka.ms/aci/rest) para ver a que a API ACI dá suporte. Se houver algo ausente, envie uma solicitação nos fóruns de [comentários do ACI](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>O endereço IP do grupo de contêineres pode não estar acessível devido a portas incompatíveis

As instâncias de contêiner do Azure ainda não dão suporte ao mapeamento de porta como com a configuração regular do Docker. Se você achar que o endereço IP de um grupo de contêineres não está acessível quando acreditar que deveria ser, certifique-se de ter configurado sua imagem de contêiner para escutar as mesmas portas que você expõe em seu grupo de contêineres com a propriedade `ports`.

Se você quiser confirmar que as instâncias de contêiner do Azure podem escutar na porta configurada na sua imagem de contêiner, teste uma implantação da imagem de `aci-helloworld` que expõe a porta. Execute também o aplicativo `aci-helloworld` para que ele escute na porta. `aci-helloworld` aceita uma variável de ambiente opcional `PORT` para substituir a porta padrão 80 escutada. Por exemplo, para testar a porta 9000, defina a [variável de ambiente](container-instances-environment-variables.md) ao criar o grupo de contêineres:

1. Configure o grupo de contêineres para expor a porta 9000 e passe o número da porta como o valor da variável de ambiente. O exemplo é formatado para o shell bash. Se preferir outro shell, como o PowerShell ou o prompt de comando, você precisará ajustar a atribuição de variável de forma adequada.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Localize o endereço IP do grupo de contêineres na saída de comando de `az container create`. Procure o valor de **IP**. 
1. Depois que o contêiner for provisionado com êxito, navegue até o endereço IP e a porta do aplicativo de contêiner em seu navegador, por exemplo: `192.0.2.0:9000`. 

    Você deve ver o "bem-vindo às instâncias de contêiner do Azure!" mensagem exibida pelo aplicativo Web.
1. Quando você terminar o contêiner, remova-o usando o comando `az container delete`:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Passos seguintes

Saiba como [recuperar logs de contêiner e eventos](container-instances-get-logs.md) para ajudar a depurar seus contêineres.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/considerations/naming-and-tagging#resource-naming
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
