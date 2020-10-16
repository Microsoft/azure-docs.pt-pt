---
title: Trabalhar com Node.js Módulos
description: Saiba como trabalhar com Node.js módulos quando utilizar o Azure App Service ou os Cloud Services.
services: ''
documentationcenter: nodejs
author: rloutlaw
manager: rloutlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: routlaw
ms.openlocfilehash: 6c1bbe48ca5205cf1db49d67a711e9a7523e1845
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "88077123"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizar Módulos do Node.js com aplicações do Azure
Este documento fornece orientações sobre a utilização de módulos Node.js com aplicações hospedadas no Azure. Fornece orientações para garantir que a sua aplicação utiliza uma versão específica de um módulo, bem como a utilização de módulos nativos com o Azure.

Se já está familiarizado com a utilização de módulos Node.js, **package.js** e **npm-shrinkwrap.jsem** ficheiros, as seguintes informações fornecem um resumo rápido do que é discutido neste artigo:

* O Azure App Service compreende **package.jse** **npm-shrinkwrap.jsem** ficheiros e pode instalar módulos baseados em entradas nestes ficheiros.

* O Azure Cloud Services espera que todos os módulos sejam instalados no ambiente de desenvolvimento, e o diretório de ** \_ módulos** de nó para ser incluído como parte do pacote de implementação. É possível permitir o suporte para a instalação de módulos ** utilizandopackage.jsem** ounpm-shrinkwrap.js** em** ficheiros em Serviços cloud; no entanto, esta configuração requer a personalização dos scripts predefinidos utilizados pelos projetos do Cloud Service. Para um exemplo de como configurar este ambiente, consulte a [tarefa da Startup Azure para executar a instalação npm para evitar a implementação de módulos de nó](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> As Máquinas Virtuais Azure não são discutidas neste artigo, uma vez que a experiência de implantação num VM depende do sistema operativo hospedado pela Máquina Virtual.
>
>

## <a name="nodejs-modules"></a>Node.js Módulos
Os módulos são pacotes JavaScript carregados que fornecem funcionalidades específicas para a sua aplicação. Os módulos são normalmente instalados utilizando a ferramenta **npm** command-line, no entanto alguns módulos (como o módulo http) são fornecidos como parte do pacote de Node.js do núcleo.

Quando os módulos são instalados, são armazenados no diretório de ** \_ módulos** de nó na raiz da sua estrutura de diretório de aplicações. Cada módulo dentro do diretório de ** \_ módulos de nó** mantém o seu próprio diretório que contém quaisquer módulos de que depende, e este comportamento repete-se para cada módulo até à cadeia de dependência. Este ambiente permite que cada módulo instalado tenha os seus próprios requisitos de versão para os módulos de que depende, no entanto pode resultar numa estrutura de diretório bastante grande.

A implantação do diretório de ** \_ módulos** de nó como parte da sua aplicação aumenta o tamanho da implementação quando comparada com a utilização de um **package.jsem** ounpm-shrinkwrap.jsem ficheiro; ** no** entanto, garante que as versões dos módulos utilizados na produção são as mesmas que os módulos utilizados no desenvolvimento.

### <a name="native-modules"></a>Módulos Nativos
Embora a maioria dos módulos sejam simplesmente ficheiros JavaScript de texto simples, alguns módulos são imagens binárias específicas da plataforma. Estes módulos são compilados no momento de instalação, geralmente usando Python e nó-gyp. Uma vez que o Azure Cloud Services conta com a pasta de ** \_ módulos** de nó que está a ser implantada como parte da aplicação, qualquer módulo nativo incluído como parte dos módulos instalados deve funcionar num serviço de nuvem desde que tenha sido instalado e compilado num sistema de desenvolvimento do Windows.

O Azure App Service não suporta todos os módulos nativos, e pode falhar na compilação de módulos com pré-requisitos específicos. Enquanto alguns módulos populares como o MongoDB têm dependências nativas opcionais e funcionam bem sem eles, duas soluções revelaram-se bem sucedidas com quase todos os módulos nativos disponíveis hoje:

* Executar **a instalação npm** numa máquina Windows que tenha todos os pré-requisitos do módulo nativo instalados. Em seguida, implemente a pasta **de \_ módulos de nó** criado como parte da aplicação para o Azure App Service.

  * Antes de compilar, verifique se a sua instalação local Node.js tem arquitetura correspondente e a versão está o mais próxima possível da utilizada no Azure (os valores atuais podem ser verificados no tempo de execução a partir do **processo de propriedades.arch** e **process.version).**

* O Azure App Service pode ser configurado para executar scripts personalizados de bash ou shell durante a implementação, dando-lhe a oportunidade de executar comandos personalizados e configurar precisamente a forma como a **instalação npm** está a ser executada. Para um vídeo que mostre como configurar esse ambiente, consulte [scripts de implementação de sites personalizados com Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Usando um package.jsno arquivo

O **package.jsem** ficheiro é uma forma de especificar as dependências de nível superior que a sua aplicação requer para que a plataforma de hospedagem possa instalar as dependências, em vez de exigir que inclua a pasta de ** \_ módulos de nó** como parte da implementação. Depois de a aplicação ter sido implementada, o comando **de instalação npm** é utilizado para analisar a **package.jsno** ficheiro e instalar todas as dependências listadas.

Durante o desenvolvimento, pode utilizar os parâmetros **--save-dev**, **ou** **--guardar-opcionais** ao instalar módulos para adicionar automaticamente uma entrada do módulo aopackage.js** no** ficheiro. Para mais informações, consulte [a instalação npm.](https://docs.npmjs.com/cli/install)

Um problema potencial com o **package.jsem** ficheiro é que apenas especifica a versão para dependências de alto nível. Cada módulo instalado pode ou não especificar a versão dos módulos de que depende, pelo que é possível que acabe com uma cadeia de dependência diferente da utilizada no desenvolvimento.

> [!NOTE]
> Ao implementar no Azure App Service, se o seu <b>package.jsem</b> referências de ficheiros um módulo nativo poderá ver um erro semelhante ao seguinte exemplo ao publicar a aplicação utilizando o Git:
>
> npm ERR! module-name@0.6.0 instalação: 'node-gyp configure construção'
>
> npm ERR! 'cmd "/c" "node-gyp configure build" falhou com 1
>
>

### <a name="using-a-npm-shrinkwrapjson-file"></a>Usando um npm-shrinkwrap.jsno arquivo
O **npm-shrinkwrap.jsem** ficheiro é uma tentativa de resolver as limitações de versão do módulo dopackage.js** em** ficheiro. Embora a **package.jsem** ficheiros inclua apenas versões para os módulos de nível superior, o **npm-shrinkwrap.jsem** ficheiro contém os requisitos de versão para toda a cadeia de dependência do módulo.

Quando a sua aplicação estiver pronta para ser produção, pode bloquear os requisitos da versão e criar uma **npm-shrinkwrap.jsno** ficheiro utilizando o comando **de encolhimento npm.** Este comando utilizará as versões atualmente ** \_ instaladas** na pasta dos módulos de nó, e gravará estas versões ** nonpm-shrinkwrap.jsem** ficheiro. Depois de a aplicação ter sido implantada no ambiente de hospedagem, o comando **de instalação npm** é utilizado para analisar o **npm-shrinkwrap.jsem** ficheiro e instalar todas as dependências listadas. Para obter mais informações, consulte [npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ao implementar no Azure App Service, se o seu <b>npm-shrinkwrap.jsem</b> referências de ficheiros um módulo nativo poderá ver um erro semelhante ao seguinte exemplo ao publicar a aplicação utilizando o Git:
>
> npm ERR! module-name@0.6.0 instalação: 'node-gyp configure construção'
>
> npm ERR! 'cmd "/c" "node-gyp configure build" falhou com 1
>
>

## <a name="next-steps"></a>Passos seguintes
Agora que compreende como utilizar Node.js módulos com o Azure, aprenda a [especificar a versão Node.js,](https://github.com/squillace/nodejs-microservice) [construa e implemente uma aplicação web Node.js](app-service/quickstart-nodejs.md), e [Como utilizar a Interface de Command-Line Azure para Mac e Linux.](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/)

Para mais informações, consulte o [Centro de Programadores de Node.js](/azure/developer/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
