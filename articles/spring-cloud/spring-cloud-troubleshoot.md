---
title: Guia de solução de problemas do Azure Spring Cloud | Microsoft Docs
description: Guia de solução de problemas do Azure Spring Cloud
author: bmitchell287
ms.service: spring-cloud
ms.topic: troubleshooting
ms.date: 11/04/2019
ms.author: brendm
ms.openlocfilehash: 5dcdb03a6d4ec4f448108dbd771a44f362aa7f20
ms.sourcegitcommit: 5397b08426da7f05d8aa2e5f465b71b97a75550b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/19/2020
ms.locfileid: "76277587"
---
# <a name="troubleshoot-common-azure-spring-cloud-issues"></a>Solucionar problemas comuns do Azure Spring Cloud

Este artigo fornece instruções para solucionar problemas de desenvolvimento na nuvem do Azure Spring. Para obter informações adicionais, consulte [FAQ do Azure Spring Cloud](spring-cloud-faq.md).

## <a name="availability-performance-and-application-issues"></a>Problemas de disponibilidade, desempenho e aplicativo

### <a name="my-application-cant-start-for-example-the-endpoint-cant-be-connected-or-it-returns-a-502-after-a-few-retries"></a>Meu aplicativo não pode iniciar (por exemplo, o ponto de extremidade não pode ser conectado ou retorna um 502 após algumas repetições)

Exporte os logs para o Azure Log Analytics. A tabela para os logs de aplicativo Spring é denominada *AppPlatformLogsforSpring*. Para saber mais, consulte [analisar logs e métricas com configurações de diagnóstico](diagnostic-services.md).

A seguinte mensagem de erro pode aparecer em seus logs:

> "org. springframework. Context. ApplicationContextException: não é possível iniciar o servidor Web"

A mensagem indica um dos dois prováveis problemas: 
* Um dos Beans ou uma de suas dependências está ausente.
* Uma das propriedades do bean está em falta ou é inválida. Nesse caso, "Java. lang. IllegalArgumentException" provavelmente será exibido.

As associações de serviço também podem causar falhas de início do aplicativo. Para consultar os logs, use palavras-chave relacionadas aos serviços associados. Por exemplo, vamos supor que seu aplicativo tenha uma associação a uma instância do MySQL definida como hora do sistema local. Se o aplicativo falhar ao iniciar, a seguinte mensagem de erro poderá aparecer no log:

> "Java. Sql. SQLException: o valor de fuso horário do servidor" tempo universal coordenado "não é reconhecido ou representa mais de um fuso horário."

Para corrigir esse erro, vá para o `server parameters` da instância do MySQL e altere o valor de `time_zone` de *sistema* para *+ 0:00*.


### <a name="my-application-crashes-or-throws-an-unexpected-error"></a>A minha aplicação falha ou emite um erro inesperado

Quando você estiver Depurando falhas do aplicativo, comece verificando o status de execução e o status de descoberta do aplicativo. Para fazer isso, vá para _Gerenciamento de aplicativo_ no portal do Azure para garantir que o status de todos os aplicativos esteja _em execução e em_ funcionamento.

* Se o status estiver _em execução_ , mas o status da descoberta não estiver _ativo_, vá para a seção ["meu aplicativo não pode ser registrado"](#my-application-cant-be-registered) .

* Se o status da descoberta estiver _ativo_, vá para métricas para verificar a integridade do aplicativo. Inspecione as seguintes métricas:


  - `TomcatErrorCount` (_tomcat. global. Error_): todas as exceções de aplicativo Spring são contadas aqui. Se esse número for grande, vá para o Azure Log Analytics para inspecionar os logs do aplicativo.

  - `AppMemoryMax` (_JVM. Memory. Max_): a quantidade máxima de memória disponível para o aplicativo. O valor pode ser indefinido ou pode mudar ao longo do tempo se for definido. Se ele for definido, a quantidade de memória usada e confirmada será sempre menor ou igual ao máximo. No entanto, uma alocação de memória poderá falhar com uma mensagem de `OutOfMemoryError` se a alocação tentar aumentar a memória usada, de modo que *usada > confirmada*, mesmo se *usada < = Max* ainda for true. Nessa situação, tente aumentar o tamanho máximo do heap usando o parâmetro `-Xmx`.

  - `AppMemoryUsed` (_JVM. Memory. Used_): a quantidade de memória em bytes que é usada atualmente pelo aplicativo. Para um aplicativo Java de carregamento normal, essa série de métricas forma um padrão *Sawtooth* , em que o uso de memória aumenta e diminui continuamente em pequenos incrementos e, repentinamente, cai muito e, em seguida, o padrão se repete. Essa série de métricas ocorre devido à coleta de lixo dentro da máquina virtual Java, em que as ações de coleta representam quedas no padrão Sawtooth.
    
    Essa métrica é importante para ajudar a identificar problemas de memória, como:
    * Uma explosão de memória no início.
    * A alocação de memória de sobretensão para um caminho lógico específico.
    * Vazamentos de memória gradual.

  Para obter mais informações, consulte [métricas](spring-cloud-concept-metrics.md).

Para saber mais sobre o Log Analytics do Azure, confira [introdução ao log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal).

### <a name="my-application-experiences-high-cpu-usage-or-high-memory-usage"></a>A minha aplicação tem uma elevada utilização da CPU ou da memória

Se seu aplicativo apresentar alta utilização de CPU ou memória, uma das duas coisas será verdadeira:
* Todas as instâncias de aplicativo apresentam alta utilização de CPU ou memória.
* Algumas das instâncias do aplicativo apresentam alta utilização de CPU ou memória.

Para verificar qual situação se aplica, faça o seguinte:

1. Vá para **métricas**e selecione **percentual de uso da CPU de serviço** ou **memória de serviço usada**.
2. Adicione um **app =** Filter para especificar qual aplicativo você deseja monitorar.
3. Divida as métricas por **instância**.

Se *todas as instâncias* estiverem apresentando alta utilização de CPU ou de memória, você precisará escalar horizontalmente o aplicativo ou escalar verticalmente o uso de CPU ou memória. Para obter mais informações, consulte [tutorial: dimensionar um aplicativo no Azure Spring Cloud](spring-cloud-tutorial-scale-manual.md).

Se *algumas instâncias* estiverem apresentando alto uso de CPU ou de memória, verifique o status da instância e seu status de descoberta.

Para obter mais informações, consulte [métricas para o Azure Spring Cloud](spring-cloud-concept-metrics.md).

Se todas as instâncias estiverem em execução, vá para o Azure Log Analytics para consultar os logs do aplicativo e examinar a lógica do código. Isso irá ajudá-lo a ver se algum deles pode afetar o particionamento de escala. Para obter mais informações, consulte [analisar logs e métricas com configurações de diagnóstico](diagnostic-services.md).

Para saber mais sobre o Log Analytics do Azure, confira [introdução ao log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Consulte os logs usando a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="checklist-for-deploying-your-spring-application-to-azure-spring-cloud"></a>Lista de verificação para implantar seu aplicativo Spring no Azure Spring Cloud

Antes de carregar seu aplicativo, verifique se ele atende aos seguintes critérios:

* O aplicativo pode ser executado localmente com a versão de tempo de execução Java especificada.
* A configuração de ambiente (CPU/RAM/instâncias) atende ao requisito mínimo definido pelo provedor de aplicativos.
* Os itens de configuração têm seus valores esperados. Para obter mais informações, consulte [servidor de configuração](spring-cloud-tutorial-config-server.md).
* As variáveis de ambiente têm seus valores esperados.
* Os parâmetros da JVM têm seus valores esperados.
* Recomendamos que você desabilite ou remova o _servidor de configuração_ inserido e os serviços de _registro do serviço Spring_ do pacote de aplicativos.
* Se os recursos do Azure forem vinculados através do _Vínculo de Serviços_, confirme que os recursos de destino estão a funcionar.

## <a name="configuration-and-management"></a>Configuração e gestão

### <a name="i-encountered-a-problem-with-creating-an-azure-spring-cloud-service-instance"></a>Eu encontrei um problema ao criar uma instância do serviço de nuvem do Azure Spring

Quando você configura uma instância do serviço de nuvem do Azure Spring usando o portal do Azure, o Azure Spring Cloud executa a validação para você.

Mas se você tentar configurar a instância do serviço de nuvem do Azure Spring usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli) ou o [modelo de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/), verifique se:

* A assinatura está ativa.
* O local é [suportado](spring-cloud-faq.md) pelo Azure Spring Cloud.
* O grupo de recursos para a instância já foi criado.
* O nome do recurso está em conformidade com a regra de nomenclatura. Ele deve conter apenas letras minúsculas, números e hifens. O primeiro caráter tem de ser uma letra. O último caráter tem de ser uma letra ou um número. O valor deve conter de 2 a 32 caracteres.

Se você quiser configurar a instância do serviço de nuvem do Azure Spring usando o modelo do Resource Manager, primeiro consulte [entender a estrutura e a sintaxe dos modelos de Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates).

O nome da instância do serviço de nuvem do Azure Spring será usado para solicitar um nome de subdomínio em `azureapps.io`, de modo que a instalação falhará se o nome estiver em conflito com um existente. Você pode encontrar mais detalhes nos logs de atividade.

### <a name="i-cant-deploy-a-jar-package"></a>Não consigo implantar um pacote JAR

Não é possível carregar o pacote/Source do arquivo morto Java (JAR) usando o portal do Azure ou o modelo do Resource Manager.

Quando você implanta o pacote de aplicativos usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), o CLI do Azure sonda periodicamente o progresso da implantação e, no final, exibe o resultado da implantação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

Verifique se seu aplicativo está empacotado no [formato jar executável](https://docs.spring.io/spring-boot/docs/current/reference/html/executable-jar.html)correto. Se não for empacotado corretamente, você receberá uma mensagem de erro semelhante à seguinte:

> "Erro: inválido ou corrompido jarFile/jar/38bc8ea1-a6bb-4736-8E93-e8f3b52c8714"

### <a name="i-cant-deploy-a-source-package"></a>Não consigo implantar um pacote de origem

Não é possível carregar o pacote JAR/de origem usando o portal do Azure ou o modelo do Resource Manager.

Quando você implanta o pacote de aplicativos usando o [CLI do Azure](https://docs.microsoft.com/cli/azure/get-started-with-azure-cli), o CLI do Azure sonda periodicamente o progresso da implantação e, no final, exibe o resultado da implantação.

Se a consulta for interrompida, ainda poderá utilizar o seguinte comando para obter e construir os registos de implementação:

`az spring-cloud app show-deploy-log -n <app-name>`

No entanto, observe que uma instância do serviço de nuvem do Azure Spring pode disparar apenas um trabalho de compilação para um pacote de origem ao mesmo tempo. Para obter mais informações, consulte [implantar um aplicativo](spring-cloud-quickstart-launch-app-portal.md) e [configurar um ambiente de preparo no Azure Spring Cloud](spring-cloud-howto-staging-environment.md).

### <a name="my-application-cant-be-registered"></a>Meu aplicativo não pode ser registrado

Na maioria dos casos, essa situação ocorre quando as *dependências necessárias* e a *descoberta de serviço* não estão configuradas corretamente em seu arquivo de modelo de objeto do projeto (POM). Depois de configurado, o ponto de extremidade do servidor do registro de serviço interno é injetado como uma variável de ambiente com seu aplicativo. Os aplicativos se registram no servidor do registro de serviço e detectam outros microserviços dependentes.

Aguarde pelo menos dois minutos antes que uma instância recentemente registrada comece a receber tráfego.

Se você estiver migrando uma solução baseada em nuvem existente do Spring para o Azure, verifique se as instâncias _do registro de serviço_ ad hoc e do _servidor de configuração_ foram removidas (ou desabilitadas) para evitar conflitos com as instâncias gerenciadas fornecidas pelo Azure Spring Cloud.

Você também pode verificar os logs do cliente _do registro de serviço_ no Azure log Analytics. Para obter mais informações, consulte [analisar logs e métricas com configurações de diagnóstico](diagnostic-services.md)

Para saber mais sobre o Log Analytics do Azure, confira [introdução ao log Analytics no Azure monitor](https://docs.microsoft.com/azure/azure-monitor/log-query/get-started-portal). Consulte os logs usando a [linguagem de consulta Kusto](https://docs.microsoft.com/azure/kusto/query/).

### <a name="i-want-to-inspect-my-applications-environment-variables"></a>Quero inspecionar as variáveis de ambiente do meu aplicativo

As variáveis de ambiente informam a estrutura de nuvem Spring do Azure, garantindo que o Azure entenda onde e como configurar os serviços que compõem seu aplicativo. Garantir que suas variáveis de ambiente estejam corretas é uma primeira etapa necessária para solucionar problemas em potencial.  Você pode usar o ponto de extremidade do acionador do Spring boot para examinar suas variáveis de ambiente.  

> [!WARNING]
> Esse procedimento expõe suas variáveis de ambiente usando seu ponto de extremidade de teste.  Não prossiga se o ponto de extremidade de teste estiver publicamente acessível ou se você tiver atribuído um nome de domínio ao seu aplicativo.

1. Aceda a `https://<your application test endpoint>/actuator/health`.  
    - Uma resposta semelhante a `{"status":"UP"}` indica que o ponto de extremidade foi habilitado.
    - Se a resposta for negativa, inclua a seguinte dependência em seu arquivo *pom. xml* :

        ```xml
            <dependency>
                <groupId>org.springframework.boot</groupId>
                <artifactId>spring-boot-starter-actuator</artifactId>
            </dependency>
        ```

1. Com o ponto de extremidade do acionador do Spring Boot habilitado, vá para a portal do Azure e procure a página de configuração do seu aplicativo.  Adicione uma variável de ambiente com o nome `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE` e o valor `*`. 

1. Reinicie o aplicativo.

1. Vá para `https://<your application test endpoint>/actuator/env` e inspecione a resposta.  Deve ter o seguinte aspeto:

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

Procure o nó filho chamado `systemEnvironment`.  Esse nó contém as variáveis de ambiente do seu aplicativo.

> [!IMPORTANT]
> Lembre-se de reverter a exposição de suas variáveis de ambiente antes de tornar seu aplicativo acessível ao público.  Vá para a portal do Azure, procure a página de configuração do seu aplicativo e exclua essa variável de ambiente: `MANAGEMENT_ENDPOINTS_WEB_EXPOSURE_INCLUDE`.

### <a name="i-cant-find-metrics-or-logs-for-my-application"></a>Não consigo encontrar métricas ou logs para meu aplicativo

Vá para **Gerenciamento de aplicativo** para garantir que os status do aplicativo estejam _em execução e em_ funcionamento.

Se você puder ver as métricas da _JVM_ , mas nenhuma métrica do _tomcat_, verifique se a dependência de `spring-boot-actuator` está habilitada no pacote de aplicativo e se ela foi inicializada com êxito.

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-actuator</artifactId>
</dependency>
```

Se os logs do aplicativo puderem ser arquivados em uma conta de armazenamento, mas não forem enviados para o Azure Log Analytics, verifique se você [configurou seu espaço de trabalho corretamente](https://docs.microsoft.com/azure/azure-monitor/learn/quick-create-workspace). Se você estiver usando uma camada gratuita da Log Analytics do Azure, observe que [a camada gratuita não fornece um SLA (contrato de nível de serviço)](https://azure.microsoft.com/support/legal/sla/log-analytics/v1_3/).
