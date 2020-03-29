---
title: Guia de resolução de problemas para a Nuvem de primavera de Azure [ Microsoft Docs
description: Guia de resolução de problemas para nuvem de primavera azure
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "76277587"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Problemas problemas comum azure spring cloud problemas

Este artigo fornece instruções para problemas de resolução de problemas de desenvolvimento da Nuvem de primavera de Azure. Para mais informações, consulte [Azure Spring Cloud FAQ](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemas de disponibilidade, desempenho e aplicação

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>A minha aplicação não pode começar (por exemplo, o ponto final não pode ser ligado, ou devolve um 502 após algumas tentativas)

Exporte os registos para AnaLítica de Log Azure. A tabela para registos de aplicações da primavera chama-se *AppPlatformLogsforSpring*. Para saber mais, consulte [registos e métricas de análise com definições](diagnostic-services.md)de diagnóstico .

A seguinte mensagem de erro pode aparecer nos seus registos:

> "org.springframework.context.ApplicationContextException: Unable to start web server"

A mensagem indica um de dois problemas prováveis: 
* Falta um dos feijões ou uma das suas dependências.
* Uma das propriedades do bean está em falta ou é inválida. Neste caso, "java.lang.IllegalArgumentException" será provavelmente exibido.

As encadernações de serviço também podem causar falhas no início da aplicação. Para consultar os registos, utilize palavras-chave relacionadas com os serviços vinculados. Por exemplo, vamos assumir que a sua aplicação tem uma ligação a um caso MySQL que está definido para o tempo do sistema local. Se a aplicação não arrancar, a seguinte mensagem de erro pode aparecer no registo:

> "java.sql.SQLException: O valor do fuso horário do servidor 'Tempo Universal Coordenado' não é reconhecido ou representa mais de um fuso horário."

Para corrigir este erro, `server parameters` vá ao seu caso MySQL e altere o `time_zone` valor do *SISTEMA* para *+0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>A minha aplicação falha ou emite um erro inesperado

Quando estiver a depurar falhas na aplicação, comece por verificar o estado de execução e o estado de descoberta da aplicação. Para tal, vá à _gestão_ de Aplicações no portal Azure para garantir que os estados de todas as aplicações estão _em Execução_ e _UP_.

* Se o estado estiver _em execução_ mas o estado de descoberta não for _UP,_ vá para a secção ["A minha aplicação não pode ser registada".](#my-application-cant-be-registered)

* Se o estado de descoberta for _UP,_ vá às Métricas para verificar a saúde da aplicação. Inspecione as seguintes métricas:


  - `TomcatErrorCount`_(tomcat.global.error):_ Todas as exceções à aplicação da primavera são contabilizadas aqui. Se este número for grande, aceda ao Azure Log Analytics para inspecionar os registos de aplicação.

  - `AppMemoryMax`(_jvm.memory.max_): A quantidade máxima de memória disponível para a aplicação. O montante pode estar indefinido, ou pode mudar ao longo do tempo se for definido. Se for definida, a quantidade de memória usada e comprometida é sempre inferior ou igual a máx. No entanto, uma alocação de memória pode falhar com uma `OutOfMemoryError` mensagem se a atribuição tentar aumentar a memória usada de tal forma que usou > *comprometidas,* mesmo que *utilizadas <= max* ainda é verdade. Em tal situação, tente aumentar o tamanho `-Xmx` máximo da pilha utilizando o parâmetro.

  - `AppMemoryUsed`_(jvm.memory.used_): A quantidade de memória em bytes que é atualmente utilizado pela aplicação. Para uma aplicação Java de carga normal, esta série métrica forma um padrão *de dente de serra,* onde o uso da memória aumenta e diminui constantemente em pequenos incrementos e de repente cai muito, e então o padrão repete-se. Esta série métrica ocorre devido à recolha de lixo dentro da máquina virtual java, onde as ações de recolha representam gotas no padrão sawtooth.
    
    Esta métrica é importante para ajudar a identificar problemas de memória, tais como:
    * Uma explosão de memória no início.
    * A atribuição de memória de ondas para um caminho lógico específico.
    * Fugas de memória gradual.

  Para mais informações, consulte [Métricas](spring-cloud-concept-metrics.md).

Para saber mais sobre o Azure Log Analytics, consulte [Iniciar com Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>A minha aplicação tem uma elevada utilização da CPU ou da memória

Se a sua aplicação experimentar um alto CPU ou uso de memória, uma de duas coisas é verdade:
* Todas as instâncias de aplicação experimentam alta CPU ou uso de memória.
* Alguns dos casos de aplicações experimentam alta CPU ou uso de memória.

Para verificar qual a situação aplicável, faça o seguinte:

1. Vá para **métricas**e, em seguida, selecione a **Percentagem de Utilização** do CPU do Serviço ou **a Memória de Serviço Utilizada**.
2. Adicione um filtro **App=** para especificar qual a aplicação que pretende monitorizar.
3. Divida as métricas por **instância.**

Se *todos os casos* estiverem a experimentar um elevado CPU ou uso de memória, é necessário aumentar a aplicação ou aumentar o CPU ou o uso da memória. Para mais informações, consulte [Tutorial: Scale a application in Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Se *alguns casos* estiverem a experimentar um elevado CPU ou uso de memória, verifique o estado da instância e o seu estado de descoberta.

Para mais informações, consulte [Métricas para Azure Spring Cloud](spring-cloud-concept-metrics.md).

Se todos os casos estiverem em funcionamento, vá ao Azure Log Analytics consultar os registos da sua aplicação e reveja a sua lógica de código. Isto irá ajudá-lo a ver se algum deles pode afetar a divisão de escala. Para mais informações, consulte [registos e métricas de análise com definições](diagnostic-services.md)de diagnóstico .

Para saber mais sobre o Azure Log Analytics, consulte [Iniciar com Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Consultar os registos utilizando a linguagem de [consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Lista de verificação para implementar a sua aplicação de primavera para Azure Spring Cloud

Antes de embarcar na sua candidatura, certifique-se de que satisfaz os seguintes critérios:

* A aplicação pode ser executada localmente com a versão de tempo de execução java especificada.
* O config ambiente (CPU/RAM/Instances) cumpre o requisito mínimo estabelecido pelo fornecedor de aplicações.
* Os itens de configuração têm os seus valores esperados. Para mais informações, consulte [o Config Server](spring-cloud-tutorial-config-server.md).
* As variáveis ambientais têm os seus valores esperados.
* Os parâmetros JVM têm os seus valores esperados.
* Recomendamos que desative ou remova os serviços de registo de serviços da _Config Server_ e da _Mola_ incorporados do pacote de aplicações.
* Se os recursos do Azure forem vinculados através do _Vínculo de Serviços_, confirme que os recursos de destino estão a funcionar.

## <a name="configuration-and-management"></a>Configuração e gestão

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Encontrei um problema com a criação de uma instância de serviço Azure Spring Cloud

Quando configura uma instância de serviço Azure Spring Cloud utilizando o portal Azure, a Azure Spring Cloud realiza a validação para si.

Mas se tentar configurar a instância de serviço Azure Spring Cloud utilizando o [modelo Azure CLI](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ou o Modelo De Gestor de [Recursos Azure,](https://docs.microsoft.com/azure/azure-resource-manager/)verifique se:

* A subscrição está ativa.
* A localização é [suportada](spring-cloud-faq.md) por Azure Spring Cloud.
* O grupo de recursos, por exemplo, já está criado.
* O nome do recurso está em conformidade com a regra de nomeação. Deve conter apenas letras minúsculas, números e hífenes. O primeiro caráter tem de ser uma letra. O último caráter tem de ser uma letra ou um número. O valor deve conter de 2 a 32 caracteres.

Se quiser configurar a instância de serviço Azure Spring Cloud utilizando o modelo de Gestor de Recursos, consulte primeiro [compreender a estrutura e a sintaxe dos modelos do Gestor](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)de Recursos Azure .

O nome da instância de serviço Azure Spring Cloud será `azureapps.io`usado para solicitar um nome de subdomínio em , pelo que a configuração falhará se o nome entrar em conflito com um existente. Pode encontrar mais detalhes nos registos de atividade.

### <a name="i-cant-deploy-a-jar-package"></a>Não posso implantar um pacote JAR.

Não é possível fazer o upload do ficheiro Java Archive (JAR)/pacote de origem utilizando o portal Azure ou o modelo de Gestor de Recursos.

Quando implementa o seu pacote de aplicação utilizando o [Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)o Azure CLI vota periodicamente o progresso da implementação e, no final, exibe o resultado da implantação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

Certifique-se de que a sua aplicação está embalada no [formato JAR executável](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)correto . Se não estiver embalado corretamente, receberá uma mensagem de erro semelhante à seguinte:

> "Erro: Jarfile inválido ou corrupto /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Não posso enviar um pacote de origem.

Não é possível fazer o upload do pacote JAR/Fonte utilizando o portal Azure ou o modelo de Gestor de Recursos.

Quando implementa o seu pacote de aplicação utilizando o [Azure CLI,](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli)o Azure CLI vota periodicamente o progresso da implementação e, no final, exibe o resultado da implantação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter e construir os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

No entanto, note que uma instância de serviço Azure Spring Cloud pode desencadear apenas um trabalho de construção para um pacote de origem de uma vez. Para mais informações, consulte [A implementação de uma aplicação](spring-cloud-quickstart-launch-app-portal.md) e instale um ambiente de [preparação na Nuvem de primavera azure](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>O meu pedido não pode ser registado.

Na maioria dos casos, esta situação ocorre quando *as Dependências Necessárias* e a Descoberta de *Serviços* não estão devidamente configuradas no ficheiro Do Modelo de Objeto sonoridade (POM). Uma vez configurado, o ponto final do servidor do registo de serviço incorporado é injetado como uma variável ambiental com a sua aplicação. As aplicações registam-se então no servidor do Registo de Serviços e descobrem outros microserviços dependentes.

Aguarde pelo menos dois minutos antes que uma instância recém-registada comece a receber tráfego.

Se estiver a migrar uma solução baseada na Nuvem de primavera existente para o Azure, certifique-se de que os seus _casos_ de Registo de Serviço ad-hoc e Desativados do _Servidor Config_ são removidos (ou desativados) para evitar conflitos com as instâncias geridas fornecidas pela Azure Spring Cloud.

Também pode consultar os registos de registo de _serviço_ sessão no Azure Log Analytics. Para mais informações, consulte [Registos e métricas do Analisar com definições](diagnostic-services.md) de diagnóstico

Para saber mais sobre o Azure Log Analytics, consulte [Iniciar com Log Analytics no Monitor Azure](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Consultar os registos utilizando a linguagem de [consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Quero inspecionar as variáveis ambientais da minha aplicação

As variáveis ambientais informam o quadro azure Spring Cloud, garantindo que azure entende onde e como configurar os serviços que compõem a sua aplicação. Garantir que as variáveis ambientais estão corretas é um primeiro passo necessário para resolver problemas potenciais.  Pode utilizar o ponto final do Acionador de Botas de primavera para rever as variáveis ambientais.  

> [!WARNING]
> Este procedimento expõe as variáveis ambientais utilizando o seu ponto final de teste.  Não proceda se o seu ponto final de teste for acessível ao público ou se atribuiu um nome de domínio à sua aplicação.

1. Aceda a `https://<your application test endpoint>/actuator/health`.  
    - Uma resposta `{"status":"UP"}` semelhante indica que o ponto final foi ativado.
    - Se a resposta for negativa, inclua a seguinte dependência no seu ficheiro *POM.xml:*

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Com o ponto final do Acionador de Arranque de primavera ativado, vá ao portal Azure e procure a página de configuração da sua aplicação.  Adicione uma variável `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` ambiental com `*` o nome e o valor. 

1. Reinicie a sua candidatura.

1. Vá `https://<your application test endpoint>/actuator/env` inspecionar a resposta.  Deve ter o seguinte aspeto:

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

Procure o nó infantil `systemEnvironment`chamado.  Este nó contém as variáveis ambientais da sua aplicação.

> [!IMPORTANT]
> Lembre-se de inverter a exposição das variáveis ambientais antes de tornar a sua aplicação acessível ao público.  Vá ao portal Azure, procure a página de configuração da `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`sua aplicação e elimine esta variável ambiental: .

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Não encontro métricas ou registos para a minha aplicação.

Vá à **gestão** de Aplicações para garantir que os estados da aplicação estão _em execução_ e _UP_.

Se conseguir ver métricas da _JVM_ mas sem métricas da `spring-boot-actuator` _Tomcat,_ verifique se a dependência está ativada no seu pacote de aplicação e se consegue arrancar.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se os registos de aplicação puderem ser arquivados numa conta de armazenamento, mas não enviados para o Azure Log Analytics, verifique se [configura corretamente o seu espaço](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace)de trabalho . Se estiver a utilizar um nível gratuito de Azure Log Analytics, note que [o nível livre não fornece um acordo de nível de serviço (SLA)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).
