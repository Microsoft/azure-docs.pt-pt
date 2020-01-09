---
title: Solucionar problemas do Azure FarmBeats
description: Este artigo descreve como solucionar problemas do Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: f017b19ef8bd8e4c44d9e2885da6fbaf172808a1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75485978"
---
# <a name="troubleshoot"></a>Resolução de problemas

Este artigo fornece soluções para problemas comuns do FarmBeats do Azure.

Para obter ajuda adicional, entre em contato conosco em farmbeatssupport@microsoft.com. Certifique-se de incluir o arquivo **implantador. log** em seu email.

Para baixar o arquivo **implantador. log** , faça o seguinte:

1. Entre para **portal do Azure** e selecione sua assinatura e o locatário do Azure AD.
2. Inicie o Cloud Shell a partir do menu de navegação superior do portal do Azure.
3. Selecione **bash** como a experiência de Cloud Shell preferida.
4. Selecione o ícone realçado e, na lista suspensa, selecione **baixar**.

    ![FarmBeats do projeto](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. No próximo painel, insira o caminho para o arquivo **implantador. log** . Por exemplo, digite **farmbeats-deployer. log**.

## <a name="sensor-telemetry"></a>Telemetria do sensor

### <a name="cant-view-telemetry-data"></a>Não é possível exibir dados de telemetria

**Sintoma**: dispositivos ou sensores são implantados e você vinculou o FarmBeats ao seu parceiro de dispositivo, mas não pode obter ou exibir dados de telemetria no FarmBeats.

**Ação corretiva**:

1. Vá para o grupo de recursos do FarmBeats Datahub.   
2. Selecione o **Hub de eventos** (DatafeedEventHubNamespace) e, em seguida, verifique o número de mensagens de entrada.
3. Efetue um dos seguintes procedimentos:   
   - Se não houver *nenhuma mensagem de entrada*, entre em contato com o parceiro do dispositivo.  
   - Se houver *mensagens de entrada*, entre em contato com farmbeatssupport@microsoft.com. Anexe seus logs de Datahub e acelerador e telemetria capturada.

Para entender como baixar logs, vá para a seção ["coletar logs manualmente"](#collect-logs-manually) .  

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Não tem a cadeia de conexão dos hubs de eventos do Azure

**Ação corretiva**:

1. No Datahub Swagger, vá para a API de parceiro.
2. Selecione **obter** > **experimentar** > **executar**.
3. Observe a ID de parceiro do parceiro do sensor no qual você está interessado.
4. Volte para a API de parceiro e selecione **obter/\<ID >** .
5. Especifique a ID de parceiro da etapa 3 e, em seguida, selecione **executar**.

   A resposta da API deve ter a cadeia de conexão dos hubs de eventos.

### <a name="device-appears-offline"></a>O dispositivo aparece offline

**Sintomas**: os dispositivos são instalados e você vinculou o FarmBeats ao seu parceiro de dispositivo. Os dispositivos estão online e enviando dados de telemetria, mas aparecem offline.

**Ação corretiva**: o intervalo de relatório não está configurado para este dispositivo. Para definir o intervalo de relatórios, contate o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro ao excluir um dispositivo

Enquanto estiver excluindo um dispositivo, você poderá encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "o dispositivo é referenciado em sensores: há um ou mais sensores associados ao dispositivo. Exclua os sensores e, em seguida, exclua o dispositivo. "  

**Significando**: o dispositivo está associado a vários sensores que são implantados no farm.   

**Ação corretiva**:  

1. Exclua os sensores associados ao dispositivo por meio do acelerador.  
2. Se você quiser associar os sensores a um dispositivo diferente, peça ao seu parceiro de dispositivo para fazer o mesmo.  
3. Exclua o dispositivo usando uma chamada `DELETE API` e defina o parâmetro Force como *true*.  

**Mensagem**: "o dispositivo é referenciado em dispositivos como ParentDeviceId: há um ou mais dispositivos associados a este dispositivo como dispositivos filho. Exclua-os e exclua este dispositivo. "  

**Significado**: seu dispositivo tem outros dispositivos associados a ele.  

**Ação corretiva**

1. Exclua os dispositivos associados a este dispositivo específico.  
2. Exclua o dispositivo específico.  

    > [!NOTE]
    > Você não poderá excluir um dispositivo se os sensores estiverem associados a ele. Para obter mais informações sobre como excluir sensores associados, consulte a seção "excluir sensor" em [obter dados de sensor de parceiros de sensor](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Problemas com trabalhos

### <a name="farmbeats-internal-error"></a>Erro interno de FarmBeats

**Mensagem**: "erro interno do FarmBeats, consulte o guia de solução de problemas para obter mais detalhes".

**Ação corretiva**: esse problema pode resultar de uma falha temporária no pipeline de dados. Crie o trabalho novamente. Se o erro persistir, adicione a mensagem de erro em uma postagem no fórum do FarmBeats ou entre em contato com FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Solução de problemas do acelerador

### <a name="access-control"></a>Controlo de acesso

**Problema**: você recebe um erro enquanto está adicionando uma atribuição de função.

**Mensagem**: "nenhum usuário correspondente encontrado."

**Ação corretiva**: Verifique a ID de email para a qual você está tentando adicionar uma atribuição de função. A ID de email deve ser uma correspondência exata da ID registrada para esse usuário na Active Directory. Se o erro persistir, adicione a mensagem de erro em uma postagem no fórum do FarmBeats ou entre em contato com FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Não é possível fazer logon no acelerador

**Mensagem**: "erro: você não está autorizado a chamar o serviço. Contate o administrador para autorização. "

**Ação corretiva**: peça ao administrador para autorizar você a acessar a implantação do FarmBeats. Isso pode ser feito fazendo uma POSTAgem das APIs RoleAssignment ou por meio do controle de acesso no painel **configurações** no acelerador.  

Se você já tiver recebido o acesso e estiver enfrentando esse erro, tente novamente atualizando a página. Se o erro persistir, adicione a mensagem de erro em uma postagem no fórum do FarmBeats ou entre em contato com FarmBeatsSupport@microsoft.com.

![FarmBeats do projeto](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas do acelerador  

**Problema**: você recebeu um erro de acelerador de causa indeterminada.

**Mensagem**: "erro: ocorreu um erro desconhecido."

**Ação corretiva**: esse erro ocorrerá se você deixar a página ociosa por muito tempo. Atualize a página.  

Se o erro persistir, adicione a mensagem de erro em uma postagem no fórum do FarmBeats ou entre em contato com FarmBeatsSupport@microsoft.com.

**Problema**: o acelerador de FarmBeats não está mostrando a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva**: esse erro ocorre devido à persistência do trabalhador do serviço no navegador. Faça o seguinte:
1. Feche todas as guias do navegador que têm o acelerador aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o URI do acelerador. Essa ação carrega a nova versão do acelerador.

## <a name="sentinel-imagery-related-issues"></a>Sentinela: problemas relacionados a imagens

### <a name="wrong-username-or-password"></a>Nome de usuário ou senha incorreto

**Mensagem de falha de trabalho**: "a autenticação completa é necessária para acessar este recurso."

**Ação corretiva**:

Efetue uma das seguintes ações:
- Execute novamente o instalador para atualizar Datahub com o nome de usuário e a senha corretos.
- Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e, em seguida, verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Hub Sentinel: URL ou site incorreto inacessível 

**Mensagem de falha de trabalho**: "Opa, algo deu errado. A página que você estava tentando acessar está (temporariamente) indisponível. " 

**Ação corretiva**:
1. Abra o [Sentinel](https://scihub.copernicus.eu/dhus/) em seu navegador para ver se o site está acessível. 
2. Se o site não estiver acessível, verifique se algum firewall, rede da empresa ou outro software de bloqueio está impedindo o acesso ao site e execute as etapas necessárias para permitir a URL do sentinela. 
3. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e, em seguida, verifique se o trabalho foi bem-sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor Sentinel: inoperante para manutenção

**Mensagem de falha do trabalho**: "o Hub de acesso aberto do Copernicus será feito em breve! Lamentamos o inconveniente, estamos realizando alguma manutenção no momento. Voltaremos a ficar online em breve! " 

**Ação corretiva**:

Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel.

1. Se algum trabalho ou pipeline falhar porque a manutenção está sendo executada, envie o trabalho novamente após algum tempo. 

   Para obter informações sobre qualquer atividade de manutenção de sentinela planejada ou não planejada, vá para o site de [notícias do hub de acesso do Copernicus Open](https://scihub.copernicus.eu/news/) .  
2. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e, em seguida, verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinela: número máximo de conexões atingido

**Mensagem de falha de trabalho**: "número máximo de dois fluxos simultâneos obtidos pelo usuário '\<nome de usuário > '."

**Significando**: se um trabalho falhar porque o número máximo de conexões foi atingido, a mesma conta do Sentinel está sendo usada em outra implantação de software.

**Ação corretiva**: Tente uma das seguintes opções:
* Crie uma nova conta do Sentinel e execute novamente o instalador para atualizar o Datahub usando um novo nome de usuário e senha de sentinela.  
* Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e, em seguida, verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-server-refused-connection"></a>Servidor Sentinel: conexão recusada 

**Mensagem de falha do trabalho**: "o servidor recusou a conexão em: http://172.30.175.69:8983/solr/dhus". 

**Ação corretiva**: esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel. 
1. Se algum trabalho ou pipeline falhar porque a manutenção está sendo executada, envie o trabalho novamente após algum tempo. 

   Para obter informações sobre qualquer atividade de manutenção de sentinela planejada ou não planejada, vá para o site de [notícias do hub de acesso do Copernicus Open](https://scihub.copernicus.eu/news/) .  
2. Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5 a 7 dias e, em seguida, verifique se o trabalho foi bem-sucedido.

## <a name="collect-logs-manually"></a>Coletar logs manualmente

[Instalar e implantar Gerenciador de armazenamento do Azure]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Coletar Azure Data Factory logs de trabalho no Datahub
1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na caixa de **pesquisa** , procure o grupo de recursos FarmBeats Datahub.

    > [!NOTE]
    > Selecione o grupo de recursos Datahub que você especificou durante a instalação do FarmBeats.

3. No painel do **grupo de recursos** , procure a conta de armazenamento do *datahublogs\** . Por exemplo, pesquise **datahublogsmvxmq**.  
4. Na coluna **nome** , selecione a conta de armazenamento para exibir o painel da **conta de armazenamento** .
5. No painel de **\*do datahubblogs** , selecione **abrir no Explorer** para exibir o aplicativo **Open Gerenciador de armazenamento do Azure** .
6. No painel esquerdo, selecione **contêineres de blob**e, em seguida, selecione **logs de trabalho**.
7. No painel **logs de trabalho** , selecione **baixar**.
8. Baixe os logs em uma pasta local em seu computador.
9. Envie por email o arquivo. zip baixado para farmbeatssupport@microsoft.com.

    ![FarmBeats do projeto](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Coletar Azure Data Factory logs de trabalho no acelerador

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na caixa de **pesquisa** , procure o grupo de recursos do acelerador FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos do acelerador que você especificou durante a instalação do FarmBeats.

3. No painel do **grupo de recursos** , procure a conta de armazenamento do\*de *armazenamento* . Por exemplo, pesquise **storagedop4k\*** .
4. Selecione a conta de armazenamento na coluna **nome** para exibir o painel da **conta de armazenamento** .
5. No painel **\*de armazenamento** , selecione **abrir no Explorer** para abrir o aplicativo Gerenciador de armazenamento do Azure.
6. No painel esquerdo, selecione **contêineres de blob**e, em seguida, selecione **logs de trabalho**.
7. No painel **logs de trabalho** , selecione **baixar**.
8. Baixe os logs em uma pasta local em seu computador.
9. Envie por email o arquivo. zip baixado para farmbeatssupport@microsoft.com.


### <a name="collect-datahub-app-service-logs"></a>Coletar logs do serviço de aplicativo Datahub

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na caixa de **pesquisa** , procure o grupo de recursos FarmBeats Datahub.

    > [!NOTE]
    > Selecione o grupo de recursos Datahub que você especificou durante a instalação do FarmBeats.

3. No grupo de recursos, procure a conta de armazenamento do *datahublogs\** . Por exemplo, pesquise **fordatahublogsmvxmq\*** .
4. Selecione a conta de armazenamento na coluna **nome** para exibir o painel da **conta de armazenamento** .
5. No painel de **\*do datahubblogs** , selecione **abrir no Explorer** para abrir o aplicativo Gerenciador de armazenamento do Azure.
6. No painel esquerdo, selecione **contêineres de blob**e, em seguida, selecione **appinsights-logs**.
7. No painel **appinsights-logs** , selecione **baixar**.
8. Baixe os logs em uma pasta local em seu computador.
9. Envie por email o arquivo. zip baixado para farmbeatssupport@microsoft.com.

### <a name="collect-accelerator-app-service-logs"></a>Coletar logs do serviço de aplicativo acelerador

1. Inicie sessão no [Portal do Azure](https://portal.azure.com).
2. Na caixa de **pesquisa** , procure o grupo de recursos do acelerador FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos do acelerador de FarmBeats que foi fornecido durante a instalação do FarmBeats.

3. No grupo de recursos, pesquise o *armazenamento\** conta de armazenamento. Por exemplo, pesquise **storagedop4k\*** .
4. Selecione a conta de armazenamento na coluna **nome** para exibir o painel da **conta de armazenamento** .
5. No painel **\*de armazenamento** , selecione **abrir no Explorer** para abrir o aplicativo Gerenciador de armazenamento do Azure.
6. No painel esquerdo, selecione **contêineres de blob**e, em seguida, selecione **appinsights-logs**.
7. No painel **appinsights-logs** , selecione **baixar**.
8. Baixe os logs em uma pasta local em seu computador.
9. Envie por email a pasta baixada para farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Problemas conhecidos

## <a name="batch-related-issues"></a>Problemas relacionados ao lote

**Mensagem de erro**: "a cota da conta regional das contas do lote para a assinatura especificada foi atingida".

**Ação corretiva**: aumente a cota ou exclua as contas do lote não usadas e execute a implantação novamente.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Problemas relacionados ao Azure Active Directory (Azure AD)

**Mensagem de erro**: "não foi possível atualizar as configurações necessárias para aplicativo Azure ad d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: privilégios insuficientes para concluir a operação. Verifique se as configurações acima estão definidas corretamente para o Aplicativo Azure AD ".

**Significado**: a configuração de registro de aplicativo do Azure ad não foi concluída corretamente.  

**Ação corretiva**: peça ao administrador de ti (a pessoa com acesso de leitura de locatário) para usar nosso [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) para criar o registro de aplicativo do Azure AD. Esse script também cuida automaticamente das etapas de configuração.

**Mensagem de erro**: "não foi possível criar o novo aplicativo de Active Directory '\<nome do aplicativo\>' neste locatário: outro objeto com o mesmo valor para os URIs do identificador de propriedade já existe."

**Significado**: já existe um registro de aplicativo do Azure AD com o mesmo nome.

**Ação corretiva**: exclua o registro de aplicativo do Azure ad existente ou reutilize-o para instalação. Se você estiver reutilizando o registro de aplicativo do Azure AD existente, passe a ID do aplicativo e o segredo do cliente para o instalador e reimplante.

## <a name="issues-with-the-inputjson-file"></a>Problemas com o arquivo Input. JSON

**Erro**: há um erro ao ler a entrada do arquivo *Input. JSON* .

**Ação corretiva**: normalmente, esse problema ocorre devido a um erro na especificação do caminho ou nome do arquivo de *entrada. JSON* correto para o instalador. Faça as correções apropriadas e tente a implantação novamente.

**Erro**: há um erro ao analisar os valores no arquivo *Input. JSON* .

**Ação corretiva**: esse problema ocorre principalmente devido a valores incorretos no arquivo *Input. JSON* . Faça as correções apropriadas e tente a implantação novamente.

## <a name="high-cpu-usage"></a>Elevada utilização da CPU

**Erro**: você recebe um alerta de email que se refere a um **alerta de alto uso da CPU**. 

**Ação corretiva**: 
1. Vá para o grupo de recursos do FarmBeats Datahub.
2. Selecione o **serviço de aplicativo**.  
3. Vá para a página escalar verticalmente os [preços do serviço de aplicativo](https://azure.microsoft.com/pricing/details/app-service/windows/)e selecione um tipo de preço apropriado.

## <a name="next-steps"></a>Passos seguintes

Se você ainda estiver enfrentando problemas de FarmBeats, entre em contato com nosso [Fórum de suporte](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
