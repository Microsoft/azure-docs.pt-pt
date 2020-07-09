---
title: Azure Quickstart - Criar um centro de eventos utilizando o portal Azure
description: Neste início rápido, irá aprender a criar um hub de eventos do Azure com o portal do Azure e, em seguida, enviar e receber eventos com o SDK .NET Standard.
ms.topic: quickstart
ms.date: 06/23/2020
ms.openlocfilehash: 9ca71dbb1a82e3fd9fe241e197b0bcbbfec2dcb8
ms.sourcegitcommit: 01cd19edb099d654198a6930cebd61cae9cb685b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/24/2020
ms.locfileid: "85323139"
---
# <a name="quickstart-create-an-event-hub-using-azure-portal"></a>Início Rápido: Criar um hub de eventos com o portal do Azure
Os Hubs de Eventos do Azure são uma plataforma de fluxo de Macrodados e um serviço de ingestão de eventos capaz de receber e processar milhões de eventos por segundo. Os Hubs de Eventos podem processar e armazenar eventos, dados ou telemetria produzidos por dispositivos e software distribuído. Os dados enviados para um hub de eventos podem ser transformados e armazenados em qualquer fornecedor de análise em tempo real ou adaptadores de armazenamento/criação de batches. Para uma descrição geral detalhada dos Hubs de Eventos, veja [Descrição geral dos Hubs de Eventos](event-hubs-about.md) e [Funcionalidades dos Hubs de Eventos](event-hubs-features.md).

Neste início rápido, vai criar um hub de eventos com o [portal do Azure](https://portal.azure.com).

## <a name="prerequisites"></a>Pré-requisitos

Para concluir este início rápido, certifique-se de que tem:

- Subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- [Estúdio Visual 2019)](https://www.visualstudio.com/vs) ou mais tarde.
- [SDK .NET Standard](https://www.microsoft.com/net/download/windows), versão 2.0 ou posterior.

## <a name="create-a-resource-group"></a>Criar um grupo de recursos

Um grupo de recursos é uma coleção lógica de recursos do Azure. Todos os recursos são implementados e geridos num grupo de recursos. Para criar um grupo de recursos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na navegação à esquerda, clique em **Grupos de recursos**. Em seguida, clique em **Adicionar**.

   ![Grupos de recursos - Botão Adicionar](./media/event-hubs-quickstart-portal/resource-groups1.png)

2. Para **Subscrição**, selecione o nome da subscrição Azure na qual pretende criar o grupo de recursos.
3. Digite um nome único **para o grupo de recursos.** O sistema verifica imediatamente a disponibilidade do nome na subscrição do Azure atualmente selecionada.
4. Selecione uma **região** para o grupo de recursos.
5. Selecione **Review + Criar**.

   ![Grupo de recursos - criar](./media/event-hubs-quickstart-portal/resource-groups2.png)
6. Na página **'Rever + Criar',** selecione **Criar**. 

## <a name="create-an-event-hubs-namespace"></a>Criar um espaço de nomes dos Hubs de Eventos

Um espaço de nomes dos Hubs de Eventos fornece um contentor de âmbito exclusivo, referenciado pelo respetivo nome de domínio completamente qualificado, no qual cria um ou mais hubs de eventos. Para criar um espaço de nomes no seu grupo de recursos com o portal, faça as ações seguintes:

1. No portal do Azure, clique em **Criar um recurso** na parte superior esquerda do ecrã.
2. Selecione **todos os serviços** no menu esquerdo e selecione **star `*` ()** ao lado **de Event Hubs** na categoria **Analytics.** Confirme que **os Hubs de Evento são adicionados** aos **FAVORITOS** no menu de navegação à esquerda. 
    
   ![Pesquisa rumo aos Centros de Eventos](./media/event-hubs-quickstart-portal/select-event-hubs-menu.png)
3. Selecione **Os Centros de Eventos** em **FAVORITOS** no menu de navegação à esquerda e selecione **Adicione** na barra de ferramentas.

   ![Botão Adicionar](./media/event-hubs-quickstart-portal/event-hubs-add-toolbar.png)
4. Na página Criar espaço **de nome,** tome os seguintes passos:
    1. Selecione a **subscrição** na qual pretende criar o espaço de nomes.
    2. Selecione o **grupo de recursos** que criou no passo anterior. 
    3. Insira um **nome** para o espaço de nomes. O sistema verifica imediatamente a disponibilidade do nome.
    4. Selecione um **local** para o espaço de nomes.    
    5. Escolha o **nível de preços** (Básico ou Standard).  
    6. Deixe as definições das unidades de **produção** como estão. Para conhecer as unidades de produção, consulte [a escalabilidade do Event Hubs](event-hubs-scalability.md#throughput-units)  
    5. Selecione **'Rever + Criar** na parte inferior da página'.

       ![Criar um espaço de nomes do hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub1.png)
   6. Na página **'Rever + Criar',** rever as definições e selecionar **Criar**. Aguarde pela conclusão da implementação. 

       ![Rever + criar página](./media/event-hubs-quickstart-portal/review-create.png)
   7. Na página **'Implementação',** selecione **Ir para o recurso** para navegar na página para o seu espaço de nome. 

      ![Implementação completa - vá para o recurso](./media/event-hubs-quickstart-portal/deployment-complete.png)
   8. Confirme que vê a página **De Nomes do Event Hubs** semelhante ao seguinte exemplo: 

       ![Página inicial para o espaço de nome](./media/event-hubs-quickstart-portal/namespace-home-page.png)       

       > [!NOTE]
       > O Azure Event Hubs fornece-lhe um ponto final kafka. Este ponto final permite que o seu espaço de nomes De Event Hubs compreenda de forma nativa o protocolo de mensagem [Apache Kafka](https://kafka.apache.org/intro) e ASPIs. Com esta capacidade, pode comunicar com os seus centros de eventos como faria com tópicos kafka sem alterar os seus clientes de protocolo ou executar os seus próprios clusters. O Event Hubs suporta as [versões Apache Kafka 1.0](https://kafka.apache.org/10/documentation.html) e posterior. Para mais informações, consulte [Use Event Hubs a partir de aplicações Apache Kafka.](event-hubs-for-kafka-ecosystem-overview.md)
    
## <a name="create-an-event-hub"></a>Criar um hub de eventos

Para criar um hub de eventos no espaço de nomes, faça as ações seguintes:

1. Na página De Evento Hubs Namespace, selecione **'Centros de Eventos'** no menu esquerdo.
1. Na parte superior da janela, clique em **+ Hub de Eventos**.
   
    ![Adicionar Hub de Eventos - botão](./media/event-hubs-quickstart-portal/create-event-hub4.png)
1. Escreva um nome para o seu hub de eventos e, em seguida, clique em **Criar**.
   
    ![Criar hub de eventos](./media/event-hubs-quickstart-portal/create-event-hub5.png)
4. Pode verificar o estado da criação do centro de eventos em alertas. Após a criação do centro de eventos, vê-o na lista de centros de eventos, como mostrado na seguinte imagem:

    ![Centro de eventos criado](./media/event-hubs-quickstart-portal/event-hub-created.png)

## <a name="next-steps"></a>Passos seguintes

Neste artigo, criou um grupo de recursos, um espaço de nomes de Hubs de Eventos e um hub de eventos. Para instruções passo a passo para enviar eventos para (ou) receber eventos de um centro de eventos, consulte os tutoriais **de Envio e receber eventos:** 

- [.NET Core](get-started-dotnet-standard-send-v2.md)
- [Java](get-started-java-send-v2.md)
- [Python](get-started-python-send-v2.md)
- [JavaScript](get-started-node-send-v2.md)
- [Ir](event-hubs-go-get-started-send.md)
- [C (apenas enviar)](event-hubs-c-getstarted-send.md)
- [Apache Storm (apenas receber)](event-hubs-storm-getstarted-receive.md)


[Azure portal]: https://portal.azure.com/
[3]: ./media/event-hubs-quickstart-portal/sender1.png
[4]: ./media/event-hubs-quickstart-portal/receiver1.png
