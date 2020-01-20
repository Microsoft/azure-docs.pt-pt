---
title: PRETERIDO Versão canário com a edição no cluster de DC/so do Azure
description: Como usar o modelar para os serviços de versão canário e aplicar a filtragem de tráfego inteligente em um cluster DC/so do serviço de contêiner do Azure
author: gggina
ms.service: container-service
ms.topic: conceptual
ms.date: 04/17/2017
ms.author: rasquill
ms.custom: mvc
ms.openlocfilehash: 60ff148e044df81e64b54fc48c1cb6f67aee14df
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76275667"
---
# <a name="deprecated-canary-release-microservices-with-vamp-on-an-azure-container-service-dcos-cluster"></a>PRETERIDO Canário liberar os microserviços com uma nova edição em um cluster DC/OS do serviço de contêiner do Azure

[!INCLUDE [ACS deprecation](../../../includes/container-service-deprecation.md)]

Neste tutorial, configuramos a orientação sobre o serviço de contêiner do Azure com um cluster de DC/so. Nós canário liberamos o serviço de demonstração de modelação "Sava" e, em seguida, resolvemos uma incompatibilidade do serviço com o Firefox aplicando a filtragem de tráfego inteligente. 

> [!TIP] 
> Nessas orientações, as ações são executadas em um cluster DC/OS, mas você também pode usar a orientação com kubernetes como o orquestrador.
>

## <a name="about-canary-releases-and-vamp"></a>Sobre as versões de canário e a nova


A [liberação do canário](https://martinfowler.com/bliki/CanaryRelease.html) é uma estratégia de implantação inteligente adotada por organizações inovadoras como Netflix, Facebook e Spotify. Trata-se de uma abordagem que faz sentido, pois reduz problemas, apresenta redes de segurança e aumenta a inovação. Então, por que as empresas não estão usando isso? Estender um pipeline de CI/CD para incluir estratégias de canário adiciona complexidade e requer conhecimento e experiência de DevOps extensivos. Isso é suficiente para bloquear empresas menores e empresas antes mesmo de começar. 

A [estrutura é um](https://vamp.io/) sistema de software livre projetado para facilitar essa transição e colocar os recursos de lançamento do canário em seu Agendador de contêiner preferido. A funcionalidade canário de uma nova modela vai além das distribuições baseadas em porcentagem. O tráfego pode ser filtrado e dividido em uma ampla gama de condições, por exemplo, para direcionar usuários específicos, intervalos de IP ou dispositivos. Acompanhe as faixas e analise as métricas de desempenho, permitindo a automação baseada em dados do mundo real. Você pode configurar a reversão automática em erros ou dimensionar variantes de serviço individuais com base na carga ou latência.

## <a name="set-up-azure-container-service-with-dcos"></a>Configurar o serviço de contêiner do Azure com o DC/so



1. [Implante um cluster DC/os](container-service-deployment.md) com um mestre e dois agentes de tamanho padrão. 

2. [Crie um túnel SSH](../container-service-connect.md) para se conectar ao cluster DC/os. Este artigo pressupõe que você túnel para o cluster na porta local 80.


## <a name="set-up-vamp"></a>Configurar a remodelação

Agora que você tem um cluster DC/OS em execução, é possível instalar o modestas da interface do usuário do DC/OS (http:\//localhost: 80). 

![IU do DC/OS](./media/container-service-dcos-vamp-canary-release/01_set_up_vamp.png)

A instalação é feita em dois estágios:

1. **Implantar Elasticsearch**.

2. Em seguida, **implante o remodelado** instalando o pacote do universo DC/so de modelação.

### <a name="deploy-elasticsearch"></a>Implantar Elasticsearch

A remodelação requer Elasticsearch para coleta e agregação de métricas. Você pode usar as [imagens do Docker magneticio](https://hub.docker.com/r/magneticio/elastic/) para implantar uma pilha Elasticsearch de remodelação compatível.

1. Na interface do usuário do DC/so, acesse **Serviços** e clique em **implantar serviço**.

2. Selecione **modo JSON** no pop-up **implantar novo serviço** .

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
  

3. Clique em **implementar**.

   O DC/OS implanta o contêiner Elasticsearch. Você pode acompanhar o progresso na página **Serviços** .  

   ![implantar e? Elasticsearch](./media/container-service-dcos-vamp-canary-release/03_deply_elasticsearch.png)

### <a name="deploy-vamp"></a>Implantar a nova

Depois que o Elasticsearch reportar como **em execução**, você poderá adicionar o pacote de universo DC/os de remodelação. 

1. Vá para o **universo** e pesquise por uma **nova**localização. 
   ![se remodelar no universo DC/OS](./media/container-service-dcos-vamp-canary-release/04_universe_deploy_vamp.png)

2. Clique em **instalar** ao lado do pacote de remodelação e escolha **instalação avançada**.

3. Role para baixo e insira o seguinte elasticsearch: `http://elasticsearch.marathon.mesos:9200`. 

   ![Insira a URL do Elasticsearch](./media/container-service-dcos-vamp-canary-release/05_universe_elasticsearch_url.png)

4. Clique em **revisar e instalar e**, em seguida, clique em **instalar** para iniciar a implantação.  

   O DC/OS implanta todos os componentes de remodelação necessários. Você pode acompanhar o progresso na página **Serviços** .
  
   ![Implantar uma nova planta como um pacote do universo](./media/container-service-dcos-vamp-canary-release/06_deploy_vamp.png)
  
5. Depois que a implantação for concluída, você poderá acessar a interface do usuário da remodelação:

   ![Serviço de remodelação no DC/so](./media/container-service-dcos-vamp-canary-release/07_deploy_vamp_complete.png)
  
   ![IU de remodelação](./media/container-service-dcos-vamp-canary-release/08_vamp_ui.png)


## <a name="deploy-your-first-service"></a>Implantar seu primeiro serviço

Agora que a figura está em funcionamento, implante um serviço de um plano gráfico. 

Em sua forma mais simples, um [plano gráfico de remodelação](https://vamp.io/documentation/using-vamp/blueprints/) descreve os pontos de extremidade (gateways), os clusters e os serviços a serem implantados. A remodelação usa clusters para agrupar variantes diferentes do mesmo serviço em grupos lógicos para A liberação de canário ou teste A/B.  

Esse cenário usa um aplicativo monolítico de exemplo chamado [**Sava**](https://github.com/magneticio/sava), que está na versão 1,0. O monolítico é empacotado em um contêiner do Docker, que está no Hub do Docker em magneticio/Sava: 1.0.0. O aplicativo normalmente é executado na porta 8080, mas você deseja expô-lo na porta 9050 nesse caso. Implante o aplicativo por meio de uma nova estrutura usando um plano gráfico simples.

1. Vá para **implantações**.

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

A implantação é listada na página **implantações** . Clique na implantação para monitorar seu status.

![Interface do usuário de remodelação – implantando Sava](./media/container-service-dcos-vamp-canary-release/09_sava100.png)

![serviço Sava na interface do usuário de remodelação](./media/container-service-dcos-vamp-canary-release/09a_sava100.png)

Dois gateways são criados, que são listados na página **gateways** :

* um ponto de extremidade estável para acessar o serviço em execução (porta 9050) 
* um gateway interno com gerenciamento de Nova modela (mais informações sobre esse gateway mais tarde). 

![Remodelar interface do usuário – gateways Sava](./media/container-service-dcos-vamp-canary-release/10_vamp_sava_gateways.png)

O serviço Sava agora foi implantado, mas você não pode acessá-lo externamente porque o Azure Load Balancer não sabe encaminhar o tráfego para ele ainda. Para acessar o serviço, atualize a configuração de rede do Azure.


## <a name="update-the-azure-network-configuration"></a>Atualizar a configuração de rede do Azure

A nova implantação implantou o serviço Sava nos nós do agente DC/so, expondo um ponto de extremidade estável na porta 9050. Para acessar o serviço de fora do cluster DC/OS, faça as seguintes alterações na configuração de rede do Azure na implantação do cluster: 

1. **Configure o Azure Load Balancer** para os agentes (o recurso chamado **DCOs-Agent-lb-xxxx**) com uma investigação de integridade e uma regra para encaminhar o tráfego na porta 9050 para as instâncias de Sava. 

2. **Atualize o grupo de segurança de rede** para os agentes públicos (o recurso chamado **xxxx-Agent-Public-NSG-xxxx**) para permitir o tráfego na porta 9050.

Para obter etapas detalhadas para concluir essas tarefas usando o portal do Azure, consulte [habilitar acesso público a um aplicativo do serviço de contêiner do Azure](container-service-enable-public-access.md). Especifique a porta 9050 para todas as configurações de porta.


Depois que tudo tiver sido criado, vá para a folha **visão geral** do balanceador de carga do agente DC/so (o recurso chamado **DCOs-Agent-lb-xxxx**). Localize o **endereço IP público**e use o endereço para acessar o Sava na porta 9050.

![Portal do Azure-obter endereço IP público](./media/container-service-dcos-vamp-canary-release/18_public_ip_address.png)

![sava](./media/container-service-dcos-vamp-canary-release/19_sava100.png)


## <a name="run-a-canary-release"></a>Executar uma versão do canário

Suponha que você tenha uma nova versão deste aplicativo que deseja canário a liberação para produção. Você o tem em contêineres como magneticio/Sava: 1.1.0 e está pronto para começar. A nova forma permite que você adicione facilmente novos serviços à implantação em execução. Esses serviços "mesclados" são implantados juntamente com os serviços existentes no cluster e atribuídos a um peso de 0%. Nenhum tráfego é roteado para um serviço mesclado recentemente até que você ajuste a distribuição de tráfego. O controle deslizante peso na interface do usuário de remodelação fornece a você controle total sobre a distribuição, permitindo ajustes incrementais (versão canário) ou uma reversão instantânea.

### <a name="merge-a-new-service-variant"></a>Mesclar uma nova variante de serviço

Para mesclar o novo serviço Sava 1,1 com a implantação em execução:

1. Na interface do usuário da remodelação, clique em **plantas**.

2. Clique em **Adicionar** e cole o seguinte YAML Blueprint: este projeto descreve uma nova variante de serviço (Sava: 1.1.0) a ser implantada no cluster existente (sava_cluster).

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
  
3. Clique em **Guardar**. O plano gráfico é armazenado e listado na página **plantas** .

4. Abra o menu Ação no plano gráfico Sava: 1.1 e clique em **Mesclar para**.

   ![Interface do usuário de remodelação-plantas](./media/container-service-dcos-vamp-canary-release/20_sava110_mergeto.png)

5. Selecione a implantação do **Sava** e clique em **mesclar**.

   ![Interface do usuário de remodelação-gráfico de mesclagem para implantação](./media/container-service-dcos-vamp-canary-release/21_sava110_merge.png)

A estrutura implanta a nova variante de serviço Sava: 1.1.0 descrita no Blueprint juntamente com Sava: 1.0.0 no **sava_cluster** da implantação em execução. 

![Interface do usuário de remodelação-implantação de Sava atualizada](./media/container-service-dcos-vamp-canary-release/22_sava_cluster.png)

O gateway **/webport Sava/sava_cluster** (o ponto de extremidade do cluster) também é atualizado, adicionando uma rota para o Sava: 1.1.0 implantado recentemente. Neste ponto, nenhum tráfego é roteado aqui (o **peso** é definido como 0%).

![Interface do usuário de remodelação-gateway de cluster](./media/container-service-dcos-vamp-canary-release/23_sava_cluster_webport.png)

### <a name="canary-release"></a>Versão canário

Com as duas versões do Sava implantadas no mesmo cluster, ajuste a distribuição do tráfego entre elas movendo o controle deslizante **peso** .

1. Clique em ![interface do usuário de remodelação – editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado de **peso**.

2. Defina a distribuição de peso para 50%/50% e clique em **salvar**.

   ![IU de remodelação-controle deslizante de peso](./media/container-service-dcos-vamp-canary-release/24_sava_cluster_webport_weight.png)

3. Volte para o navegador e atualize a página Sava mais algumas vezes. O aplicativo Sava agora alterna entre uma página Sava: 1.0 e uma página Sava: 1.1.

   ![alternando os serviços Sava 1.0 e Sava 1.1](./media/container-service-dcos-vamp-canary-release/25_sava_100_101.png)


  > [!NOTE]
  > Essa alternância da página funciona melhor com o modo "Incognito" ou "anônimo" do seu navegador devido ao cache de ativos estáticos.
  >

### <a name="filter-traffic"></a>Filtrar tráfego

Suponha que após a implantação você descobriu uma incompatibilidade no Sava: 1.1.0 que causa problemas de exibição em navegadores Firefox. Você pode definir uma nova medida para filtrar o tráfego de entrada e direcionar todos os usuários do Firefox para o Sava estável conhecido: 1.0.0. Esse filtro resolve instantaneamente a interrupção dos usuários do Firefox, enquanto todos os outros continuam a aproveitar os benefícios do Sava: 1.1.0 aprimorado.

A remodelação usa **condições** para filtrar o tráfego entre rotas em um gateway. O tráfego é primeiro filtrado e direcionado de acordo com as condições aplicadas a cada rota. Todo o tráfego restante é distribuído de acordo com a configuração de peso do gateway.

Você pode criar uma condição para filtrar todos os usuários do Firefox e direcioná-los para o antigo Sava: 1.0.0:

1. Na página **gateways** do sava/sava_cluster/webport, clique em ![interface do usuário de remodelação-editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para adicionar uma **condição** à rota Sava/sava_cluster/Sava: 1.0.0/webport. 

2. Insira a condição **User-Agent = = Firefox** e clique em ![interface do usuário de remodelar – salvar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png).

   Remodelar adiciona a condição com uma força padrão de 0%. Para iniciar a filtragem de tráfego, você precisa ajustar a intensidade da condição.

3. Clique em ![interface do usuário de remodelação – editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) para alterar a **força** aplicada à condição.
 
4. Defina a **força** como 100% e clique em ![interface do usuário de remodelação – salvar](./media/container-service-dcos-vamp-canary-release/vamp_ui_save.png) para salvar.

   A remodelação agora envia todo o tráfego que corresponde à condição (todos os usuários do Firefox) para Sava: 1.0.0.

   ![Interface do usuário de remodelação – aplicar condição ao gateway](./media/container-service-dcos-vamp-canary-release/26_apply_condition.png)

5. Por fim, ajuste o peso do gateway para enviar todo o tráfego restante (todos os usuários que não são do Firefox) para o novo Sava: 1.1.0. Clique em ![interface do usuário de remodelação – editar](./media/container-service-dcos-vamp-canary-release/vamp_ui_edit.png) ao lado de **peso** e defina a distribuição de peso de modo que 100% seja direcionado para a rota sava/sava_cluster/Sava: 1.1.0/webport.

   Todo o tráfego não filtrado pela condição agora é direcionado para o novo Sava: 1.1.0.

6. Para ver o filtro em ação, abra dois navegadores diferentes (um Firefox e outro navegador) e acesse o serviço Sava de ambos. Todas as solicitações do Firefox são enviadas para Sava: 1.0.0, enquanto todos os outros navegadores são direcionados para Sava: 1.1.0.

   ![Interface do usuário de remodelação-tráfego de filtro](./media/container-service-dcos-vamp-canary-release/27_filter_traffic.png)

## <a name="summing-up"></a>Somando

Este artigo foi uma breve introdução à remodelação em um cluster de DC/so. Para iniciantes, você já se resumiu e em execução no seu cluster DC/OS do serviço de contêiner do Azure, implantou um serviço com um plano gráfico de remodelação e o acessava no ponto de extremidade exposto (gateway).

Também abordamos alguns recursos poderosos de remodelação: mesclando uma nova variante de serviço à implantação em execução e introduzindo-a de forma incremental e filtrando o tráfego para resolver uma incompatibilidade conhecida.


## <a name="next-steps"></a>Passos seguintes

* Saiba como gerenciar ações de remodelação por meio da [API REST de Dimodelação](https://vamp.io/documentation/api/api-reference/).

* Crie scripts de automação de forma nova no node. js e execute-os como fluxos de trabalho de forma de [modelação](https://vamp.io/documentation/using-vamp/v1.0.0/workflows/#create-a-workflow).

* Consulte tutoriais adicionais de [remodelação](https://vamp.io/documentation/tutorials/).

