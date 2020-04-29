---
title: Resolver problemas do Azure FarmBeats
description: Este artigo descreve como resolver problemas com a Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: a40c64910260a2d63a529d25e1089fb618fcec1b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "81113496"
---
# <a name="troubleshoot"></a>Resolução de problemas

Este artigo fornece soluções para questões comuns da Azure FarmBeats. Para obter ajuda adicional, contacte o farmbeatssupport@microsoft.comnosso Fórum de [Suporte](https://social.msdn.microsoft.com/Forums/home?forum=ProjectFarmBeats) ou envie-nos um e-mail para .

> [!NOTE]
  > Se instalou FarmBeats durante o mês de abril e os seus trabalhos estão a falhar com uma mensagem de erro vazia, a sua instalação pode não ter sido atribuída qualquer quota de lote para priorizar o apoio a organizações críticas de saúde e segurança. Consulte [aqui](https://azure.microsoft.com/blog/update-2-on-microsoft-cloud-services-continuity/) mais informações. Terá de solicitar que os VMs sejam alocados à conta do Lote para executar em sucesso os postos de trabalho.

## <a name="install-issues"></a>Instalar problemas

  > [!NOTE]
  > Se estiver a reiniciar a instalação devido a um erro, certifique-se de eliminar o **Grupo de Recursos** ou eliminar todos os recursos do Grupo de Recursos antes de reiniciar a instalação.

### <a name="invalid-sentinel-credentials"></a>Credenciais Sentinelinas inválidas

As credenciais Sentinel fornecidas durante a instalação estão incorretas. Reiniciar a instalação com as credenciais corretas.

### <a name="the-regional-account-quota-of-batch-accounts-for-the-specified-subscription-has-been-reached"></a>O contingente de conta regional das Contas de Lote para a subscrição especificada foi atingido

Aumente a quota ou elimine as contas de lote não utilizadas e reinicie a instalação.

### <a name="invalid-resource-group-location"></a>Localização do grupo de recursos inválido

Certifique-se de que o **Grupo de Recursos** está no mesmo local que a **Região** especificada durante a instalação.

### <a name="other-install-issues"></a>Outros problemas de instalação

Contacte-nos com os seguintes detalhes:

- O seu ID de subscrição
- Nome do Grupo de Recursos
- Siga os passos abaixo para anexar o ficheiro de registo para a falha de implantação:

    1. Navegue para o **Grupo de Recursos** no portal Azure.

    2. Selecione **As implementações** na secção **Definições** do lado esquerdo.

    3. Para cada implementação que mostrar **Failed**, selecione através dos detalhes e descarregue os detalhes da implementação. Prenda este ficheiro ao correio.

## <a name="sensor-telemetry"></a>Telemetria do sensor

### <a name="cant-view-telemetry-data"></a>Não se pode ver dados de telemetria

**Sintoma**: Dispositivos ou sensores estão implantados e ligou farmBeats com o seu parceiro de dispositivo, mas não pode obter ou ver dados de telemetria no FarmBeats.

**Ação corretiva**

1. Vá ao seu grupo de recursos FarmBeats Datahub.
2. Selecione o Site de **Eventos** (DatafeedEventHubNamespace) e, em seguida, verifique o número de mensagens que chegam.
3. Efetue um dos seguintes procedimentos:

   - Se não houver *mensagens de entrada,* contacte o parceiro do dispositivo.  
   - Se houver *mensagens de entrada,* contacte-nos com os registos do Datahub e do Acelerador e a telemetria capturada.

Para entender como descarregar registos, aceda à secção ["Recolher registos manualmente".](#collect-logs-manually)  

### <a name="cant-view-telemetry-data-after-ingesting-historicalstreaming-data-from-your-sensors"></a>Não é possível visualizar dados de telemetria depois de ingerir dados históricos/de streaming dos seus sensores

**Sintoma**: Dispositivos ou sensores são implantados e você criou os dispositivos/sensores em FarmBeats e ingerido telemetria para o EventHub, mas você não pode obter ou ver dados de telemetria em FarmBeats.

**Ação corretiva**

1. Certifique-se de que fez o registo do parceiro corretamente - pode verificar isso indo para o seu datahub swagger, navegar para /Partner API, Fazer um Get e verificar se o parceiro está registado. Caso contrário, siga estes [passos](get-sensor-data-from-sensor-partner.md#enable-device-integration-with-farmbeats) para adicionar o parceiro.

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
          "<sensor measure name (as defined in the Sensor Model)>":"<value>"
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

**Ação corretiva**

1. Em Datahub Swagger, vá à API partner.
2. Selecione **Tente** > **Try it out** > **executar**.

> [!NOTE]
> A identificação do parceiro de sensores que te interessa.

3. Volte para a API partner e selecione **Get/ID\<>**.
4. Especifique o ID do parceiro a partir do passo 3 e, em seguida, selecione **Executar**.

   A resposta DaPI deve ter a cadeia de ligação Do Event Hubs.

### <a name="device-appears-offline"></a>Dispositivo aparece offline

**Sintomas**: Os dispositivos estão instalados e ligou o FarmBeats ao seu parceiro de dispositivo. Os dispositivos estão online e enviam dados de telemetria, mas parecem offline.

**Ação corretiva** O intervalo de reporte não está configurado para este dispositivo. Para definir o intervalo de reporte, contacte o fabricante do dispositivo. 

### <a name="error-deleting-a-device"></a>Erro ao apagar um dispositivo

Enquanto está a apagar um dispositivo, pode encontrar um dos seguintes cenários de erro comuns:  

**Mensagem**: "O dispositivo é referenciado nos sensores: Existem um ou mais sensores associados ao dispositivo. Apague os sensores e, em seguida, apague o dispositivo."  

**Significando:** O dispositivo está associado a múltiplos sensores que são implantados na exploração.

**Ação corretiva**  

1. Elimine os sensores associados ao dispositivo através do Acelerador.  
2. Se quiser associar os sensores a um dispositivo diferente, peça ao seu parceiro do dispositivo para fazer o mesmo.  
3. Elimine o dispositivo `DELETE API` utilizando uma chamada e coloque o parâmetro de força *como verdadeiro*.  

**Mensagem**: "O dispositivo é referenciado em dispositivos como ParentDeviceId: Existem um ou mais dispositivos que estão associados a este dispositivo como dispositivos infantis. Apague-os e, em seguida, apague este dispositivo."  

**Ou seja:** O seu dispositivo tem outros dispositivos associados.  

**Ação corretiva**

1. Elimine os dispositivos associados a este dispositivo específico.  
2. Elimine o dispositivo específico.  

    > [!NOTE]
    > Não é possível apagar um dispositivo se os sensores estiverem associados a ele. Para obter mais informações sobre como eliminar os sensores associados, consulte a secção **de sensores Delete** in [Get dados de sensores dos parceiros de sensores](get-sensor-data-from-sensor-partner.md).
    > Os parceiros não têm permissão para apagar um dispositivo ou sensor. Só os administradores têm permissão para apagar.

## <a name="issues-with-jobs"></a>Questões com emprego

### <a name="farmbeats-internal-error"></a>Erro interno FarmBeats

**Mensagem**: "FarmBeats erro interno, consulte guia de resolução de problemas para mais detalhes."

**Ação corretiva** Esta questão pode resultar de uma falha temporária no gasoduto de dados. Criar o trabalho de novo. Se o erro persistir, contacte-nos com a mensagem/registos de erro.

## <a name="accelerator-troubleshooting"></a>Resolução de problemas do acelerador

### <a name="access-control"></a>Controlo de acesso

**Emissão**: Recebe um erro enquanto adiciona uma atribuição de funções.

**Mensagem**: "Não foram encontrados utilizadores correspondentes."

**Ação corretiva** Verifique o ID de e-mail para o qual está a tentar adicionar uma atribuição de funções. O ID de e-mail deve ser uma correspondência exata do ID, que está registado para esse utilizador no Diretório Ativo. Se o erro persistir, contacte-nos com a mensagem/registos de erro.

### <a name="unable-to-log-in-to-accelerator"></a>Incapaz de iniciar sessão no Acelerador

**Mensagem**: "Erro: Não está autorizado a ligar para o serviço. Contacte o administrador para obter autorização."

**Ação corretiva** Peça ao administrador que o autorize a aceder à implementação do FarmBeats. Isto pode ser feito fazendo uma publicação das APIs de Atribuição de Funções ou através do Controlo de Acesso no painel **definições** no Acelerador.  

Se já lhe foi concedido acesso e enfrentando este erro, tente novamente refrescando a página. Se o erro persistir, contacte-nos com a mensagem/registos de erro.

![Project FarmBeats](./media/troubleshoot-azure-farmbeats/accelerator-troubleshooting-1.png)

### <a name="accelerator-issues"></a>Problemas de acelerador  

**Problema**: Recebeu um erro acelerador de causa indeterminada.

**Mensagem**: "Erro: Ocorreu um erro desconhecido".

**Ação corretiva** Este erro ocorre se deixar a página inativa por muito tempo. Atualize a página. Se o erro persistir, contacte-nos com a mensagem/registos de erro.

**Problema**: FarmBeats Accelerator não está a mostrar a versão mais recente, mesmo depois de ter atualizado o FarmBeatsDeployment.

**Ação corretiva** Este erro ocorre devido à persistência do trabalhador de serviço no navegador. Faça o seguinte:

1. Feche todos os separadores do navegador que tenham o Acelerador aberto e feche a janela do navegador.
2. Inicie uma nova instância do navegador e recarregue o Acelerador URI. Esta ação carrega a nova versão do Acelerador.

## <a name="sentinel-imagery-related-issues"></a>Sentinela: Questões relacionadas com imagens

### <a name="wrong-username-or-password"></a>Nome de utilizador errado ou senha

**Mensagem de falha de emprego**: "A autenticação total é necessária para aceder a este recurso."

**Ação corretiva**: Faça um dos seguintes:

- Atualizar FarmBeats com o nome de utilizador/senha correto utilizando os passos abaixo e voltar a tentar o trabalho.

  **Atualizar nome de utilizador Sentinel**

    1. Inicie sessão no [portal do Azure](https://portal.azure.com).
    2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.
    3. Selecione armazenamento da conta de armazenamento***** > **contentores** > **ficheiros de** > preparação**to_vm** > **config.ini**
    4. **Selecione Editar**
    5. Atualize o nome de utilizador na secção sentinel_account

  **Atualizar palavra-passe do Sentinel**

    1. Inicie sessão no [portal do Azure](https://portal.azure.com).
    2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.
    3. Selecione keyvault-*****
    4. Selecione Políticas de Acesso em Definições
    5. **Selecione Adicionar Política de Acesso**
    6. Use **a gestão secreta** para configurar a partir do modelo e adicione-se ao Principal
    7. Selecione **Adicionar**, e, em seguida, selecione **Guardar** na página Políticas de **Acesso**
    8. Selecione **Segredos** em **Definições**
    9. Selecione **Palavra-passe sentinela**
    10. Crie uma nova versão do valor e ative-a.

- Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e depois verificar se o trabalho é bem sucedido.

### <a name="sentinel-hub-wrongurlor-site-not-accessible"></a>Centro Sentinel: URL errado ou site não acessível

Mensagem de **insucesso de emprego:**"Oops, algo correu mal. A página a que estava a tentar aceder está (temporariamente) indisponível."

**Ação corretiva:**

1. Abra o [Sentinel](https://scihub.copernicus.eu/dhus/) no seu navegador para ver se o site está acessível.
2. Se o site não estiver acessível, verifique se qualquer firewall, rede da empresa ou outro software de bloqueio está impedindo o acesso ao site e, em seguida, tome as medidas necessárias para permitir o URL Sentinel. 
3. Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.  

### <a name="sentinel-server-down-for-maintenance"></a>Servidor Sentinel: Para baixo para manutenção

**Mensagem de falha de emprego**: "O Centro de Acesso Aberto Copernicus estará de volta em breve! Desculpe pelo inconveniente, estamos fazendo alguma manutenção no momento. Estaremos de volta on-line em breve! 

**Ação corretiva:**

Este problema pode ocorrer se alguma atividade de manutenção estiver sendo feita no servidor Sentinel.

1. Se algum trabalho ou oleoduto falhar porque a manutenção está a ser realizada, reenvie o trabalho após algum tempo. 

   Para obter informações sobre quaisquer atividades de manutenção planeadas ou não planeadas do Sentinel, aceda ao site [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.

### <a name="sentinel-maximum-number-of-connections-reached"></a>Sentinela: Número máximo de ligações alcançadas

**Mensagem de falha de emprego**: "Número máximo de\<dois fluxos simultâneos alcançados pelo 'username>'."

**Significa:** Se um trabalho falhar porque o número máximo de ligações foi atingido, a mesma conta Sentinel está a ser utilizada em múltiplos postos de trabalho.

**Ação corretiva**: Tente qualquer uma das seguintes seguintes:

* Espere que os outros trabalhos terminem antes de reexecutar o trabalho falhado.
* Crie uma nova conta Sentinel e, em seguida, atualize o nome de utilizador sentinel e a palavra-passe no FarmBeats.

### <a name="sentinel-server-refused-connection"></a>Servidor Sentinel: Ligação recusada

**Mensagem de falha de emprego** http://172.30.175.69:8983/solr/dhus: "O servidor recusou a ligação a: ."

**Ação corretiva**: Este problema pode ocorrer se alguma atividade de manutenção estiver a ser feita no servidor Sentinel.

1. Se algum trabalho ou oleoduto falhar porque a manutenção está a ser realizada, reenvie o trabalho após algum tempo.

   Para obter informações sobre quaisquer atividades de manutenção planeadas ou não planeadas do Sentinel, aceda ao site [Copernicus Open Access Hub News.](https://scihub.copernicus.eu/news/)  

2. Reexecutar o trabalho falhado, ou executar um trabalho de índices de satélite por um intervalo de data de 5 a 7 dias, e, em seguida, verificar se o trabalho é bem sucedido.

### <a name="soil-moisture-map-has-white-areas"></a>Mapa da humidade do solo tem áreas brancas

**Emissão**: O mapa da humidade do **solo** foi gerado, mas o mapa tem principalmente áreas brancas.

**Ação corretiva**: Esta questão pode ocorrer se os índices de satélite gerados para o tempo para o qual o mapa foi solicitado tiver valores NDVI inferiores a 0,3. Para mais informações, visite [o Guia Técnico do Sentinel.](https://earth.esa.int/web/sentinel/technical-guides/sentinel-2-msi/level-2a/algorithm)

1. Refaça o trabalho para uma gama de datas diferente e verifique se os valores NDVI nos índices de satélite são superiores a 0,3.

## <a name="collect-logs-manually"></a>Recolher registos manualmente

[Instale e implemente o Azure Storage Explorer.]( https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer?tabs=windows)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-in-datahub"></a>Recolher registos de trabalho na Azure Data Factory ou registos do Serviço de Aplicações no Datahub

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos FarmBeats Datahub.
3. No painel do **Grupo de Recursos,** procure a conta de armazenamento de *datahublogs.\* * Por exemplo, *datahublogsmvxmq*.  
4. Na coluna **Nome,** selecione a conta de armazenamento para visualizar o painel de **instrumentos da Conta de Armazenamento.**
5. No painel **datahubblogs,\* ** selecione **Open in Explorer** para ver a aplicação Open **Azure Storage Explorer.**
6. No painel esquerdo, selecione **Blob Containers**, e, em seguida, selecione **registos de trabalho** para registos da Azure Data Factory ou **registos de aplicações** para registos do Serviço de Aplicações.
7. Selecione **Baixar** e transferir os registos para uma pasta local na sua máquina.

    ![Project FarmBeats](./media/troubleshoot-azure-farmbeats/collecting-logs-manually-1.png)

### <a name="collect-azure-data-factory-job-logs-or-app-service-logs-for-accelerator"></a>Recolher registos de trabalho na Azure Data Factory ou registos do Serviço de Aplicações para Acelerador

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na caixa **de pesquisa,** procure o grupo de recursos do Acelerador FarmBeats.
3. No painel do **Grupo de Recursos,** procure a conta de *armazenamento.\* * Por exemplo, *storagedop4k\**.
4. Selecione a conta de armazenamento na coluna **Nome** para visualizar o painel de **instrumentos da Conta de Armazenamento.**
5. No painel de **armazenamento,\* ** selecione **Open in Explorer** para abrir a aplicação Azure Storage Explorer.
6. No painel esquerdo, selecione **Blob Containers**, e, em seguida, selecione **registos de trabalho** para registos da Azure Data Factory ou **registos de aplicações** para registos do Serviço de Aplicações.
7. Selecione **Baixar** e transferir os registos para uma pasta local na sua máquina.

## <a name="high-cpu-usage"></a>Elevada utilização da CPU

**Erro**: Recebe um alerta de e-mail que se refere a um alerta de utilização de **CPU .**

**Ação corretiva:**

1. Vá ao seu grupo de recursos FarmBeats Datahub.
2. Selecione o **serviço App**.  
3. Vá à página de preços do Serviço de [Aplicações](https://azure.microsoft.com/pricing/details/app-service/windows/)e, em seguida, selecione um nível de preços apropriado.
