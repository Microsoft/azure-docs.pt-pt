---
title: Implementar a ferramenta de diagnóstico para Windows Virtual Desktop - Azure
description: Como implementar a ferramenta DE DIAGNÓSTICO UX para windows Virtual Desktop.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 03/20/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 4eb63fe4bd8f8a8b0961aa6a7fccb8de9b7c2f16
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80123412"
---
# <a name="deploy-the-diagnostics-tool"></a>Implementar a ferramenta de diagnóstico

>[!IMPORTANT]
>A partir de 16 de março de 2020, desativamos temporariamente consultas de diagnóstico que afetaram a experiência do utilizador devido ao aumento da procura no serviço. Isto fará com que a ferramenta deixe de funcionar porque depende dessas consultas para funcionar. Atualizaremos este artigo quando as consultas de diagnóstico estiverem novamente disponíveis.
>
>Até lá, recomendamos vivamente que [utilize o Log Analytics](diagnostics-log-analytics.md) para uma monitorização contínua.

Eis o que a ferramenta de diagnóstico do Windows Virtual Desktop pode fazer por si:

- Procure atividades de diagnóstico (gestão, ligação ou alimentação) para um único utilizador durante um período de uma semana.
- Reúna informações sobre o anfitrião da sessão para atividades de conexão a partir do seu espaço de trabalho Log Analytics.
- Reveja os detalhes de desempenho da máquina virtual (VM) para um determinado anfitrião.
- Veja em que utilizadores estão inscritos no anfitrião da sessão.
- Envie mensagem a utilizadores ativos num anfitrião de sessão específico.
- Inscreva os utilizadores num anfitrião da sessão.

## <a name="prerequisites"></a>Pré-requisitos

Você precisa criar um Registo de App de Diretório Ativo Azure e um espaço de trabalho Log Analytics antes de poder implementar o modelo de Gestor de Recursos Azure para a ferramenta. Você ou o administrador precisam destas permissões para fazer isso:

- Proprietário da assinatura Azure
- Permissão para criar recursos na sua subscrição Azure
- Permissão para criar uma aplicação Azure AD
- Direitos de Proprietário ou Contribuinte RDS

Também precisa de instalar estes dois módulos PowerShell antes de começar:

- [Módulo do Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.4.0/)
- [Módulo Azure AD](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/)

Certifique-se de que tem o seu ID de subscrição pronto para quando iniciar a sua inscrição.

Depois de ter tudo em ordem, pode criar o registo da aplicação Azure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar uma inscrição de aplicativo de Diretório Ativo Azure

Esta secção irá mostrar-lhe como usar o PowerShell para criar a aplicação Azure Ative Directory com um diretor de serviço e obter permissões API para o mesmo.

>[!NOTE]
>As permissões API são as permissões Windows Virtual Desktop, Log Analytics e Microsoft Graph API são adicionadas à Aplicação de Diretório Ativo Azure.

1. Abra o PowerShell como Administrador.
2. Inscreva-se no Azure com uma conta que tenha permissões do Proprietário ou do Colaborador na subscrição do Azure que gostaria de utilizar para a ferramenta de diagnóstico:
   ```powershell
   Login-AzAccount
   ```
3. Inscreva-se na Azure AD com a mesma conta:
   ```powershell
   Connect-AzureAD
   ```
4. Vá ao [repo do RDS-Templates GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateADAppRegistrationforDiagnostics.ps1** no PowerShell.
5.  Quando o script lhe pedir para nomear a sua aplicação, insira um nome de aplicação único.


Depois de o guião ser executado com sucesso, deve mostrar as seguintes coisas na sua saída:

-  Uma mensagem que confirma que a sua aplicação tem agora uma atribuição principal de serviço.
-  O ID do cliente e a chave secreta do cliente que necessitará quando implementar a ferramenta de diagnóstico.

Agora que registou a sua aplicação, é hora de configurar o seu espaço de trabalho log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Configure o seu espaço de trabalho log Analytics

Para a melhor experiência possível, recomendamos que configure o seu espaço de trabalho Log Analytics com os seguintes contadores de desempenho que lhe permitam obter declarações da experiência do utilizador numa sessão remota. Para obter uma lista de contadores recomendados com limiares sugeridos, consulte [os limiares](deploy-diagnostics.md#windows-performance-counter-thresholds)de contador de desempenho do Windows .

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Criar um espaço de trabalho Azure Log Analytics usando powerShell

Pode executar um script PowerShell para criar um espaço de trabalho de Log Analytics e configurar os contadores de desempenho recomendados do Windows para monitorizar a experiência do utilizador e o desempenho da aplicação.

>[!NOTE]
>Se já tem um espaço de trabalho de Log Analytics existente que fez sem o script PowerShell que deseja utilizar, salte para a frente para [Validar os resultados do script no portal Azure](#validate-the-script-results-in-the-azure-portal).

Para executar o script PowerShell:

1.  Open PowerShell como administrador.
2.  Vá ao [repo de modelos RDS GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o script **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** no PowerShell.
3. Introduza os seguintes valores para os parâmetros:

    - Para **ResourceGroupName,** introduza o nome para o grupo de recursos.
    - Para **logAnalyticsWorkspaceName,** introduza um nome único para o seu espaço de trabalho Log Analytics.
    - Para **localização,** entre na região de Azure que está a usar.
    - Introduza o ID de **subscrição Azure,** que pode encontrar no portal Azure em **Subscrições**.

4. Introduza as credenciais de um utilizador com acesso administrativo delegado.
5. Inscreva-se no portal Azure com as credenciais do mesmo utilizador.
6. Escreva ou memorize o ID logAnalyticsWorkspace para mais tarde.
7. Se configurar o espaço de trabalho do Log Analytics com o script PowerShell, então os seus contadores de desempenho já devem estar configurados e pode saltar para a frente para [validar os resultados do script no portal Azure](#validate-the-script-results-in-the-azure-portal). Caso contrário, continue para a próxima secção.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Configure os contadores de desempenho do Windows no espaço de trabalho existente no Log Analytics

Esta secção destina-se a utilizadores que pretendam utilizar um espaço de trabalho existente no Azure Log Analytics criado sem o script PowerShell na secção anterior. Se não utilizou o script, então deve configurar manualmente os contadores de desempenho recomendados do Windows.

Aqui está como configurar manualmente os contadores de desempenho recomendados:

1. Abra o seu navegador de Internet e inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. Em seguida, vá aos espaços de **trabalho do Log Analytics** para rever os contadores de desempenho configurados do Windows.
3. Na secção **Definições,** selecione **Definições Avançadas**.
4. Depois disso, navegue **para** > **Data Windows Performance Counters** e adicione os seguintes contadores:

    -   LogicalDisk,\*\\%Free Space
    -   LogicalDisk (C:)\\Avg. Comprimento da fila do disco
    -   Memória(\*\\) Mbytes disponíveis
    -   Informação\*do\\Processador( ) Tempo do Processador
    -   Atraso de entrada do\*\\utilizador por sessão( ) Atraso de entrada max

Saiba mais sobre os contadores de desempenho do [Windows e do Linux no Azure Monitor](/azure/azure-monitor/platform/data-sources-performance-counters).

>[!NOTE]
>Quaisquer contadores adicionais que configurar não aparecerão na própria ferramenta de diagnóstico. Para que apareça na ferramenta de diagnóstico, é necessário configurar o ficheiro config da ferramenta. Instruções para como fazê-lo com administração avançada estarão disponíveis no GitHub mais tarde.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Validar os resultados do script no portal Azure

Antes de continuar a implementar a ferramenta de diagnóstico, recomendamos que verifique se a sua aplicação Azure Ative Directory tem permissões API e que o seu espaço de trabalho log Analytics tem os contadores de desempenho do Windows reconfigurados.

### <a name="review-your-app-registration"></a>Reveja o registo da sua aplicação

Para garantir que o registo da sua aplicação tem permissões API:

1. Abra um navegador e ligue-se ao [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. Vá ao **Diretório Ativo Azure.**
3. Vá às **inscrições da App** e selecione **Todas as Aplicações**.
4. Procure o registo da sua aplicação Azure AD com o mesmo nome de aplicação que inseriu no passo 5 da [Create a Azure Ative Directory.](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)

### <a name="review-your-log-analytics-workspace"></a>Reveja o seu espaço de trabalho log Analytics

Para se certificar de que o seu espaço de trabalho log Analytics tem os contadores de desempenho do Windows reconfigurados:

1. No [portal Azure,](https://portal.azure.com/)vá a espaços de **trabalho do Log Analytics** para rever os contadores de desempenho configurados do Windows.
2. Em **Definições,** selecione **Definições avançadas**.
3. Depois disso, vá aos Contadores de Desempenho do**Windows**de **Dados** > .
4. Certifique-se de que os seguintes contadores estão pré-configurados:

   - LogicalDisk,\*\\%Free Space: Exibe em percentagem a quantidade de espaço livre do espaço total utilizável no disco.
   - LogicalDisk (C:)\\Avg. Comprimento da fila do disco: O comprimento do pedido de transferência do disco para a sua unidade C. O valor não deve exceder 2 por mais de um curto período de tempo.
   - Memória:\*\\Mbytes disponível: A memória disponível para o sistema em megabytes.
   - Informações\*\\do Processador: a percentagem de tempo decorrido que o processador gasta para executar uma linha não ociosa.
   - Atraso de entrada do\*\\utilizador por sessão( ) Atraso de entrada max

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Ligue-se a VMs no seu espaço de trabalho Log Analytics

Para poder ver a saúde dos VMs, terá de ativar a ligação Log Analytics. Siga estes passos para ligar os seus VMs:

1. Abra um navegador e inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. Vá ao seu espaço de trabalho de Log Analytics.
3. No painel esquerdo, sob fontes de dados do Espaço de Trabalho, selecione **máquinas virtuais**.
4. Selecione o nome do VM a que pretende ligar.
5. Selecione **Ligar**.

## <a name="deploy-the-diagnostics-tool"></a>Implementar a ferramenta de diagnóstico

Para implementar o modelo de Gestão de Recursos Azure para a ferramenta de diagnóstico:

1.  Vá à [página GitHub Azure RDS-Templates](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Desloque o modelo para Azure e siga as instruções no modelo. Certifique-se de que tem as seguintes informações disponíveis:

    -   Cliente-Id
    -   Segredo de Cliente
    -   O ID da área de trabalho do Log Analytics

3.  Uma vez fornecidos os parâmetros de entrada, aceite os termos e condições e, em seguida, **selecione Comprar**.

O destacamento levará 2 a 3 minutos. Após a implementação bem sucedida, vá ao grupo de recursos e certifique-se de que os recursos do plano de aplicação web e do plano de aplicações estão lá.

Depois disso, tens de definir o Redirecionamento URI.

### <a name="set-the-redirect-uri"></a>Definir o Redirecionamento URI

Para definir o Redirecionamento URI:

1.  No [portal Azure,](https://portal.azure.com/)vá aos Serviços de **Aplicações** e localize a aplicação que criou.
2.  Vá à página de visão geral e copie o URL que encontrar lá.
3.  Navegue para registos de **aplicações** e selecione a aplicação que pretende implementar.
4.  No painel esquerdo, na secção Gerir, **selecione Autenticação**.
5.  Introduza o REdirect URI desejado na caixa de texto **Redirecionamento URI** e, em seguida, selecione **Guardar** no canto superior esquerdo do menu.
6. Selecione **Web** no menu suspenso em Type.
7. Introduza o URL a partir da página de visão geral da aplicação e adicione **/security/signin-callback** até ao fim da sua. Por exemplo: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   ![A página URI redirecionamento](media/redirect-uri-page.png)

8. Agora, vá aos seus recursos Azure, selecione o recurso Azure App Services com o nome que forneceu no modelo e navegue para o URL associado ao mesmo. (Por exemplo, se o nome da `contosoapp45`aplicação que usou <https://contosoapp45.azurewebsites.net>no modelo fosse, então o seu URL associado é).
9. Inscreva-se na utilização da conta de utilizador do Diretório Ativo Azure apropriado.
10.   Selecione **Aceitar**.

## <a name="distribute-the-diagnostics-tool"></a>Distribua a ferramenta de diagnóstico

Antes de disponibilizar a ferramenta de diagnóstico aos seus utilizadores, certifique-se de que possuem as seguintes permissões:

- Os utilizadores precisam de acesso a leitura para análise de registo. Para mais informações, consulte [Começar com funções, permissões e segurança com o Monitor Azure.](/azure/azure-monitor/platform/roles-permissions-security)
-  Os utilizadores também precisam de ler o acesso para o inquilino do Windows Virtual Desktop (função RDS Reader). Para mais informações, consulte [o acesso delegado no Windows Virtual Desktop](delegated-access-virtual-desktop.md).

Também precisa de dar aos seus utilizadores as seguintes informações:

- Url da aplicação
- Os nomes do inquilino individual inquilino a que podem aceder.

## <a name="use-the-diagnostics-tool"></a>Utilize a ferramenta de diagnóstico

Depois de ter assinado a sua conta usando as informações recebidas da sua organização, tenha a UPN pronta para o utilizador que pretende consultar as atividades. Uma pesquisa irá dar-lhe todas as atividades sob o tipo de atividade especificado que aconteceu na última semana.

### <a name="how-to-read-activity-search-results"></a>Como ler os resultados da pesquisa de atividade

As atividades são classificadas por carimbo temporal, com a última atividade em primeiro lugar. Se os resultados devolverem um erro, verifique primeiro se se trata de um erro de serviço. Para erros de serviço, crie um bilhete de suporte com a informação da atividade para nos ajudar a depurar o problema. Todos os outros tipos de erros podem geralmente ser resolvidos pelo utilizador ou administrador. Para obter uma lista dos cenários de erro mais comuns e como resolvê-los, consulte [identificar e diagnosticar problemas](diagnostics-role-service.md#common-error-scenarios).

>[!NOTE]
>Os erros de serviço são chamados de "erros externos" na documentação ligada. Isto será alterado quando atualizarmos a referência PowerShell.

As atividades de ligação podem ter mais do que um erro. Pode expandir o tipo de atividade para ver quaisquer outros erros que o utilizador tenha deparar. Selecione o nome do código de erro para abrir um diálogo para ver mais informações sobre o mesmo.

### <a name="investigate-the-session-host"></a>Investigue o anfitrião da sessão 

Nos resultados da pesquisa, encontre e selecione o anfitrião da sessão sobre o qual deseja informações.

Pode analisar a sessão de saúde do anfitrião:

- Com base num limiar predefinido, pode recuperar a sessão de informações de saúde que log Analytics consultas.
- Quando não há atividade ou o anfitrião da sessão não está ligado ao Log Analytics, a informação não estará disponível.

Também pode interagir com os utilizadores no anfitrião da sessão:

- Pode assinar ou enviar uma mensagem para os utilizadores.
- O utilizador que procurou originalmente é selecionado por padrão, mas também pode selecionar utilizadores adicionais para enviar mensagens ou assinar vários utilizadores ao mesmo tempo.

### <a name="windows-performance-counter-thresholds"></a>Limiares de contra-limite de desempenho do Windows

- LogicalDisk,\*\\%Free Space:

    - Exibe a percentagem do espaço total utilizável no disco lógico que é gratuito.
    - Limiar: Menos de 20% é marcado como insalubre.

- LogicalDisk (C:)\\Avg. Comprimento da fila do disco:

    - Representa condições do sistema de armazenamento.
    - Limiar: Mais de 5 é marcado como insalubre.

- Memória(\*\\) Mbytes disponíveis:

    - A memória disponível para o sistema.
    - Limiar: Menos de 500 megabytes marcados como insalubres.

- Informações\*sobre\\processadores( ) Tempo do processador:

    - Limiar: Mais de 80% é marcado como insalubre.

- Atraso de entrada do [utilizador por\*sessão( )\\Atraso](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)de entrada máxima :

    - Limiar: Mais de 2000 ms é marcado como insalubre.

## <a name="next-steps"></a>Passos seguintes

- Saiba como monitorizar os registos de atividade em [Utilizar diagnósticos com Log Analytics](diagnostics-log-analytics.md).
- Leia sobre cenários de erro comuns e como corrigi-los em [questões de identificação e diagnóstico](diagnostics-role-service.md).
