---
title: Trabalhando com módulos node.js
description: Saiba como trabalhar com módulos Node.js ao utilizar o Serviço de Aplicações Azure ou os Serviços cloud.
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
ms.openlocfilehash: 61be6bcd957a4e81147d5ef472b8f850e5605e41
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "70309281"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizar Módulos do Node.js com aplicações do Azure
Este documento fornece orientações sobre a utilização de módulos Node.js com aplicações hospedadas no Azure. Fornece orientações para garantir que a sua aplicação utiliza uma versão específica de um módulo, bem como a utilização de módulos nativos com o Azure.

Se já está familiarizado com a utilização de módulos Node.js, **pacote.json** e ficheiros **npm-shrinkwrap.json,** as seguintes informações fornecem um resumo rápido do que é discutido neste artigo:

* O Azure App Service compreende os ficheiros **package.json** e **npm-shrinkwrap.json** e pode instalar módulos com base em entradas nestes ficheiros.

* A Azure Cloud Services espera que todos os módulos sejam instalados no ambiente de desenvolvimento, e o diretório de **\_módulos** de nó seja incluído como parte do pacote de implementação. É possível permitir o suporte para a instalação de módulos utilizando **ficheiros package.json** ou **npm-shrinkwrap.json** nos Serviços cloud; no entanto, esta configuração requer personalização dos scripts padrão utilizados pelos projetos do Cloud Service. Para um exemplo de como configurar este ambiente, consulte a [tarefa do Azure Startup para executar a instalação npm para evitar a implementação](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown) de módulos de nó

> [!NOTE]
> As Máquinas Virtuais Azure não são discutidas neste artigo, uma vez que a experiência de implantação num VM está dependente do sistema operativo hospedado pela Máquina Virtual.
> 
> 

## <a name="nodejs-modules"></a>Módulos Node.js
Os módulos são pacotes JavaScript carregáveis que fornecem funcionalidades específicas para a sua aplicação. Os módulos são normalmente instalados utilizando a ferramenta de linha de comando **npm,** no entanto alguns módulos (como o módulo http) são fornecidos como parte do pacote node.js central.

Quando os módulos são instalados, são armazenados no diretório de **\_módulos** de nó na raiz da sua estrutura de diretório de aplicações. Cada módulo dentro do diretório de **\_módulos** de nó mantém o seu próprio diretório que contém quaisquer módulos de que dependa, e este comportamento repete-se para cada módulo até ao fundo da cadeia de dependência. Este ambiente permite que cada módulo instalado tenha os seus próprios requisitos de versão para os módulos de que depende, no entanto pode resultar numa estrutura de diretório bastante grande.

A implementação do diretório de **\_módulos** de nó como parte da sua aplicação aumenta o tamanho da implementação quando comparada com a utilização de um ficheiro **embalagem.json** ou **npm-shrinkwrap.json;** no entanto, garante que as versões dos módulos utilizados na produção são as mesmas que os módulos utilizados no desenvolvimento.

### <a name="native-modules"></a>Módulos Nativos
Embora a maioria dos módulos sejam simplesmente ficheiros JavaScript de texto simples, alguns módulos são imagens binárias específicas da plataforma. Estes módulos são compilados no momento de instalação, geralmente utilizando Python e nó-gyp. Uma vez que o Azure Cloud Services conta com a implementação da pasta de **\_módulos** de nó como parte da aplicação, qualquer módulo nativo incluído como parte dos módulos instalados deve funcionar num serviço de nuvem desde que seja instalado e compilado num sistema de desenvolvimento do Windows.

O Azure App Service não suporta todos os módulos nativos e pode falhar na compilação de módulos com pré-requisitos específicos. Embora alguns módulos populares como o MongoDB tenham dependências nativas opcionais e funcionem bem sem eles, duas salões revelaram-se bem-sucedidas com quase todos os módulos nativos disponíveis hoje:

* Executar **a instalação npm** numa máquina do Windows que tenha todos os pré-requisitos do módulo nativo instalados. Em seguida, implemente a pasta de **\_módulos** de nó criado como parte da aplicação para o Serviço de Aplicações Azure.

  * Antes de compilar, verifique se a instalação local nonódeo.js tem arquitetura correspondente e a versão é o mais próxima possível da utilizada no Azure (os valores atuais podem ser verificados no tempo de execução a partir do processo de **propriedades.arch** e **process.version).**

* O Azure App Service pode ser configurado para executar scripts personalizados de bash ou shell durante a implementação, dando-lhe a oportunidade de executar comandos personalizados e configurar com precisão a forma como a **instalação npm** está sendo executada. Para um vídeo que mostre como configurar esse ambiente, consulte scripts de implementação de [websites personalizados com Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Usando um ficheiro pacote.json

O ficheiro **package.json** é uma forma de especificar as dependências de nível superior que a sua aplicação necessita para que a plataforma de hospedagem possa instalar as dependências, em vez de exigir que inclua a pasta de **\_módulos** de nó como parte da implementação. Depois de a aplicação ter sido implantada, o comando de **instalação npm** é utilizado para analisar o ficheiro **package.json** e instalar todas as dependências listadas.

Durante o desenvolvimento, pode utilizar automaticamente os parâmetros **--save**, **--save-dev**, ou **-- save-optional** ao instalar módulos para adicionar automaticamente uma entrada para o módulo ao seu ficheiro **pacote.json.** Para mais informações, consulte a [instalação npm .](https://docs.npmjs.com/cli/install)

Um problema potencial com o ficheiro **package.json** é que apenas especifica a versão para dependências de alto nível. Cada módulo instalado pode ou não especificar a versão dos módulos de que depende, pelo que é possível que possa acabar com uma cadeia de dependência diferente da utilizada no desenvolvimento.

> [!NOTE]
> Ao ser implantado no Serviço de Aplicações Azure, se o seu ficheiro <b>pacote.json</b> se referir a um módulo nativo, poderá ver um erro semelhante ao seguinte exemplo ao publicar a aplicação utilizando git:
> 
> npm ERR! module-name@0.6.0instalar: 'node-gyp configurar construção'
> 
> npm ERR! 'cmd "/c" "node-gyp configuração construção" falhou com 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Usando um ficheiro npm-shrinkwrap.json
O ficheiro **npm-shrinkwrap.json** é uma tentativa de abordar as limitações de versão do módulo do ficheiro **package.json.** Embora o ficheiro **package.json** apenas inclua versões para os módulos de topo, o ficheiro **npm-shrinkwrap.json** contém os requisitos da versão para a cadeia de dependência do módulo completo.

Quando a sua aplicação estiver pronta para produção, pode bloquear os requisitos da versão e criar um ficheiro **npm-shrinkwrap.json** utilizando o comando **de encolhimento npm.** Este comando utilizará as versões atualmente instaladas na pasta de **\_módulos** de nó e gravará estas versões para o ficheiro **npm-shrinkwrap.json.** Depois de a aplicação ter sido implantada para o ambiente de hospedagem, o comando de **instalação npm** é usado para analisar o ficheiro **npm-shrinkwrap.json** e instalar todas as dependências listadas. Para mais informações, consulte [o npm-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ao ser implantado no Azure App Service, se o seu ficheiro <b>npm-shrinkwrap.json</b> referer um módulo nativo, poderá ver um erro semelhante ao seguinte exemplo ao publicar a aplicação utilizando git:
> 
> npm ERR! module-name@0.6.0instalar: 'node-gyp configurar construção'
> 
> npm ERR! 'cmd "/c" "node-gyp configuração construção" falhou com 1
> 
> 

## <a name="next-steps"></a>Passos seguintes
Agora que compreende como usar os módulos Node.js com o Azure, aprenda a [especificar a versão Node.js,](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md) [construa e implemente uma aplicação web Node.js](app-service/app-service-web-get-started-nodejs.md)e como utilizar a Interface de [Linha de Comando Azure para Mac e Linux.](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/)

Para mais informações, consulte o [Centro de Programadores de Node.js](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
