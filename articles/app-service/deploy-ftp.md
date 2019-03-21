---
title: Implementar conteúdo com FTP/S - serviço de aplicações do Azure | Documentos da Microsoft
description: Saiba como implementar a aplicação no serviço de aplicações do Azure através de FTP ou FTPS.
services: app-service
documentationcenter: ''
author: cephalin
manager: erikre
editor: ''
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/30/2018
ms.author: cephalin;dariac
ms.custom: seodec18
ms.openlocfilehash: db8445ec2b3dd8bdefa661d7f186e720c6fada09
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57858882"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implementar a aplicação no serviço de aplicações do Azure com FTP/S

Este artigo mostra-lhe como utilizar o FTP ou FTPS para implementar a aplicação web, o back-end de aplicação móvel ou a aplicação API [App Service do Azure](https://go.microsoft.com/fwlink/?LinkId=529714).

O ponto de extremidade FTP/S para a sua aplicação já está ativo. Nenhuma configuração é necessária ativar a implementação de FTP/S.

## <a name="open-ftp-dashboard"></a>Abrir dashboard FTP

Na [portal do Azure](https://portal.azure.com), abra a sua aplicação [página de recursos](../azure-resource-manager/manage-resources-portal.md#manage-resources).

Para abrir o dashboard FTP, clique em **Deployment Center** > **FTP** > **Dashboard**.

![Abrir dashboard FTP](./media/app-service-deploy-ftp/open-dashboard.png)

## <a name="get-ftp-connection-information"></a>Obter informações de ligação de FTP

No dashboard do FTP, clique em **cópia** para copiar as credenciais de ponto final e a aplicação FTPS.

![Copiar informações de FTP](./media/app-service-deploy-ftp/ftp-dashboard.png)

É recomendado que utilize **aplicação credenciais** para implementar na sua aplicação, porque é exclusivo para cada aplicação. No entanto, se clicar **credenciais do utilizador**, pode definir as credenciais de nível de usuário que pode utilizar para início de sessão FTP/S para todas as aplicações de serviço de aplicações na sua subscrição.

> [!NOTE]
> Autenticar para um ponto de extremidade FTP/FTPS, com um nome de utilizador de requirers de credenciais de nível de usuário no seguinte formato: 
>
>`<app-name>\<user-name>`
>
> Uma vez que as credenciais de nível de usuário são associadas ao usuário e não um recurso específico, tem de ser o nome de utilizador neste formato para direcionar a ação de início de sessão para o ponto final de aplicação certa.
>

## <a name="deploy-files-to-azure"></a>Implementar os ficheiros para o Azure

1. Partir do seu cliente FTP (por exemplo, [Visual Studio](https://www.visualstudio.com/vs/community/), [Cyberduck](https://cyberduck.io/), ou [WinSCP](https://winscp.net/index.php)), utilize as informações de ligação que recolheu para ligar à sua aplicação.
2. Copiar os ficheiros e a sua respetiva estrutura de diretório para o [ **/site/wwwroot** diretório](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) no Azure (ou o **/site/wwwroot/aplicação_dados/tarefas/** diretório para o WebJobs).
3. Navegue para o URL da sua aplicação para verificar se que a aplicação está a funcionar corretamente. 

> [!NOTE] 
> Ao contrário [Implantações com base no Git](deploy-local-git.md), implementação de FTP não suporta as automatizações de fluxos de implementação seguintes: 
>
> - dependência restaura (por exemplo, o NuGet, o NPM, o PIP e o compositor automatizações)
> - compilação de binários de .NET
> - geração de Web. config (este é um [exemplo de node. js](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps))
> 
> Gerar esses arquivos necessários manualmente no seu computador local e, em seguida, implementá-las em conjunto com a sua aplicação.
>

## <a name="enforce-ftps"></a>Impor FTPS

Para maior segurança, deve permitir FTP sobre SSL apenas. Também pode desativar FTP e FTPS se não usar a implementação de FTP.

Na página de recursos da sua aplicação no [portal do Azure](https://portal.azure.com), selecione **as definições da aplicação** na navegação à esquerda.

Para desativar o FTP não encriptada, selecione **FTPS apenas**. Para desabilitar totalmente o FTP e FTPS, selecione **desativar**. Quando terminar, clique em **Guardar**. Se utilizar **FTPS só** têm de impor TLS 1.2 ou superior ao navegar para o **definições de SSL** painel da sua aplicação web. O TLS 1.0 e 1.1 não são suportadas com **FTPS apenas**.

![Desativar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

## <a name="automate-with-scripts"></a>Automatizar com scripts

Para a utilização de implementação de FTP [CLI do Azure](/cli/azure), consulte [criar uma aplicação web e implementar ficheiros com FTP (CLI do Azure)](./scripts/cli-deploy-ftp.md).

Para a utilização de implementação de FTP [do Azure PowerShell](/cli/azure), consulte [carregar ficheiros para uma aplicação web com FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Resolver problemas de implementação de FTP

- [Como posso resolver problemas de implementação de FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Não sou capaz de FTP e publicar o meu código. Como posso resolver o problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Como posso ligar a FTP no serviço de aplicações do Azure por meio do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como posso resolver problemas de implementação de FTP?

A primeira etapa para resolução de problemas de implementação de FTP é isolar um problema de implementação de um problema de aplicativo de tempo de execução.

Um problema de implementação normalmente resulta em nenhum arquivo ou arquivos errados implementados para a sua aplicação. Pode resolver ao investigar a implementação de FTP ou selecionar um caminho de implementação alternativos (por exemplo, o controlo de origem).

Um problema de aplicativo de tempo de execução normalmente resulta no conjunto certo de ficheiros implementado para a sua aplicação, mas o comportamento da aplicação incorreto. Pode solucionar, concentrando-se no comportamento do código em tempo de execução e investigar caminhos de falha específica.

Para determinar um problema de implementação ou tempo de execução, veja [implementação vs. problemas de runtime](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues).

### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>Não sou capaz de FTP e publicar o meu código. Como posso resolver o problema?
Verifique se introduziu o nome de anfitrião correto e [credenciais](#open-ftp-dashboard). Verifique também se as seguintes portas FTP no seu computador não estão bloqueadas por uma firewall:

- Porta de ligação de controlo de FTP: 21
- Porta de ligação de dados FTP: 989, 10001-10300
 
### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como posso ligar a FTP no serviço de aplicações do Azure por meio do modo passivo?
Serviço de aplicações do Azure suporta a ligação por meio do modo ativo e passivo. Modo passivo é preferido porque sua implementação de máquinas virtuais é, normalmente, protegidos por uma firewall (no sistema operativo ou como parte de uma casa ou de rede de negócios). Veja uma [exemplo de documentação do WinSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="next-steps"></a>Passos Seguintes

Para cenários de implementação mais avançados, tente [implementar no Azure com o Git](deploy-local-git.md). Implementação baseada no Git para o Azure permite o controle de versão, o restauro de pacote, MSBuild e muito mais.

## <a name="more-resources"></a>Mais recursos

* [Credenciais de implementação do serviço de aplicações do Azure](deploy-configure-credentials.md)
