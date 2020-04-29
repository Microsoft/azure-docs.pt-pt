---
title: Configurar runtime do PHP
description: Saiba como configurar a instalação PHP predefinida ou adicione uma instalação PHP personalizada para o Serviço de Aplicações Azure.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/13/2020
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 208f4f7b4c2d8562d5237a40f52e4774ea5c5606
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272479"
---
# <a name="configure-php-in-azure-app-service"></a>Configure PHP no Serviço de Aplicações Azure

## <a name="introduction"></a>Introdução

Este guia mostra-lhe como configurar o tempo de execução de PHP incorporado para aplicações web e aplicações API no [Azure App Service,](https://go.microsoft.com/fwlink/?LinkId=529714)fornecer um tempo de execução PHP personalizado e ativar extensões. Para utilizar o Serviço de Aplicações, inscreva-se para o [teste gratuito]. Para tirar o máximo partido deste guia, deve primeiro criar uma aplicação PHP no App Service.

## <a name="how-to-change-the-built-in-php-version"></a>Como: Alterar a versão PHP incorporada

Ao criar uma aplicação web, pode escolher a versão de PHP que será configurada. Consulte [php no Serviço](https://github.com/Azure/app-service-linux-docs/blob/master/Runtime_Support/php_support.md) de Aplicações para obter informações atualizadas de versões atualmente suportadas.

Para verificar a versão de tempo de execução existente da sua aplicação, pode implementar um script que chame a função [phpinfo() ()]

Para atualizar a versão PHP, siga um destes métodos:

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

        az webapp config set --php-version {5.6 | 7.2 | 7.3} --name {app-name} --resource-group {resource-group-name}

3. A versão PHP está agora definida. Pode confirmar estas definições:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como: Alterar as configurações php incorporadas

Para qualquer tempo de execução php incorporado, pode alterar qualquer uma das opções de configuração seguindo estes passos. (Para obter informações sobre as diretivas php.ini, consulte a [lista das diretivas php.ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Alterando\_as\_definições de\_configuração PHP INI USER, PHP INI\_PERDIR, PHP\_INI\_ALL

1. Adicione um ficheiro [.user.ini] ao seu diretório raiz.
1. Adicione as definições de configuração ao `.user.ini` ficheiro utilizando `php.ini` a mesma sintaxe que utilizaria num ficheiro. Por exemplo, se quisesse ligar `display_errors` a `upload_max_filesize` definição e definir `.user.ini` a definição para 10M, o seu ficheiro conteria este texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implemente a sua aplicação.
3. Reinicie a aplicação. (É necessário reiniciar porque a frequência com `.user.ini` que o PHP lê ficheiros é regida pela `user_ini.cache_ttl` definição, que é uma definição de nível de sistema e é de 300 segundos (5 minutos) por padrão. Reiniciar a aplicação obriga a PHP `.user.ini` a ler as novas definições no ficheiro.)

Como alternativa à `.user.ini` utilização de um ficheiro, pode utilizar a função [ini_set()] em scripts para definir opções de configuração que não são diretivas ao nível do sistema.

### <a name="changing-php_ini_system-configuration-settings"></a>Alterar as\_definições de configuração do SISTEMA PHP INI\_

1. Adicione uma Definição de App `PHP_INI_SCAN_DIR` à sua app com a chave e valor`d:\home\site\ini`
1. Crie `settings.ini` um ficheiro utilizando&lt;a Consola&gt;Kudu (http:// nome do site .scm.azurewebsite.net) no `d:\home\site\ini` diretório.
1. Adicione as definições de configuração ao `settings.ini` ficheiro utilizando `php.ini` a mesma sintaxe que utilizaria num ficheiro. Por exemplo, se quisesse `curl.cainfo` indicar a `*.crt` definição para um ficheiro e definir a definição `settings.ini` de 'wincache.maxfilesize' para 512 K, o seu ficheiro conteria este texto:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Para recarregar as alterações, reinicie a sua aplicação.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como: Ativar extensões no tempo de funcionação padrão php

Como notado na secção anterior, a melhor maneira de ver a versão PHP padrão, a sua configuração predefinida e as extensões ativadas é implementar um script que chama [phpinfo()]. Para permitir extensões adicionais, seguindo estes passos:

### <a name="configure-via-ini-settings"></a>Configurar através das definições ini

1. Adicione `ext` um diretório `d:\home\site` ao diretório.
1. Coloque `.dll` ficheiros `ext` de extensão no `php_xdebug.dll`diretório (por exemplo, ). Certifique-se de que as extensões são compatíveis com a versão padrão do PHP e são compatíveis com VC9 e não-thread-safe (nts).
1. Adicione uma Definição de App `PHP_INI_SCAN_DIR` à sua app com a chave e valor`d:\home\site\ini`
1. Crie `ini` um `d:\home\site\ini` `extensions.ini`ficheiro chamado .
1. Adicione as definições de configuração ao `extensions.ini` ficheiro utilizando `php.ini` a mesma sintaxe que utilizaria num ficheiro. Por exemplo, se quisesse ativar as extensões MongoDB `extensions.ini` e XDebug, o seu ficheiro conteria este texto:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Reinicie a sua aplicação para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configurar através da definição de aplicativos

1. Adicione `bin` um diretório ao diretório de raiz.
2. Coloque `.dll` ficheiros `bin` de extensão no `php_xdebug.dll`diretório (por exemplo, ). Certifique-se de que as extensões são compatíveis com a versão padrão do PHP e são compatíveis com VC9 e não-thread-safe (nts).
3. Implemente a sua aplicação.
4. Navegue na sua aplicação no portal Azure e clique na **secção Configuração** localizada abaixo da secção **Definições.**
5. A partir da lâmina **de configuração,** selecione Definições de **Aplicação**.
6. Na secção de definições de **Aplicação,** clique em **+ Nova definição** de aplicação e crie uma chave **PHP_EXTENSIONS.** O valor desta chave seria um caminho em relação à raiz do site: **bin\your-ext-file**.
7. Clique no botão **'Actualizar'** na parte inferior e, em seguida, clique em **Guardar** acima do separador de **definições de Aplicação.**

As extensões zend também são suportadas usando uma chave **PHP_ZENDEXTENSIONS.** Para permitir várias extensões, inclua uma `.dll` lista separada de ficheiros separados pela vírvia para o valor de definição da aplicação.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: Utilizar um tempo de funcionação php personalizado

Em vez do tempo de execução padrão php, o Serviço de Aplicações pode utilizar um tempo de execução PHP que fornece para executar scripts PHP. O tempo de execução que fornece `php.ini` pode ser configurado por um ficheiro que também fornece. Para utilizar um tempo de execução PHP personalizado com o Serviço de Aplicações, seguindo estes passos.

1. Obtenha uma versão compatível com PHP não-thread-safe, VC9 ou VC11 para Windows. Os recentes lançamentos de PHP para [https://windows.php.net/download/]Windows podem ser encontrados aqui: . Os lançamentos mais antigos podem [https://windows.php.net/downloads/releases/archives/]ser encontrados no arquivo aqui: .
2. Modifique `php.ini` o ficheiro para o seu tempo de execução. Quaisquer configurações de configuração que sejam diretivas apenas de nível de sistema são ignoradas pelo Serviço de Aplicações. (Para obter informações sobre diretivas apenas de nível do sistema, consulte [a lista das diretivas php.ini).]
3. Opcionalmente, adicione extensões ao seu tempo de `php.ini` execução PHP e ative-as no ficheiro.
4. Adicione `bin` um diretório ao seu diretório de raiz e coloque o diretório que `bin\php`contém o seu tempo de execução php nele (por exemplo, ).
5. Implemente a sua aplicação.
6. Navegue na sua aplicação no portal Azure e clique na lâmina de **Configuração.**
8. A partir da lâmina de **configuração,** selecione **mapeamentos de caminho**. 
9. Clique **+ Novo** `*.php` Manipulador e adicione ao campo `php-cgi.exe` De extensão e adicione o caminho ao executável no **processador Script**. Se colocar o `bin` seu tempo de execução php no diretório `D:\home\site\wwwroot\bin\php\php-cgi.exe`na raiz da sua aplicação, o caminho é .
10. Na parte inferior, clique em **Atualizar** para terminar de adicionar o mapeamento do manipulador.
11. Clique em **Guardar** para guardar as alterações.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Como: Ativar a automatização do compositor em Azure

Por padrão, o Serviço de Aplicações não faz nada com o compositor.json, se tiver um no seu projeto PHP. Se utilizar a [implementação git,](deploy-local-git.md)pode ativar `git push` o processamento do compositor.json durante a extensão do Compositor.

> [!NOTE]
> Você pode votar no suporte de compositor de primeira classe no Serviço de [Aplicações aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Na lâmina da sua aplicação PHP no [portal Azure,](https://portal.azure.com)clique em**Extensões**de **Ferramentas** > .

    ![Lâmina de configuração do portal Azure para permitir automatização do compositor em Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique em **Adicionar**e, em seguida, clique em **Compositor.**

    ![Adicione extensão de compositor para permitir a automatização do compositor em Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique **em OK** para aceitar termos legais. Clique em **OK** novamente para adicionar a extensão.

    A lâmina de **extensões instalada** mostra a extensão do Compositor.
    ![Aceitar termos legais para permitir automação de compositor em Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, numa janela terminal da sua `git add` `git commit`máquina `git push` local, execute, e para a sua aplicação. Note que o Compositor está a instalar dependências definidas em compositor.json.

    ![Implantação de Git com automação de compositor em Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Passos seguintes

Para mais informações, consulte o [PhP Developer Center](https://azure.microsoft.com/develop/php/).

[avaliação gratuita]: https://www.windowsazure.com/pricing/free-trial/
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
