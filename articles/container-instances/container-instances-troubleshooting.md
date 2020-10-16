---
title: Resolver problemas comuns
description: Saiba como resolver problemas comuns quando implementar, executar ou gerir instâncias de contentores do Azure
ms.topic: article
ms.date: 06/25/2020
ms.custom: mvc
ms.openlocfilehash: 46d3ad6afb1761ca9503676ad2176482b7e4530e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "86260741"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Resolver problemas comuns no Azure Container Instances

Este artigo mostra como resolver problemas comuns para gerir ou implantar contentores em instâncias de contentores Azure. Consulte também [perguntas frequentes.](container-instances-faq.md)

Se precisar de suporte adicional, consulte as opções de apoio disponíveis **no** [portal Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problemas durante a implantação do grupo de contentores
### <a name="naming-conventions"></a>Convenções de nomenclatura

Ao definir a especificação do seu recipiente, certos parâmetros requerem adesão às restrições de nomeação. Abaixo está uma tabela com requisitos específicos para propriedades de grupo de contentores. Para mais informações, consulte [as convenções de nomeação][azure-name-restrictions] no Centro de Arquitetura Azure e [as regras de nomeação e restrições para os recursos Azure.][naming-rules]

| Âmbito | Comprimento | Maiúsculas e Minúsculas | Carateres válidos | Padrão sugerido | Exemplo |
| --- | --- | --- | --- | --- | --- |
| Nome do recipiente<sup>1</sup> | 1-63 |Minúsculas | Alfanumérico, e hífen em qualquer lugar, exceto o primeiro ou último personagem |`<name>-<role>-container<number>` |`web-batch-container1` |
| Portos de contentores | Entre 1 e 65535 |Número inteiro |Inteiro entre 1 e 65535 |`<port-number>` |`443` |
| Etiqueta de nome DNS | 5-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico, e hífen em qualquer lugar, exceto o primeiro ou último personagem |`<name>` |`frontend-site1` |
| Variável de ambiente | 1-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico, e sublinha (_) em qualquer lugar, exceto o primeiro ou último personagem |`<name>` |`MY_VARIABLE` |
| Nome do volume | 5-63 |Minúsculas |Alfanumérico, e hífenes em qualquer lugar, exceto o primeiro ou último personagem. Não pode conter dois hífenes consecutivos. |`<name>` |`batch-output-volume` |

<sup>1</sup> Restrição também para nomes de grupos de contentores quando não especificados independentemente de casos de contentores, por exemplo com `az container create` destacamentos de comando.

### <a name="os-version-of-image-not-supported"></a>Versão so de imagem não suportada

Se especificar uma imagem que as Instâncias do Contentor Azure não suportam, `OsVersionNotSupported` um erro é devolvido. O erro é semelhante ao seguinte, onde `{0}` está o nome da imagem que tentou implementar:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Este erro é encontrado mais frequentemente ao implementar imagens do Windows que são baseadas no Semi-Annual lançamento do Canal 1709 ou 1803, que não são suportados. Para imagens suportadas do Windows em Instâncias de Contentores Azure, consulte [perguntas frequentes](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Incapaz de puxar a imagem

Se o Azure Container Instances não conseguir puxar a sua imagem, retritada por um período de tempo. Se a operação de puxar a imagem continuar a falhar, a ACI acaba por falhar na implantação e poderá ver um `Failed to pull image` erro.

Para resolver este problema, elimine a instância do recipiente e redoça a sua colocação. Certifique-se de que a imagem existe no registo e que escreveu corretamente o nome da imagem.

Se a imagem não puder ser puxada, eventos como o seguinte são mostrados na saída do [contentor az show][az-container-show]:

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
### <a name="resource-not-available-error"></a>Erro não disponível de recursos

Devido à variação da carga de recursos regionais em Azure, poderá receber o seguinte erro ao tentar implantar uma instância de contentor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Este erro indica que devido à carga pesada na região em que está a tentar implantar, os recursos especificados para o seu contentor não podem ser atribuídos nessa altura. Use uma ou mais das seguintes medidas de mitigação para ajudar a resolver o seu problema.

* Verifique se as definições de implantação do contentor se enquadram nos parâmetros definidos na [disponibilidade da Região para instâncias de contentores Azure](container-instances-region-availability.md)
* Especificar as definições mais baixas de CPU e memória para o recipiente
* Implantar para uma região de Azure diferente
* Implementar mais tarde

## <a name="issues-during-container-group-runtime"></a>Problemas durante o tempo de funcionação do grupo de contentores
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>O contentor é desligado e reiniciado continuamente (sem processo de longa duração)

Os grupos de contentores não têm uma política de [reinício](container-instances-restart-policy.md) da **Always**, pelo que os contentores do grupo de contentores reiniciam sempre após a sua conclusão. Pode ter de alterar isto para **OnFailure** ou **nunca** se pretender executar recipientes baseados em tarefas. Se especificar **o OnFailure** e continuar a ver o recomeço contínuo, pode haver um problema com a aplicação ou script executado no seu contentor.

Ao executar grupos de contentores sem processos de longa duração, poderá ver saídas repetidas e reiniciar com imagens como Ubuntu ou Alpine. A ligação via [EXEC](container-instances-exec.md) não funcionará, uma vez que o contentor não tem qualquer processo para mantê-lo vivo. Para resolver este problema, inclua um comando inicial como o seguinte com a colocação do seu grupo de contentores para manter o recipiente em funcionamento.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

O portal API e Azure de Instâncias de Contentores inclui uma `restartCount` propriedade. Para verificar o número de reiniciações de um recipiente, pode utilizar o comando de demonstração do [recipiente Az][az-container-show] no Azure CLI. No seguinte exemplo de saída (que foi truncado para a brevidade), você pode ver a `restartCount` propriedade no final da saída.

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
> A maioria das imagens de contentores para distribuição linux definem uma concha, como a bash, como o comando padrão. Uma vez que uma concha por si só não é um serviço de longa duração, estes recipientes saem imediatamente e caem num ciclo de reinício quando configurados com a política **de** reinício de sempre por defeito.

### <a name="container-takes-a-long-time-to-start"></a>O contentor demora muito tempo a iniciar

Os três fatores primários que contribuem para o tempo de arranque do contentor em Instâncias de Contentores Azure são:

* [Tamanho da imagem](#image-size)
* [Localização da imagem](#image-location)
* [Imagens em cache](#cached-images)

As imagens do Windows têm [considerações adicionais.](#cached-images)

#### <a name="image-size"></a>Tamanho da imagem

Se o seu recipiente demorar muito tempo a começar, mas eventualmente tiver sucesso, comece por olhar para o tamanho da sua imagem de recipiente. Como a Azure Container Instances puxa a imagem do seu contentor a pedido, o tempo de arranque que vê está diretamente relacionado com o seu tamanho.

Pode ver o tamanho da sua imagem de recipiente utilizando o `docker images` comando no Docker CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A chave para manter os tamanhos de imagem pequenos é garantir que a sua imagem final não contenha nada que não seja necessário em tempo de execução. Uma maneira de fazê-lo é com [construções em várias fases.][docker-multi-stage-builds] As construções em várias fases facilitam a garantia de que a imagem final contém apenas os artefactos de que necessita para a sua aplicação, e não qualquer conteúdo extra necessário no momento da construção.

#### <a name="image-location"></a>Localização da imagem

Outra forma de reduzir o impacto da imagem puxada para o tempo de arranque do seu contentor é hospedar a imagem do contentor no [Registo de Contentores Azure](../container-registry/index.yml) na mesma região onde pretende implantar casos de contentores. Isto encurta o caminho de rede que a imagem do contentor precisa de percorrer, reduzindo significativamente o tempo de descarregamento.

#### <a name="cached-images"></a>Imagens em cache

Azure Container Instances usa um mecanismo de caching para ajudar a acelerar o tempo de arranque do contentor para imagens construídas em [imagens comuns do Windows base](container-instances-faq.md#what-windows-base-os-images-are-supported), incluindo `nanoserver:1809` , e `servercore:ltsc2019` `servercore:1809` . Imagens linux comumente usadas, como `ubuntu:1604` e também são em `alpine:3.6` cache. Para obter uma lista atualizada de imagens e tags em cache, utilize a [Lista cached Images][list-cached-images] API.

> [!NOTE]
> A utilização de imagens baseadas no Windows Server 2019 em Instâncias de Contentores Azure está em pré-visualização.

#### <a name="windows-containers-slow-network-readiness"></a>Os recipientes do Windows retardam a prontidão da rede

Na criação inicial, os recipientes Windows podem não ter conectividade de entrada ou saída até 30 segundos (ou mais, em casos raros). Se a sua aplicação de contentor precisar de uma ligação à Internet, adicione a lógica de atraso e de relemcrôs-se para permitir que 30 segundos estabeleçam conectividade com a Internet. Após a configuração inicial, a rede de contentores deve ser retomada adequadamente.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Não é possível ligar-se à API subjacente ou executar contentores privilegiados

As instâncias do contentor Azure não expõem o acesso direto à infraestrutura subjacente que acolhe grupos de contentores. Isto inclui o acesso à API docker que funciona no hospedeiro do contentor e executa recipientes privilegiados. Se necessitar de interação com o Docker, consulte a [documentação de referência REST](https://aka.ms/aci/rest) para ver o que a API suporta. Se faltar alguma coisa, submeta um pedido nos [fóruns de feedback](https://aka.ms/aci/feedback)da ACI .

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>O endereço IP do grupo de contentores pode não estar acessível devido a portas incompatíveis

Azure Container Instances ainda não suporta mapeamento de porta como com configuração regular de estivador. Se encontrar o endereço IP de um grupo de contentores não está acessível quando acredita que deve ser, certifique-se de que configura a sua imagem de contentor para ouvir as mesmas portas que expõe no seu grupo de contentores com a `ports` propriedade.

Se quiser confirmar que as instâncias do recipiente Azure podem ouvir na porta que configuraram na sua imagem de contentor, teste uma colocação da `aci-helloworld` imagem que expõe a porta. Também execute a `aci-helloworld` aplicação para que ouça na porta. `aci-helloworld` aceita uma variável de ambiente opcional `PORT` para anular a porta padrão 80 que ouve. Por exemplo, para testar a porta 9000, desaprote a [variável ambiente](container-instances-environment-variables.md) quando criar o grupo de contentores:

1. Crie o grupo de contentores para expor a porta 9000 e passe o número da porta como o valor da variável ambiente. O exemplo é formatado para a casca bash. Se preferir outra concha, como PowerShell ou Command Prompt, terá de ajustar a atribuição variável em conformidade.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Encontre o endereço IP do grupo de contentores na saída de comando de `az container create` . Procure o valor do **IP.** 
1. Depois de o contentor ser aprovisionado com sucesso, navegue pelo endereço IP e porta da aplicação do contentor no seu navegador, por exemplo: `192.0.2.0:9000` . 

    Devia ver o "Bem-vindo a Azure Container Instances!" mensagem exibida pela aplicação web.
1. Quando terminar o recipiente, retire-o utilizando o `az container delete` comando:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Passos seguintes

Saiba como [recuperar troncos e eventos](container-instances-get-logs.md) de contentores para ajudar a depurar os seus contentores.

<!-- LINKS - External -->
[azure-name-restrictions]: /azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[naming-rules]: ../azure-resource-manager/management/resource-name-rules.md
[windows-sac-overview]: /windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/location/listcachedimages
