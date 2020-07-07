---
title: (PRECADO) Libertação canária com Vamp no cluster Azure DC/OS
description: Como utilizar os serviços de libertação de canários e aplicar a filtragem de tráfego inteligente num cluster de serviço de contentores Azure DC/OS
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: dfdf6e1f8edfb4dafaf93e62090ed51878f9b2aa
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/02/2020
ms.locfileid: "82734829"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(PRECADO) Microserviços de libertação canária com Vamp num cluster de serviço de contentores Azure DC/OS

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste passeio, instalamos o Vamp no Serviço de Contentores Azure com um cluster DC/OS. O canário lança o serviço de demonstração Vamp "sava", e depois resolvemos uma incompatibilidade do serviço com o Firefox aplicando filtragem de tráfego inteligente. 

> [!TIP] 
> Neste walkthrough Vamp corre em um cluster DC/OS, mas você também pode usar Vamp com Kubernetes como orquestrador.
>

## <a name="about-canary-releases-and-vamp"></a>Sobre lançamentos canários e Vamp


[O lançamento de Canário](https://martinfowler.com/bliki/CanaryRelease.html) é uma estratégia de implementação inteligente adotada por organizações inovadoras como netflix, Facebook e Spotify. É uma abordagem que faz sentido, porque reduz as questões, introduz redes de segurança e aumenta a inovação. Então porque é que as empresas não o estão a usar? O alargamento de um oleoduto CI/CD para incluir estratégias canárias adiciona complexidade, e requer um vasto conhecimento e experiência dedessatários. É o suficiente para bloquear empresas e empresas mais pequenas antes mesmo de começarem. 

[O Vamp](https://vamp.io/) é um sistema de código aberto projetado para facilitar esta transição e trazer funcionalidades de libertação canária para o seu programador de contentores preferido. A funcionalidade canária do Vamp vai além dos lançamentos baseados em percentagem. O tráfego pode ser filtrado e dividido numa ampla gama de condições, por exemplo, para utilizadores específicos, gamas IP ou dispositivos. Vamp rastreia e analisa métricas de desempenho, permitindo a automatização com base em dados do mundo real. Pode configurar o revés automático em erros ou escalar variantes de serviço individuais com base na carga ou latência.

## <a name="set-up-azure-container-service-with-dcos"></a>Criar serviço de contentores Azure com DC/OS



1. [Implementar um cluster DC/OS](container-service-deployment.md) com um mestre e dois agentes de tamanho predefinido. 

2. [Crie um túnel SSH](../container-service-connect.md) para ligar ao cluster DC/OS. Este artigo pressupõe que você faça um túnel para o aglomerado no porto local 80.


## <a name="set-up-vamp"></a>Configurar Vamp

Agora que tem um cluster DC/OS em execução, pode instalar o Vamp a partir do UI DC/OS (http: \/ /localhost:80). 

![IU do DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

A instalação é feita em duas fases:

1. **Desdobre o elástico.**

2. Em **seguida, insiram Vamp** instalando o pacote universo Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Implementar elásticos

O vamp requer elasticsearch para recolha de métricas e agregação. Pode utilizar as [imagens magnéticas do Docker](https://hub.docker.com/r/magneticio/elastic/) para implantar uma pilha de elasticsearch Vamp compatível.

1. No UI DC/OS, vá aos **Serviços** e clique em **Serviço de Implantação.**

2. Selecione **o modo JSON** a partir do pop-up **do Serviço Novo** Deploy.

   ![Selecione o modo JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Pasta no seguinte JSON. Esta configuração executa o recipiente com 1 GB de RAM e uma verificação de saúde básica na porta Elasticsearch.
  
   ```JSON
   {
    "id": "elasticsearch",
    "instances": 1,
    "cpus": 0.2,
    "mem": 1024.0,
    "container": {
      "docker": {
        "image": "magneticio/elastic:2.2",
        "network": "HOST",
        "forcePullImage": true
      }
    },
    "healthChecks": [
      {
        "protocol": "TCP",
        "gracePeriodSeconds": 30,
        "intervalSeconds": 10,
        "timeoutSeconds": 5,
        "port": 9200,
        "maxConsecutiveFailures": 0
      }
    ]
   }
   ```
  

3. Clique **em Implementar**.

   DC/OS implanta o recipiente Elástico. Pode acompanhar o progresso na página **Serviços.**  

   ![implantar e? Elástico](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Desdobrar Vampiro

Assim que o Elasticsearch reportar como **Running,** pode adicionar o pacote Vamp DC/OS Universe. 

1. Vá ao **Universo** e procure **vampiros.** 
   ![Vampiro no universo DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Clique **em instalar** ao lado do pacote de vampiros e escolha Advanced **Installation**.

3. Desloque-se para baixo e introduza o seguinte elástico-url: `http://elasticsearch.marathon.mesos:9200` . 

   ![Insira URL elástico](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Clique em **Rever e instalar**e, em seguida, clique em **Instalar** para iniciar a implementação.  

   A DC/OS implementa todos os componentes Vamp necessários. Pode acompanhar o progresso na página **Serviços.**
  
   ![Implementar Vamp como pacote do universo](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Uma vez concluída a implementação, pode aceder à UI Vamp:

   ![Serviço vamp em DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![Vamp UI](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Implemente o seu primeiro serviço

Agora que o Vamp está a funcionar, implante um serviço a partir de uma planta. 

Na sua forma mais simples, uma [planta Vamp](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) descreve os pontos finais (gateways), clusters e serviços para implantar. Vamp usa clusters para agrupar diferentes variantes do mesmo serviço em grupos lógicos para libertação de canários ou testes A/B.  

Este cenário utiliza uma aplicação monolítica de amostra chamada [**sava**](https://github.com/magneticio/sava-product), que está na versão 1.0. O monólito é embalado num recipiente Docker, que fica em Docker Hub sob magneticio/sava:1.0.0. A aplicação normalmente funciona na porta 8080, mas você quer expô-la sob a porta 9050 neste caso. Implemente a aplicação através do Vamp utilizando uma planta simples.

1. Ir para **implementações**.

2. Clique em **Adicionar**.

3. Cole na seguinte planta YAML. Esta planta contém um cluster com apenas uma variante de serviço, que mudamos num passo posterior:

   ```YAML
   name: sava                        # deployment name
   gateways:
    9050: sava_cluster/webport      # stable endpoint
   clusters:
    sava_cluster:               # cluster to create
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
   ```

4. Clique em **Guardar**. Vamp inicia a implantação.

A implementação está listada na página **De Implementações.** Clique na implementação para monitorizar o seu estado.

![Vamp UI - implantação de sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![serviço sava em Vamp UI](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

São criados dois gateways, que estão listados na página **Gateways:**

* um ponto final estável para aceder ao serviço de funcionamento (porta 9050) 
* um gateway interno gerido por Vamp (mais sobre este gateway mais tarde). 

![Vamp UI - gateways de sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

O serviço sava já foi implantado, mas não é possível aceder-lhe externamente porque o Azure Load Balancer ainda não sabe encaminhar o tráfego para ele. Para aceder ao serviço, atualize a configuração de rede Azure.


## <a name="update-the-azure-network-configuration"></a>Atualizar a configuração da rede Azure

Vamp implantou o serviço sava nos nós do agente DC/OS, expondo um ponto final estável no porto 9050. Para aceder ao serviço a partir do cluster DC/OS, escrume as seguintes alterações à configuração da rede Azure na sua implantação do cluster: 

1. **Configure o Balançador de Carga Azure** para os agentes (o recurso denominado **dcos-agent-lb-xxxx)** com uma sonda de saúde e uma regra para encaminhar o tráfego no porto 9050 para as instâncias sava. 

2. **Atualize o grupo de segurança da rede** para os agentes públicos (o recurso denominado **XXXX-agente-public-nsg-XXXX**) para permitir o tráfego na porta 9050.

Para obter medidas detalhadas para completar estas tarefas utilizando o portal Azure, consulte Permitir o [acesso do público a uma aplicação de Serviço de Contentores Azure](container-service-enable-public-access.md). Especifique a porta 9050 para todas as definições da porta.


Uma vez criado tudo, vá à **lâmina** geral do equilibrador de carga do agente DC/OS (o recurso denominado **dcos-agent-lb-xxxx).** Encontre o **endereço IP público**e use o endereço para aceder ao sava no porto 9050.

![Portal Azure - obtenha endereço IP público](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Executar um lançamento canário

Suponha que tem uma nova versão desta aplicação que quer lançar canários para a produção. Tem-no contentorizado como magneticio/sava:1.1.0 e está pronto para ir. O Vamp permite-lhe adicionar facilmente novos serviços à implementação em execução. Estes serviços "fundidos" são implantados juntamente com os serviços existentes no cluster, e atribuídos um peso de 0%. Nenhum tráfego é encaminhado para um serviço recém-fundido até ajustar a distribuição de tráfego. O deslizador de peso no Vamp UI dá-lhe controlo total sobre a distribuição, permitindo ajustes incrementais (libertação canária) ou uma reversão instantânea.

### <a name="merge-a-new-service-variant"></a>Fundir uma nova variante de serviço

Para fundir o novo serviço sava 1.1 com a implementação em execução:

1. Na UI Vamp, clique em **Plantas.**

2. Clique em **Adicionar** e colar na seguinte planta YAML: Esta planta descreve uma nova variante de serviço (sava:1.1.0) para implantar dentro do cluster existente (sava_cluster).

   ```YAML
   name: sava:1.1.0      # blueprint name
   clusters:
    sava_cluster:       # cluster to update
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
   ```
  
3. Clique em **Guardar**. A planta é armazenada e listada na página **Blueprints.**

4. Abra o menu de ação na planta sava:1.1 e clique em **Merge to**.

   ![Vamp UI - plantas](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecione a implementação **de sava** e clique em **Merge**.

   ![Vamp UI - fundir a planta para implantação](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp implementa a nova variante de serviço sava:1.1.0 descrita na planta ao lado de sava:1.0.0 no **sava_cluster** da implementação em execução. 

![Vamp UI - implementação atualizada de sava](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

O **portal sava/sava_cluster/webport** (o ponto final do cluster) também é atualizado, adicionando uma rota para o sava:1.1.0 recém-implantado. Neste ponto, não há tráfego encaminhado para cá (o **PESO** está definido para 0%).

![Vamp UI - porta de entrada de cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Libertação canária

Com ambas as versões de sava implantadas no mesmo cluster, ajuste a distribuição do tráfego entre elas movendo o slider **WEIGHT.**

1. Clique em ![ Vamp UI - edite ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado do **WEIGHT**.

2. Desagre a distribuição de peso para 50%/50% e clique em **Guardar**.

   ![Vamp UI - slider de peso de gateway](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Volte para o seu navegador e refresque a página sava mais algumas vezes. A aplicação sava agora alterna entre uma página sava:1.0 e uma página sava:1.1.

   ![serviços alternados sava1.0 e sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Esta alternância da página funciona melhor com o modo "Incógnito" ou "Anónimo" do seu navegador devido ao cache de ativos estáticos.
  >

### <a name="filter-traffic"></a>Tráfego de filtros

Suponha que após a implementação tenha descoberto uma incompatibilidade em sava:1.1.0 que causa problemas de exibição nos navegadores do Firefox. Pode configurar o Vamp para filtrar o tráfego de entrada e direcionar todos os utilizadores do Firefox de volta para o sava:1.0.0 do estábulo conhecido. Este filtro resolve instantaneamente a perturbação para os utilizadores do Firefox, enquanto todos os outros continuam a usufruir dos benefícios do sava:1.1.0 melhorado.

Vamp usa **condições** para filtrar o tráfego entre as rotas num gateway. O tráfego é primeiro filtrado e direcionado de acordo com as condições aplicadas a cada rota. Todo o tráfego restante é distribuído de acordo com a definição de peso do gateway.

Pode criar uma condição para filtrar todos os utilizadores do Firefox e direcioná-los para a velha sava:1.0.0:

1. Na página sava/sava_cluster/webport **Gateways,** clique em ![ Vamp UI - edite ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para adicionar uma **CONDIÇÃO** à rota sava/sava_cluster/sava:1.0.0/webport. 

2. Introduza o agente de utilizador da condição **== Firefox** e clique em ![ Vamp UI - salvo ](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) .

   Vamp adiciona a condição com uma força padrão de 0%. Para começar a filtrar o tráfego, é necessário ajustar a força da condição.

3. Clique ![ em Vamp UI - edite ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para alterar o **STRENGTH** aplicado à condição.
 
4. Desagregar o **STRENGTH** a 100% e clique em ![ Vamp UI - economize ](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) para economizar.

   Vamp envia agora todo o tráfego correspondente à condição (todos os utilizadores do Firefox) para sava:1.0.0.

   ![Vamp UI - aplique condição ao gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Por fim, ajuste o peso do gateway para enviar todo o tráfego restante (todos os utilizadores não-Firefox) para o novo sava:1.1.0. Clique em ![ Vamp UI - edite ](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado do **WEIGHT** e desfaça a distribuição de peso para que 100% seja direcionada para a rota sava/sava_cluster/sava:1.0/webport.

   Todo o tráfego não filtrado pela condição é agora direcionado para o novo sava:1.1.0.

6. Para ver o filtro em ação, abra dois navegadores diferentes (um Firefox e outro navegador) e aceda ao serviço sava de ambos. Todos os pedidos do Firefox são enviados para sava:1.0.0, enquanto todos os outros navegadores são direcionados para sava:1.1.0.

   ![Vamp UI - tráfego de filtros](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Resumindo

Este artigo foi uma rápida introdução a Vamp num cluster DC/OS. Para começar, tem o Vamp a funcionar no seu cluster DC/OS do Serviço de Contentores Azure, implementou um serviço com uma planta Vamp e acedeu-o no ponto final exposto (gateway).

Também tocámos em algumas características poderosas do Vamp: fundir uma nova variante de serviço com a implementação em execução e introduzi-la incrementalmente, em seguida, filtrar o tráfego para resolver uma incompatibilidade conhecida.


## <a name="next-steps"></a>Passos seguintes

* Saiba mais sobre a gestão das ações da Vamp através da [API Vamp REST](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Construa scripts de automação Vamp em Node.js e execute-os como [fluxos de trabalho vamp](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* Consulte [tutoriais adicionais do VAMP.](https://docs.vamp.io/tutorials/)

