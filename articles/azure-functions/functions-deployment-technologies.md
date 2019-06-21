---
title: Tecnologias de implantação nas funções do Azure | Documentos da Microsoft
description: Saiba as vantagens e desvantagens das formas diferentes, que pode implementar código para as funções do Azure.
services: functions
documentationcenter: .net
author: ColbyTresness
manager: dariac
ms.service: azure-functions
ms.custom: vs-azure
ms.topic: conceptual
ms.date: 04/25/2019
ms.author: cotresne
ms.openlocfilehash: 10976c9cf16dfab4c31d0d77c519dc3277204a51
ms.sourcegitcommit: 2d3b1d7653c6c585e9423cf41658de0c68d883fa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/20/2019
ms.locfileid: "67293052"
---
# <a name="deployment-technologies-in-azure-functions"></a>Tecnologias de implantação nas funções do Azure

Há algumas tecnologias diferentes, que pode utilizar para implementar o código de projeto de funções do Azure para o Azure. Este artigo fornece uma lista exaustiva dessas tecnologias, informa a quais tecnologias estão disponíveis para quais tipos de funções, explica o que realmente acontece quando cada método é usado e fornece recomendações para o melhor método utilizar no vários cenários. As várias ferramentas que suportam a implementação para as funções do Azure são ajustadas para a tecnologia certa com base no seu contexto.

## <a name="deployment-technology-availability"></a>Disponibilidade de tecnologia de implementação

> [!IMPORTANT]
> Funções do Azure suporta várias plataforma de desenvolvimento local e o alojamento em dois sistemas operacionais: Windows e Linux. Existem três planos de alojamento atualmente disponíveis, cada uma com diferentes comportamentos - [consumo](functions-scale.md#consumption-plan), [Premium](functions-scale.md#premium-plan), e [dedicado (serviço de aplicações)](functions-scale.md#app-service-plan). Nem todas as tecnologias de implantação estão disponíveis para cada sabor das funções do Azure.

| Tecnologia de implantação | Consumo de Windows | Premium do Windows (pré-visualização) | Windows dedicado  | Consumo de Linux (pré-visualização) | Linux dedicado |
|-----------------------|:-------------------:|:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| O URL do pacote externo<sup>1</sup> |✔|✔|✔|✔|✔|
| Implementar zip |✔|✔|✔| |✔|
| Contentor do docker | | | | |✔|
| Web Deploy |✔|✔|✔| | |
| Controlo de origem |✔|✔|✔| |✔|
| Local Git<sup>1</sup> |✔|✔|✔| |✔|
| Sincronização da cloud<sup>1</sup> |✔|✔|✔| |✔|
| FTP<sup>1</sup> |✔|✔|✔| |✔|
| A edição de portal |✔|✔|✔| |✔<sup>2</sup>|

<sup>1</sup> tecnologia de implementação que requer [sincronização do acionador manual](#trigger-syncing).
<sup>2</sup> portal edição está ativada para apenas os acionadores HTTP e temporizador para as funções no Linux com o plano de dedicado.

## <a name="key-concepts"></a>Conceitos-chave

Antes de continuar, é importante obter alguns dos principais conceitos que irão ser fundamentais para entender como implementações funcionam nas funções do Azure.

### <a name="trigger-syncing"></a>A sincronização de Acionador

Quando altera qualquer uma das suas acionadores, a infraestrutura de funções tem de estar atento estas alterações. Esta sincronização ocorre automaticamente para muitas tecnologias de implantação. No entanto, em alguns casos tem sincronizar manualmente sua acionadores. Ao implementar as atualizações através de um URL do pacote externo, local Git, da sincronização da cloud ou FTP, tem de ser-se sincronizar manualmente sua acionadores. Pode sincronizar os acionadores de uma das três formas:

* Reinicie a aplicação de função no portal do Azure
* Enviar um pedido de HTTP POST para `https://www.{functionappname}.azurewebsites.net/admin/host/synctriggers?code=<API_KEY>` utilizando o [chave mestra](functions-bindings-http-webhook.md#authorization-keys).
* Enviar um pedido de HTTP POST para `https://management.azure.com/subscriptions/<SUBSCRIPTION_ID>/resourceGroups/<RESOURCE_GROUP_NAME>/providers/Microsoft.Web/sites/<FUNCTION_APP_NAME>/syncfunctiontriggers?api-version=2016-08-01`. Substitua os marcadores de posição pelo seu ID de subscrição, nome do grupo de recursos e o nome da sua aplicação de função.

## <a name="deployment-technology-details"></a>Detalhes da tecnologia de implementação  

### <a name="external-package-url"></a>URL do pacote externo

Pode fazer referência a um ficheiro de pacote remoto (. zip) que contém a aplicação de funções. O ficheiro é transferido do URL fornecido e a aplicação é executada [execução do pacote](run-functions-from-deployment-package.md) modo.

>__Como usá-lo:__ Adicionar `WEBSITE_RUN_FROM_PACKAGE` para as definições da aplicação. O valor desta definição deve ser um URL - a localização do ficheiro de pacote específico que pretende executar. Pode adicionar as definições de qualquer um dos [no portal](functions-how-to-use-azure-function-app-settings.md#settings) ou [ao utilizar a CLI do Azure](/cli/azure/functionapp/config/appsettings#az-functionapp-config-appsettings-set). Se utilizar o armazenamento de Blobs do Azure, deve usar um contêiner privado com um [assinatura de acesso partilhado (SAS)](../vs-azure-tools-storage-manage-with-storage-explorer.md#attach-a-storage-account-by-using-a-shared-access-signature-sas) para conceder acesso de funções para o pacote. Em qualquer altura do reinício da aplicação, irá obter uma cópia do conteúdo, o que significa que sua referência tem de ser válida para o tempo de vida do aplicativo.

>__Quando utilizá-lo:__ Este é o método de implementação apenas suportado para as funções do Azure em execução no Linux no plano de consumo (pré-visualização). Ao atualizar o ficheiro de pacote que está a referenciar a uma aplicação de funções, deve [sincronizar manualmente o acionadores](#trigger-syncing) para indicar ao Auzre que seu aplicativo foi alterado.

### <a name="zip-deploy"></a>Implementar zip

Permite-lhe enviar um ficheiro zip que contém a aplicação de funções para o Azure. Opcionalmente, também pode especificar a sua aplicação iniciar em [execução do pacote](run-functions-from-deployment-package.md) modo.

>__Como usá-lo:__ Implementar com os seus favoritos ferramenta de cliente - [VS Code](functions-create-first-function-vs-code.md#publish-the-project-to-azure), [Visual Studio](functions-develop-vs.md#publish-to-azure), ou o [da CLI do Azure](functions-create-first-azure-function-azure-cli.md#deploy-the-function-app-project-to-azure). Para implementar manualmente um arquivo zip na sua aplicação function app, siga as instruções em [implementar a partir de um ficheiro zip ou uma url](https://github.com/projectkudu/kudu/wiki/Deploying-from-a-zip-file-or-url).
>
>Além disso, quando implementar a implantação por meio do zip, os utilizadores podem especificar para executar a aplicação a partir na [execução do pacote](run-functions-from-deployment-package.md) modo ao definir o `WEBSITE_RUN_FROM_PACKAGE` valor de definição de aplicação como `1`. Esta opção é sugerida e resulta em tempos de carregamento mais rápidos para as suas aplicações. Isso é feito por predefinição para as ferramentas de cliente acima.

>__Quando utilizá-lo:__ Esta é a tecnologia de implementação recomendado para as funções do Azure em execução no Windows e as funções do Azure em execução no Linux no plano de dedicado.

### <a name="docker-container"></a>Contentor do docker

Implemente uma imagem de contentor do Linux que contém a aplicação de funções.

>__Como usá-lo:__ Criar uma aplicação de funções do Linux no plano de dedicado e especifique a imagem de contentor para executar a partir de. Pode fazê-lo de duas formas:
>
>* Crie uma aplicação de funções do Linux num plano do serviço de aplicações no portal do Azure. Selecione **imagem do Docker** para **Publish**e configurar o contentor, fornecendo a localização onde a imagem é alojada.
>* Crie uma aplicação de funções do Linux num plano do serviço de aplicações através da CLI do Azure. Saiba como ao rever [criar uma função no Linux com uma imagem personalizada](functions-create-function-linux-custom-image.md#create-and-deploy-the-custom-image).
>
>Para implementar uma aplicação existente com um contentor personalizado, utilize o [ `func deploy` ](functions-run-local.md#publish) comando da [ferramentas de núcleo de funções do Azure](functions-run-local.md).

>__Quando utilizá-lo:__ Utilize esta opção se precisar de mais controlo sobre o ambiente do Linux em que a aplicação function app é executada. Esse mecanismo de implementação só está disponível para as funções em execução no Linux num plano do serviço de aplicações.

### <a name="web-deploy-msdeploy"></a>Web implementar (MSDeploy)

Pacotes e implementa seus aplicativos do Windows a qualquer servidor IIS, incluindo as suas aplicações de função do Azure em execução no Windows.

>__Como usá-lo:__ Utilize o [ferramentas do Visual Studio das funções do Azure](functions-create-your-first-function-visual-studio.md), e não escala a `Run from package file (recommended)` caixa de verificação.
>
>Em alternativa, chamar `MSDeploy.exe` diretamente depois de baixar [3.6 de implementar Web](https://www.iis.net/downloads/microsoft/web-deploy).

>__Quando utilizá-lo:__ Esta tecnologia de implantação é suportada e não tem problemas, mas agora é o mecanismo preferencial [Zip implementar com a execução do pacote habilitado](#zip-deploy). Para saber mais, visite o [guia de desenvolvimento do Visual Studio](functions-develop-vs.md#publish-to-azure).

### <a name="source-control"></a>Controlo de origem

Permite-lhe ligar a sua aplicação de função para um repositório de git, de modo a que todas as atualizações ao código nesse repositório aciona a implementação. Para obter mais informações, veja a [Kudu Wiki](https://github.com/projectkudu/kudu/wiki/VSTS-vs-Kudu-deployments).

>__Como usá-lo:__ Utilize o Centro de implementação no portal de funções do Azure para configurar a publicação no controle da fonte. Para obter mais informações, consulte [implementação contínua para funções do Azure](functions-continuous-deployment.md).

>__Quando utilizá-lo:__ Usando o controle de origem é a melhor prática para as equipes colaboram em seus aplicativos de função e esta é uma ótima opção que permite que os pipelines de implantação mais sofisticados.

### <a name="local-git"></a>Local git

Permite enviar o código a partir do seu computador local para as funções do Azure com o Git.

>__Como usá-lo:__ Siga as instruções em [implementação de Git Local para o serviço de aplicações do Azure](../app-service/deploy-local-git.md).

>__Quando utilizá-lo:__ Recomenda-se em geral, outros métodos de implantação. Durante a publicação de local git, deve [sincronizar manualmente o acionadores](#trigger-syncing).

### <a name="cloud-sync"></a>Sincronização da cloud

Permite que sincronize seu conteúdo da Dropbox e OneDrive para as funções do Azure.

>__Como usá-lo:__ Siga as instruções em [sincronizar conteúdos de uma pasta de cloud](../app-service/deploy-content-sync.md).

>__Quando utilizá-lo:__ Recomenda-se em geral, outros métodos de implantação. Ao publicar com sincronização de nuvem, deve [sincronizar manualmente o acionadores](#trigger-syncing).

### <a name="ftp"></a>FTP

Permite-lhe diretamente ficheiros de transferência para as funções do Azure.

>__Como usá-lo:__ Siga as instruções em [implementar conteúdo com FTP/s](../app-service/deploy-ftp.md).

>__Quando utilizá-lo:__ Recomenda-se em geral, outros métodos de implantação. Quando a publicação de FTP, deve [sincronizar manualmente o acionadores](#trigger-syncing).

### <a name="portal-editing"></a>A edição de portal

Usando o editor baseado no portal, pode editar diretamente os ficheiros na sua aplicação function app (essencialmente implementar em qualquer altura, clique em **guardar**).

>__Como usá-lo:__ Para conseguir editar as suas funções no portal do Azure, tem de ter [criado as suas funções no portal do](functions-create-first-azure-function.md). Utilizar qualquer outro método de implementação torna sua função só de leitura e impede a portal a edição contínuo, para preservar uma única fonte de verdade. Para voltar para um Estado no qual pode editar os ficheiros com o portal do Azure, pode ativar manualmente o modo de edição para `Read/Write` e remover as definições relacionadas com a implementação de aplicação (como `WEBSITE_RUN_FROM_PACKAGE`). 

>__Quando utilizá-lo:__ O portal é uma ótima maneira de começar com as funções do Azure, mas para qualquer trabalho de desenvolvimento mais intenso, utilizando o cliente de ferramentas é recomendada:
>
>* [Começar a utilizar o VS Code](functions-create-first-function-vs-code.md)
>* [Começar a utilizar as ferramentas de núcleo de funções do Azure](functions-run-local.md)
>* [Introdução à utilização do Visual Studio](functions-create-your-first-function-visual-studio.md)

A tabela seguinte mostra os sistemas operativos e os idiomas para o qual o portal de edição é suportada:

| | Consumo de Windows | Premium do Windows (pré-visualização) | Windows dedicado | Consumo de Linux (pré-visualização) | Linux dedicado |
|-|:-----------------: |:-------------------------:|:-----------------:|:---------------------------:|:---------------:|
| C# | | | | | |
| C#Script |✔|✔|✔| |✔<sup>*</sup>|
| F# | | | | | |
| Java | | | | | |
| JavaScript (Node.js) |✔|✔|✔| |✔<sup>*</sup>|
| Python (Pré-visualização) | | | | | |
| PowerShell (pré-visualização) |✔|✔|✔| | |
| TypeScript (Node.js) | | | | | |

<sup>*</sup> A edição portal está ativada para apenas os acionadores HTTP e temporizador para as funções no Linux com o plano de dedicado.

## <a name="deployment-slots"></a>Blocos de implementação

Quando implementar a aplicação de funções do Azure, pode implementar para um bloco de implementação separados em vez de diretamente para a produção. Para obter mais informações sobre as ranhuras de implementação, consulte [a documentação de blocos de serviço de aplicação do Azure](../app-service/deploy-staging-slots.md).

### <a name="deployment-slots-levels-of-support"></a>Níveis de ranhuras de implementação de suporte

Existem dois níveis de suporte:

* _Disponibilidade geral (GA)_ - totalmente suportado e aprovada para utilização em produção.
* _Pré-visualização_ - ainda não suportado, mas é esperado para alcançar o estado de disponibilidade geral no futuro.

| Plano de alojamento/SO | Nível de suporte |
| --------------- | ------ |
| Consumo de Windows | Pré-visualização |
| Premium do Windows (pré-visualização) | Pré-visualização |
| Windows dedicado | Disponível em geral |
| Consumo de Linux | Não suportado |
| Linux dedicado | Disponível em geral |

## <a name="next-steps"></a>Passos Seguintes

Saiba mais sobre como implementar as suas aplicações de funções nos seguintes artigos: 

+ [Implementação contínua para Funções do Azure](functions-continuous-deployment.md)
+ [Entrega contínua através do Azure DevOps](functions-how-to-azure-devops.md)
+ [Implementações de zip para as funções do Azure](deployment-zip-push.md)
+ [Execute as suas funções do Azure a partir de um ficheiro de pacote](run-functions-from-deployment-package.md)
+ [Automatizar a implementação de recursos para a sua aplicação de função nas funções do Azure](functions-infrastructure-as-code.md)
