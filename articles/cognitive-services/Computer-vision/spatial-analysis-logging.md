---
title: Telemetria e registo de contentores de análise espacial
titleSuffix: Azure Cognitive Services
description: A Análise Espacial fornece a cada recipiente uma configuração comum insights, registos e definições de segurança.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: conceptual
ms.date: 01/12/2021
ms.author: aahi
ms.openlocfilehash: dda3ece27fd2c687647e0aa289bd1596a87b274f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "98186027"
---
# <a name="telemetry-and-troubleshooting"></a>Telemetria e resolução de problemas

A análise espacial inclui um conjunto de funcionalidades para monitorizar o estado de funcionamento do sistema e ajudar a diagnosticar problemas.

## <a name="enable-visualizations"></a>Permitir visualizações

Para ativar uma visualização de eventos de AI Insights numa moldura de vídeo, é necessário utilizar a `.debug` versão de uma [operação](spatial-analysis-operations.md) de análise espacial numa máquina de ambiente de trabalho. A visualização não é possível nos dispositivos Azure Stack Edge. Há quatro operações de depuro disponíveis.

Se o seu dispositivo não for um dispositivo Azure Stack Edge, edite o ficheiro manifesto de implementação para [máquinas de ambiente de trabalho](https://github.com/Azure-Samples/cognitive-services-sample-data-files/blob/master/ComputerVision/spatial-analysis/DeploymentManifest_for_non_ASE_devices.json) para utilizar o valor correto para a `DISPLAY` variável ambiente. Precisa combinar com a `$DISPLAY` variável no computador anfitrião. Após a atualização do manifesto de implantação, reimplante o recipiente.

Depois de concluída a implementação, poderá ter de copiar o `.Xauthority` ficheiro do computador anfitrião para o recipiente e reiniciá-lo. Na amostra abaixo, `peopleanalytics` está o nome do recipiente no computador anfitrião.

```bash
sudo docker cp $XAUTHORITY peopleanalytics:/root/.Xauthority
sudo docker stop peopleanalytics
sudo docker start peopleanalytics
xhost +
```


## <a name="collect-system-health-telemetry"></a>Recolher telemetria de saúde do sistema

A Telegraf é uma imagem de código aberto que funciona com análise espacial, e está disponível no Registo de Contentores da Microsoft. Pega nas seguintes entradas e envia-as para o Azure Monitor. O módulo telegraf pode ser construído com entradas e saídas personalizadas desejadas. A configuração do módulo telegraf na análise espacial faz parte do manifesto de implantação (ligado acima). Este módulo é opcional e pode ser removido do manifesto se não precisar. 

Entradas: 
1. Análise espacial Métricas
2. Métricas de disco
3. Métricas do CPU
4. Métricas docker
5. Métricas gpu

Saídas:
1. Azure Monitor

O módulo de análise espacial fornecido telegraf publicará todos os dados de telemetria emitidos pelo recipiente de análise espacial ao Azure Monitor. Consulte o [Monitor Azure](../../azure-monitor/overview.md) para obter informações sobre a adição do Monitor Azure à sua subscrição.

Depois de configurar o Azure Monitor, terá de criar credenciais que permitam ao módulo enviar telemetria. Pode utilizar o portal Azure para criar um novo Diretor de Serviços ou utilizar o comando Azure CLI abaixo para criar um.

> [!NOTE] 
> Este comando requer que tenha privilégios do Proprietário na subscrição. 

```bash
# Find your Azure IoT Hub resource ID by running this command. The resource ID  should start with something like 
# "/subscriptions/b60d6458-1234-4be4-9885-c7e73af9ced8/resourceGroups/...”
az iot hub list

# Create a Service Principal with `Monitoring Metrics Publisher` role in the IoTHub resource:
# Save the output from this command. The values will be used in the deployment manifest. The password won't be shown again so make sure to write it down
az ad sp create-for-rbac --role="Monitoring Metrics Publisher" --name "<principal name>" --scopes="<resource ID of IoT Hub>"
```

No manifesto de implantação do seu [dispositivo Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179) [máquina de ambiente de trabalho,](https://go.microsoft.com/fwlink/?linkid=2152270)ou [Azure VM com GPU,](https://go.microsoft.com/fwlink/?linkid=2152189)procure o módulo *telegraf,* e substitua os seguintes valores com as informações principais do serviço do passo anterior e da recolocação.

```json

"telegraf": { 
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/telegraf:1.0",
  "createOptions":   "{\"HostConfig\":{\"Runtime\":\"nvidia\",\"NetworkMode\":\"azure-iot-edge\",\"Memory\":33554432,\"Binds\":[\"/var/run/docker.sock:/var/run/docker.sock\"]}}"
},
"type": "docker",
"env": {
    "AZURE_TENANT_ID": {
        "value": "<Tenant Id>"
    },
    "AZURE_CLIENT_ID": {
        "value": "Application Id"
    },
    "AZURE_CLIENT_SECRET": {
        "value": "<Password>"
    },
    "region": {
        "value": "<Region>"
    },
    "resource_id": {
        "value": "/subscriptions/{subscriptionId}/resourceGroups/{resoureGroupName}/providers/Microsoft.Devices/IotHubs/{IotHub}"
    },
...
```

Uma vez implantado o módulo telegraf, as métricas reportadas podem ser acedidas através do serviço Azure Monitor ou selecionando **Monitor no** Hub IoT no portal Azure.

:::image type="content" source="./media/spatial-analysis/iot-hub-telemetry.png" alt-text="Relatório de telemetria do Monitor Azure":::

### <a name="system-health-events"></a>Eventos de saúde do sistema

| Nome do Evento | Description|
|------|---------|
|archon_exit    |Enviado quando um utilizador altera o estado do módulo de análise espacial de *correr* para *parar*.  |
|archon_error   |Enviado quando qualquer um dos processos dentro do contentor se despenhou. Isto é um erro crítico.  |
|InputRate  |A taxa a que o gráfico processa a entrada de vídeo. Reportado a cada 5 minutos. | 
|Taxa de saída     |A taxa a que o gráfico produz insights de IA. Reportado a cada 5 minutos. |
|archon_allGraphsStarted | Enviado quando todos os gráficos terminarem de começar. |
|archon_configchange    | Enviado quando uma configuração de gráfico mudou. |
|archon_graphCreationFailed     |Enviado quando o gráfico com o relato `graphId` não começa. |
|archon_graphCreationSuccess    |Enviado quando o gráfico com o relatado `graphId` começa com sucesso. |
|archon_graphCleanup    | Enviado quando o gráfico com o relato `graphId` limpa e sai. |
|archon_graphHeartbeat  |Batimentos cardíacos enviados a cada minuto por cada gráfico de uma habilidade. |
|archon_apiKeyAuthFail |Enviado quando a tecla de recursos de Visão Por Computador não autentica o recipiente por mais de 24 horas, devido às seguintes razões: Fora de Quota, Inválido, Offline. |
|VideoIngesterHeartbeat     |Enviado a cada hora para indicar que o vídeo é transmitido a partir da fonte de Vídeo, com o número de erros naquela hora. Reportado para cada gráfico. |
|Estado do VideoIngester | Relatórios *Parados* ou *Iniciados* para transmissão de vídeo. Reportado para cada gráfico. |

##  <a name="troubleshooting-an-iot-edge-device"></a>Resolução de problemas de um dispositivo de borda IoT

Pode utilizar `iotedge` a ferramenta da linha de comando para verificar o estado e os registos dos módulos de funcionamento. Por exemplo:
* `iotedge list`: Reporta uma lista de módulos de funcionamento. 
  Pode verificar se há erros com `iotedge logs edgeAgent` . Se `iotedge` ficar preso, pode tentar reiniciá-lo com `iotedge restart edgeAgent`
* `iotedge logs <module-name>`
* `iotedge restart <module-name>` para reiniciar um módulo específico 

## <a name="collect-log-files-with-the-diagnostics-container"></a>Recolher ficheiros de registo com o recipiente de diagnóstico

A análise espacial gera registos de depurar Docker que pode usar para diagnosticar problemas de tempo de execução, ou incluir em bilhetes de apoio. O módulo de diagnóstico de análise espacial está disponível no Registo de Contentores da Microsoft para que possa descarregar. No ficheiro de implantação manifesto para o seu [Azure Stack Edge Device,](https://go.microsoft.com/fwlink/?linkid=2142179) [máquina de ambiente de trabalho,](https://go.microsoft.com/fwlink/?linkid=2152270)ou [Azure VM com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) procure o módulo *de diagnóstico.*

Na secção "env" adicione a seguinte configuração:

```json
"diagnostics": {  
  "settings": {
  "image":   "mcr.microsoft.com/azure-cognitive-services/vision/spatial-analysis/diagnostics:1.0",
  "createOptions":   "{\"HostConfig\":{\"Mounts\":[{\"Target\":\"/usr/bin/docker\",\"Source\":\"/home/data/docker\",\"Type\":\"bind\"},{\"Target\":\"/var/run\",\"Source\":\"/run\",\"Type\":\"bind\"}],\"LogConfig\":{\"Config\":{\"max-size\":\"500m\"}}}}"
  }
```    

Para otimizar os registos enviados para um ponto final remoto, como o Azure Blob Storage, recomendamos manter um pequeno tamanho de ficheiro. Veja o exemplo abaixo para a configuração recomendada de registos do Docker.

```json
{
    "HostConfig": {
        "LogConfig": {
            "Config": {
                "max-size": "500m",
                "max-file": "1000"
            }
        }
    }
}
```

### <a name="configure-the-log-level"></a>Configure o nível de registo

A configuração do nível de registo permite-lhe controlar a verbosidade dos registos gerados. Os níveis de registo suportados são: `none` , , , e `verbose` `info` `warning` `error` . O nível verboso de registo predefinido para os nós e plataforma é `info` . 

Os níveis de registo podem ser modificados globalmente, definindo a `ARCHON_LOG_LEVEL` variável ambiental para um dos valores permitidos.
Também pode ser definido através do documento IoT Edge Module Twin, quer a nível global, para todas as competências implementadas, quer para todas as habilidades específicas, definindo os valores para `platformLogLevel` e `nodeLogLevel` como mostrado abaixo.

```json
{
    "version": 1,
    "properties": {
        "desired": {
            "globalSettings": {
                "platformLogLevel": "verbose"
            },
            "graphs": {
                "samplegraph": {
                    "nodeLogLevel": "verbose",
                    "platformLogLevel": "verbose"
                }
            }
        }
    }
}
```

### <a name="collecting-logs"></a>Recolha de Registos

> [!NOTE]
> O `diagnostics` módulo não afeta o conteúdo da registo, é apenas ajuda na recolha, filtragem e upload dos registos existentes.
> Tem de ter a versão API 1.40 ou superior para utilizar este módulo.

O ficheiro manifesto de implantação da amostra para o seu [dispositivo Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179) [máquina de ambiente de trabalho,](https://go.microsoft.com/fwlink/?linkid=2152270)ou [Azure VM com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) inclui um módulo nomeado `diagnostics` que recolhe e carrega registos. Este módulo é desativado por predefinição e deve ser ativado através da configuração do módulo IoT Edge quando necessitar de aceder a registos. 

A `diagnostics` coleção é a pedido e controlada através de um método direto IoT Edge, e pode enviar registos para um Azure Blob Storage.

### <a name="configure-diagnostics-upload-targets"></a>Configurar metas de upload de diagnósticos

A partir do portal IoT Edge, selecione o seu dispositivo e, em seguida, o módulo **de diagnóstico.** No ficheiro manifesto de implantação da amostra para o seu [dispositivo Azure Stack Edge,](https://go.microsoft.com/fwlink/?linkid=2142179) [máquinas de ambiente de trabalho,](https://go.microsoft.com/fwlink/?linkid=2152270)ou [VM Azure com GPU](https://go.microsoft.com/fwlink/?linkid=2152189) procurem a secção **de Variáveis ambientais** para diagnósticos, nomeados, `env` e adicione as seguintes informações:

**Configurar o upload para o armazenamento de blob Azure**

1. Crie a sua própria conta de Armazenamento Azure Blob, se ainda não o fez.
2. Obtenha a **Cadeia de Ligação** para a sua conta de armazenamento no portal Azure. Estará localizado em **Teclas de acesso.**
3. Os registos de análise espacial serão automaticamente enviados para um recipiente de Armazenamento Blob denominado *rtcvlogs* com o seguinte formato de nome de ficheiro: `{CONTAINER_NAME}/{START_TIME}-{END_TIME}-{QUERY_TIME}.log` .

```json
"env":{
    "IOTEDGE_WORKLOADURI":"fd://iotedge.socket",
    "AZURE_STORAGE_CONNECTION_STRING":"XXXXXX",   //from the Azure Blob Storage account
    "ARCHON_LOG_LEVEL":"info"
}
```

### <a name="uploading-spatial-analysis-logs"></a>Upload de registos de análise espacial

Os registos são carregados a pedido com o `getRTCVLogs` método IoT Edge, no `diagnostics` módulo. 


1. Vá à página do portal IoT Hub, selecione **Dispositivos edge** e, em seguida, selecione o seu dispositivo e o seu módulo de diagnóstico. 
2. Vá à página de detalhes do módulo e clique no ***separador método direto.***
3. Digite `getRTCVLogs` no Nome do Método e uma cadeia de formato json na carga útil. Pode `{}` entrar, que é uma carga vazia. 
4. Desa estaca de ligação e de tempo de método, e clique em **Invocar Método**.
5. Selecione o seu recipiente-alvo e construa uma corda de json de carga útil utilizando os parâmetros descritos na secção **de sintaxe de registo.** Clique **em Invocar Método** para realizar o pedido.

>[!NOTE]
> Invocar o `getRTCVLogs` método com uma carga útil vazia devolverá uma lista de todos os recipientes implantados no dispositivo. O nome do método é sensível a maiôs. Obterá um erro 501 se for dado um nome de método incorreto.

:::image type="content" source="./media/spatial-analysis/direct-log-collection.png" alt-text="Invocando o método getRTCVLogs ":::
![getRTCVLogs Página de método direto](./media/spatial-analysis/direct-log-collection.png)

 
### <a name="logging-syntax"></a>Sintaxe de registo

A tabela abaixo lista os parâmetros que pode utilizar ao consultar os registos.

| Palavra-chave | Description | Valor Predefinido |
|--|--|--|
| StartTime | Troncos desejados começam na hora de início, em milissegundos UTC. | `-1`, o início do tempo de funcionação do contentor. Quando `[-1.-1]` é usado como um intervalo de tempo, a API retorna os registos da última hora.|
| EndTime | Os troncos desejados terminam o tempo, em milissegundos UTC. | `-1`, a hora atual. Quando `[-1.-1]` o intervalo de tempo é utilizado, a API retorna os registos da última hora. |
| Contentor | Recipiente-alvo para recolher troncos.| `null`, quando não há identificação do contentor. A API devolve todas as informações de contentores disponíveis com IDs.|
| DoPost | Execute a operação de upload. Quando isto estiver `false` programado, executa a operação solicitada e devolve o tamanho do upload sem efetuar o upload. Quando definido para `true` , iniciará o upload assíncronos dos registos selecionados | `false`, não faça upload.|
| Limitação | Indicar quantas linhas de registos devem ser carregados por lote | `1000`, Utilize este parâmetro para ajustar a velocidade do poste. |
| Filtros | Registos de filtros a serem carregados | `null`, os filtros podem ser especificados como pares de valor-chave com base na estrutura dos registos de análise espacial: `[UTC, LocalTime, LOGLEVEL,PID, CLASS, DATA]` . Por exemplo: `{"TimeFilter":[-1,1573255761112]}, {"TimeFilter":[-1,1573255761112]}, {"CLASS":["myNode"]`|

A tabela seguinte lista os atributos na resposta de consulta.

| Palavra-chave | Description|
|--|--|
|DoPost| Verdadeiro  ou *falso.* Indica se os registos foram carregados ou não. Quando opta por não fazer o upload de registos, a api devolve informações *** sincronizadamente** _. Quando opta por fazer o upload de registos, a api devolve 200, se o pedido for válido, e começa a carregar registos _*_assíncronamente_**.|
|Filtro de tempo| Filtro de tempo aplicado aos troncos.|
|Filtros de Valor| Filtros de palavras-chave aplicados nos registos. |
|TimeStamp| Tempo de início da execução do método. |
|Contentor| Identificação do contentor do alvo. |
|FetchCounter| Número total de linhas de registo. |
|FetchSizeInByte| Quantidade total de dados de registo nos bytes. |
|MatchCounter| Número válido de linhas de registo. |
|MatchSizeInByte| Quantidade válida de bytes de registo de dados. |
|Contador de Filtros| Número total de linhas de registo após a aplicação do filtro. |
|FilterSizeInByte| Quantidade total de bytes de registo após a aplicação do filtro. |
|FetchLogsDurationInMiliSec| Buscar a duração da operação. |
|PaseLogsDurationInMiliSec| Duração do funcionamento do filtro. |
|PostLogsDurationInMiliSec| Duração pós-operação. |

#### <a name="example-request"></a>Pedido de exemplo 

```json
{
    "StartTime": -1,
    "EndTime": -1,
    "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
    "DoPost": false,
    "Filters": null
}
```

#### <a name="example-response"></a>Resposta de exemplo 

```json
{
    "status": 200,
    "payload": {
        "DoPost": false,
        "TimeFilter": [-1, 1581310339411],
        "ValueFilters": {},
        "Metas": {
            "TimeStamp": "2020-02-10T04:52:19.4365389+00:00",
            "ContainerId": "5fa17e4d8056e8d16a5a998318716a77becc01b36fde25b3de9fde98a64bf29b",
            "FetchCounter": 61,
            "FetchSizeInByte": 20470,
            "MatchCounter": 61,
            "MatchSizeInByte": 20470,
            "FilterCount": 61,
            "FilterSizeInByte": 20470,
            "FetchLogsDurationInMiliSec": 0,
            "PaseLogsDurationInMiliSec": 0,
            "PostLogsDurationInMiliSec": 0
        }
    }
}
```

Verifique as linhas, horas e tamanhos do log, se essas definições parecerem boas substituem o ***DoPost*** `true` e que empurrarão os registos com os mesmos filtros para destinos. 

Pode exportar registos do Azure Blob Storage quando resolver problemas. 

## <a name="common-issues"></a>Problemas comuns

Se vir a seguinte mensagem nos registos do módulo, pode significar que a sua subscrição do Azure precisa de ser aprovada: 

"O contentor não está num estado válido. A validação da subscrição falhou com o estado de 'Desajuste'. A Api Key não se destina ao tipo de recipiente dado."

Para obter mais informações, consulte [Pedir a aprovação para executar o recipiente.](spatial-analysis-container.md#request-approval-to-run-the-container)

## <a name="troubleshooting-the-azure-stack-edge-device"></a>Resolução de problemas do dispositivo Azure Stack Edge

A secção seguinte é fornecida para ajudar na depuração e verificação do estado do seu dispositivo Azure Stack Edge.

### <a name="access-the-kubernetes-api-endpoint"></a>Aceda ao Ponto Final da API da Kubernetes. 

1. Na UI local do seu dispositivo, aceda à página **dispositivos.** 
2. Nos **pontos finais do dispositivo,** copie o ponto final de serviço da API da Kubernetes. Este ponto final é uma cadeia no seguinte formato: `https://compute..[device-IP-address]` .
3. Salve a corda do ponto final. Usará isto mais tarde ao configurar `kubectl` para aceder ao cluster Kubernetes.

### <a name="connect-to-powershell-interface"></a>Ligue-se à interface PowerShell

Remotamente, conecte-se a partir de um cliente Windows. Após a criação do cluster Kubernetes, pode gerir as aplicações através deste cluster. Terá de se ligar à interface PowerShell do dispositivo. Dependendo do sistema operativo do cliente, os procedimentos para ligar remotamente ao dispositivo podem ser diferentes. Os seguintes passos são para um cliente Windows que executa o PowerShell.

> [!TIP]
> * Antes de começar, certifique-se de que o seu cliente Windows está a executar o Windows PowerShell 5.0 ou mais tarde.
> * PowerShell também está [disponível no Linux.](/powershell/scripting/install/installing-powershell-core-on-linux)

1. Executar uma sessão Windows PowerShell como administrador. 
    1. Certifique-se de que o serviço de Gestão Remota do Windows está a funcionar no seu cliente. Na solicitação de comando, escreva `winrm quickconfig` .

2. Atribua uma variável para o endereço IP do dispositivo. Por exemplo, `$ip = "<device-ip-address>"`.

3. Utilize o seguinte comando para adicionar o endereço IP do seu dispositivo à lista de anfitriões fidedignos do cliente. 

    ```powershell
    Set-Item WSMan:\localhost\Client\TrustedHosts $ip -Concatenate -Force
    ```
 
4. Inicie uma sessão Do Windows PowerShell no dispositivo. 

    ```powershell
    Enter-PSSession -ComputerName $ip -Credential $ip\EdgeUser -ConfigurationName Minishell
    ```

5. Forneça a palavra-passe quando solicitado. Utilize a mesma palavra-passe que é usada para iniciar sinsus na interface web local. A palavra-passe padrão da interface web local é `Password1` . 

### <a name="access-the-kubernetes-cluster"></a>Aceda ao cluster Kubernetes

Após a criação do cluster Kubernetes, pode utilizar a `kubectl` ferramenta da linha de comando para aceder ao cluster.

1. Criar um novo espaço de nome. 

    ```powershell
    New-HcsKubernetesNamespace -Namespace
    ```

2. Crie um utilizador e obtenha um ficheiro config. Este comando irá obter informações de configuração para o cluster Kubernetes. Copie esta informação e guarde-as num ficheiro chamado *config*. Não guarde o ficheiro como extensão de ficheiro.
    
    ```powershell
    New-HcsKubernetesUser -UserName
    ```

3. Adicione o ficheiro *config* à pasta *.kube* no seu perfil de utilizador na máquina local.   

4. Associe o espaço de nomes ao utilizador que criou.

    ```powershell
    Grant-HcsKubernetesNamespaceAccess -Namespace -UserName
    ```

5. Instale `kubectl` no seu cliente Windows utilizando o seguinte comando:
    
    ```powershell
    curl https://storage.googleapis.com/kubernetesrelease/release/v1.15.2/bin/windows/amd64/kubectl.exe -O kubectl.exe
    ```

6. Adicione uma entrada DNS no ficheiro hostes no seu sistema. 
    1. Executar o Bloco de Notas como administrador e abrir o ficheiro *de anfitriões* localizado em `C:\windows\system32\drivers\etc\hosts` . 
    2. Crie uma entrada no ficheiro hostis com o endereço IP do dispositivo e o domínio DNS que obteve na página **do Dispositivo** na UI local. O ponto final que deve utilizar será semelhante a: `https://compute.asedevice.microsoftdatabox.com/10.100.10.10` .

7. Verifique se pode ligar-se às cápsulas Kubernetes.

    ```powershell
    kubectl get pods -n "iotedge"
    ```

Para obter registos de contentores, executar o seguinte comando:

```powershell
kubectl logs <pod-name> -n <namespace> --all-containers
```

### <a name="useful-commands"></a>Comandos úteis

|Comando  |Descrição  |
|---------|---------|
|`Get-HcsKubernetesUserConfig -AseUser`     | Gera um ficheiro de configuração Kubernetes. Ao utilizar o comando, copie a informação num ficheiro denominado *config*. Não guarde o ficheiro com uma extensão de ficheiro.        |
| `Get-HcsApplianceInfo` | Devolve informações sobre o seu dispositivo. |
| `Enable-HcsSupportAccess` | Gera credenciais de acesso para iniciar uma sessão de suporte. |

## <a name="next-steps"></a>Passos seguintes

* [Implementar uma aplicação web People Counting](spatial-analysis-web-app.md)
* [Configure operações de análise espacial](./spatial-analysis-operations.md)
* [Guia de colocação de câmera](spatial-analysis-camera-placement.md)
* [Guia de colocação de zona e linha](spatial-analysis-zone-line-placement.md)
