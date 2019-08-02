---
title: Introdução à integração de logs do Azure | Microsoft Docs
description: Saiba como instalar o serviço de integração de logs do Azure e integrar logs do armazenamento do Azure, logs de auditoria do Azure e alertas da central de segurança do Azure.
services: security
documentationcenter: na
author: Barclayn
manager: barbkess
editor: TomShinder
ms.assetid: 53f67a7c-7e17-4c19-ac5c-a43fabff70e1
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ums.workload: na
ms.date: 05/28/2019
ms.author: barclayn
ms.custom: azlog
ms.openlocfilehash: 861a72b6abdc3d06cc4737630c92d34eee2e8545
ms.sourcegitcommit: 85b3973b104111f536dc5eccf8026749084d8789
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/01/2019
ms.locfileid: "68727677"
---
# <a name="azure-log-integration-with-azure-diagnostics-logging-and-windows-event-forwarding"></a>Integração de log do Azure com log de Diagnóstico do Azure e encaminhamento de eventos do Windows


>[!IMPORTANT]
> O recurso integração de logs do Azure será preterido por 06/15/2019. Os downloads do AzLog foram desabilitados em 27 de junho de 2018. Para obter diretrizes sobre o que fazer ao mover para frente examine o [Azure monitor de envio para integração com ferramentas Siem](https://azure.microsoft.com/blog/use-azure-monitor-to-integrate-with-siem-tools/) 

Você só deve usar a integração de log do Azure se um conector de [Azure monitor](/azure/monitoring-and-diagnostics/monitoring-get-started) não estiver disponível no fornecedor de Siem (gerenciamento de eventos e incidentes de segurança).

A integração de logs do Azure disponibiliza os logs do Azure para o SIEM, para que você possa criar um painel de segurança unificado para todos os seus ativos.
Para obter mais informações sobre o status de um conector de Azure Monitor, entre em contato com o fornecedor do SIEM.

> [!IMPORTANT]
> Se seu interesse principal é coletar logs de máquina virtual, a maioria dos fornecedores de SIEM inclui essa opção em sua solução. Usar o conector do fornecedor do SIEM é sempre a alternativa preferida.

Este artigo ajuda você a começar a usar a integração de log do Azure. Ele se concentra na instalação do serviço de integração de logs do Azure e na integração do serviço com o Diagnóstico do Azure. Em seguida, o serviço de integração de log do Azure coleta informações do log de eventos do Windows do canal de eventos de segurança do Windows de máquinas virtuais implantadas em uma infraestrutura do Azure como um serviço. Isso é semelhante ao *encaminhamento de eventos* que você pode usar em um sistema local.

> [!NOTE]
> A integração da saída da integração de log do Azure com um SIEM é feita pelo próprio SIEM. Para obter mais informações, consulte [integrar a integração de log do Azure com o Siem local](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/).

O serviço de integração de log do Azure é executado em um computador físico ou virtual que executa o Windows Server 2008 R2 ou posterior (o Windows Server 2016 ou o Windows Server 2012 R2 é preferencial).

Um computador físico pode ser executado localmente ou em um site de hospedagem. Se você optar por executar o serviço de integração de logs do Azure em uma máquina virtual, a máquina virtual poderá ser localizada localmente ou em uma nuvem pública, como no Microsoft Azure.

A máquina virtual ou física que executa o serviço de integração de logs do Azure requer conectividade de rede para a nuvem pública do Azure. Este artigo fornece detalhes sobre a configuração necessária.

## <a name="prerequisites"></a>Pré-requisitos

No mínimo, a instalação da integração de logs do Azure requer os seguintes itens:

* Uma **subscrição do Azure**. Se não tiver uma, pode inscrever-se numa [conta gratuita](https://azure.microsoft.com/free/).
* Uma **conta de armazenamento** que pode ser usada para registro em log do Windows diagnóstico do Azure (wad). Você pode usar uma conta de armazenamento pré-configurada ou criar uma nova conta de armazenamento. Posteriormente neste artigo, descreveremos como configurar a conta de armazenamento.

  > [!NOTE]
  > Dependendo do seu cenário, uma conta de armazenamento pode não ser necessária. Para o cenário de Diagnóstico do Azure abordado neste artigo, é necessária uma conta de armazenamento.

* **Dois sistemas**: 
  * Um computador que executa o serviço de integração de logs do Azure. Este computador coleta todas as informações de log que posteriormente são importadas para o SIEM. Este sistema:
    * Pode ser local ou hospedado em Microsoft Azure.  
    * Deve estar executando uma versão x64 do Windows Server 2008 R2 SP1 ou posterior e ter o Microsoft .NET 4.5.1 instalado. Para determinar a versão do .NET instalada, consulte [determinar quais versões do .NET Framework estão instaladas](https://msdn.microsoft.com/library/hh925568).  
    * Deve ter conectividade com a conta de armazenamento do Azure que é usada para Diagnóstico do Azure log. Posteriormente neste artigo, descreveremos como confirmar a conectividade.
  * Um computador que você deseja monitorar. Esta é uma VM em execução como uma [máquina virtual do Azure](/azure/virtual-machines/virtual-machines-windows-overview). As informações de log deste computador são enviadas para o computador do serviço de integração de logs do Azure.

Para ver uma demonstração rápida de como criar uma máquina virtual usando o portal do Azure, veja o vídeo a seguir:<br /><br />


> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Create-a-Virtual-Machine/player]

## <a name="deployment-considerations"></a>Considerações sobre implementação

Durante o teste, você pode usar qualquer sistema que atenda aos requisitos mínimos do sistema operacional. Para um ambiente de produção, a carga pode exigir que você planeje o dimensionamento ou a expansão.

Você pode executar várias instâncias do serviço de integração de logs do Azure. No entanto, você pode executar apenas uma instância do serviço por máquina virtual ou física. Além disso, você pode balancear a carga de Diagnóstico do Azure contas de armazenamento para WAD. O número de assinaturas a serem fornecidas às instâncias é baseado em sua capacidade.

> [!NOTE]
> Atualmente, não temos recomendações específicas sobre quando expandir instâncias de computadores de integração de log do Azure (ou seja, computadores que executam o serviço de integração de log do Azure) ou para contas de armazenamento ou assinaturas. Tome decisões de dimensionamento com base em suas observações de desempenho em cada uma dessas áreas.

Para ajudar a melhorar o desempenho, você também tem a opção de escalar verticalmente o serviço de integração de logs do Azure. As métricas de desempenho a seguir podem ajudá-lo a dimensionar os computadores que você escolher para executar o serviço de integração de logs do Azure:

* Em um computador com 8 processadores (núcleo), uma única instância da integração de log do Azure pode processar cerca de 24 milhões eventos por dia (aproximadamente 1 milhão eventos por hora).
* Em um computador com 4 processadores (núcleo), uma única instância da integração de log do Azure pode processar cerca de 1,5 milhões eventos por dia (aproximadamente 62.500 eventos por hora).

## <a name="install-azure-log-integration"></a>Instalar a integração de log do Azure

Execute a rotina de configuração. Escolha se deseja fornecer informações de telemetria à Microsoft.

O serviço de integração de logs do Azure coleta dados de telemetria do computador no qual ele está instalado.  

Os dados de telemetria coletados incluem o seguinte:

* Exceções que ocorrem durante a execução da integração de log do Azure.
* Métricas sobre o número de consultas e eventos processados.
* Estatísticas sobre quais opções de linha de comando do Azlog. exe são usadas. 

> [!NOTE]
> Recomendamos que a Microsoft colete dados de telemetria. Você pode desativar a coleta de dados de telemetria desmarcando a caixa de seleção **permitir que a Microsoft colete dados de telemetria** .
>

![Captura de tela do painel de instalação, com a caixa de seleção telemetria selecionada](./media/azure-log-integration-get-started/telemetry.png)


O processo de instalação é abordado no vídeo a seguir:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Install-Azure-Log-Integration/player]

## <a name="post-installation-and-validation-steps"></a>Etapas pós-instalação e validação

Depois de concluir a configuração básica, você estará pronto para executar as etapas de pós-instalação e validação:

1. Abra o PowerShell como um Administrador. Em seguida, vá para C:\Program Files\Microsoft Azure log Integration.
2. Importe os cmdlets de integração de log do Azure. Para importar os cmdlets, execute o script `LoadAzlogModule.ps1`. Digite `.\LoadAzlogModule.ps1`e pressione Enter (Observe o uso de **.\\**  nesse comando). Você deve ver algo como o que aparece na figura a seguir:

   ![Captura de tela da saída do comando LoadAzlogModule. ps1](./media/azure-log-integration-get-started/loaded-modules.png)
3. Em seguida, configure a integração de log do Azure para usar um ambiente específico do Azure. Um *ambiente do Azure* é o tipo de datacenter de nuvem do Azure com o qual você deseja trabalhar. Embora existam vários ambientes do Azure, atualmente, as opções relevantes são **AzureCloud** ou **AzureUSGovernment**. Executando o PowerShell como administrador, verifique se você está em C:\Program Files\Microsoft Azure log Integration\. Em seguida, execute este comando:

   `Set-AzlogAzureEnvironment -Name AzureCloud`(para **AzureCloud**)
  
   Se você quiser usar a nuvem do Azure do governo dos EUA, use **AzureUSGovernment** para a variável **-Name** . Atualmente, não há suporte para outras nuvens do Azure.  

   > [!NOTE]
   > Você não recebe comentários quando o comando é executado com sucesso. 

4. Para poder monitorar um sistema, você precisa do nome da conta de armazenamento usada para Diagnóstico do Azure. Na portal do Azure, vá para **máquinas virtuais**. Procure uma máquina virtual do Windows que será monitorada. Na seção **Propriedades** , selecione **configurações de diagnóstico**.  Em seguida, selecione **agente**. Anote o nome da conta de armazenamento que é especificado. Você precisará desse nome de conta para uma etapa posterior.

   ![Captura de tela do painel configurações de Diagnóstico do Azure](./media/azure-log-integration-get-started/storage-account-large.png) 

   ![Captura de tela do botão habilitar monitoramento em nível de convidado](./media/azure-log-integration-get-started/azure-monitoring-not-enabled-large.png)

   > [!NOTE]
   > Se o monitoramento não tiver sido habilitado quando a máquina virtual foi criada, você poderá habilitá-la conforme mostrado na imagem anterior.

5. Agora, volte para o computador de integração de log do Azure. Verifique se você tem conectividade com a conta de armazenamento do sistema em que você instalou a integração de log do Azure. O computador que executa o serviço de integração de logs do Azure precisa acessar a conta de armazenamento para recuperar as informações registradas pelo Diagnóstico do Azure em cada um dos sistemas monitorados. Para verificar a conectividade: 
   1. [Baixar Gerenciador de armazenamento do Azure](https://storageexplorer.com/).
   2. Conclua a instalação.
   3. Quando a instalação for concluída, selecione **Avançar**. Deixe a caixa de seleção **iniciar Gerenciador de armazenamento do Microsoft Azure** marcada.  
   4. Inicie sessão no Azure.
   5. Verifique se você pode ver a conta de armazenamento que você configurou para Diagnóstico do Azure: 

   ![Captura de tela de contas de armazenamento no Gerenciador de Armazenamento](./media/azure-log-integration-get-started/storage-explorer.png)

   1. Algumas opções aparecem em contas de armazenamento. Em **tabelas**, você deve ver uma tabela chamada **WADWindowsEventLogsTable**.

   Se o monitoramento não tiver sido habilitado quando a máquina virtual foi criada, você poderá habilitá-la, conforme descrito anteriormente.


## <a name="integrate-windows-vm-logs"></a>Integrar logs de VM do Windows

Nesta etapa, você configura o computador que executa o serviço de integração de logs do Azure para se conectar à conta de armazenamento que contém os arquivos de log.

Para concluir esta etapa, você precisa de algumas coisas:  
* **FriendlyNameForSource**: Um nome amigável que você pode aplicar à conta de armazenamento que você configurou para a máquina virtual para armazenar informações de Diagnóstico do Azure.
* **StorageAccountName**: O nome da conta de armazenamento que você especificou quando configurou o Diagnóstico do Azure.  
* **StorageKey**: A chave de armazenamento para a conta de armazenamento em que as informações de Diagnóstico do Azure são armazenadas para essa máquina virtual.  

Para obter a chave de armazenamento, conclua as seguintes etapas:
1. Aceda ao [Portal do Azure](https://portal.azure.com).
2. No painel de navegação, selecione **todos os serviços**.
3. Na caixa de **filtro** , insira **armazenamento**. Em seguida, selecione **contas de armazenamento**.

   ![Captura de tela que mostra as contas de armazenamento em todos os serviços](./media/azure-log-integration-get-started/filter.png)

4. Uma lista de contas de armazenamento é exibida. Clique duas vezes na conta que você atribuiu ao armazenamento de log.

   ![Captura de tela que mostra uma lista de contas de armazenamento](./media/azure-log-integration-get-started/storage-accounts.png)

5. Em **Definições**, selecione **Chaves de acesso**.

   ![Captura de tela que mostra a opção chaves de acesso no menu](./media/azure-log-integration-get-started/storage-account-access-keys.png)

6. Copie **key1**e, em seguida, salve-o em um local seguro que você possa acessar para a etapa a seguir.
7. No servidor em que você instalou a integração de log do Azure, abra uma janela de prompt de comando como administrador. (Não se esqueça de abrir uma janela de prompt de comando como administrador e não PowerShell).
8. Vá para C:\Program Files\Microsoft Azure log Integration.
9. Execute este comando: `Azlog source add <FriendlyNameForTheSource> WAD <StorageAccountName> <StorageKey>`.
 
   Exemplo:
  
   `Azlog source add Azlogtest WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

   Se você quiser que a ID da assinatura apareça no XML do evento, acrescente a ID da assinatura ao nome amigável:

   `Azlog source add <FriendlyNameForTheSource>.<SubscriptionID> WAD <StorageAccountName> <StorageKey>`
  
   Exemplo:
  
   `Azlog source add Azlogtest.YourSubscriptionID WAD Azlog9414 fxxxFxxxxxxxxywoEJK2xxxxxxxxxixxxJ+xVJx6m/X5SQDYc4Wpjpli9S9Mm+vXS2RVYtp1mes0t9H5cuqXEw==`

> [!NOTE]
> Aguarde até 60 minutos e exiba os eventos extraídos da conta de armazenamento. Para exibir os eventos, na integração de log do Azure, selecione **Visualizador de eventos** > **eventos encaminhados**de**logs** > do Windows.

O vídeo a seguir aborda as etapas anteriores:<br /><br />

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-Security-Videos/Azure-Log-Integration-Videos-Enable-Diagnostics-and-Storage/player]


## <a name="if-data-isnt-showing-up-in-the-forwarded-events-folder"></a>Se os dados não estiverem aparecendo na pasta eventos encaminhados
Se os dados não estiverem aparecendo na pasta eventos encaminhados após uma hora, conclua estas etapas:

1. Verifique o computador que está executando o serviço de integração de logs do Azure. Confirme se ele pode acessar o Azure. Para testar a conectividade, em um navegador, tente ir para a [portal do Azure](https://portal.azure.com).
2. Verifique se a conta de usuário Azlog tem permissão de gravação para a pasta users\Azlog.
   1. Abra o Explorador de Ficheiros.
   2. Vá para C:\Users.
   3. Clique com o botão direito do mouse em C:\users\Azlog.
   4. Selecione **segurança**.
   5. Selecione **NT Service\Azlog**. Verifique as permissões para a conta. Se a conta estiver ausente nessa guia ou se as permissões apropriadas não estiverem sendo exibidas, você poderá conceder as permissões de conta nessa guia.
3. Quando você executar o comando `Azlog source list`, verifique se a conta de armazenamento que foi adicionada no comando `Azlog source add` está listada na saída.
4. Para ver se algum erro é relatado do serviço de integração de log do Azure, acesse **Visualizador de eventos** > **aplicativo**de**logs** > do Windows.

Se você tiver problemas durante a instalação e a configuração, poderá criar uma [solicitação de suporte](../../azure-supportability/how-to-create-azure-support-request.md). Para o serviço, selecione **integração de log**.

Outra opção de suporte é o [Fórum do MSDN integração de logs do Azure](https://social.msdn.microsoft.com/Forums/home?forum=AzureLogIntegration). No fórum do MSDN, a Comunidade pode oferecer suporte respondendo a perguntas e compartilhando dicas e truques sobre como obter o máximo da integração de logs do Azure. A equipe de integração de logs do Azure também monitora esse fórum. Eles ajudam sempre que puderem.

## <a name="integrate-azure-activity-logs"></a>Integrar logs de atividades do Azure

O registo de atividades do Azure é um registo de subscrição que fornece informações sobre os eventos de nível de assinatura que ocorreram no Azure. Isso inclui um intervalo de dados, de Azure Resource Manager dados operacionais a atualizações em eventos de integridade do serviço. Os alertas da central de segurança do Azure também estão incluídos nesse log.
> [!NOTE]
> Antes de tentar as etapas neste artigo, você deve examinar [o artigo de](azure-log-integration-get-started.md) introdução e concluir as etapas aqui.

### <a name="steps-to-integrate-azure-activity-logs"></a>Etapas para integrar os logs de atividades do Azure

1. Abra o prompt de comando e execute este comando:```cd c:\Program Files\Microsoft Azure Log Integration```
2. Execute este comando:```azlog createazureid```

    Esse comando solicita seu logon do Azure. O comando cria uma entidade de serviço Azure Active Directory nos locatários do Azure AD que hospedam as assinaturas do Azure nas quais o usuário conectado é um administrador, um coadministrador ou um proprietário. O comando falhará se o usuário conectado for apenas um usuário convidado no locatário do Azure AD. A autenticação para o Azure é feita por meio do Azure AD. A criação de uma entidade de serviço para a integração de log do Azure cria a identidade do Azure AD que recebe acesso para leitura de assinaturas do Azure.
3. Execute o seguinte comando para autorizar a entidade de serviço de integração de logs do Azure criada na etapa anterior acesso à leitura do log de atividades para a assinatura. Você precisa ser um proprietário na assinatura para executar o comando.

   ```Azlog.exe authorize subscriptionId```Exemplo

   ```AZLOG.exe authorize ba2c2367-d24b-4a32-17b5-4443234859```

4. Verifique as seguintes pastas para confirmar se os arquivos JSON do log de auditoria do Azure Active Directory foram criados neles:
   - C:\Users\azlog\AzureResourceManagerJson
   - C:\Users\azlog\AzureResourceManagerJsonLD

> [!NOTE]
> Para obter instruções específicas sobre como colocar as informações nos arquivos JSON em seu sistema SIEM (gerenciamento de eventos e informações de segurança), entre em contato com seu fornecedor SIEM.

A assistência da Comunidade está disponível por meio do [Fórum do MSDN integração de logs do Azure](https://social.msdn.microsoft.com/Forums/office/home?forum=AzureLogIntegration). Este fórum permite que as pessoas na Comunidade de integração de logs do Azure ofereçam suporte entre si com perguntas, respostas, dicas e truques. Além disso, a equipe de integração de logs do Azure monitora esse fórum e ajuda sempre que possível.

Você também pode abrir uma [solicitação de suporte](../../azure-supportability/how-to-create-azure-support-request.md). Selecione integração de log como o serviço para o qual você está solicitando suporte.

## <a name="next-steps"></a>Passos seguintes

Para saber mais sobre a integração de log do Azure, consulte os seguintes artigos: Antes de tentar as etapas neste artigo, você deve examinar o artigo de introdução e concluir as etapas aqui.

* [Introdução à integração de log do Azure](azure-log-integration-overview.md). Este artigo apresenta a integração de log do Azure, seus principais recursos e como ele funciona.
* [Etapas de configuração de parceiro](https://blogs.msdn.microsoft.com/azuresecurity/2016/08/23/azure-log-siem-configuration-steps/). Esta postagem de Blog mostra como configurar a integração de log do Azure para trabalhar com as soluções de parceiros Splunk, HP ArcSight e IBM QRadar. Ele descreve nossa orientação atual sobre como configurar os componentes do SIEM. Consulte o fornecedor do SIEM para obter detalhes adicionais.
* [Perguntas frequentes sobre a integração de logs do Azure](azure-log-integration-faq.md). Estas perguntas frequentes respondem a perguntas comuns sobre a integração de logs do Azure.
* [Integração dos alertas da central de segurança do Azure com a integração de log do Azure](/azure/security-center/security-center-integrating-alerts-with-log-integration). Este artigo mostra como sincronizar alertas da central de segurança e eventos de segurança de máquina virtual que são coletados por Diagnóstico do Azure e logs de atividades do Azure. Você sincroniza os logs usando seus logs de Azure Monitor ou a solução SIEM.
* [Novos recursos para diagnóstico do Azure e logs de auditoria do Azure](https://azure.microsoft.com/blog/new-features-for-azure-diagnostics-and-azure-audit-logs/). Esta postagem de blog apresenta os logs de auditoria do Azure e outros recursos que podem ajudá-lo a obter informações sobre as operações dos recursos do Azure.
