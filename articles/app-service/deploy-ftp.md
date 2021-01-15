---
title: Implementar conteúdo usando FTP/S
description: Saiba como implementar a sua aplicação no Azure App Service utilizando FTP ou FTPS. Melhorar a segurança do site desativando ftp não encriptado.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: cfec5ec5f14afc8c4eba5c21c5904687c9b187cc
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209258"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implemente a sua aplicação para o Azure App Service utilizando FTP/S

Este artigo mostra-lhe como usar FTP ou FTPS para implementar a sua aplicação web, suporte de aplicativo móvel ou app API para [o Azure App Service](./overview.md).

O ponto final FTP/S da sua aplicação já está ativo. Não é necessária nenhuma configuração para ativar a implantação FTP/S.

## <a name="open-ftp-dashboard"></a>Painel FTP aberto

1. No [portal Azure,](https://portal.azure.com)procure e selecione **Serviços de Aplicações.**

    ![Procure por serviços de aplicações.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Selecione a aplicação web que pretende implementar.

    ![Selecione a sua aplicação.](media/app-service-continuous-deployment/select-your-app.png)

3. Selecione **O Painel**  >  **FTP**  >  **do** Centro de Implantação .

    ![Painel FTP aberto](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Obtenha informações de ligação FTP

No painel FTP, selecione **Copy** para copiar o ponto final ftps e as credenciais de aplicação.

![Copiar informações FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

Recomenda-se que utilize **as Credenciais de Aplicação** para implementar na sua aplicação porque é única em cada aplicação. No entanto, se clicar **em Credenciais de Utilizador,** pode definir credenciais de nível de utilizador que pode utilizar para o login FTP/S em todas as aplicações do Serviço de Aplicações na sua subscrição.

> [!NOTE]
> A autenticação num ponto final FTP/FTPS utilizando credenciais de nível de utilizador requer um nome de utilizador no seguinte formato: 
>
>`<app-name>\<user-name>`
>
> Uma vez que as credenciais ao nível do utilizador estão ligadas ao utilizador e não a um recurso específico, o nome de utilizador deve estar neste formato para direcionar a ação de entrada para o ponto final da aplicação certa.
>

## <a name="deploy-files-to-azure"></a>Implementar ficheiros para o Azure

1. Do seu cliente FTP (por exemplo, [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberduck](https://cyberduck.io/)ou [WinSCP](https://winscp.net/index.php)), utilize as informações de ligação que recolheu para ligar à sua aplicação.
2. Copie os seus ficheiros e a respetiva estrutura do diretório para o [ **diretório /site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) em Azure (ou o **/site/wwwroot/App_Data/Jobs/diretório** para WebJobs).
3. Navegue no URL da sua aplicação para verificar se a aplicação está a funcionar corretamente. 

> [!NOTE] 
> Ao contrário [das implementações baseadas em Git,](deploy-local-git.md)a implementação ftp não suporta as seguintes automatizações de implantação: 
>
> - restaurações de dependência (tais como automações NuGet, NPM, PIP e Compositor)
> - compilação de binários .NET
> - geração de web.config (aqui está um [exemploNode.js)](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Gere estes ficheiros necessários manualmente na sua máquina local e, em seguida, desloque-os em conjunto com a sua aplicação.
>

## <a name="enforce-ftps"></a>Impor FTPS

Para uma maior segurança, deve permitir ftp apenas em TLS/SSL. Também pode desativar tanto o FTP como o FTPS se não utilizar a implementação FTP.

Na página de recursos da sua aplicação no [portal Azure,](https://portal.azure.com)selecione   >  **Configuração Definições Gerais** a partir da navegação à esquerda.

Para desativar o FTP não encriptado, selecione **FTPS Apenas** no **estado FTP**. Para desativar totalmente o FTP e o FTPS, **selecione Desativado**. Quando terminar, clique em **Guardar**. Se utilizar **apenas o FTPS,** tem de impor o TLS 1.2 ou superior navegando para a lâmina de **definições TLS/SSL** da sua aplicação web. Os TLS 1.0 e 1.1 não são suportados apenas com **FTPS**.

![Desativar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Para a implementação de FTP utilizando [o Azure CLI,](/cli/azure)consulte [criar uma aplicação web e implementar ficheiros com FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

Para a implementação de FTP utilizando [o Azure PowerShell,](/cli/azure)consulte [os ficheiros upload para uma aplicação web utilizando FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Resolução de problemas implantação FTP

- [Implemente a sua aplicação para o Azure App Service utilizando FTP/S](#deploy-your-app-to-azure-app-service-using-ftps)
  - [Painel FTP aberto](#open-ftp-dashboard)
  - [Obtenha informações de ligação FTP](#get-ftp-connection-information)
  - [Implementar ficheiros para o Azure](#deploy-files-to-azure)
  - [Impor FTPS](#enforce-ftps)
  - [Automatizar com scripts](#automate-with-scripts)
  - [Resolução de problemas implantação FTP](#troubleshoot-ftp-deployment)
    - [Como posso resolver a implantação do FTP?](#how-can-i-troubleshoot-ftp-deployment)
    - [Não sou capaz de ftp e publicar o meu código. Como posso resolver o problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
    - [Como posso ligar-me à FTP no Azure App Service através do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)
  - [Próximos passos](#next-steps)
  - [Mais recursos](#more-resources)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como posso resolver a implantação do FTP?

O primeiro passo para a resolução de problemas da implantação da FTP é isolar um problema de implantação de um problema de aplicação de tempo de funcionamento.

Um problema de implementação normalmente resulta em nenhum ficheiro ou ficheiros errados implantados na sua aplicação. Pode resolver problemas investigando a sua implantação FTP ou selecionando uma trajetória de implantação alternativa (como controlo de origem).

Um problema de aplicação de tempo de execução normalmente resulta no conjunto certo de ficheiros implantados na sua aplicação, mas comportamento incorreto da aplicação. Pode resolver problemas focando-se no comportamento do código em tempo de execução e investigando caminhos específicos de falha.

Para determinar um problema de implementação ou tempo de execução, consulte [questões de execução vs. .](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I'm not able to FTP and publish my code. Como posso resolver o problema?
Verifique se inseriu o nome de anfitrião e [as credenciais corretas.](#open-ftp-dashboard) Verifique também se as seguintes portas FTP da sua máquina não estão bloqueadas por uma firewall:

- Porta de ligação de controlo FTP: 21.990
- Porta de ligação de dados FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como posso ligar-me à FTP no Azure App Service através do modo passivo?
O Azure App Service suporta a ligação através do modo Ative e Passivo. O modo passivo é preferido porque as suas máquinas de implantação estão geralmente por trás de uma firewall (no sistema operativo ou como parte de uma rede doméstica ou empresarial). Veja um [exemplo da documentação winSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Passos seguintes

Para cenários de implementação mais avançados, tente [implementar para Azure com Git](deploy-local-git.md). A implementação baseada em Git para o Azure permite o controlo da versão, a restauração do pacote, o MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Credenciais de implementação do serviço de aplicações Azure](deploy-configure-credentials.md)