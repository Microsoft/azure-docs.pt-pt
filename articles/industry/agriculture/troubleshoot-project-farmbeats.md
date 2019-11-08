---
title: Resolução de problemas
description: Como solucionar problemas do Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: ab0d3aac6944e331223d91eb98b50b3308be12c3
ms.sourcegitcommit: 018e3b40e212915ed7a77258ac2a8e3a660aaef8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/07/2019
ms.locfileid: "73797637"
---
# <a name="troubleshooting"></a>Resolução de problemas

As seções a seguir descrevem problemas comuns do FarmBeats do Azure e como corrigi-los.

Para obter ajuda adicional, escreva para nós em farmbeatssupport@microsoft.com, inclua o arquivo implanter. log neste email.

 Use estas etapas para baixar o arquivo implantador. log:

1. O ícone realçado e selecione a opção **baixar** na lista suspensa.

    ![Batidas no farm de projetos](./media/troubleshooting-farmbeats/download-deployer-log-1.png)

2. Na próxima tela, insira o caminho para o arquivo implantador. log. Por exemplo, farmbeats-deployer. log.

## <a name="sensor-telemetry"></a>Telemetria do sensor

### <a name="telemetry-not-seen"></a>Telemetria não vista

**Sintoma**: dispositivos/sensores são implantados e você vinculou o FarmBeats ao seu parceiro de dispositivo; Mas você não pode obter/exibir dados de telemetria no FarmBeats.

**Ação corretiva**: visite a Portal do Azure e siga estas etapas:

1. Vá para o grupo de recursos do hub de dados do FarmBeats.   
2. Selecione o **Hub de eventos** (DatafeedEventHubNamespace...)  e verifique o número de mensagens de entrada.   
3. Caso não haja **mensagens de entrada**, entre em contato com o parceiro do dispositivo.  
4. Caso haja mensagens de **entrada**, entre em contato com farmbeatssupport@microsoft.com com os logs de Hub de dados e acelerador e a telemetria capturada.

Consulte a [seção logs](#collect-logs-manually) do documento para entender como baixar logs.    

### <a name="device-appears-offline"></a>O dispositivo aparece offline

**Sintomas**: os dispositivos são instalados e você vinculou o FarmBeats ao seu parceiro de dispositivo. Os dispositivos estão online e enviando dados de telemetria, mas aparecem offline.

**Ação corretiva**: o intervalo de relatório não está configurado para este dispositivo. Contate o fabricante do dispositivo para definir o intervalo de relatórios. 

### <a name="error-deleting-a-resource"></a>Erro ao excluir um recurso

A seguir estão os cenários de erro comuns ao excluir um dispositivo:  

**Mensagem**: o dispositivo é referenciado em sensores: há um ou mais sensores associados ao dispositivo. Exclua os sensores e, em seguida, exclua o dispositivo.  

**Significado**: o dispositivo está associado a vários sensores implantados no farm.   

**Ação corretiva**:  

1. Exclua os sensores associados ao dispositivo por meio do acelerador.  
2. Caso você queira associar os sensores a um dispositivo diferente, peça ao seu parceiro de dispositivo para fazer o mesmo.  
3. Exclua o dispositivo usando uma chamada de exclusão de API definindo o parâmetro Force como ' true '.  

**Mensagem**: o dispositivo é referenciado em dispositivos como ParentDeviceId: há um ou mais dispositivos associados a este dispositivo como dispositivos filho. Exclua-os e, em seguida, exclua este dispositivo.  

**Significado**: seu dispositivo tem outros dispositivos associados a ele  

**Ação corretiva**

1. Excluir os dispositivos associados ao dispositivo específico  
2. Excluir o dispositivo específico  

    > [!NOTE]
    > Você não poderá excluir um dispositivo se os sensores estiverem associados a ele. Consulte [excluir sensores](get-sensor-data-from-sensor-partner.md) no capítulo obter dados do sensor para obter mais informações sobre como excluir sensores associados.


## <a name="issues-with-jobs"></a>Problemas com trabalhos

### <a name="farmbeats-internal-error"></a>Erro interno de FarmBeats

**Mensagem**: FarmBeats erro interno, consulte o guia de solução de problemas para obter mais detalhes.

**Ação corretiva**: isso pode ser devido a uma falha temporária no pipeline de dados. Crie o trabalho novamente. Se o erro persistir, adicione o erro em uma postagem no fórum do FarmBeats ou contate a FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Solução de problemas do acelerador

### <a name="access-control"></a>Controlo de Acesso

**Erro ao adicionar atribuição de função**

**Mensagem**: nenhum usuário correspondente encontrado

**Ação corretiva**: Verifique a ID de email para a qual você está tentando fazer uma atribuição de função. A ID de email deve ser uma correspondência exata do registrado para esse usuário na Active Directory. Se o erro persistir, adicione o erro em uma postagem no fórum do FarmBeats ou contate FarmBeatsSupport@microsoft.com

### <a name="unable-to-log-in-to-accelerator"></a>Não é possível fazer logon no acelerador

**Mensagem**: erro: você não está autorizado a chamar o serviço. Contate o administrador para autorização.

**Ação corretiva**: peça ao administrador para autorizar você a acessar a implantação do FarmBeats. Isso pode ser feito fazendo uma POSTAgem das APIs RoleAssignment ou por meio do controle de acesso no painel configurações no acelerador.  

Se você já tiver sido adicionado e estiver enfrentando esse erro, tente novamente atualizando a página.  Se o erro persistir, adicione o erro em uma postagem no fórum do FarmBeats ou contate a FarmBeatsSupport@microsoft.com.

![Batidas no farm de projetos](./media/troubleshooting-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator---an-unknown-error-occurred"></a>Acelerador-ocorreu um erro desconhecido  

**Mensagem**: erro: ocorreu um erro desconhecido

**Ação corretiva**: isso ocorrerá se você deixar a página ociosa por muito tempo. Atualize a página.  

Se o erro ainda persistir, adicione o erro em uma postagem no fórum do FarmBeats ou contate FarmBeatsSupport@microsoft.com

**O acelerador do FarmBeats não está mostrando a versão mais recente mesmo depois de atualizar o FarmBeatsDeployment**

**Ação corretiva**: isso ocorre devido à persistência do trabalhador do serviço no navegador.
Feche todas as guias do navegador que têm o acelerador aberto e feche a janela do navegador. Inicie uma nova instância do navegador e carregue o URI do acelerador novamente. Isso carregará a nova versão do acelerador.

## <a name="sentinel-imagery-related-issues"></a>Problemas relacionados a imagens Sentinel

### <a name="sentinel-wrong-username-or-password"></a>Sentinela nome de usuário ou senha incorreto

**Mensagem de falha de trabalho**; A autenticação completa é necessária para acessar esse recurso.

**Ação de correção**: execute novamente o instalador para atualizar o Hub de dados com o nome de usuário e senha corretos.

**Ação corretiva**: execute novamente o trabalho com falha ou execute um trabalho de índices satélite para o intervalo de datas de 5-7 dias e verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-hub-wrongurlor-not-accessible"></a>URL incorreta do Hub Sentinel ou não acessível 

**Mensagem de falha de trabalho**: Opa, algo deu errado. A página que você estava tentando acessar está (temporariamente) indisponível. 

**Ação corretiva**:
1.  Abra a URL do Sentinel (https://scihub.copernicus.eu/dhus/) no navegador e verifique se o site está acessível. 
2.  Se o site não estiver acessível, verifique se alguma rede de firewall/empresa, etc. está bloqueando o site e execute as etapas necessárias para permitir a URL acima. 
3.  Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5-7 dias e verifique se o trabalho foi bem-sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor Sentinel inoperante para manutenção

**Mensagem de falha do trabalho**: o Hub de acesso aberto do Copernicus será feito em breve! Lamentamos o inconveniente, estamos realizando alguma manutenção no momento. Vamos voltar online em breve! 

**Ação corretiva**:

1.  Esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel. 
2.  Se algum trabalho/pipeline falhar com o motivo acima, envie o trabalho novamente após algum tempo. 
3.  O usuário pode visitar https://scihub.copernicus.eu/news/ para verificar informações sobre qualquer atividade de manutenção de sentinela planejada/não planejada.  
4.  Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para um intervalo de datas de 5-7 dias e verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>O número máximo de conexões do sentinela foi atingido

**Mensagem de falha de trabalho**: número máximo de dois fluxos simultâneos obtidos pelo usuário "<username>" 

**Ação corretiva**
1.  Se algum trabalho falhar com o motivo acima, a mesma conta do Sentinel está sendo usada em outra implantação/software. 
2.  O usuário pode criar uma nova conta do Sentinel e executar novamente o instalador para atualizar o Hub de dados com novo nome de usuário e senha do Sentinel.  
3.  Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para o intervalo de datas de 5-7 dias e verifique se o trabalho foi bem-sucedido.

### <a name="sentinel-server-refused-connection"></a>Conexão recusada pelo servidor Sentinel 

**Mensagem de falha de trabalho**:

O servidor recusou a conexão em: http://172.30.175.69:8983/solr/dhus 

**Ação corretiva**: esse problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel. 
1.  Se algum trabalho/pipeline falhar com o motivo acima, envie o trabalho novamente após algum tempo. 
2.  O usuário pode visitar https://scihub.copernicus.eu/news/ para verificar informações sobre qualquer atividade de manutenção de sentinela planejada/não planejada.  
3.  Execute novamente o trabalho com falha ou execute um trabalho de índices satélite para o intervalo de datas de 5-7 dias e verifique se o trabalho foi bem-sucedido.


## <a name="collect-logs-manually"></a>Coletar logs manualmente

[Instale e implante]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows) no Gerenciador de armazenamento do Azure.

### <a name="how-to-collect-data-hub-adf-job-logs"></a>Como coletar logs de trabalho do ADF do hub de dados
1. Faça logon no https://portal.azure.com
2. Na caixa de texto **Pesquisar** , procure por grupo de recursos do hub de dados FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos do hub de dados que foi especificado no momento da implantação do FarmBeats.

No painel do grupo de recursos, procure a conta de armazenamento (datahublogs...). Por exemplo, datahublogsmvxmq  

1.  Selecione a conta de armazenamento na coluna **nome** para exibir o painel da **conta de armazenamento** .
2.  Na página (datahubblogs...), selecione **abrir no Explorer** para exibir o aplicativo **aberto Gerenciador de armazenamento do Azure** .
3.  No painel esquerdo, (datahubblogs...), **contêineres de blob**, selecione **trabalhos-logs**.
4.  Na guia **trabalhos-logs** , selecione **baixar**.
5.  Selecione o local para baixar os logs em uma pasta local em seu computador.
6.  Enviar por email o arquivo zip baixado para farmbeatssupport@microsoft.com

    ![Batidas no farm de projetos](./media/troubleshooting-farmbeats/collecting-logs-manually-1.png)

### <a name="how-to-collect-accelerator-adf-job-logs"></a>Como coletar logs de trabalho do acelerador ADF

1.  Faça logon no https://portal.azure.com
2.  Na caixa de texto de **pesquisa** , pesquise por grupo de recursos do acelerador de FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos do acelerador que foi especificado no momento da implantação do FarmBeats.

3.  No painel do grupo de recursos, procure armazenamento.... Conta de armazenamento. Por exemplo, storagedop4k
4.  Selecione a conta de armazenamento na coluna **nome** para exibir o painel da conta de armazenamento.
5.  Na página (armazenamento...), selecione **abrir no Explorer** para exibir o aplicativo aberto Gerenciador de armazenamento do Azure.
6.  No painel esquerdo, < armazenamento...), **contêineres de blob**, selecione **logs de trabalho**.
7.  Na guia **trabalhos-logs** , selecione **baixar**.
8.  Selecione o local para baixar os logs em uma pasta local em seu computador.
9.  Enviar por email o arquivo zip baixado para farmbeatssupport@microsoft.com


### <a name="how-to-collect-data-hub-app-service-logs"></a>Como coletar logs do serviço de aplicativo do hub de dados

1.  Faça logon no https://portal.azure.com
2.  Na caixa de texto **Pesquisar** , procure por grupo de recursos do hub de dados FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos do hub de dados que foi especificado no momento da implantação do FarmBeats.

3.  No grupo de recursos, procure por conta de armazenamento (datahublogs...). Por exemplo, datahublogsmvxmq
4.  Selecione a conta de armazenamento na coluna **nome** para exibir o painel da **conta de armazenamento** .
5.  Na página (datahubblogs...), selecione **abrir no Explorer** para exibir o aplicativo **aberto Gerenciador de armazenamento do Azure** .
6.  No painel esquerdo, (datahubblogs...), **contêineres de blob**, selecione appinsights-logs.
7.  Na guia appinsights-logs, selecione **baixar**.
8.  Selecione o caminho para baixar os logs em uma pasta local em seu computador.
9.  Envie por email a pasta baixada para farmbeatssupport@microsoft.com

### <a name="how-to-collect-accelerator-app-service-logs"></a>Como coletar logs do serviço de aplicativo acelerador

1.  Faça logon no https://portal.azure.com
2.  Na **pesquisa**, procure o grupo de recursos do acelerador FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos do acelerador de Farmbeats que é fornecido no momento da implantação do FarmBeats.

3.  No grupo de recursos, procure por conta de armazenamento (armazenamento...). Por exemplo, storagedop4k
4.  Selecione a conta de armazenamento na coluna **nome** para exibir o painel da **conta de armazenamento** .
5.  Na página (armazenamento...), selecione **abrir no Explorer** para exibir o aplicativo **aberto Gerenciador de armazenamento do Azure** .
6.  No painel esquerdo, (Storage....), **contêineres de blob**, selecione appinsights-logs.
7.  Na guia appinsights-logs, selecione **baixar**.
8.  Selecione o local para baixar os logs em uma pasta local em seu computador.
9.  Envie por email a pasta baixada para farmbeatssupport@microsoft.com

## <a name="known-issues"></a>Problemas conhecidos

## <a name="batch-related-issues"></a>Problemas relacionados ao lote

**Erro**: a cota da conta regional das contas do lote para a assinatura especificada foi atingida.

**Ação corretiva**: aumente a cota ou exclua as contas do lote não usadas e execute a implantação novamente.

### <a name="azure-active-directory-related-issues"></a>Azure Active Directory problemas relacionados

**Erro**: não foi possível atualizar as configurações necessárias para aplicativo Azure ad d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: privilégios insuficientes para concluir a operação. Verifique se as configurações acima estão definidas corretamente para o Aplicativo Azure AD.

**Significado**: a configuração de registro de aplicativo do Azure ad não aconteceu corretamente.  

**Ação corretiva**: peça ao administrador de ti (com acesso de leitura de locatário) para usar nosso [script](https://aka.ms/PPCreateAADappregistration) para gerar a criação do registro de aplicativo do Azure AD. Esse script também cuidará automaticamente das etapas de configuração. como  

**Erro**: não foi possível criar o novo Active Directory aplicativo "fictícioname" neste locatário: já existe outro objeto com o mesmo valor para URIs de identificador de propriedade

**Significado**: já existe um registro de aplicativo do Azure AD com o mesmo nome.

**Ação corretiva**: exclua o registro de aplicativo do Azure ad existente ou reutilize-o para instalação. Se você estiver reutilizando o Azure AD existente, passe a ID do aplicativo e o segredo do cliente para o instalador e reimplante.

## <a name="inputjson-related-issues"></a>Problemas relacionados ao Input. JSON

**Erro ao** ler a entrada do arquivo Input. JSON

**Ação corretiva**: esse problema ocorre principalmente devido à perda na especificação do caminho JSON de entrada ou do nome correto para o instalador. Faça as correções apropriadas e tente reimplantar novamente.

**Erro ao analisar entrada JSON**

**Ação corretiva**: esse problema ocorre principalmente devido a valores incorretos dentro do arquivo JSON de entrada. Faça as correções apropriadas e tente implantar novamente.

## <a name="high-cpu-usage"></a>Elevada utilização da CPU

**Erro**: você recebe um alerta de email referindo-se ao alto alerta de uso da CPU. 

**Ação corretiva**: 
1.  Vá para o grupo de recursos do hub de dados do FarmBeats.
2.  Selecione o serviço de aplicativo.  
3.  Vá para escalar verticalmente (plano do serviço de aplicativo) e selecione um [tipo de preço](https://azure.microsoft.com/pricing/details/app-service/windows/) apropriado

## <a name="next-steps"></a>Passos seguintes

Se você ainda estiver enfrentando problemas, entre em contato conosco em nosso [Fórum de suporte](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
