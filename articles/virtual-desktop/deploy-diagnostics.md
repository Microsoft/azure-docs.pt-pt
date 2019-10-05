---
title: Implantar a ferramenta de diagnóstico para a área de trabalho virtual do Windows-Azure
description: Como implantar a ferramenta de diagnóstico UX para a área de trabalho virtual do Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: helohr
ms.openlocfilehash: 83f10eb9dadfda5b87f1da287718f59da17c5110
ms.sourcegitcommit: 4f7dce56b6e3e3c901ce91115e0c8b7aab26fb72
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/04/2019
ms.locfileid: "71947609"
---
# <a name="deploy-the-diagnostics-tool"></a>Implementar a ferramenta de diagnóstico

Veja o que a ferramenta de diagnóstico para a área de trabalho virtual do Windows pode fazer por você:

- Pesquisar atividades de diagnóstico (gerenciamento, conexão ou feed) para um único usuário durante um período de uma semana.
- Reúna informações de host de sessão para atividades de conexão de seu espaço de trabalho Log Analytics.
- Examine os detalhes de desempenho da VM (máquina virtual) para um host específico.
- Veja quais usuários estão conectados ao host da sessão.
- Enviar mensagem para usuários ativos em um host de sessão específico.
- Desconectar usuários de um host de sessão.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa criar um registro de aplicativo Azure Active Directory e um espaço de trabalho Log Analytics antes de poder implantar o modelo de Azure Resource Manager para a ferramenta. Você ou o administrador precisa dessas permissões para fazer isso:

- Proprietário da assinatura do Azure
- Permissão para criar recursos em sua assinatura do Azure
- Permissão para criar um aplicativo do Azure AD
- Proprietário RDS ou direitos de colaborador

Você também precisa instalar esses dois módulos do PowerShell antes de começar:

- [Módulo Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-az-ps?view=azps-2.4.0)
- [Módulo do AD do Azure](https://docs.microsoft.com/powershell/azure/active-directory/install-adv2?view=azureadps-2.0)

Verifique se sua ID de assinatura está pronta para quando você entrar.

Depois de ter tudo em ordem, você pode criar o registro de aplicativo do Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar um registro de aplicativo Azure Active Directory

Esta seção mostrará como usar o PowerShell para criar o aplicativo Azure Active Directory com uma entidade de serviço e obter permissões de API para ele.

>[!NOTE]
>As permissões de API são área de trabalho virtual do Windows, Log Analytics e permissões de API Microsoft Graph são adicionadas ao aplicativo Azure Active Directory.

1. Abra o PowerShell como administrador.
2. Entre no Azure com uma conta que tenha permissões de proprietário ou colaborador na assinatura do Azure que você gostaria de usar para a ferramenta de diagnóstico:
   ```powershell
   Login-AzAccount
   ```
3. Entre no Azure AD com a mesma conta:
   ```powershell
   Connect-AzureAD
   ```
4. Vá para o [repositório do GitHub de modelos de RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateADAppRegistrationforDiagnostics. ps1** no PowerShell.
5.  Quando o script solicitar que você nomeie seu aplicativo, insira um nome de aplicativo exclusivo.


Depois que o script for executado com êxito, ele deverá mostrar as seguintes medidas em sua saída:

-  Uma mensagem que confirma que seu aplicativo agora tem uma atribuição de função de entidade de serviço.
-  A ID do cliente e a chave secreta do cliente que você precisará para quando implantar a ferramenta de diagnóstico.

Agora que você registrou seu aplicativo, é hora de configurar seu espaço de trabalho Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Configurar seu espaço de trabalho Log Analytics

Para obter a melhor experiência possível, recomendamos que você configure seu espaço de trabalho Log Analytics com os seguintes contadores de desempenho que permitem derivar instruções da experiência do usuário em uma sessão remota. Para obter uma lista de contadores recomendados com limites sugeridos, consulte [limites do contador de desempenho do Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Criar um espaço de trabalho do Azure Log Analytics usando o PowerShell

Você pode executar um script do PowerShell para criar um Log Analytics espaço de trabalho e configurar os contadores de desempenho do Windows recomendados para monitorar a experiência do usuário e o desempenho do aplicativo.

>[!NOTE]
>Se você já tiver um espaço de trabalho Log Analytics existente que você fez sem o script do PowerShell que deseja usar, pule para [validar os resultados do script na portal do Azure](#validate-the-script-results-in-the-azure-portal).

Para executar o script do PowerShell:

1.  Abra o PowerShell como administrador.
2.  Vá para o [repositório do GitHub de modelos de RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateLogAnalyticsWorkspaceforDiagnostics. ps1** no PowerShell.
3. Insira os seguintes valores para os parâmetros:

    - Para **ResourceGroupName**, insira o nome para o grupo de recursos.
    - Para **LogAnalyticsWorkspaceName**, insira um nome exclusivo para seu espaço de trabalho log Analytics.
    - Para **local**, insira a região do Azure que você está usando.
    - Insira a **ID da assinatura do Azure**, que pode ser encontrada no portal do Azure em **assinaturas**.

4. Insira as credenciais de um usuário com acesso de administrador delegado.
5. Entre no portal do Azure com as credenciais do mesmo usuário.
6. Anote ou memorize a ID do LogAnalyticsWorkspace para mais tarde.
7. Se você configurar o espaço de trabalho Log Analytics com o script do PowerShell, os contadores de desempenho já deverão estar configurados e você poderá pular para [validar os resultados do script na portal do Azure](#validate-the-script-results-in-the-azure-portal). Caso contrário, prossiga para a próxima seção.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Configurar contadores de desempenho do Windows no espaço de trabalho de Log Analytics existente

Esta seção é para os usuários que desejam usar um espaço de trabalho do Azure Log Analytics existente criado sem o script do PowerShell na seção anterior. Se você não tiver usado o script, deverá configurar os contadores de desempenho do Windows recomendados manualmente.

Veja como configurar manualmente os contadores de desempenho recomendados:

1. Abra seu navegador da Internet e entre no [portal do Azure](https://portal.azure.com/) com sua conta administrativa.
2. Em seguida, vá para **log Analytics espaços de trabalho** para examinar os contadores de desempenho do Windows configurados.
3. Na seção **configurações** , selecione **Configurações avançadas**.
4. Depois disso, navegue até **dados** > **contadores de desempenho do Windows** e adicione os seguintes contadores:

    -   LogicalDisk (\*) \|% de espaço livre
    -   LogicalDisk (C:) \\Avg. Comprimento da fila de discos
    -   Memória (\*) \\Available Mbytes
    -   Informações do processador (\*) \\Processor tempo
    -   Atraso de entrada do usuário por sessão (\*) @no__t-atraso de entrada-1Max

Saiba mais sobre os contadores de desempenho em [fontes de dados de desempenho do Windows e do Linux no Azure monitor](/azure/azure-monitor/platform/data-sources-performance-counters).

>[!NOTE]
>Quaisquer contadores adicionais que você configurar não aparecerão na ferramenta de diagnóstico em si. Para que ele apareça na ferramenta de diagnóstico, você precisa configurar o arquivo de configuração da ferramenta. As instruções sobre como fazer isso com a administração avançada estarão disponíveis no GitHub em uma data posterior.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Validar os resultados do script no portal do Azure

Antes de continuar a implantação da ferramenta de diagnóstico, recomendamos que você verifique se o aplicativo Azure Active Directory tem permissões de API e se o espaço de trabalho Log Analytics tem os contadores de desempenho do Windows pré-configurados.

### <a name="review-your-app-registration"></a>Examine o registro do aplicativo

Para certificar-se de que seu registro de aplicativo tenha permissões de API:

1. Abra um navegador e conecte-se ao [portal do Azure](https://portal.azure.com/) com sua conta administrativa.
2. Vá para **registros de aplicativo** e procure seu registro de aplicativo Azure AD.

      ![A página permissões de API.](media/api-permissions-page.png)


### <a name="review-your-log-analytics-workspace"></a>Examine seu espaço de trabalho do Log Analytics

Para certificar-se de que seu espaço de trabalho Log Analytics tem os contadores de desempenho do Windows pré-configurados:

1. Na [portal do Azure](https://portal.azure.com/), acesse **log Analytics espaços de trabalho** para examinar os contadores de desempenho do Windows configurados.
2. Em **configurações**, selecione **Configurações avançadas**.
3. Depois disso, vá para **dados** > **contadores de desempenho do Windows**.
4. Verifique se os seguintes contadores estão pré-configurados:

   - LogicalDisk (\*) \|% de espaço livre: Exibe a quantidade de espaço livre do total de espaço utilizável no disco como uma porcentagem.
   - LogicalDisk (C:) \\Avg. Comprimento da fila de disco: O comprimento da solicitação de transferência de disco para a unidade C. O valor não deve exceder 2 por mais de um curto período de tempo.
   - Memória (\*) \\Available Mbytes: A memória disponível para o sistema em megabytes.
   - Informações do processador (\*) \\Processor tempo: a porcentagem de tempo decorrido que o processador gasta para executar um thread não ocioso.
   - Atraso de entrada do usuário por sessão (\*) @no__t-atraso de entrada-1Max

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Conectar-se a VMs em seu espaço de trabalho Log Analytics

Para poder exibir a integridade das VMs, você precisará habilitar a conexão Log Analytics. Siga estas etapas para conectar suas VMs:

1. Abra um navegador e entre no [portal do Azure](https://portal.azure.com/) com sua conta administrativa.
2. Vá para o espaço de trabalho Log Analytics.
3. No painel esquerdo, em fontes de dados do espaço de trabalho, selecione **máquinas virtuais**.
4. Selecione o nome da VM à qual você deseja se conectar.
5. Selecione **Ligar**.

## <a name="deploy-the-diagnostics-tool"></a>Implementar a ferramenta de diagnóstico

Para implantar o modelo de gerenciamento de recursos do Azure para a ferramenta de diagnóstico:

1.  Vá para a [página do Azure RDS-templates do GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Implante o modelo no Azure e siga as instruções no modelo. Verifique se você tem as seguintes informações disponíveis:

    -   ID do cliente
    -   Segredo do cliente
    -   O ID da área de trabalho do Log Analytics

3.  Depois que os parâmetros de entrada forem fornecidos, aceite os termos e condições e, em seguida, selecione **comprar**.

A implantação levará de 2 a 3 minutos. Após a implantação bem-sucedida, vá para o grupo de recursos e verifique se o aplicativo Web e os recursos do plano do serviço de aplicativo estão lá.

Depois disso, você precisa definir o URI de redirecionamento.

### <a name="set-the-redirect-uri"></a>Definir o URI de redirecionamento

Para definir o URI de redirecionamento:

1.  Na [portal do Azure](https://portal.azure.com/), vá para **serviços de aplicativos** e localize o aplicativo que você criou.
2.  Vá para a página Visão geral e copie a URL que encontrar.
3.  Navegue até **registros do aplicativo** e selecione o aplicativo que você deseja implantar.
4.  No painel esquerdo, em gerenciar seção, selecione **autenticação**.
5.  Insira o URI de redirecionamento desejado na caixa de texto **URI de redirecionamento** e, em seguida, selecione **salvar** no canto superior esquerdo do menu.
6. Selecione **Web** no menu suspenso em tipo.
7. Insira a URL na página Visão geral do aplicativo e adicione **/Security/SignIn-callback** ao final dela. Por exemplo: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![A página URI de redirecionamento](media/redirect-uri-page.png)

8. Agora, vá para os recursos do Azure, selecione o recurso serviços de Azure App com o nome fornecido no modelo e navegue até a URL associada a ele. (Por exemplo, se o nome do aplicativo usado no modelo foi `contosoapp45`, a URL associada será <https://contosoapp45.azurewebsites.net>).
9. Entre usando a conta de usuário do Azure Active Directory apropriada.
10.   Selecione **Aceitar**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuir a ferramenta de diagnóstico

Antes de disponibilizar a ferramenta de diagnóstico para seus usuários, verifique se eles têm as seguintes permissões:

- Os usuários precisam de acesso de leitura para o log Analytics. Para obter mais informações, consulte Introdução [às funções, permissões e segurança com Azure monitor](/articles/azure-monitor/platform/roles-permissions-security.md).
-  Os usuários também precisam de acesso de leitura para o locatário da área de trabalho virtual do Windows (função leitor do RDS). Para obter mais informações, consulte [acesso delegado na área de trabalho virtual do Windows](delegated-access-virtual-desktop.md).

Você também precisa dar aos seus usuários as seguintes informações:

- A URL do aplicativo
- Os nomes do locatário individual do grupo de locatários que eles podem acessar.

## <a name="use-the-diagnostics-tool"></a>Usar a ferramenta de diagnóstico

Depois de entrar na sua conta usando as informações que você recebeu da sua organização, tenha o UPN pronto para o usuário para o qual você deseja consultar atividades. Uma pesquisa fornecerá a você todas as atividades sob o tipo de atividade especificado que ocorreram na última semana.

### <a name="how-to-read-activity-search-results"></a>Como ler os resultados da pesquisa de atividade

As atividades são classificadas por carimbo de data/hora, com a atividade mais recente primeiro. Se os resultados retornarem um erro, primeiro verifique se é um erro de serviço. Para erros de serviço, crie um tíquete de suporte com as informações de atividade para nos ajudar a depurar o problema. Todos os outros tipos de erro geralmente podem ser resolvidos pelo usuário ou pelo administrador. Para obter uma lista dos cenários de erro mais comuns e como resolvê-los, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Os erros de serviço são chamados de "erros externos" na documentação vinculada. Isso será alterado quando atualizarmos a referência do PowerShell.

As atividades de conexão podem ter mais de um erro. Você pode expandir o tipo de atividade para ver quaisquer outros erros que o usuário tenha fornecido. Selecione o nome do código de erro para abrir uma caixa de diálogo para ver mais informações sobre ela.

### <a name="investigate-the-session-host"></a>Investigar o host da sessão 

Nos resultados da pesquisa, localize e selecione o host da sessão sobre o qual você deseja obter informações.

Você pode analisar a integridade do host da sessão:

- Com base em um limite predefinido, você pode recuperar as informações de integridade do host da sessão que Log Analytics consultas.
- Quando não houver atividade ou o host da sessão não estiver conectado a Log Analytics, as informações não estarão disponíveis.

Você também pode interagir com os usuários no host da sessão:

- Você pode sair ou enviar uma mensagem para usuários conectados.
- O usuário que você pesquisou originalmente está selecionado por padrão, mas você também pode selecionar usuários adicionais para enviar mensagens ou desconectar vários usuários ao mesmo tempo.

### <a name="windows-performance-counter-thresholds"></a>Limites do contador de desempenho do Windows

- LogicalDisk (\*) \|% de espaço livre:

    - Exibe o percentual do total de espaço utilizável no disco lógico que é gratuito.
    - Os Menos de 20% é marcado como não íntegro.

- LogicalDisk (C:) \\Avg. Comprimento da fila de disco:

    - Representa as condições do sistema de armazenamento.
    - Os Maior que 5 é marcado como não íntegro.

- Memória (\*) \\Available Mbytes:

    - A memória disponível para o sistema.
    - Os Menos de 500 megabytes marcados como não íntegros.

- Informações do processador (\*) \\Processor tempo:

    - Os Maior que 80% é marcado como não íntegro.

- [Atraso de entrada do usuário por sessão (\*) @no__t-atraso de entrada-2max](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters):

    - Os Mais de 2000 MS está marcado como não íntegro.
