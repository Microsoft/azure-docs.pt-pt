---
título: Armazenar eventos Azure Media Services em Azure Log Analytics : Descrição dos Serviços Azure Media: Saiba como armazenar eventos da Azure Media Services em Azure Log Analytics.
serviços: media-services documentationcenter: 'author: IngridAtMicrosoft manager: femila editor: '' ms.service: media-services ms.workload: ms.topic: tutorial ms.date: 08/24/2020 ms.author: inhenkel
---

# <a name="tutorial-store-azure-media-services-events-in-azure-log-analytics"></a>Tutorial: Armazenar eventos da Azure Media Services em Azure Log Analytics

## <a name="azure-media-services-events"></a>Eventos Azure Media Services

Azure Media Services v3 emite eventos na [Azure Event Grid](media-services-event-schemas.md). Pode subscrever eventos de várias maneiras e armazená-los em lojas de dados. Neste tutorial, irá subscrever eventos de Serviços de Mídia utilizando um [Fluxo de Aplicações de Registo.](https://azure.microsoft.com/services/logic-apps/) A Aplicação Lógica será ativada para cada evento e armazenará o corpo do evento no Azure Log Analytics. Uma vez que os eventos estejam no Azure Log Analytics, você pode usar outros serviços Azure para criar um dashboard, monitor e alerta sobre estes eventos, embora não estejamos cobrindo isso neste tutorial.

> [!NOTE]
> Seria útil se já estivesse familiarizado com o uso do FFmpeg como codificadora no local.  Se não, tudo bem. A linha de comando e as instruções para o streaming de um vídeo estão incluídas abaixo.

Vai aprender a:

> [!div class="checklist"]
> * Criar um fluxo de aplicativos de lógica sem código
> * Subscreva os tópicos do evento Azure Media Services
> * Parse eventos e armazenar para Azure Log Analytics
> * Eventos de consulta da Azure Log Analytics

Se não tiver uma subscrição do Azure, crie uma [conta gratuita](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) antes de começar.

## <a name="prerequisites"></a>Pré-requisitos

> * Uma [subscrição do Azure](how-to-set-azure-subscription.md)
> * Uma conta [de Serviços de Comunicação](create-account-howto.md) Social e um grupo de recursos.
> * Uma instalação de [FFmpeg](https://ffmpeg.org/download.html) para o seu SO.
> * Um espaço de trabalho [Log Analytics](../../azure-monitor/learn/quick-create-workspace.md)

## <a name="subscribe-to-a-media-services-event-with-logic-app"></a>Subscreva um evento de Serviços de Mídia com a Logic App

1. No portal Azure, se ainda não o fez, crie um espaço de trabalho [log Analytics.](../../azure-monitor/learn/quick-create-workspace.md) Você vai precisar do ID do espaço de trabalho e uma das chaves, então mantenha a janela do navegador aberta. Em seguida, abra o portal em outra aba ou janela.

1. Navegue na sua conta Azure Media Services e selecione **Eventos.** Isto mostrará todos os métodos para subscrever eventos da Azure Media Services.
    > [!div class="mx-imgBorder"]
    > ![Azure Media Services Portal](media/tutorial-events-log-analytics/select-events-01a.png)

1. Selecione o **ícone De Aplicações Lógicas** para criar uma App Lógica. Isto abrirá o Logic App Designer onde pode criar o fluxo para capturar os eventos e empurrá-los para o Log Analytics. 
    > [!div class="mx-imgBorder"]
    > ![Criar App Lógica](media/tutorial-events-log-analytics/select-logic-app-02.png)

1. Selecione o **ícone +,** selecione o inquilino que pretende utilizar e, em seguida, selecione Iniciar sôm. Verá um pedido de inscrição da Microsoft.
    > [!div class="mx-imgBorder"]
    > ![Ligue-se à grelha de eventos Azure ](media/tutorial-events-log-analytics/select-event-add-grid-03.png)
 ![ Selecione o inquilino](media/tutorial-events-log-analytics/select-tenant-03a.png)

1. **Selecione Continuar** a subscrever os Eventos de Serviços de Comunicação.
    > [!div class="mx-imgBorder"]
    > ![Conectado à grelha de eventos Azure](media/tutorial-events-log-analytics/select-continue-04.png)

1. Na lista **de tipos de recursos,** localize "Microsoft.Media.MediaServices".
    > [!div class="mx-imgBorder"]
    >![Eventos de Recursos de Recursos da Azure Media Services](media/tutorial-events-log-analytics/locate-azure-media-services-events-05.png)

1. Selecione o **item Tipo de Evento**. Haverá uma lista de todos os eventos que a Azure Media Services emite. Pode selecionar os eventos que pretende acompanhar. Pode adicionar vários tipos de eventos. (Mais tarde, irá fazer uma pequena alteração no fluxo de Aplicação Lógica para armazenar cada tipo de evento num Log Analytics Separado e propagar o nome do Tipo de Evento para o nome log Analytics'dynamicly.)
    > [!div class="mx-imgBorder"]
    > ![Tipo de evento de serviços de mídia Azure](media/tutorial-events-log-analytics/select-azure-media-services-event-type-06.png)

1. Selecione **Guardar Como**.

1. Dê um nome à sua App Lógica.  O grupo de recursos é selecionado por padrão. Deixe as outras definições como estão e, em seguida, **selecione Criar**.  Será devolvido ao ecrã principal do Azure.
    > [!div class="mx-imgBorder"]
    > ![Interface de nomeação de aplicativos lógicos](media/tutorial-events-log-analytics/give-logic-app-name-06a.png)

## <a name="create-an-action"></a>Create an action (Criar uma ação)

Agora que está inscrito no evento, crie uma ação.

1. Se o portal o levou de volta ao ecrã principal, volte à App Lógica que acabou de criar, procurando todos os recursos para obter o nome da aplicação.

1. Selecione a sua aplicação e, em seguida, selecione **o designer de aplicações Logic.** O painel de designers vai abrir.

1. Selecione **+ Novo Passo**.

1. Uma vez que pretende levar os eventos para o serviço Azure Log Analytics, procure "Azure Log Analytics" e selecione o "Azure Log Analytics Data Collector".
    > [!div class="mx-imgBorder"]
    > ![Recoletor de Dados do Azure Log Analytics](media/tutorial-events-log-analytics/select-azure-log-analytics-data-collector-07.png)

1. Para se ligar ao espaço de trabalho do Log Analytics, precisa do ID do espaço de trabalho e de uma chave de agente. Abra o portal Azure num novo separador ou janela, navegue para o espaço de trabalho Log Analytics que criou antes do início deste tutorial.
    > [!div class="mx-imgBorder"]
    > ![ID do espaço de trabalho Azure Log Analytics](media/tutorial-events-log-analytics/log-analytics-workspace-id-08.png)

1. No menu esquerdo, localize a **Gestão de Agentes** e selecione-o. Isto vai mostrar-lhe as chaves do agente que foram geradas.
    > [!div class="mx-imgBorder"]
    > ![Gestão de agentes Azure Log Analytics](media/tutorial-events-log-analytics/select-agents-management-09.png)

1. Copie o ID do *espaço de trabalho.*
    > [!div class="mx-imgBorder"]
    > ![Copiar ID do espaço de trabalho](media/tutorial-events-log-analytics/copy-workspace-id.png)

1. No outro separador ou janela do navegador, sob o Azure Log Analytics Data Collector selecione **Enviar Dados,** dar à sua ligação um nome e, em seguida, colar o *ID* do Espaço de Trabalho no campo de ID do **espaço de trabalho.**

1. Volte ao separador ou janela do navegador Workspace e copie a *Chave espaço de trabalho.*
    > [!div class="mx-imgBorder"]
    > ![Chave primária de gestão de agentes](media/tutorial-events-log-analytics/agents-management-primary-key-10.png)

1. No outro separador ou janela do navegador, cole a *chave do espaço de trabalho* no campo Chave do Espaço de **Trabalho.**

1. Selecione **Criar**. Agora irá criar o corpo de pedido JSON e o Nome de Registo Personalizado.

1. Selecione o campo **de corpo JSON Request.**  Aparecerá um link para **adicionar conteúdo dinâmico.**

1. Selecione **Adicionar Conteúdo Dinâmico** e, em seguida, selecione **Tópico**.

1. Faça o mesmo para o **Nome de Registo Personalizado.**
    > [!div class="mx-imgBorder"]
    > ![Tópico selecionado](media/tutorial-events-log-analytics/topic-selected.png)

1. Selecione **a visão** de código da Aplicação Lógica. Procure as linhas de entrada e Log-Type.
    > [!div class="mx-imgBorder"]
    > ![Vista de código de duas linhas](media/tutorial-events-log-analytics/code-view-two-lines.png)

1. Alterar o `body` valor de `"@triggerBody()?['topic']"` `"@{triggerBody()}"` . Isto é para analisar toda a mensagem para Log Analytics.

1. Mude o `Log-Type` `"@triggerBody()?['topic']"` de. para `"@replace(triggerBody()?['eventType'],'.','')"` . (Isto substituirá "." uma vez que estes não são permitidos em Nomes de Registos de Log Analytics.)
    > [!div class="mx-imgBorder"]
    > ![Lógica App json após mudança](media/tutorial-events-log-analytics/changed-lines.png)

1. Selecione **Guardar**.

1. Para verificar, selecione **o designer de aplicações Logic**.
    > [!div class="mx-imgBorder"]
    > ![Verificar etapas de corpo e função](media/tutorial-events-log-analytics/verify-changes-to-json.png)

1. Quando examinar todos os recursos do grupo de recursos, haverá uma App Lógica e dois conectores API de aplicação lógica listados, um para os Eventos e outro para Log Analytics. Para obter mais informações sobre os tópicos do sistema de grelha de eventos, leia [tópicos do sistema de grelha de eventos.](../../event-grid/system-topics.md)
    > [!div class="mx-imgBorder"]
    > ![Ver todos os novos recursos no Grupo de Recursos](media/tutorial-events-log-analytics/contoso-rg-listing.png)

## <a name="test"></a>Teste

Para testar como realmente funciona, crie um Evento Ao Vivo nos Serviços de Media Azure. Crie um Evento AO Vivo RTMP e use ffmpeg para empurrar um fluxo "live" baseado num ficheiro de amostra .mp4. Após a criação do evento, obtenha o URL de ingestão de RTMP.

1. Na sua conta de Media Services, selecione **Live streaming**.
    > [!div class="mx-imgBorder"]
    > ![Criar um Azure Media Services Live Event](media/tutorial-events-log-analytics/live-event.png)

1. **Selecione Adicionar evento ao vivo**.

1. Insira um nome no campo **de nome do evento Live.** (O campo **Descrição** é opcional.)

1. Selecione codificação de nuvem **standard.**

1. Selecione **Predefinição 720p** para a pré-sintonia codificante.

1. Selecione o protocolo de entrada **RTMP.**

1. Selecione **Sim** para o URL de entrada persistente.

1. Selecione **Sim** para iniciar o evento quando o evento for criado.

    > [!WARNING]
    > A faturação começará se escolher "Sim".  Se quiser esperar para iniciar o stream até *começar* a transmitir com fFmpeg, selecione **Não** e lembre-se de iniciar o seu evento ao vivo então.

    > [!div class="mx-imgBorder"]
    > ![Configurações de eventos ao vivo](media/tutorial-events-log-analytics/live-event-settings.png)

1. **Selecione Tenho todos os direitos de usar o conteúdo/arquivo...** caixa de verificação.

1. Selecione **Rever + criar**.

1. Reveja as suas definições e, em seguida, **selecione Criar**.  A listagem de eventos ao vivo será apresentada e o evento ao vivo Ingest URL será mostrado.

1. Copie o **URL Ingest** para a sua área de transferência.

1. Selecione o **evento ao vivo** na listagem para ver a vista do Produtor.

### <a name="stream-with-ffmpeg-cli"></a>Fluxo com FFmpeg CLI

1. Utilize a seguinte linha de comando.

    ```AzureCLI
    ffmpeg -i <localpathtovideo> -map 0 -c:v libx264 -c:a copy -f flv <ingestURL>/mystream
    ```

1. Altere `<ingestURL>` para o URL Ingest que copiou para a sua área de transferência.
1. Mude `<localpathtovideo>` para o caminho local do ficheiro que pretende transmitir a partir de FFmpeg.
1. Adicione um nome único no final, por `mystream` exemplo, .
1. Ajuste a linha de comando para refletir o seu ficheiro de origem de teste e quaisquer outras variáveis do sistema.
1. Execute o comando. Após alguns segundos, o leitor de "visão do produtor" deve começar a transmitir. (Refresque o leitor se o vídeo não aparecer automaticamente.)

    > [!div class="mx-imgBorder"]
    > ![Verifique a ingestão de vídeo adequada no Leitor de Pré-Visualização do Produtor](media/tutorial-events-log-analytics/live-event-producer-view.png)

## <a name="verify-the-events"></a>Verifique os eventos

Com o live stream, a Azure Media Services está a emitir vários eventos que estão a desencadear o fluxo da App Lógica. Para verificar, navegue na App Lógica e determine se existem gatilhos a serem disparados pelos eventos dos Media Services.

1. Navegue para a página de Visão Geral da Aplicação Lógica, deverá ver os trabalhos de listagem "Run History" que tenham sido concluídos com sucesso.
    > [!div class="mx-imgBorder"]
    > ![Verifique a execução de emprego bem sucedida na Logic App](media/tutorial-events-log-analytics/run-history.png)

1. Selecione um trabalho de sucesso. Os detalhes do trabalho durante o tempo de funcionação são mostrados.
1. Selecione **Enviar Dados** para expandi-lo. Neste caso, o `MicrosoftMediaLiveEventEncoderConnected` evento mostra que foi capturado, bem como o corpo analisado. Isto é o que é empurrado para o espaço de trabalho Azure Log Analytics.
    > [!div class="mx-imgBorder"]
    > ![Ver enviar dados](media/tutorial-events-log-analytics/verify-send-data.png)

## <a name="verify-the-logs"></a>Verifique os registos

1. Navegue para Log Analytics Workspace que criou anteriormente.

1. Selecionar **Registos**.
1. Feche o exemplo de consultas popup.
1. Haverá uma listagem de Registos Personalizados. Selecione a seta para baixo para expandi-la. Aí verá o nome do `MicrosoftMediaLiveEventEncoderConnected` evento.
1. Selecione o nome do evento para expandi-lo.
1. Quando selecionar o ícone "olho", apresentará uma pré-visualização do resultado da consulta.
1. Selecione **Ver em editor de consulta** e, em seguida, selecione o item na listagem **UTC de TempoGenerado** para expandi-lo e ver os dados brutos.

![Ver saída detalhada do evento no Log Analytics](media/tutorial-events-log-analytics/raw-data.png)

## <a name="delete-resources"></a>Eliminar recursos

Se não quiser continuar a utilizar os recursos que criou durante este tutorial, certifique-se de que elimina todos os recursos do grupo de recursos ou continuará a ser cobrado.

## <a name="next-steps"></a>Próximos passos

Pode criar diferentes consultas e salvá-las. Estes podem ser adicionados ao [Azure Dashboard](../../azure-monitor/learn/tutorial-logs-dashboards.md).