---
title: Módulo Python de resolução de problemasNotFoundError em Funções Azure
description: Saiba como resolver problemas o módulo Azure Functions não encontrou erros nas funções Python.
author: Hazhzeng
ms.topic: article
ms.date: 05/12/2020
ms.author: hazeng
ms.custom: tracking-python
ms.openlocfilehash: b2582caf407b3983b32c40482fa0f0275f00fb8d
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/09/2020
ms.locfileid: "84554763"
---
# <a name="troubleshoot-python-module-errors-in-azure-functions"></a>Resolução de problemas Erros do módulo Python em Funções Azure

Este artigo ajuda-o a resolver erros relacionados com módulos na sua aplicação de função Python. Estes erros normalmente resultam na seguinte mensagem de erro do Azure Functions:

> `Exception: ModuleNotFoundError: No module named 'module_name'.`

Este problema de erro ocorre quando uma aplicação de função Python não carrega um módulo Python. A causa principal deste erro é uma das seguintes questões:

- [O pacote não pode ser encontrado.](#the-package-cant-be-found)
- [O pacote não é resolvido com a roda linux adequada](#the-package-isnt-resolved-with-proper-linux-wheel)
- [O pacote é incompatível com a versão do intérprete Python](#the-package-is-incompatible-with-the-python-interpreter-version)
- [O pacote entra em conflito com outros pacotes](#the-package-conflicts-with-other-packages)
- [O pacote suporta apenas plataformas Windows ou macOS](#the-package-only-supports-windows-or-macos-platforms)

## <a name="view-project-files"></a>Ver ficheiros de projeto

Para identificar a causa real do seu problema, precisa de obter os ficheiros do projeto Python que funcionam na sua aplicação de função. Se não tiver os ficheiros do projeto no seu computador local, pode obtê-los de uma das seguintes formas:

- Se a aplicação de função tiver `WEBSITE_RUN_FROM_PACKAGE` a definição de aplicação e o seu valor for um URL, descarregue o ficheiro por cópia e cole o URL no seu navegador.
- Se a aplicação de função `WEBSITE_RUN_FROM_PACKAGE` tiver e estiver definida para , `1` navegue para e `https://<app-name>.scm.azurewebsites.net/api/vfs/data/SitePackages` descarregue o ficheiro a partir do URL mais `href` recente.
- Se a aplicação de função não tiver a definição de aplicação acima mencionada, navegue para `https://<app-name>.scm.azurewebsites.net/api/settings` e encontre o URL sob `SCM_RUN_FROM_PACKAGE` . Faça o download do ficheiro por cópia e cole o URL no seu browser.
- Se nenhum destes trabalhos for você, navegue `https://<app-name>.scm.azurewebsites.net/DebugConsole` para e revele o conteúdo sob `/home/site/wwwroot` .

O resto deste artigo ajuda-o a resolver potenciais causas deste erro, inspecionando o conteúdo da sua aplicação de função, identificando a causa principal e resolvendo o problema específico.

## <a name="diagnose-modulenotfounderror"></a>Módulo de DiagnósticoNotFoundError

Esta secção detalha potenciais causas de erros relacionados com o módulo. Depois de descobrir qual é a causa provável, pode ir para a mitigação relacionada.

### <a name="the-package-cant-be-found"></a>O pacote não pode ser encontrado.

Navegue para `.python_packages/lib/python3.6/site-packages/<package-name>` ou `.python_packages/lib/site-packages/<package-name>` . . Se o caminho do ficheiro não existir, este caminho em falta é provavelmente a causa principal.

A utilização de ferramentas de terceiros ou desatualizadas durante a implementação pode causar este problema.

Consulte [Ativar a construção remota](#enable-remote-build) ou [construir dependências nativas](#build-native-dependencies) para mitigação.

### <a name="the-package-isnt-resolved-with-proper-linux-wheel"></a>O pacote não é resolvido com a roda linux adequada

Ir para `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` ou. Utilize o seu editor de texto favorito para abrir o ficheiro da **roda** e verificar a **etiqueta:** secção. Se o valor da etiqueta não contiver **linux,** este pode ser o problema.

As funções python funcionam apenas em Linux em Azure: Funções executadas v2.x em Debian Stretch e o tempo de execução v3.x em Debian Buster. Espera-se que o artefacto contenha os binários Linux corretos. A utilização de `--build local` bandeiras em Ferramentas Core, ferramentas de terceiros ou desatualizadas pode fazer com que binários mais antigos sejam usados.

Consulte [Ativar a construção remota](#enable-remote-build) ou [construir dependências nativas](#build-native-dependencies) para mitigação.

### <a name="the-package-is-incompatible-with-the-python-interpreter-version"></a>O pacote é incompatível com a versão do intérprete Python

Ir para `.python_packages/lib/python3.6/site-packages/<package-name>-<version>-dist-info` `.python_packages/lib/site-packages/<package-name>-<version>-dist-info` ou. Utilizando um editor de texto, abra o ficheiro METADAS e verifique **os Classificadores:** secção. Se a secção não contiver `Python :: 3` , ou , isto significa que a versão do pacote é demasiado `Python :: 3.6` `Python :: 3.7` `Python :: 3.8` velha, ou provavelmente, a embalagem já está fora de manutenção.

Pode consultar a versão Python da sua aplicação de função a partir do [portal Azure.](https://portal.azure.com) Navegue para a sua aplicação de função, escolha **o Explorador de Recursos**e selecione **Go**.

:::image type="content" source="media/recover-module-not-found/resource-explorer.png" alt-text="Abra o Explorador de Recursos para a aplicação de função no portal":::

Depois da carga do explorador, procure **linuxFxVersion,** que mostra a versão Python.

Consulte [a atualização do seu pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [substitua a embalagem por equivalentes](#replace-the-package-with-equivalents) para mitigação.

### <a name="the-package-conflicts-with-other-packages"></a>O pacote entra em conflito com outros pacotes

Se tiver verificado que a embalagem está resolvida corretamente com as rodas Linux adequadas, pode haver um conflito com outros pacotes. Em certos pacotes, as documentações do PyPi podem clarificar os módulos incompatíveis. Por exemplo, em [`azure 4.0.0`](https://pypi.org/project/azure/4.0.0/) , há uma declaração da seguinte forma:

<pre>This package isn't compatible with azure-storage.
If you installed azure-storage, or if you installed azure 1.x/2.x and didn’t uninstall azure-storage,
you must uninstall azure-storage first.</pre>

Pode encontrar a documentação para a sua versão do pacote em `https://pypi.org/project/<package-name>/<package-version>` .

Consulte [a atualização do seu pacote para a versão mais recente](#update-your-package-to-the-latest-version) ou [substitua a embalagem por equivalentes](#replace-the-package-with-equivalents) para mitigação.

### <a name="the-package-only-supports-windows-or-macos-platforms"></a>O pacote suporta apenas plataformas Windows ou macOS

Abra o `requirements.txt` com um editor de texto e verifique o pacote em `https://pypi.org/project/<package-name>` . Alguns pacotes funcionam apenas nas plataformas Windows ou macOS. Por exemplo, o pywin32 só funciona no Windows.

O `Module Not Found` erro pode não ocorrer quando estiver a utilizar o Windows ou o macOS para o desenvolvimento local. No entanto, o pacote não importa as funções Azure, que utiliza o Linux em tempo de execução. Isto é provavelmente causado pela utilização `pip freeze` para exportar ambiente virtual em requisitos.txt a partir do seu Windows ou máquina macOS durante a inicialização do projeto.

Consulte [Substituir a embalagem por equivalentes](#replace-the-package-with-equivalents) ou [requisitos de artesanato.txt](#handcraft-requirementstxt) para mitigação.

## <a name="mitigate-modulenotfounderror"></a>Módulo MitigaçãoNotFoundError

Seguem-se potenciais atenuações para questões relacionadas com módulos. Use os [diagnósticos acima](#diagnose-modulenotfounderror) para determinar qual destas mitigações tentar.

### <a name="enable-remote-build"></a>Ativar a construção remota

Certifique-se de que a construção remota está ativada. A forma como o fazes depende do teu método de implantação.

# <a name="visual-studio-code"></a>[Visual Studio Code](#tab/vscode)
Certifique-se de que a versão mais recente da [extensão Azure Functions para o Código do Estúdio Visual](https://marketplace.visualstudio.com/items?itemName=ms-azuretools.vscode-azurefunctions) está instalada. Verifique se `.vscode/settings.json` existe e contém a definição `"azureFunctions.scmDoBuildDuringDeployment": true` . Caso contrário, por favor, crie este ficheiro com a `azureFunctions.scmDoBuildDuringDeployment` definição ativada e reenfectue o projeto.

# <a name="azure-functions-core-tools"></a>[Ferramentas principais de funções Azure](#tab/coretools)

Certifique-se de que a versão mais recente das [Ferramentas Principais de Funções Azure](https://github.com/Azure/azure-functions-core-tools/releases) está instalada. Vá à pasta do projeto de função local e use `func azure functionapp publish <app-name>` para implantação.

# <a name="manual-publishing"></a>[Publicação manual](#tab/manual)

Se estiver a publicar manualmente o seu pacote no `https://<app-name>.scm.azurewebsites.net/api/zipdeploy` ponto final, certifique-se de que tanto **SCM_DO_BUILD_DURING_DEPLOYMENT** como **ENABLE_ORYX_BUILD** estão definidos como **verdadeiros**. Para saber mais, [consulte como trabalhar com as definições de aplicação](functions-how-to-use-azure-function-app-settings.md#settings).

---

### <a name="build-native-dependencies"></a>Construir dependências nativas

Certifique-se de que a versão mais recente de **Ferramentas** Core do Docker e [do Azure Functions](https://github.com/Azure/azure-functions-core-tools/releases) está instalada. Vá à pasta do projeto de função local e use `func azure functionapp publish <app-name> --build-native-deps` para implantação.

### <a name="update-your-package-to-the-latest-version"></a>Atualize o seu pacote para a versão mais recente

Navegue na versão mais recente do pacote `https://pypi.org/project/<package-name>` e verifique os **Classificadores:** secção. A embalagem deve ser `OS Independent` , ou compatível com `POSIX` ou em Sistema `POSIX :: Linux` **Operativo**. Além disso, a Linguagem de Programação deve conter `Python :: 3` `Python :: 3.6` , , ou `Python :: 3.7` `Python :: 3.8` .

Se estiverem corretas, pode atualizar o pacote para a versão mais recente alterando a linha `<package-name>~=<latest-version>` em requisitos.txt.

### <a name="handcraft-requirementstxt"></a>Requisitos de artesanato.txt

Alguns desenvolvedores usam `pip freeze > requirements.txt` para gerar a lista de pacotes Python para os seus ambientes em desenvolvimento. Embora esta conveniência deva funcionar na maioria dos casos, pode haver problemas em cenários de implementação de plataformas cruzadas, como o desenvolvimento de funções localmente no Windows ou macOS, mas a publicação para uma aplicação de função, que funciona no Linux. Neste cenário, `pip freeze` pode introduzir dependências ou dependências inesperadas do sistema operativo para o seu ambiente de desenvolvimento local. Estas dependências podem quebrar a aplicação de função Python quando estiver em funcionamento no Linux.

A melhor prática é verificar a declaração de importação de cada ficheiro .py no seu código fonte do projeto e apenas fazer o check-in desses módulos em ficheiro requirements.txt. Isto garante que a resolução de pacotes pode ser manuseada corretamente em diferentes sistemas operativos.

### <a name="replace-the-package-with-equivalents"></a>Substitua a embalagem por equivalentes

Em primeiro lugar, devemos dar uma olhada na versão mais recente do pacote em `https://pypi.org/project/<package-name>` . Normalmente, este pacote tem a sua própria página GitHub, vá à secção **de Problemas** no GitHub e procure se o seu problema foi corrigido. Em caso afirmativo, atualize o pacote para a versão mais recente.

Por vezes, o pacote pode ter sido integrado na [Biblioteca Padrão python](https://docs.python.org/3/library/) (como o pathlib). Em caso afirmativo, uma vez que fornecemos uma certa distribuição python em Funções Azure (Python 3.6, Python 3.7 e Python 3.8), o pacote nos seus requisitos.txt deve ser removido.

No entanto, se está a enfrentar um problema que não foi corrigido e que está num prazo. Encorajo-vos a pesquisar e a encontrar um pacote semelhante para o vosso projeto. Normalmente, a comunidade Python irá fornecer-lhe uma grande variedade de bibliotecas semelhantes que você pode usar.

## <a name="next-steps"></a>Passos seguintes

Se não conseguir resolver o seu problema relacionado com o módulo, por favor reporte isto à equipa de Funções:

> [!div class="nextstepaction"]
> [Reportar um problema não resolvido](https://github.com/Azure/azure-functions-python-worker/issues)
