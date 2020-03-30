---
title: FaQs de tecnologias de código aberto
description: Obtenha respostas a perguntas frequentes sobre tecnologias de código aberto no Serviço de Aplicações Azure.
author: genlin
manager: dcscontentpm
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 33590f9fc7e6c4d46123cbc7088086a3197d52ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "74672434"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>FaQs de tecnologias de código aberto para Web Apps em Azure

Este artigo tem respostas a perguntas frequentes (PERGUNTAS) sobre questões com tecnologias de código aberto para a funcionalidade de [Aplicações Web do Azure App Service](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Como ligo a php logging para resolver problemas de PHP?

Para ligar a exploração madeireira PHP:

1. Inscreva-se no seu [site da Kudu.](https://*yourwebsitename*.scm.azurewebsites.net)
2. No menu superior, selecione **Debug Console** > **CMD**.
3. Selecione a pasta **Site.**
4. Selecione a pasta **wwwroot.**
5. Selecione o **+** ícone e, em seguida, selecione **New File**.
6. Desloque o nome do ficheiro para **.user.ini**.
7. Selecione o ícone do lápis ao lado de **.user.ini**.
8. No ficheiro, adicione este código:`log_errors=on`
9. Selecione **Guardar**.
10. Selecione o ícone do lápis ao lado de **wp-config.php**.
11. Altere o texto para o seguinte código:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. No portal Azure, no menu de aplicações web, reinicie a sua aplicação web.

Para mais informações, consulte os registos de [erros do Enable WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Como registo erros de aplicação Python em apps que estão hospedadas no App Service?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Como posso alterar a versão da aplicação Node.js que está hospedada no Serviço de Aplicações?

Para alterar a versão da aplicação Node.js, pode utilizar uma das seguintes opções:

* No portal Azure, utilize **as definições**da App .
  1. No portal Azure, vá à sua aplicação web.
  2. Na lâmina **Definições,** selecione **as definições de aplicação**.
  3. Nas definições da **App,** pode incluir WEBSITE_NODE_DEFAULT_VERSION como chave e a versão do Node.js que deseja como valor.
  4. Vá para a sua [consola Kudu.](https://*yourwebsitename*.scm.azurewebsites.net)
  5. Para verificar a versão Node.js, introduza o seguinte comando:  
     ```
     node -v
     ```
* Modificar o ficheiro iisnódeo.yml. Alterar a versão Node.js no ficheiro iisnóde.yml apenas define o ambiente de tempo de execução que o iisnode utiliza. O seu Kudu cmd e outros ainda utilizam a versão Node.js que está definida nas **definições** da App no portal Azure.

  Para definir o iisnode.yml manualmente, crie um ficheiro iisnode.yml na pasta raiz da aplicação. No ficheiro, inclua a seguinte linha:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Desloque o ficheiro iisnode.yml utilizando o pacote.json durante a implantação do controlo de fonte.
  O processo de implantação de controlo de fontes Azure envolve os seguintes passos:
  1. Move conteúdo para a aplicação web Azure.
  2. Cria um script de implementação predefinido, se não houver um (deploy.cmd, .deployidos) na pasta raiz da aplicação web.
  3. Executa um script de implementação no qual cria um ficheiro iisnode.yml se mencionar a versão Node.js no ficheiro pacote.json > motor`"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. O ficheiro iisnode.yml tem a seguinte linha de código:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Vejo a mensagem "Error estabelecendo uma ligação de base de dados" na minha aplicação WordPress que está hospedada no Serviço de Aplicações. Como é que eu consigo resolver isto?

Se vir este erro na sua aplicação Azure WordPress, para ativar php_errors.log e debug.log, complete os passos detalhados nos registos de [erros do Enable WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Quando os registos estiverem ativados, reproduza o erro e verifique os registos para ver se está a ficar sem ligações:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Se vir este erro nos ficheiros de debug.log ou php_errors.log, a sua aplicação está a exceder o número de ligações. Se estiver hospedado no ClearDB, verifique o número de ligações disponíveis no seu plano de [serviço](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Como desbugilo uma aplicação Node.js que está hospedada no Serviço de Aplicações?

1.  Vá para a sua [consola Kudu.](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole)
2.  Aceda à pasta de registos de aplicações (D:\home\LogFiles\Application).
3.  No ficheiro logging_errors.txt, verifique se há conteúdo.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Como posso instalar módulos Python nativos numa aplicação web do App Service ou na aplicação API?

Alguns pacotes podem não ser instalados utilizando pip em Azure. O pacote pode não estar disponível no Índice de Pacotes Python, ou pode ser necessário um compilador (um compilador não está disponível no computador que está a executar a aplicação web no App Service). Para obter informações sobre a instalação de módulos nativos em aplicações web do App Service e aplicações API, consulte [instalar módulos Python no App Service](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Como posso implementar uma aplicação Django para o App Service utilizando o Git e a nova versão da Python?

Para obter informações sobre a instalação do Django, consulte [a implementação de uma aplicação Django para o App Service](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Onde estão localizados os ficheiros de registo tomcat?

Para o Azure Marketplace e implementações personalizadas:

* Localização da pasta: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Ficheiros de interesse:
    * catalina. *yyy-mm-dd*.log
    * anfitrião-gerente. *yyy-mm-dd*.log
    * hospedeiro local. *yyy-mm-dd*.log
    * gerente. *yyy-mm-dd*.log
    * site_access_log. *yyy-mm-dd*.log


Para **implementações** de definições de aplicativos do portal:

* Localização da pasta: D:\home\LogFiles
* Ficheiros de interesse:
    * catalina. *yyy-mm-dd*.log
    * anfitrião-gerente. *yyy-mm-dd*.log
    * hospedeiro local. *yyy-mm-dd*.log
    * gerente. *yyy-mm-dd*.log
    * site_access_log. *yyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Como posso resolver erros de ligação ao condutor da JDBC?

Pode ver a seguinte mensagem nos seus registos Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Para resolver o erro:

1. Remova o ficheiro sqljdbc*.jar da sua aplicação/pasta lib.
2. Se estiver a utilizar o servidor web Tomcat ou Azure Marketplace Tomcat personalizado, copie este ficheiro .jar para a pasta Tomcat lib.
3. Se estiver a permitir que a Java a partir do portal Azure (selecione o servidor **Java 1.8** > **Tomcat),** copie o ficheiro sqljdbc.* na pasta paralela à sua aplicação. Em seguida, adicione a seguinte definição de caminho de classe ao ficheiro web.config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Por que vejo erros quando tento copiar ficheiros de registo ao vivo?

Se tentar copiar ficheiros de registo ao vivo para uma aplicação Java (por exemplo, Tomcat), poderá ver este erro ftp:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A mensagem de erro pode variar, dependendo do cliente FTP.

Todas as aplicações java têm este problema de bloqueio. Apenas kudu suporta descarregar este ficheiro enquanto a aplicação está em execução.

Parar a aplicação permite o acesso ftp a estes ficheiros.

Outra sobra é escrever um WebJob que executa um horário e copia estes ficheiros para um diretório diferente. Para um projeto de amostra, consulte o projeto [CopyLogsJob.](https://github.com/kamilsykora/CopyLogsJob)

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Onde encontro os ficheiros de registo do Jetty?

Para marketplace e implementações personalizadas, o ficheiro de registo está na pasta D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs pasta. Note que a localização da pasta depende da versão do Jetty que está a utilizar. Por exemplo, o caminho aqui fornecido é para Jetty 9.1.2. Procure jetty_*YYYY_MM_DD*.stderrout.log.

Para implementações de definição de aplicativos do portal, o ficheiro de registo está em D:\home\LogFiles. Procure jetty_*YYYY_MM_DD*.stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Posso enviar e-mail da minha aplicação web Azure?

O Serviço de Aplicações não tem uma funcionalidade de e-mail incorporada. Para obter algumas boas alternativas para enviar e-mail da sua aplicação, consulte esta [discussão do Stack Overflow](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Porque é que o meu site WordPress redireciona para outro URL?

Se emigrou recentemente para o Azure, o WordPress poderá redirecionar para o antigo URL de domínio. Isto é causado por uma definição na base de dados MySQL.

WordPress Buddy+ é uma extensão do site Azure que pode utilizar para atualizar o URL de reorientação diretamente na base de dados. Para obter mais informações sobre a utilização do WordPress Buddy+, consulte [as ferramentas WordPress e a migração MySQL com o WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

Em alternativa, se preferir atualizar manualmente o URL de reorientação utilizando consultas SQL ou PHPMyAdmin, consulte [WordPress: Redirecionamento para URL errado](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Como mudo a minha senha de inscrição no WordPress?

Se esqueceu a sua senha de entrada do WordPress, pode utilizar o WordPress Buddy+ para atualizá-la. Para redefinir a sua palavra-passe, instale a extensão do site WordPress Buddy+ Azure e, em seguida, complete os passos descritos nas [ferramentas WordPress e migração MySQL com WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Não posso inscrever-me no WordPress. Como posso resolver isto?

Se ficar bloqueado fora do WordPress depois de recentemente ter instalado um plugin, poderá ter um plugin defeituoso. WordPress Buddy+ é uma extensão do site Azure que pode ajudá-lo a desativar os plugins no WordPress. Para mais informações, consulte [as ferramentas WordPress e a migração MySQL com o WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Como migrar a minha base de dados wordpress?

Tem várias opções para migrar a base de dados MySQL que está ligada ao seu website WordPress:

* Desenvolvedores: Utilize o pedido de [comando ou PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Não desenvolvedores: Use [WordPress Buddy+](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Como posso ajudar a tornar o WordPress mais seguro?

Para conhecer as melhores práticas de segurança para o WordPress, consulte [as melhores práticas para a segurança wordPress em Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Estou a tentar usar phPMyAdmin, e vejo a mensagem "Acesso negado". Como posso resolver isto?

Poderá experimentar este problema se a funcionalidade mySQL na aplicação ainda não estiver a funcionar nesta instância do Serviço de Aplicações. Para resolver o problema, tente aceder ao seu website. Isto inicia os processos necessários, incluindo o processo mySQL na aplicação. Para verificar se a aplicação mySQL está em execução, no Process Explorer, certifique-se de que mysqld.exe está listado nos processos.

Depois de garantir que a aplicação mySQL está em execução, tente utilizar phPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Obtenho um erro HTTP 403 quando tento importar ou exportar a minha base de dados mySQL na aplicação utilizando PHPMyadmin. Como posso resolver isto?

Se estiver a utilizar uma versão mais antiga do Chrome, poderá estar a experimentar um bug conhecido. Para resolver o problema, atualize para uma versão mais recente do Chrome. Tente também utilizar um navegador diferente, como o Internet Explorer ou o Microsoft Edge, onde o problema não ocorre.
