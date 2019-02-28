---
title: Migrar soluções EDI do BizTalk Server para os serviços BizTalk | Documentos da Microsoft
description: Saiba como é possível migrar suas soluções EDI do Microsoft BizTalk Server para o Microsoft Azure BizTalk Services (MABS)
services: biztalk-services
ms.service: biztalk-services
author: jonfancey
ms.author: jonfan
manager: jeconnoc
ms.topic: article
ms.date: 07/31/2018
ms.reviewer: jonfan, LADocs
ms.suite: integration
ms.openlocfilehash: 5543fd5ee2b86a57414a384df9d808e87b297a5e
ms.sourcegitcommit: 1afd2e835dd507259cf7bb798b1b130adbb21840
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/28/2019
ms.locfileid: "56983035"
---
# <a name="migrate-biztalk-server-edi-solutions-to-biztalk-services-technical-guide"></a>Migre soluções EDI do BizTalk Server para os serviços BizTalk: Guia técnico

> [!INCLUDE [BizTalk Services is being retired, and replaced with Azure Logic Apps](../../includes/biztalk-services-retirement.md)]

Autor: Tim Wieman e Nitin Mehrotra

Revisores: Karthik Bharthy

Escrito usando:  BizTalk Services para o Microsoft Azure – lançamento de Fevereiro de 2014.

## <a name="introduction"></a>Introdução
Intercâmbio de dados Eletrónicos (EDI) é um dos meios mais predominantes por dados do exchange que as empresas eletronicamente, também denominadas transações empresa-empresa ou B2B. BizTalk Server teve EDI suporte para mais de uma década, uma vez que a versão inicial do BizTalk Server. Com os serviços do BizTalk, a Microsoft continua o suporte para soluções EDI na plataforma Microsoft Azure. Transações B2B principalmente são externas à organização e, por conseguinte, é mais fácil de implementar se ele foi implementado numa plataforma na cloud. Microsoft Azure proporciona esta capacidade por meio de serviços do BizTalk.

Embora alguns clientes olham para os serviços do BizTalk como plataforma "green field" para novas soluções EDI, muitos clientes têm soluções EDI do BizTalk Server atuais, que podem optar por migrar para o Azure. Como é arquitetado de EDI do BizTalk Services com base no mesmo entidades-chave como a arquitetura de EDI do BizTalk Server (comércio de parceiros, entidades, contratos), é possível migrar os artefactos de EDI do BizTalk Server para os serviços BizTalk.

Este documento discute algumas das diferenças envolvidas com migração artefactos de EDI do BizTalk Server para os serviços BizTalk. Este documento pressupõe um conhecimento prático dos processos e contratos de parceiros comerciais EDI do BizTalk Server. Para obter mais informações sobre o EDI do BizTalk Server, consulte [comerciais parceiro de gestão através do BizTalk Server](https://msdn.microsoft.com/library/bb259970.aspx).

## <a name="which-version-of-biztalk-server-edi-artifacts-can-be-migrated-to-biztalk-services"></a>Qual é a versão dos artefactos de EDI do BizTalk Server pode ser migrada para os serviços BizTalk?
O módulo de EDI do BizTalk Server foi significativamente melhorado para o BizTalk Server 2010, quando ele foi remodeled para incluir parceiros, perfis e contratos. Os serviços do BizTalk usa o mesmo modelo para organizar os parceiros comerciais e as divisões de negócios dentro desses parceiros comerciais. Como resultado, a migrar os artefactos EDI do BizTalk Server 2010 e versões posteriores para os serviços do BizTalk, é um processo muito mais direta. Para migrar os artefactos EDI associados com versões anteriores ao BizTalk Server 2010, tem de atualizar primeiro para o BizTalk Server 2010 e, em seguida, migrar seus artefactos EDI para os serviços BizTalk.

## <a name="scenariosmessage-flow"></a>Fluxo de mensagens/cenários
Como com o BizTalk Server, EDI processamento nos serviços do BizTalk é criado em torno de uma solução de gestão de parceiros comerciais (TPM). A solução TPM tem os seguintes componentes principais:

* Parceiros comerciais, que representa a organização numa transação de B2B.
* Perfis, que representam as divisões dentro de um parceiro comercial.
* Negociação de contratos de parceiros (ou contratos), que representam o acordo empresarial entre dois parceiros/perfis.

A ilustração seguinte ilustra as semelhanças, bem como as diferenças, entre uma solução EDI do BizTalk Server e a solução EDI do BizTalk Services:

![][EDImessageflow]

As principais diferenças e semelhanças, entre um fluxo de solução EDI no BizTalk Server e os serviços BizTalk são:

* Tal como o BizTalk Server utiliza um pipeline de EDIReceive para receber uma mensagem EDI e um pipeline de EDISend para enviar uma mensagem EDI, os serviços do BizTalk usa uma ponte de EDI receber de mensagens em fila para receber e enviar de EDI ponte para enviar mensagens EDI. No BizTalk Server, os pipelines são associados a um contrato com o envio ou portas de recebimento. Nos serviços do BizTalk, o contrato do próprio denota a enviar ou receber bridge.
* No BizTalk Server, depois do pipeline de EDIReceive processa a mensagem EDI, é despejada a mensagem para uma base de dados do SQL Server. O pipeline de EdiSend, em seguida, seleciona a mensagem da base de dados do SQL Server, processa-os e, em seguida, envia-ao parceiro comercial.
  
    Os serviços BizTalk, depois de receber o EDI ponte processa a mensagem EDI, ele encaminha a mensagem para um processo externo. O processo externo poderia estar em execução no Microsoft Azure ou no local. O processo externo deve encaminhar a mensagem para a ponte de envio EDI; a ponte de envio não inerentemente a mensagem de solicitação. Depois de processar a mensagem, a ponte de envio EDI encaminha a mensagem ao parceiro comercial.

Os serviços BizTalk fornecem uma experiência de configuração de fácil de usar para rapidamente criar e implementar um contrato entre parceiros comerciais sem configurar quaisquer instâncias de computação do Microsoft Azure (funções Web ou de trabalho), qualquer base de dados SQL do Microsoft Azure ou qualquer um de B2B Contas de armazenamento do Microsoft Azure. Cenários mais complexos exigirá amarrando em fluxos de trabalho ou outro processamento de serviço "ao redor das margens" de um contrato de parceiro comercial, ou seja, antes ou após o processamento de ponte de EDI do contrato de parceiro comercial. Detalhadamente, as seguintes sequências de eventos ocorrerem durante uma mensagem EDI processamento nos serviços do BizTalk.

1. É recebida uma mensagem EDI de comerciais parceiro, Fabrikam.  Para receber mensagens EDI de parceiros comerciais, os serviços do BizTalk oferece suporte a protocolos de transporte, como FTP, SFTP, AS2 e HTTP/S.
2. O processamento de lado da receção de contrato de parceiro comercial desmonta a mensagem EDI para o formato XML.  Pode encaminhar a mensagem EDI desmontada (no formato XML) para pontos finais do Service Bus como um ponto de extremidade do reencaminhamento do Service Bus, o tópico do Service Bus, fila do Service Bus ou uma ponte de serviços do BizTalk.
3. As mensagens XML desmontadas, em seguida, poderiam ser recebidas do ponto de extremidade para ainda mais processamento personalizado.  Estes pontos finais poderiam ser processados por um componente no local ou uma instância de computação do Microsoft Azure para continuar o processamento da mensagem num serviço do Windows. o fluxo de trabalho (WF) ou o Windows Communication Foundation (WCF), por exemplo.
4. O processamento"lado de envio" de parceiro comercial contrato, em seguida, monta a mensagem XML no formato EDI e envia-os para o parceiro comercial, Contoso.  Para enviar mensagens EDI para parceiros comerciais, os serviços BizTalk suporta os mesmos protocolos usados para o recebimento de mensagens EDI.

Ainda mais este documento disponibiliza orientações conceptuais por migrar algumas das diferentes artefatos de EDI do BizTalk Server para os serviços BizTalk.

## <a name="sendreceive-ports-to-trading-partners"></a>Envio/receção de portas para parceiros comerciais
BizTalk Server, configurar localizações recebidas e portas de recebimento para receber mensagens EDI/XML de parceiros comerciais e configurar as portas de envio para enviar mensagens EDI/XML para o parceiro comercial. Em seguida, obstruir estas portas para um contrato de parceiro comercial utilizando a consola de administração do BizTalk Server. Nos serviços do BizTalk, as localizações onde recebe as mensagens de parceiros comerciais e de onde envia mensagens para parceiros comerciais são configuradas como parte do contrato de parceiro comercial propriamente dito (como parte das definições de transporte) no Portal de serviços do BizTalk.  Por isso, não temos o conceito de "portas de envio" e "locais de recebimento", por si só, dos serviços BizTalk. Para obter mais informações, consulte [criar contratos](https://msdn.microsoft.com/library/windowsazure/hh689908.aspx).

## <a name="pipelines-bridges"></a>Pipelines (pontes)
No EDI do BizTalk Server, os pipelines são entidades de processamento de mensagem também podem incluir a lógica personalizada para recursos de processamento específico, conforme exigido pelo aplicativo. Para os serviços do BizTalk, o equivalente seria uma ponte EDI. No entanto nos serviços do BizTalk, por enquanto, pontes EDI são "fechados".  Ou seja, não é possível adicionar suas próprias atividades personalizadas para uma bridge EDI. Qualquer processamento personalizado deve ser feito fora a ponte EDI em seu aplicativo, antes ou depois da mensagem aciona a ponte configurada como parte do contrato de parceiro comercial. EAI bridges tem a opção para efetuar o processamento personalizado. Se pretender que o processamento personalizado, pode usar EAI bridges antes ou depois da mensagem é processada pela ponte EDI. Para obter mais informações, consulte [como incluir código de personalizado em pontes](https://msdn.microsoft.com/library/azure/dn232389.aspx).

Pode inserir um fluxo de publicação/subscrição com código personalizado e/ou através de mensagens de filas e tópicos antes do contrato de parceiro comercial recebe a mensagem ou depois do contrato de processa a mensagem e a encaminha para um ponto de extremidade do barramento de serviço do Service Bus.

Ver **fluxo de mensagens/cenários** neste artigo para o padrão de fluxo de mensagens.

## <a name="agreements"></a>Contratos
Se estiver familiarizado com o BizTalk Server 2010 intercâmbio contratos de parceiros utilizado para processamento de EDI, em seguida, os serviços BizTalk contratos de parceiros comerciais ter um aspeto familiares. A maioria das definições de contrato é os mesmos e utiliza a mesma terminologia. Em alguns casos, as definições de contrato são muito mais simples em comparação com as mesmas definições no BizTalk Server. Os serviços BizTalk do Microsoft Azure suporta X12, AS2 e EDIFACT, de transporte.

Os serviços BizTalk do Microsoft Azure também fornece um **migração de dados de TPM** ferramenta para migrar de parceiros e contratos comerciais do módulo de parceiro de negociação do BizTalk Server para o Portal dos serviços BizTalk. A ferramenta de migração de dados de TPM está disponível como parte de um pacote de ferramentas, que pode ser transferido a partir da [MABS SDK](https://go.microsoft.com/fwlink/p/?LinkId=235057). O pacote também inclui um ficheiro Leia-me que fornece instruções sobre como utilizar a ferramenta e informações de resolução de problemas básicas para a ferramenta.

## <a name="schemas"></a>Esquemas
Os serviços BizTalk fornecem esquemas EDI, que podem ser utilizadas em soluções de serviços do BizTalk.  Além disso, os esquemas EDI do BizTalk Server também podem ser utilizadas com os serviços BizTalk porque o nó de raiz do esquema EDI é igual em BizTalk Server, bem como os serviços BizTalk. Portanto, conseguir diretamente tirar seus esquemas EDI do BizTalk Server e utilizá-los nas soluções EDI que estiver desenvolvendo usando os serviços BizTalk. Também pode transferir os esquemas do [MABS SDK](https://go.microsoft.com/fwlink/p/?LinkId=235057).

## <a name="maps-transforms"></a>Maps (transformações)
Mapas no BizTalk Server são chamados de transformações nos serviços do BizTalk. Mapas migrando do BizTalk Server para os serviços BizTalk podem ser uma das tarefas mais complexas para alcançar (dependendo da complexidade do mapa). A ferramenta de mapeamento utilizada para os serviços do BizTalk é diferente do mapeador de pontos do BizTalk. Embora o mapeador de igual, principalmente, o formato do mapa subjacente é diferente. O functoids (chamado **operações de mapa** dos serviços BizTalk) disponíveis para os usuários também são diferentes.  Na verdade, não pode utilizar diretamente um mapa de BizTalk nos serviços do BizTalk. Além disso, nem todos os functoids disponíveis no BizTalk Server estão disponíveis como operações de mapa dos serviços BizTalk.

### <a name="new-transform-operations"></a>Novas operações de transformação
Embora a lista de operações de mapa de transformação disponíveis pode parecer bastante diferente do mapeador de pontos do BizTalk Server, transforma os BizTalk Services têm novas formas de realizar as mesmas tarefas. Por exemplo, transforma os BizTalk Services ter **operações de lista** disponíveis. Isso não estava disponível no mapeador de pontos do BizTalk.  O **operações de lista** permitem-lhe criar e operar numa "lista", em que uma lista é um conjunto de itens (também conhecido como "linhas") e onde cada item pode ter vários membros (também conhecido como "colunas").  Pode ordenar a lista, selecione os itens com base numa condição, etc.

Outro exemplo de nova funcionalidade no transforma os serviços BizTalk são a **operações de ciclo**.  É difícil criar loops aninhados no mapeador de pontos do BizTalk Server.  Portanto, as operações de mapa de Loop são adicionadas para transformações de serviços do BizTalk.

Ainda outro exemplo é o **If-Then-Else** operação do mapa de expressão.  Fazer uma operação if-then-else era possível no mapeador de pontos do BizTalk, mas necessário functoids de vários para realizar uma tarefa aparentemente simples.

### <a name="migrating-biztalk-server-maps"></a>Migrando do BizTalk Server mapeia
Os serviços BizTalk do Microsoft Azure fornece uma ferramenta para migrar o BizTalk Server mapeia para transformações de serviços do BizTalk. O **BTMMigrationTool** está disponível como parte dos **ferramentas** pacote fornecido com o [download do SDK dos serviços BizTalk](https://go.microsoft.com/fwlink/p/?LinkId=235057). Para obter mais informações sobre a ferramenta, consulte [converter um mapa do BizTalk para dos serviços do BizTalk transformar](https://msdn.microsoft.com/library/windowsazure/hh949812.aspx).

Também pode ver um exemplo por Sandro Pereira, MVP do BizTalk, sobre como [migrar mapas do BizTalk Server para os serviços BizTalk transformações](https://social.technet.microsoft.com/wiki/contents/articles/23220.migrating-biztalk-server-maps-to-windows-azure-biztalk-services-wabs-maps.aspx).

## <a name="orchestrations"></a>Orquestrações
Se precisar de migrar a orquestração do BizTalk Server para o Microsoft Azure de processamento, as orquestrações precisaria ser reescrito porque o Microsoft Azure não suporta orquestrações do BizTalk Server em execução.  Poderia reescrever a funcionalidade de orquestração num serviço do Windows Workflow Foundation 4.0 (WF4).  Isso seria uma reescrita completa porque não existe atualmente nenhuma migração de orquestrações do BizTalk Server para o WF4. Aqui estão alguns recursos para o fluxo de trabalho do Windows:

* [*Como integrar um serviço de fluxo de trabalho do WCF com tópicos e filas do Service Bus* ](https://blogs.msdn.microsoft.com/paolos/2013/04/09/how-to-integrate-a-wcf-workflow-service-with-service-bus-queues-and-topics/) por Salvatori Paulo. 
* [*Criar aplicações com o Windows Workflow Foundation e do Azure* sessão](https://go.microsoft.com/fwlink/p/?LinkId=237314) da conferência Build 2011.
* [*Centro de programadores do Windows Workflow Foundation*](https://docs.microsoft.com/previous-versions/dotnet/articles/ee342461(v=msdn.10)).
* [*Documentação de Windows Workflow Foundation 4 (WF4)* ](https://msdn.microsoft.com/library/dd489441.aspx) no MSDN.

## <a name="other-considerations"></a>Outras considerações
Seguem-se algumas considerações que é necessário efetuar ao utilizar os serviços BizTalk.

### <a name="fallback-agreements"></a>Contratos de contingência
Processamento de EDI do BizTalk Server tem o conceito de "Contratos de contingência".  Os serviços BizTalk faz **não** tem um conceito de contrato de contingência até agora.  Consulte os tópicos de documentação do BizTalk [a função de contratos no processamento de EDI](https://go.microsoft.com/fwlink/p/?LinkId=237317) e [Configuração Global ou de propriedades do contrato de contingência](https://msdn.microsoft.com/library/bb245981.aspx) para obter informações sobre como os contratos de contingência são usados no BizTalk Servidor.

### <a name="routing-to-multiple-destinations"></a>Encaminhamento para vários destinos
Pontes de serviços do BizTalk, no respetivo estado atual não suporta encaminhamento de mensagens para vários destinos com um publicar-subscrever o modelo. Em vez disso, pode encaminhar mensagens de uma ponte de serviços do BizTalk para um tópico do Service Bus, que, em seguida, pode ter várias subscrições para receber a mensagem em mais do que um ponto de extremidade.

## <a name="see-also"></a>Consultar Também
[Soluções LOB no Azure](https://azure.microsoft.com/solutions/lob-applications)

[EDImessageflow]: ./media/biztalk-migrating-to-edi-guide/IC719455.png
