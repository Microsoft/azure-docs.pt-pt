---
title: Resolver problemas comuns
description: Aprenda a resolver problemas comuns quando o seu destacamento, execução ou gestão de instâncias de contentores Azure
ms.topic: article
ms.date: 09/25/2019
ms.custom: mvc
ms.openlocfilehash: 07cdbfb27aaf9076e726ebda861ed24996e10135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74533383"
---
# <a name="troubleshoot-common-issues-in-azure-container-instances"></a>Resolver problemas comuns no Azure Container Instances

Este artigo mostra como resolver problemas comuns para gerir ou implantar contentores para as instâncias de contentores de Azure. Consulte também [perguntas frequentes.](container-instances-faq.md)

Se precisar de suporte adicional, consulte as opções de **suporte disponíveis ajuda +** no portal [Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="issues-during-container-group-deployment"></a>Problemas durante a implantação do Grupo de Contentores
### <a name="naming-conventions"></a>Convenções de nomenclatura

Ao definir a especificação do recipiente, certos parâmetros requerem a adesão às restrições de nomeação. Abaixo está uma tabela com requisitos específicos para propriedades do grupo de contentores. Para obter mais informações sobre as convenções de nomeação do Azure, consulte [convenções de naming][azure-name-restrictions] no Centro de Arquitetura Azure.

| Âmbito | Comprimento | Maiúsculas e Minúsculas | Carateres válidos | Padrão sugerido | Exemplo |
| --- | --- | --- | --- | --- | --- |
| Nome do grupo de contentores | 1-64 |Não sensível a maiúsculas e minúsculas |Alfanumérico, e hífen em qualquer lugar, exceto o primeiro ou último personagem |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Nome do contentor | 1-64 |Não sensível a maiúsculas e minúsculas |Alfanumérico, e hífen em qualquer lugar, exceto o primeiro ou último personagem |`<name>-<role>-CG<number>` |`web-batch-CG1` |
| Portos de contentores | Entre 1 e 65535 |Número inteiro |Inteiro entre 1 e 65535 |`<port-number>` |`443` |
| Etiqueta de nome DNS | 5-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico, e hífen em qualquer lugar, exceto o primeiro ou último personagem |`<name>` |`frontend-site1` |
| Variável de ambiente | 1-63 |Não sensível a maiúsculas e minúsculas |Alfanumérico, e sublinhar (_) em qualquer lugar, exceto o primeiro ou último personagem |`<name>` |`MY_VARIABLE` |
| Nome de volume | 5-63 |Não sensível a maiúsculas e minúsculas |Letras minúsculas e números, e hífenes em qualquer lugar, exceto o primeiro ou último personagem. Não pode conter dois hífenes consecutivos. |`<name>` |`batch-output-volume` |

### <a name="os-version-of-image-not-supported"></a>Versão ossa da imagem não suportada

Se especificar uma imagem que as Instâncias de `OsVersionNotSupported` Contentores Azure não suportam, um erro é devolvido. O erro é semelhante `{0}` ao seguinte, onde está o nome da imagem que tentou implementar:

```json
{
  "error": {
    "code": "OsVersionNotSupported",
    "message": "The OS version of image '{0}' is not supported."
  }
}
```

Este erro é encontrado com maior frequência ao implementar imagens do Windows baseadas no lançamento do Canal 1709 ou 1803, que não são suportados. Para imagens suportadas do Windows em Instâncias de Contentores Azure, consulte [frequentemente perguntas](container-instances-faq.md#what-windows-base-os-images-are-supported).

### <a name="unable-to-pull-image"></a>Incapaz de puxar a imagem

Se as instâncias de contentores Azure forem inicialmente incapazes de puxar a sua imagem, ela se retenta durante um período de tempo. Se a operação de puxar a imagem continuar a falhar, a `Failed to pull image` ACI acaba por falhar na implementação e poderá ver um erro.

Para resolver este problema, elimine a instância do contentor e tente novamente a sua implantação. Certifique-se de que a imagem existe no registo e que escreveu corretamente o nome da imagem.

Se a imagem não puder ser puxada, eventos como o seguinte são mostrados na saída do [contentor az mostrar:][az-container-show]

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
### <a name="resource-not-available-error"></a>Erro não disponível de recurso

Devido à variação da carga de recursos regionais em Azure, poderá receber o seguinte erro ao tentar implementar uma instância de contentor:

`The requested resource with 'x' CPU and 'y.z' GB memory is not available in the location 'example region' at this moment. Please retry with a different resource request or in another location.`

Este erro indica que devido à carga pesada na região em que está a tentar implantar, os recursos especificados para o seu contentor não podem ser atribuídos nessa altura. Utilize uma ou mais das seguintes medidas de mitigação para ajudar a resolver o seu problema.

* Verifique se as definições de implantação do seu contentor se enquadram nos parâmetros definidos na [disponibilidade da Região para instâncias de contentores Azure](container-instances-region-availability.md)
* Especifique as definições de CPU mais baixos e de memória para o recipiente
* Desdobre para uma região diferente de Azure
* Implementar mais tarde

## <a name="issues-during-container-group-runtime"></a>Problemas durante o tempo de execução do Grupo de Contentores
### <a name="container-continually-exits-and-restarts-no-long-running-process"></a>O contentor sai e reinicia continuamente (sem processo de longo prazo)

Os grupos de contentores não se inserem numa política de [reinício](container-instances-restart-policy.md) do **Always**, para que os contentores do grupo de contentores reiniciem sempre depois de estarem concluídos. Poderá ter de alterar isto para **OnFailure** ou **Nunca** se pretender executar contentores baseados em tarefas. Se especificar **o OnFailure** e ainda vir reinícios contínuos, poderá haver um problema com a aplicação ou script executado no seu recipiente.

Ao executar grupos de contentores sem processos de longa duração, poderá ver saídas repetidas e reiniciar com imagens como Ubuntu ou Alpine. A ligação via [EXEC](container-instances-exec.md) não funcionará, uma vez que o recipiente não tem processo para mantê-lo vivo. Para resolver este problema, inclua um comando de início como o seguinte com a implantação do grupo de contentores para manter o contentor em funcionamento.

```azurecli-interactive
## Deploying a Linux container
az container create -g MyResourceGroup --name myapp --image ubuntu --command-line "tail -f /dev/null"
```

```azurecli-interactive 
## Deploying a Windows container
az container create -g myResourceGroup --name mywindowsapp --os-type Windows --image mcr.microsoft.com/windows/servercore:ltsc2019
 --command-line "ping -t localhost"
```

O portal Container Instances API `restartCount` e Azure inclui uma propriedade. Para verificar o número de reinícios de um recipiente, pode utilizar o comando de exibição de [contentores az][az-container-show] no ClI Azure. No seguinte exemplo de saída (que foi truncada para `restartCount` brevidade), você pode ver a propriedade no final da saída.

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
> A maioria das imagens de contentores para distribuição de Linux estabeleceuma concha, como a bash, como o comando padrão. Uma vez que uma concha por si só não é um serviço de longa duração, estes contentores saem imediatamente e caem num ciclo de reinício quando configurados com a política de reinício **de sempre.**

### <a name="container-takes-a-long-time-to-start"></a>O contentor leva muito tempo a começar

Os três fatores primários que contribuem para o tempo de arranque de contentores em casos de contentores azure são:

* [Tamanho da imagem](#image-size)
* [Localização da imagem](#image-location)
* [Imagens em cache](#cached-images)

As imagens do Windows têm [considerações adicionais.](#cached-images)

#### <a name="image-size"></a>Tamanho da imagem

Se o seu recipiente demorar muito tempo a começar, mas eventualmente conseguir, comece por olhar para o tamanho da sua imagem de recipiente. Uma vez que a Azure Container Instances puxa a sua imagem de contentor a pedido, o tempo de arranque que vê está diretamente relacionado com o seu tamanho.

Pode visualizar o tamanho da sua `docker images` imagem de contentor utilizando o comando no Docker CLI:

```console
$ docker images
REPOSITORY                                    TAG       IMAGE ID        CREATED          SIZE
mcr.microsoft.com/azuredocs/aci-helloworld    latest    7367f3256b41    15 months ago    67.6MB
```

A chave para manter os tamanhos de imagem pequenos é garantir que a sua imagem final não contém nada que não seja necessário no tempo de execução. Uma maneira de fazer isto é com [construções em várias fases.][docker-multi-stage-builds] As construções em várias fases facilitam a garantia de que a imagem final contém apenas os artefactos necessários para a sua aplicação, e nenhum dos conteúdos extra que era necessário no momento da construção.

#### <a name="image-location"></a>Localização da imagem

Outra forma de reduzir o impacto da imagem no tempo de arranque do seu contentor é alojar a imagem do contentor no Registo de [Contentores Azure,](/azure/container-registry/) na mesma região onde pretende implementar instâncias de contentores. Isto encurta o caminho de rede que a imagem do contentor precisa de percorrer, encurtando significativamente o tempo de descarregamento.

#### <a name="cached-images"></a>Imagens em cache

O Azure Container Instances utiliza um mecanismo de cache para ajudar a `nanoserver:1809`acelerar `servercore:ltsc2019`o `servercore:1809`tempo de arranque do contentor para imagens construídas em imagens comuns da [base do Windows,](container-instances-faq.md#what-windows-base-os-images-are-supported)incluindo , e . Imagens linux comumente `ubuntu:1604` usadas, como e `alpine:3.6` também são em cache. Para obter uma lista atualizada de imagens e etiquetas em cache, utilize a [Lista De Imagens Em Cached][list-cached-images] API.

> [!NOTE]
> A utilização de imagens baseadas no Windows Server 2019 em Instâncias de Contentores Azure está em pré-visualização.

#### <a name="windows-containers-slow-network-readiness"></a>Os recipientes do Windows retardam a prontidão da rede

Na criação inicial, os contentores windows podem não ter conectividade de entrada ou saída até 30 segundos (ou mais, em casos raros). Se a sua aplicação de contentores necessitar de uma ligação à Internet, adicione uma lógica de atraso e de novo para permitir 30 segundos para estabelecer a conectividade da Internet. Após a configuração inicial, a rede de contentores deve ser retomada adequadamente.

### <a name="cannot-connect-to-underlying-docker-api-or-run-privileged-containers"></a>Não é possível ligar-se à API subjacente ou executar contentores privilegiados

As instâncias de contentores Azure não expõem o acesso direto à infraestrutura subjacente que alberga grupos de contentores. Isto inclui o acesso à API docker que funciona no hospedeiro do contentor e em contentores privilegiados de funcionamento. Se necessitar de interação com o Docker, verifique a [documentação](https://aka.ms/aci/rest) de referência REST para ver o que suporta a ACI API. Se faltar alguma coisa, submeta um pedido sobre os fóruns de feedback da [ACI](https://aka.ms/aci/feedback).

### <a name="container-group-ip-address-may-not-be-accessible-due-to-mismatched-ports"></a>O endereço IP do grupo de contentores não pode ser acessível devido a portas desajustadas

As instâncias de contentores Azure ainda não suportam o mapeamento da porta como com a configuração regular do estivador. Se encontrar o endereço IP de um grupo de contentores não é acessível quando acredita que deve ser, certifique-se `ports` de ter configurado a sua imagem de contentor para ouvir as mesmas portas que expõe no seu grupo de contentores com a propriedade.

Se quiser confirmar que as instâncias de contentores Azure podem ouvir na porta `aci-helloworld` configurada na sua imagem de contentor, teste uma implementação da imagem que expõe a porta. Também execute a `aci-helloworld` aplicação para que ouça na porta. `aci-helloworld`aceita uma variável `PORT` ambiental opcional para anular a porta padrão 80 que ouve. Por exemplo, para testar a porta 9000, defino a [variável ambiental](container-instances-environment-variables.md) quando criar o grupo de contentores:

1. Instale o grupo de contentores para expor a porta 9000 e passe o número da porta como o valor da variável ambiental. O exemplo é formatado para a concha bash. Se preferir outra concha como powerShell ou Comando Prompt, terá de ajustar a atribuição variável em conformidade.
    ```azurecli
    az container create --resource-group myResourceGroup \
    --name mycontainer --image mcr.microsoft.com/azuredocs/aci-helloworld \
    --ip-address Public --ports 9000 \
    --environment-variables 'PORT'='9000'
    ```
1. Encontre o endereço IP do grupo de `az container create`contentores na saída de comando de . Procure o valor do **IP.** 
1. Depois de o recipiente ser aprovisionado com sucesso, navegue para o endereço `192.0.2.0:9000`IP e porta da aplicação de contentores no seu navegador, por exemplo: . 

    Devia ver o "Bem-vindo às Instâncias do Contentor De Azure!" mensagem exibida pela aplicação web.
1. Quando terminar com o recipiente, retire-o utilizando o `az container delete` comando:

    ```azurecli
    az container delete --resource-group myResourceGroup --name mycontainer
    ```

## <a name="next-steps"></a>Passos seguintes

Aprenda a [recuperar troncos e eventos](container-instances-get-logs.md) de contentores para ajudar a desinver os seus contentores.

<!-- LINKS - External -->
[azure-name-restrictions]: https://docs.microsoft.com/azure/cloud-adoption-framework/ready/azure-best-practices/naming-and-tagging#naming-and-tagging-resources
[windows-sac-overview]: https://docs.microsoft.com/windows-server/get-started/semi-annual-channel-overview
[docker-multi-stage-builds]: https://docs.docker.com/engine/userguide/eng-image/multistage-build/
[docker-hub-windows-core]: https://hub.docker.com/_/microsoft-windows-servercore
[docker-hub-windows-nano]: https://hub.docker.com/_/microsoft-windows-nanoserver

<!-- LINKS - Internal -->
[az-container-show]: /cli/azure/container#az-container-show
[list-cached-images]: /rest/api/container-instances/listcachedimages
