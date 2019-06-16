---
title: Configurar o runtime PHP - serviço de aplicações do Azure
description: Saiba como configurar a instalação do PHP predefinida ou adicionar uma instalação personalizada do PHP para o serviço de aplicações do Azure.
services: app-service
documentationcenter: php
author: msangapu
manager: cfowler
ms.assetid: 95c4072b-8570-496b-9c48-ee21a223fb60
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.devlang: PHP
ms.topic: article
ms.date: 04/11/2018
ms.author: msangapu
ms.custom: seodec18
ms.openlocfilehash: 38e0983830c540082a915332aa4158d2af84567b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65408892"
---
# <a name="configure-php-in-azure-app-service"></a>Configurar o PHP no serviço de aplicações do Azure

## <a name="introduction"></a>Introdução

Este guia mostra-lhe como configurar o runtime PHP integrado para aplicações web, móveis back-ends e aplicações API no [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714), forneça um runtime PHP personalizado e ativar extensões. Para utilizar o serviço de aplicações, inscreva-se para o [avaliação gratuita]. Para obter o máximo partido neste guia, deve primeiro criar uma aplicação PHP no serviço de aplicações.

## <a name="how-to-change-the-built-in-php-version"></a>Como: Alterar a versão PHP incorporada

Por predefinição, 5.6 do PHP está instalado e imediatamente disponível para utilização quando criar uma aplicação de serviço de aplicações. A melhor forma de ver as extensões ativadas, sua configuração padrão e a revisão da versão disponível é implantar um script que chama o [phpinfo()] função.

Versões do PHP 7.0 e PHP 7.2 também estão disponíveis, mas não ativado por predefinição. Para atualizar a versão do PHP, siga um dos seguintes métodos:

### <a name="azure-portal"></a>Portal do Azure

1. Navegue para a sua aplicação no [portal do Azure](https://portal.azure.com) e desloque-se para o **configuração** página.

2. Partir **Configuration**, selecione **definições gerais** e escolha a nova versão PHP.

3. Clique no **salvar** botão na parte superior a **definições gerais** painel.

### <a name="azure-powershell-windows"></a>Azure PowerShell (Windows)

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

1. Aberto do Azure PowerShell e inicie sessão na sua conta:

        PS C:\> Connect-AzAccount
2. Defina a versão PHP para a aplicação.

        PS C:\> Set-AzureWebsite -PhpVersion {5.6 | 7.0 | 7.2} -Name {app-name}
3. A versão do PHP está agora definida. Pode confirmar estas definições:

        PS C:\> Get-AzureWebsite -Name {app-name} | findstr PhpVersion

### <a name="azure-cli"></a>CLI do Azure 

Para utilizar a Interface de linha de comandos do Azure, terá [instalar a CLI do Azure](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) no seu computador.

1. Abrir Terminal e inicie sessão na sua conta.

        az login

1. Verifique a lista de runtimes suportados.

        az webapp list-runtimes | grep php

2. Defina a versão PHP para a aplicação.

        az webapp config set --php-version {5.6 | 7.0 | 7.1 | 7.2} --name {app-name} --resource-group {resource-group-name}

3. A versão do PHP está agora definida. Pode confirmar estas definições:

        az webapp show --name {app-name} --resource-group {resource-group-name}

## <a name="how-to-change-the-built-in-php-configurations"></a>Como: Alterar as configurações internas do PHP

Para qualquer tempo de execução do PHP incorporado, pode alterar qualquer uma das opções de configuração ao seguir estes passos. (Para obter informações sobre as diretivas de PHP. ini, consulte [lista de diretivas de PHP. ini].)

### <a name="changing-phpiniuser-phpiniperdir-phpiniall-configuration-settings"></a>A alteração do PHP\_INI\_utilizador, o PHP\_INI\_PERDIR, PHP\_INI\_todas as definições de configuração

1. Adicionar uma [. user.ini] ficheiro para o seu diretório de raiz.
1. Adicionar definições de configuração para o `.user.ini` de ficheiros com a mesma sintaxe que usaria num `php.ini` ficheiro. Por exemplo, se quiser ativar a `display_errors` definição e defina `upload_max_filesize` definição para 10M, sua `.user.ini` ficheiros iria conter este texto:

        ; Example Settings
        display_errors=On
        upload_max_filesize=10M

        ; OPTIONAL: Turn this on to write errors to d:\home\LogFiles\php_errors.log
        ; log_errors=On
2. Implemente a sua aplicação.
3. Reinicie a aplicação. (Reinício é necessário porque a frequência com que PHP lê `.user.ini` arquivos é regida pelo `user_ini.cache_ttl` definição, que é uma configuração de nível de sistema e é de 300 segundos (5 minutos) por predefinição. Reiniciar a aplicação força o PHP para ler as novas configurações no `.user.ini` ficheiro.)

Como alternativa à utilização uma `.user.ini` ficheiro, pode utilizar o [ini_set()] função em scripts para definir as opções de configuração que não são diretivas de nível de sistema.

### <a name="changing-phpinisystem-configuration-settings"></a>A alteração do PHP\_INI\_as configurações do sistema

1. Adicionar uma definição de aplicação à sua aplicação com a chave `PHP_INI_SCAN_DIR` e valor `d:\home\site\ini`
1. Criar uma `settings.ini` de ficheiros utilizando a consola Kudu (http://&lt;nome do site&gt;. scm.azurewebsite.net) no `d:\home\site\ini` diretório.
1. Adicionar definições de configuração para o `settings.ini` de ficheiros com a mesma sintaxe que usaria num `php.ini` ficheiro. Por exemplo, se quisesse apontar o `curl.cainfo` definição de um `*.crt` de ficheiros e definir a definição de 'wincache.maxfilesize' para 512K, sua `settings.ini` ficheiros iria conter este texto:

        ; Example Settings
        curl.cainfo="%ProgramFiles(x86)%\Git\bin\curl-ca-bundle.crt"
        wincache.maxfilesize=512
1. Recarregar as alterações, reinicie a aplicação.

## <a name="how-to-enable-extensions-in-the-default-php-runtime"></a>Como: Ativar extensões no runtime PHP predefinido

Conforme observado na seção anterior, a melhor forma de ver a versão PHP predefinido, a configuração predefinida e as extensões ativadas é implantar um script que chama [phpinfo()]. Para ativar extensões adicionais, seguindo estes passos:

### <a name="configure-via-ini-settings"></a>Configurar através das definições de ini

1. Adicionar uma `ext` diretório para o `d:\home\site` diretório.
1. Colocar `.dll` extensão de arquivos no `ext` diretório (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões são compatíveis com a versão predefinida do PHP e são VC9 e compatível com o não thread-safe (ualizar).
1. Adicionar uma definição de aplicação à sua aplicação com a chave `PHP_INI_SCAN_DIR` e valor `d:\home\site\ini`
1. Criar uma `ini` de ficheiros `d:\home\site\ini` chamado `extensions.ini`.
1. Adicionar definições de configuração para o `extensions.ini` de ficheiros com a mesma sintaxe que usaria num `php.ini` ficheiro. Por exemplo, se quiser ativar extensões MongoDB e XDebug, sua `extensions.ini` ficheiros iria conter este texto:

        ; Enable Extensions
        extension=d:\home\site\ext\php_mongo.dll
        zend_extension=d:\home\site\ext\php_xdebug.dll
1. Reinicie a aplicação para carregar as alterações.

### <a name="configure-via-app-setting"></a>Configurar através de definição de aplicação

1. Adicionar um `bin` diretório para o diretório de raiz.
2. Colocar `.dll` extensão de arquivos no `bin` diretório (por exemplo, `php_xdebug.dll`). Certifique-se de que as extensões são compatíveis com a versão predefinida do PHP e são VC9 e compatível com o não thread-safe (ualizar).
3. Implemente a sua aplicação.
4. Navegue para a sua aplicação no portal do Azure e clique no **Configuration** localizada abaixo **definições** secção.
5. Do **Configuration** painel, selecione **configurações de aplicativo**.
6. Na **as configurações do aplicativo** secção, clique em **+ nova definição de aplicação** e crie um **PHP_EXTENSIONS** chave. O valor para esta chave seria um caminho relativo à raiz do Web site: **bin\your-ext-ficheiro**.
7. Clique nas **atualização** botão na parte inferior, em seguida, clique em **guardar** acima a **as definições da aplicação** separador.

As extensões de Zend também são suportadas ao utilizar um **PHP_ZENDEXTENSIONS** chave. Para ativar várias extensões, inclua uma lista separada por vírgulas de `.dll` ficheiros para o valor de definição de aplicação.

## <a name="how-to-use-a-custom-php-runtime"></a>Como: Utilizar um runtime PHP personalizado

Em vez do runtime PHP predefinido, o serviço de aplicações pode utilizar um tempo de execução do PHP por si para executar scripts PHP. O tempo de execução que fornecer pode ser configurado por um `php.ini` arquivo que também fornecer. Para utilizar um runtime PHP personalizado com o serviço de aplicações, siga estes passos.

1. Obter um não-thread-safe, VC9 ou VC11 versão compatível do PHP para Windows. Versões recentes do PHP para Windows podem ser encontradas aqui: [ https://windows.php.net/download/ ]. Versões mais antigas podem ser encontradas no arquivo morto aqui: [ https://windows.php.net/downloads/releases/archives/ ].
2. Modificar o `php.ini` ficheiro para o runtime. As definições de configuração que são as diretivas de sistema de nível-só são ignoradas pelo serviço de aplicações. (Para obter informações sobre as diretivas de sistema de nível-só, consulte [lista de diretivas de PHP. ini]).
3. Opcionalmente, adicionar extensões ao seu tempo de execução do PHP e ativá-los no `php.ini` ficheiro.
4. Adicionar uma `bin` diretório para o seu diretório de raiz e put o diretório que contém o runtime PHP no mesmo (por exemplo, `bin\php`).
5. Implemente a sua aplicação.
6. Navegue para a sua aplicação no portal do Azure e clique nas **configuração** painel.
8. Partir do **Configuration** painel, selecione **mapeamentos de caminho**. 
9. Clique em **+ novo manipulador** e adicione `*.php` para a extensão de campo e adicione o caminho para o `php-cgi.exe` executável no **processador de scripts**. Se colocar o runtime PHP no `bin` é o caminho de diretório na raiz da sua aplicação, `D:\home\site\wwwroot\bin\php\php-cgi.exe`.
10. Na parte inferior, clique em **atualização** para acabar de adicionar o mapeamento do processador.
11. Clique em **Guardar** para guardar as alterações.

<a name="composer" />

## <a name="how-to-enable-composer-automation-in-azure"></a>Como: Ativar a automatização do compositor no Azure

Por predefinição, o serviço de aplicações não faz nada com Composer. JSON, se tiver um no seu projeto PHP. Se usar [implementação de Git](deploy-local-git.md), pode ativar Composer. JSON processamento durante `git push` ao ativar a extensão de compositor.

> [!NOTE]
> Pode [voto para compositor suporte de primeira classe no serviço de aplicações aqui](https://feedback.azure.com/forums/169385-web-apps-formerly-websites/suggestions/6477437-first-class-support-for-composer-and-pip)!
>

1. No painel da sua aplicação PHP no [portal do Azure](https://portal.azure.com), clique em **ferramentas** > **extensões**.

    ![Painel de definições do portal do Azure para ativar a automatização do compositor no Azure](./media/web-sites-php-configure/composer-extension-settings.png)
2. Clique em **Add**, em seguida, clique em **compositor**.

    ![Adicionar a extensão de compositor para ativar a automatização do compositor no Azure](./media/web-sites-php-configure/composer-extension-add.png)
3. Clique em **OK** para aceitar os termos legais. Clique em **OK** novamente para adicionar a extensão.

    O **extensões instaladas** painel mostra a extensão de compositor.
    ![Aceitar termos legais para ativar a automatização do compositor no Azure](./media/web-sites-php-configure/composer-extension-view.png)
4. Agora, numa janela de terminal no seu computador local, efetuar `git add`, `git commit`, e `git push` à sua aplicação. Tenha em atenção que o compositor está a instalar dependências definidas nas Composer. JSON.

    ![Implementação de Git com a automatização do compositor no Azure](./media/web-sites-php-configure/composer-extension-success.png)

## <a name="next-steps"></a>Passos Seguintes

Para obter mais informações, consulte a [Centro de programadores PHP](https://azure.microsoft.com/develop/php/).

[avaliação gratuita]: https://www.windowsazure.com/pricing/free-trial/
[phpinfo()]: https://php.net/manual/en/function.phpinfo.php
[select-php-version]: ./media/web-sites-php-configure/select-php-version.png
[Lista de diretivas de PHP. ini]: https://www.php.net/manual/en/ini.list.php
[. user.ini]: https://www.php.net/manual/en/configuration.file.per-user.php
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
