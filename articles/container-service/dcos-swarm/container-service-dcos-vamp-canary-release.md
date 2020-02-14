---
title: PRETERIDO Versão canário com a edição no cluster de DC/so do Azure
description: Como usar o modelar para os serviços de versão canário e aplicar a filtragem de tráfego inteligente em um cluster DC/so do serviço de contêiner do Azure
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 2af20a1ddf4239b7eec6cceabf2ff9711959c128
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/13/2020
ms.locfileid: "77189115"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>PRETERIDO Canário liberar os microserviços com uma nova edição em um cluster DC/OS do serviço de contêiner do Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste tutorial, configuramos a orientação sobre o serviço de contêiner do Azure com um cluster de DC/so. Nós canário liberamos o serviço de demonstração de modelação "Sava" e, em seguida, resolvemos uma incompatibilidade do serviço com o Firefox aplicando a filtragem de tráfego inteligente. 

> [!TIP] 
> Nessas orientações, as ações são executadas em um cluster DC/OS, mas você também pode usar a orientação com kubernetes como o orquestrador.
>

## <a name="about-canary-releases-and-vamp"></a>Sobre as versões de canário e a nova


[A libertação de canários](https://martinfowler.com/bliki/CanaryRelease.html) é uma estratégia de implementação inteligente adotada por organizações inovadoras como netflix, Facebook e Spotify. Trata-se de uma abordagem que faz sentido, pois reduz problemas, apresenta redes de segurança e aumenta a inovação. Então, por que as empresas não estão usando isso? Estender um pipeline de CI/CD para incluir estratégias de canário adiciona complexidade e requer conhecimento e experiência de DevOps extensivos. Isso é suficiente para bloquear empresas menores e empresas antes mesmo de começar. 

[Vamp](https://vamp.io/) é um sistema de código aberto projetado para facilitar esta transição e trazer funcionalidades de libertação canária ao seu programador de contentores preferido. A funcionalidade canário de uma nova modela vai além das distribuições baseadas em porcentagem. O tráfego pode ser filtrado e dividido em uma ampla gama de condições, por exemplo, para direcionar usuários específicos, intervalos de IP ou dispositivos. Acompanhe as faixas e analise as métricas de desempenho, permitindo a automação baseada em dados do mundo real. Você pode configurar a reversão automática em erros ou dimensionar variantes de serviço individuais com base na carga ou latência.

## <a name="set-up-azure-container-service-with-dcos"></a>Configurar o serviço de contêiner do Azure com o DC/so



1. [Implementar um cluster DC/OS](container-service-deployment.md) com um mestre e dois agentes de tamanho padrão. 

2. [Crie um túnel SSH](../container-service-connect.md) para ligar ao cluster DC/OS. Este artigo pressupõe que você túnel para o cluster na porta local 80.


## <a name="set-up-vamp"></a>Configurar a remodelação

Agora que tem um cluster DC/OS em execução, pode instalar o Vamp a partir do DC/OS UI (http:\//localhost:80). 

![IU do DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

A instalação é feita em dois estágios:

1. **Implementar elástico**.

2. Em seguida, **implemente o Vamp** instalando o pacote do universo Vamp DC/OS.

### <a name="deploy-elasticsearch"></a>Implantar Elasticsearch

A remodelação requer Elasticsearch para coleta e agregação de métricas. Pode usar as [imagens magneticio Docker](https://hub.docker.com/r/magneticio/elastic/) para implementar uma pilha de pesquisa de vamp elástico compatível.

1. Na UI DC/OS, vá aos **Serviços** e clique em **Serviço de Implantação**.

2. Selecione o **modo JSON** a partir do pop-up **Deploy New Service.**

   ![Selecionar modo JSON](./media/container-service-dcos-vamp-canary-release/02_deploy_service_json_mode.png)

3. Cole o JSON a seguir. Essa configuração executa o contêiner com 1 GB de RAM e uma verificação de integridade básica na porta Elasticsearch.
  
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
  

3. Clique em **Implementar**.

   O DC/OS implanta o contêiner Elasticsearch. Pode acompanhar o progresso na página **dos Serviços.**  

   ![implantar e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Implantar a nova

Uma vez que a Pesquisa Elástica reporte como **Running,** pode adicionar o pacote Vamp DC/OS Universe. 

1. Vá ao **Universo** e procure **vampiros.** 
   ![Vamp no universo DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Clique em **instalar** ao lado da embalagem vamp e escolha **instalação avançada**.

3. Desloque-se para baixo e introduza o seguinte elástico url de pesquisa: `http://elasticsearch.marathon.mesos:9200`. 

   ![Insira a URL do Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Clique em **Rever e Instalar**e, em seguida, clique em **Instalar** para iniciar a implementação.  

   O DC/OS implanta todos os componentes de remodelação necessários. Pode acompanhar o progresso na página **dos Serviços.**
  
   ![Implantar uma nova planta como um pacote do universo](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Depois que a implantação for concluída, você poderá acessar a interface do usuário da remodelação:

   ![Serviço de remodelação no DC/so](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![IU de remodelação](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Implantar seu primeiro serviço

Agora que a figura está em funcionamento, implante um serviço de um plano gráfico. 

Na sua forma mais simples, um [projeto vamp](https://docs.vamp.io/how-vamp-works/vamp-and-kubernetes#vamp-deployments) descreve os pontos finais (gateways), clusters e serviços para implantar. A remodelação usa clusters para agrupar variantes diferentes do mesmo serviço em grupos lógicos para A liberação de canário ou teste A/B.  

Este cenário utiliza uma aplicação monolítica de amostra chamada [**sava**](https://github.com/magneticio/sava), que está na versão 1.0. O monolítico é empacotado em um contêiner do Docker, que está no Hub do Docker em magneticio/Sava: 1.0.0. O aplicativo normalmente é executado na porta 8080, mas você deseja expô-lo na porta 9050 nesse caso. Implante o aplicativo por meio de uma nova estrutura usando um plano gráfico simples.

1. Ir a **Destacamentos.**

2. Clique em **Adicionar**.

3. Cole o YAML Blueprint a seguir. Este projeto contém um cluster com apenas uma variante de serviço, que mudamos em uma etapa posterior:

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

4. Clique em **Guardar**. A remodelação inicia a implantação.

A implementação está listada na página **de Implantações.** Clique na implantação para monitorar seu status.

![Interface do usuário de remodelação – implantando Sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![serviço Sava na interface do usuário de remodelação](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

São criados dois gateways, que estão listados na página **Gateways:**

* um ponto de extremidade estável para acessar o serviço em execução (porta 9050) 
* um gateway interno com gerenciamento de Nova modela (mais informações sobre esse gateway mais tarde). 

![Remodelar interface do usuário – gateways Sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

O serviço Sava agora foi implantado, mas você não pode acessá-lo externamente porque o Azure Load Balancer não sabe encaminhar o tráfego para ele ainda. Para acessar o serviço, atualize a configuração de rede do Azure.


## <a name="update-the-azure-network-configuration"></a>Atualizar a configuração de rede do Azure

A nova implantação implantou o serviço Sava nos nós do agente DC/so, expondo um ponto de extremidade estável na porta 9050. Para acessar o serviço de fora do cluster DC/OS, faça as seguintes alterações na configuração de rede do Azure na implantação do cluster: 

1. **Configure o Balancer** de Carga Azure para os agentes (o recurso chamado **dcos-agent-lb-xxxx**) com uma sonda de saúde e uma regra para encaminhar o tráfego na porta 9050 para os casos de sava. 

2. **Atualize o grupo** de segurança da rede para os agentes públicos (o recurso chamado **XXXX-agent-public-nsg-XXXX**) para permitir o tráfego no porto 9050.

Para que as diligências detalhadas completem estas tarefas utilizando o portal Azure, consulte Permitir o acesso do público a uma aplicação do Serviço de [Contentores Azure.](container-service-enable-public-access.md) Especifique a porta 9050 para todas as configurações de porta.


Uma vez que tudo tenha sido criado, vá à lâmina de **visão geral** do equilibrador de carga do agente DC/OS (o recurso chamado **dcos-agent-lb-xxxx**). Encontre o **endereço IP público**e utilize o endereço para aceder ao sava no porto 9050.

![Portal do Azure-obter endereço IP público](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Executar uma versão do canário

Suponha que você tenha uma nova versão deste aplicativo que deseja canário a liberação para produção. Você o tem em contêineres como magneticio/Sava: 1.1.0 e está pronto para começar. A nova forma permite que você adicione facilmente novos serviços à implantação em execução. Estes serviços "fundidos" são implantados juntamente com os serviços existentes no cluster, e atribuíram um peso de 0%. Nenhum tráfego é roteado para um serviço mesclado recentemente até que você ajuste a distribuição de tráfego. O controle deslizante peso na interface do usuário de remodelação fornece a você controle total sobre a distribuição, permitindo ajustes incrementais (versão canário) ou uma reversão instantânea.

### <a name="merge-a-new-service-variant"></a>Mesclar uma nova variante de serviço

Para mesclar o novo serviço Sava 1,1 com a implantação em execução:

1. Na UI Vamp, clique em **Plantas**.

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

4. Abra o menu de ação na planta sava:1.1 e clique em **Fundir para**.

   ![Interface do usuário de remodelação-plantas](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecione a implementação da **sava** e clique em **Fundir**.

   ![Interface do usuário de remodelação-gráfico de mesclagem para implantação](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

Vamp implementa a nova variante de serviço sava:1.1.0 descrita na planta ao lado de sava:1.0.0 no **sava_cluster** da implantação em execução. 

![Interface do usuário de remodelação-implantação de Sava atualizada](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

O gateway **sava/sava_cluster/webport** (o ponto final do cluster) também é atualizado, adicionando uma rota para o recém-implantado sava:1.1.0. Neste ponto, não há tráfego aqui (o **PESO** está fixado em 0%).

![Interface do usuário de remodelação-gateway de cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Versão canário

Com ambas as versões de sava implantadas no mesmo cluster, ajuste a distribuição de tráfego entre elas movendo o slider **WEIGHT.**

1. Clique ![Vamp UI - edite](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado do **PESO**.

2. Detete a distribuição de peso para 50%/50% e clique em **Guardar**.

   ![IU de remodelação-controle deslizante de peso](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Volte para o navegador e atualize a página Sava mais algumas vezes. O aplicativo Sava agora alterna entre uma página Sava: 1.0 e uma página Sava: 1.1.

   ![alternando os serviços Sava 1.0 e Sava 1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Essa alternância da página funciona melhor com o modo "Incognito" ou "anônimo" do seu navegador devido ao cache de ativos estáticos.
  >

### <a name="filter-traffic"></a>Filtrar tráfego

Suponha que após a implantação você descobriu uma incompatibilidade no Sava: 1.1.0 que causa problemas de exibição em navegadores Firefox. Você pode definir uma nova medida para filtrar o tráfego de entrada e direcionar todos os usuários do Firefox para o Sava estável conhecido: 1.0.0. Esse filtro resolve instantaneamente a interrupção dos usuários do Firefox, enquanto todos os outros continuam a aproveitar os benefícios do Sava: 1.1.0 aprimorado.

Vamp usa **condições** para filtrar o tráfego entre as rotas de um portal. O tráfego é primeiro filtrado e direcionado de acordo com as condições aplicadas a cada rota. Todo o tráfego restante é distribuído de acordo com a configuração de peso do gateway.

Você pode criar uma condição para filtrar todos os usuários do Firefox e direcioná-los para o antigo Sava: 1.0.0:

1. Na página sava/sava_cluster/webport **Gateways,** clique ![Vamp UI - edite](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para adicionar uma **CONDIÇÃO** à rota sava/sava_cluster/sava:1.0.0/webport. 

2. Introduza a condição **de agente utilizador == Firefox** e clique em ![Vamp UI - guarde](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   Vamp adiciona a condição com uma força padrão de 0%. Para iniciar a filtragem de tráfego, você precisa ajustar a intensidade da condição.

3. Clique ![Vamp UI - edite](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para alterar a **STRENGTH** aplicada à circunstância.
 
4. Desloque a **FORÇA** a 100% e clique ![Vamp UI - poupe](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) para guardar.

   A remodelação agora envia todo o tráfego que corresponde à condição (todos os usuários do Firefox) para Sava: 1.0.0.

   ![Interface do usuário de remodelação – aplicar condição ao gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Por fim, ajuste o peso do gateway para enviar todo o tráfego restante (todos os usuários que não são do Firefox) para o novo Sava: 1.1.0. Clique ![Vamp UI - edite](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado do **WEIGHT** e detete a distribuição de peso para que 100% seja direcionado para a rota sava/sava_cluster/sava:1.1.0/webport.

   Todo o tráfego não filtrado pela condição agora é direcionado para o novo Sava: 1.1.0.

6. Para ver o filtro em ação, abra dois navegadores diferentes (um Firefox e outro navegador) e acesse o serviço Sava de ambos. Todas as solicitações do Firefox são enviadas para Sava: 1.0.0, enquanto todos os outros navegadores são direcionados para Sava: 1.1.0.

   ![Interface do usuário de remodelação-tráfego de filtro](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Somando

Este artigo foi uma breve introdução à remodelação em um cluster de DC/so. Para iniciantes, você já se resumiu e em execução no seu cluster DC/OS do serviço de contêiner do Azure, implantou um serviço com um plano gráfico de remodelação e o acessava no ponto de extremidade exposto (gateway).

Também abordamos alguns recursos poderosos de remodelação: mesclando uma nova variante de serviço à implantação em execução e introduzindo-a de forma incremental e filtrando o tráfego para resolver uma incompatibilidade conhecida.


## <a name="next-steps"></a>Passos seguintes

* Aprenda sobre a gestão das ações do Vamp através da [API Vamp REST](https://docs.vamp.io/how-vamp-works/events-and-metrics#events).

* Construa scripts de automação vamp em Node.js e execute-os como fluxos de [trabalho vamp](https://docs.vamp.io/how-vamp-works/concepts-and-components#workflows).

* Consulte [tutoriais VAMP](https://docs.vamp.io/tutorials/)adicionais .

