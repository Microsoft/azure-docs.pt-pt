---
title: Aplicativos de função Python de resolução de problemas em funções Azure
description: Aprenda a resolver as funções python.
author: Hazhzeng
ms.topic: article
ms.date: 07/29/2020
ms.author: hazeng
ms.custom: devx-track-python
ms.openlocfilehash: 56da006dc5a0eef46d5b13984983ca680359b968
ms.sourcegitcommit: d23602c57d797fb89a470288fcf94c63546b1314
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106168098"
---
# <a name="troubleshoot-python-errors-in-azure-functions"></a>Resolver erros do Python nas Funções do Azure

Segue-se uma lista de guias de resolução de problemas para questões comuns nas funções python:

* [MóduloNotFoundError e ImportError](#troubleshoot-modulenotfounderror)
* [Não é possível importar 'cygrpc'](#troubleshoot-cannot-import-cygrpc)
* [Python saiu com o código 137](#troubleshoot-python-exited-with-code-137)
* [Python saiu com o código 139](#troubleshoot-python-exited-with-code-139)

## <a name="troubleshoot-modulenotfounderror"></a>Módulo de resolução de problemasNotFoundError

Esta secção ajuda-o a resolver erros relacionados com módulos na sua aplicação de função Python. Estes erros normalmente resultam na seguinte mensagem de erro do Azure Functions:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Este erro ocorre quando uma aplicação de função Python não carrega um módulo Python. A causa principal deste erro é uma das seguintes questões:

* [O pacote não pode ser encontrado.](#the-package-cant-be-found)
* [O pacote não é resolvido com a roda linux adequada](#the-package-isnt-resolved-with-proper-linux-wheel)
* [O pacote é incompatível com a versão do intérprete Python](#the-package-is-incompatible-with-the-python-interpreter-version)
* [O pacote entra em conflito com outros pacotes](#the-package-conflicts-with-other-packages)
* [O pacote suporta apenas plataformas Windows ou macOS](#the-package-only-supports-windows-or-macos-platforms)

### <a name="view-project-files"></a>Ver ficheiros de projeto

Para identificar a causa real do seu problema, precisa de obter os ficheiros do projeto Python que funcionam na sua aplicação de função. Se não tiver os ficheiros do projeto no seu computador local, pode obtê-los de uma das seguintes formas:

* Se a aplicação de função tiver `WEBSITE_RUN_FROM_PACKAGE` a definição de aplicação e o seu valor for um URL, descarregue o ficheiro por cópia e cole o URL no seu navegador.
* Se a aplicação de função `WEBSITE_RUN_FROM_PACKAGE` tiver e estiver definida para , `1` navegue para e `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` descarregue o ficheiro a partir do URL mais `href` recente.
* Se a aplicação de função não tiver a definição de aplicação acima mencionada, navegue para `https://<app-name>.scm.azurewebsites.net/api/settings` e encontre o URL sob `SCM_RUN_FROM_PACKAGE` . Faça o download do ficheiro por cópia e cole o URL no seu browser.
* Se nenhum destes trabalhos for você, navegue `https://<app-name>.scm.azurewebsites.net/DebugConsole` para e revele o conteúdo sob `/home/site/wwwroot` .

O resto deste artigo ajuda-o a resolver potenciais causas deste erro, inspecionando o conteúdo da sua aplicação de função, identificando a causa principal e resolvendo o problema específico.

### <a name="diagnose-modulenotfounderror"></a>Módulo de DiagnósticoNotFoundError

Esta secção detalha potenciais causas de erros relacionados com o módulo. Depois de descobrir qual é a causa provável, pode ir para a mitigação relacionada.

#### <a name="the-package-cant-be-found"></a>O pacote não pode ser encontrado.

Navegue para `.python_packages/lib/python3.6/site-packages/<package-name>` ou `.python_packages/lib/site-packages/<package-name>` . . Se o caminho do ficheiro não existir, este caminho em falta é provavelmente a causa principal.

A utilização de ferramentas de terceiros ou desatualizadas durante a implementação pode causar este problema.

Consulte [Ativar a construção remota](#enable-remote-build) ou [construir dependências nativas](#build-native-dependencies) para mitigação.

#### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>O pacote não é resolvido com a roda linux adequada

Ir para `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` ou. Utilize o seu editor de texto favorito para abrir o ficheiro da **roda** e verificar a **etiqueta:** secção. Se o valor da etiqueta não contiver **linux,** este pode ser o problema.

As funções python funcionam apenas em Linux em Azure: Funções executadas v2.x em Debian Stretch e o tempo de execução v3.x em Debian Buster. Espera-se que o artefacto contenha os binários Linux corretos. A utilização de `--build local` bandeiras em Ferramentas Core, ferramentas de terceiros ou desatualizadas pode fazer com que binários mais antigos sejam usados.

Consulte [Ativar a construção remota](#enable-remote-build) ou [construir dependências nativas](#build-native-dependencies) para mitigação.

#### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>O pacote é incompatível com a versão do intérprete Python

Ir para `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` ou. Utilizando um editor de texto, abra o ficheiro METADAS e verifique **os Classificadores:** secção. Se a secção não contiver `Python :: 3` , ou , isto significa que a versão do pacote é demasiado `Python :: 3.6` `Python :: 3.7` `Python :: 3.8` velha, ou provavelmente, a embalagem já está fora de manutenção.

Pode consultar a versão Python da sua aplicação de função a partir do [portal Azure.](https://portal.azure.com) Navegue para a sua aplicação de função, escolha **o Explorador de Recursos** e selecione **Go**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Abra o Explorador de Recursos para a aplicação de função no portal":::

Depois da carga do explorador, procure **linuxFxVersion,** que mostra a versão Python.

Consulte [a atualização do seu pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [substitua a embalagem por equivalentes](#replace-the-package-with-equivalents) para mitigação.

#### <a name="the-package-conflicts-with-other-packages"></a>O pacote entra em conflito com outros pacotes

Se tiver verificado que a embalagem está resolvida corretamente com as rodas Linux adequadas, pode haver um conflito com outros pacotes. Em certos pacotes, as documentações do PyPi podem clarificar os módulos incompatíveis. Por exemplo, em [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) , há uma declaração da seguinte forma:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Pode encontrar a documentação para a sua versão do pacote em `https://pypi.org/project/<package-name>/<package-version>` .

Consulte [a atualização do seu pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [substitua a embalagem por equivalentes](#replace-the-package-with-equivalents) para mitigação.

#### <a name="the-package-only-supports-windows-or-macos-platforms"></a>O pacote suporta apenas plataformas Windows ou macOS

Abra o `requirements.txt` com um editor de texto e verifique o pacote em `https://pypi.org/project/<package-name>` . Alguns pacotes funcionam apenas nas plataformas Windows ou macOS. Por exemplo, o pywin32 só funciona no Windows.

O `Module Not Found` erro pode não ocorrer quando estiver a utilizar o Windows ou o macOS para o desenvolvimento local. No entanto, o pacote não importa as funções Azure, que utiliza o Linux em tempo de execução. Isto é provavelmente causado pela utilização `pip freeze` para exportar ambiente virtual para requirements.txt a partir da sua máquina Windows ou macOS durante a inicialização do projeto.

Consulte [Substituir a embalagem por equivalentes](#replace-the-package-with-equivalents) ou requirements.txtde [artesanato ](#handcraft-requirementstxt) para mitigação.

### <a name="mitigate-modulenotfounderror"></a>Módulo MitigaçãoNotFoundError

Seguem-se potenciais atenuações para questões relacionadas com módulos. Use os [diagnósticos acima](#diagnose-modulenotfounderror) para determinar qual destas mitigações tentar.

#### <a name="enable-remote-build"></a>Ativar a construção remota

Certifique-se de que a construção remota está ativada. A forma como o fazes depende do teu método de implantação.

## <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Certifique-se de que a versão mais recente da [extensão Azure Functions para o Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) está instalada. Verifique se `.vscode/settings.json` existe e contém a definição `"azureFunctions.scmDoBuildDuringDeployment": true` . Caso contrário, por favor, crie este ficheiro com a `azureFunctions.scmDoBuildDuringDeployment` definição ativada e reenfectue o projeto.

## <a name="azure-functions-core-tools"></a>[Azure Functions Core Tools](#tab/coretools)

Certifique-se de que a versão mais recente das [Ferramentas Principais de Funções Azure](https://github.com/Azure/azure-functions-core-tools/releases) está instalada. Vá à pasta do projeto de função local e use `func azure functionapp publish <app-name>` para implantação.

## <a name="manual-publishing"></a>[Publicação manual](#tab/manual)

Se estiver a publicar manualmente o seu pacote no `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` ponto final, certifique-se de que tanto **SCM_DO_BUILD_DURING_DEPLOYMENT** como **ENABLE_ORYX_BUILD** estão definidos como **verdadeiros**. Para saber mais, [consulte como trabalhar com as definições de aplicação](functions-how-to-use-azure-function-app-settings.md#settings).

---

#### <a name="build-native-dependencies"></a>Construir dependências nativas

Certifique-se de que a versão mais recente de **Ferramentas** Core do Docker e [do Azure Functions](https://github.com/Azure/azure-functions-core-tools/releases) está instalada. Vá à pasta do projeto de função local e use `func azure functionapp publish <app-name> --build-native-deps` para implantação.

#### <a name="update-your-package-to-the-latest-version"></a>Atualize o seu pacote para a versão mais recente

Navegue na versão mais recente do pacote `https://pypi.org/project/<package-name>` e verifique os **Classificadores:** secção. A embalagem deve ser `OS Independent` , ou compatível com `POSIX` ou em Sistema `POSIX :: Linux` **Operativo**. Além disso, a Linguagem de Programação deve conter `Python :: 3` `Python :: 3.6` , , ou `Python :: 3.7` `Python :: 3.8` .

Se estes estiverem corretos, pode atualizar o pacote para a versão mais recente, alterando a linha `<package-name>~=<latest-version>` em requirements.txt.

#### <a name="handcraft-requirementstxt"></a>requirements.txt de artesanato

Alguns desenvolvedores usam `pip freeze > requirements.txt` para gerar a lista de pacotes Python para os seus ambientes em desenvolvimento. Embora esta conveniência deva funcionar na maioria dos casos, pode haver problemas em cenários de implementação de plataformas cruzadas, como o desenvolvimento de funções localmente no Windows ou macOS, mas a publicação para uma aplicação de função, que funciona no Linux. Neste cenário, `pip freeze` pode introduzir dependências ou dependências inesperadas do sistema operativo para o seu ambiente de desenvolvimento local. Estas dependências podem quebrar a aplicação de função Python quando estiver em funcionamento no Linux.

A melhor prática é verificar a declaração de importação de cada .py ficheiro no seu código fonte do projeto e apenas fazer o check-in desses módulos no ficheiro requirements.txt. Isto garante que a resolução de pacotes pode ser manuseada corretamente em diferentes sistemas operativos.

#### <a name="replace-the-package-with-equivalents"></a>Substitua a embalagem por equivalentes

Em primeiro lugar, devemos dar uma olhada na versão mais recente do pacote em `https://pypi.org/project/<package-name>` . Normalmente, este pacote tem a sua própria página GitHub, vá à secção **de Problemas** no GitHub e procure se o seu problema foi corrigido. Em caso afirmativo, atualize o pacote para a versão mais recente.

Por vezes, o pacote pode ter sido integrado na [Biblioteca Padrão python](https://docs.python.org/3/library/) (como o pathlib). Se assim for, uma vez que fornecemos uma certa distribuição python em Funções Azure (Python 3.6, Python 3.7 e Python 3.8), o pacote no seu requirements.txt deve ser removido.

No entanto, se está a enfrentar um problema que não foi corrigido e que está num prazo. Encorajo-vos a pesquisar e a encontrar um pacote semelhante para o vosso projeto. Normalmente, a comunidade Python irá fornecer-lhe uma grande variedade de bibliotecas semelhantes que você pode usar.

---

## <a name="troubleshoot-cannot-import-cygrpc"></a>Resolução de problemas não pode importar 'cygrpc'

Esta secção ajuda-o a resolver erros relacionados com o 'cygrpc' na sua aplicação de função Python. Estes erros normalmente resultam na seguinte mensagem de erro do Azure Functions:

> `Cannot import name 'cygrpc' from 'grpc._cython'`

Este erro ocorre quando uma aplicação de função Python não começa com um intérprete Python adequado. A causa principal deste erro é uma das seguintes questões:

- [O intérprete Python desencontra a arquitetura OS](#the-python-interpreter-mismatches-os-architecture)
- [O intérprete Python não é apoiado pelo Trabalhador Python functions Azure Functions](#the-python-interpreter-is-not-supported-by-azure-functions-python-worker)

### <a name="diagnose-cygrpc-reference-error"></a>Diagnóstico erro de referência 'cygrpc'

#### <a name="the-python-interpreter-mismatches-os-architecture"></a>O intérprete Python desencontra a arquitetura OS

Isto é provavelmente causado por um intérprete Python de 32 bits está instalado no seu sistema operativo de 64 bits.

Se estiver a funcionar num sistema operativo x64, certifique-se de que o seu intérprete Python 3.6, 3.7 ou 3.8 também está na versão de 64 bits.

Pode verificar a sua mordida de intérprete Python pelos seguintes comandos:

No Windows in PowerShell: `py -c 'import platform; print(platform.architecture()[0])'`

Na concha semelhante ao Unix: `python3 -c 'import platform; print(platform.architecture()[0])'`

Se houver um desfasamento entre a bitidão do intérprete Python e a arquitetura do sistema operativo, por favor descarregue um intérprete python adequado da [Python Software Foundation.](https://python.org/downloads/release)

#### <a name="the-python-interpreter-is-not-supported-by-azure-functions-python-worker"></a>O intérprete Python não é apoiado pelo Trabalhador Python functions Azure Functions

O Azure Functions Python Worker só suporta Python 3.6, 3.7 e 3.8.
Por favor, verifique se o seu intérprete Python corresponde à nossa versão esperada `py --version` no Windows ou em `python3 --version` sistemas semelhantes ao Unix. Certifique-se de que o resultado do retorno é Python 3.6.x, Python 3.7.x ou Python 3.8.x.

Se a sua versão de intérprete Python não corresponder às nossas expectativas, por favor baixe o intérprete Python 3.6, 3.7 ou 3.8 da [Python Software Foundation.](https://python.org/downloads/release)

---

## <a name="troubleshoot-python-exited-with-code-137"></a>Pitão de resolução de problemas saiu com o código 137

Os erros do Código 137 são normalmente causados por problemas fora de memória na sua aplicação de função Python. Como resultado, obtém a seguinte mensagem de erro Azure Functions:

> `Microsoft.Azure.WebJobs.Script.Workers.WorkerProcessExitException : python exited with code 137`

Este erro ocorre quando uma aplicação de função Python é forçada a terminar pelo sistema operativo com um sinal SIGKILL. Este sinal geralmente indica um erro fora de memória no seu processo Python. A plataforma Azure Functions possui uma [limitação de serviço](functions-scale.md#service-limits) que irá encerrar quaisquer aplicações de função que excedam este limite.

Visite a secção tutorial em [perfis de memória nas funções Python](python-memory-profiler-reference.md#memory-profiling-process) para analisar o estrangulamento da memória na sua aplicação de função.

---

## <a name="troubleshoot-python-exited-with-code-139"></a>Pitão de resolução de problemas saiu com o código 139

Esta secção ajuda-o a resolver erros de falha de segmentação na sua aplicação de função Python. Estes erros normalmente resultam na seguinte mensagem de erro do Azure Functions:

> `Microsoft.Azure.WebJobs.Script.Workers.WorkerProcessExitException : python exited with code 139`

Este erro ocorre quando uma aplicação de função Python é forçada a terminar pelo sistema operativo com um sinal SIGSEGV. Este sinal indica uma violação da segmentação da memória que pode ser causada pela leitura inesperada ou escrita numa região de memória restrita. Nas secções seguintes, fornecemos uma lista de causas de raiz comuns.

### <a name="a-regression-from-third-party-packages"></a>Uma regressão de pacotes de terceiros

Na requirements.txt da sua aplicação de funções, um pacote não pinned será atualizado para a versão mais recente em todas as implementações do Azure Functions. Os fornecedores destes pacotes podem introduzir regressões no seu mais recente lançamento. Para recuperar desta questão, tente comentar as declarações de importação, desativando as referências do pacote ou fixando o pacote numa versão anterior em requirements.txt.

### <a name="unpickling-from-a-malformed-pkl-file"></a>Desaderente de um ficheiro .pkl mal formado

Se a sua aplicação de função estiver a utilizar a biblioteca python pickel para carregar o objeto Python a partir do ficheiro .pkl, é possível que o .pkl contenha uma cadeia de bytes mal formada ou referência de endereço inválida nele. Para recuperar deste problema, tente comentar a função pickle.load().

### <a name="pyodbc-connection-collision"></a>Colisão de ligação pyodbc

Se a sua aplicação de função estiver a utilizar o popular controlador de base de dados ODBC [pyodbc,](https://github.com/mkleehammer/pyodbc)é possível que sejam abertas múltiplas ligações dentro de uma única aplicação de função. Para evitar este problema, utilize o padrão singleton e certifique-se de que apenas uma ligação pyodbc é usada em toda a aplicação de função.

---

## <a name="next-steps"></a>Passos seguintes

Se não conseguir resolver o seu problema, por favor reporte isto à equipa de Funções:

> [!div class="nextstepaction"]
> [Reportar um problema não resolvido](https://github.com/Azure/azure-functions-python-worker/issues)
