---
title: Configurar o tempo de execução do PHP
description: Saiba como configurar a instalação padrão do PHP ou adicionar uma instalação personalizada do PHP para Azure App serviço.
author: msangapu-msft
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.devlang: php
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 2d35c31e23da7addcf0b4c341c6925f258d5c232
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/02/2019
ms.locfileid: "74688253"
---
# <a name="configure-php-in-azure-app-service"></a>Configurar PHP no serviço Azure App

## <a name="introduction"></a>Introdução

Este guia mostra como configurar o tempo de execução do PHP interno para aplicativos Web, back-ends móveis e aplicativos de API no [serviço Azure app](https://go.microsoft.com/fwlink/?LinkId=529714), fornecer um tempo de execução do PHP personalizado e habilitar extensões. Para usar o serviço de aplicativo, Inscreva-se para a [avaliação gratuita]. Para obter o máximo deste guia, primeiro você deve criar um aplicativo PHP no serviço de aplicativo.

## <a name="how-to-change-the-built-in-php-version"></a>Como: alterar a versão interna do PHP

Por padrão, o PHP 5,6 é instalado e imediatamente disponível para uso quando você cria um aplicativo do serviço de aplicativo. A melhor maneira de ver a revisão de versão disponível, sua configuração padrão e as extensões habilitadas é implantar um script que chama a função [phpinfo ()] .

As versões PHP 7,0 e PHP 7,2 também estão disponíveis, mas não são habilitadas por padrão. Para atualizar a versão do PHP, siga um destes métodos:

### <a name="azure-portal"></a>Portal do Azure

1. Navegue até seu aplicativo na [portal do Azure](https://portal.azure.com) e role até a página de **configuração** .

2. Em **configuração**, selecione **configurações gerais** e escolha a nova versão do php.

3. Clique no botão **salvar** na parte superior da folha **configurações gerais** .

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Abra Azure PowerShell e faça logon em sua conta:

        PS C:\> Connect-AzAccount
2. Defina a versão do PHP para o aplicativo.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
3. A versão do PHP agora está definida. Você pode confirmar essas configurações:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>CLI do Azure 

Para usar a interface de linha de comando do Azure, você deve [instalar o CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) em seu computador.

1. Abra o terminal e faça logon na sua conta.

        az login

1. Verifique para ver a lista de tempos de execução com suporte.

        az webapp list-runtimes | grep php

2. Defina a versão do PHP para o aplicativo.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. A versão do PHP agora está definida. Você pode confirmar essas configurações:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como alterar as configurações internas do PHP

Para qualquer tempo de execução interno do PHP, você pode alterar qualquer uma das opções de configuração seguindo estas etapas. (Para obter informações sobre diretivas php. ini, consulte [lista de diretivas php. ini].)

### <a name="changing-php_ini_user-php_ini_perdir-php_ini_all-configuration-settings"></a>Alterando o PHP\_INI\_usuário, PHP\_INI\_PERDIR, PHP\_INI\_todas as definições de configuração

1. Adicione um arquivo [. User. ini] ao seu diretório raiz.
1. Adicione definições de configuração ao arquivo de `.user.ini` usando a mesma sintaxe que você usaria em um arquivo de `php.ini`. Por exemplo, se você quisesse ativar a configuração de `display_errors` e definir `upload_max_filesize` configuração como 10 milhões, seu arquivo de `.user.ini` conteria esse texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implante seu aplicativo.
3. Reinicie a aplicação. (A reinicialização é necessária porque a frequência com a qual o PHP lê `.user.ini` arquivos é governada pela configuração de `user_ini.cache_ttl`, que é uma configuração de nível de sistema e é de 300 segundos (5 minutos) por padrão. Reiniciar o aplicativo força o PHP a ler as novas configurações no arquivo de `.user.ini`.)

Como alternativa ao uso de um arquivo de `.user.ini`, você pode usar a função [ini_set ()] em scripts para definir opções de configuração que não são diretivas de nível de sistema.

### <a name="changing-php_ini_system-configuration-settings"></a>Alteração do PHP\_INI\_definições de configuração do sistema

1. Adicione uma configuração de aplicativo ao seu aplicativo com a chave `PHP_INI_SCAN_DIR` e o valor `d:\home\site\ini`
1. Crie um arquivo de `settings.ini` usando o console do kudu (http://&lt;site-Name&gt;. scm.azurewebsite.net) no diretório `d:\home\site\ini`.
1. Adicione definições de configuração ao arquivo de `settings.ini` usando a mesma sintaxe que você usaria em um arquivo de `php.ini`. Por exemplo, se você quisesse apontar a configuração de `curl.cainfo` para um arquivo de `*.crt` e definir a configuração ' WinCache. MaxFileSize ' como 512K, seu arquivo de `settings.ini` conteria esse texto:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Para recarregar as alterações, reinicie o aplicativo.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como habilitar extensões no tempo de execução padrão do PHP

Conforme observado na seção anterior, a melhor maneira de ver a versão padrão do PHP, sua configuração padrão e as extensões habilitadas é implantar um script que chama [phpinfo ()]. Para habilitar extensões adicionais, seguindo estas etapas:

### <a name="configure-via-ini-settings"></a>Configurar por meio de configurações ini

1. Adicione um diretório de `ext` ao diretório `d:\home\site`.
1. Coloque `.dll` arquivos de extensão no diretório `ext` (por exemplo, `php_xdebug.dll`). Verifique se as extensões são compatíveis com a versão padrão do PHP e se são compatíveis com VC9 e não-thread-safe (NTS).
1. Adicione uma configuração de aplicativo ao seu aplicativo com a chave `PHP_INI_SCAN_DIR` e o valor `d:\home\site\ini`
1. Crie um arquivo de `ini` no `d:\home\site\ini` chamado `extensions.ini`.
1. Adicione definições de configuração ao arquivo de `extensions.ini` usando a mesma sintaxe que você usaria em um arquivo de `php.ini`. Por exemplo, se você quisesse habilitar as extensões MongoDB e XDebug, seu arquivo de `extensions.ini` conteria este texto:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Reinicie o aplicativo para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configurar por meio de configuração de aplicativo

1. Adicione um diretório de `bin` ao diretório raiz.
2. Coloque `.dll` arquivos de extensão no diretório `bin` (por exemplo, `php_xdebug.dll`). Verifique se as extensões são compatíveis com a versão padrão do PHP e se são compatíveis com VC9 e não-thread-safe (NTS).
3. Implante seu aplicativo.
4. Navegue até seu aplicativo no portal do Azure e clique na seção **configuração** localizada abaixo de **configurações** .
5. Na folha **configuração** , selecione **configurações do aplicativo**.
6. Na seção **configurações do aplicativo** , clique em **+ nova configuração de aplicativo** e crie uma chave de **PHP_EXTENSIONS** . O valor dessa chave seria um caminho relativo à raiz do site: **bin\your-ext-File**.
7. Clique no botão **Atualizar** na parte inferior e, em seguida, clique em **salvar** acima da guia **configurações do aplicativo** .

As extensões Zend também têm suporte usando uma chave de **PHP_ZENDEXTENSIONS** . Para habilitar várias extensões, inclua uma lista separada por vírgulas de arquivos de `.dll` para o valor de configuração do aplicativo.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: usar um tempo de execução do PHP personalizado

Em vez do tempo de execução padrão do PHP, o serviço de aplicativo pode usar um tempo de execução do PHP que você fornece para executar scripts PHP. O tempo de execução que você fornece pode ser configurado por um arquivo `php.ini` que você também fornece. Para usar um tempo de execução do PHP personalizado com o serviço de aplicativo, siga estas etapas.

1. Obtenha uma versão compatível com não thread-safe, VC9 ou VC11 do PHP para Windows. Versões recentes do PHP para Windows podem ser encontradas aqui: [https://windows.php.net/download/]. Versões mais antigas podem ser encontradas no arquivo morto aqui: [https://windows.php.net/downloads/releases/archives/].
2. Modifique o arquivo de `php.ini` para o tempo de execução. Quaisquer definições de configuração que são diretivas somente de nível de sistema são ignoradas pelo serviço de aplicativo. (Para obter informações sobre diretivas somente de nível de sistema, consulte [lista de diretivas php. ini]).
3. Opcionalmente, adicione extensões ao tempo de execução do PHP e habilite-as no arquivo de `php.ini`.
4. Adicione um diretório `bin` ao seu diretório raiz e coloque o diretório que contém o tempo de execução do PHP nele (por exemplo, `bin\php`).
5. Implante seu aplicativo.
6. Navegue até seu aplicativo no portal do Azure e clique na folha **configuração** .
8. Na folha **configuração** , selecione **mapeamentos de caminho**. 
9. Clique em **+ novo manipulador** e adicione `*.php` ao campo extensão e adicione o caminho para o `php-cgi.exe` executável no **processador de script**. Se você colocar o tempo de execução do PHP no diretório `bin` na raiz do seu aplicativo, o caminho será `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Na parte inferior, clique em **Atualizar** para concluir a adição do mapeamento do manipulador.
11. Clique em **Guardar** para guardar as alterações.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Como habilitar a automação do Composer no Azure

Por padrão, o serviço de aplicativo não faz nada com o Composer. JSON, se você tiver um em seu projeto PHP. Se você usar a [implantação do git](deploy-local-git.md), poderá habilitar o processamento do Composer. JSON durante a `git push` habilitando a extensão do compositor.

> [!NOTE]
> Você pode [votar no suporte do Composer de primeira classe no serviço de aplicativo aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. Na folha do aplicativo PHP na [portal do Azure](https://portal.azure.com), clique em **ferramentas** > **extensões**.

    ![portal do Azure folha configurações para habilitar a automação do Composer no Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique em **Adicionar**e em **Composer**.

    ![Adicionar extensão do Composer para habilitar a automação do Composer no Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique em **OK** para aceitar os termos legais. Clique em **OK** novamente para adicionar a extensão.

    A folha **extensões instaladas** mostra a extensão do Composer.
    ![aceitar termos legais para habilitar a automação do Composer no Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, em uma janela de terminal em seu computador local, execute `git add`, `git commit`e `git push` ao seu aplicativo. Observe que o Composer está instalando dependências definidas em Composer. JSON.

    ![Implantação do git com a automação do Composer no Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Passos seguintes

Para obter mais informações, consulte a [central de desenvolvedores do PHP](https://azure.microsoft.com/develop/php/).

[avaliação gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo ()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de diretivas php. ini]: https://www.php.net/manual/en/ini.list.php
[. User. ini]: https://www.php.net/manual/en/configuration.file.per-user.php
[ini_set ()]: https://www.php.net/manual/en/function.ini-set.php
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
