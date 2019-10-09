---
title: Perguntas frequentes sobre tecnologias de software livre – serviço de Azure App | Microsoft Docs
description: Obtenha respostas para perguntas frequentes sobre tecnologias de software livre no recurso aplicativos Web do serviço Azure App.
services: app-service\web
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: top-support-issue
ms.assetid: 2fa5ee6b-51a6-4237-805f-518e6c57d11b
ms.service: app-service-web
ms.workload: web
ms.tgt_pltfrm: ibiza
ms.topic: article
ms.date: 10/31/2018
ms.author: genli
ms.custom: seodec18
ms.openlocfilehash: b92fe0a8073437d2cf0e908b4e734a0c01c342eb
ms.sourcegitcommit: aef6040b1321881a7eb21348b4fd5cd6a5a1e8d8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2019
ms.locfileid: "72169688"
---
# <a name="open-source-technologies-faqs-for-web-apps-in-azure"></a>Perguntas frequentes sobre tecnologias de software livre para aplicativos Web no Azure

Este artigo tem respostas para perguntas frequentes sobre problemas com tecnologias de software livre para o [recurso aplicativos Web do serviço de Azure app](https://azure.microsoft.com/services/app-service/web/).

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="how-do-i-turn-on-php-logging-to-troubleshoot-php-issues"></a>Como fazer ativar o log de PHP para solucionar problemas de PHP?

Para ativar o registro em log do PHP:

1. Entre no site do [kudu](https://*yourwebsitename*.scm.azurewebsites.net).
2. No menu superior, selecione **console de depuração** > **cmd**.
3. Selecione a pasta do **site** .
4. Selecione a pasta **wwwroot** .
5. Selecione o ícone **+** e, em seguida, selecione **novo arquivo**.
6. Defina o nome do arquivo para **. User. ini**.
7. Selecione o ícone de lápis ao lado de **. User. ini**.
8. No arquivo, adicione este código: `log_errors=on`
9. Selecione **Guardar**.
10. Selecione o ícone de lápis ao lado de **wp-config. php**.
11. Altere o texto para o código a seguir:
    ```php
    //Enable WP_DEBUG modedefine('WP_DEBUG', true);//Enable debug logging to /wp-content/debug.logdefine('WP_DEBUG_LOG', true);
    //Suppress errors and warnings to screendefine('WP_DEBUG_DISPLAY', false);//Suppress PHP errors to screenini_set('display_errors', 0);
    ```
12. No portal do Azure, no menu do aplicativo Web, reinicie o aplicativo Web.

Para obter mais informações, consulte [Habilitar logs de erros do WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

## <a name="how-do-i-log-python-application-errors-in-apps-that-are-hosted-in-app-service"></a>Como fazer log de erros de aplicativo Python em aplicativos hospedados no serviço de aplicativo?
[!INCLUDE [web-sites-python-troubleshooting-wsgi-error-log](../../includes/web-sites-python-troubleshooting-wsgi-error-log.md)]

## <a name="how-do-i-change-the-version-of-the-nodejs-application-that-is-hosted-in-app-service"></a>Como fazer alterar a versão do aplicativo node. js que está hospedado no serviço de aplicativo?

Para alterar a versão do aplicativo node. js, você pode usar uma das seguintes opções:

* No portal do Azure, use **as configurações do aplicativo**.
  1. No portal do Azure, acesse seu aplicativo Web.
  2. Na folha **configurações** , selecione **configurações do aplicativo**.
  3. Em **configurações do aplicativo**, você pode incluir WEBSITE_NODE_DEFAULT_VERSION como a chave e a versão do node. js que você deseja como o valor.
  4. Vá para o [console do kudu](https://*yourwebsitename*.scm.azurewebsites.net).
  5. Para verificar a versão do node. js, digite o seguinte comando:  
     ```
     node -v
     ```
* Modifique o arquivo iisnode. yml. Alterar a versão do node. js no arquivo iisnode. yml apenas define o ambiente de tempo de execução que o iisnode usa. Seu kudu cmd e outros ainda usam a versão do node. js que é definida nas **configurações do aplicativo** no portal do Azure.

  Para definir o iisnode. yml manualmente, crie um arquivo iisnode. yml na pasta raiz do aplicativo. No arquivo, inclua a seguinte linha:
  ```yml
  nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
  ```
   
* Defina o arquivo iisnode. yml usando Package. JSON durante a implantação do controle do código-fonte.
  O processo de implantação do controle do código-fonte do Azure envolve as seguintes etapas:
  1. Move o conteúdo para o aplicativo Web do Azure.
  2. Cria um script de implantação padrão, se não houver um (arquivos Deploy. cmd e. Deployment) na pasta raiz do aplicativo Web.
  3. Executa um script de implantação no qual ele cria um arquivo iisnode. yml se você mencionar a versão do node. js no arquivo Package. JSON > mecanismo `"engines": {"node": "5.9.1","npm": "3.7.3"}`
  4. O arquivo iisnode. yml tem a seguinte linha de código:
      ```yml
      nodeProcessCommandLine: "D:\Program Files (x86)\nodejs\5.9.1\node.exe"
      ```

## <a name="i-see-the-message-error-establishing-a-database-connection-in-my-wordpress-app-thats-hosted-in-app-service-how-do-i-troubleshoot-this"></a>Vejo a mensagem "erro ao estabelecer uma conexão de banco de dados" em meu aplicativo WordPress que está hospedado no serviço de aplicativo. Como fazer solucionar problemas disso?

Se você vir esse erro em seu aplicativo do Azure WordPress, para habilitar o php_errors. log e o Debug. log, conclua as etapas detalhadas em [Habilitar logs de erros do WordPress](https://blogs.msdn.microsoft.com/azureossds/2015/10/09/logging-php-errors-in-wordpress-2/).

Quando os logs estiverem habilitados, reproduza o erro e, em seguida, verifique os logs para ver se você está ficando sem conexões:
```
[09-Oct-2015 00:03:13 UTC] PHP Warning: mysqli_real_connect(): (HY000/1226): User ‘abcdefghijk79' has exceeded the ‘max_user_connections’ resource (current value: 4) in D:\home\site\wwwroot\wp-includes\wp-db.php on line 1454
```

Se você vir esse erro nos arquivos debug. log ou php_errors. log, seu aplicativo estará excedendo o número de conexões. Se você estiver hospedando no ClearDB, verifique o número de conexões que estão disponíveis no seu [plano de serviço](https://www.cleardb.com/pricing.view).

## <a name="how-do-i-debug-a-nodejs-app-thats-hosted-in-app-service"></a>Como fazer depurar um aplicativo node. js hospedado no serviço de aplicativo?

1.  Vá para o [console do kudu](https://*yourwebsitename*.scm.azurewebsites.net/DebugConsole).
2.  Vá para a pasta logs de aplicativos (D:\home\LogFiles\Application).
3.  No arquivo logging_errors. txt, verifique o conteúdo.

## <a name="how-do-i-install-native-python-modules-in-an-app-service-web-app-or-api-app"></a>Como fazer instalar módulos Python nativos em um aplicativo Web do serviço de aplicativo ou aplicativo de API?

Alguns pacotes podem não ser instalados usando Pip no Azure. O pacote pode não estar disponível no índice do pacote Python ou um compilador pode ser necessário (um compilador não está disponível no computador que está executando o aplicativo Web no serviço de aplicativo). Para obter informações sobre como instalar módulos nativos em aplicativos Web do serviço de aplicativo e aplicativos de API, consulte [instalar módulos Python no serviço de aplicativo](https://blogs.msdn.microsoft.com/azureossds/2015/06/29/install-native-python-modules-on-azure-web-apps-api-apps/).

## <a name="how-do-i-deploy-a-django-app-to-app-service-by-using-git-and-the-new-version-of-python"></a>Como fazer implantar um aplicativo Django no serviço de aplicativo usando o git e a nova versão do Python?

Para obter informações sobre como instalar o Django, consulte [implantando um aplicativo Django no serviço de aplicativo](https://blogs.msdn.microsoft.com/azureossds/2016/08/25/deploying-django-app-to-azure-app-services-using-git-and-new-version-of-python/).

## <a name="where-are-the-tomcat-log-files-located"></a>Onde estão localizados os arquivos de log do Tomcat?

Para o Azure Marketplace e implantações personalizadas:

* Local da pasta: D:\home\site\wwwroot\bin\apache-tomcat-8.0.33\logs
* Arquivos de interesse:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log


Para implantações de **configurações do aplicativo** do portal:

* Local da pasta: D:\home\LogFiles
* Arquivos de interesse:
    * catalina.*yyyy-mm-dd*.log
    * host-manager.*yyyy-mm-dd*.log
    * localhost.*yyyy-mm-dd*.log
    * manager.*yyyy-mm-dd*.log
    * site_access_log.*yyyy-mm-dd*.log

## <a name="how-do-i-troubleshoot-jdbc-driver-connection-errors"></a>Como fazer solucionar problemas de erros de conexão do driver JDBC?

Você pode ver a seguinte mensagem em seus logs do Tomcat:

```
The web application[ROOT] registered the JDBC driver [com.mysql.jdbc.Driver] but failed to unregister it when the web application was stopped. To prevent a memory leak,the JDBC Driver has been forcibly unregistered
```

Para resolver o erro:

1. Remova o arquivo sqljdbc*. jar da sua pasta app/lib.
2. Se você estiver usando o Tomcat personalizado ou o servidor Web Tomcat do Azure Marketplace, copie esse arquivo. jar para a pasta lib do Tomcat.
3. Se você estiver habilitando o Java do portal do Azure (selecione **java 1,8** > **servidor Tomcat**), copie o arquivo JAR sqljdbc. * na pasta que é paralela ao seu aplicativo. Em seguida, adicione a seguinte configuração de classpath ao arquivo Web. config:

    ```xml
    <httpPlatform>
    <environmentVariables>
    <environmentVariablename ="JAVA_OPTS" value=" -Djava.net.preferIPv4Stack=true
    -Xms128M -classpath %CLASSPATH%;[Path to the sqljdbc*.jarfile]" />
    </environmentVariables>
    </httpPlatform>
    ```

## <a name="why-do-i-see-errors-when-i-attempt-to-copy-live-log-files"></a>Por que vejo erros quando tento copiar arquivos de log em tempo real?

Se você tentar copiar arquivos de log em tempo real para um aplicativo Java (por exemplo, Tomcat), você poderá ver esse erro de FTP:

```
Error transferring file [filename] Copying files from remote side failed.
    
The process cannot access the file because it is being used by another process.
```

A mensagem de erro pode variar, dependendo do cliente FTP.

Todos os aplicativos Java têm esse problema de bloqueio. Somente o kudu dá suporte ao download desse arquivo enquanto o aplicativo está em execução.

Parar o aplicativo permite o acesso de FTP a esses arquivos.

Outra solução alternativa é escrever um WebJob que é executado em um agendamento e copia esses arquivos para um diretório diferente. Para um projeto de exemplo, consulte o projeto [CopyLogsJob](https://github.com/kamilsykora/CopyLogsJob) .

## <a name="where-do-i-find-the-log-files-for-jetty"></a>Onde encontro os arquivos de log para Jetty?

Para implantações de Marketplace e personalizadas, o arquivo de log está na pasta D:\home\site\wwwroot\bin\jetty-Distribution-9.1.2.v20140210\logs. Observe que o local da pasta depende da versão do Jetty que você está usando. Por exemplo, o caminho fornecido aqui é para Jetty 9.1.2. Procure jetty_*YYYY_MM_DD*. stderrout. log.

Para implantações de configuração de aplicativo do portal, o arquivo de log está em D:\home\LogFiles. Procure por jetty_*YYYY_MM_DD*. stderrout. log

## <a name="can-i-send-email-from-my-azure-web-app"></a>Posso enviar email do meu aplicativo Web do Azure?

O serviço de aplicativo não tem um recurso de email interno. Para obter algumas boas alternativas para enviar email de seu aplicativo, consulte esta [Stack Overflow discussão](https://stackoverflow.com/questions/17666161/sending-email-from-azure).

## <a name="why-does-my-wordpress-site-redirect-to-another-url"></a>Por que meu site do WordPress é redirecionado para outra URL?

Se você migrou recentemente para o Azure, o WordPress pode redirecionar para a URL de domínio antiga. Isso é causado por uma configuração no banco de dados MySQL.

O WordPress Buddy + é uma extensão de site do Azure que você pode usar para atualizar a URL de redirecionamento diretamente no banco de dados. Para obter mais informações sobre como usar o WordPress Buddy +, consulte [Ferramentas do WordPress e migração do MySQL com WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

Como alternativa, se você preferir atualizar manualmente a URL de redirecionamento usando consultas SQL ou PHPMyAdmin, consulte [WordPress: Redirecionando para a URL incorreta @ no__t-0.

## <a name="how-do-i-change-my-wordpress-sign-in-password"></a>Como fazer alterar minha senha de entrada do WordPress?

Se você esqueceu sua senha de entrada do WordPress, você pode usar o WordPress Buddy + para atualizá-la. Para redefinir sua senha, instale o WordPress Buddy + extensão de site do Azure e conclua as etapas descritas em [Ferramentas do WordPress e migração do MySQL com WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="i-cant-sign-in-to-wordpress-how-do-i-resolve-this"></a>Não consigo entrar no WordPress. Como fazer resolver isso?

Se você estiver bloqueado fora do WordPress após a instalação recente de um plug-in, você poderá ter um plug-in com falha. O WordPress Buddy + é uma extensão de site do Azure que pode ajudá-lo a desabilitar plug-ins no WordPress. Para obter mais informações, consulte [Ferramentas do WordPress e migração do MySQL com WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/).

## <a name="how-do-i-migrate-my-wordpress-database"></a>Como fazer migrar meu banco de dados do WordPress?

Você tem várias opções para migrar o banco de dados MySQL que está conectado ao seu site do WordPress:

* Programadores: Use o [prompt de comando ou phpMyAdmin](https://blogs.msdn.microsoft.com/azureossds/2016/03/02/migrating-data-between-mysql-databases-using-kudu-console-azure-app-service/)
* Não desenvolvedores: Usar o [WordPress Buddy +](https://sharepointforum.org/threads/wordpress-tools-and-mysql-migration-with-wordpress-buddy.82929/)

## <a name="how-do-i-help-make-wordpress-more-secure"></a>Como fazer ajudar a tornar o WordPress mais seguro?

Para saber mais sobre as práticas recomendadas de segurança para o WordPress, consulte [práticas recomendadas para segurança do WordPress no Azure](https://blogs.msdn.microsoft.com/azureossds/2016/12/26/best-practices-for-wordpress-security-on-azure/).

## <a name="i-am-trying-to-use-phpmyadmin-and-i-see-the-message-access-denied-how-do-i-resolve-this"></a>Estou tentando usar PHPMyAdmin e vejo a mensagem "acesso negado". Como fazer resolver isso?

Você poderá enfrentar esse problema se o recurso MySQL no aplicativo ainda não estiver em execução nesta instância do serviço de aplicativo. Para resolver o problema, tente acessar seu site. Isso inicia os processos necessários, incluindo o processo de MySQL no aplicativo. Para verificar se o MySQL no aplicativo está em execução, no Gerenciador de processos, verifique se o mysqld. exe está listado nos processos.

Depois de garantir que o MySQL no aplicativo está em execução, tente usar PHPMyAdmin.

## <a name="i-get-an-http-403-error-when-i-try-to-import-or-export-my-mysql-in-app-database-by-using-phpmyadmin-how-do-i-resolve-this"></a>Recebo um erro HTTP 403 ao tentar importar ou exportar meu banco de dados de MySQL no aplicativo usando o PHPMyadmin. Como fazer resolver isso?

Se você estiver usando uma versão mais antiga do Chrome, talvez esteja experimentando um bug conhecido. Para resolver o problema, atualize para uma versão mais recente do Chrome. Além disso, tente usar um navegador diferente, como o Internet Explorer ou o Microsoft Edge, em que o problema não ocorre.
