---
title: Tecnologias de código aberto FAQs
description: Obtenha respostas para perguntas frequentes sobre tecnologias de código aberto no Azure App Service.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18, devx-track-python
ms.openlocfilehash: 36dfbf0fda060a8f273fee64098d6234b575088c
ms.sourcegitcommit: beacda0b2b4b3a415b16ac2f58ddfb03dd1a04cf
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/31/2020
ms.locfileid: "97831844"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Tecnologias de código aberto FAQs para Web Apps em Azure

Este artigo tem respostas a perguntas frequentes (FAQs) sobre problemas com tecnologias de código aberto para a [funcionalidade de Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Como posso ligar o registo de PHP para resolver problemas de PHP?

Para ligar a registo de PHP:

1. Inscreva-se no seu **website da Kudu** `https://*yourwebsitename*.scm.azurewebsites.net` ().
2. No menu superior, selecione **Debug Console**  >  **CMD**.
3. Selecione a pasta **'Site'.**
4. Selecione a pasta **wwwroot.**
5. Selecione o **+** ícone e, em seguida, selecione **Novo Ficheiro**.
6. Desave o nome do ficheiro para **.user.ini**.
7. Selecione o ícone do lápis ao lado **de.user.ini**.
8. No ficheiro, adicione este código: `log_errors=on`
9. Selecione **Guardar**.
10. Selecione o ícone do lápis ao lado **do wp-config.php**.
11. Altere o texto para o seguinte código:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. No portal Azure, no menu de aplicações web, reinicie a sua aplicação web.

Para obter mais informações, consulte [ativar os registos de erro wordpress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Como faço o registo de erros de aplicação Python em aplicações que estão hospedadas no Serviço de Aplicações?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Como posso alterar a versão da aplicação Node.js que está hospedada no Serviço de Aplicações?

Para alterar a versão da aplicação Node.js, pode utilizar uma das seguintes opções:

* No portal Azure, utilize **as definições de App**.
  1. No portal Azure, vá à sua aplicação web.
  2. Na lâmina **Definições,** selecione **definições de aplicação**.
  3. Nas **definições de App,** pode incluir WEBSITE_NODE_DEFAULT_VERSION como chave, e a versão de Node.js deseja como valor.
  4. Vá para a sua **consola Kudu** `https://*yourwebsitename*.scm.azurewebsites.net` ().
  5. Para verificar a versão Node.js, insira o seguinte comando:  
     ```
     node -v
     ```
* Modifique o ficheiro iisnode.yml. Alterar a versão Node.js no ficheiro iisnode.yml apenas define o ambiente de tempo de execução que o iisnode utiliza. O seu Kudu cmd e outros ainda utilizam a versão Node.js que está definida nas **definições** de App no portal Azure.

  Para definir o iisnode.yml manualmente, crie um ficheiro iisnode.yml na pasta raiz da sua aplicação. No ficheiro, inclua a seguinte linha:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Desave o ficheiro iisnode.yml utilizando package.jsdurante a colocação de controlo de origem.
  O processo de implantação do controlo de fontes Azure envolve as seguintes etapas:
  1. Move o conteúdo para a aplicação web Azure.
  2. Cria um script de implementação predefinido, se não houver um (implementar ficheiros.cmd, .deployment files) na pasta raiz da aplicação web.
  3. Executa um script de implementação no qual cria um ficheiro iisnode.yml se mencionar a versão Node.js no package.jsno ficheiro > motor `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. O ficheiro iisnode.yml tem a seguinte linha de código:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Vejo a mensagem "Erro que estabelece uma ligação de base de dados" na minha aplicação WordPress que está hospedada no Serviço de Aplicações. Como é que resolvo isto?

Se vir este erro na sua aplicação Azure WordPress, para ativar php_errors.log e depurar.log, complete os passos detalhados nos [registos de erro do Enable WordPress](/archive/blogs/azureossds/logging-php-errors-in-wordpress-2).

Quando os registos estiverem ativados, reproduza o erro e, em seguida, verifique os registos para ver se está a ficar sem ligações:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Se vir este erro nos seus ficheiros de depurado.log ou php_errors.log, a sua aplicação está a exceder o número de ligações. Se estiver hospedado no ClearDB, verifique o número de ligações disponíveis no seu [plano de serviço.](https://www.cleardb.com/pricing.view)

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Como depurar uma Node.js aplicação que está hospedada no Serviço de Aplicações?

1.  Vá para a sua **consola Kudu** `https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole` ().
2.  Aceda à pasta de registos de aplicações (D:\home\LogFiles\Application).
3.  No ficheiro logging_errors.txt, verifique se há conteúdo.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Como posso instalar módulos Python nativos numa aplicação web do Serviço de Aplicações ou numa aplicação API?

Algumas embalagens podem não ser instaladas utilizando pip em Azure. O pacote pode não estar disponível no Python Package Index, ou pode ser necessário um compilador (um compilador não está disponível no computador que está a executar a aplicação web no Serviço de Aplicações). Para obter informações sobre a instalação de módulos nativos em aplicações web do App Service e aplicações API, consulte [instalar módulos Python no Serviço de Aplicações.](/archive/blogs/azureossds/install-native-python-modules-on-azure-web-apps-api-apps)

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Como posso implementar uma aplicação Django para o App Service usando o Git e a nova versão do Python?

Para obter informações sobre a instalação do Django, consulte [implementar uma aplicação Django para o Serviço de Aplicações.](/archive/blogs/azureossds/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python)

## <a name="where-are-the-tomcat-log-files-located"></a>Onde estão os ficheiros de registo do Tomcat?

Para a Azure Marketplace e implementações personalizadas:

* Localização da pasta: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Ficheiros de interesse:
    * A Catalina. *yyyy-mm-dd*.log
    * anfitrião-gerente. *yyyy-mm-dd*.log
    * local. *yyyy-mm-dd*.log
    * gerente. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log


Para **implementações de configurações de aplicações** do portal app:

* Localização da pasta: D:\home\LogFiles
* Ficheiros de interesse:
    * A Catalina. *yyyy-mm-dd*.log
    * anfitrião-gerente. *yyyy-mm-dd*.log
    * local. *yyyy-mm-dd*.log
    * gerente. *yyyy-mm-dd*.log
    * site_access_log. *yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Como é que resolvo os erros de ligação do condutor JDBC?

Pode ver a seguinte mensagem nos seus registos Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Para resolver o erro:

1. Remova o ficheiro sqljdbc*.jar da sua pasta app/lib.
2. Se estiver a utilizar o servidor web Tomcat ou Azure Marketplace Tomcat, copie este ficheiro .jar para a pasta Lib Tomcat.
3. Se estiver a ativar a Java a partir do portal Azure (selecione **Java 1.8**  >  **Tomcat server),** copie o ficheiro de frasco sqljdbc.* na pasta paralela à sua aplicação. Em seguida, adicione a seguinte definição de classe ao ficheiro web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Por que vejo erros quando tento copiar ficheiros de registos ao vivo?

Se tentar copiar ficheiros de registo ao vivo para uma aplicação Java (por exemplo, Tomcat), poderá ver este erro FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A mensagem de erro pode variar, dependendo do cliente FTP.

Todas as aplicações java têm este problema de bloqueio. Apenas a Kudu suporta o download deste ficheiro enquanto a aplicação está em execução.

Parar a aplicação permite o acesso ftp a estes ficheiros.

Outra solução alternativa é escrever um WebJob que executa um horário e copia estes ficheiros para um diretório diferente. Para um projeto de amostra, consulte o projeto [CopyLogsJob.](https://github.com/kamilsykora/CopyLogsJob)

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Onde encontro os registos do Jetty?

Para o Marketplace e as implementações personalizadas, o ficheiro de registo encontra-se na pasta D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Note que a localização da pasta depende da versão do Jetty que está a utilizar. Por exemplo, o caminho aqui fornecido é para Jetty 9.1.2. Procure jetty_ *YYYY_MM_DD*.stderrout.log.

Para implementações de Definição de aplicativos do portal, o ficheiro de registo está em D:\home\LogFiles. Procure jetty_ *YYYY_MM_DD*.stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Posso enviar e-mail da minha aplicação web Azure?

O Serviço de Aplicações não tem uma funcionalidade de e-mail incorporada. Para algumas boas alternativas para o envio de e-mail da sua app, consulte esta [discussão Stack Overflow](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Porque é que o meu site WordPress redireciona para outro URL?

Se emigrou recentemente para Azure, o WordPress poderá redirecionar para o antigo URL de domínio. Isto é causado por uma definição na base de dados MySQL.

WordPress Buddy+ é uma extensão do Site Azure que pode utilizar para atualizar o URL de reorientação diretamente na base de dados. Para obter mais informações sobre a utilização do WordPress Buddy+, consulte [as ferramentas WordPress e a migração MySQL com o WordPress Buddy+](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

Em alternativa, se preferir atualizar manualmente o URL de reorientação utilizando consultas SQL ou PHPMyAdmin, consulte [WordPress: Redirecionamento para URL errado](/archive/blogs/azureossds/wordpress-redirecting-to-wrong-url).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Como posso alterar a minha palavra-passe de entrada do WordPress?

Se esqueceu a sua palavra-passe de entrada do WordPress, pode utilizar o WordPress Buddy+ para a atualizar. Para redefinir a sua palavra-passe, instale a extensão do WordPress Buddy+ Azure Site e, em seguida, complete os passos descritos nas [ferramentas WordPress e na migração mySQL com o WordPress Buddy+](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Não posso assinar com o WordPress. Como posso resolver isto?

Se se encontrar bloqueado fora do WordPress depois de ter instalado recentemente um plugin, poderá ter um plugin defeituoso. WordPress Buddy+ é uma extensão do Site Azure que pode ajudá-lo a desativar plugins no WordPress. Para mais informações, consulte [as ferramentas WordPress e a migração MySQL com o WordPress Buddy+](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Como posso migrar a minha base de dados WordPress?

Tem várias opções para migrar a base de dados MySQL que está ligada ao seu website WordPress:

* Desenvolvedores: Utilize o [pedido de comando ou PHPMyAdmin](/archive/blogs/azureossds/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service)
* Não desenvolvedores: Use [WordPress Buddy+](https://www.electrongeek.com/blog/2016/12/21/wordpress-buddy-site-extension-for-app-service-on-windows)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Como posso ajudar a tornar o WordPress mais seguro?

Para saber mais sobre as melhores práticas de segurança para o WordPress, consulte [as melhores práticas para a segurança WordPress em Azure](/archive/blogs/azureossds/best-practices-for-wordpress-security-on-azure).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Estou a tentar usar phpMyAdmin, e vejo a mensagem "Acesso negado". Como posso resolver isto?

Poderá experimentar este problema se a funcionalidade de aplicação mySQL ainda não estiver em execução neste caso de Serviço de Aplicações. Para resolver o problema, tente aceder ao seu website. Isto inicia os processos necessários, incluindo o processo de aplicação MySQL. Para verificar se o MySQL in-app está em execução, no Process Explorer, certifique-se de que mysqld.exe está listado nos processos.

Depois de garantir que o MySQL está em execução, tente utilizar PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Recebo um erro HTTP 403 quando tento importar ou exportar a minha base de dados mySQL in-app utilizando PHPMyadmin. Como posso resolver isto?

Se estiver a utilizar uma versão mais antiga do Chrome, poderá estar a experimentar um bug conhecido. Para resolver o problema, atualize para uma versão mais recente do Chrome. Tente também utilizar um navegador diferente, como o Internet Explorer ou o Microsoft Edge, onde o problema não ocorre.