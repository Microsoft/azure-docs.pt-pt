---
title: OpenCensus ir rastreio com o Azure Application Insights | Documentos da Microsoft
description: Fornece instruções para integrar OpenCensus ir rastreio com o reencaminhador local e o Application Insights
services: application-insights
keywords: ''
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.service: application-insights
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: cdf01fbbcc8ef1f90b2e0f8973f59c46c5bf70f8
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60577912"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Recolher rastreios de distribuída a partir do Go (pré-visualização)

O Application Insights agora suporta distribuídas de aplicações do Go através da integração com o rastreamento [OpenCensus](https://opencensus.io) e a nova [reencaminhador local](./opencensus-local-forwarder.md). Este artigo irá guiá-lo passo a passo pelo processo de configuração OpenCensus para Go e obter os seus dados de rastreio para o Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma Subscrição do Azure.
- Deve ser instalado o Go, este artigo utiliza a versão 1.11 [ir transferir](https://golang.org/dl/).
- Siga as instruções para instalar o [reencaminhador local como um serviço do Windows](./opencensus-local-forwarder.md).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Criar recurso do Application Insights

Primeiro tem de criar um recurso do Application Insights que irá gerar uma chave de instrumentação (ikey). A ikey, em seguida, é utilizada para configurar o reencaminhador local para enviar os rastreios distribuídos da sua aplicação de OpenCensus instrumentado, para o Application Insights.   

1. Selecione **criar um recurso** > **ferramentas de programação** > **Application Insights**.

   ![Adicionar um Recurso do Application Insights](./media/opencensus-Go/0001-create-resource.png)

   É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

    | Definições        | Value           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Tipo de Aplicação** | Geral | Tipo de aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Location** | EUA Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="configure-local-forwarder"></a>Configurar o reencaminhador de local

1. Selecione **Descrição geral** > **Essentials** > Copie a **Chave de Instrumentação** da sua aplicação.

   ![Captura de ecrã da chave de instrumentação](./media/opencensus-Go/0003-instrumentation-key.png)

2. Editar seu `LocalForwarder.config` de ficheiros e adicionar a sua chave de instrumentação. Se tiver seguido as instruções a [pré-requisito](./opencensus-local-forwarder.md) o ficheiro está localizado em `C:\LF-WindowsServiceHost`

    ```xml
      <OpenCensusToApplicationInsights>
        <!--
          Instrumentation key to track telemetry to.
          -->
        <InstrumentationKey>{enter-instrumentation-key}</InstrumentationKey>
      </OpenCensusToApplicationInsights>
    
      <!-- Describes aspects of processing Application Insights telemetry-->
      <ApplicationInsights>
        <LiveMetricsStreamInstrumentationKey>{enter-instrumentation-key}</LiveMetricsStreamInstrumentationKey>
      </ApplicationInsights>
    </LocalForwarderConfiguration>
    ```

3. Reiniciar o aplicativo **reencaminhador Local** serviço.

## <a name="opencensus-go-packages"></a>Pacotes de OpenCensus Go

1. Instale os pacotes de censo aberto para Go da linha de comando:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Adicione o seguinte código para um ficheiro de go e, em seguida, crie e execute. (Neste exemplo é derivado com as diretrizes de OpenCensus oficial com o código adicionado, que facilita a integração com o reencaminhador local)

     ```go
        // Copyright 2018, OpenCensus Authors
        //
        // Licensed under the Apache License, Version 2.0 (the "License");
        // you may not use this file except in compliance with the License.
        // You may obtain a copy of the License at
        //
        //     https://www.apache.org/licenses/LICENSE-2.0
        //
        // Unless required by applicable law or agreed to in writing, software
        // distributed under the License is distributed on an "AS IS" BASIS,
        // WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
        // See the License for the specific language governing permissions and
        // limitations under the License.
        package main
        
        import (
        
            "bytes"
            "fmt"
            "log"
            "net/http"
            os "os"
            
            ocagent "contrib.go.opencensus.io/exporter/ocagent"
            "go.opencensus.io/plugin/ochttp"
            "go.opencensus.io/plugin/ochttp/propagation/tracecontext"
            "go.opencensus.io/trace"
        
        )
        
        func main() {
            // Register stats and trace exporters to export the collected data.
            serviceName := os.Getenv("SERVICE_NAME")
            if len(serviceName) == 0 {
                serviceName = "go-app"
            }
            fmt.Printf(serviceName)
            agentEndpoint := os.Getenv("OCAGENT_TRACE_EXPORTER_ENDPOINT")

            if len(agentEndpoint) == 0 {
                agentEndpoint = fmt.Sprintf("%s:%d", ocagent.DefaultAgentHost, ocagent.DefaultAgentPort)
            }
        
            fmt.Printf(agentEndpoint)
            exporter, err := ocagent.NewExporter(ocagent.WithInsecure(), ocagent.WithServiceName(serviceName), ocagent.WithAddress(agentEndpoint))
        
            if err != nil {
                log.Printf("Failed to create the agent exporter: %v", err)
            }
        
            trace.RegisterExporter(exporter)
        
            trace.ApplyConfig(trace.Config{DefaultSampler: trace.AlwaysSample()})
        
            client := &http.Client{Transport: &ochttp.Transport{Propagation: &tracecontext.HTTPFormat{}}}
        
            http.HandleFunc("/", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                var jsonStr = []byte(`[ { "url": "http://blank.org", "arguments": [] } ]`)
                r, _ := http.NewRequest("POST", "http://blank.org", bytes.NewBuffer(jsonStr))
                r.Header.Set("Content-Type", "application/json")
        
                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }
            })
        
            http.HandleFunc("/call_blank", func(w http.ResponseWriter, req *http.Request) {
                fmt.Fprintf(w, "hello world")
        
                r, _ := http.NewRequest("GET", "http://blank.org", nil)

                // Propagate the trace header info in the outgoing requests.
                r = r.WithContext(req.Context())
                resp, err := client.Do(r)
        
                if err != nil {
                    log.Println(err)
                } else {
                    // TODO: handle response
                    resp.Body.Close()
                }        
            })
        
            log.Fatal(http.ListenAndServe(":50030", &ochttp.Handler{Propagation: &tracecontext.HTTPFormat{}}))
        
        }
     ```

3. Quando a aplicação go simple estiver em execução, navegue para `http://localhost:50030`. Cada atualização do navegador irá gerar o texto "hello world" acompanhado por dados span correspondentes, que são recolhidos pelo reencaminhador de local.

4. Para confirmar que o **local reencaminhador** recolhe a verificação de rastreios a `LocalForwarder.config` ficheiro. Se tiver seguido os passos a [pré-requisito](https://docs.microsoft.com/azure/application-insights/local-forwarder), estarão localizado em `C:\LF-WindowsServiceHost`.

    Na imagem abaixo do ficheiro de registo, pode ver que, antes de executar o segundo script onde adicionamos uma principal exportadora `OpenCensus input BatchesReceived` foi 0. Depois que começamos a executar o script atualizado `BatchesReceived` incrementado igual ao número de valores que introduziu:
    
    ![Formulário de recurso novo do App Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora, pode reabrir o Application Insights **descrição geral** página no portal do Azure, para ver os detalhes sobre a sua aplicação em execução. Selecione **Live Stream métrica**.

   ![Captura de ecrã do painel de descrição geral com o fluxo de métricas em direto selecionado na caixa vermelha](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Se executar novamente a segunda aplicação Go e começar a atualizar o browser para `http://localhost:50030`, verá em direto a dados de rastreio à medida que chegam no Application Insights do serviço do reencaminhador local.

   ![Captura de ecrã do fluxo de métricas em direto com dados de desempenho apresentados](./media/opencensus-go/0006-stream.png)

3. Navegue de volta para o **descrição geral** página e selecione **mapa da aplicação** para um esquema visual das relações de dependência e chamada de tempo entre os componentes da aplicação.

    ![Captura de ecrã do mapa de aplicativo básico](./media/opencensus-go/0007-application-map.png)

    Uma vez que estamos apenas foram rastrear uma chamada de método, nosso mapa da aplicação não é tão interessante. Mas o mapa da aplicação pode ser dimensionado para visualizar as aplicações distribuídas muito mais:

   ![Mapeamento de Aplicações](media/opencensus-go/application-map.png)

4. Selecione **investigar desempenho** para efetuar a análise detalhada do desempenho e determinar a causa de raiz de um desempenho lento.

    ![Captura de ecrã do painel de desempenho](./media/opencensus-go/0008-performance.png)

5. Selecionando **amostras** e, em seguida, clicar em qualquer um dos exemplos que são apresentados no painel da direita iniciará a experiência de detalhes de transação de ponta a ponta. Embora a nossa aplicação de exemplo irá mostrar-num único evento, uma aplicação mais complexa permitiria que explore a transação de ponta a ponta para baixo até o nível de pilha de chamadas de um evento individual.

     ![Captura de ecrã da interface de transação de ponta a ponta](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Rastreio de OpenCensus para Go

Abordamos apenas as noções básicas de integração OpenCensus para Go com o reencaminhador local e o Application Insights. O [instruções de utilização de OpenCensus Go oficial](https://godoc.org/go.opencensus.io) aborda tópicos mais avançados.

## <a name="next-steps"></a>Passos Seguintes

* [Mapa da aplicação](./../../azure-monitor/app/app-map.md)
* [Monitorização do desempenho de ponto a ponto](./../../azure-monitor/learn/tutorial-performance.md)
