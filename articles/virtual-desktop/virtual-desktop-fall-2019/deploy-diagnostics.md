---
title: Implementar a ferramenta de diagnóstico para Windows Virtual Desktop (clássico) - Azure
description: Como implementar a ferramenta UX de diagnóstico para o Windows Virtual Desktop (clássico).
author: Heidilohr
ms.topic: how-to
ms.date: 12/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 05d312296b48f35ea87c5443cc587989e2d11fd5
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/17/2021
ms.locfileid: "100587973"
---
# <a name="deploy-the-windows-virtual-desktop-classic-diagnostics-tool"></a>Implementar a ferramenta de diagnóstico virtual do Windows Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows.

Aqui está o que a ferramenta de diagnóstico para o Windows Virtual Desktop pode fazer por si:

- Procure atividades de diagnóstico (gestão, ligação ou feed) para um único utilizador durante um período de uma semana.
- Recolha informações do anfitrião da sessão para atividades de conexão a partir do seu espaço de trabalho Log Analytics.
- Reveja os detalhes de desempenho da máquina virtual (VM) para um anfitrião em particular.
- Veja quais os utilizadores que estão inscritos no anfitrião da sessão.
- Envie mensagem a utilizadores ativos num anfitrião de sessão específico.
- Assine os utilizadores de um anfitrião de sessão.

## <a name="prerequisites"></a>Pré-requisitos

Precisa de criar um Registo de Aplicações de Diretório Ativo Azure e um espaço de trabalho Log Analytics antes de poder implementar o modelo de Gestor de Recursos Azure para a ferramenta. Você ou o administrador precisam destas permissões para o fazer:

- Proprietário da assinatura Azure
- Permissão para criar recursos na sua subscrição Azure
- Permissão para criar uma aplicação AD AZure
- Direitos do Proprietário ou contribuinte rds

Também precisa de instalar estes dois módulos PowerShell antes de começar:

- [Módulo do Azure PowerShell](/powershell/azure/install-az-ps?view=azps-2.4.0/&preserve-view=true)
- [Módulo AD AZure](/powershell/azure/active-directory/install-adv2?view=azureadps-2.0/&preserve-view=true)

Certifique-se de que tem o seu ID de subscrição pronto para quando iniciar sôs o seu sôm.

Depois de ter tudo em ordem, pode criar o registo da aplicação AZure AD.

## <a name="create-an-azure-active-directory-app-registration"></a>Criar um registo de aplicativos Azure Ative Directory

Esta secção irá mostrar-lhe como usar o PowerShell para criar a aplicação Azure Ative Directory com um diretor de serviço e obter permissões API para o mesmo.

>[!NOTE]
>As permissões API são permissões de Desktop Virtual Windows, Log Analytics e Microsoft Graph API adicionadas à Aplicação de Diretório Ativo Azure.

1. Abra o PowerShell como Administrador.
2. Inscreva-se no Azure com uma conta que tenha permissões de Proprietário ou Contribuinte na subscrição Azure que gostaria de utilizar para a ferramenta de diagnóstico:
   ```powershell
   Login-AzAccount
   ```
3. Inscreva-se no AZure AD com a mesma conta:
   ```powershell
   Connect-AzureAD
   ```
4. Vá ao [repo GitHub dos modelos RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o **CreateADAppRegistrationforDiagnostics.ps1** script em PowerShell.
5.  Quando o script lhe pedir para nomear a sua aplicação, insira um nome de aplicação único.


Após a execução do script com sucesso, deve mostrar as seguintes coisas na sua saída:

-  Uma mensagem que confirma que a sua aplicação tem agora uma atribuição principal de função de serviço.
-  O seu ID de Cliente e Chave Secreta do Cliente para a qual necessitará quando implementar a ferramenta de diagnóstico.

Agora que registou a sua aplicação, está na hora de configurar o seu espaço de trabalho Log Analytics.

## <a name="configure-your-log-analytics-workspace"></a>Configure o seu espaço de trabalho Log Analytics

Para obter a melhor experiência possível, recomendamos que configuure o seu espaço de trabalho Log Analytics com os seguintes contadores de desempenho que lhe permitem obter declarações da experiência do utilizador numa sessão remota. Para obter uma lista de contadores recomendados com limiares sugeridos, consulte [os limiares do contador de desempenho do Windows](deploy-diagnostics.md#windows-performance-counter-thresholds).

### <a name="create-an-azure-log-analytics-workspace-using-powershell"></a>Criar um espaço de trabalho Azure Log Analytics utilizando o PowerShell

Pode executar um script PowerShell para criar um espaço de trabalho Log Analytics e configurar os contadores de desempenho recomendados do Windows para monitorizar a experiência do utilizador e o desempenho da aplicação.

>[!NOTE]
>Se já tem um espaço de trabalho log analytics existente que fez sem o script PowerShell que pretende utilizar, avance para [validar os resultados do script no portal Azure](#validate-the-script-results-in-the-azure-portal).

Para executar o script PowerShell:

1.  Open PowerShell como administrador.
2.  Vá ao [repo GitHub dos modelos RDS](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy/scripts) e execute o **CreateLogAnalyticsWorkspaceforDiagnostics.ps1** script em PowerShell.
3. Introduza os seguintes valores para os parâmetros:

    - Para **o Conjunto de Recursos,** insira o nome do grupo de recursos.
    - Para **LogAnalyticsWorkspaceName, insira** um nome único para o seu espaço de trabalho Log Analytics.
    - Para **localização,** insira a região Azure que está a usar.
    - Introduza o **ID de subscrição Azure,** que pode encontrar no portal Azure em **Subscrições**.

4. Insira as credenciais de um utilizador com acesso de administração delegado.
5. Inscreva-se no portal Azure com as credenciais do mesmo utilizador.
6. Escreva ou memorize o ID logAnalyticsWorkspace para mais tarde.
7. Se configurar o espaço de trabalho Do Log Analytics com o script PowerShell, então os seus contadores de desempenho já devem estar configurados e pode antecipar-se para [validar os resultados do script no portal Azure](#validate-the-script-results-in-the-azure-portal). Caso contrário, continue para a secção seguinte.

### <a name="configure-windows-performance-counters-in-your-existing-log-analytics-workspace"></a>Configure os contadores de desempenho do Windows no seu espaço de trabalho log analytics existente

Esta secção destina-se a utilizadores que pretendam utilizar um espaço de trabalho Azure Log Analytics criado sem o script PowerShell na secção anterior. Se não tiver utilizado o script, então deve configurar manualmente os contadores de desempenho recomendados do Windows.

Eis como configurar manualmente os contadores de desempenho recomendados:

1. Abra o seu navegador de Internet e inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. Em seguida, vá aos **espaços de trabalho do Log Analytics** para rever os contadores de desempenho do Windows configurados.
3. Na secção **Definições,** selecione  **Definições Avançadas**.
4. Depois disso, navegue nos  >  **Contadores de Desempenho do Windows de dados** e adicione os seguintes contadores:

    -   LogicalDisk, \* \\ %Espaço Livre
    -   LogicalDisk(C:) \\ Avg. Comprimento da fila do disco
    -   Memória( \* ) \\ Mbytes disponíveis
    -   Tempo de \* processador de informação do \\ processador
    -   Atraso de entrada do utilizador por \* sessão. \\

Saiba mais sobre os contadores de desempenho no [Windows e nas fontes de dados de desempenho do Linux no Azure Monitor](../../azure-monitor/agents/data-sources-performance-counters.md).

>[!NOTE]
>Quaisquer contadores adicionais que configurar não aparecerão na própria ferramenta de diagnóstico. Para que apareça na ferramenta de diagnóstico, é necessário configurar o ficheiro config da ferramenta. As instruções para como fazê-lo com administração avançada estarão disponíveis no GitHub mais tarde.

## <a name="validate-the-script-results-in-the-azure-portal"></a>Validar os resultados do script no portal Azure

Antes de continuar a implementar a ferramenta de diagnóstico, recomendamos que verifique se a sua aplicação Azure Ative Directory tem permissões API e que o seu espaço de trabalho Log Analytics tem os contadores de desempenho pré-configurados do Windows.

### <a name="review-your-app-registration"></a>Reveja o registo da sua aplicação

Para garantir que o registo da sua aplicação tem permissões de API:

1. Abra um browser e ligue-se ao [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. Ir ao **Azure Ative Directory**.
3. Aceda às **inscrições da App** e selecione **Todas as Aplicações.**
4. Procure o seu registo de aplicações AZure AD com o mesmo nome de aplicação que inseriu no passo 5 do Registo de [aplicações do Azure Ative Directory.](deploy-diagnostics.md#create-an-azure-active-directory-app-registration)

### <a name="review-your-log-analytics-workspace"></a>Reveja o seu espaço de trabalho Log Analytics

Para se certificar de que o seu espaço de trabalho Log Analytics tem os contadores de desempenho pré-configurados do Windows:

1. No [portal Azure,](https://portal.azure.com/)vá aos **espaços de trabalho do Log Analytics** para rever os contadores de desempenho do Windows configurados.
2. Em **Definições**, selecione **Definições Avançadas**.
3. Depois disso, aceda aos  >  **contadores de desempenho do Data Windows**.
4. Certifique-se de que os seguintes contadores estão pré-configurados:

   - Por \* \\ %Free Space: Exibe em percentagem a quantidade de espaço livre do espaço total utilizável do espaço utilizável total do disco.
   - LogicalDisk (C:) \\ Avg. Comprimento da fila do disco: O comprimento do pedido de transferência de disco para a sua unidade C. O valor não deve exceder 2 por um curto período de tempo.
   - Memória: \* \\ Mbytes disponíveis: A memória disponível para o sistema em megabytes.
   - Informação do \* \\ processador: a percentagem de tempo decorrido que o processador gasta para executar um fio não inativo.
   - Atraso de entrada do utilizador por \* sessão. \\

### <a name="connect-to-vms-in-your-log-analytics-workspace"></a>Conecte-se a VMs no seu espaço de trabalho Log Analytics

Para poder ver a saúde dos VMs, terá de ativar a ligação Log Analytics. Siga estes passos para ligar os seus VMs:

1. Abra um browser e inscreva-se no [portal Azure](https://portal.azure.com/) com a sua conta administrativa.
2. Vá ao seu log analytics workspace.
3. No painel esquerdo, em Fontes de Dados do Espaço de Trabalho, selecione **máquinas virtuais.**
4. Selecione o nome do VM a que pretende ligar.
5. Selecione **Ligar**.

## <a name="deploy-the-diagnostics-tool"></a>Implementar a ferramenta de diagnóstico

Para implementar o modelo de Gestão de Recursos Azure para a ferramenta de diagnóstico:

1.  Vá à [página de RDS-Templates GitHub Azure](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/diagnostics-sample/deploy).
2.  Desloque o modelo para Azure e siga as instruções do modelo. Certifique-se de que tem as seguintes informações disponíveis:

    -   Client-Id
    -   Client-Secret
    -   O ID da área de trabalho do Log Analytics

3.  Assim que os parâmetros de entrada forem fornecidos, aceite os termos e condições e, em seguida, selecione **Comprar**.

A colocação levará 2 a 3 minutos. Após uma implementação bem sucedida, vá ao grupo de recursos e certifique-se de que os recursos da aplicação web e do plano de aplicações estão lá.

Depois disso, tens de definir o URI de redirecionamento.

### <a name="set-the-redirect-uri"></a>Definir o URI de redirecionamento

Para definir o URI de redirecionamento:

1.  No [portal Azure,](https://portal.azure.com/)vá aos **Serviços de Aplicações** e localize a aplicação que criou.
2.  Vá à página geral e copie o URL que encontrar lá.
3.  Navegue para **registos de aplicações** e selecione a app que pretende implementar.
4.  No painel esquerdo, na secção Manage, selecione **Autenticação**.
5.  Introduza o URI de redirecionamento desejado na caixa de texto **Redirect URI** e, em seguida, selecione **Guardar** no canto superior esquerdo do menu.
6. Selecione **Web** no menu suspenso em Type.
7. Introduza o URL a partir da página geral da aplicação e adicione **/security/signin-callback** até ao final da visão. Por exemplo: `https://<yourappname>.azurewebsites.net/security/signin-callback`.

   > [!div class="mx-imgBorder"]
   > ![A página URI redirecionado](../media/redirect-uri-page.png)

8. Agora, vá aos seus recursos Azure, selecione o recurso Azure App Services com o nome que forneceu no modelo e navegue para o URL associado a ele. (Por exemplo, se o nome da aplicação que usou no modelo `contosoapp45` foi, então o url associado é <http://contoso.azurewebsites.net> ).
9. Inscreva-se utilizando a conta de utilizador Azure Ative Directory apropriada.
10.   Selecione **Aceitar**.

## <a name="distribute-the-diagnostics-tool"></a>Distribuir a ferramenta de diagnóstico

Antes de disponibilizar a ferramenta de diagnóstico aos seus utilizadores, certifique-se de que dispõem das seguintes permissões:

- Os utilizadores precisam de ler o acesso para análise de registos. Para mais informações, consulte [Começar com papéis, permissões e segurança com o Azure Monitor.](../../azure-monitor/roles-permissions-security.md)
-  Os utilizadores também precisam de acesso lido para o inquilino virtual do Windows Desktop (função RDS Reader). Para obter mais informações, consulte [o acesso delegado no Windows Virtual Desktop](delegated-access-virtual-desktop-2019.md).

Também precisa de dar aos seus utilizadores as seguintes informações:

- URL da aplicação
- Os nomes do inquilino do grupo de inquilinos podem aceder.

## <a name="use-the-diagnostics-tool"></a>Utilize a ferramenta de diagnóstico

Depois de ter feito o seu contrato com a sua conta utilizando as informações que recebeu da sua organização, tenha a UPN pronta para o utilizador para o qual pretende consultar as atividades. Uma pesquisa irá dar-lhe todas as atividades sob o tipo de atividade especificado que aconteceu na última semana.

### <a name="how-to-read-activity-search-results"></a>Como ler resultados de pesquisa de atividades

As atividades são ordenadas por timetamp, com a mais recente atividade em primeiro lugar. Se os resultados devolverem um erro, verifique primeiro se é um erro de serviço. Para erros de serviço, crie um bilhete de apoio com a informação da atividade para nos ajudar a depurar o problema. Todos os outros tipos de erros podem normalmente ser resolvidos pelo utilizador ou administrador. Para obter uma lista dos cenários de erro mais comuns e como resolvê-los, consulte [identificar e diagnosticar problemas.](diagnostics-role-service-2019.md#common-error-scenarios)

>[!NOTE]
>Os erros de serviço são chamados de "erros externos" na documentação ligada. Isto será alterado quando atualizarmos a referência PowerShell.

As atividades de ligação podem ter mais do que um erro. Pode expandir o tipo de atividade para ver quaisquer outros erros que o utilizador tenha visto. Selecione o nome do código de erro para abrir um diálogo para ver mais informações sobre o mesmo.

### <a name="investigate-the-session-host"></a>Investigue o anfitrião da sessão

Nos resultados da pesquisa, encontre e selecione o anfitrião da sessão sobre o qual deseja obter informações.

Pode analisar a sessão de saúde do anfitrião:

- Com base num limiar predefinido, pode obter a sessão de informação de saúde que log Analytics consulta.
- Quando não há atividade ou o anfitrião da sessão não estiver ligado ao Log Analytics, a informação não estará disponível.

Também pode interagir com os utilizadores no anfitrião da sessão:

- Pode assinar ou enviar uma mensagem para os utilizadores assinados.
- O utilizador que procurou originalmente é selecionado por padrão, mas também pode selecionar utilizadores adicionais para enviar mensagens ou assinar vários utilizadores de uma só vez.

### <a name="windows-performance-counter-thresholds"></a>Limiares de contador de desempenho do Windows

- LogicalDisk, \* \\ %Espaço Livre:

    - Apresenta a percentagem do espaço utilizável total no disco lógico que é gratuito.
    - Limiar: Menos de 20% é marcado como insalubre.

- LogicalDisk(C:) \\ Avg. Comprimento da fila do disco:

    - Representa condições do sistema de armazenamento.
    - Limiar: Superior a 5 é marcado como insalubre.

- Memória( \* ) \\ Mbytes disponíveis:

    - A memória disponível para o sistema.
    - Limiar: Menos de 500 megabytes marcados como insalubres.

- Informação do \* \\ processador: Tempo de processador:

    - Limiar: Mais de 80% é marcado como insalubre.

- [Atraso de entrada \* do \\ utilizador por sessão.](/windows-server/remote/remote-desktop-services/rds-rdsh-performance-counters/)

    - Limiar: Mais de 2000 ms é marcado como insalubre.

## <a name="next-steps"></a>Passos seguintes

- Saiba como monitorizar os registos de atividade no [Use diagnostics com Log Analytics](diagnostics-log-analytics-2019.md).
- Leia sobre cenários de erro comuns e como corrigi-los em [Identificar e diagnosticar problemas](diagnostics-role-service-2019.md).