---
title: 'Tutorial: Processar faturas EDIFACT com os serviços BizTalk do Azure | Documentos da Microsoft'
description: Como criar e configurar a aplicação de caixa de conector ou API e utilizá-la numa aplicação lógica no serviço de aplicações do Azure
services: biztalk-services
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
ms.assetid: 7e0b93fa-3e2b-4a9c-89ef-abf1d3aa8fa9
ms.service: biztalk-services
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 05/31/2016
ms.author: deonhe
ms.openlocfilehash: 5eb9740bdd0543556265f54a1a37b632f79ac861
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/07/2019
ms.locfileid: "57550127"
---
# <a name="tutorial-process-edifact-invoices-using-azure-biztalk-services"></a>Tutorial: Processar faturas EDIFACT com os Serviços BizTalk do Azure

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Pode utilizar o Portal de serviços do BizTalk para configurar e implementar X12 e contratos EDIFACT. Neste tutorial, vamos ver como criar um contrato EDIFACT para a troca de notas fiscais entre parceiros comerciais. Este tutorial foi escrito em torno de uma solução de negócios de ponto-a-ponto que envolve dois parceiros comerciais, Northwind e Contoso trocar mensagens EDIFACT.  

## <a name="sample-based-on-this-tutorial"></a>Exemplo com base neste tutorial
Este tutorial foi escrito em torno de um exemplo, **enviar EDIFACT notas fiscais usando os serviços BizTalk**, que está disponível para transferir a partir do [MSDN Code Gallery](https://go.microsoft.com/fwlink/?LinkId=401005). Pode utilizar o exemplo e seguir este tutorial para compreender como o exemplo foi criado. Em alternativa, poderia usar este tutorial para criar sua própria solução zero-up. Este tutorial destina-se para a segunda abordagem, para que entenda como essa solução foi criada. Além disso, tanto quanto possível, o tutorial é consistente com o exemplo e utiliza os mesmos nomes para artefactos (por exemplo, esquemas, transformações) como utilizadas no exemplo.  

> [!NOTE]
> Uma vez que esta solução envolve enviando uma mensagem a partir de uma ponte EAI para uma bridge EDI, reutiliza os [Bridge de serviços do BizTalk exemplo de encadeamento](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104) exemplo.  
> 
> 

## <a name="what-does-the-solution-do"></a>O que faz a solução?
Nesta solução, Northwind recebe faturas EDIFACT da Contoso. Essas notas fiscais não estão num formato EDI padrão. Portanto, antes de enviar a nota fiscal para Northwind, precisam ser transformada para um documento de nota fiscal (também denominado INVOIC) EDIFACT. Após a receção, Northwind tem de processar a nota fiscal EDIFACT e devolver uma mensagem de controlo (também denominada CONTRL) para a Contoso.

![][1]  

Para alcançar este cenário de negócio, a Contoso utiliza os recursos fornecidos com os serviços BizTalk do Microsoft Azure.

* A Contoso utiliza EAI bridges, para transformar a nota fiscal original para EDIFACT INVOIC.
* A ponte EAI, em seguida, envia a mensagem para uma bridge de envio EDI implementada como parte de um contrato configurado no Portal de serviços do BizTalk.
* A ponte de envio EDI processa o INVOIC EDIFACT e encaminha o mesmo para Northwind.
* Depois de receber a nota fiscal, Northwind devolve um CONTRL da mensagem para o EDI recebe ponte implementada como parte do contrato.  

> [!NOTE]
> Opcionalmente, esta solução também demonstra como utilizar a criação de batches de mensagens em fila para enviar as notas fiscais em lotes, em vez de enviar a cada uma das faturas em separado.  
> 
> 

Para concluir o cenário, usamos filas do Service Bus para enviar a nota fiscal da Contoso para Northwind ou receber confirmação da Northwind. Estas filas podem ser criadas através de uma aplicação de cliente, o que está disponível para download e está incluída no pacote de exemplo que está disponível como parte deste tutorial.  

## <a name="prerequisites"></a>Pré-requisitos
* Tem de ter um espaço de nomes do Service Bus. Para obter instruções sobre como criar um espaço de nomes, consulte [como: Criar ou modificar um espaço de nomes de serviço do Service Bus](https://msdn.microsoft.com/library/azure/hh674478.aspx). Vamos assumir que já tem um espaço de nomes do Service Bus aprovisionado, chamado **edifactbts**.
* Tem de ter uma subscrição de serviços do BizTalk. Para este tutorial, vamos assumir que tiver uma subscrição de serviços do BizTalk, chamada **contosowabs**.
* Registe a sua subscrição de serviços do BizTalk no Portal de serviços do BizTalk. Para obter instruções, consulte [registar uma implementação de serviços do BizTalk no Portal dos serviços do BizTalk](https://msdn.microsoft.com/library/hh689837.aspx)
* Tem de ter o Visual Studio instalado.
* Tem de ter o SDK dos BizTalk Services instalado. Pode baixar o SDK do [https://go.microsoft.com/fwlink/?LinkId=235057](https://go.microsoft.com/fwlink/?LinkId=235057)  

## <a name="step-1-create-the-service-bus-queues"></a>Passo 1: Criar as filas do Service Bus
Esta solução utiliza filas do Service Bus para trocar mensagens entre parceiros comerciais. Contoso e Northwind enviam mensagens para as filas de onde os pontes EAI e/ou EDI aceder aos mesmos. Para esta solução, terá três filas do Service Bus:

* **northwindreceive** – Northwind recebe a nota fiscal da Contoso através desta fila.
* **contosoreceive** – Contoso recebe a confirmação do Northwind através desta fila.
* **suspenso** – todas as mensagens suspensas são encaminhadas para esta fila. As mensagens são suspensas caso de falha durante o processamento.

Pode criar estas filas do Service Bus, utilizando uma aplicação de cliente incluída no pacote de exemplo.  

1. A partir da localização onde transferiu o exemplo, abra **Tutorial enviar faturas usando BizTalk serviços EDI Bridges.sln**.
2. Prima **F5** para compilar e iniciar a **Tutorial cliente** aplicação.
3. No ecrã, introduza o espaço de nomes, nome do emissor e chave do emissor do ACS de barramento de serviço.
   
   ![][2]  
4. Uma caixa de mensagem solicita que serão criadas três filas no espaço de nomes do Service Bus. Clique em **OK**.
5. Deixe o cliente de Tutorial em execução. Abrir, clique em **do Service Bus** > ***seu espaço de nomes do Service Bus*** > **filas**e certifique-se de que as filas de três são criadas.  

## <a name="step-2-create-and-deploy-trading-partner-agreement"></a>Passo 2: Criar e implementar o contrato de parceiro comercial
Crie um contrato de parceiro comercial entre Contoso e Northwind. Um contrato de parceiro comercial define um contrato de trade entre os parceiros de negócio, como o esquema de mensagens para utilizar o protocolo de mensagens a utilizar, etc. Um contrato de parceiro comercial inclui duas pontes EDI, um para enviar mensagens para parceiros comerciais (chamado a **EDI enviar ponte**) e outro para receber mensagens de parceiros comerciais (chamado o **EDI receber ponte**).

No contexto desta solução, a ponte de envio EDI corresponde ao lado de envio do acordo e é utilizada para enviar a nota fiscal EDIFACT da Contoso para Northwind. Da mesma forma, a ponte de receção EDI corresponde ao lado do recebimento do contrato e é utilizado para as confirmações de receção do Northwind.  

### <a name="create-the-trading-partners"></a>Criar os parceiros comerciais
Para começar, crie parceiros comerciais para Contoso e Northwind.  

1. No Portal de serviços do BizTalk, sobre o **parceiros** separador, clique em **Add**.
2. Na nova página de parceiros, introduza **Contoso** como nome do parceiro e, em seguida, clique **guardar**.
3. Repita o passo para criar o parceiro de segundo **Northwind**.  

### <a name="create-the-agreement"></a>Criar o contrato
Contratos de parceiros comerciais são criados entre perfis de negócios de parceiros comerciais. Esta solução utiliza os perfis de parceiro do padrão que são criados automaticamente quando criamos os parceiros.  

1. No Portal de serviços do BizTalk, clique em **contratos** > **Add**.
2. Do novo contrato **definições gerais** página, especifique os valores, conforme mostrado na imagem abaixo e, em seguida, clique em **continuar**.
   
   ![][3]  
   
   Depois de clicar em **continuar**, duas guias, **receber definições** e **enviar definições** se tornarem disponíveis.
3. Crie o contrato de envio entre Contoso e Northwind. Este contrato controla como o Contoso envia a nota fiscal EDIFACT para Northwind.
   
   1. Clique em **definições de envio**.
   2. Manter os valores predefinidos no **URL de entrada**, **transformar**, e **criação de batches** separadores.
   3. Sobre o **protocolo** separador, no **esquemas** secção, carregue o **EFACT_D93A_INVOIC.xsd** esquema. Esse esquema está disponível com o pacote de exemplo.
      
      ![][4]  
   4. Sobre o **transporte** separador, especifique os detalhes para as filas do Service Bus. O contrato de lado de envio, vamos utilizar o **northwindreceive** fila para enviar a nota fiscal EDIFACT para Northwind e o **suspenso** fila para encaminhar todas as mensagens que falharem durante o processamento e são suspensos. Criou estes filas em **passo 1: Criar as filas do Service Bus** (deste tópico).
      
      ![][5]  
      
      Sob **transporte definições > tipo de transporte** e **definições de suspensão da mensagem > tipo de transporte**, selecione Azure Service Bus e forneça os valores, conforme mostrado na imagem.
4. Crie o contrato de receção entre Contoso e Northwind. Este contrato controla como o Contoso recebe a confirmação da Northwind.
   
   1. Clique em **receber definições**.
   2. Manter os valores predefinidos no **transporte** e **transformar** separadores.
   3. Sobre o **protocolo** separador, no **esquemas** secção, carregue o **EFACT_4.1_CONTRL.xsd** esquema. Esse esquema está disponível com o pacote de exemplo.
   4. Sobre o **rota** separador, criar um filtro para garantir que apenas as confirmações do Northwind são encaminhadas para a Contoso. Sob **definições de rota**, clique em **Add** para criar o filtro de encaminhamento.
      
      ![][6]  
      
      1. Fornecer valores para **nome da regra**, **regra de rota**, e **destino da rota** conforme mostrado na imagem.
      2. Clique em **Guardar**.
   5. Sobre o **rota** separador novamente, especifique onde as confirmações suspensas (confirmações falharem durante o processamento) são encaminhadas para. Defina o tipo de transporte para o Azure Service Bus, encaminhar o tipo de destino para **fila**, tipo de autenticação para **assinatura de acesso partilhado** (SAS), forneça a cadeia de ligação SAS para o espaço de nomes do Service Bus e, em seguida, introduza o nome da fila como **suspenso**.
5. Por fim, clique em **Deploy** para implementar o contrato. Tenha em atenção os pontos de extremidade em que a enviar e receber contratos implementados.
   
   * Sobre o **enviar definições** separador, em **URL de entrada**, tenha em atenção o ponto final. Para enviar uma mensagem da Contoso para Northwind usando a ponte de envio EDI, terá de enviar uma mensagem para este ponto final.
   * Sobre o **receber definições** separador, em **transporte**, tenha em atenção o ponto final. Para enviar uma mensagem do Northwind, para a Contoso, usando o EDI receber ponte, terá de enviar uma mensagem para este ponto final.  

## <a name="step-3-create-and-deploy-the-biztalk-services-project"></a>Passo 3: Criar e implementar o projeto de serviços BizTalk
No passo anterior, implementou o envio EDI e contratos para processar faturas EDIFACT e as confirmações de receção. Estes contratos podem apenas as mensagens de processo que está em conformidade com o esquema padrão de mensagem EDIFACT. No entanto, pelo cenário para esta solução, Contoso envia uma nota fiscal para Northwind num esquema de proprietário internamente. Então, antes da mensagem é enviada para a ponte de envio EDI, ele precisam ser transformado do esquema internamente para o esquema de nota fiscal EDIFACT padrão. O projeto EAI de serviços do BizTalk faz isso.

O projeto de serviços do BizTalk **InvoiceProcessingBridge**, que transformações a mensagem também é incluído como parte do exemplo que transferiu. O projeto inclui os seguintes artefatos:

* **INHOUSEINVOICE. XSD** – esquema da nota fiscal interna que é enviada para Northwind.
* **EFACT_D93A_INVOIC. XSD** – esquema da nota fiscal EDIFACT padrão.
* **EFACT_4.1_CONTRL. XSD** – esquema da confirmação de EDIFACT que Northwind envia para a Contoso.
* **INHOUSEINVOICE_to_D93AINVOIC. TRFM** – a transformação que mapeia o esquema de nota fiscal internamente para o esquema de nota fiscal EDIFACT padrão.  

### <a name="create-the-biztalk-services-project"></a>Criar o projeto de serviços BizTalk
1. Na solução do Visual Studio, expanda o projeto de InvoiceProcessingBridge e, em seguida, abra a **MessageFlowItinerary.bcs** ficheiro.
2. Clique em qualquer local na tela e defina a **URL do serviço BizTalk** na caixa de propriedade para especificar o nome da sua subscrição de serviços do BizTalk. Por exemplo, `https://contosowabs.biztalk.windows.net`.
   
   ![][7]  
3. Na caixa de ferramentas, arraste um **Xml One-way ponte** à tela. Definir o **nome da entidade** e **endereço relativo** propriedades de ponte para **ProcessInvoiceBridge**. Faça duplo clique em **ProcessInvoiceBridge** para abrir a superfície de configuração de ponte.
4. Dentro de **tipos de mensagem** caixa, clique no sinal de adição (**+**) botão para especificar o esquema de mensagem de entrada. Uma vez que a mensagem de entrada para a ponte EAI é sempre a nota fiscal internamente, defina esta opção como **INHOUSEINVOICE**.
   
   ![][8]  
5. Clique nas **transformação Xml** forma e na caixa de propriedade, para o **Maps** propriedade, clique nas reticências (**...** ) botão. Na **mapas de seleção** caixa de diálogo, selecione a **INHOUSEINVOICE_to_D93AINVOIC** transformar o ficheiro e, em seguida, clique em **OK**.
   
   ![][9]  
6. Volte ao **MessageFlowItinerary.bcs**e na caixa de ferramentas, arraste um **ponto final de serviço externo bidirecional** à direita do **ProcessInvoiceBridge**. Definir seus **nome da entidade** propriedade **EDIBridge**.
7. No Solution Explorer, expanda o **MessageFlowItinerary.bcs** e clique duas vezes o **EDIBridge.config** ficheiro. Substitua o conteúdo dos **EDIBridge.config** com o seguinte.
   
   > [!NOTE]
   > Por que preciso editar o arquivo. config? O ponto de extremidade de serviço externo que adicionamos à tela do estruturador de ponte representa as pontes EDI que foi implementada anteriormente. Pontes EDI são pontes bidirecionais, com o envio e recebem lado. No entanto, a ponte EAI, adicionámos o designer de ponte é uma ponte unidirecional. Então, para lidar com os padrões de troca de mensagem diferentes ponte dois, podemos usar um comportamento de ponte personalizado, incluindo a respetiva configuração no arquivo. config. Além disso, o comportamento personalizado também processa a autenticação para o ponto de final de ponte de envio EDI. Este comportamento personalizado está disponível como um exemplo separado em [Bridge de serviços do BizTalk encadeamento de exemplo - EAI EDI](https://code.msdn.microsoft.com/BizTalk-Bridge-chaining-2246b104). Esta solução reutiliza o exemplo.  
   > 
   > 
   
   ```
   <?xml version="1.0" encoding="utf-8"?>
   <configuration>
     <system.serviceModel>
       <extensions>
         <behaviorExtensions>
           <add name="BridgeAuthentication" 
                 type="Microsoft.BizTalk.Bridge.Behaviour.BridgeBehaviorElement, Microsoft.BizTalk.Bridge.Behaviour, Version=1.0.0.0, Culture=neutral, PublicKeyToken=ae58f69b69495c05" />
         </behaviorExtensions>
       </extensions>
       <behaviors>
         <endpointBehaviors>
           <behavior name="BridgeAuthenticationConfiguration">
             <!-- Enter the ACS namespace, issuer name and issuer secret of the BizTalk Services deployment -->
             <BridgeAuthentication acsnamespace="[YOUR ACS NAMESPACE]" 
                                   issuername="owner" 
                                   issuersecret="[YOUR ACS SECRET]" />
             <webHttp />
           </behavior>
         </endpointBehaviors>
       </behaviors>
       <bindings>
         <webHttpBinding>
           <binding name="BridgeBindingConfiguration">
             <security mode="Transport" />
           </binding>
         </webHttpBinding>
       </bindings>
       <client>
         <clear />
         <!--
           Go BizTalk Portal > Agreement > Send Settings > Inbound URL
           Copy the Endpoint URL and paste it in the below address field
         -->
         <endpoint name="TwoWayExternalServiceEndpointReference1" 
                   address="[YOUR EDI BRIDGE SEND URI]" 
                   behaviorConfiguration="BridgeAuthenticationConfiguration" 
                   binding="webHttpBinding" 
                   bindingConfiguration="BridgeBindingConfiguration" 
                   contract="System.ServiceModel.Routing.IRequestReplyRouter" />
       </client>
     </system.serviceModel>
   </configuration>
   
   ```
8. Atualizar o ficheiro de EDIBridge.config para incluir detalhes de configuração
   
   * Sob *<behaviors>*, forneça o espaço de nomes do ACS e a chave associada à subscrição dos serviços BizTalk.
   * Sob *<client>*, forneça o ponto de extremidade em que o contrato de envio EDI é implementado.
   
   Guardar as alterações e feche o ficheiro de configuração.
9. Na caixa de ferramentas, clique nas **conector** e Junte-se a **ProcessInvoiceBridge** e **EDIBridge** componentes. Selecione o conector e, na caixa de propriedades, defina **condição de filtro** ao **correspondência todos**. Isto garante que todas as mensagens processadas pela ponte EAI são encaminhadas para a ponte EDI.
   
   ![][10]  
10. Guarde as alterações à solução.  

### <a name="deploy-the-project"></a>Implementar o projeto
1. No computador onde criou o projeto de serviços do BizTalk, transfira e instale o certificado SSL para a sua subscrição de serviços do BizTalk. A partir, em serviços do BizTalk, clique em **Dashboard**e, em seguida, clique em **Transferir certificado de SSL**. Clique duas vezes o certificado e siga o pedido para concluir a instalação. Certifique-se de que instala o certificado no **autoridades de certificação de raiz fidedigna** arquivo de certificados.
2. No Explorador de soluções do Visual Studio, clique com botão direito a **InvoiceProcessingBridge** projeto e, em seguida, clique em **implementar**.
3. Forneça os valores, conforme mostrado na imagem e, em seguida, clique em **Deploy**. Pode obter as credenciais de ACS para os serviços BizTalk clicando **informações da ligação** partir do dashboard de serviços do BizTalk.
   
   ![][11]  
   
   No painel de saída, copiar o ponto de extremidade em que a ponte EAI é implementada, por exemplo, `https://contosowabs.biztalk.windows.net/default/ProcessInvoiceBridge`. Irá precisar posteriormente este URL de ponto final.  

## <a name="step-4-test-the-solution"></a>Passo 4: Testar a solução
Neste tópico, vamos ver como testar a solução utilizando o **Tutorial cliente** fornecida como parte do exemplo de aplicação.  

1. No Visual Studio, prima F5 para iniciar o **Tutorial cliente**.
2. O ecrã tem de ter os valores pré-preenchido do passo em que criamos as filas do Service Bus. Clique em **Seguinte**.
3. Na janela seguinte, forneça as credenciais de ACS para a subscrição dos serviços BizTalk e os pontos de extremidade onde EAI e EDI (recepção) pontes são implementadas.
   
   Tinha copiou o ponto final de ponte EAI no passo anterior. Para EDI receber o ponto final de ponte, no Portal de serviços do BizTalk, vá para o contrato > receber definições > transporte > ponto de extremidade.
   
   ![][12]  
4. Na janela seguinte, na Contoso, clique nas **enviar faturas internas** botão. No ficheiro de abrir a caixa de diálogo, abra o ficheiro de INHOUSEINVOICE.txt. Examinar o conteúdo do ficheiro e, em seguida, clique em **OK** para enviar a nota fiscal.
   
   ![][13]  
5. Em alguns segundos a nota fiscal é recebida no Northwind. Clique nas **Zobrazit Zprávu** ligação para ver a nota fiscal recebida pelo Northwind. Observe como a nota fiscal recebida pelo Northwind é no esquema EDIFACT padrão enquanto o enviada pela Contoso se um esquema internamente.
   
   ![][14]  
6. Selecione a nota fiscal e, em seguida, clique em **Enviar confirmação**. Na caixa de diálogo que aparece, tenha em atenção que o ID de intercâmbio é a mesmo da nota fiscal recebida e a confirmação de que está a ser enviados. Clique em OK na **Enviar confirmação** caixa de diálogo.
   
   ![][15]  
7. Em alguns segundos, a confirmação for recebida com êxito na Contoso.
   
   ![][16]  

## <a name="step-5-optional-send-edifact-invoice-in-batches"></a>Passo 5 (opcional): Enviar faturas EDIFACT em lotes
Pontes de EDI do BizTalk Services também suporta a criação de batches de mensagens de saída. Esta funcionalidade é útil para o recebimento de parceiros que receber um lote de mensagens (determinado critério de reuniões) em vez de mensagens individuais.

O aspecto mais importante ao trabalhar com lotes é a versão real do batch, também denominado os critérios de versão. Os critérios de versão podem basear-se em como o parceiro de receção quer receber mensagens. Se a criação de batches estiver ativada, a ponte EDI não envia os mensagem de saída para o parceiro de receção, até que os critérios de versão é concluído. Por exemplo, um critério de criação de batches com base na mensagem tamanho expedir um lote apenas quando "n" são limitativas mensagens. Um critério de batch também pode ser o baseados no tempo, que é enviado um lote uma hora fixa todos os dias. Nesta solução, tentamos os critérios com base do tamanho da mensagem.

1. No Portal de serviços do BizTalk, clique no contrato que criou anteriormente. Clique em definições de envio > criação de batches > Adicionar Batch.
2. Para nome do batch, introduza **InvoiceBatch**, forneça uma descrição e, em seguida, clique em **próxima**.
3. Especifique um critério de lote, que define quais mensagens devem ser loteadas. Nesta solução, vamos todas as mensagens do batch. Por isso, selecione a utilização de opções de definições avançadas e insira **1 = 1**. Trata-se uma condição que sempre será verdadeira e, por conseguinte, irão ser loteadas todas as mensagens. Clique em **Seguinte**.
   
   ![][17]  
4. Especifique um critério de versão do batch. Na caixa de lista, selecione **MessageCountBased**e para **contagem**, especifique **3**. Isso significa que um lote de três mensagens sejam enviado para Northwind. Clique em **Seguinte**.
   
   ![][18]  
5. Reveja o resumo e, em seguida, clique em **guardar**. Clique em **Deploy** para voltar a implementar o contrato.
6. Volte para o **cliente Tutorial**, clique em **enviar faturas internas**e siga as instruções para enviar a nota fiscal. Observará que nenhum nota fiscal é recebida no Northwind, porque o tamanho do lote não for cumprido. Repita este passo mais duas vezes, para que tenha três nota fiscal mensagens enviadas para Northwind. Isso satisfaça os critérios de lançamento de lote de 3 mensagens e deverá ver uma nota fiscal no Northwind.

<!--Image references-->
[1]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-1.PNG  
[2]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-2.PNG  
[3]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-3.PNG
[4]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-4.PNG  
[5]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-5.PNG  
[6]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-6.PNG  
[7]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-7.PNG  
[8]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-8.PNG
[9]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-9.PNG  
[10]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-10.PNG  
[11]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-11.PNG  
[12]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-12.PNG  
[13]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-13.PNG
[14]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-14.PNG  
[15]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-15.PNG  
[16]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-16.PNG  
[17]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-17.PNG  
[18]: ./media/biztalk-process-edifact-invoice/process-edifact-invoices-with-auzure-bts-18.PNG

