---
title: Resolução de problemas de instâncias de contentor do Azure
description: Saiba como resolver problemas com o Azure Container Instances
services: container-instances
author: dlepow
manager: jeconnoc
ms.service: container-instances
ms.topic: article
ms.date: 02/15/2019
ms.author: danlep
ms.custom: mvc
ms.openlocfilehash: bf783c988c0163fe562669a8331c332dbf8d535e
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/22/2019
ms.locfileid: "58371881"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Resolver problemas comuns no Azure Container Instances

Este artigo mostra como resolver problemas comuns de gestão ou implementar contentores no Azure Container Instances.

## <a name="naming-conventions"></a>Convenções de nomenclatura

Ao definir sua especificação de contentor, determinados parâmetros requerem o cumprimento das restrições de nomenclatura. Segue-se uma tabela com requisitos específicos para o contentor de propriedades do grupo. Para obter mais informações sobre as convenções de nomenclatura do Azure, consulte [convenções de nomenclatura] [ azure-name-restrictions] no Centro de arquitetura do Azure.

| Âmbito | Comprimento | Maiúsculas e Minúsculas | Carateres válidos | Padrão sugerido | Exemplo |
| --- | --- | --- | --- | --- | --- |
| Nome do grupo de contentor | 1-64 |Não sensível a maiúsculas e minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caráter |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nome do contentor | 1-64 |Não sensível a maiúsculas e minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caráter |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Portas de contentores | Entre 1 e 65535 |Número inteiro |Número inteiro entre 1 e 65535 |`<port-number>` |`443` |
| Etiqueta de nome DNS | 5-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico e hífen em qualquer lugar, exceto o primeiro ou último caráter |`<name>` |`frontend-site1` |
| Variável de ambiente | 1-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico e o caráter de sublinhado (_) em qualquer lugar, exceto o primeiro ou último caráter |`<name>` |`MY_VARIABLE` |
| Nome do volume | 5-63 |Não sensível a maiúsculas e minúsculas |Letras minúsculas e números e hífenes em qualquer lugar, exceto o primeiro ou último caráter. Não pode conter dois hífenes consecutivos. |`<name>` |`batch-output-volume` |

## <a name="os-version-of-image-not-supported"></a>Versão do SO da imagem não suportada

Se especificar uma imagem que não suportam o Azure Container Instances, um `OsVersionNotSupported` erro é retornado. O erro é semelhante ao seguinte, onde `{0}` é o nome da imagem tentou implementar:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Este erro for encontrado com mais freqüência quando implementar imagens do Windows que se baseiam num canal Semianual (SAC). Por exemplo, o Windows versões 1709 e versão 1803 são versões SAC e geram este erro após a implementação.

Atualmente, o Azure Container Instances suporta imagens do Windows com base apenas nos **Windows Server 2016 term Servicing canal (LTSC)** de versão. Para atenuar este problema ao implementar contentores do Windows, implemente sempre as imagens baseadas no Windows Server 2016 LTSC. Imagens com base no 2019 do Windows Server (LTSC) não são suportadas.

Para obter detalhes sobre as versões LTSC e SAC do Windows, consulte [descrição geral do Windows Server via de atualizações Semianuais][windows-sac-overview].

## <a name="unable-to-pull-image"></a>Não é possível a imagem de extração

Se o Azure Container Instances é inicialmente não é possível extrair a imagem, ele tentará novamente durante um período de tempo. Se a operação de solicitação de imagem continua a falhar, ACI, eventualmente, falha a implementação e poderá ver um `Failed to pull image` erro.

Para resolver este problema, elimine a instância de contentor e repita a implementação. Certifique-se de que a imagem existe no registo e de que já escreveu corretamente o nome da imagem.

Se a imagem não pode ser obtida, eventos semelhantes ao seguinte são apresentados no resultado de [show de contentor az][az-container-show]:

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

## <a name="container-continually-exits-and-restarts-no-long-running-process"></a>Contentor continuamente fecha e reinicia (nenhum processo de execução longa)

Grupos de contentor predefinido para um [política de reinício](container-instances-restart-policy.md) dos **sempre**, por isso, os contentores no grupo de contentores sempre reiniciado depois de executarem até à conclusão. Poderá ter de alterar esta opção para **OnFailure** ou **Never** se pretende executar contentores com base em tarefas. Se especificar **OnFailure** e veja ainda contínua é reiniciado, pode haver um problema com a aplicação ou script foi executado no seu contentor.

Quando executar a grupos de contentores sem processos de longa execução, que pode ver repetido sai e reinícios com imagens, tais como o Ubuntu ou Alpine. Pode ligar através do [EXEC](container-instances-exec.md) não irá funcionar conforme o contentor não tem nenhum processo mantê-lo ativo. Para resolver este problema, inclua um comando de início como o seguinte à sua implementação do grupo de contentor para manter o contentor em execução.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2016
 --command-line "ping -t localhost"
```

O portal de API de instâncias de contentor e o Azure inclui um `restartCount` propriedade. Para verificar o número de reinicializações para um contentor, pode utilizar o [show de contentor az] [ az-container-show] comando na CLI do Azure. No exemplo seguinte (que foi truncado para fins de brevidade) de saída, pode ver o `restartCount` propriedade no final de saída.

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
> A maioria das imagens de contentor para as distribuições de Linux definir um shell, como bash, como o comando padrão. Como um shell por conta própria não é um serviço de execução longa, estes contentores imediatamente sair de colunas e enquadram-se num loop de reinício quando configurado com a predefinição **sempre** política de reinício.

## <a name="container-takes-a-long-time-to-start"></a>Contentor demora muito tempo a iniciar

Os dois principais fatores que contribuem para o tempo de inicialização de contentor no Azure Container Instances são:

* [Tamanho da imagem](#image-size)
* [Localização de imagem](#image-location)

Imagens do Windows têm [considerações adicionais](#cached-windows-images).

### <a name="image-size"></a>Tamanho da imagem

Se o seu contentor demora muito tempo para iniciar, mas, eventualmente, for bem-sucedida, comece por ver o tamanho de sua imagem de contentor. Porque o Azure Container Instances extrai a imagem de contentor a pedido, o tempo de inicialização, ver está diretamente relacionado ao respetivo tamanho.

Pode ver o tamanho da sua imagem de contentor utilizando o `docker images` comando na CLI do Docker:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A chave para manter os tamanhos de imagem pequena consiste em garantir que sua imagem final não contém tudo o que não é necessário no tempo de execução. Uma forma de fazer isso é com [compilações de vários estágios][docker-multi-stage-builds]. Vários estágios baseia-se tornar fácil garantir que a imagem final contém apenas os artefatos que precisa para seu aplicativo, e não qualquer o extras de conteúdo que era necessário no momento da compilação.

### <a name="image-location"></a>Localização de imagem

Outra forma de reduzir o impacto da solicitação de imagem no tempo de inicialização do seu contentor é alojar a imagem de contentor [Azure Container Registry](/azure/container-registry/) na mesma região onde pretende implementar instâncias de contentor. Isso encurta o caminho de rede que a imagem de contentor precisa de deslocação, reduzindo significativamente o tempo de transferência.

### <a name="cached-windows-images"></a>Imagens do Windows em cache

O Azure Container Instances utiliza um mecanismo de colocação em cache para ajudar a acelerar o tempo de inicialização contentor de imagens com base nas imagens comuns do Windows e Linux. Para obter uma lista detalhada de em cache de imagens e etiquetas, utilize o [lista de imagens em cache] [ list-cached-images] API.

Para garantir que o tempo de inicialização de contentor do Windows mais rápido, utilize um da **três mais recente** versões dos seguintes **duas imagens** como a imagem base:

* [Windows Server Core 2016] [ docker-hub-windows-core] (LTSC apenas)
* [Windows Server 2016 Nano Server][docker-hub-windows-nano]

### <a name="windows-containers-slow-network-readiness"></a>Preparação de rede lenta de contentores do Windows

A criação inicial, contentores do Windows não podem ter nenhuma conectividade de entrada ou saída para até 30 segundos (ou mais tempo, em casos raros). Se a aplicação de contentor precisa de uma ligação à Internet, adicione o atraso e repetir a lógica para permitir que 30 segundos estabelecer ligação à Internet. Após a configuração inicial, redes de contentores devem ser retomada adequadamente.

## <a name="resource-not-available-error"></a>Recurso erro não está disponível

Devido a diversos recursos regionais de carga no Azure, poderá receber o seguinte erro ao tentar implementar uma instância de contentor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Este erro indica que, devido a uma carga pesada na região em que está a tentar implementar, não não possível alocar os recursos especificados para o contentor nesse momento. Utilize um ou mais dos seguintes passos de mitigação para o ajudar a resolver o problema.

* Certifique-se de que as definições de implementação do contentor ser abrangidos os parâmetros definidos no [disponibilidade das regiões do Azure Container Instances](container-instances-region-availability.md)
* Especificar definições de CPU e memória inferior para o contentor
* Implementar noutra região do Azure
* Implementar mais tarde

## <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Não é possível estabelecer ligação à API subjacente do Docker ou executar contentores com privilégios

O Azure Container Instances não expõe o acesso direto para a infraestrutura subjacente que aloja a grupos de contentores. Isto inclui o acesso à API do Docker em execução no anfitrião do contentor e contentores com privilégios em execução. Se necessitar de interação de Docker, verifique os [documentação de referência do REST](https://aka.ms/aci/rest) para ver o que suporta da API ACI. Se houver algo em falta, submeta um pedido no [fóruns de comentários do ACI](https://aka.ms/aci/feedback).

## <a name="ips-may-not-be-accessible-due-to-mismatched-ports"></a>IPs não estar acessível devido a portas não correspondentes

O Azure Container Instances não suporta atualmente mapeamento de porta, como com a configuração de regular docker, no entanto, esta correção está nas previsões. Se encontrar IPs não estão acessíveis quando considerar que deve ser, certifique-se de que configurou a sua imagem de contentor para ouvir as mesmas portas expor no seu grupo de contentores com o `ports` propriedade.

## <a name="next-steps"></a>Passos Seguintes

Saiba como [obter registos de contentor e eventos](container-instances-get-logs.md) para ajudar a depurar os contentores.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/architecture/best-practices/naming-conventions#naming-rules-and-restrictions
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
