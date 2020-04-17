---
title: Implementar conteúdo utilizando FTP/S
description: Saiba como implementar a sua aplicação para o Azure App Service utilizando FTP ou FTPS. Melhorar a segurança do site desativando ftp não encriptado.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 09/18/2019
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: fcc7c5b8fa182cace6e3dae0b1cae4cd41c5dcb9
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/16/2020
ms.locfileid: "81532600"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implemente a sua aplicação para o Serviço de Aplicações Azure utilizando FTP/S

Este artigo mostra-lhe como usar FTP ou FTPS para implementar a sua aplicação web, backend de aplicações móveis ou app API para [o Azure App Service](https://go.microsoft.com/fwlink/?LinkId=529714).

O ponto final ftp/S para a sua aplicação já está ativo. Nenhuma configuração é necessária para permitir a implantação ftp/S.

## <a name="open-ftp-dashboard"></a>Painel de instrumentos FTP aberto

1. No [portal Azure,](https://portal.azure.com)procure e selecione Serviços de **Aplicações.**

    ![Pesquisa de serviços de Aplicações.](media/app-service-continuous-deployment/search-for-app-services.png)

2. Selecione a aplicação web que pretende implementar.

    ![Selecione a sua aplicação.](media/app-service-continuous-deployment/select-your-app.png)

3. Selecione **central** > de implantação**FTP** > **Dashboard**.

    ![Painel de instrumentos FTP aberto](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Obtenha informações de ligação FTP

No painel ftp, selecione **Copy** para copiar as credenciais de ponto final ftps e app.

![Copiar informações ftp](./media/app-service-deploy-ftp/ftp-dashboard.png)

Recomenda-se que utilize credenciais de **aplicação** para implementar na sua aplicação porque é única em cada aplicação. No entanto, se clicar em Credenciais de **Utilizador,** pode definir credenciais de nível de utilizador que pode utilizar para login FTP/S em todas as aplicações do Serviço de Aplicações na sua subscrição.

> [!NOTE]
> Autenticar para um ponto final FTP/FTPS utilizando credenciais de nível de utilizador requer um nome de utilizador no seguinte formato: 
>
>`<app-name>\<user-name>`
>
> Uma vez que as credenciais de nível de utilizador estão ligadas ao utilizador e não a um recurso específico, o nome de utilizador deve estar neste formato para direcionar a ação de início de sessão para o ponto final da aplicação certa.
>

## <a name="deploy-files-to-azure"></a>Implementar ficheiros para o Azure

1. Do seu cliente FTP (por exemplo, [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberduck,](https://cyberduck.io/)ou [WinSCP](https://winscp.net/index.php)), utilize as informações de ligação que recolheu para se ligar à sua aplicação.
2. Copie os seus ficheiros e respetiva estrutura de diretório para o [diretório **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) em Azure (ou o **/site/wwwroot/App_Data/Jobs/** diretório para WebJobs).
3. Navegue no URL da sua aplicação para verificar se a aplicação está a funcionar corretamente. 

> [!NOTE] 
> Ao contrário [das implementações baseadas em Git,](deploy-local-git.md)a implantação ftp não suporta as seguintes automatizações de implantação: 
>
> - dependência restaura (tais como nuGet, NPM, PIP e automações de compositor)
> - compilação de binários .NET
> - geração de web.config (aqui é um [exemplo de Node.js)](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Gere estes ficheiros necessários manualmente na sua máquina local e, em seguida, implemente-os juntamente com a sua aplicação.
>

## <a name="enforce-ftps"></a>Impor FTPS

Para uma maior segurança, deve permitir ftp apenas sobre TLS/SSL. Também pode desativar tanto o FTP como o FTPS se não utilizar a implantação ftp.

Na página de recursos da sua aplicação no [portal Azure,](https://portal.azure.com)selecione**configurações gerais** de **configuração** > a partir da navegação à esquerda.

Para desativar o FTP não encriptado, selecione **FTPS apenas** no **estado FTP**. Para desativar totalmente o FTP e o FTPS, selecione **Desativado**. Quando terminar, clique em **Guardar**. Se utilizar **apenas ftps,** deve impor TLS 1.2 ou superior, navegando para a lâmina de **definições TLS/SSL** da sua aplicação web. Os TLS 1.0 e 1.1 não são suportados apenas com **FTPS**.

![Desativar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Para a implementação de FTP utilizando [o Azure CLI,](/cli/azure)consulte [Criar uma aplicação web e implementar ficheiros com FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).

Para a implementação ftp utilizando [o Azure PowerShell,](/cli/azure)consulte [ficheiros upload para uma aplicação web utilizando FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Implantação de FTP de resolução de problemas

- [Como posso resolver a implantação de FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Não posso publicar o meu código. Como posso resolver o problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Como posso ligar-me ao FTP no Azure App Service através do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como posso resolver a implantação de FTP?

O primeiro passo para a resolução de problemas da implantação de FTP é isolar um problema de implantação de um problema de aplicação em tempo de execução.

Um problema de implementação normalmente resulta em ficheiros ou ficheiros errados implantados na sua aplicação. Pode resolver problemas investigando a sua implantação ftp ou selecionando um caminho de implantação alternativo (como o controlo de fonte).

Um problema de aplicação em tempo de execução normalmente resulta no conjunto certo de ficheiros implantados na sua aplicação, mas comportamento incorreto da aplicação. Você pode resolver problemas focando-se no comportamento do código em tempo de execução e investigando caminhos específicos de falha.

Para determinar um problema de implantação ou tempo de execução, consulte problemas de [implantação vs. tempo](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)de execução .

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I'm not able to FTP and publish my code. Como posso resolver o problema?
Verifique se inseriu o nome de anfitrião e [as credenciais corretas.](#open-ftp-dashboard) Verifique também se as seguintes portas FTP na sua máquina não estão bloqueadas por uma firewall:

- Porta de ligação de controlo de FTP: 21
- Porta de ligação de dados FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como posso ligar-me ao FTP no Azure App Service através do modo passivo?
O Azure App Service suporta a ligação através do modo Ativo e Passivo. O modo passivo é preferido porque as suas máquinas de implantação estão geralmente por detrás de uma firewall (no sistema operativo ou como parte de uma rede doméstica ou empresarial). Veja um [exemplo da documentação winscp](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Passos seguintes

Para cenários de implantação mais avançados, tente [implantar-se para Azure com Git](deploy-local-git.md). A implantação baseada em Git para o Azure permite o controlo da versão, restauro de pacotes, MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Credenciais de implantação do serviço de aplicações Azure](deploy-configure-credentials.md)
