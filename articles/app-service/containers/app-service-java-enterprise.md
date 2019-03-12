---
title: Suporte da empresa de Java no Linux - serviço de aplicações do Azure | Documentos da Microsoft
description: Guia do programador para implementar aplicações de Java Enterprise com Wildfly com o serviço de aplicações do Azure no Linux.
keywords: azure app service, web app, linux, oss, java, wildfly, enterprise, java ee, jee, javaee
services: app-service
author: rloutlaw
manager: angerobe
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 08/29/2018
ms.author: routlaw
ms.custom: seodec18
ms.openlocfilehash: ee39d2d2cf85b8de52804ac764b17db348a053e0
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57538226"
---
# <a name="java-enterprise-guide-for-app-service-on-linux"></a>Guia de Java Enterprise para o serviço de aplicações no Linux

> [!NOTE] 
> Java Enterprise Edition no Linux de serviço de aplicação está atualmente em pré-visualização. É essa pilha **não** recomendado para trabalho com acesso à produção. Consulte a [guia para programadores do Java](app-service-linux-java.md) para obter informações sobre nossos pilhas Java SE e Tomcat.

Serviço de aplicações do Azure no Linux permite que os programadores de Java para criar, implementar e dimensionar aplicações Java empresariais (Java EE) num serviço totalmente gerido baseado em Linux.  O ambiente de tempo de execução Java Enterprise subjacente é o código-fonte aberto [Wildfly](https://wildfly.org/) servidor de aplicações.

Este guia fornece os conceitos chave e instruções para desenvolvedores de Java empresariais usando-se no serviço de aplicações para Linux. Se nunca implementar aplicações Java com o serviço de aplicações do Azure para Linux, deverá concluir os [início rápido de Java](quickstart-java.md) primeiro. Perguntas sobre o serviço de aplicações para Linux que não sejam específicas para Java Enterprise são respondidas no [guia para programadores de Java](app-service-linux-java.md) e o [FAQ de Linux do serviço de aplicações](app-service-linux-faq.md).

## <a name="scale-with-app-service"></a>Dimensionamento com o serviço de aplicações 

O servidor de aplicações de WildFly em execução no serviço de aplicações no Linux é executado no modo autónomo, não numa configuração de domínio. Ao aumentar horizontalmente o plano do serviço de aplicações, cada instância de WildFly está configurada como um servidor autónomo.

 Dimensionar vertical ou horizontalmente a sua aplicação com [Dimensionar regras](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-autoscale-get-started?toc=%2Fazure%2Fapp-service%2Fcontainers%2Ftoc.json) e por [aumentar sua contagem de instâncias](https://docs.microsoft.com/azure/app-service/web-sites-scale?toc=%2fazure%2fapp-service%2fcontainers%2ftoc.json). 

## <a name="customize-application-server-configuration"></a>Personalizar a configuração do servidor de aplicação

Instâncias de aplicações Web são sem monitoração de estado, portanto, cada nova instância iniciada tem de ser configurada no arranque para suportar a configuração de Wildfly aplicação precisa.
Pode escrever um script de Bash para chamar a CLI WildFly para de arranque:

- Configurar origens de dados
- Configurar fornecedores de mensagens
- Adicione outros módulos e dependências para a configuração do servidor Wildfly.

 O script é executado quando Wildfly está em execução, mas antes de iniciar a aplicação. O script deve utilizar o [JBOSS CLI](https://docs.jboss.org/author/display/WFLY/Command+Line+Interface) chamado a partir de `/opt/jboss/wildfly/bin/jboss-cli.sh` para configurar o servidor de aplicações com qualquer configuração ou as alterações necessárias depois do servidor é iniciado. 

Não utilize o modo interativo da CLI para configurar Wildfly. Em vez disso, pode fornecer um script de comandos para a CLI JBoss usando o `--file` comando, por exemplo:

```bash
/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli
```

Carregar o script de inicialização para `/home/site/deployments/tools` na sua instância do serviço de aplicações. Ver [este documento](/azure/app-service/deploy-configure-credentials#userscope) para obter instruções sobre como obter as suas credenciais FTP. 

Definir o **Script de inicialização** campo no portal do Azure para a localização do seu script de shell de inicialização, por exemplo `/home/site/deployments/tools/your-startup-script.sh`.

Fornecer [as definições da aplicação](/azure/app-service/web-sites-configure#application-settings) na configuração da aplicação para passar as variáveis de ambiente para utilização no script. Definições da aplicação tenha cadeias de ligação e outros segredos necessários para configurar o aplicativo fora do controle de versão.

## <a name="modules-and-dependencies"></a>Módulos e dependências

Para instalar os módulos e suas dependências no caminho da classe Wildfly através da CLI JBoss, terá de criar os seguintes ficheiros no seu próprio diretório.  Alguns módulos e dependências poderão ter uma configuração adicional, como JNDI de nomenclatura ou outra configuração de API específicas, para que esta lista é um conjunto mínimo de que vai precisar configurar uma dependência na maioria dos casos.

- Uma [descritor de módulo XML](https://jboss-modules.github.io/jboss-modules/manual/#descriptors). Esse arquivo XML define o nome, atributos e as dependências do seu módulo. Isso [Module. XML de exemplo de ficheiro](https://access.redhat.com/documentation/en-us/jboss_enterprise_application_platform/6/html/administration_and_configuration_guide/example_postgresql_xa_datasource) define um módulo de Postgres, sua dependência JDBC do ficheiro JAR e outras dependências do módulo necessárias.
- Quaisquer dependências de ficheiro JAR necessárias para seu módulo.
- Um script com seus comandos do JBoss CLI para configurar o módulo de novo. Este ficheiro irá conter os comandos a serem executados pela CLI JBoss para configurar o servidor para utilizar a dependência. Para obter documentação sobre os comandos para adicionar módulos, origens de dados e provedores de mensagens, consulte [este documento](https://access.redhat.com/documentation/red_hat_jboss_enterprise_application_platform/7.0/html-single/management_cli_guide/#how_to_cli).
-  Um script de inicialização do Bash para chamar a CLI JBoss e executar o script no passo anterior. Este ficheiro será executado quando sua instância do serviço de aplicações é reiniciada ou quando novas instâncias são aprovisionadas durante um Escalamento horizontal.  Este script de inicialização é onde pode realizar quaisquer outras configurações para a sua aplicação à medida que os comandos de JBoss são transmitidos para a CLI JBoss. No mínimo, este ficheiro pode ser um único comando para passar o script do comando CLI de JBoss para a CLI JBoss: 
   
```bash
`/opt/jboss/wildfly/bin/jboss-cli.sh -c --file=/path/to/your/jboss_commands.cli` 
``` 

Assim que tiver os ficheiros e conteúdo para o seu módulo, siga os passos abaixo para adicionar o módulo para o servidor de aplicações Wildfly. 

1. Os ficheiros para de FTP `/home/site/deployments/tools` na sua instância do serviço de aplicações. Veja este documento para obter instruções sobre como obter as suas credenciais FTP. 
2. No painel de definições de aplicação do portal do Azure, defina o campo "Script de inicialização" para a localização do seu script de shell de inicialização, por exemplo `/home/site/deployments/tools/your-startup-script.sh` .
3. Reinicie a instância de serviço de aplicações ao premir o **reinicie** botão no **descrição geral** seção do Portal ou através da CLI do Azure.

## <a name="data-sources"></a>Origens de dados

Para configurar Wildfly para uma ligação de origem de dados, siga o mesmo processo descrito acima na seção instalar módulos e dependências. Pode seguir os mesmos passos para qualquer serviço de base de dados do Azure.

1. Baixe o JDBC driver para seu sabor de base de dados. Para sua comodidade, aqui estão os controladores para [Postgres](https://jdbc.postgresql.org/download.html) e [MySQL](https://dev.mysql.com/downloads/connector/j/). Descompacte o download para obter o ficheiro. JAR.
2. Siga o contorno de etapas em "Módulos e dependências" para criar e carregar o descritor de módulo XML, script da CLI de JBoss, script de inicialização e dependência. JAR do JDBC.


Obter mais informações sobre como configurar Wildfly com [PostgreSQL](https://developer.jboss.org/blogs/amartin-blog/2012/02/08/how-to-set-up-a-postgresql-jdbc-driver-on-jboss-7) , [MySQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#Using_other_Databases-Using_MySQL_as_the_Default_DataSource), e [base de dados SQL](https://docs.jboss.org/jbossas/docs/Installation_And_Getting_Started_Guide/5/html/Using_other_Databases.html#d0e3898) está disponível. Pode utilizar estas instruções personalizadas, juntamente com a abordagem generalizada acima para adicionar definições de origem de dados ao seu servidor.

## <a name="messaging-providers"></a>Fornecedores de mensagens

Para ativar Beans mensagem controlado por utilizar o Service Bus como o mecanismo de mensagens:

1. Utilize o [biblioteca de mensagens do Apache QPId JMS](https://qpid.apache.org/proton/index.html). Inclua esta dependência em sua pom. XML (ou outro arquivo de compilação) para a aplicação.

2.  Crie [recursos do Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp). Criar um espaço de nomes do Service bus do Azure e a fila dentro desse espaço de nomes e uma política de acesso partilhado com o envie e receba recursos.

3. Passar a chave de política de acesso partilhado para o seu código com codificação do URL da chave primária da sua política ou [utilize o SDK do Service Bus](/azure/service-bus-messaging/service-bus-java-how-to-use-jms-api-amqp#setup-jndi-context-and-configure-the-connectionfactory).

4. Siga os passos descritos na seção instalar módulos e as dependências com o descritor XML do módulo,. JAR dependências, comandos da CLI de JBoss e script de inicialização para o fornecedor JMS. Além dos quatro arquivos, também terá de criar um arquivo XML que define o nome JNDI para a fila JMS e tópico. Ver [este repositório](https://github.com/JasonFreeberg/widlfly-server-configs/tree/master/appconfig) para ficheiros de configuração de referência.


## <a name="configure-session-management-caching"></a>Configurar a colocação em cache da gestão de sessão

Por predefinição, o serviço de aplicações no Linux irá utilizar cookies de afinidade de sessão para se certificar de que os pedidos de cliente com sessões existentes são encaminhados a mesma instância do seu aplicativo. Este comportamento predefinido não requer configurações, mas tem algumas limitações:

- Se uma instância de aplicação é reiniciada ou reduzida verticalmente, o estado de sessão do utilizador no servidor de aplicativos serão perdido.
- Se as aplicações têm definições de tempo limite da sessão de longa ou um número fixo de utilizadores, pode demorar algum tempo para dimensionado automaticamente novas instâncias para receber a carga, uma vez que apenas novas sessões serão encaminhados para as instâncias recentemente iniciadas.

Pode configurar Wildfly a usar um armazenamento de sessão externo, como [a Cache de Redis do Azure](/azure/azure-cache-for-redis/). Precisará [desativar a afinidade de instância existente do ARR](https://azure.microsoft.com/blog/disabling-arrs-instance-affinity-in-windows-azure-web-sites/) configuração para desativar a sessão com base no cookie encaminhamento e permitir que o arquivo de sessão Wildfly configurado operar sem interferência.

## <a name="enable-web-sockets"></a>Ativar web sockets

Por predefinição, os web sockets estão ativadas no serviço de aplicações. Para obter uma introdução WebSockets na sua aplicação, consulte [este guia de introdução](https://github.com/wildfly/quickstart/tree/master/websocket-hello).

## <a name="logs-and-troubleshooting"></a>Os registos e a resolução de problemas

Serviço de aplicações fornece ferramentas para ajudar a resolver problemas com a sua aplicação.

-   Ativar o registo ao clicar em **registos de diagnóstico** no painel de navegação esquerdo. Clique em **sistema de ficheiros** para definir o período de retenção e a quota de armazenamento e guardar as alterações. Pode encontrar estes registos em `/home/LogFiles/`.
-   [Utilizar o SSH para ligar à instância do aplicativo](app-service-linux-ssh-support.md) para ver os registos para a execução de aplicativos.
-   Registos de diagnóstico de verificação **registos de diagnóstico** painel do Portal ou através do comando da CLI do Azure: ` az webapp log tail --name <your-app-name> --resource-group <your-apps-resource-group> `
