---
title: Resolver problemas do Azure FarmBeats
description: Este artigo descreve como resolver problemas a Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-ummehabiba
ms.openlocfilehash: c45b6196b82682b37e253a33eed3940b68b4d61e
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/05/2021
ms.locfileid: "102172989"
---
# <a name="troubleshoot-azure-farmbeats"></a>Resolver problemas do Azure FarmBeats

Este artigo fornece soluções para problemas comuns do Azure FarmBeats. Para obter ajuda adicional, contacte o nosso [Q&A Support Forum](/answers/topics/azure-farmbeats.html) ou envie-nos um e-mail para farmbeatssupport@microsoft.com .

> [!NOTE]
  > Se instalou FarmBeats durante o mês de abril e os seus trabalhos estão a falhar com uma mensagem de erro vazia, a sua instalação pode não ter sido atribuída qualquer quota de lote para priorizar o apoio a organizações críticas de saúde e segurança. Veja [aqui](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) para obter mais informações. Terá de solicitar que os VM sejam atribuídos à conta Batch para executar empregos com sucesso.

## <a name="install-issues"></a>Instalar problemas

  > [!NOTE]
  > Se estiver a reiniciar a instalação devido a um erro, certifique-se de eliminar o **Grupo de Recursos** ou de eliminar todos os recursos do Grupo de Recursos antes de voltar a ativar a instalação.

### <a name="invalid-sentinel-credentials"></a>Credenciais sentinelas inválidas

As credenciais Sentinel fornecidas durante a instalação estão incorretas. Reinicie a instalação com as credenciais corretas.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>A quota de conta regional das contas de lote para a subscrição especificada foi alcançada

Aumente a quota ou elimine as contas de lote não reutilizadas e reinicie a instalação.

### <a name="invalid-resource-group-location"></a>Localização inválida do grupo de recursos invá

Certifique-se de que o **Grupo de Recursos** está no mesmo local que a **Região** especificada durante a instalação.

### <a name="other-install-issues"></a>Outros problemas de instalação

Contacte-nos com os seguintes detalhes:

- O seu ID de subscrição
- Nome do Grupo de Recursos
- Siga os passos abaixo para anexar o ficheiro de registo para a falha de implementação:

    1. Navegue para o **Grupo de Recursos** no portal Azure.

    2. Selecione **Implementações** na secção **Definições** do lado esquerdo.

    3. Para cada implementação que mostrar **Falhado,** selecione para os detalhes e descarregue os detalhes da implementação. Anexe este ficheiro ao correio.

## <a name="sensor-telemetry"></a>Telemetria sensorial

### <a name="cant-view-telemetry-data"></a>Não é possível ver dados de telemetria

**Sintoma**: Dispositivos ou sensores são implantados e ligou FarmBeats ao seu parceiro de dispositivo, mas não consegue obter ou visualizar dados de telemetria em FarmBeats.

**Ação corretiva**

1. Vá ao seu grupo de recursos FarmBeats.
2. Selecione o espaço de **nomes do Event Hub** ("sensor-partner-eh-namespace-xxxx"), clique em "Event Hubs" e, em seguida, verifique o número de mensagens recebidas no centro de eventos que é atribuído ao parceiro
3. Efetue um dos seguintes procedimentos:

   - Se não houver *mensagens recebidas,* contacte o seu parceiro de dispositivo.  
   - Se houver *mensagens recebidas,* contacte-nos com os seus registos Datahub e Accelerator e capture a telemetria.

Para entender como descarregar registos, aceda à secção ["Recolher registos manualmente".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível ver dados de telemetria depois de ingerir dados históricos/de streaming dos seus sensores

**Sintoma**: Dispositivos ou sensores são implantados, e você criou os dispositivos/sensores em FarmBeats e ingeriu telemetria para o EventHub, mas você não pode obter ou ver dados de telemetria em FarmBeats.

**Ação corretiva**

1. Certifique-se de que fez o registo do parceiro corretamente - pode verificar isso indo ao seu datahub swagger, navegar para /Partner API, Fazer um Get e verificar se o parceiro está registado. Caso contrário, siga estes [passos](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar o parceiro.

2. Certifique-se de que utilizou o formato correto da mensagem de telemetria:

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
          "<sensor measure name (as defined in the Sensor Model)>":<value>
        },
        {
          "timestamp": "<timestamp in ISO 8601 format>",
          "<sensor measure name (as defined in the Sensor Model)>": <value>
        }
      ]
    }
 ]
}
```

### <a name="dont-have-the-azure-event-hubs-connection-string"></a>Não tem a cadeia de conexão Azure Event Hubs

**Ação corretiva**

1. Em Datahub Swagger, vá à API parceira.
2. **Selecione**  >  **Experimente executar**  >  .

> [!NOTE]
> A identificação do parceiro do parceiro de sensores que lhe interessa.

3. Volte para a API do Parceiro e selecione **Get/ \<ID>**.
4. Especifique o ID do parceiro a partir do passo 3 e, em seguida, **selecione Executar**.

   A resposta da API deve ter a cadeia de ligação Event Hubs.

### <a name="device-appears-offline"></a>Dispositivo aparece offline

**Sintomas**: Os dispositivos estão instalados e ligaste o FarmBeats ao teu parceiro de dispositivo. Os dispositivos estão online e a enviar dados de telemetria, mas parecem offline.

**Ação corretiva** O intervalo de reporte não está configurado para este dispositivo. Para definir o intervalo de reporte, contacte o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro de eliminação de um dispositivo

Enquanto está a apagar um dispositivo, poderá encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "O dispositivo é referenciado nos sensores: Existem um ou mais sensores associados ao dispositivo. Elimine os sensores e, em seguida, elimine o dispositivo."  

**Significado:** O dispositivo está associado a múltiplos sensores que são implantados na quinta.

**Ação corretiva**  

1. Elimine os sensores associados ao dispositivo através do Acelerador.  
2. Se pretender associar os sensores a um dispositivo diferente, peça ao seu parceiro de dispositivo para fazer o mesmo.  
3. Elimine o dispositivo utilizando uma `DELETE API` chamada e desate o parâmetro de força como *verdadeiro*.  

**Mensagem**: "O dispositivo é referenciado em dispositivos como ParentDeviceId: Existem um ou mais dispositivos que estão associados a este dispositivo como dispositivos infantis. Apague-os e, em seguida, elimine este dispositivo."  

**Ou seja:** O seu dispositivo tem outros dispositivos associados a ele.  

**Ação corretiva**

1. Elimine os dispositivos associados a este dispositivo específico.  
2. Elimine o dispositivo específico.  

    > [!NOTE]
    > Não é possível apagar um dispositivo se os sensores estiverem associados. Para obter mais informações sobre como eliminar sensores associados, consulte a secção **de sensores Delete** in [Get sensor data from sensor partners](get-sensor-data-from-sensor-partner.md).
    > Os parceiros não têm permissão para eliminar um dispositivo ou sensor. Só os Administradores têm permissão para apagar.

## <a name="issues-with-jobs"></a>Problemas com empregos

### <a name="farmbeats-internal-error"></a>Erro interno do FarmBeats

**Mensagem**: "FarmBeats erro interno, consulte o guia de resolução de problemas para obter mais detalhes."

**Ação corretiva** Esta questão pode resultar de uma falha temporária no gasoduto de dados. Criar o trabalho de novo. Se o erro persistir, contacte-nos com a mensagem de erro/registos.

## <a name="accelerator-troubleshooting"></a>Resolução de problemas do acelerador

### <a name="access-control"></a>Controlo de acesso

**Problema**: Recebe um erro enquanto adiciona uma atribuição de função.

**Mensagem:**"Não foram encontrados utilizadores correspondentes."

**Ação corretiva** Verifique o ID de e-mail para o qual está a tentar adicionar uma atribuição de funções. O ID de e-mail deve ser uma correspondência exata do ID, que está registado para esse utilizador no Diretório Ativo. Se o erro persistir, contacte-nos com a mensagem de erro/registos.

### <a name="unable-to-log-in-to-accelerator"></a>Incapaz de iniciar sessão no Acelerador

**Mensagem**: "Erro: Não está autorizado a ligar para o serviço. Contacte o administrador para obter autorização."

**Ação corretiva** Peça ao administrador que o autorize a aceder à implantação farmbeats. Isto pode ser feito fazendo um POST das APIs de Assinatura de Função ou através do Controlo de Acesso no painel **de Definições** no Acelerador.  

Se já lhe foi concedido acesso e enfrentar este erro, tente novamente refrescando a página. Se o erro persistir, contacte-nos com a mensagem de erro/registos.

![Screenshot que mostra o erro de autorização.](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas de acelerador  

**Problema**: Recebeu um erro de aceleração de causa indeterminada.

**Mensagem**: "Erro: ocorreu um erro desconhecido."

**Ação corretiva** Este erro ocorre se deixar a página inativa por muito tempo. Atualize a página. Se o erro persistir, contacte-nos com a mensagem de erro/registos.

**Problema**: FarmBeats Accelerator não está a mostrar a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva** Este erro ocorre devido à persistência do trabalhador de serviço no navegador. Faça o seguinte:

1. Feche todos os separadores de navegador que tenham o Accelerator aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o Accelerator URI. Esta ação carrega a nova versão do Acelerador.

## <a name="sentinel-imagery-related-issues"></a>Sentinela: Questões relacionadas com imagens

### <a name="wrong-username-or-password"></a>Nome de utilizador errado ou senha

**Mensagem de insuficiência de emprego**: "A autenticação total é necessária para aceder a este recurso."

**Ação corretiva**: Faça uma das seguintes:

- Atualize FarmBeats com o nome de utilizador/palavra-passe correto utilizando os passos abaixo e reveja o trabalho.

  **Atualizar nome de utilizador Sentinel**

    1. Inscreva-se no [portal Azure](https://portal.azure.com).
    2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.
    3. Selecione armazenamento de conta de armazenamento***** > **contentores** de  >  **ficheiros de lote**  >  **to_vm**  >  **config.ini**
    4. Selecione **Editar**
    5. Atualizar o nome de utilizador na secção sentinel_account

  **Atualizar palavra-passe sentinela**

    1. Inscreva-se no [portal Azure](https://portal.azure.com).
    2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.
    3. Selecione keyvault-****
    4. Selecione políticas de acesso em definições
    5. Selecione **Adicionar Política de Acesso**
    6. Use **a gestão secreta** para configurar do modelo e adicione-se ao Principal
    7. Selecione **Adicionar** e, em seguida, selecione **Guardar** na página **'Políticas de Acesso'**
    8. Selecione **segredos** em **Definições**
    9. Selecione **palavra-passe sentinela**
    10. Crie uma nova versão do valor e ative-o.

- Re-executar o trabalho falhado, ou executar um trabalho de índice de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centro sentinela: URL errado ou site não acessível

**Mensagem de insuficiência de emprego:**"Oops, algo correu mal. A página a que estava a tentar aceder está (temporariamente) indisponível."

**Ação corretiva:**

1. Abra [o Sentinel](https://scihub.copernicus.eu/dhus/) no seu browser para ver se o site está acessível.
2. Se o site não estiver acessível, verifique se qualquer firewall, rede da empresa ou outro software de bloqueio está impedindo o acesso ao site e, em seguida, tome as medidas necessárias para permitir o URL Sentinel. 
3. Re-executar o trabalho falhado, ou executar um trabalho de índice de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor Sentinel: Para baixo para manutenção

**Mensagem de insucesso de emprego**: "O Copernicus Open Access Hub estará de volta em breve! Desculpe pelo inconveniente, estamos a fazer alguma manutenção no momento. Vamos estar de volta on-line em breve! 

**Ação corretiva:**

Este problema pode ocorrer se estiverem a ser feitas atividades de manutenção no servidor Sentinel.

1. Se algum trabalho ou gasoduto falhar porque a manutenção está a ser realizada, reenvia o trabalho após algum tempo. 

   Para obter informações sobre quaisquer atividades de manutenção planeadas ou não planeadas do Sentinel, aceda ao site [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Re-executar o trabalho falhado, ou executar um trabalho de índice de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinela: Número máximo de ligações alcançadas

**Mensagem de insuficiência de emprego**: "Número máximo de dois fluxos simultâneos alcançados pelo utilizador ' ' \<username> ."

**Ou seja:** Se um trabalho falhar porque o número máximo de ligações foi atingido, a mesma conta Sentinel está a ser utilizada em múltiplos postos de trabalho.

**Ação corretiva**: Experimente qualquer uma das seguintes:

* Espere que os outros trabalhos terminem antes de recandidatar o trabalho falhado.
* Crie uma nova conta Sentinel e, em seguida, atualize o nome de utilizador sentinel e a palavra-passe em FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Servidor Sentinel: Ligação recusada

**Mensagem de falha de emprego**: "Servidor recusou ligação em: http://172.30.175.69:8983/solr/dhus ."

**Ação corretiva**: Este problema pode ocorrer se estiverem a ser feitas atividades de manutenção no servidor Sentinel.

1. Se algum trabalho ou gasoduto falhar porque a manutenção está a ser realizada, reenvia o trabalho após algum tempo.

   Para obter informações sobre quaisquer atividades de manutenção planeadas ou não planeadas do Sentinel, aceda ao site [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Re-executar o trabalho falhado, ou executar um trabalho de índice de satélite para um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.

### <a name="soil-moisture-map-has-white-areas"></a>Mapa da humidade do solo tem áreas brancas

**Edição**: O **mapa da humidade do solo** foi gerado, mas o mapa tem principalmente áreas brancas.

**Ação corretiva**: Esta questão pode ocorrer se os índices de satélite gerados pelo tempo para o qual o mapa foi solicitado ndvi mentem valores inferiores a 0,3. Para mais informações, visite [o Guia Técnico da Sentinela.](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)

1. Volte a fazer o trabalho para um intervalo de datas diferente e verifique se os valores NDVI nos índices de satélite são superiores a 0,3.

## <a name="collect-logs-manually"></a>Recolher registos manualmente

[Instale e instale o Explorador de Armazenamento Azure]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows).

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Recolher registos de trabalho da Azure Data Factory ou registos de Serviços de Aplicações no Datahub

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.
3. No painel de instrumentos **do Grupo de Recursos,** procure a conta de armazenamento *de datahublogs. \** Por exemplo, *datahublogsmvxmq*.  
4. Na coluna **Nome,** selecione a conta de armazenamento para visualizar o painel **de instrumentos da Conta de Armazenamento.**
5. Nos **datahubblogs \* *_ painel, selecione _* Abra no Explorer** para ver a aplicação Open **Azure Storage Explorer.**
6. No painel esquerdo, selecione **Blob Containers** e, em seguida, selecione **registos de trabalho** para registos da Azure Data Factory ou **registos de aplicações** para registos do Serviço de Aplicações.
7. Selecione **Baixar** e transferir os registos para uma pasta local na sua máquina.

    ![Screenshot que mostra os ficheiros de registo descarregados.](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Recolher registos de trabalho da Azure Data Factory ou registos de Serviço de Aplicações para Acelerador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos farmbeats accelerator.
3. No painel de instrumentos **do Grupo de Recursos,** procure *\* a* conta de armazenamento. Por exemplo, *armazenamento 4k \**.
4. Selecione a conta de armazenamento na coluna **Nome** para ver o painel **de instrumentos da Conta de Armazenamento.**
5. No **armazenamento \* *_ painel, selecione _* Abra no Explorer** para abrir a aplicação Azure Storage Explorer.
6. No painel esquerdo, selecione **Blob Containers** e, em seguida, selecione **registos de trabalho** para registos da Azure Data Factory ou **registos de aplicações** para registos do Serviço de Aplicações.
7. Selecione **Baixar** e transferir os registos para uma pasta local na sua máquina.

## <a name="high-cpu-usage"></a>Elevada utilização da CPU

**Erro**: Recebe um alerta de e-mail que se refere a um **Alerta de Utilização de CPU Elevado**.

**Ação corretiva:**

1. Vá ao seu grupo de recursos FarmBeats Datahub.
2. Selecione o **serviço App**.  
3. Vá à página de preços do [Serviço de Aplicações](https://azure.microsoft.com/pricing/details/app-service/windows/)de escala e, em seguida, selecione um nível de preços apropriado.

## <a name="weather-data-job-failures"></a>Falhas no emprego dos dados meteorológicos

**Erro:** Você gere jobs para obter dados meteorológicos, mas o trabalho falha

### <a name="collect-logs-to-troubleshoot-weather-data-job-failures"></a>Recolher registos para resolver problemas de falhas no emprego de dados meteorológicos

1. Vá ao seu grupo de recursos FarmBeats no portal Azure.
2. Clique no serviço Data Factory que faz parte do grupo de recursos. O serviço terá uma etiqueta "sku: Datahub"

> [!NOTE]
> Para ver as etiquetas dos serviços dentro do grupo de recursos, clique em "Editar Colunas" e adicione "Tags" à vista do grupo de recursos

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-1.png" alt-text="Screenshot que destaca a etiqueta sku:Datahub.":::

3. Na página geral da fábrica de dados, clique em **Autor e Monitor.** Um novo separador abre no seu navegador. Clique no **Monitor**

:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-2.png" alt-text="Screenshot que realça a opção do menu Monitor.":::

4. Verá uma lista de oleodutos que fazem parte da execução do trabalho meteorológico. Clique no Trabalho para o que pretende recolher registos
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-3.png" alt-text="Screenshot que realça a opção de menu de Pipeline executa e o trabalho selecionado.":::

5. Na página geral do pipeline, verá a lista de atividades. Tome nota dos IDs de execução das atividades para as quais pretende recolher registos
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-4.png" alt-text="Screenshot que mostra a lista de atividades é executado.":::

6. Volte para o seu grupo de recursos FarmBeats no portal Azure e clique na Conta de Armazenamento com o nome **datahublogs-XXXX**
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-5.png" alt-text="Screenshot que destaca a Conta de Armazenamento com o nome datahublogs-XXXX.":::

7. Clique em **contentores**  ->  **adfjobs**. Na caixa de Pesquisa, insira o trabalho Executar ID que notou no passo 5 acima.
 
:::image type="content" source="./media/troubleshoot-Azure-farmbeats/weather-log-6.png" alt-text="Projeto FarmBeats":::

8. O resultado da pesquisa conterá a pasta que tem os registos relativos ao trabalho. Descarregue os registos e farmbeatssupport@microsoft.com envie-os para assistência na depuragem do problema.