---
title: Introdução ao Azure Log Integration | Documentos da Microsoft
description: Saiba como instalar o serviço de integração de registos do Azure e integrar registos do armazenamento do Azure, os registos de auditoria do Azure e alertas do Centro de segurança do Azure.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 01/14/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 7e43af7d749719c2f69df9b53766c5452931884b
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57542918"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integração de registos do Azure com o registo de diagnósticos do Azure e o reencaminhamento de eventos do Windows


>[!IMPORTANT]
> A funcionalidade de integração de registo do Azure vai ser preterida até 06/01/2019. Downloads de AzLog foram desativados a 27 de Junho de 2018. Para obter orientações sobre o que fazer a postagem de revisão de encaminhamento de movimentação [monitor do Azure de utilização para integração com ferramentas SIEM](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Só deve utilizar a integração de registos do Azure se uma [do Azure Monitor](../monitoring-and-diagnostics/monitoring-get-started.md) conector não está disponível a partir do fornecedor de incidente de segurança e gestão de eventos (SIEM).

Integração de registos do Azure disponibiliza registos do Azure para o SIEM para que possa criar um dashboard de segurança unificada para todos os seus ativos.
Para obter mais informações sobre o estado de um conector do Azure Monitor, contacte o fornecedor SIEM.

> [!IMPORTANT]
> Se o seu interesse principal está a recolher registos de máquina virtual, a maioria dos fornecedores SIEM incluir esta opção nas suas soluções. Usando o SIEM conector do fornecedor é sempre a alternativa preferida.

Este artigo ajuda-o a começar com o Azure Log Integration. Concentra-se sobre como instalar o serviço de integração de registos do Azure e integrar o serviço com o diagnóstico do Azure. O serviço de integração de registos do Azure, em seguida, recolhe informações de registo de eventos do Windows do canal de eventos de segurança do Windows a partir de máquinas virtuais implementadas numa infraestrutura do Azure como um serviço. Isto é semelhante à *reencaminhamento de eventos* que pode usar num sistema no local.

> [!NOTE]
> Integrar a saída de integração de registos do Azure com um SIEM é efetuada pelo próprio SIEM. Para obter mais informações, consulte [integrar o Azure Log Integration com o seu SIEM no local](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

O serviço de integração de registos do Azure é executado em física ou um computador virtual com o Windows Server 2008 R2 ou posterior (Windows Server 2016 ou Windows Server 2012 R2 é preferível).

Um computador físico pode ser executado no local ou num site de hospedagem. Se optar por executar o serviço de integração de registos do Azure numa máquina virtual, a máquina virtual pode estar localizada no local ou numa nuvem pública, como no Microsoft Azure.

A máquina física ou virtual a executar o serviço de integração de registos do Azure necessita de conectividade de rede para a cloud pública do Azure. Este artigo fornece detalhes sobre a configuração necessária.

## <a name="prerequisites"></a>Pré-requisitos

No mínimo, a instalar o Azure Log Integration requer os seguintes itens:

* Uma **subscrição do Azure**. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* R **conta de armazenamento** que podem ser utilizados para o Windows Azure Diagnostics (WAD) registo. Pode utilizar uma conta de armazenamento pré-configuradas ou criar uma nova conta de armazenamento. Neste artigo, descrevemos como configurar a conta de armazenamento.

  > [!NOTE]
  > Dependendo do seu cenário, uma conta de armazenamento poderá não ser necessária. Para o cenário de diagnóstico do Azure abordado neste artigo, é necessária uma conta de armazenamento.

* **Dois sistemas**: 
  * Uma máquina que executa o serviço de integração de registos do Azure. Esta máquina recolhe todas as informações de registo que mais tarde, são importadas para o SIEM. Este sistema:
    * Pode ser local ou alojado no Microsoft Azure.  
    * Tem de executar um x64 versão do Windows Server 2008 R2 SP1 ou posterior, e ter o Microsoft .NET 4.5.1 instalado. Para determinar a versão do .NET instalada, veja [determinar quais versões do .NET Framework estão instaladas](https://msdn.microsoft.com/library/hh925568).  
    * Tem de ter conectividade com a conta de armazenamento do Azure que utilizou para o registo de diagnóstico do Azure. Neste artigo, descrevemos como confirmar a conectividade.
  * Uma máquina que pretende monitorizar. Trata-se uma VM em execução como um [máquina virtual do Azure](../virtual-machines/virtual-machines-windows-overview.md). As informações de registo desta máquina são enviadas para a máquina do serviço de integração de registos do Azure.

Para uma rápida demonstração de como criar uma máquina virtual com o portal do Azure, veja o vídeo seguinte:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Considerações sobre implementação

Durante os testes, pode usar qualquer sistema que cumpra os requisitos mínimos do sistema operativo. Para um ambiente de produção, a carga pode necessitar de-o a planear para aumentar ou reduzir horizontalmente.

Pode executar várias instâncias do serviço de integração de registos do Azure. No entanto, pode executar apenas uma instância do serviço por máquina física ou virtual. Além disso, pode contas de armazenamento do diagnóstico do Azure de balanceamento de carga para WAD. O número de subscrições para fornecer às instâncias baseia-se a sua capacidade.

> [!NOTE]
> Atualmente, não temos recomendações específicas sobre o quando aumentar horizontalmente as instâncias de máquinas do Azure Log Integration (ou seja, máquinas que executa o serviço de integração de registos do Azure) ou para contas de armazenamento ou subscrições. Tome decisões de dimensionamento com base na sua observações sobre o desempenho em cada uma dessas áreas.

Para ajudar a melhorar o desempenho, tem também a opção para aumentar verticalmente o serviço de integração de registos do Azure. As métricas de desempenho seguintes podem ajudar a dimensionar as máquinas que optar por executar o serviço de integração de registos do Azure:

* Numa máquina de processador de 8 (núcleos), uma única instância do Azure Log Integration consegue processar cerca de 24 milhões de eventos por dia (cerca de 1 milhão de eventos por hora).
* Numa máquina de processador de 4 (núcleos), uma única instância do Azure Log Integration consegue processar cerca de 1,5 milhões de eventos por dia (aproximadamente 62,500 eventos por hora).

## <a name="install-azure-log-integration"></a>Instalar a integração de registos do Azure

Percorrer o conjunto de rotina de instalação. Escolha se pretende fornecer informações de telemetria para a Microsoft.

O serviço de integração de registos do Azure recolhe dados telemétricos a partir do computador no qual está instalado.  

Os dados de telemetria que são recolhidos incluem o seguinte:

* Exceções que ocorrem durante a execução de integração de registos do Azure.
* Métricas sobre o número de consultas e os eventos processados.
* Estatísticas sobre quais Azlog.exe Opções da linha de comandos são utilizadas. 

> [!NOTE]
> Recomendamos que permite que a Microsoft recolha dados de telemetria. Pode desativar a recolha de dados de telemetria ao desmarcar a **permitir que a Microsoft para recolher dados telemétricos** caixa de verificação.
>

![Captura de ecrã do painel de instalação, com a caixa de verificação de telemetria selecionada](./media/security-azure-log-integration-get-started/telemetry.png)


O processo de instalação encontra-se no vídeo seguinte:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Passos de pós-instalação e validação

Depois de concluir a configuração básica, está pronto para executar etapas pós-instalação e validação:

1. Abra o PowerShell como um Administrador. Em seguida, vá para C:\Program Files\Microsoft Azure Log Integration.
2. Importe os cmdlets de integração de registos do Azure. Para importar os cmdlets, execute o script `LoadAzlogModule.ps1`. ENTER `.\LoadAzlogModule.ps1`, e, em seguida, prima Enter (Observe o uso de **.\\**  neste comando). Deverá ver algo como o que é apresentado na figura a seguir:

  ![Captura de ecrã da saída do comando LoadAzlogModule.ps1](./media/security-azure-log-integration-get-started/loaded-modules.png)
3. Em seguida, configure a integração de registos do Azure para utilizar um ambiente do Azure específico. Uma *ambiente do Azure* é o tipo de centro de dados de nuvem do Azure que pretende trabalhar com. Embora existam vários ambientes do Azure, atualmente, as opções relevantes são **AzureCloud** ou **AzureUSGovernment**. A executar o PowerShell como administrador, certifique-se de que está em C:\Program Files\Microsoft Azure Log Integration\. Em seguida, execute este comando:

  `Set-AzlogAzureEnvironment -Name AzureCloud` (para **AzureCloud**)
  
  Se pretender utilizar a cloud do Azure de Governo dos E.u.a., utilize **AzureUSGovernment** para o **-nome** variável. Atualmente, não são suportadas outras clouds do Azure.  

  > [!NOTE]
  > Não receber comentários quando o comando for concluída com êxito. 

4. Antes de poder monitorizar um sistema, precisa do nome da conta de armazenamento que é utilizada para diagnóstico do Azure. No portal do Azure, aceda a **máquinas virtuais**. Procure por uma máquina virtual do Windows que irá monitorizar. Na **propriedades** secção, selecione **das definições de diagnóstico**.  Em seguida, selecione **agente**. Anote o nome da conta de armazenamento especificada. Precisa este nome de conta para um passo posterior.

  ![Captura de ecrã do painel de definições de diagnóstico do Azure](./media/security-azure-log-integration-get-started/storage-account-large.png) 

  ![Captura de ecrã de ativar botão de monitorização de ao nível do convidado](./media/security-azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

  > [!NOTE]
  > Se a monitorização não foi ativada quando a máquina virtual foi criada, pode ativá-la conforme mostrado na imagem anterior.

5. Agora, volte para a máquina de integração de registos do Azure. Certifique-se de que tem conectividade à conta de armazenamento do sistema onde instalou a integração de registos do Azure. O computador que executa o serviço de integração de registos do Azure precisa de aceder à conta de armazenamento para obter informações de diagnóstico do Azure, são registadas em cada um dos sistemas monitorados. Para verificar a conectividade: 
  1. [Transfira o Explorador de armazenamento do Azure](http://storageexplorer.com/).
  2. Concluir a configuração.
  3. Quando a instalação estiver concluída, selecione **seguinte**. Deixe o **iniciar o Explorador de armazenamento do Microsoft Azure** caixa de verificação selecionada.  
  4. Inicie sessão no Azure.
  5. Certifique-se de que pode ver a conta de armazenamento que configurou para o diagnóstico do Azure: 

   ![Captura de ecrã das contas de armazenamento no Explorador de armazenamento](./media/security-azure-log-integration-get-started/storage-explorer.png)

  6. Algumas opções são apresentados em contas de armazenamento. Sob **tabelas**, deverá ver uma tabela chamada **WADWindowsEventLogsTable**.

  Se a monitorização não foi ativada quando a máquina virtual foi criada, pode ativá-la, conforme descrito anteriormente.


## <a name="integrate-windows-vm-logs"></a>Integrar registos VM do Windows

Neste passo, irá configurar a máquina que executa o serviço de integração de registos do Azure para ligar à conta de armazenamento que contém os ficheiros de registo.

Para concluir este passo, precisa de algumas coisas:  
* **FriendlyNameForSource**: Um nome amigável que pode aplicar à conta de armazenamento que configurou para a máquina virtual armazenar informações de diagnóstico do Azure.
* **StorageAccountName**: O nome da conta de armazenamento que especificou quando configurou o diagnóstico do Azure.  
* **StorageKey**: A chave de armazenamento para a conta de armazenamento onde as informações de diagnóstico do Azure são armazenadas para esta máquina virtual.  

Para obter a chave de armazenamento, conclua os seguintes passos:
1. Aceda ao [Portal do Azure](http://portal.azure.com).
2. No painel de navegação, selecione **todos os serviços**.
3. Na **filtro** , introduza **armazenamento**. Em seguida, selecione **contas de armazenamento**.

  ![Captura de ecrã que mostra as contas de armazenamento em todos os serviços](./media/security-azure-log-integration-get-started/filter.png)

4. É apresentada uma lista de contas de armazenamento. Faça duplo clique a conta de que atribuiu para iniciar sessão de armazenamento.

  ![Captura de ecrã que mostra uma lista de contas de armazenamento](./media/security-azure-log-integration-get-started/storage-accounts.png)

5. Em **Definições**, selecione **Chaves de acesso**.

  ![Captura de ecrã que mostra a opção de chaves de acesso no menu](./media/security-azure-log-integration-get-started/storage-account-access-keys.png)

6. Cópia **chave1**e, em seguida, guarde-o numa localização segura que tem acesso para o passo seguinte.
7. No servidor onde instalou a integração de registos do Azure, abra uma janela de linha de comandos como administrador. (Certifique-se de que abra uma janela de linha de comandos como administrador e não o PowerShell).
8. Vá para C:\Program Files\Microsoft Azure Log Integration.
9. Execute este comando: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
  Exemplo:
  
  `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

  Se pretender que o ID de subscrição sejam apresentados de evento XML, acrescente o ID de subscrição para o nome amigável:

  `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
  Exemplo:
  
  `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Aguarde até 60 minutos e, em seguida, veja os eventos que são extraídos da conta de armazenamento. Para ver os eventos, na integração de registos do Azure, selecione **Visualizador de eventos** > **Windows registos** > **eventos reencaminhados**.

O vídeo seguinte aborda os passos anteriores:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Se os dados não estão a aparecer na pasta eventos reencaminhados
Se os dados não estão a aparecer na pasta eventos reencaminhados após uma hora, conclua estes passos:

1. Verifique a máquina que está a executar o serviço de integração de registos do Azure. Certifique-se que ele pode aceder ao Azure. Para testar a conectividade, num browser, experimente ir para o [portal do Azure](https://portal.azure.com).
2. Certifique-se de que a conta de utilizador Azlog tem permissão de escrita para a pasta users\Azlog.
  1. Abra o Explorador de Ficheiros.
  2. Vá para c:\Users.
  3. Com o botão direito C:\users\Azlog.
  4. Selecione **segurança**.
  5. Selecione **NT Service\Azlog**. Verifique as permissões para a conta. Se a conta está em falta a partir deste separador, ou se não apresentarem as permissões adequadas, pode conceder as permissões de conta neste separador.
3. Quando executa o comando `Azlog source list`, certifique-se de que a conta de armazenamento que foi adicionado no comando `Azlog source add` é listado no resultado.
4. Para ver se algum erro forem comunicado a partir do serviço de integração de registos do Azure, aceda à **Visualizador de eventos** > **Windows registos** > **aplicação**.

Caso se depare com quaisquer problemas durante a instalação e configuração, pode criar uma [pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md). Para o serviço, selecione **integração de registos**.

Outra opção de suporte é a [fórum MSDN do Azure Log Integration](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). No fórum do MSDN, a Comunidade pode fornecer suporte ao responder às perguntas e compartilhar dicas e truques sobre como obter o máximo partido da integração de registos do Azure. A equipa de integração de registos do Azure também monitora neste fórum. Eles ajudam a sempre que podem.

## <a name="integrate-azure-activity-logs"></a>Integrar registos de atividades do Azure

O registo de atividades do Azure é um registo de subscrição que fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. Isto inclui uma variedade de dados de dados operacionais do Azure Resource Manager para as atualizações em eventos de estado de funcionamento do serviço. Alertas do Centro de segurança do Azure também estão incluídos neste registo.
> [!NOTE]
> Antes de tentar os passos neste artigo, tem de rever o [começar](security-azure-log-integration-get-started.md) artigo e conclua os passos aqui.

### <a name="steps-to-integrate-azure-activity-logs"></a>Passos para integrar registos de atividades do Azure

1. Abra a linha de comandos e execute este comando:  ```cd c:\Program Files\Microsoft Azure Log Integration```
2. Execute este comando:  ```azlog createazureid```

    Este comando pede-lhe o início de sessão do Azure. O comando, em seguida, cria um Azure Active Directory principal de serviço em inquilinos do Azure AD que alojam as subscrições do Azure em que o utilizador com sessão iniciada é um administrador, coadministrador ou um proprietário. O comando irá falhar se o utilizador com sessão iniciada é apenas um utilizador convidado no inquilino do Azure AD. Autenticação para o Azure é feita através do Azure AD. Criar um principal de serviço para o Azure Log Integration cria a identidade do Azure AD que é dado acesso ao ler a partir de subscrições do Azure.
3.  Execute o seguinte comando para autorizar o principal de serviço do Azure Log Integration criado no acesso do passo anterior para a leitura do registo de atividades para a subscrição. Tem de ser um proprietário da subscrição para executar o comando.

    ```Azlog.exe authorize subscriptionId``` Exemplo:

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4.  Verifique as seguintes pastas para confirmar que os ficheiros JSON de registo de auditoria do Azure Active Directory são criados nos mesmos:
    - C:\Users\azlog\AzureResourceManagerJson
    - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Para obter instruções específicas sobre trazer as informações dos ficheiros JSON para suas informações de segurança e o sistema de gestão (SIEM) de eventos, contacte o fornecedor SIEM.

Assistência de Comunidade está disponível através da [fórum MSDN do Azure Log Integration](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na Comunidade do Azure Log Integration para suportar entre si com perguntas, respostas, dicas e truques. Além disso, a equipa de integração de registos do Azure monitoriza este Fórum e ajuda a sempre que possível.

Também pode abrir um [pedido de suporte](../azure-supportability/how-to-create-azure-support-request.md). Selecione a integração de registos que o serviço para o qual está a solicitar suporte.

## <a name="next-steps"></a>Passos Seguintes

Para saber mais sobre a integração de registo do Azure, veja os artigos seguintes: Antes de tentar os passos neste artigo, tem de rever o artigo de introdução de Get e conclua os passos aqui.

* [Introdução à integração de registos do Azure](security-azure-log-integration-overview.md). Este artigo apresenta o Azure Log Integration, suas principais capacidades e como ele funciona.
* [Passos de configuração de parceiros](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Esta postagem de blog mostra-lhe como configurar a integração de registos do Azure para trabalhar com soluções de parceiros Splunk e cso da HP e IBM QRadar. Ele descreve a nossa documentação de orientação atual sobre como configurar os componentes SIEM. Verifique junto do fornecedor do SIEM para obter mais detalhes.
* [Integração de registos do Azure perguntas mais frequentes (FAQ)](security-azure-log-integration-faq.md). Encontre respostas para perguntas comuns sobre a integração de registos do Azure.
* [Integrar alertas do Centro de segurança do Azure com o Azure Log Integration](../security-center/security-center-integrating-alerts-with-log-integration.md). Este artigo mostra-lhe como sincronizar alertas do Centro de segurança e eventos de segurança de máquinas virtuais que são recolhidos pelo diagnóstico do Azure e a atividade do Azure registos. Sincronizar os registos ao utilizar a solução de SIEM ou registos do Azure Monitor.
* [Registos de auditoria de novas funcionalidades para o diagnóstico do Azure e Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Esta postagem de blog apresenta os registos de auditoria do Azure e outros recursos que podem ajudá-lo a obterem informações sobre as operações de recursos do Azure.
