---
title: OpenCensus o rastreamento com o Aplicativo Azure insights | Microsoft Docs
description: Fornece instruções para integrar o rastreamento do OpenCensus Go com o encaminhador local e Application Insights
ms.service: azure-monitor
ms.subservice: application-insights
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/15/2018
ms.openlocfilehash: ef67801fee574ae43d83af650d6b715e12426c7e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/25/2019
ms.locfileid: "75432549"
---
# <a name="collect-distributed-traces-from-go-preview"></a>Coletar rastreamentos distribuídos do Go (visualização)

O Application Insights agora dá suporte ao rastreamento distribuído de aplicativos Go por meio da integração com o [OpenCensus](https://opencensus.io) e nosso novo [encaminhador local](./opencensus-local-forwarder.md). Este artigo orientará você passo a passo pelo processo de configuração do OpenCensus para o Go e a obtenção dos dados de rastreamento para Application Insights.

## <a name="prerequisites"></a>Pré-requisitos

- Precisa de uma Subscrição do Azure.
- O go deve ser instalado, este artigo usa a versão 1,11 [Go Download](https://golang.org/dl/).
- Siga as instruções para instalar o [encaminhador local como um serviço do Windows](./opencensus-local-forwarder.md).

Se não tiver uma subscrição do Azure, crie uma conta [gratuita](https://azure.microsoft.com/free/) antes de começar.

## <a name="sign-in-to-the-azure-portal"></a>Iniciar sessão no portal do Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com/).

## <a name="create-application-insights-resource"></a>Criar Application Insights recurso

Primeiro, você precisa criar um recurso de Application Insights que irá gerar uma chave de instrumentação (iKey). Em seguida, o iKey é usado para configurar o encaminhador local para enviar rastreamentos distribuídos de seu aplicativo OpenCensus instrumentado, para Application Insights.   

1. Selecione **criar um recurso** > **ferramentas para desenvolvedores** > **Application insights**.

   ![Adicionar um Recurso do Application Insights](./media/opencensus-Go/0001-create-resource.png)

 > [!NOTE]
   >Se esta for a primeira vez que você cria um recurso de Application Insights, você pode aprender mais visitando o artigo [criar um recurso de Application insights](https://docs.microsoft.com/azure/azure-monitor/app/create-new-resource) .

   É apresentada uma caixa de configuração; utilize a tabela abaixo para preencher os campos de texto.

   | Definições        | Valor           | Descrição  |
   | ------------- |:-------------|:-----|
   | **Nome**      | Valor Exclusivo Global | Nome que identifica a aplicação que está a monitorizar |
   | **Grupo de Recursos**     | myResourceGroup      | Nome do novo grupo de recursos para alojar os dados do Application Insights |
   | **Localização** | Este dos E.U.A. | Escolha uma localização perto de si ou perto do local onde a sua aplicação está alojada |

2. Clique em **Criar**.

## <a name="configure-local-forwarder"></a>Configurar encaminhador local

1. Selecione **Descrição geral** > **Essentials** > Copie a **Chave de Instrumentação** da sua aplicação.

   ![Captura de tela da chave de instrumentação](./media/opencensus-Go/0003-instrumentation-key.png)

2. Edite seu arquivo de `LocalForwarder.config` e adicione sua chave de instrumentação. Se você seguiu as instruções no [pré-requisito](./opencensus-local-forwarder.md) , o arquivo está localizado em `C:\LF-WindowsServiceHost`

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

3. Reinicie o serviço de **encaminhador local** do aplicativo.

## <a name="opencensus-go-packages"></a>Pacotes go do OpenCensus

1. Instale os pacotes abertos do censo para ir da linha de comando:

    ```go
    go get -u go.opencensus.io
    go get -u contrib.go.opencensus.io/exporter/ocagent
    ```

2. Adicione o código a seguir a um arquivo. go e, em seguida, compile e execute. (Este exemplo é derivado da orientação oficial do OpenCensus com código adicionado que facilita a integração com o encaminhador local)

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

3. Quando o aplicativo go simples estiver em execução, navegue até `http://localhost:50030`. Cada atualização do navegador irá gerar o texto "Olá, mundo" acompanhado pelos dados de span correspondentes que são selecionados pelo encaminhador local.

4. Para confirmar se o **encaminhador local** está selecionando os rastreamentos, verifique o arquivo de `LocalForwarder.config`. Se você seguiu as etapas no [pré-requisito](https://docs.microsoft.com/azure/application-insights/local-forwarder), elas estarão localizadas em `C:\LF-WindowsServiceHost`.

    Na imagem abaixo do arquivo de log, você pode ver que, antes de executar o segundo script em que adicionamos um exportador `OpenCensus input BatchesReceived` era 0. Depois de começar a executar o script atualizado `BatchesReceived` ser incrementado igual ao número de valores que inserimos:
    
    ![Formulário de recurso novo do App Insights](./media/opencensus-go/0004-batches-received.png)

## <a name="start-monitoring-in-the-azure-portal"></a>Iniciar a monitorização no portal do Azure

1. Agora você pode reabrir a página de **visão geral** Application Insights no portal do Azure, para exibir detalhes sobre o aplicativo em execução no momento. Selecione **fluxo de métrica ao vivo**.

   ![Captura de tela do painel Visão geral com o Live Metric Stream selecionado na caixa vermelha](./media/opencensus-go/0005-overview-live-metrics-stream.png)

2. Se você executar o aplicativo de segunda vez novamente e iniciar a atualização do navegador para `http://localhost:50030`, verá os dados de rastreamento ao vivo conforme eles chegam em Application Insights do serviço de encaminhador local.

   ![Captura de tela do Live Metric Stream com dados de desempenho exibidos](./media/opencensus-go/0006-stream.png)

3. Navegue de volta para a página **visão geral** e selecione **mapa do aplicativo** para obter um layout visual das relações de dependência e o tempo de chamada entre os componentes do aplicativo.

    ![Captura de tela do mapa do aplicativo básico](./media/opencensus-go/0007-application-map.png)

    Como estamos rastreando apenas uma chamada de método, nosso mapa de aplicativos não é tão interessante. Mas o mapa do aplicativo pode ser dimensionado para visualizar muito mais aplicativos distribuídos:

   ![Mapeamento de Aplicações](media/opencensus-go/application-map.png)

4. Selecione **investigar desempenho** para executar análise de desempenho detalhada e determinar a causa raiz do desempenho lento.

    ![Captura de tela do painel de desempenho](./media/opencensus-go/0008-performance.png)

5. Selecionar **exemplos** e clicar em qualquer um dos exemplos que aparecem no painel à direita iniciará a experiência de detalhes da transação de ponta a ponta. Embora nosso aplicativo de exemplo apenas mostre-nos um único evento, um aplicativo mais complexo permitiria que você explorasse a transação de ponta a ponta até o nível da pilha de chamadas de um evento individual.

     ![Captura de tela da interface de transação de ponta a ponta](./media/opencensus-go/0009-end-to-end-transaction.png)

## <a name="opencensus-trace-for-go"></a>Rastreamento de OpenCensus para go

Abordamos apenas os conceitos básicos da integração do OpenCensus para o Go com o encaminhador local e Application Insights. As [diretrizes de uso oficial do OpenCensus go](https://godoc.org/go.opencensus.io) abrangem tópicos mais avançados.

## <a name="next-steps"></a>Passos seguintes

* [Mapa do aplicativo](./../../azure-monitor/app/app-map.md)
* [Monitoramento de desempenho de ponta a ponta](./../../azure-monitor/learn/tutorial-performance.md)
