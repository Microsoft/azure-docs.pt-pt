---
title: Resolver problemas do Azure FarmBeats
description: Este artigo descreve como resolver problemas com a Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 20d07be99aa2f9881218f8d581ac8d429a1fe4d0
ms.sourcegitcommit: c29b7870f1d478cec6ada67afa0233d483db1181
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/13/2020
ms.locfileid: "79298806"
---
# <a name="troubleshoot"></a>Resolver Problemas

Este artigo fornece soluções para questões comuns da Azure FarmBeats.

Para obter ajuda adicional, contacte-nos em farmbeatssupport@microsoft.com. Certifique-se de incluir o ficheiro **deployer.log** no seu e-mail.

Para descarregar o ficheiro **deployer.log,** faça o seguinte:

1. Inscreva-se no **portal Azure** e selecione a sua subscrição e inquilino da Azure AD.
2. Inicie o Cloud Shell a partir do menu de navegação superior do portal do Azure.
3. Selecione **Bash** como a experiência preferida da Cloud Shell.
4. Selecione o ícone realçado e, em seguida, na lista de drop-down, selecione **Download**.

    ![Project FarmBeats](./media/troubleshoot-azure-farmbeats/download-deployer-log-1.png)

5. No painel seguinte, introduza o caminho para o seu ficheiro **deployer.log.** Por exemplo, introduza **farmbeats-deployer.log**.

## <a name="sensor-telemetry"></a>Telemetria do sensor

### <a name="cant-view-telemetry-data"></a>Não se pode ver dados de telemetria

**Sintoma**: Dispositivos ou sensores estão implantados e ligou farmBeats com o seu parceiro de dispositivo, mas não pode obter ou ver dados de telemetria no FarmBeats.

**Ação corretiva:**

1. Vá ao seu grupo de recursos FarmBeats Datahub.   
2. Selecione o Site de **Eventos** (DatafeedEventHubNamespace) e, em seguida, verifique o número de mensagens que chegam.
3. Efetue um dos seguintes procedimentos:   
   - Se não houver *mensagens de entrada,* contacte o parceiro do dispositivo.  
   - Se houver *mensagens de entrada,* contacte farmbeatssupport@microsoft.com. Prenda os seus registos datahub e acelerador e a telemetria capturada.

Para entender como descarregar registos, aceda à secção ["Recolher registos manualmente".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível visualizar dados de telemetria depois de ingerir dados históricos/de streaming dos seus sensores

**Sintoma**: Dispositivos ou sensores são implantados e você criou os dispositivos/sensores em FarmBeats e ingerido telemetria para o EventHub, mas você não pode obter ou ver dados de telemetria em FarmBeats.

**Ação corretiva:**

1. Certifique-se de que fez o registo do parceiro corretamente - pode verificar isso indo para o seu datahub swagger, navegar para /Partner API, Fazer um Get e verificar se o parceiro está registado. Caso contrário, siga os [passos aqui](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar parceiro.
2. Certifique-se de que utilizou o formato de mensagem telemetria correto:

```json
{
"deviceid": "<id of the Device created>",
"timestamp": "<timestamp in ISO 8601 format>",
"version" : "1",
"sensors": [
    {
      "id": "<id of the sensor created>",
      "sensordata": [
        {
          "timestamp": "< timestamp in ISO 8601 format >",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": "<value>"
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Não tenha a cadeia de conexão Azure Event Hubs

**Ação corretiva:**

1. Em Datahub Swagger, vá à API partner.
2. Selecione **Obter** > **Experimente** > **executar**.
3. Reparem na identificação do parceiro de sensor que lhe interessa.
4. Volte para a API partner e selecione **Get/\<ID>**
5. Especifique o ID do parceiro a partir do passo 3 e, em seguida, selecione **Executar**.

   A resposta DaPI deve ter a cadeia de ligação Do Event Hubs.

### <a name="device-appears-offline"></a>Dispositivo aparece offline

**Sintomas**: Os dispositivos estão instalados e ligou o FarmBeats ao seu parceiro de dispositivo. Os dispositivos estão online e enviam dados de telemetria, mas parecem offline.

**Ação corretiva**: O intervalo de reporte não está configurado para este dispositivo. Para definir o intervalo de reporte, contacte o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro ao apagar um dispositivo

Enquanto está a apagar um dispositivo, pode encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "O dispositivo é referenciado nos sensores: Existem um ou mais sensores associados ao dispositivo. Apague os sensores e, em seguida, apague o dispositivo."  

**Significando:** O dispositivo está associado a múltiplos sensores que são implantados na exploração.   

**Ação corretiva:**  

1. Elimine os sensores associados ao dispositivo através do Acelerador.  
2. Se quiser associar os sensores a um dispositivo diferente, peça ao seu parceiro do dispositivo para fazer o mesmo.  
3. Elimine o dispositivo utilizando uma chamada `DELETE API` e defina o parâmetro de força como *verdadeiro*.  

**Mensagem**: "O dispositivo é referenciado em dispositivos como ParentDeviceId: Existem um ou mais dispositivos que estão associados a este dispositivo como dispositivos infantis. Apague-os e, em seguida, apague este dispositivo."  

**Ou seja:** O seu dispositivo tem outros dispositivos associados.  

**Ação corretiva**

1. Elimine os dispositivos associados a este dispositivo específico.  
2. Elimine o dispositivo específico.  

    > [!NOTE]
    > Não é possível apagar um dispositivo se os sensores estiverem associados a ele. Para obter mais informações sobre como eliminar os sensores associados, consulte a secção **de sensores Delete** in [Get dados de sensores dos parceiros de sensores](get-sensor-data-from-sensor-partner.md).


## <a name="issues-with-jobs"></a>Questões com emprego

### <a name="farmbeats-internal-error"></a>Erro interno FarmBeats

**Mensagem**: "FarmBeats erro interno, consulte guia de resolução de problemas para mais detalhes".

**Ação corretiva**: Esta questão pode resultar de uma falha temporária no gasoduto de dados. Criar o trabalho de novo. Se o erro persistir, adicione a mensagem de erro numa publicação no fórum FarmBeats ou contacte FarmBeatsSupport@microsoft.com.

## <a name="accelerator-troubleshooting"></a>Resolução de problemas do acelerador

### <a name="access-control"></a>Controlo de acesso

**Emissão**: Recebe um erro enquanto adiciona uma atribuição de funções.

**Mensagem**: "Não foram encontrados utilizadores correspondentes."

**Ação corretiva**: Verifique o ID de e-mail para o qual está a tentar adicionar uma atribuição de funções. O ID de e-mail deve ser uma correspondência exata do ID, que está registado para esse utilizador no Diretório Ativo. Se o erro persistir, adicione a mensagem de erro numa publicação no fórum FarmBeats ou contacte FarmBeatsSupport@microsoft.com.

### <a name="unable-to-log-in-to-accelerator"></a>Incapaz de iniciar sessão no Acelerador

**Mensagem**: "Erro: Não está autorizado a ligar para o serviço. Contacte o administrador para obter autorização."

**Ação corretiva**: Peça ao administrador que o autorize a aceder à implementação farmBeats. Isto pode ser feito fazendo uma publicação das APIs de Atribuição de Funções ou através do Controlo de Acesso no painel **definições** no Acelerador.  

Se já lhe foi concedido acesso e está a enfrentar este erro, tente novamente refrescando a página. Se o erro persistir, adicione a mensagem de erro numa publicação no fórum FarmBeats ou contacte FarmBeatsSupport@microsoft.com.

![Project FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas de acelerador  

**Problema**: Recebeu um erro acelerador de causa indeterminada.

**Mensagem**: "Erro: Ocorreu um erro desconhecido".

**Ação corretiva**: Este erro ocorre se deixar a página inativa por muito tempo. Atualize a página.  

Se o erro persistir, adicione a mensagem de erro numa publicação no fórum FarmBeats ou contacte FarmBeatsSupport@microsoft.com.

**Problema**: FarmBeats Accelerator não está a mostrar a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva**: Este erro ocorre devido à persistência do trabalhador de serviço no navegador. efetue o seguinte:

1. Feche todos os separadores do navegador que tenham o Acelerador aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o Acelerador URI. Esta ação carrega a nova versão do Acelerador.

## <a name="sentinel-imagery-related-issues"></a>Sentinela: Questões relacionadas com imagens

### <a name="wrong-username-or-password"></a>Nome de utilizador errado ou senha

**Mensagem de falha de emprego**: "A autenticação total é necessária para aceder a este recurso."

**Ação corretiva:**

Efetue uma das seguintes ações:

- Reexecutar o instalador para atualizar o Datahub com o nome de utilizador e a palavra-passe corretos.
- Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e depois verificar se o trabalho é bem sucedido.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centro Sentinel: URL errado ou site não acessível 

Mensagem de **insucesso de emprego:** "Oops, algo correu mal. A página a que estava a tentar aceder está (temporariamente) indisponível." 

**Ação corretiva:**
1. Abra o [Sentinel](https://scihub.copernicus.eu/dhus/) no seu navegador para ver se o site está acessível. 
2. Se o site não estiver acessível, verifique se qualquer firewall, rede da empresa ou outro software de bloqueio está impedindo o acesso ao site e, em seguida, tome as medidas necessárias para permitir o URL Sentinel. 
3. Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e depois verificar se o trabalho é bem sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor Sentinel: Para baixo para manutenção

**Mensagem de falha de emprego**: "O Centro de Acesso Aberto Copernicus estará de volta em breve! Desculpe pelo inconveniente, estamos fazendo alguma manutenção no momento. Estaremos de volta on-line em breve! 

**Ação corretiva:**

Este problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel.

1. Se algum trabalho ou oleoduto falhar porque a manutenção está a ser realizada, reenvie o trabalho após algum tempo. 

   Para obter informações sobre quaisquer atividades de manutenção planeadas ou não planeadas do Sentinel, aceda ao site [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e depois verificar se o trabalho é bem sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinela: Número máximo de ligações alcançadas

**Mensagem de falha de emprego**: "Número máximo de dois fluxos simultâneos alcançados pelo utilizador '\<username>'."

**Significa:** Se um trabalho falhar porque o número máximo de ligações foi atingido, a mesma conta Sentinel está a ser utilizada noutra implementação de software.

**Ação corretiva**: Tente qualquer uma das seguintes seguintes:

* Crie uma nova conta Sentinel e, em seguida, regere o instalador para atualizar o Datahub utilizando um novo nome de utilizador Sentinel e senha.  
* Reexecutar o trabalho falhado ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.

### <a name="sentinel-server-refused-connection"></a>Servidor Sentinel: Ligação recusada 

**Mensagem de falha de emprego**: "O servidor recusou a ligação a: http://172.30.175.69:8983/solr/dhus." 

**Ação corretiva**: Este problema pode ocorrer se alguma atividade de manutenção estiver a ser feita no servidor Sentinel. 
1. Se algum trabalho ou oleoduto falhar porque a manutenção está a ser realizada, reenvie o trabalho após algum tempo. 

   Para obter informações sobre quaisquer atividades de manutenção planeadas ou não planeadas do Sentinel, aceda ao site [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e depois verificar se o trabalho é bem sucedido.

## <a name="collect-logs-manually"></a>Recolher registos manualmente

[Instale e implemente o Azure Storage Explorer.]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

### <a name="collect-azure-data-factory-job-logs-in-datahub"></a>Recolher registos de trabalho da Azure Data Factory no Datahub

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.

    > [!NOTE]
    > Selecione o grupo de recursos Datahub que especificou durante a instalação FarmBeats.

3. No painel do **Grupo de Recursos,** procure os *datahublogs\** conta de armazenamento. Por exemplo, procure **datahublogsmvxmq**.  
4. Na coluna **Nome,** selecione a conta de armazenamento para visualizar o painel de **instrumentos da Conta de Armazenamento.**
5. Nos **datahubblogs\*** painel, selecione **Open in Explorer** para ver a aplicação Open **Azure Storage Explorer.**
6. No painel esquerdo, selecione **Recipientes Blob**, e, em seguida, selecione **registos de trabalho**.
7. No painel **de registos de trabalho,** selecione **Download**.
8. Descarregue os registos para uma pasta local na sua máquina.
9. Envie um e-mail para farmbeatssupport@microsoft.com.zip.

    ![Project FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-in-accelerator"></a>Recolher registos de trabalho da Azure Data Factory no Acelerador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos do Acelerador FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos acelerador estoiradores que especificou durante a instalação FarmBeats.

3. No painel do **Grupo de Recursos,** procure a conta *de armazenamento\** armazenamento. Por exemplo, procure **armazenamentodop4k\*** .
4. Selecione a conta de armazenamento na coluna **Nome** para visualizar o painel de **instrumentos da Conta de Armazenamento.**
5. No **painel de armazenamento\*,** selecione **Open in Explorer** para abrir a aplicação Azure Storage Explorer.
6. No painel esquerdo, selecione **Recipientes Blob**, e, em seguida, selecione **registos de trabalho**.
7. No painel **de registos de trabalho,** selecione **Download**.
8. Descarregue os registos para uma pasta local na sua máquina.
9. Envie um e-mail para farmbeatssupport@microsoft.com.zip.


### <a name="collect-datahub-app-service-logs"></a>Recolher registos de serviços de aplicações Datahub

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.

    > [!NOTE]
    > Selecione o grupo de recursos Datahub que especificou durante a instalação FarmBeats.

3. No grupo de recursos, procure os *datahublogs\** conta de armazenamento. Por exemplo, procure **por datahublogsmvxmq\*** .
4. Selecione a conta de armazenamento na coluna **Nome** para visualizar o painel de **instrumentos da Conta de Armazenamento.**
5. Nos **datahubblogs\*** painel, selecione **Open in Explorer** para abrir a aplicação Azure Storage Explorer.
6. No painel esquerdo, selecione **Recipientes Blob**, e, em seguida, selecione **registos de insights de aplicações**.
7. No painel de **registos de informações de aplicações,** selecione **Download**.
8. Descarregue os registos para uma pasta local na sua máquina.
9. Envie um e-mail para farmbeatssupport@microsoft.com.zip.

### <a name="collect-accelerator-app-service-logs"></a>Recolher registos de serviços de aplicações accelerator

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos do Acelerador FarmBeats.

    > [!NOTE]
    > Selecione o grupo de recursos farmBeats Accelerator que foi fornecido durante a instalação FarmBeats.

3. No grupo de recursos, procure a conta *de armazenamento\** armazenamento. Por exemplo, procure **armazenamentodop4k\*** .
4. Selecione a conta de armazenamento na coluna **Nome** para visualizar o painel de **instrumentos da Conta de Armazenamento.**
5. No **painel de armazenamento\*,** selecione **Open in Explorer** para abrir a aplicação Azure Storage Explorer.
6. No painel esquerdo, selecione **Recipientes Blob**, e, em seguida, selecione **registos de insights de aplicações**.
7. No painel de **registos de informações de aplicações,** selecione **Download**.
8. Descarregue os registos para uma pasta local na sua máquina.
9. Envie um e-mail para a pasta descarregada para farmbeatssupport@microsoft.com.

## <a name="known-issues"></a>Problemas conhecidos

## <a name="batch-related-issues"></a>Questões relacionadas com lotes

**Mensagem de erro**: "Foi atingido o contingente de contas regionais das Contas de Lote para a subscrição especificada."

**Ação corretiva**: Aumente a quota ou elimine as contas de lote não utilizadas e reecorra a implantação.

### <a name="azure-active-directory-azure-ad-related-issues"></a>Questões relacionadas com o Diretório Ativo Azure (Azure AD)

**Error message**: "Não consegui atualizar as definições necessárias para a App Azure AD d41axx40-xx21-4fbd-8xxf-97xxx9e2xxc0: Privilégios insuficientes para completar a operação. Certifique-se de que as definições acima estão configuradas corretamente para a App AD Azure."

**Ou seja:** A configuração de registo da aplicação Azure AD não foi concluída corretamente.  

**Ação corretiva**: Peça ao administrador de TI (a pessoa com acesso ao arrendatário) para usar o nosso [script](https://github.com/Azure-Samples/active-directory-dotnet-webapp-openidconnect/tree/master/AppCreationScripts) para criar o registo da aplicação Azure AD. Este script também cuida automaticamente dos passos de configuração.

**Mensagem de erro**: "Não foi possível criar uma nova aplicação de diretório ativo '\<nome de aplicação\>' neste inquilino: Outro objeto com o mesmo valor para identificadores de imóveis URIs já existe."

**Significando:** Já existe um registo de aplicações Azure AD com o mesmo nome.

**Ação corretiva**: Eliminar o registo da aplicação Azure AD existente ou reutilizá-la para instalação. Se estiver a reutilizar o registo de aplicações Azure AD existente, passe o ID da aplicação e o segredo do cliente para o instalador e reimplemente.

## <a name="issues-with-the-inputjson-file"></a>Problemas com o ficheiro input.json

**Erro**: Há uma entrada de leitura de erro do ficheiro *input.json.*

**Ação corretiva**: Este problema surge geralmente devido a um erro na especificação da correta via de ficheiro *son.json* ou nome do instalador. Faça as correções adequadas e tente novamente a implantação.

**Erro**: Há um erro de análise de valores no ficheiro *input.json.*

**Ação corretiva**: Esta questão surge principalmente devido a valores incorretos dentro do ficheiro *input.json.* Faça as correções adequadas e tente novamente a implantação.

## <a name="high-cpu-usage"></a>Elevada utilização da CPU

**Erro**: Recebe um alerta de e-mail que se refere a um alerta de utilização de **CPU .** 

**Ação corretiva:** 
1. Vá ao seu grupo de recursos FarmBeats Datahub.
2. Selecione o **serviço App**.  
3. Vá à página de preços do Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/windows/)e, em seguida, selecione um nível de preços apropriado.

## <a name="next-steps"></a>Passos Seguintes

Se ainda estiver a enfrentar problemas da FarmBeats, contacte o nosso Fórum de [Apoio](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats).
