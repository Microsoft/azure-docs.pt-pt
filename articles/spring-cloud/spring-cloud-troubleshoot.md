---
title: Guia de resolução de problemas para Azure Spring Cloud | Microsoft Docs
description: Guia de resolução de problemas para Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 09/08/2020
ms.author: brendm
ms.custom: devx-track-java
ms.openlocfilehash: 7c4dbeae0cfb76063fdca70b3ad1d264b59f9ace
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "100634248"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Resolução de problemas problemas comuns da Nuvem de primavera de Azure

Este artigo fornece instruções para resolver problemas de desenvolvimento da Azure Spring Cloud. Para obter informações adicionais, consulte [a Azure Spring Cloud FAQ](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Questões de disponibilidade, desempenho e aplicação

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>A minha aplicação não pode arrancar (por exemplo, o ponto final não pode ser ligado, ou devolve um 502 após algumas retrósbos)

Exporte os registos para a Azure Log Analytics. A tabela para registos de aplicações primavera chama-se *AppPlatformLogsforSpring*. Para saber mais, consulte [os registos e métricas com as definições de diagnóstico](diagnostic-services.md).

A seguinte mensagem de erro pode aparecer nos seus registos:

> "org.springframework.context.ApplicationContextExcepção: Incapaz de iniciar o servidor web"

A mensagem indica um de dois problemas prováveis: 
* Falta um dos feijões ou uma das suas dependências.
* Uma das propriedades do bean está em falta ou é inválida. Neste caso, "java.lang.IllegalArgumentException" provavelmente será exibido.

As ligações de serviço também podem causar falhas no início da aplicação. Para consultar os registos, utilize palavras-chave relacionadas com os serviços vinculados. Por exemplo, vamos supor que a sua aplicação tem uma ligação a um caso MySQL que está definido para o tempo do sistema local. Se a aplicação não arrancar, poderá aparecer a seguinte mensagem de erro no registo:

> "java.sql.SQLExcepção: O valor do fuso horário do servidor 'Tempo Universal Coordenado' não é reconhecido ou representa mais de um fuso horário."

Para corrigir este erro, vá à `server parameters` sua instância MySQL e altere o `time_zone` valor de *SYSTEM* para *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>A minha aplicação falha ou emite um erro inesperado

Quando estiver a depurar falhas de aplicação, comece por verificar o estado de funcionamento e o estado de descoberta da aplicação. Para tal, vá à _gestão da App_ no portal Azure para garantir que os estatutos de todas as aplicações estão _Em Execução_ e _UP_.

* Se o estado estiver _em execução_ mas o estado de descoberta não for _UP,_ vá para a secção ["A minha candidatura não pode ser registada".](#my-application-cant-be-registered)

* Se o estado de descoberta for _UP,_ vá ao Metrics verificar a saúde da aplicação. Inspecione as seguintes métricas:


  - `TomcatErrorCount`_(tomcat.global.error):_ Todas as exceções à aplicação da primavera são contadas aqui. Se este número for grande, vá ao Azure Log Analytics para inspecionar os registos da sua aplicação.

  - `AppMemoryMax`_(jvm.memory.max):_ A quantidade máxima de memória disponível para a aplicação. A quantidade pode ser indefinida, ou pode mudar ao longo do tempo se for definida. Se for definida, a quantidade de memória usada e comprometida é sempre inferior ou igual ao máximo. No entanto, uma atribuição de memória pode falhar com uma `OutOfMemoryError` mensagem se a atribuição tentar aumentar a memória usada tal que *a utilização > cometida*, mesmo que usada <= *max* ainda é verdadeira. Em tal situação, tente aumentar o tamanho máximo da pilha usando o `-Xmx` parâmetro.

  - `AppMemoryUsed` (_jvm.memory.used):_ A quantidade de memória em bytes que é atualmente utilizada pela aplicação. Para uma aplicação java de carga normal, esta série métrica forma um padrão *de dentes de serra,* onde o uso da memória aumenta e diminui constantemente em pequenos incrementos e de repente cai muito, e então o padrão repete-se. Esta série métrica ocorre devido à recolha de lixo dentro da máquina virtual java, onde as ações de recolha representam gotas no padrão sawtooth.
    
    Esta métrica é importante para ajudar a identificar problemas de memória, tais como:
    * Uma explosão de memória no início.
    * A alocação de memória de pico para um caminho lógico específico.
    * Fugas de memória graduais.
  Para mais informações, consulte [Métricas.](spring-cloud-concept-metrics.md)
  
* Se a aplicação não arrancar, verifique se a aplicação tem parâmetros jvm válidos. Se a memória jvm estiver demasiado alta, pode aparecer nos registos a seguinte mensagem de erro:

  >"memória necessária 2728741K é superior a 2000M disponível para alocação"



Para saber mais sobre o Azure Log Analytics, consulte [Começar com o Log Analytics no Azure Monitor.](../azure-monitor/logs/log-analytics-tutorial.md)

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>A minha aplicação tem uma elevada utilização da CPU ou da memória

Se a sua aplicação experimentar um elevado uso de CPU ou memória, uma de duas coisas é verdade:
* Todas as instâncias de aplicação experimentam alta cpu ou uso de memória.
* Algumas das instâncias da aplicação experimentam um elevado uso de CPU ou memória.

Para apurar qual a situação aplicável, faça o seguinte:

1. Vá a **Métricas** e, em seguida, selecione **a percentagem de utilização do CPU de serviço** ou a memória de serviço **utilizada**.
2. Adicione um filtro **App=** para especificar qual a aplicação que pretende monitorizar.
3. Divida as métricas por **exemplo.**

Se *todas as instâncias* estiverem a experimentar uma elevada utilização de CPU ou memória, é necessário aumentar a aplicação ou aumentar o uso do CPU ou da memória. Para mais informações, consulte [Tutorial: Escalar uma aplicação em Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Se *algumas situações* estiverem a passar por um elevado uso de CPU ou memória, verifique o estado da instância e o seu estado de descoberta.

Para obter mais informações, consulte [métricas para Azure Spring Cloud](spring-cloud-concept-metrics.md).

Se todas as instâncias estiverem em funcionamento, vá ao Azure Log Analytics para consultar os registos da sua aplicação e rever a sua lógica de código. Isto irá ajudá-lo a ver se algum deles pode afetar a divisória em escala. Para obter mais informações, consulte [os registos e métricas com as definições de diagnóstico](diagnostic-services.md).

Para saber mais sobre o Azure Log Analytics, consulte [Começar com o Log Analytics no Azure Monitor.](../azure-monitor/logs/log-analytics-tutorial.md) Consultar os registos utilizando o [idioma de consulta kusto](/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Lista de verificação para implementar a sua aplicação primavera para Azure Spring Cloud

Antes de embarcar na sua aplicação, certifique-se de que cumpre os seguintes critérios:

* A aplicação pode ser executada localmente com a versão de tempo de execução de Java especificada.
* A config ambiental (CPU/RAM/Instâncias) cumpre o requisito mínimo estabelecido pelo fornecedor de aplicações.
* Os itens de configuração têm os seus valores esperados. Para mais informações, consulte [o Config Server](spring-cloud-tutorial-config-server.md).
* As variáveis ambientais têm os seus valores esperados.
* Os parâmetros JVM têm os seus valores esperados.
* Recomendamos que desative ou remova os serviços de registo de _serviços Config_ e _Serviço de Mola_ incorporados do pacote de aplicações.
* Se os recursos do Azure forem vinculados através do _Vínculo de Serviços_, confirme que os recursos de destino estão a funcionar.

## <a name="configuration-and-management"></a>Configuração e gestão

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Deparei-me com um problema na criação de um caso de serviço Azure Spring Cloud

Quando configura uma instância de serviço Azure Spring Cloud utilizando o portal Azure, a Azure Spring Cloud executa a validação para si.

Mas se tentar configurar a instância de serviço Azure Spring Cloud utilizando o [modelo Azure CLI](/cli/azure/get-started-with-azure-cli) ou O [Gestor de Recursos Azure,](../azure-resource-manager/index.yml)verifique se:

* A subscrição está ativa.
* A localização é [suportada](spring-cloud-faq.md) por Azure Spring Cloud.
* O grupo de recursos, por exemplo, já está criado.
* O nome do recurso está em conformidade com a regra de nomeação. Deve conter apenas letras minúsculas, números e hífenes. O primeiro caráter tem de ser uma letra. O último caráter tem de ser uma letra ou um número. O valor deve conter de 2 a 32 caracteres.

Se pretender configurar a instância de serviço Azure Spring Cloud utilizando o modelo de Gestor de Recursos, consulte primeiro para [Compreender a estrutura e sintaxe dos modelos do Gestor de Recursos Azure](../azure-resource-manager/templates/template-syntax.md).

O nome da instância de serviço Azure Spring Cloud será usado para solicitar um nome de subdomínio sob `azureapps.io` , pelo que a configuração falhará se o nome entrar em conflito com um existente. Pode encontrar mais detalhes nos registos de atividades.

### <a name="i-cant-deploy-a-net-core-app"></a>Não posso implementar uma aplicação .NET Core

Não é possível carregar um ficheiro *.zip* para uma aplicação .NET Core Steeltoe utilizando o portal Azure ou o modelo de Gestor de Recursos.

Quando implementa o seu pacote de aplicações utilizando o [Azure CLI,](/cli/azure/get-started-with-azure-cli)o CLI Azure sonda periodicamente o progresso da implantação e, no final, apresenta o resultado da implantação.

Certifique-se de que a sua aplicação está embalada no formato de ficheiro *.zip* correto. Se não estiver embalado corretamente, o processo deixará de responder ou receberá uma mensagem de erro.

### <a name="i-cant-deploy-a-jar-package"></a>Não posso implantar um pacote JAR.

Não é possível carregar o ficheiro Java Archive (JAR)/pacote de origem utilizando o portal Azure ou o modelo de Gestor de Recursos.

Quando implementa o seu pacote de aplicações utilizando o [Azure CLI,](/cli/azure/get-started-with-azure-cli)o CLI Azure sonda periodicamente o progresso da implantação e, no final, apresenta o resultado da implantação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

Certifique-se de que a sua aplicação está embalada no [formato JAR executável](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)correto . Se não estiver embalado corretamente, receberá uma mensagem de erro semelhante à seguinte:

> "Erro: Jarfile inválido ou corrupto /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Não posso implantar um pacote de origem.

Não é possível carregar o pacote JAR/source utilizando o portal Azure ou o modelo de Gestor de Recursos.

Quando implementa o seu pacote de aplicações utilizando o [Azure CLI,](/cli/azure/get-started-with-azure-cli)o CLI Azure sonda periodicamente o progresso da implantação e, no final, apresenta o resultado da implantação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter e construir os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

No entanto, note que uma instância de serviço Azure Spring Cloud pode desencadear apenas um trabalho de construção para um pacote de origem de uma vez. Para obter mais informações, consulte [implementar uma aplicação](spring-cloud-quickstart.md) e [configurar um ambiente de preparação na Nuvem de primavera de Azure.](spring-cloud-howto-staging-environment.md)

### <a name="my-application-cant-be-registered"></a>O meu pedido não pode ser registado.

Na maioria dos casos, esta situação ocorre quando *as Dependências Necessárias* e *a Descoberta de Serviço* não estão devidamente configuradas no seu ficheiro Project Object Model (POM). Uma vez configurado, o ponto final do servidor de registo de serviço incorporado é injetado como uma variável ambiental com a sua aplicação. Em seguida, as aplicações registam-se no servidor do Registo de Serviços e descobrem outros microserviços dependentes.

Espere pelo menos dois minutos antes que uma instância recém-registada comece a receber tráfego.

Se estiver a migrar uma solução baseada em Nuvem de primavera para o Azure, certifique-se de que as suas instâncias de Registo de _Serviço_ ad-hoc e _Config Server_ são removidas (ou desativadas) para evitar conflitos com as instâncias geridas fornecidas pela Azure Spring Cloud.

Também pode verificar os registos de clientes _do Registo de Serviço_ no Azure Log Analytics. Para obter mais informações, consulte [os registos e métricas de análise com as definições de diagnóstico](diagnostic-services.md)

Para saber mais sobre o Azure Log Analytics, consulte [Começar com o Log Analytics no Azure Monitor.](../azure-monitor/logs/log-analytics-tutorial.md) Consultar os registos utilizando o [idioma de consulta kusto](/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Quero inspecionar as variáveis ambientais da minha aplicação

As variáveis ambientais informam o quadro Azure Spring Cloud, garantindo que a Azure compreende onde e como configurar os serviços que compõem a sua aplicação. Garantir que as variáveis ambientais estão corretas é um primeiro passo necessário para resolver problemas potenciais.  Pode utilizar o ponto final do Actuador de Botas de Mola para rever as variáveis ambientais.  

> [!WARNING]
> Este procedimento expõe as variáveis ambientais utilizando o seu ponto final de teste.  Não prossiga se o seu ponto final de teste estiver acessível ao público ou se tiver atribuído um nome de domínio à sua aplicação.

1. Aceda a `https://<your application test endpoint>/actuator/health`.  
    - Uma resposta semelhante indica `{"status":"UP"}` que o ponto final foi ativado.
    - Se a resposta for negativa, inclua a seguinte dependência no seu ficheiro *POM.xml:*

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Com o ponto final do Actuador de Arranque de Mola ativado, vá ao portal Azure e procure a página de configuração da sua aplicação.  Adicione uma variável ambiental com o nome `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` e o `*` valor. 

1. Reinicie a sua candidatura.

1. Vá `https://<your application test endpoint>/actuator/env` e inspecione a resposta.  Deverá ter o seguinte aspeto:

    ```json
    {
        "activeProfiles": [],
        "propertySources": {,
            "name": "server.ports",
            "properties": {
                "local.server.port": {
                    "value": 1025
                }
            }
        }
    }
    ```

Procure o nó da criança chamado `systemEnvironment` .  Este nó contém variáveis ambientais da sua aplicação.

> [!IMPORTANT]
> Lembre-se de inverter a exposição das variáveis ambientais antes de tornar a sua aplicação acessível ao público.  Vá ao portal Azure, procure a página de configuração da sua aplicação e elimine esta variável ambiental:  `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Não consigo encontrar métricas ou registos para a minha candidatura.

Vá à **gestão da App** para garantir que os status da aplicação estão _a correr_ e _a SUBIR._

Verifique se o _JMX_ está ativado no seu pacote de aplicações. Esta funcionalidade pode ser ativada com a propriedade de `spring.jmx.enabled=true` configuração.  

Verifique se a `spring-boot-actuator` dependência está ativada no seu pacote de aplicações e se é bem sucedida.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se os registos da sua aplicação puderem ser arquivados numa conta de armazenamento, mas não enviados para o Azure Log Analytics, verifique se [configura corretamente o seu espaço de trabalho](../azure-monitor/logs/quick-create-workspace.md). Se estiver a utilizar um nível gratuito de Azure Log Analytics, note que [o nível gratuito não fornece um acordo de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).

## <a name="next-steps"></a>Passos seguintes

* [Como auto-diagnosticar e resolver problemas na Nuvem de primavera de Azure](spring-cloud-howto-self-diagnose-solve.md)
