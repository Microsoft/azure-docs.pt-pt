---
title: Implantar conteúdo usando o serviço FTP/S-Azure App | Microsoft Docs
description: Saiba como implantar seu aplicativo para Azure App serviço usando FTP ou FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 09/18/2019
ms.author: cephalin
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: 6e8a6820b3cf3031f11ab04d9baf4a7888491c81
ms.sourcegitcommit: cd70273f0845cd39b435bd5978ca0df4ac4d7b2c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/18/2019
ms.locfileid: "71098087"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implantar seu aplicativo no serviço de Azure App usando FTP/S

Este artigo mostra como usar FTP ou FTPS para implantar seu aplicativo Web, back-end do aplicativo móvel ou aplicativo de API para [Azure app serviço](https://go.microsoft.com/fwlink/?LinkId=529714).

O ponto de extremidade de FTP/S para seu aplicativo já está ativo. Nenhuma configuração é necessária para habilitar a implantação de FTP/S.

## <a name="open-ftp-dashboard"></a>Abrir painel de FTP

Na [portal do Azure](https://portal.azure.com), abra a [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources)do aplicativo.

Para abrir o painel de FTP, clique em **central** > de implantação**painel**de**FTP** > .

![Abrir painel de FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Obter informações de conexão FTP

No painel de FTP, clique em **copiar** para copiar o ponto de extremidade de FTPS e as credenciais do aplicativo.

![Copiar informações de FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

É recomendável que você use **as credenciais do aplicativo** para implantar em seu aplicativo porque ele é exclusivo para cada aplicativo. No entanto, se você clicar em **credenciais de usuário**, poderá definir credenciais de nível de usuário que você pode usar para logon de FTP/S para todos os aplicativos do serviço de aplicativo em sua assinatura.

> [!NOTE]
> A autenticação em um ponto de extremidade de FTP/FTPS usando credenciais de nível de usuário exige um nome de usuário no seguinte formato: 
>
>`<app-name>\<user-name>`
>
> Como as credenciais de nível de usuário são vinculadas ao usuário e não a um recurso específico, o nome de usuários deve estar nesse formato para direcionar a ação de entrada para o ponto de extremidade do aplicativo certo.
>

## <a name="deploy-files-to-azure"></a>Implantar arquivos no Azure

1. No seu cliente FTP (por exemplo, [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/)ou [WinSCP](https://winscp.net/index.php)), use as informações de conexão coletadas para se conectar ao seu aplicativo.
2. Copie os arquivos e sua respectiva estrutura de diretório para o [diretório **/site/wwwroot** ](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o diretório **/site/wwwroot/App_Data/Jobs/** para webjobs).
3. Navegue até a URL do seu aplicativo para verificar se o aplicativo está sendo executado corretamente. 

> [!NOTE] 
> Diferentemente das [implantações baseadas em git](deploy-local-git.md), a implantação de FTP não dá suporte às seguintes automaçãos de implantação: 
>
> - restaurações de dependências (como as automaçãos NuGet, NPM, PIP e Composer)
> - compilação de binários do .NET
> - geração de Web. config (aqui está um [exemplo de Node. js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Gere esses arquivos necessários manualmente no computador local e, em seguida, implante-os junto com seu aplicativo.
>

## <a name="enforce-ftps"></a>Impor FTPS

Para aumentar a segurança, você deve permitir somente o FTP sobre SSL. Você também pode desabilitar FTP e FTPS se não usar a implantação de FTP.

Na página de recursos do aplicativo no [portal do Azure](https://portal.azure.com), selecione**configurações gerais** de **configuração** > no painel de navegação esquerdo.

Para desabilitar o FTP não criptografado, selecione **FTPS somente** no **estado do FTP**. Para desabilitar totalmente o FTP e a FTPS, selecione **desabilitado**. Quando terminar, clique em **Guardar**. Se estiver usando **apenas FTPS**, você deve impor o TLS 1,2 ou superior navegando até a folha **configurações de TLS/SSL** do seu aplicativo Web. O TLS 1,0 e o 1,1 não têm suporte **apenas com FTPS**.

![Desabilitar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Para a implantação de FTP usando [CLI do Azure](/cli/azure), consulte [criar um aplicativo Web e implantar arquivos com FTP (CLI do Azure)](./scripts/cli-deploy-ftp.md).

Para a implantação de FTP usando [Azure PowerShell](/cli/azure), consulte [carregar arquivos em um aplicativo Web usando FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Solucionar problemas de implantação de FTP

- [Como posso solucionar problemas de implantação de FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [I'm not able to FTP and publish my code. Como posso resolver o problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Como posso me conectar ao FTP no serviço Azure App por meio do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como posso solucionar problemas de implantação de FTP?

A primeira etapa para solucionar problemas de implantação de FTP é isolar um problema de implantação de um problema de aplicativo em tempo de execução.

Um problema de implantação normalmente resulta em nenhum arquivo ou arquivos errados implantados em seu aplicativo. Você pode solucionar problemas investigando sua implantação de FTP ou selecionando um caminho de implantação alternativo (como o controle do código-fonte).

Um problema de aplicativo em tempo de execução normalmente resulta no conjunto correto de arquivos implantados em seu aplicativo, mas comportamento incorreto do aplicativo. Você pode solucionar problemas concentrando-se no comportamento do código em tempo de execução e investigando caminhos de falha específicos.

Para determinar um problema de implantação ou tempo de execução, consulte [implantação versus problemas de tempo de execução](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Não consigo fazer o FTP e publicar meu código. Como posso resolver o problema?
Verifique se você inseriu o nome de host e [as credenciais](#open-ftp-dashboard)corretos. Verifique também se as seguintes portas FTP em seu computador não estão bloqueadas por um firewall:

- Porta de conexão de controle FTP: 21
- Porta de conexão de dados FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como posso me conectar ao FTP no serviço Azure App por meio do modo passivo?
Azure App serviço dá suporte à conexão por meio do modo ativo e passivo. O modo passivo é preferencial porque as máquinas de implantação geralmente estão atrás de um firewall (no sistema operacional ou como parte de uma rede doméstica ou de negócios). Consulte um [exemplo da documentação do winscp](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Passos Seguintes

Para cenários de implantação mais avançados, tente [implantar no Azure com o Git](deploy-local-git.md). A implantação baseada em git para o Azure habilita o controle de versão, a restauração de pacote, o MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Azure App credenciais de implantação do serviço](deploy-configure-credentials.md)
