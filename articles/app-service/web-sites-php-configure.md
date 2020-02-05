---
title: Configure o tempo de execução php
description: Saiba como configurar a instalação PHP predefinida ou adicione uma instalação PHP personalizada para o Serviço de Aplicações Azure.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: c73fb55e485d0c92d27eac2ac197a81337b9d5e1
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/05/2020
ms.locfileid: "77016804"
---
# <a name="configure-php-in-azure-app-service"></a>Configure PHP no Serviço de Aplicações Azure

## <a name="introduction"></a>Introdução

Este guia mostra-lhe como configurar o tempo de execução de PHP incorporado para aplicações web, back ends móveis e aplicações API no [Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)fornecer um tempo de execução PHP personalizado e ativar extensões. Para utilizar o Serviço de Aplicações, inscreva-se para o [teste gratuito]. Para tirar o máximo partido deste guia, deve primeiro criar uma aplicação PHP no App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Como: Alterar a versão PHP incorporada

Por predefinição, o PHP 5.6 está instalado e imediatamente disponível para utilização quando criar uma aplicação do Serviço de Aplicações. A melhor maneira de ver a revisão de lançamento disponível, a sua configuração predefinida e as extensões ativadas é implementar um script que chame a função [phpinfo..]

As versões PHP 7.0 e PHP 7.2 também estão disponíveis, mas não estão ativas por padrão. Para atualizar a versão PHP, siga um destes métodos:

### <a name="azure-portal"></a>Portal do Azure

1. Navegue na sua aplicação no [portal Azure](https://portal.azure.com) e percorra a página **de Configuração.**

2. A partir da **Configuração,** selecione **Configurações Gerais** e escolha a nova versão PHP.

3. Clique no botão **Guardar** na parte superior da lâmina de **definições gerais.**

### <a name="azure-cli"></a>CLI do Azure 

Para utilizar a interface de linha de comando Azure, tem de [instalar o CLI Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no seu computador.

1. Abra o Terminal e faça login na sua conta.

        az login

1. Consulte a lista de tempos de execução suportados.

        az webapp list-runtimes | grep php

2. Defina a versão PHP para a aplicação.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. A versão PHP está agora definida. Pode confirmar estas definições:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como: Alterar as configurações php incorporadas

Para qualquer tempo de execução php incorporado, pode alterar qualquer uma das opções de configuração seguindo estes passos. (Para obter informações sobre as diretivas php.ini, consulte a [Lista de diretivas php.ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Alterar o PHP\_INI\_UTILIZADOR, PHP\_INI\_PERDIR, PHP\_INI\_todas as configurações de configuração

1. Adicione um ficheiro [.user.ini] ao seu diretório raiz.
1. Adicione as definições de configuração ao ficheiro `.user.ini` utilizando a mesma sintaxe que utilizaria num ficheiro `php.ini`. Por exemplo, se quisesse ligar a definição de `display_errors` e definir `upload_max_filesize` definição para 10M, o seu ficheiro `.user.ini` conteria este texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implemente a sua aplicação.
3. Reinicie a aplicação. (É necessário reiniciar porque a frequência com que o PHP lê `.user.ini` ficheiros é regida pela definição `user_ini.cache_ttl`, que é uma definição de nível de sistema e é de 300 segundos (5 minutos) por padrão. Reiniciar a aplicação obriga a PHP a ler as novas definições no ficheiro `.user.ini`.)

Como alternativa à utilização de um ficheiro `.user.ini`, pode utilizar a função [ini_set()] em scripts para definir opções de configuração que não são diretivas de nível de sistema.

### <a name="changing-php_ini_system-configuration-settings"></a>Alterando as definições de configuração do SISTEMA de\_PHP\_INI

1. Adicione uma Definição de App à sua app com a chave `PHP_INI_SCAN_DIR` e valor `d:\home\site\ini`
1. Crie um ficheiro `settings.ini` utilizando a Consola Kudu (http://&lt;nome do site&gt;.scm.azurewebsite.net) no diretório `d:\home\site\ini`.
1. Adicione as definições de configuração ao ficheiro `settings.ini` utilizando a mesma sintaxe que utilizaria num ficheiro `php.ini`. Por exemplo, se quisesse indicar a definição de `curl.cainfo` para um ficheiro `*.crt` e definir a definição de 'wincache.maxfilesize' para 512K, o seu ficheiro `settings.ini` conteria este texto:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Para recarregar as alterações, reinicie a sua aplicação.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como: Ativar extensões no tempo de funcionação padrão php

Como notado na secção anterior, a melhor maneira de ver a versão PHP padrão, a sua configuração predefinida e as extensões ativadas é implementar um script que chama [phpinfo..]. Para permitir extensões adicionais, seguindo estes passos:

### <a name="configure-via-ini-settings"></a>Configurar através das definições ini

1. Adicione um diretório `ext` ao diretório `d:\home\site`.
1. Coloque `.dll` ficheiros de extensão no diretório `ext` (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões são compatíveis com a versão padrão do PHP e são compatíveis com VC9 e não-thread-safe (nts).
1. Adicione uma Definição de App à sua app com a chave `PHP_INI_SCAN_DIR` e valor `d:\home\site\ini`
1. Crie um ficheiro `ini` em `d:\home\site\ini` chamado `extensions.ini`.
1. Adicione as definições de configuração ao ficheiro `extensions.ini` utilizando a mesma sintaxe que utilizaria num ficheiro `php.ini`. Por exemplo, se quisesse ativar as extensões MongoDB e XDebug, o seu ficheiro `extensions.ini` conteria este texto:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Reinicie a sua aplicação para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configurar através da definição de aplicativos

1. Adicione um diretório `bin` ao diretório de raiz.
2. Coloque `.dll` ficheiros de extensão no diretório `bin` (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões são compatíveis com a versão padrão do PHP e são compatíveis com VC9 e não-thread-safe (nts).
3. Implemente a sua aplicação.
4. Navegue na sua aplicação no portal Azure e clique na **secção Configuração** localizada abaixo da secção **Definições.**
5. A partir da lâmina **de configuração,** selecione Definições de **Aplicação**.
6. Na secção de definições de **Aplicação,** clique em **+ Nova definição** de aplicação e crie uma chave **PHP_EXTENSIONS.** O valor desta chave seria um caminho em relação à raiz do site: **bin\your-ext-file**.
7. Clique no botão **'Actualizar'** na parte inferior e, em seguida, clique em **Guardar** acima do separador de **definições de Aplicação.**

As extensões zend também são suportadas usando uma chave **PHP_ZENDEXTENSIONS.** Para permitir várias extensões, inclua uma lista separada de `.dll` ficheiros para o valor de definição da aplicação.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: Utilizar um tempo de funcionação php personalizado

Em vez do tempo de execução padrão php, o Serviço de Aplicações pode utilizar um tempo de execução PHP que fornece para executar scripts PHP. O tempo de execução que fornece pode ser configurado por um ficheiro `php.ini` que também fornece. Para utilizar um tempo de execução PHP personalizado com o Serviço de Aplicações, seguindo estes passos.

1. Obtenha uma versão compatível com PHP não-thread-safe, VC9 ou VC11 para Windows. As recentes versões de PHP para Windows podem ser encontradas aqui: [https://windows.php.net/download/]. Os lançamentos mais antigos podem ser encontrados no arquivo aqui: [https://windows.php.net/downloads/releases/archives/].
2. Modifique o ficheiro `php.ini` para o seu tempo de execução. Quaisquer configurações de configuração que sejam diretivas apenas de nível de sistema são ignoradas pelo Serviço de Aplicações. (Para obter informações sobre diretivas apenas de nível do sistema, consulte [Lista de diretivas php.ini]
3. Opcionalmente, adicione extensões ao seu tempo de execução PHP e ative-as no ficheiro `php.ini`.
4. Adicione um diretório `bin` ao seu diretório de raiz, e coloque o diretório que contém o seu tempo de execução php nele (por exemplo, `bin\php`).
5. Implemente a sua aplicação.
6. Navegue na sua aplicação no portal Azure e clique na lâmina de **Configuração.**
8. A partir da lâmina de **configuração,** selecione **mapeamentos de caminho**. 
9. Clique **+ Novo Manipulador** e adicione `*.php` ao campo de extensão e adicione o caminho ao `php-cgi.exe` executável no processador **Script**. Se colocar o seu tempo de execução php no diretório `bin` na raiz da sua aplicação, o caminho é `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Na parte inferior, clique em **Atualizar** para terminar de adicionar o mapeamento do manipulador.
11. Clique em **Guardar** para guardar as alterações.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Como: Ativar a automatização do compositor em Azure

Por padrão, o Serviço de Aplicações não faz nada com o compositor.json, se tiver um no seu projeto PHP. Se utilizar a [implementação git,](deploy-local-git.md)pode ativar o processamento do compositor.json durante `git push`, permitindo a extensão do Compositor.

> [!NOTE]
> Você pode votar no suporte de compositor de primeira classe no Serviço de [Aplicações aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Na lâmina da sua aplicação PHP no [portal Azure,](https://portal.azure.com)clique em **Ferramentas** > **Extensões**.

    ![Lâmina de configuração do portal Azure para permitir automatização do compositor em Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique em **Adicionar**e, em seguida, clique em **Compositor.**

    ![Adicione extensão de compositor para permitir a automatização do compositor em Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique **em OK** para aceitar termos legais. Clique em **OK** novamente para adicionar a extensão.

    A lâmina de **extensões instalada** mostra a extensão do Compositor.
    ![Aceitar termos legais para permitir automação de compositor estoiro em](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, numa janela terminal da sua máquina local, execute `git add`, `git commit`e `git push` à sua aplicação. Note que o Compositor está a instalar dependências definidas em compositor.json.

    ![Implantação de Git com automação de compositor em Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte o [PhP Developer Center](https://azure.microsoft.com/develop/php/).

[teste gratuito]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo..]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de diretivas php.ini]: https://www.php.net/manual/en/ini.list.php
[.user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set()]: https://www.php.net/manual/en/function.ini-set.php
[application-settings]: ./media/web-sites-php-configure/application-settings.png
[settings-button]: ./media/web-sites-php-configure/settings-button.png
[save-button]: ./media/web-sites-php-configure/save-button.png
[php-extensions]: ./media/web-sites-php-configure/php-extensions.png
[handler-mappings]: ./media/web-sites-php-configure/handler-mappings.png
[https://windows.php.net/download/]: https://windows.php.net/download/
[https://windows.php.net/downloads/releases/archives/]: https://windows.php.net/downloads/releases/archives/
[SETPHPVERCLI]: ./media/web-sites-php-configure/ChangePHPVersion-XPlatCLI.png
[GETPHPVERCLI]: ./media/web-sites-php-configure/ShowPHPVersion-XplatCLI.png
[SETPHPVERPS]: ./media/web-sites-php-configure/ChangePHPVersion-PS.png
[GETPHPVERPS]: ./media/web-sites-php-configure/ShowPHPVersion-PS.png
