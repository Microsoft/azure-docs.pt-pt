---
title: OpenCensus Vai rastreio com insights de aplicação azure  Microsoft Docs
description: Fornece instruções para integrar o OpenCensus Ir rastreando com o avançado local e insights de aplicação
ms.topic: conceptual
ms.date: 09/15/2018
ms.openlocfilehash: ddb8dfbf14f2c75e4d16693076e56a711d8861ad
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/27/2020
ms.locfileid: "77669986"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Recolher vestígios distribuídos de Go (Pré-visualização)

Application Insights agora suporta rastreio distribuído de aplicações Go através da integração com [openCensus](https://opencensus.io) e nosso novo [avançado local](./opencensus-local-forwarder.md). Este artigo irá acompanhá-lo passo a passo através do processo de criação de OpenCensus for Go e obter os seus dados de rastreio para Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma Subscrição do Azure.
- Vá instalado, este artigo utiliza a versão 1.11 [Go Download](https://golang.org/dl/).
- Siga as instruções para instalar o [avançado local como um serviço Windows](./opencensus-local-forwarder.md).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Criar recurso Deinsights de Aplicação

Primeiro, é preciso criar um recurso Application Insights que gere uma chave de instrumentação (ikey). O ikey é então usado para configurar o avançado local para enviar vestígios distribuídos da sua aplicação instrumentada OpenCensus, para Application Insights.   

1. Selecione **Criar um recurso** > **Ferramentas** de Desenvolvimento > **Insights de aplicação**.

   ![Adicionar um Recurso do Application Insights](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Se for a primeira vez que cria um recurso de Insights de Aplicação, pode aprender mais visitando o artigo [Create a Application Insights Resource.](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource)

   É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

   | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Localização** | E.U.A. Leste | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="configure-local-forwarder"></a>Configure o avançado local

1. Selecione **Descrição geral** > **Essentials** > Copie a **Chave de Instrumentação** da sua aplicação.

   ![Screenshot da chave de instrumentação](./media/opencensus-Go/0003-instrumentation-key.png)

2. Edite o seu ficheiro `LocalForwarder.config` e adicione a sua chave de instrumentação. Se seguiu as instruções no [pré-requisito,](./opencensus-local-forwarder.md) o ficheiro encontra-se em `C:\LF-WindowsServiceHost`

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

3. Reinicie o serviço **Local Forwarder** da aplicação.

## <a name="opencensus-go-packages"></a>Pacotes OpenCensus Go

1. Instale os pacotes Open Census para ir a partir da linha de comando:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Adicione o seguinte código a um ficheiro .go e, em seguida, construa e corra. (Este exemplo deriva da orientação oficial do OpenCensus com código adicional que facilita a integração com o avançado local)

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

3. Uma vez que a aplicação simples go está a correr navegapara `http://localhost:50030`. Cada atualização do navegador gerará o texto "hello world" acompanhado pelos dados de span correspondentes que são recolhidos pelo avançado local.

4. Para confirmar que o **avançado local** está a recolher os vestígios, verifique o ficheiro `LocalForwarder.config`. Se seguir os passos no [pré-requisito,](https://docs.microsoft.com/azure/application-insights/local-forwarder)ficará localizado em `C:\LF-WindowsServiceHost`.

    Na imagem abaixo do ficheiro de registo, pode ver que antes de executar o segundo script onde adicionámos um exportador `OpenCensus input BatchesReceived` era 0. Assim que começamos a executar o script atualizado `BatchesReceived` incrementado igual ao número de valores que introduzimos:
    
    ![Formulário de recurso novo do App Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora pode reabrir a página de **visão geral** da Aplicação Insights no portal Azure, para ver detalhes sobre a sua aplicação em execução. Selecione **Live Metric Stream**.

   ![Screenshot do painel de visão geral com fluxo métrico ao vivo selecionado na caixa vermelha](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Se executar a segunda aplicação Go novamente e começar a refrescar o navegador para `http://localhost:50030`, verá dados de rastreio ao vivo à medida que chega à Aplicação Insights do serviço de avançados local.

   ![Screenshot do fluxo métrico ao vivo com dados de desempenho apresentados](./media/opencensus-go/0006-stream.png)

3. Navegue de volta para a página **de visão geral** e selecione O Mapa de **Aplicações** para um layout visual das relações de dependência e tempo de chamada entre os seus componentes de aplicação.

    ![Screenshot do mapa básico de aplicações](./media/opencensus-go/0007-application-map.png)

    Como estávamos apenas a localizar uma chamada de método, o nosso mapa de aplicações não é tão interessante. Mas o mapa de aplicações pode escalar para visualizar aplicações muito mais distribuídas:

   ![Mapeamento de Aplicações](media/opencensus-go/application-map.png)

4. Selecione **Investigar performance** para efetuar uma análise detalhada de desempenho e determinar a causa principal de um desempenho lento.

    ![Screenshot do painel de desempenho](./media/opencensus-go/0008-performance.png)

5. Selecionando **amostras** e clicando em qualquer uma das amostras que aparecem no painel da direita lançará a experiência de detalhes de transação de ponta a ponta. Enquanto a nossa aplicação de amostras apenas nos mostrará um único evento, uma aplicação mais complexa permitir-lhe-ia explorar a transação de ponta a ponta até ao nível da pilha de chamadas de um evento individual.

     ![Screenshot da interface de transação de ponta a ponta](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Traço openCensus para Go

Apenas cobrimos o básico de integrar o OpenCensus para Ir com o avançado local e insights de aplicação. A orientação oficial de utilização do [OpenCensus Go](https://godoc.org/go.opencensus.io) abrange temas mais avançados.

## <a name="next-steps"></a>Passos seguintes

* [Mapa de aplicações](./../../azure-monitor/app/app-map.md)
* [Monitorização do desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
