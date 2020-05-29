---
title: Configurar runtime do PHP
description: Saiba como configurar a instalação PHP predefinida ou adicione uma instalação PHP personalizada para o Azure App Service.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 0605249ea0602b33e144fce8d0a77439c2077a2f
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/29/2020
ms.locfileid: "84170754"
---
# <a name="configure-php-in-azure-app-service"></a>Configure PHP no Azure App Service

## <a name="introduction"></a>Introdução

Este guia mostra-lhe como configurar o tempo de execução PHP incorporado para aplicações web e aplicações API no [Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)fornecer um tempo de execução PHP personalizado e ativar extensões. Para utilizar o Serviço de Aplicações, inscreva-se para o [teste gratuito.] Para obter o máximo deste guia, você deve primeiro criar uma aplicação PHP no Serviço de Aplicações.

## <a name="how-to-change-the-built-in-php-version"></a>Como: Alterar a versão PHP incorporada

Ao criar uma aplicação web, pode escolher a versão de PHP que será configurada. Consulte [o PHP no Serviço](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) de Aplicações para obter informações atualizadas das versões atualmente suportadas.

Para verificar a versão de tempo de execução existente da sua aplicação, pode implementar um script que chame a função [phpinfo().]

Para atualizar a versão PHP, siga um destes métodos:

### <a name="azure-portal"></a>Portal do Azure

1. Navegue pela sua aplicação no [portal Azure](https://portal.azure.com) e percorra a página **de Configuração.**

2. A partir da **Configuração**, selecione **Definições Gerais** e escolha a nova versão PHP.

3. Clique no botão **Guardar** na parte superior da lâmina **de definições gerais.**

### <a name="azure-cli"></a>CLI do Azure 

Para utilizar a Interface Azure Command-Line, tem de [instalar o Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no seu computador.

1. Abra o Terminal e faça login na sua conta.

        az login

1. Verifique a lista de tempos de execução suportados.

        az webapp list-runtimes | grep php

2. Desaprote a versão PHP para a aplicação.

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. A versão PHP está agora definida. Pode confirmar estas definições:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como: Alterar as configurações php incorporadas

Para qualquer tempo de execução PHP incorporado, pode alterar qualquer uma das opções de configuração seguindo estes passos. (Para obter informações sobre as diretivas php.ini, consulte [lista de diretivas php.ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Alteração de PHP \_ INI \_ USER, PHP \_ INI \_ PERDIR, PHP \_ INI \_ TODAS as configurações

1. Adicione um ficheiro [.user.ini] ao seu diretório de raiz.
1. Adicione as definições de configuração ao `.user.ini` ficheiro utilizando a mesma sintaxe que utilizaria num `php.ini` ficheiro. Por exemplo, se pretender ligar a `display_errors` definição e definir `upload_max_filesize` a definição para 10M, o seu `.user.ini` ficheiro conteria este texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implemente a sua aplicação.
3. Reinicie a aplicação. (O reinício é necessário porque a frequência com que o PHP lê `.user.ini` os ficheiros é regida pela `user_ini.cache_ttl` definição, que é uma definição de nível do sistema e é de 300 segundos (5 minutos) por defeito. Reiniciar a aplicação obriga o PHP a ler as novas definições no `.user.ini` ficheiro.)

Como alternativa à utilização de um `.user.ini` ficheiro, pode utilizar a função [ini_set()] em scripts para definir opções de configuração que não são diretivas de nível do sistema.

### <a name="changing-php_ini_system-configuration-settings"></a>Alterar as definições de configuração do SISTEMA PHP \_ INI \_

1. Adicione uma Configuração de Aplicativo à sua aplicação com a chave `PHP_INI_SCAN_DIR` e valor`d:\home\site\ini`
1. Crie um `settings.ini` ficheiro utilizando a Kudu Console (http:// &lt; nome do site &gt; .scm.azurewebsite.net) no `d:\home\site\ini` diretório.
1. Adicione as definições de configuração ao `settings.ini` ficheiro utilizando a mesma sintaxe que utilizaria num `php.ini` ficheiro. Por exemplo, se pretender apontar `curl.cainfo` a definição para um `*.crt` ficheiro e definir a definição 'wincache.maxfilesize' para 512 K, o seu `settings.ini` ficheiro conteria este texto:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Para recarregar as alterações, reinicie a sua aplicação.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como: Permitir extensões no tempo de funcionação php predefinido

Como indicado na secção anterior, a melhor maneira de ver a versão PHP predefinida, a sua configuração padrão e as extensões ativadas é implementar um script que chama [phpinfo()]. Para permitir extensões adicionais, seguindo estes passos:

### <a name="configure-via-ini-settings"></a>Configurar através das definições ini

1. Adicione um `ext` diretório ao `d:\home\site` diretório.
1. Coloque `.dll` os ficheiros de extensão no `ext` diretório (por exemplo, `php_xdebug.dll` ). Certifique-se de que as extensões são compatíveis com a versão padrão de PHP e são compatíveis com VC9 e não-resistentes a fios(nts).
1. Adicione uma Configuração de Aplicativo à sua aplicação com a chave `PHP_INI_SCAN_DIR` e valor`d:\home\site\ini`
1. Crie um `ini` ficheiro `d:\home\site\ini` chamado `extensions.ini` .
1. Adicione as definições de configuração ao `extensions.ini` ficheiro utilizando a mesma sintaxe que utilizaria num `php.ini` ficheiro. Por exemplo, se quisesse ativar as extensões MongoDB e XDebug, o seu `extensions.ini` ficheiro conteria este texto:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Reinicie a sua aplicação para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configuração através da Definição de Aplicativos

1. Adicione um `bin` diretório ao diretório de raiz.
2. Coloque `.dll` os ficheiros de extensão no `bin` diretório (por exemplo, `php_xdebug.dll` ). Certifique-se de que as extensões são compatíveis com a versão padrão de PHP e são compatíveis com VC9 e não-resistentes a fios(nts).
3. Implemente a sua aplicação.
4. Navegue pela sua aplicação no portal Azure e clique na **secção Configuração** localizada abaixo da secção **Definições.**
5. A partir da lâmina **de configuração,** selecione **Definições de aplicação**.
6. Na secção definições de **Aplicação,** clique na **definição de aplicação + nova** e crie uma chave **PHP_EXTENSIONS.** O valor desta chave seria um caminho relativo à raiz do site: **bin\your-ext-file**.
7. Clique no botão **'Actualização'** na parte inferior e clique em **Guardar** acima do separador **Definições de Aplicação.**

As extensões Zend também são suportadas usando uma chave **PHP_ZENDEXTENSIONS.** Para ativar várias extensões, inclua uma lista de ficheiros separados por vírgula `.dll` para o valor de definição da aplicação.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: Use um tempo de execução PHP personalizado

Em vez do tempo de funcionação php predefinido, o Serviço de Aplicações pode utilizar um tempo de execução PHP que fornece para executar scripts PHP. O tempo de execução que fornece pode ser configurado por um `php.ini` ficheiro que também fornece. Para utilizar um tempo de funcionação PHP personalizado com o Serviço de Aplicações, seguindo estes passos.

1. Obtenha uma versão compatível com VC9 ou VC11 não compatível com o Windows. As recentes versões de PHP para Windows podem ser consultadas aqui: [https://windows.php.net/download/] . Versões antigas podem ser encontradas no arquivo aqui: [https://windows.php.net/downloads/releases/archives/] .
2. Modifique o `php.ini` ficheiro para o seu tempo de funcionamento. Quaisquer definições de configuração que sejam diretivas apenas para o sistema são ignoradas pelo Serviço de Aplicações. (Para obter informações sobre diretivas apenas a nível do sistema, consulte [lista das diretivas php.ini]).
3. Opcionalmente, adicione extensões ao seu tempo de funcionamento PHP e ative-as no `php.ini` ficheiro.
4. Adicione um `bin` diretório ao seu diretório de raiz e coloque o diretório que contém o seu tempo de funcionação PHP nele (por exemplo, `bin\php` ).
5. Implemente a sua aplicação.
6. Navegue pela sua aplicação no portal Azure e clique na lâmina **de configuração.**
8. A partir da lâmina **de configuração,** selecione **mapeamentos path**. 
9. Clique **em + Novo Manipulador** e adicione ao campo `*.php` extensão e adicione o caminho ao `php-cgi.exe` executável no processador **Script**. Se colocar o seu tempo de funcionamento php `bin` no diretório na raiz da sua aplicação, o caminho é `D:\home\site\wwwroot\bin\php\php-cgi.exe` .
10. Na parte inferior, clique em **Update** para terminar de adicionar o mapeamento do manipulador.
11. Clique em **Guardar** para guardar as alterações.

<a name="composer"></a>

## <a name="how-to-enable-composer-automation-in-azure"></a>Como: Permitir a automação do compositor em Azure

Por padrão, o App Service não faz nada com o compositor.json, se tiver um no seu projeto PHP. Se utilizar [a implementação do Git,](deploy-local-git.md)pode ativar o processamento do compositor.json durante `git push` a extensão do Compositor.

> [!NOTE]
> Você pode [votar no suporte do compositor de primeira classe no Serviço de Aplicações aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Na lâmina da sua aplicação PHP no [portal Azure,](https://portal.azure.com)clique em **Tools**  >  **Extensões de Ferramentas**.

    ![Lâmina de configuração do portal Azure para permitir a automatização do compositor em Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique **em Adicionar**e, em seguida, clique em **Compositor**.

    ![Adicionar extensão de Compositor para permitir a automatização do Compositor em Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique **em OK** para aceitar termos legais. Clique **em OK** novamente para adicionar a extensão.

    A lâmina **de extensões instalada** mostra a extensão do Compositor.
    ![Aceite termos legais para permitir a automação do Compositor em Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, numa janela terminal da sua máquina local, `git add` execute, `git commit` e para a `git push` sua aplicação. Note que o Compositor está a instalar dependências definidas em composer.json.

    ![Implantação de Git com automação compositora em Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Próximos passos

Para mais informações, consulte o [Centro de Desenvolvimento PHP.](https://azure.microsoft.com/develop/php/)

[julgamento livre]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
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
