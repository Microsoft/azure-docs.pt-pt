---
title: (PRETERIDO) Versão canary com Vamp num cluster DC/OS do Azure
description: Como utilizar Vamp para serviços de versão canary e aplicar filtragem num cluster DC/OS do Azure Container Service de tráfego inteligente
services: container-service
author: gggina
manager: jeconnoc
ms.service: container-service
ms.topic: article
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: cd2eb3ba1d3207f4f210aa259e938bb42b44d37a
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57535455"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>(PRETERIDO) Microsserviços de versão canary com Vamp num cluster DC/OS do Azure Container Service

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Nestas instruções, criamos Vamp no Azure Container Service com um cluster DC/OS. Podemos canary o serviço de demonstração de Vamp "sava" da versão e, em seguida, resolver uma incompatibilidade do serviço com o Firefox ao aplicar a filtragem de tráfego inteligente. 

> [!TIP] 
> Nestas instruções Vamp é executado num cluster DC/OS, mas também pode utilizar Vamp com o Kubernetes como o orchestrator.
>

## <a name="about-canary-releases-and-vamp"></a>Sobre versões de proteção e Vamp


[Lançar canary](https://martinfowler.com/bliki/CanaryRelease.html) é uma estratégia de implantação do smart adotada por organizações inovadoras, como o Netflix, o Facebook e o Spotify. É uma abordagem que faz sentido, pois ele reduz problemas, introduz a redes de segurança para e aumenta a inovação. Então, por que não são todas as empresas de usá-lo? Estendendo um pipeline CI/CD para incluir as estratégias canary adiciona complexidade e requer conhecimento de devops extensa e experiência. Isso é o suficiente para bloquear mais pequenas empresas e empresas semelhantes, antes de eles até mesmo começar a utilizar. 

[Vamp](https://vamp.io/) é um sistema de código-fonte aberto projetado para facilitar esta transição e trazer Canárias liberação de recursos para o agendador de contentor preferencial. Funcionalidade de proteção do vamp vai além da implementação baseado em percentagem. Tráfego pode ser filtrado e dividido numa ampla variedade de condições, por exemplo, para utilizadores específicos de destino, intervalos de IP ou dispositivos. Vamp controla e analisa métricas de desempenho, permitindo a automatização com base nos dados do mundo real. Pode configurar a reversão automática em caso de erro ou dimensionar as variantes de serviço individual com base na carga ou latência.

## <a name="set-up-azure-container-service-with-dcos"></a>Configurar o Azure Container Service com DC/OS



1. [Implementar um cluster DC/OS](container-service-deployment.md) com um mestre e dois agentes de tamanho predefinido. 

2. [Criar um túnel SSH](../container-service-connect.md) para ligar ao cluster DC/OS. Este artigo pressupõe que o túnel para o cluster na porta local 80.


## <a name="set-up-vamp"></a>Configurar Vamp

Agora que tem um cluster DC/OS em execução, pode instalar Vamp a partir da IU do DC/OS (http://localhost:80). 

![IU do DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

Instalação é feita em duas fases:

1. **Implemente o Elasticsearch**.

2. Em seguida, **implementar Vamp** ao instalar o pacote de universo de Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Implemente o Elasticsearch

Vamp requer o Elasticsearch para a coleta de métricas e de agregação. Pode utilizar o [imagens do Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) para implementar uma pilha de Vamp Elasticsearch compatível.

1. Na IU do DC/OS, aceda a **serviços** e clique em **implementar serviço**.

2. Selecione **modo JSON** partir do **implementar o novo serviço** pop-up.

  ![Selecione o modo JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Cole o seguinte JSON. Esta configuração é executado o contentor com 1 GB de RAM e uma verificação de estado de funcionamento básico na porta Elasticsearch.
  
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
  

3. Clique em **implementar**.

  DC/OS implementa o contentor de Elasticsearch. Pode controlar o progresso no **serviços** página.  

  ![implementar e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Implementar Vamp

Assim que o Elasticsearch comunica **em execução**, pode adicionar o pacote de DC/OS Universe Vamp. 

1. Aceda a **universo** e procure **vamp**. 
  ![Vamp no DC/OS universe](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Clique em **instale** junto a vamp empacotamento e a escolha **instalação avançada**.

3. Desloque para baixo e introduza o seguinte url de elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

  ![Introduza o URL do Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Clique em **reveja e instale**, em seguida, clique em **instalar** para iniciar a implementação.  

  DC/OS implementa todos os Vamp componentes obrigatórios. Pode controlar o progresso no **serviços** página.
  
  ![Implementar Vamp como pacote do universo](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Depois de concluída a implementação, pode acessar a interface do Usuário Vamp:

  ![Serviço de vamp no DC/OS](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
  ![Vamp da interface do Usuário](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Implementar o seu primeiro serviço

Agora que essa Vamp está em execução, implemente um serviço a partir de um plano gráfico. 

Em sua forma mais simples, um [Vamp esquema](https://vamp.io/documentation/using-vamp/blueprints/) descreve os pontos de extremidade (gateways), clusters e serviços para implementar. Vamp utiliza clusters para agrupar variantes diferentes do mesmo serviço em grupos lógicos para lançar canary ou A testes a / B.  

Este cenário utiliza uma aplicação monolítica de exemplo chamada [ **sava**](https://github.com/magneticio/sava), que está na versão 1.0. A solução é empacotada num contentor do Docker, que está no Docker Hub em magneticio/sava:1.0.0. A aplicação é executada normalmente na porta 8080, mas se quiser expô-los em porta 9050 neste caso. Implemente a aplicação através de Vamp usando uma esquema simple.

1. Aceda a **implementações**.

2. Clique em **Adicionar**.

3. Cole o seguinte esquema YAML. Este esquema contém um cluster com a variante de apenas um serviço, o que podemos alterar num passo posterior:

  ```YAML
  name: sava                        # deployment name
  gateways:
    9050: sava_cluster/webport      # stable endpoint
  clusters:
    sava_cluster:               # cluster to create
     services:
        -
          breed:
            name: sava:1.0.0        # service variant name
            deployable: magneticio/sava:1.0.0
            ports:
              webport: 8080/http # cluster endpoint, used for canary releasing
  ```

4. Clique em **Guardar**. Vamp inicia a implementação.

A implementação está listada na **implementações** página. Clique na implementação para monitorizar o estado.

![Vamp da interface do Usuário - implementar sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![serviço de Sava na interface de Usuário Vamp](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

São criados dois gateways, que estão listado no **Gateways** página:

* um ponto de extremidade estável para aceder ao serviço em execução (porta 9050) 
* um gerido Vamp gateway interno (mais sobre este gateway mais tarde). 

![Vamp da interface do Usuário - sava gateways](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

O serviço de sava tem agora implementado, mas não é possível aceder-lhe externamente porque o Balanceador de carga do Azure não sabe ainda a reencaminhar o tráfego para o mesmo. Para aceder ao serviço, atualize a configuração de rede do Azure.


## <a name="update-the-azure-network-configuration"></a>Atualizar a configuração de rede do Azure

Vamp implementaram o serviço de sava em nós de agente do DC/OS, expor um ponto de extremidade na porta 9050 estável. Para aceder ao serviço de fora do cluster do DC/OS, efetue as seguintes alterações à configuração de rede do Azure na sua implementação de cluster: 

1. **Configurar o Azure Load Balancer** para os agentes (o recurso com o nome **dcos-agent-lb-xxxx**) com uma sonda de estado de funcionamento e uma regra para encaminhar o tráfego na porta 9050 para as instâncias de sava. 

2. **Atualizar o grupo de segurança de rede** para os agentes públicos (o recurso com o nome **XXXX-agent-public-nsg-XXXX**) para permitir o tráfego na porta 9050.

Para obter passos detalhados concluir estas tarefas com o portal do Azure, consulte [ativar o acesso público a uma aplicação do Azure Container Service](container-service-enable-public-access.md). Especifique a porta 9050 para todas as definições de porta.


Quando tudo tiver sido criada, vá para o **descrição geral** painel do Balanceador de carga do agente DC/OS (o recurso com o nome **dcos-agent-lb-xxxx**). Encontrar o **endereço IP público**e utilize o endereço para aceder a sava na porta 9050.

![Portal do Azure – obter um endereço IP](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Executar uma versão canary

Suponha que tenha uma nova versão da aplicação que pretende que a versão canary em produção. Tê-lo em contentores como magneticio/sava:1.1.0 e está pronto para começar. Vamp permite-lhe adicionar facilmente novos serviços para a implementação em execução. Estes serviços "intercalados" são implementados em conjunto com os serviços existentes no cluster e recebe um peso de 0%. Não existe tráfego é encaminhado para um serviço recentemente intercalado até que ajustar a distribuição de tráfego. O controlo de deslize de peso na IU do Vamp dá-lhe controlo total sobre a distribuição, permitindo ajustes incrementais (versão canary) ou uma reversão instantânea.

### <a name="merge-a-new-service-variant"></a>Intercalar uma nova variante de serviço

Para mesclar o novo serviço de sava 1.1 com a implementação em execução:

1. Na IU do Vamp, clique em **esquemas**.

2. Clique em **adicionar** e cole o seguinte esquema YAML: Este esquema descreve uma nova variante de serviço (sava: 1.1.0) para implementar dentro do cluster existente (sava_cluster).

  ```YAML
  name: sava:1.1.0      # blueprint name
  clusters:
    sava_cluster:       # cluster to update
      services:
        -
          breed:
            name: sava:1.1.0    # service variant name
            deployable: magneticio/sava:1.1.0    
            ports:
              webport: 8080/http # cluster endpoint to update
  ```
  
3. Clique em **Guardar**. O plano gráfico é armazenado e listado no **esquemas** página.

4. Abra o menu de ação no esquema a sava: 1.1 e clique em **intercalar com o**.

  ![Vamp da interface do Usuário - esquemas](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecione o **sava** implementação e clique em **intercalar**.

  ![Vamp da interface do Usuário - esquema de intercalação para implementação](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp implementa a variante de serviço novo sava: 1.1.0 descrita no esquema juntamente com sava: 1.0.0 no **sava_cluster** da implementação em execução. 

![Vamp da interface do Usuário - implementação de sava atualizada](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

O **sava/sava_cluster/webport** gateway (o ponto final de cluster) também é atualizado, adicionar uma rota para o sava: 1.1.0 recentemente implementado. Neste momento, não existe tráfego é encaminhado aqui (a **peso** está definido como 0%).

![Vamp da interface do Usuário - gateway do cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Versão canary

Com as duas versões do sava implementadas no mesmo cluster, ajustar a distribuição do tráfego entre elas ao mover o **peso** controlo de deslize.

1. Clique em ![Vamp interface do Usuário - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) junto a **peso**.

2. Defina a distribuição da ponderação como 50% 50% e clique em **guardar**.

  ![Vamp da interface do Usuário - controlo de deslize de peso de gateway](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Volte ao seu navegador e atualize a página de sava mais algumas vezes. A aplicação de sava agora muda entre uma página de sava: 1.0 e uma página de sava: 1.1.

  ![alternando serviços sava1.0 e sava1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Este alternation da página funciona melhor com o "Incognito" ou "Anónimo" modo de seu navegador devido a colocação em cache de elementos estáticos.
  >

### <a name="filter-traffic"></a>Filtrar o tráfego

Suponha que após a implementação que detetar uma incompatibilidade em sava: 1.1.0, que faz com que problemas de exibição em navegadores Firefox. Pode definir Vamp para filtrar o tráfego de entrada e direcionar que todos os usuários do Firefox de volta para o conhecido sava: 1.0.0 estável. Este filtro instantaneamente resolve a interrupção para os usuários do Firefox, enquanto todas as outras pessoas continua a desfrutar dos benefícios sava: 1.1.0 melhorada.

Vamp utiliza **condições** para filtrar o tráfego entre as rotas de um gateway. Tráfego em primeiro lugar é filtrado e direcionado, de acordo com as condições aplicadas a cada rota. Todo o restante tráfego é distribuído, de acordo com a definição de peso de gateway.

Pode criar uma condição para filtrar todos os usuários do Firefox e direcioná-lo para o antigo sava: 1.0.0:

1. No sava/sava_cluster/webport **Gateways** página, clique em ![Vamp interface do Usuário - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para adicionar um **condição** para sava/sava_cluster/sava:1.0.0/webport a rota. 

2. Introduza a condição **agente do usuário = = Firefox** e clique em ![Vamp IU – guardar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

  Vamp adiciona da condição com uma força de padrão de 0%. Para começar a filtrar o tráfego, terá de ajustar a força da condição.

3. Clique em ![Vamp interface do Usuário - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para alterar a **força** aplicadas à condição.
 
4. Definir o **força** para 100% e clique em ![Vamp IU – guardar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) para guardar.

  Vamp agora envia o tráfego que correspondem à condição (todos os usuários do Firefox) para sava: 1.0.0.

  ![Vamp da interface do Usuário - aplicar condição ao gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Por último, ajuste o peso de gateway para enviar todo o tráfego restante (todos os utilizadores não Firefox) para o novo sava: 1.1.0. Clique em ![Vamp interface do Usuário - editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) junto a **peso** e defina a distribuição da ponderação, para 100% é direcionado para a rota sava/sava_cluster/sava:1.1.0/webport.

  Todo o tráfego não filtrado pela condição agora é direcionado para o novo sava: 1.1.0.

6. Para ver o filtro em ação, abra dois diferentes navegadores (um Firefox e um outro navegador) e aceder ao serviço de sava de ambos. Todos os pedidos do Firefox são enviados para sava: 1.0.0, enquanto todos os outros navegadores são direcionados para sava: 1.1.0.

  ![Interface do Usuário - filtrar o tráfego de vamp](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Resumindo

Este artigo foi uma breve introdução à Vamp num cluster DC/OS. Para os iniciantes, obteve Vamp cópia de segurança e em execução no seu Azure DC/OS Container Service cluster, implementar um serviço com um esquema de Vamp e acedidos-lo com o ponto de extremidade exposto (gateway).

Também abordamos alguns recursos poderosos do Vamp: a intercalação de uma nova variante de serviço para a implementação em execução e introduzi-lo de forma incremental, em seguida, a filtragem de tráfego para resolver a uma incompatibilidade conhecida.


## <a name="next-steps"></a>Passos Seguintes

* Saiba mais sobre a gestão de ações de Vamp através da [Vamp API de REST](https://vamp.io/documentation/api/api-reference/).

* Criar scripts de automatização de Vamp em node. js e executá-los como [Vamp fluxos de trabalho](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* Consulte adicional [VAMP tutoriais](https://vamp.io/documentation/tutorials/).

