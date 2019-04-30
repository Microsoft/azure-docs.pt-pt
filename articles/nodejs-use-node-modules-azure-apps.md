---
title: Trabalhar com módulos do node. js
description: Saiba como trabalhar com módulos node. js ao utilizar o serviço de aplicações do Azure ou serviços Cloud.
services: ''
documentationcenter: nodejs
author: TomArcher
manager: routlaw
editor: ''
ms.assetid: c0e6cd3d-932d-433e-b72d-e513e23b4eb6
ms.service: multiple
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: nodejs
ms.topic: article
ms.date: 08/17/2016
ms.author: tarcher
ms.openlocfilehash: 08f3a2dcf9d36eb76b2f657232a426b078066273
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "60634926"
---
# <a name="using-nodejs-modules-with-azure-applications"></a>Utilizar Módulos do Node.js com aplicações do Azure
Este documento fornece orientações sobre como utilizar módulos do node. js com aplicações alojadas no Azure. Oferece orientações para garantir que a sua aplicação utiliza uma versão específica de um módulo, bem como utilizar módulos nativos com o Azure.

Se já estiver familiarizado com a utilização de módulos do node. js, **Package. JSON** e **npm shrinkwrap.json** arquivos, as seguintes informações fornecem um resumo rápido das quais é abordada neste artigo:

* Serviço de aplicações do Azure compreende **Package. JSON** e **npm shrinkwrap.json** ficheiros e pode instalar os módulos com base nas entradas nesses arquivos.

* Serviços Cloud do Azure espera que todos os módulos esteja instalado no ambiente de desenvolvimento e o **nó\_módulos** diretório para ser incluído como parte do pacote de implementação. É possível ativar o suporte para instalar os módulos usando **Package. JSON** ou **npm shrinkwrap.json** ficheiros nos serviços Cloud; no entanto, esta configuração requer personalização do padrão scripts usados pelos projetos de serviço em nuvem. Para obter um exemplo de como configurar este ambiente, consulte [tarefa de arranque do Azure para executar a instalação de npm para evitar a implementação de módulos de nó](https://github.com/woloski/nodeonazure-blog/blob/master/articles/startup-task-to-run-npm-in-azure.markdown)

> [!NOTE]
> Máquinas virtuais do Azure não são abordadas neste artigo, como a experiência de implementação numa VM é dependente de sistema operacional alojado pela máquina Virtual.
> 
> 

## <a name="nodejs-modules"></a>Módulos do node. js
Os módulos são carregável pacotes de JavaScript que fornecem funcionalidade específica para a sua aplicação. Módulos são geralmente instalados usando o **npm** da linha de comandos ferramenta, no entanto alguns módulos (por exemplo, o módulo de http) são fornecidos como parte do pacote de node. js core.

Quando os módulos são instalados, eles são armazenados no **nó\_módulos** diretório na raiz da sua estrutura de diretório do aplicativo. Cada módulo dentro de **nó\_módulos** directory mantém seu próprio diretório que contém quaisquer módulos que depende, e esse comportamento é repetida para cada módulo todo o caminho para baixo de cadeia de dependência. Esse ambiente permite que cada módulo instalado para que os seus próprios requisitos de versão para os módulos depende, no entanto, pode resultar numa estrutura de diretórios de grandes.

Implementar o **nó\_módulos** diretório como parte da sua aplicação aumenta o tamanho da implementação quando em comparação ao uso de um **Package. JSON** ou  **npm shrinkwrap.json** ficheiro; no entanto, garantir que as versões dos módulos utilizados na produção são os mesmos que os módulos usados no desenvolvimento.

### <a name="native-modules"></a>Módulos nativos
Embora a maioria dos módulos são arquivos de JavaScript simplesmente texto sem formatação, alguns módulos são específicas da plataforma de imagens binárias. Estes módulos são compilados no momento da instalação, normalmente, com o Python e gyp de nó. Uma vez que dependem de serviços Cloud do Azure a **nó\_módulos** pasta a ser implementada como parte do módulo nativo qualquer de aplicativo incluído como parte dos módulos instalados deve funcionar num serviço cloud, desde que tenha sido instalada e compilado num sistema de desenvolvimento do Windows.

Serviço de aplicações do Azure não suporta todos os módulos nativos e poderá falhar ao compilar módulos com os pré-requisitos específicos. Embora alguns módulos populares, como o MongoDB têm dependências nativas opcionais e funcionam bem sem eles, duas soluções alternativas provou concluída com êxito com quase todos os módulos nativos disponíveis hoje:

* Execute **npm instalar** num computador Windows que tem os pré-requisitos de todos os nativo do módulo instalados. Em seguida, implemente o criada **nó\_módulos** pasta como parte da aplicação para o serviço de aplicações do Azure.

  * Antes da compilação, verifique se a instalação do node. js local tem arquitetura correspondente e se a versão é o mais próximo possível para aquele usado no Azure (os valores atuais podem ser verificados no tempo de execução a partir das propriedades **process.arch** e **process.version**).

* Serviço de aplicações do Azure pode ser configurado para executar o bash personalizada ou scripts do shell durante a implementação, dando-lhe a oportunidade de executar comandos personalizados e configurem com precisão a maneira **npm instalar** está a ser executado. Para ver um vídeo que mostra como configurar esse ambiente, consulte [Scripts de implantação do Web site personalizado com Kudu](https://azure.microsoft.com/resources/videos/custom-web-site-deployment-scripts-with-kudu/).

### <a name="using-a-packagejson-file"></a>Utilizar um ficheiro Package.

O **Package. JSON** arquivo é uma maneira de especificar as dependências de nível superior requer que seu aplicativo para que a plataforma de alojamento pode instalar as dependências, em vez de exigir a incluir o **nó\_ módulos** pasta como parte da implementação. Depois da aplicação foi implementada, o **instalar o npm** comando é utilizado para analisar a **Package. JSON** de ficheiros e instale todas as dependências listadas.

Durante o desenvolvimento, pode utilizar o **– guardar**, **– save-dev**, ou **– guardar opcional** parâmetros ao instalar os módulos para adicionar uma entrada para o módulo para sua **Package. JSON** automaticamente de ficheiros. Para obter mais informações, consulte [instalação de npm](https://docs.npmjs.com/cli/install).

Um potencial problema com o **Package. JSON** ficheiro é que ele especifica apenas uma versão para dependências de nível superior. Cada módulo instalado pode ou não pode especificar a versão dos módulos depende, e então, é possível que pode acabar com uma cadeia de dependência diferente daquele usado no desenvolvimento.

> [!NOTE]
> Ao implementar no serviço de aplicações do Azure, se sua <b>Package. JSON</b> arquivo faz referência a um módulo nativo, poderá ver um erro semelhante ao seguinte exemplo quando a publicação da aplicação com o Git:
> 
> npm ERR! module-name@0.6.0 instalar: 'Configurar o nó gyp compilação'
> 
> npm ERR! "cmd"/ c""Configurar o nó gyp compilação"' falhou com 1
> 
> 

### <a name="using-a-npm-shrinkwrapjson-file"></a>Através de um ficheiro de npm shrinkwrap.json
O **npm shrinkwrap.json** arquivo é uma tentativa de resolver as limitações de controlo de versões do módulo da **Package. JSON** ficheiro. Enquanto o **Package. JSON** ficheiro inclui apenas versões para os módulos de nível superior, o **npm shrinkwrap.json** arquivo contém os requisitos de versão para a cadeia de dependência do módulo completa.

Quando o aplicativo está pronto para produção, pode bloquear os requisitos de versão e criar uma **npm shrinkwrap.json** ficheiro utilizando o **npm shrinkwrap** comando. Este comando irá utilizar as versões atualmente instaladas no **nó\_módulos** pasta e registe estas versões para o **npm shrinkwrap.json** ficheiro. Depois de implementar a aplicação ao ambiente de hospedagem, o **instalar o npm** comando é utilizado para analisar a **npm shrinkwrap.json** de ficheiros e instale todas as dependências listadas. Para obter mais informações, consulte [npm shrinkwrap](https://docs.npmjs.com/cli/shrinkwrap).

> [!NOTE]
> Ao implementar no serviço de aplicações do Azure, se sua <b>npm shrinkwrap.json</b> arquivo faz referência a um módulo nativo, poderá ver um erro semelhante ao seguinte exemplo quando a publicação da aplicação com o Git:
> 
> npm ERR! module-name@0.6.0 instalar: 'Configurar o nó gyp compilação'
> 
> npm ERR! "cmd"/ c""Configurar o nó gyp compilação"' falhou com 1
> 
> 

## <a name="next-steps"></a>Passos Seguintes
Agora que sabe como utilizar módulos do node. js com o Azure, saiba como [especificar a versão do node. js](https://github.com/squillace/staging/blob/master/articles/nodejs-specify-node-version-azure-apps.md), [criar e implementar uma aplicação web do node. js](app-service/app-service-web-get-started-nodejs.md), e [como utilizar o linha de comandos do Azure Interface para Mac e Linux](https://azure.microsoft.com/blog/using-windows-azure-with-the-command-line-tools-for-mac-and-linux/).

Para obter mais informações, consulte o [Centro para Programadores do Node.js](/nodejs/azure/).

[specify the Node.js version]: nodejs-specify-node-version-azure-apps.md
[How to use the Azure Command-Line Interface for Mac and Linux]:cli-install-nodejs.md
[Custom Website Deployment Scripts with Kudu]: https://channel9.msdn.com/Shows/Azure-Friday/Custom-Web-Site-Deployment-Scripts-with-Kudu-with-David-Ebbo
