---
title: Trabalhando com módulos do node. js
description: Saiba como trabalhar com módulos do node. js ao usar Azure App serviço ou serviços de nuvem.
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
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309281"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizar Módulos do Node.js com aplicações do Azure
Este documento fornece orientação sobre como usar módulos node. js com aplicativos hospedados no Azure. Ele fornece orientação sobre como garantir que seu aplicativo use uma versão específica de um módulo, bem como o uso de módulos nativos com o Azure.

Se você já estiver familiarizado com o uso de módulos do node. js, arquivos **Package. JSON** e **NPM-shrinkwrap. JSON** , as informações a seguir fornecerão um resumo rápido do que é discutido neste artigo:

* Azure App serviço compreende os arquivos **Package. JSON** e **NPM-shrinkwrap. JSON** e pode instalar módulos com base em entradas nesses arquivos.

* Os serviços de nuvem do Azure esperam que todos os módulos sejam instalados no ambiente de desenvolvimento e o diretório **\_node modules** a ser incluído como parte do pacote de implantação. É possível habilitar o suporte para instalar módulos usando arquivos **Package. JSON** ou **NPM-shrinkwrap. JSON** nos serviços de nuvem; no entanto, essa configuração requer a personalização dos scripts padrão usados por projetos de serviço de nuvem. Para obter um exemplo de como configurar esse ambiente, consulte [tarefa de inicialização do Azure para executar a instalação do NPM para evitar a implantação de módulos de nó](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> As máquinas virtuais do Azure não são discutidas neste artigo, pois a experiência de implantação em uma VM depende do sistema operacional hospedado pela máquina virtual.
> 
> 

## <a name="nodejs-modules"></a>Módulos do node. js
Os módulos são pacotes de JavaScript carregáveis que fornecem funcionalidade específica para seu aplicativo. Os módulos geralmente são instalados usando a ferramenta de linha de comando **NPM** , no entanto, alguns módulos (como o módulo HTTP) são fornecidos como parte do pacote do node. js principal.

Quando os módulos são instalados, eles são armazenados no **diretório\_node modules** na raiz da estrutura do diretório do aplicativo. Cada módulo dentro do diretório de **módulos do nó\_** mantém seu próprio diretório que contém os módulos dos quais ele depende, e esse comportamento se repete para cada módulo até a cadeia de dependência. Esse ambiente permite que cada módulo instalado tenha seus próprios requisitos de versão para os módulos dos quais depende, no entanto, isso pode resultar em uma grande estrutura de diretórios.

Implantar o diretório **Node\_modules** como parte do seu aplicativo aumenta o tamanho da implantação quando comparado ao uso de um arquivo **Package. JSON** ou **NPM-shrinkwrap. JSON** ; no entanto, ele garante que as versões do os módulos usados na produção são os mesmos que os módulos usados no desenvolvimento.

### <a name="native-modules"></a>Módulos nativos
Embora a maioria dos módulos seja simplesmente arquivos JavaScript de texto sem formatação, alguns módulos são imagens binárias específicas da plataforma. Esses módulos são compilados no momento da instalação, geralmente usando Python e node-gyp. Como os serviços de nuvem do Azure dependem da pasta **\_node modules** que está sendo implantada como parte do aplicativo, qualquer módulo nativo incluído como parte dos módulos instalados deve funcionar em um serviço de nuvem desde que tenha sido instalado e compilado em um Sistema de desenvolvimento do Windows.

Azure App serviço não dá suporte a todos os módulos nativos e pode falhar ao compilar módulos com pré-requisitos específicos. Embora alguns módulos populares, como o MongoDB, tenham dependências nativas opcionais e funcionem bem sem eles, duas soluções alternativas provaram êxito com quase todos os módulos nativos disponíveis hoje:

* Execute o **NPM install** em um computador Windows que tenha todos os pré-requisitos do módulo nativo instalados. Em seguida, implante a **pasta\_módulos do nó** criado como parte do aplicativo para Azure app serviço.

  * Antes de Compilar, verifique se a instalação do node. js local tem a arquitetura correspondente e se a versão é a mais próxima possível da usada no Azure (os valores atuais podem ser verificados em tempo de execução nas propriedades **Process. Arch** e **Process. Version** ).

* Azure App serviço pode ser configurado para executar scripts de bash ou shell personalizados durante a implantação, oferecendo a você a oportunidade de executar comandos personalizados e configurar precisamente a maneira como a **instalação do NPM** está sendo executada. Para ver um vídeo mostrando como configurar esse ambiente, consulte [scripts de implantação de site personalizados com kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Usando um arquivo Package. JSON

O arquivo **Package. JSON** é uma maneira de especificar as dependências de nível superior que seu aplicativo requer para que a plataforma de hospedagem possa instalar as dependências, em vez de exigir que você inclua a pasta **\_node modules** como parte do planta. Depois que o aplicativo tiver sido implantado, o comando **NPM install** será usado para analisar o arquivo **Package. JSON** e instalar todas as dependências listadas.

Durante o desenvolvimento, você pode usar os parâmetros **--Save**, **--Save-dev**ou **--Save-optional** ao instalar os módulos para adicionar uma entrada para o módulo ao arquivo **Package. JSON** automaticamente. Para obter mais informações, consulte [NPM-install](https://docs.npmjs.com/cli/install).

Um possível problema com o arquivo **Package. JSON** é que ele especifica apenas a versão para as dependências de nível superior. Cada módulo instalado pode ou não especificar a versão dos módulos de que depende e, portanto, é possível que você acabe com uma cadeia de dependências diferente daquela usada no desenvolvimento.

> [!NOTE]
> Ao implantar no Azure App Service, se o arquivo <b>Package. JSON</b> fizer referência a um módulo nativo, você poderá ver um erro semelhante ao exemplo a seguir ao publicar o aplicativo usando o Git:
> 
> NPM ERR! module-name@0.6.0instalar: ' node-gyp configurar compilação '
> 
> NPM ERR! ' cmd "/c" "node-gyp configurar compilação" ' falhou com 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Usando um arquivo NPM-shrinkwrap. JSON
O arquivo **NPM-shrinkwrap. JSON** é uma tentativa de resolver as limitações de controle de versão do módulo do arquivo **Package. JSON** . Embora o arquivo **Package. JSON** inclua apenas versões para os módulos de nível superior, o arquivo **NPM-shrinkwrap. JSON** contém os requisitos de versão para a cadeia de dependência do módulo completo.

Quando seu aplicativo estiver pronto para produção, você poderá bloquear os requisitos de versão e criar um arquivo **NPM-shrinkwrap. JSON** usando o comando **NPM shrinkwrap** . Esse comando usará as versões atualmente instaladas na **pasta\_node modules** e registrará essas versões no arquivo **NPM-shrinkwrap. JSON** . Depois que o aplicativo tiver sido implantado no ambiente de hospedagem, o comando **NPM install** será usado para analisar o arquivo **NPM-shrinkwrap. JSON** e instalar todas as dependências listadas. Para obter mais informações, consulte [NPM-shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ao implantar no Azure App Service, se o arquivo <b>NPM-shrinkwrap. JSON</b> fizer referência a um módulo nativo, você poderá ver um erro semelhante ao exemplo a seguir ao publicar o aplicativo usando o Git:
> 
> NPM ERR! module-name@0.6.0instalar: ' node-gyp configurar compilação '
> 
> NPM ERR! ' cmd "/c" "node-gyp configurar compilação" ' falhou com 1
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Agora que você entende como usar os módulos do node. js com o Azure, saiba como [especificar a versão do node. js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [criar e implantar um aplicativo Web node. js](app-service/app-service-web-get-started-nodejs.md)e [como usar a interface de linha de comando do Azure para Mac e Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Para obter mais informações, consulte o [Centro para Programadores do Node.js](/azure/javascript/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
