---
title: Troubleshoot Python ModuleNotFoundError em funções azure
description: Aprenda a resolver problemas com o módulo Funções Azure não encontrado erros nas funções python.
author: Hazhzeng
ms.topic: article
ms.date: 05/12/2020
ms.author: hazeng
ms.openlocfilehash: 191cde0f90b4968ca230db72bad68cca8b1db3fd
ms.sourcegitcommit: 50673ecc5bf8b443491b763b5f287dde046fdd31
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 05/20/2020
ms.locfileid: "83691230"
---
# <a name="troubleshoot-python-module-errors-in-azure-functions"></a>Problemas de suturação de módulos Python em Funções Azure

Este artigo ajuda-o a resolver erros relacionados com módulos na sua aplicação de função Python. Estes erros normalmente resultam na seguinte mensagem de erro das Funções Azure:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Este problema de erro ocorre quando uma aplicação de função Python falha em carregar um módulo Python. A causa principal deste erro é uma das seguintes questões:

- [O pacote não pode ser encontrado.](#the-package-cant-be-found)
- [O pacote não é resolvido com roda de Linux adequada](#the-package-isnt-resolved-with-proper-linux-wheel)
- [O pacote é incompatível com a versão intérprete Python](#the-package-is-incompatible-with-the-python-interpreter-version)
- [O pacote entra em conflito com outros pacotes](#the-package-conflicts-with-other-packages)
- [O pacote apenas suporta plataformas Windows ou macOS](#the-package-only-supports-windows-or-macos-platforms)

## <a name="view-project-files"></a>Ver ficheiros do projeto

Para identificar a causa real do seu problema, precisa de obter os ficheiros do projeto Python que funcionam na sua aplicação de função. Se não tiver os ficheiros do projeto no seu computador local, pode obtê-los de uma das seguintes formas:

- Se a aplicação de funções tiver definição de aplicação e o `WEBSITE_RUN_FROM_PACKAGE` seu valor for um URL, descarregue o ficheiro por cópia e cole o URL no seu navegador.
- Se a aplicação de funções tiver `WEBSITE_RUN_FROM_PACKAGE` e estiver definida para . navegar e descarregar o ficheiro a partir do URL mais `1` `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` `href` recente.
- Se a aplicação de funções não tiver a definição da aplicação acima mencionada, navegue `https://<app-name>.scm.azurewebsites.net/api/settings` para e encontre o URL abaixo de `SCM_RUN_FROM_PACKAGE` . Descarregue o ficheiro por cópia e cole o URL no seu browser.
- Se nenhum destes funcionar para si, navegue `https://<app-name>.scm.azurewebsites.net/DebugConsole` e revele o conteúdo em `/home/site/wwwroot` .

O resto deste artigo ajuda-o a resolver potenciais causas deste erro, inspecionando o conteúdo da sua aplicação de funções, identificando a causa principal e resolvendo o problema específico.

## <a name="diagnose-modulenotfounderror"></a>Diagnosticar MóduloErro NotFound

Esta secção detalha potenciais causas de erros relacionados com módulos. Depois de descobrir qual é a causa principal provável, pode ir para a mitigação relacionada.

### <a name="the-package-cant-be-found"></a>O pacote não pode ser encontrado.

Navegue para `.python_packages/lib/python3.6/site-packages/<package-name>` ou `.python_packages/lib/site-packages/<package-name>` . Se o caminho dos ficheiros não existir, este caminho em falta é provavelmente a causa principal.

A utilização de ferramentas de terceiros ou desatualizadas durante a implantação pode causar este problema.

Ver [Ativar construção remota](#enable-remote-build) ou construir [dependências nativas](#build-native-dependencies) para mitigação.

### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>O pacote não é resolvido com roda de Linux adequada

Vai para `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` ou. Utilize o seu editor de texto favorito para abrir o ficheiro da **roda** e verificar a **secção Tag:** Se o valor da etiqueta não contiver **linux,** este pode ser o problema.

As funções python funcionam apenas em Linux em Azure: Funções funciona v2.x funciona em Debian Stretch e o tempo de execução v3.x em Debian Buster. Espera-se que o artefacto contenha os binários linux corretos. A utilização de `--build local` bandeiras em Ferramentas Core, ferramentas de terceiros ou desatualizadas pode fazer com que binários mais antigos sejam utilizados.

Ver [Ativar construção remota](#enable-remote-build) ou construir [dependências nativas](#build-native-dependencies) para mitigação.

### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>O pacote é incompatível com a versão intérprete Python

Vai para `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` ou. Utilizando um editor de texto, abra o ficheiro METADATA e verifique os **Classificadores:** secção. Se a secção não contiver `Python :: 3` , ou , isto significa que a versão do pacote é demasiado `Python :: 3.6` `Python :: 3.7` `Python :: 3.8` antiga, ou muito provavelmente, a embalagem já está fora de manutenção.

Pode verificar a versão Python da sua aplicação de funções a partir do [portal Azure](https://portal.azure.com). Navegue para a sua aplicação de funções, escolha **o Explorador de Recursos,** e selecione **Go**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Abra o Explorador de Recursos para a aplicação de função no portal":::

Depois das cargas do explorador, procure **linuxFxVersion,** que mostra a versão Python.

Consulte [a atualização do seu pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [substitua o pacote por equivalentes](#replace-the-package-with-equivalents) para mitigação.

### <a name="the-package-conflicts-with-other-packages"></a>O pacote entra em conflito com outros pacotes

Se verificou que a embalagem está resolvida corretamente com as rodas linux adequadas, pode haver um conflito com outros pacotes. Em certos pacotes, as documentações pyPi podem clarificar os módulos incompatíveis. Por [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) exemplo, há uma declaração da seguinte forma:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Pode encontrar a documentação para a sua versão pacote em `https://pypi.org/project/<package-name>/<package-version>` .

Consulte [a atualização do seu pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [substitua o pacote por equivalentes](#replace-the-package-with-equivalents) para mitigação.

### <a name="the-package-only-supports-windows-or-macos-platforms"></a>O pacote apenas suporta plataformas Windows ou macOS

Abra o com um editor de `requirements.txt` texto e faça o check-in do pacote `https://pypi.org/project/<package-name>` . Alguns pacotes funcionam apenas nas plataformas Windows ou macOS. Por exemplo, o pywin32 só funciona no Windows.

O erro pode não ocorrer quando estiver a `Module Not Found` utilizar o Windows ou o macOS para o desenvolvimento local. No entanto, o pacote não importa em Funções Azure, que utiliza o Linux em tempo de funcionamento. Isto é provável que seja causado pela `pip freeze` utilização para exportar o ambiente virtual em requisitos.txt do seu Windows ou máquina macOS durante a inicialização do projeto.

Consulte [Substituir a embalagem por equivalentes](#replace-the-package-with-equivalents) ou requisitos de [artesanato.txt](#handcraft-requirementstxt) para mitigação.

## <a name="mitigate-modulenotfounderror"></a>Atenuar o erro do MóduloNotFound

Seguem-se potenciais atenuações para problemas relacionados com módulos. Utilize os [diagnósticos acima](#diagnose-modulenotfounderror) para determinar qual destas mitigações tentar.

### <a name="enable-remote-build"></a>Ativar a construção remota

Certifique-se de que a construção remota está ativada. A forma como o fazes depende do teu método de implantação.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Certifique-se de que a versão mais recente da [extensão de Funções Azure para Código](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) de Estúdio Visual está instalada. Verifique se `.vscode/settings.json` existe e contém a definição `"azureFunctions.scmDoBuildDuringDeployment": true` . Caso contrário, crie este ficheiro com a `azureFunctions.scmDoBuildDuringDeployment` definição ativada e reimplemente o projeto.

# <a name="azure-functions-core-tools"></a>[Ferramentas centrais de funções azure](#tab/coretools)

Certifique-se de que a versão mais recente das Ferramentas Core das [Funções Azure](https://github.com/Azure/azure-functions-core-tools/releases) está instalada. Vá à pasta do projeto de função local e use `func azure functionapp publish <app-name>` para a implementação.

# <a name="manual-publishing"></a>[Publicação manual](#tab/manual)

Se estiver a publicar manualmente o seu pacote no `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` ponto final, certifique-se de que tanto **SCM_DO_BUILD_DURING_DEPLOYMENT** como **ENABLE_ORYX_BUILD** estão definidos como **verdadeiros**. Para saber mais, veja [como trabalhar com as definições de aplicação.](functions-how-to-use-azure-function-app-settings.md#settings)

---

### <a name="build-native-dependencies"></a>Construir dependências nativas

Certifique-se de que a versão mais recente das ferramentas core do **Docker** e [do Azure Functions](https://github.com/Azure/azure-functions-core-tools/releases) está instalada. Vá à pasta do projeto de função local e use `func azure functionapp publish <app-name> --build-native-deps` para a implementação.

### <a name="update-your-package-to-the-latest-version"></a>Atualize o seu pacote para a versão mais recente

Navegue na versão mais recente do pacote `https://pypi.org/project/<package-name>` e verifique os **Classifiers:** secção. A embalagem deve `OS Independent` ser, ou compatível com `POSIX` ou no `POSIX :: Linux` Sistema **Operativo.** Além disso, a Linguagem de Programação deve `Python :: 3` `Python :: 3.6` conter, `Python :: 3.7` ou `Python :: 3.8` .

Se estes estiverem corretos, pode atualizar o pacote para a versão mais recente, alterando a linha `<package-name>~=<latest-version>` em requisitos.txt.

### <a name="handcraft-requirementstxt"></a>Requisitos de artesanato.txt

Alguns desenvolvedores usam `pip freeze > requirements.txt` para gerar a lista de pacotes Python para os seus ambientes em desenvolvimento. Embora esta conveniência deva funcionar na maioria dos casos, pode haver problemas em cenários de implementação de plataformas cruzadas, como desenvolver funções localmente no Windows ou macOS, mas publicar para uma aplicação de funções, que funciona no Linux. Neste cenário, `pip freeze` pode introduzir dependências ou dependências específicas do sistema operativo inesperados para o seu ambiente de desenvolvimento local. Estas dependências podem quebrar a aplicação de função Python quando estão a correr em Linux.

A melhor prática é verificar a declaração de importação de cada ficheiro .py no seu código fonte de projeto e apenas fazer check-in nesses módulos em ficheiro requisitos.txt. Isto garante que a resolução de pacotes pode ser manuseada corretamente em diferentes sistemas operativos.

### <a name="replace-the-package-with-equivalents"></a>Substitua a embalagem por equivalentes

Em primeiro lugar, devemos dar uma olhada na versão mais recente do pacote `https://pypi.org/project/<package-name>` em . Normalmente, este pacote tem a sua própria página GitHub, vá à secção **Questões** no GitHub e procure se o seu problema foi corrigido. Em caso afirmativo, atualize o pacote para a versão mais recente.

Por vezes, o pacote pode ter sido integrado na [Biblioteca Padrão](https://docs.python.org/3/library/) Python (como pathlib). Em caso afirmativo, uma vez que fornecemos uma certa distribuição python em Funções Azure (Python 3.6, Python 3.7 e Python 3.8), o pacote nos seus requisitos.txt deve ser removido.

No entanto, se está a enfrentar um problema que não foi corrigido e que tem um prazo. Encorajo-o a fazer alguma pesquisa e a encontrar um pacote semelhante para o seu projeto. Normalmente, a comunidade Python fornecer-lhe-á uma grande variedade de bibliotecas semelhantes que você pode usar.

## <a name="next-steps"></a>Passos seguintes

Se não conseguir resolver o problema relacionado com o módulo, por favor, reporte isto à equipa de Funções:

> [!div class="nextstepaction"]
> [Reportar uma questão por resolver](https://github.com/Azure/azure-functions-python-worker/issues)
