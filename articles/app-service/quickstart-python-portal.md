---
title: 'Quickstart: Criar uma aplicação Python no portal Azure'
description: Iniciou-se com o Azure App Service, implementando a sua primeira aplicação Python num contentor Linux no Serviço de Aplicações, utilizando o portal Azure.
ms.topic: quickstart
ms.date: 04/01/2021
ms.custom: devx-track-python
robots: noindex
ms.openlocfilehash: 42f29152521da7bf90a550248e8429e51b728b24
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/07/2021
ms.locfileid: "107012361"
---
# <a name="quickstart-create-a-python-app-using-azure-app-service-on-linux-azure-portal"></a>Quickstart: Criar uma aplicação Python utilizando o Azure App Service em Linux (portal Azure)

Neste quickstart, você implementa uma aplicação web Python para [o App Service no Linux,](overview.md#app-service-on-linux)o serviço de hospedagem web altamente escalável e auto-remendado da Azure. Você usa o portal Azure para implantar uma amostra com as estruturas do Flask ou Django. A aplicação web que configura utiliza um nível básico de Serviço de Aplicações que incorre num pequeno custo na sua subscrição Azure.

## <a name="configure-accounts"></a>Configure contas

- Se ainda não tiver uma conta Azure com uma subscrição ativa, [crie uma conta gratuitamente](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio).

- Se não tiver uma conta GitHub, visite [github.com](https://github.com) para criar uma. 

## <a name="fork-the-sample-github-repository"></a>Garfo da amostra gitHub repositório

1. Abra [github.com](https://github.com) e inscreva-se.

1. Navegue para um dos seguintes repositórios de amostras:
    - [Frasco Olá Mundo](https://github.com/Azure-Samples/python-docs-hello-world)
    - [Django Hello World](https://github.com/Azure-Samples/python-docs-hello-django)

1. No canto superior direito da página GitHub, selecione **Fork** para fazer uma cópia do repositório na sua própria conta GitHub:

    ![Comando do garfo de Github](media/quickstart-python-portal/github-fork-command.png)

    O Azure requer que tenhas acesso à organização GitHub que contém o repositório. Ao falsificar a amostra para a sua própria conta GitHub, tem automaticamente o acesso necessário e também pode escoar alterações ao código.

## <a name="provision-the-app-service-web-app"></a>Fornecimento da aplicação web do Serviço de Aplicações

Uma aplicação web do Serviço de Aplicações é o servidor web ao qual implementa o seu código.

1. Abra o portal Azure [https://portal.azure.com](https://portal.azure.com) e inscreva-se se necessário.

1. Na barra de pesquisa no topo do portal Azure, insira "App Service", em seguida, selecione **Serviços de Aplicações**.

    ![Barra de pesquisa de portal e seleção do Serviço de Aplicações](media/quickstart-python-portal/portal-search-bar.png)

1. Na página **serviços de aplicações,** selecione "**+Adicionar**:

    ![Adicionar comando de Serviço de Aplicações](media/quickstart-python-portal/add-app-service.png)

1. Na página **'Criar Aplicação Web',** faça as seguintes ações:
    
    | Campo | Ação |
    | --- | --- |
    | Subscrição | Selecione a subscrição do Azure que pretende utilizar. |
    | Grupo de Recursos | Selecione **Criar Novo** abaixo do drop-down. No popup, insira "AppService-PythonQuickstart" e selecione "**OK**. |
    | Name | Insira um nome único em todo o Azure, normalmente usando uma combinação de nomes pessoais ou da sua empresa, como *contoso-testapp-123*. |
    | Publicar | Selecione **Código**. |
    | Pilha de runtime | Selecione **Python 3.8**. |
    | Sistema Operativo | Selecione **Linux** (Python é suportado apenas em Linux). |
    | Region | Selecione uma região perto de si. |
    | Plano do Linux | Selecione um Plano de Serviço de Aplicações de saída ou use **Criar novo** para criar um novo. Recomendamos a utilização do plano **B1 básico.** |

    ![Criar formulário de aplicativo web no portal Azure](media/quickstart-python-portal/create-web-app.png)

1. Na parte inferior da página, selecione **Rever + Criar,** rever os detalhes e, em seguida, selecione **Criar**.

1. Quando o fornecimento estiver completo, selecione **Ir para o recurso** para navegar para a nova página do Serviço de Aplicações. A sua aplicação web neste ponto contém apenas uma página padrão, pelo que o próximo passo implementa o código de amostra.

Tendo problemas? [Deixe-nos saber.](https://aka.ms/FlaskPortalQuickstartHelp)

## <a name="deploy-the-sample-code"></a>Implementar o código de amostra

1. Na página da aplicação web no portal Azure, selecione **Deployment Center**:
    
    ![Comando do Centro de Implementação no menu do Serviço de Aplicações](media/quickstart-python-portal/deployment-center-command.png)


1. Na página **'Centro de Implementação',** selecione o separador **Definições** se ainda não estiver aberto:

    ![Separador de definições do Centro de Implementação](media/quickstart-python-portal/deployment-center-settings-tab.png)

1. Em **Fonte**, selecione **GitHub,** em seguida, no formulário **GitHub** que aparece, faça as seguintes ações:

    | Campo | Ação |
    | --- | --- |
    | Sessão iniciada como | Se ainda não tiver assinado no GitHub, inscreva-se agora ou selecione **Alterar conta,* se necessário. |
    | Organização | Selecione a sua organização GitHub, se necessário. |
    | Repositório | Selecione o nome do repositório de amostra que já forjado anteriormente, seja **python-docs-hello-world** (Flask) ou **python-docs-hello-django** (Django). |
    | Ramo | Selecione **principal**. |

    ![Configuração de origem do Centro de Implantação GitHub](media/quickstart-python-portal/deployment-center-configure-github-source.png)

1. No topo da página, **selecione Guardar** para aplicar as definições.:

    ![Guarde a configuração de origem GitHub no Centro de Implantação](media/quickstart-python-portal/deployment-center-configure-save.png)

1. Selecione o **separador 'Registar'** para visualizar o estado da implantação. Demora alguns minutos a construir e a implantar a amostra e aparecem registos adicionais durante o processo. Após a conclusão, os registos devem refletir um Estado de **Sucesso (Ativo)**:

    ![Separador de registos do Centro de Implementação](media/quickstart-python-portal/deployment-center-logs.png)

Tendo problemas? [Deixe-nos saber.](https://aka.ms/FlaskPortalQuickstartHelp)

## <a name="browse-to-the-app"></a>Navegar para a aplicação

1. Uma vez concluída a implementação, selecione **Overview** no menu da esquerda para voltar à página principal para a aplicação web.

1. Selecione o **URL** que contém o endereço da aplicação web:

    ![URL de aplicativo web na página geral](media/quickstart-python-portal/web-app-url.png)

1. Verifique se a saída da app é "Olá, Mundo!":

    ![App em execução após implementação inicial](media/quickstart-python-portal/web-app-first-deploy-results.png)

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/FlaskPortalQuickstartHelp).

## <a name="make-a-change-and-redeploy"></a>Faça uma mudança e reimplante

Como ligou o Serviço de Aplicações ao seu repositório, as alterações que compromete com o seu repositório de origem são automaticamente implementadas na aplicação web.

1. Pode fazer alterações diretamente no seu repositório forcado no GitHub, ou pode clonar o repositório localmente, fazer e cometer alterações e, em seguida, empurrar essas alterações para o GitHub. Qualquer um dos métodos resulta numa alteração ao repositório que está ligado ao Serviço de Aplicações.

1. **No seu repositório forcado,** altere a mensagem da aplicação de "Olá, Mundo!" a "Olá, Azure!" da seguinte forma:
    - Balão (amostra python-docs-hello-world): Altere a cadeia de texto na linha 6 do ficheiro *application.py.*
    - Django (amostra python-docs-hello-django): Altere a cadeia de texto na linha 5 do ficheiro *views.py* dentro da pasta *hello.*

1. Comprometa a mudança para o repositório.

    Se estiver a usar um clone local, também empurre essas alterações para o GitHub.

1. No portal Azure para a aplicação web, volte ao **Centro de Implementação,** selecione o **separador Registos** e note a nova atividade de implementação que deve estar em curso.

1. Quando a implementação estiver concluída, volte à página **geral** da aplicação web, abra novamente o URL da aplicação web e observe as alterações na aplicação:

    ![App em execução após recolocação](media/quickstart-python-portal/web-app-second-deploy-results.png)

Tendo problemas? Consulte primeiro o [guia de resolução de problemas,](configure-language-python.md#troubleshooting)caso contrário, [avise-nos](https://aka.ms/FlaskCLIQuickstartHelp).

## <a name="clean-up-resources"></a>Limpar os recursos

Nos passos anteriores, criou recursos Azure num grupo de recursos chamado "AppService-PythonQuickstart", que é mostrado na página *Geral* da aplicação web* Se mantiver a aplicação web em funcionamento, irá incorrer em alguns custos em curso (ver [preços do Serviço de Aplicações).](https://azure.microsoft.com/pricing/details/app-service/linux/)

Se não espera precisar destes recursos no futuro, selecione o nome do grupo de recursos na página **de visão geral** da aplicação web para navegar para a visão geral dos grupos de recursos. Estes selecionam **Eliminar grupo de recursos** e seguir as instruções.

![Eliminar o grupo de recursos](media/quickstart-python-portal/delete-resource-group.png)


Tendo problemas? [Deixe-nos saber.](https://aka.ms/FlaskCLIQuickstartHelp)

## <a name="next-steps"></a>Passos seguintes

> [!div class="nextstepaction"]
> [Tutorial: Python (Django) web app com PostgreSQL](/azure/developer/python/tutorial-python-postgresql-app-portal)

> [!div class="nextstepaction"]
> [Configure app Python](configure-language-python.md)

> [!div class="nextstepaction"]
> [Adicione o sign-in do utilizador a uma aplicação web Python](../active-directory/develop/quickstart-v2-python-webapp.md)

> [!div class="nextstepaction"]
> [Tutorial: Executar app Python em recipiente personalizado](tutorial-custom-container.md)
