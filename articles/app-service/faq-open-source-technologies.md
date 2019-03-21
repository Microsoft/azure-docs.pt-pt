---
title: Tecnologias de open source FAQs - serviço de aplicações do Azure | Documentos da Microsoft
description: Obtenha respostas para perguntas freqüentes sobre tecnologias de código aberto na funcionalidade aplicações Web do serviço de aplicações do Azure.
services: app-service\web
documentationcenter: ''
author: genlin
manager: cshepard
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: 7831e5e989835b2c9432dbd61a242584a7b6244d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "58082947"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Tecnologias de open source FAQs para aplicações Web no Azure

Este artigo tem respostas a perguntas mais frequentes (FAQ) sobre problemas com as tecnologias de código-fonte aberto para o [funcionalidade de aplicações Web do serviço de aplicações do Azure](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Como ativar no PHP, o registo para resolver problemas PHP

Para ativar o registo do PHP:

1. Inicie sessão no seu [Web site do Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. No menu superior, selecione **consola de depuração** > **CMD**.
3. Selecione o **Site** pasta.
4. Selecione o **wwwroot** pasta.
5. Selecione o **+** ícone e, em seguida, selecione **novo ficheiro**.
6. Defina o nome de ficheiro **. user.ini**.
7. Selecione o ícone de lápis junto a **. user.ini**.
8. No ficheiro, adicione este código: `log_errors=on`
9. Selecione **Guardar**.
10. Selecione o ícone de lápis junto a **wp-config.php**.
11. Altere o texto para o código a seguir:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. No portal do Azure, no menu da aplicação web, reinicie a aplicação web.

Para obter mais informações, consulte [registos de erros do WordPress ativar](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Como posso fazer logon erros de aplicações Python nas aplicações que estão alojadas no serviço de aplicações?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Como posso alterar a versão da aplicação node. js que está alojada no serviço de aplicações?

Para alterar a versão da aplicação node. js, pode usar uma das seguintes opções:

* No portal do Azure, utilize **as definições da aplicação**.
  1. No portal do Azure, aceda à sua aplicação web.
  2. Sobre o **definições** painel, selecione **as definições da aplicação**.
  3. Na **as definições da aplicação**, pode incluir WEBSITE_NODE_DEFAULT_VERSION como a chave e a versão do node. js que quiser como o valor.
  4. Aceda ao seu [consola Kudu](https://*yourwebsitename*.scm.azurewebsites.net).
  5. Para verificar a versão do node. js, introduza o seguinte comando:  
     ```
     node -v
     ```
* Modificar o ficheiro iisnode. Alterar a versão do node. js no ficheiro iisnode apenas define o ambiente de runtime que o iisnode utiliza. Sua cmd Kudu e outros continuar a utilizar a versão do node. js que está definida no **as definições da aplicação** no portal do Azure.

  Para definir o iisnode manualmente, crie um ficheiro iisnode na sua pasta de raiz da aplicação. No ficheiro, incluem a seguinte linha:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Defina o ficheiro iisnode utilizando Package. JSON durante a implementação de controlo de origem.
  O processo de implementação do controlo de origem do Azure envolve os seguintes passos:
  1. Move o conteúdo para a aplicação web do Azure.
  2. Cria um script de implementação do padrão, se não existir um (cmd,. Deployment ficheiros) na pasta de raiz de aplicação web.
  3. Executa um script de implementação em que cria um ficheiro iisnode se mencionou que a versão do node. js no ficheiro Package. JSON > motor `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. O ficheiro iisnode tem a seguinte linha de código:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Posso ver a mensagem "Erro ao estabelecer uma ligação de base de dados" em meu aplicativo de WordPress que está alojado no serviço de aplicações. Como posso resolver isto?

Se vir este erro na sua aplicação do WordPress do Azure, para permitir php_errors.log e Debug. log, complete os passos detalhados no [registos de erros do WordPress ativar](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Quando os registos estão ativados, reproduzir o erro e, em seguida, verifique os registos para ver se estão a ficar sem ligações:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Se vir este erro nos arquivos Debug. log ou php_errors.log, a aplicação está a exceder o número de ligações. Se estiver hospedando em ClearDB, verifique se o número de ligações que estão disponíveis no seu [plano do serviço](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Como posso depurar uma aplicação node. js que está alojada no serviço de aplicações?

1.  Aceda ao seu [consola Kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Aceda à pasta de registos de aplicação (D:\home\LogFiles\Application).
3.  No ficheiro logging_errors.txt, verifique a existência de conteúdo.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Como posso instalar módulos de Python nativos de uma aplicação do serviço de aplicações web ou a aplicação de API?

Alguns pacotes não poderão instalar através da utilização de pip no Azure. O pacote pode não estar disponível no índice de pacote Python ou um compilador poderá ser necessário (um compilador não está disponível no computador que está a executar a aplicação web no serviço de aplicações). Para obter informações sobre como instalar os módulos nativos em aplicações web do serviço de aplicações e API apps, consulte [módulos de instalar o Python no serviço de aplicações](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Como implementar uma aplicação Django no serviço de aplicações com o Git e a nova versão do Python?

Para obter informações sobre a instalação do Django, consulte [implementar uma aplicação Django no serviço de aplicações](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Onde estão localizados os ficheiros de registo do Tomcat?

Para o Azure Marketplace e implementações personalizadas:

* Localização da pasta: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Arquivos de interesse:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


Para o portal **as definições da aplicação** implementações:

* Localização da pasta: D:\home\LogFiles
* Arquivos de interesse:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Como posso resolver erros de ligação JDBC driver?

Poderá ver a seguinte mensagem nos seus registos de Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Para resolver o erro:

1. Remova o ficheiro de sqljdbc*.jar da sua pasta de aplicação/lib.
2. Se estiver a utilizar o servidor de web personalizado do Tomcat ou o Azure Marketplace Tomcat, copie este ficheiro. JAR para a pasta lib do Tomcat.
3. Se pretende ativar a Java do portal do Azure (selecione **Java 1.8** > **servidor Tomcat**), copie o ficheiro. jar sqljdbc.* na pasta que seja paralela a sua aplicação. Em seguida, adicione a seguinte definição de caminho da classe para o ficheiro Web. config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Por que vejo erros quando tento copiar ficheiros de registo em direto?

Se tentar copiar ficheiros de registo em direto para uma aplicação de Java (por exemplo, Tomcat), poderá ver este erro FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A mensagem de erro pode variar consoante o cliente FTP.

Todas as aplicações de Java têm este problema de bloqueio. Kudu apenas suporta a transferir este ficheiro enquanto a aplicação está em execução.

A parar a aplicação permite o acesso FTP a estes ficheiros.

Outra solução alternativa é escrever um WebJob que é executada com base numa agenda e copia esses arquivos para um diretório diferente. Para um projeto de exemplo, consulte a [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) projeto.

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Onde posso encontrar os ficheiros de registo para Jetty?

Para implementações personalizadas e de Marketplace, o ficheiro de registo está na pasta D:\home\site\wwwroot\bin\jetty-distribution-9.1.2.v20140210\logs. Tenha em atenção que a localização da pasta depende da versão de Jetty estiver a utilizar. Por exemplo, o caminho fornecido aqui é para Jetty 9.1.2. Procure jetty_*YYYY_MM_DD*. stderrout.log.

Para implementações de definição de aplicação do portais, o ficheiro de registo é no D:\home\LogFiles. Procure jetty_*YYYY_MM_DD*. stderrout.log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Pode enviar e-mails do meu aplicativo web do Azure?

Serviço de aplicações não tem uma funcionalidade de e-mail incorporado. Para algumas alternativas bom para o envio de e-mail da sua aplicação, veja isso [Stack Overflow discussão](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Por que motivo o meu site WordPress redirecionar para outra URL?

Se tiver recentemente migrado para o Azure, o WordPress pode redirecionar para o URL do domínio antigo. Esta situação é provocada por uma definição na base de dados MySQL.

WordPress Buddy + é uma extensão de Site do Azure que pode utilizar para atualizar o URL de redirecionamento diretamente na base de dados. Para obter mais informações sobre como utilizar o WordPress Buddy +, consulte [WordPress ferramentas e migração do MySQL com o WordPress Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

Em alternativa, se preferir de atualizar manualmente o redirecionamento de URL com consultas SQL ou PHPMyAdmin, consulte [WordPress: Redirecionamento de URL incorreto](https://blogs.msdn.microsoft.com/azureossds/2016/07/12/wordpress-redirecting-to-wrong-url/).

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Como posso alterar minha senha de início de sessão WordPress?

Se se tiver esquecido a senha de início de sessão WordPress, pode utilizar WordPress Buddy + atualizá-la. Para repor a palavra-passe, instalar o WordPress Buddy + Azure extensão de Site e, em seguida, conclua os passos descritos em [WordPress ferramentas e migração do MySQL com o WordPress Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Não é possível iniciar sessão no WordPress. Como posso resolver isto?

Se impedido de aceder ao WordPress depois de instalar recentemente um plug-in, poderá ter um plug-in com falhas. WordPress Buddy + é uma extensão de Site do Azure que lhe permite desativar plug-ins no WordPress. Para obter mais informações, consulte [WordPress ferramentas e migração do MySQL com o WordPress Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Como posso migrar meu banco de dados do WordPress?

Tem várias opções para a migração de base de dados MySQL que está ligada ao seu Web site WordPress:

* Programadores: Utilize o [linha de comandos ou PHPMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Não-programadores: Utilize [WordPress Buddy +](https://blogs.msdn.microsoft.com/azureossds/2016/12/21/wordpress-tools-and-mysql-migration-with-wordpress-buddy/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Como ajudar a tornar o WordPress mais seguro?

Para saber mais sobre as melhores práticas de segurança para o WordPress, consulte [melhores práticas para segurança do WordPress no Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Estou a tentar utilizar PHPMyAdmin e vejo a mensagem "Acesso negado." Como posso resolver isto?

Podem ocorrer este problema se a funcionalidade do MySQL na aplicação não está ainda em execução nesta instância do serviço de aplicações. Para resolver o problema, tente fazer ao seu Web site. Esta ação inicia os processos necessários, incluindo o processo do MySQL na aplicação. Para verificar que o MySQL na aplicação está em execução, no Process Explorer, certifique-se de que mysqld.exe está listado nos processos.

Depois de assegurar que do MySQL na aplicação está em execução, tente utilizar PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Recebo um erro de HTTP 403 quando tento importar ou exportar o meu banco de dados do MySQL na aplicação através de PHPMyadmin. Como posso resolver isto?

Se estiver a utilizar uma versão anterior do Chrome, possam estar a ter um bug conhecido. Para resolver o problema, atualize para uma versão mais recente do Chrome. Também pode tente utilizar um browser diferente, como o Internet Explorer ou o Microsoft Edge, onde o problema não ocorre.
