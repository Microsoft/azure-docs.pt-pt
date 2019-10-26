---
title: Guia de solução de problemas do Azure Spring Cloud | Microsoft Docs
description: Guia de solução de problemas do Azure Spring Cloud
services: spring-cloud
author: v-vasuke
manager: gwallace
editor: ''
ms.service: spring-cloud
ms.topic: quickstart
ms.date: 10/07/2019
ms.author: v-vasuke
ms.openlocfilehash: 546c97421fdb3a581a22e34f6110986a1a0732b6
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/25/2019
ms.locfileid: "72929154"
---
# <a name="troubleshooting-guide-for-common-problems"></a>Guia de solução de problemas comuns

Este artigo detalha alguns problemas comuns e as etapas de solução de problemas para os desenvolvedores que trabalham na nuvem Spring do Azure. Também recomendamos ler nosso [artigo de perguntas frequentes](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemas de disponibilidade, desempenho e aplicativo

### <a name="my-application-cannot-start-for-example-the-endpoint-cannot-be-connected-or-returns-502-after-few-retries"></a>Meu aplicativo não pode iniciar (por exemplo, o ponto de extremidade não pode ser conectado ou retorna 502 após algumas repetições)

Exporte os logs para o _Azure log Analytics_. A tabela dos registos de aplicações Spring é denominada `AppPlatformLogsforSpring`. Para saber mais, visite [analisar logs e métricas com configurações de diagnóstico](diagnostic-services.md)

Encontrar o seguinte erro em seus logs indica um dos dois prováveis problemas:

`org.springframework.context.ApplicationContextException: Unable to start web server`

* Um dos Beans ou uma de suas dependências está ausente.
* Uma das propriedades do bean está em falta ou é inválida. Você provavelmente verá `java.lang.IllegalArgumentException`, nesse caso.

As associações de serviço também podem causar falhas de início do aplicativo. Utilize palavras-chave relacionadas com os serviços vinculados para consultar os registos.  Por exemplo, suponha que seu aplicativo tenha uma associação a uma instância do MySQL definida como hora do sistema local. Se o aplicativo falhar ao iniciar, você poderá encontrar o seguinte erro no log:

`java.sql.SQLException: The server time zone value 'Coordinated Universal Time' is unrecognized or represents more than one time zone.`

Para corrigir esse erro, vá para o `server parameters` da instância do MySql e altere `time_zone` de `SYSTEM` para `+0:00`.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>A minha aplicação falha ou emite um erro inesperado

Ao depurar o aplicativo falhar, comece verificando o status de execução e o status de descoberta do aplicativo. Vá para _Gerenciamento de aplicativos_ no portal do Azure para garantir que todos os aplicativos estejam _em execução e em funcionamento_ _._

* Se o status estiver _em execução_ , mas o status da descoberta não estiver _ativo_, vá para [meu aplicativo não pode ser registrado](#my-application-cannot-be-registered).

* Se o status da descoberta estiver _ativo_, vá para _métricas_ para verificar a integridade do aplicativo. Inspecione as seguintes métricas:


  - `TomcatErrorCount` (_tomcat. global. Error_): todas as exceções de aplicativo Spring serão contadas aqui. Se esse número for grande, vá para o _Azure log Analytics_ para inspecionar os logs do aplicativo.

  - `AppMemoryMax` (_JVM. Memory. Max_): a quantidade máxima de memória disponível para o aplicativo. Ele pode ser indefinido ou ser alterado ao longo do tempo, se definido. A quantidade de memória utilizada e consolidada será sempre inferior ou igual à máxima, se definida. No entanto, a alocação de memória poderá falhar com `OutOfMemoryError` se tentar aumentar a memória utilizada, de forma que a utilizada > consolidada mesmo se utilizada <= máx. continuar a ser verdadeira. Nesta situação, tente aumentar o tamanho máximo da área dinâmica para dados através do parâmetro `-Xmx`.

  - `AppMemoryUsed` (_JVM. Memory. Used_): a quantidade de memória em bytes que é usada atualmente pelo aplicativo. Para uma aplicação Java de carregamento normal, esta série de métricas formará um padrão de “dentes de serra”, em que a utilização de memória aumenta e diminui de forma constante em pequenos incrementos e, em seguida, tem uma queda brusca, sendo este um padrão repetitivo. Isso ocorre devido à coleta de lixo dentro da máquina virtual Java, em que as ações de coleta representam quedas no ' sawteeth '.
    Essa métrica é importante para identificar problemas de memória, como: * explosão de memória no começo * alocação de memória de surto para um caminho lógico específico * vazamentos de memória gradual

  Para obter mais detalhes, visite [métricas](spring-cloud-concept-metrics.md).

Visite [Este artigo de introdução](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) para começar a usar o _Azure log Analytics_.

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>A minha aplicação tem uma elevada utilização da CPU ou da memória

Se seu aplicativo apresentar alto uso de CPU/memória, uma das duas coisas será verdadeira:
* Todas as instâncias de aplicativo apresentam alto uso de CPU/memória ou
* Algumas das instâncias do aplicativo apresentam alto uso de CPU/memória.

Para confirmar qual é a situação,

1. Aceda a _Métricas_ e selecione `Service CPU Usage Percentage` ou `Service Memory Used`,
2. Adicione um filtro `App=` para especificar a aplicação que quer monitorizar e
3. Divida as métricas por `Instance`.

Se todas as instâncias estiverem apresentando alta CPU/memória, você precisará escalar horizontalmente o aplicativo ou escalar verticalmente a CPU/memória. Para obter mais detalhes, visite [dimensionar aplicativos](spring-cloud-tutorial-scale-manual.md)

Se algumas das instâncias estiverem apresentando alta CPU/memória, verifique o status da instância e seu status de descoberta.

Para obter mais detalhes, visite [métricas](spring-cloud-concept-metrics.md).

Se todas as instâncias estiverem em execução, vá para o _Azure log Analytics_ para consultar os logs de aplicativo e examine a lógica do código para ver se algum deles pode afetar o particionamento de escala. Para obter mais detalhes, visite [analisar logs e métricas com configurações de diagnóstico](diagnostic-services.md).

Visite [Este artigo de introdução](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) para começar a usar o _Azure log Analytics_. Consulte os logs usando a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-before-onboarding-your-spring-application-to-azure-spring-cloud"></a>Lista de verificação antes de integrar seu aplicativo Spring ao Azure Spring Cloud

* O aplicativo pode ser executado localmente com a versão de tempo de execução Java especificada.
* A configuração de ambiente (CPU/RAM/instâncias) atende ao requisito mínimo definido pelo provedor de aplicativos.
* Os itens de configuração têm os valores esperados. Para obter mais informações, consulte [servidor de configuração](spring-cloud-tutorial-config-server.md).
* As variáveis de ambiente têm valores esperados.
* Os parâmetros de JVM têm valores esperados.
* Recomendamos que você desabilite/remova o _servidor de configuração_ incorporado e o serviço de registro de _serviço Spring_ do pacote de aplicativos.
* Se os recursos do Azure forem vinculados através do _Vínculo de Serviços_, confirme que os recursos de destino estão a funcionar.

## <a name="configuration-and-management"></a>Configuração e gestão

### <a name="i-encountered-a-problem-creating-an-azure-spring-cloud-service-instance"></a>Eu encontrei um problema ao criar uma instância do serviço de nuvem Spring do Azure

Quando você tenta provisionar uma instância do serviço de _nuvem Spring do Azure_ por meio do portal, o Azure Spring Cloud executará a validação para você.

No entanto, se você tentar provisionar a instância do serviço de _nuvem Spring do Azure_ por meio do [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ou do [modelo do Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), verifique:

* A assinatura está ativa.
* O local é [suportado](spring-cloud-faq.md) pelo _Azure Spring Cloud_.
* O grupo de recursos para a instância já foi criado.
* O nome do recurso está em conformidade com a regra de nomenclatura. (Ele pode conter apenas letras minúsculas, números e hifens. O primeiro caráter tem de ser uma letra. O último caráter tem de ser uma letra ou um número. O valor deve ter entre 2 e 32 caracteres de comprimento.)

Se você tentar provisionar a instância do serviço de _nuvem Spring do Azure_ por meio do modelo do Resource Manager, visite https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates para verificar a sintaxe do modelo.

O nome da instância do serviço de _nuvem do Azure Spring_ será usado para solicitar um nome de subdomínio em `azureapps.io`, de modo que o provisionamento falhará se o nome estiver em conflito com um existente. Pode encontrar mais detalhes nos registos de atividade.

### <a name="i-cannot-deploy-a-jar-package"></a>Não consigo implementar um pacote JAR

Não é possível carregar o pacote JAR/de origem por meio do portal ou do modelo do Resource Manager.

Quando você implanta o pacote de aplicativos usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), ele sonda periodicamente o progresso da implantação e, no final, ele mostrará o resultado da implantação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

Garanta que a aplicação está comprimida no [formato executável jar](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html) correto. Caso contrário, verá um erro semelhante ao seguinte:

`Error: Invalid or corrupt jarfile /jar/38bc8ea1-a6bb-4736-8e93-e8f3b52c8714`

### <a name="i-cannot-deploy-a-source-package"></a>Não consigo implementar um pacote de origem

Não é possível carregar o pacote JAR/de origem por meio do portal ou do modelo do Resource Manager.

Quando implementar o pacote de aplicações através da [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), esta consultará periodicamente o progresso da implementação e, no final, mostrará o resultado da implementação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter e construir os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

No entanto, observe que uma instância de serviço de _nuvem do Azure Spring_ só pode disparar um trabalho de compilação para um pacote de origem ao mesmo tempo. Para obter mais detalhes, consulte [o guia implantar um aplicativo e um](spring-cloud-quickstart-launch-app-portal.md) ambiente de [preparo](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cannot-be-registered"></a>Não é possível registar a m minha aplicação

Na maioria dos casos, isso acontece quando as dependências/descoberta de serviço necessárias não estão configuradas corretamente em seu arquivo POM. Uma vez configurado, o ponto de extremidade do servidor do registro de serviço interno será injetado como uma variável de ambiente com seu aplicativo. Os aplicativos serão registrados com o servidor do registro de serviço e descobrirão outros microserviços dependentes.

Aguarde pelo menos 2 minutos antes que uma instância recentemente registrada comece a receber tráfego.

Se você estiver migrando uma solução baseada na Spring Cloud existente para o Azure, verifique se as instâncias _do registro de serviço_ ad hoc e do _servidor de configuração_ foram removidas (ou desabilitadas) para evitar conflitos com as instâncias gerenciadas fornecidas pelo _Azure Spring Cloud_ .

Você também pode verificar os logs _do cliente do registro de serviço_ no _Azure log Analytics_. Para obter mais detalhes, visite [analisar logs e métricas com configurações de diagnóstico](diagnostic-services.md)

Visite [Este artigo de introdução](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal) para começar a usar o _Azure log Analytics_. Consulte os logs usando a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Quero inspecionar as variáveis de ambiente do meu aplicativo

As variáveis de ambiente informam a estrutura de nuvem Spring do Azure, garantindo que o Azure entenda onde e como configurar os serviços que compõem seu aplicativo.  Garantir que suas variáveis de ambiente estejam corretas é uma primeira etapa necessária para solucionar problemas em potencial.  Você pode usar o ponto de extremidade do acionador do Spring boot para examinar suas variáveis de ambiente.  

> [!WARNING]
> Este procedimento expõe suas variáveis de ambiente usando seu ponto de extremidade de teste.  Não prossiga se o ponto de extremidade de teste estiver publicamente acessível ou se você tiver atribuído um nome de domínio ao seu aplicativo.

1. Navegue até esta URL: `https://<your application test endpoint>/actuator/health`.  
    - Uma resposta semelhante a `{"status":"UP"}` indica que o ponto de extremidade foi habilitado.
    - Se a resposta for negativa, inclua a seguinte dependência em seu `POM.xml`:

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Com o ponto de extremidade do acionador do Spring Boot habilitado, vá para a portal do Azure e localize a página de configuração do seu aplicativo.  Adicione uma variável de ambiente com o nome `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` e o valor `*`. 

1. Reinicie o aplicativo.

1. Navegue até `https://<the test endpoint of your app>/actuator/env` e inspecione a resposta.  Deve ter o seguinte aspeto:

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

Localize o nó filho chamado `systemEnvironment`.  Esse nó contém as variáveis de ambiente do seu aplicativo.

> [!IMPORTANT]
> Lembre-se de reverter a exposição de suas variáveis de ambiente antes de tornar seu aplicativo acessível ao público.  Vá para a portal do Azure, localize a página de configuração do seu aplicativo e exclua essa variável de ambiente: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cannot-find-metrics-or-logs-for-my-application"></a>Não consigo localizar as métricas ou os registos da minha aplicação

Vá para _Gerenciamento de aplicativo_ para verificar se o aplicativo está _em execução e em funcionamento_ .

Se você puder ver as métricas da _JVM_ , mas nenhuma métrica do _tomcat_, verifique se a dependência de `spring-boot-actuator` está habilitada no pacote do aplicativo e se foi inicializada com êxito.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se for possível arquivar os registos da aplicação numa conta de armazenamento, mas não enviá-los para o _Azure Log Analytics_, verifique se [configurou a área de trabalho corretamente](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Se você estiver usando uma camada gratuita de _log Analytics do Azure_, observe que [a camada gratuita não fornece SLA](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).