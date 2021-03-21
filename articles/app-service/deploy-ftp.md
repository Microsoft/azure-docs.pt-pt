---
title: Implementar conteúdo usando FTP/S
description: Saiba como implementar a sua aplicação no Azure App Service utilizando FTP ou FTPS. Melhorar a segurança do site desativando ftp não encriptado.
ms.assetid: ae78b410-1bc0-4d72-8fc4-ac69801247ae
ms.topic: article
ms.date: 02/26/2021
ms.reviewer: dariac
ms.custom: seodec18
ms.openlocfilehash: c7427a1f8f528fdf405b22c4e91941ea7a915ffa
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102045807"
---
# <a name="deploy-your-app-to-azure-app-service-using-ftps"></a>Implemente a sua aplicação para o Azure App Service utilizando FTP/S

Este artigo mostra-lhe como usar FTP ou FTPS para implementar a sua aplicação web, suporte de aplicativo móvel ou app API para [o Azure App Service](./overview.md).

O ponto final FTP/S da sua aplicação já está ativo. Não é necessária nenhuma configuração para ativar a implantação FTP/S.

> [!NOTE]
> A página **do Centro de Desenvolvimento (Clássico)** no portal Azure, que é a antiga experiência de implantação, será depretada em março de 2021. Esta alteração não afetará quaisquer definições de implementação existentes na sua aplicação, podendo continuar a gerir a implementação de aplicações na página **do Centro de Implementação.**

## <a name="get-deployment-credentials"></a>Obtenha credenciais de implantação

1. Siga as instruções na [Configure Para o Serviço de Aplicações Azure](deploy-configure-credentials.md) para copiar as credenciais de aplicação ou definir as credenciais de âmbito do utilizador. Pode ligar-se ao ponto final FTP/S da sua aplicação utilizando qualquer uma das credenciais.

1. Criar o nome de utilizador FTP no seguinte formato, dependendo da sua escolha de âmbito credencial:

    | Âmbito de aplicação | Âmbito de utilização |
    | - | - |
    |`<app-name>\$<app-name>`|`<app-name>\<deployment-user>`|

    ---

    No Serviço de Aplicações, o ponto final FTP/S é partilhado entre aplicações. Como as credenciais de âmbito do utilizador não estão ligadas a um recurso específico, é necessário preparar o nome de utilizador do âmbito do utilizador com o nome da aplicação, tal como mostrado acima.

## <a name="get-ftps-endpoint"></a>Obtenha o ponto final FTP/S
    
# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

Na mesma página de gestão da sua aplicação onde copiou as credenciais de implementação **(Deployment Center**  >  **FTP Credentials**), copie o ponto final **FTPS**.

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Executar o comando [de perfis de lista de publicações de listas de webapp az.](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) O exemplo a seguir utiliza um [caminho JMES](https://jmespath.org/) para extrair os pontos finais FTP/S da saída.

```azurecli-interactive
az webapp deployment list-publishing-profiles --name <app-name> --resource-group <group-name> --query "[?ends_with(profileName, 'FTP')].{profileName: profileName, publishUrl: publishUrl}"
```

Cada aplicação tem dois pontos finais FTP/S, um é leitura-escrita, enquanto a outra é apenas de leitura ( `profileName` contém `ReadOnly` ) e destina-se a cenários de recuperação de dados. Para implementar ficheiros com FTP, copie o URL do ponto final de leitura-escrita.

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Executar o comando [Get-AzWebAppPublishingProfile.](/powershell/module/az.websites/get-azwebapppublishingprofile) O exemplo a seguir extrai o ponto final FTP/S da saída XML.

```azurepowershell-interactive
$xml = [xml](Get-AzWebAppPublishingProfile -Name <app-name> -ResourceGroupName <group-name> -OutputFile null)
$xml.SelectNodes("//publishProfile[@publishMethod=`"FTP`"]/@publishUrl").value
```

-----

## <a name="deploy-files-to-azure"></a>Implementar ficheiros para o Azure

1. Do seu cliente FTP (por exemplo, [Visual Studio,](https://www.visualstudio.com/vs/community/) [Cyberduck](https://cyberduck.io/)ou [WinSCP](https://winscp.net/index.php)), utilize as informações de ligação que recolheu para ligar à sua aplicação.
2. Copie os seus ficheiros e a respetiva estrutura do diretório para o [ **diretório /site/wwwroot**](https://github.com/projectkudu/kudu/wiki/File-structure-on-azure) em Azure (ou o **/site/wwwroot/App_Data/Jobs/diretório** para WebJobs).
3. Navegue no URL da sua aplicação para verificar se a aplicação está a funcionar corretamente. 

> [!NOTE] 
> Ao contrário [das implementações baseadas em Git](deploy-local-git.md) e da [implementação zip,](deploy-zip.md)a implementação ftp não suporta automatização de construção, tais como: 
>
> - restaurações de dependência (tais como automações NuGet, NPM, PIP e Compositor)
> - compilação de binários .NET
> - geração de web.config (aqui está um [exemploNode.js)](https://github.com/projectkudu/kudu/wiki/Using-a-custom-web.config-for-Node-apps)
> 
> Gere estes ficheiros necessários manualmente na sua máquina local e, em seguida, desloque-os em conjunto com a sua aplicação.
>

## <a name="enforce-ftps"></a>Impor FTPS

Para uma maior segurança, deve permitir ftp apenas em TLS/SSL. Também pode desativar tanto o FTP como o FTPS se não utilizar a implementação FTP.

# <a name="azure-portal"></a>[Portal do Azure](#tab/portal)

1. Na página de recursos da sua aplicação no [portal Azure,](https://portal.azure.com)selecione   >  **Configuração Definições Gerais** a partir da navegação à esquerda.

2. Para desativar o FTP não encriptado, selecione **FTPS Apenas** no **estado FTP**. Para desativar totalmente o FTP e o FTPS, **selecione Desativado**. Quando terminar, clique em **Guardar**. Se utilizar **apenas o FTPS,** tem de impor o TLS 1.2 ou superior navegando para a lâmina de **definições TLS/SSL** da sua aplicação web. Os TLS 1.0 e 1.1 não são suportados apenas com **FTPS**.

    ![Desativar FTP/S](./media/app-service-deploy-ftp/disable-ftp.png)

# <a name="azure-cli"></a>[CLI do Azure](#tab/cli)

Executar o comando de [conjunto de config do webapp az](/cli/azure/webapp/deployment#az_webapp_deployment_list_publishing_profiles) com o `--ftps-state` argumento.

```azurecli-interactive
az webapp config set --name <app-name> --resource-group <group-name> --ftps-state FtpsOnly
```

Os valores possíveis `--ftps-state` são `AllAllowed` (FTP e FTPS ativados), `Disabled` (FTP e FTPs desativados) e `FtpsOnly` (apenas FTPS).

# <a name="azure-powershell"></a>[Azure PowerShell](#tab/powershell)

Executar o comando [Set-AzWebApp](/powershell/module/az.websites/set-azwebapp) com o `-FtpsState` parâmetro.

```azurepowershell-interactive
Set-AzWebApp -Name <app-name> -ResourceGroupName <group-name> -FtpsState FtpsOnly
```

Os valores possíveis `--ftps-state` são `AllAllowed` (FTP e FTPS ativados), `Disabled` (FTP e FTPs desativados) e `FtpsOnly` (apenas FTPS).

-----

[!INCLUDE [What happens to my app during deployment?](../../includes/app-service-deploy-atomicity.md)]

## <a name="troubleshoot-ftp-deployment"></a>Resolução de problemas implantação FTP

- [Como posso resolver a implantação do FTP?](#how-can-i-troubleshoot-ftp-deployment)
- [Não sou capaz de ftp e publicar o meu código. Como posso resolver o problema?](#im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue)
- [Como posso ligar-me à FTP no Azure App Service através do modo passivo?](#how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode)

#### <a name="how-can-i-troubleshoot-ftp-deployment"></a>Como posso resolver a implantação do FTP?

O primeiro passo para a resolução de problemas da implantação da FTP é isolar um problema de implantação de um problema de aplicação de tempo de funcionamento.

Um problema de implementação normalmente resulta em nenhum ficheiro ou ficheiros errados implantados na sua aplicação. Pode resolver problemas investigando a sua implantação FTP ou selecionando uma trajetória de implantação alternativa (como controlo de origem).

Um problema de aplicação de tempo de execução normalmente resulta no conjunto certo de ficheiros implantados na sua aplicação, mas comportamento incorreto da aplicação. Pode resolver problemas focando-se no comportamento do código em tempo de execução e investigando caminhos específicos de falha.

Para determinar um problema de implementação ou tempo de execução, consulte [questões de execução vs. .](https://github.com/projectkudu/kudu/wiki/Deployment-vs-runtime-issues)

#### <a name="im-not-able-to-ftp-and-publish-my-code-how-can-i-resolve-the-issue"></a>I'm not able to FTP and publish my code. Como posso resolver o problema?
Verifique se inseriu o nome de anfitrião e [as credenciais corretas.](#get-deployment-credentials) [](#get-ftps-endpoint) Verifique também se as seguintes portas FTP da sua máquina não estão bloqueadas por uma firewall:

- Porta de ligação de controlo FTP: 21.990
- Porta de ligação de dados FTP: 989, 10001-10300
 
#### <a name="how-can-i-connect-to-ftp-in-azure-app-service-via-passive-mode"></a>Como posso ligar-me à FTP no Azure App Service através do modo passivo?
O Azure App Service suporta a ligação através do modo Ative e Passivo. O modo passivo é preferido porque as suas máquinas de implantação estão geralmente por trás de uma firewall (no sistema operativo ou como parte de uma rede doméstica ou empresarial). Veja um [exemplo da documentação winSCP](https://winscp.net/docs/ui_login_connection). 

## <a name="more-resources"></a>Mais recursos

* [Implantação local de Git para o Serviço de Aplicações Azure](deploy-local-git.md)
* [Credenciais de implementação do serviço de aplicações Azure](deploy-configure-credentials.md)
* [Amostra: Criar uma aplicação web e implementar ficheiros com FTP (Azure CLI)](./scripts/cli-deploy-ftp.md).
* [Amostra: Faça upload de ficheiros para uma aplicação web utilizando FTP (PowerShell)](./scripts/powershell-deploy-ftp.md).
