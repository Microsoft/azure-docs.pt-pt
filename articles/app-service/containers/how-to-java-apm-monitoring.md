---
title: Configurar o Java APM e ferramentas no Linux - serviço de aplicações do Azure de monitorização
description: Saiba como enviar os registos e métricas para as suas aplicações de Java em execução no App Service do Linux para fornecedores NewRelic e aplicação Dynamics APM
services: app-service\web
author: rloutlaw
manager: angerobe
ms.service: app-service-web
ms.workload: web
ms.topic: article
ms.date: 03/21/2019
ms.author: astay;routlaw
ms.custom: seodec18
ms.openlocfilehash: e6a22258266bda18c9ff79590d88e70d512f6c77
ms.sourcegitcommit: 956749f17569a55bcafba95aef9abcbb345eb929
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2019
ms.locfileid: "58630147"
---
# <a name="how-to-application-performance-monitoring-tools-with-java-apps-on-azure-app-service-on-linux"></a>Procedimento: Ferramentas com aplicações Java no serviço de aplicações do Azure no Linux de monitorização do desempenho de aplicações

Este artigo mostra como ligar aplicações de Java implementadas no serviço de aplicações do Azure no Linux com o desempenho da aplicação da NewRelic e AppDynamics monitorização plataformas (APM).

## <a name="configure-new-relic"></a>Configurar o New Relic

1. Criar uma conta da NewRelic no [NewRelic.com](https://newrelic.com/signup)
2. Transferir o agente Java a partir da NewRelic, ele terá um nome de ficheiro semelhante a `newrelic-java-x.x.x.zip`.
3. Copiar a chave de licença, precisará das mesmas para configurar o agente mais tarde.
4. [SSH para a instância de serviço de aplicações](/azure/app-service/containers/app-service-linux-ssh-support) e criar um novo diretório `/home/site/wwwroot/apm`.
5. Carregar os ficheiros de agente NewRelic Java descompactados para um diretório na `/home/site/wwwroot/apm`. Os ficheiros para o agente devem estar no `/home/site/wwwroot/apm/newrelic`.
6. Modificar o ficheiro YAML em `/home/site/wwwroot/apm/newrelic/newrelic.yml` e substitua o valor de licença de marcador de posição pela sua chave de licença.
7. No portal do Azure, navegue para a sua aplicação no serviço de aplicações e criar uma nova definição de aplicação.
    - Se seu aplicativo está usando **Java SE**, criar uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se estiver a utilizar **Tomcat**, criar uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/newrelic/newrelic.jar`.
    - Se estiver a utilizar **WildFly**, consulte a documentação do New Relic [aqui](https://docs.newrelic.com/docs/agents/java-agent/additional-installation/wildfly-version-11-installation-java) para obter orientações sobre como instalar o agente Java e a configuração de JBoss.
    - Se já tiver uma variável de ambiente para `JAVA_OPTS` ou `CATALINA_OPTS`, acrescentar o `javaagent` opção ao final do valor atual.

## <a name="configure-appdynamics"></a>Configurar o AppDynamics

1. Criar uma conta do AppDynamics no [AppDynamics.com](https://www.appdynamics.com/community/register/)
1. Transferir o agente de Java a partir do site do AppDynamics, o nome do ficheiro será semelhante a `AppServerAgent-x.x.x.xxxxx.zip`
1. [SSH para a instância de serviço de aplicações](/azure/app-service/containers/app-service-linux-ssh-support) e criar um novo diretório `/home/site/wwwroot/apm`.
1. Carregar os ficheiros de agente Java para um diretório na `/home/site/wwwroot/apm`. Os ficheiros para o agente devem estar no `/home/site/wwwroot/apm/appdynamics`.
1. No portal do Azure, navegue para a sua aplicação no serviço de aplicações e criar uma nova definição de aplicação.
    - Se estiver a utilizar **Java SE**, criar uma variável de ambiente chamada `JAVA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` onde `<YOUR_SITE_NAME>` é o seu nome de serviço de aplicações.
    - Se estiver a utilizar **Tomcat**, criar uma variável de ambiente chamada `CATALINA_OPTS` com o valor `-javaagent:/home/site/wwwroot/apm/appdynamics/javaagent.jar -Dappdynamics.agent.applicationName=<YOUR_SITE_NAME>` onde `<YOUR_SITE_NAME>` é o seu nome de serviço de aplicações.
    - Se estiver a utilizar **WildFly**, consulte a documentação do AppDynamics [aqui](https://docs.appdynamics.com/display/PRO45/JBoss+and+Wildfly+Startup+Settings) para obter orientações sobre como instalar o agente Java e a configuração de JBoss.
