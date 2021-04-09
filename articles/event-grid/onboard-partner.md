---
title: A bordo como parceiro da Azure Event Grid usando o portal Azure
description: Utilize o portal Azure para embarcar num parceiro da Azure Event Grid.
ms.topic: conceptual
ms.date: 10/29/2020
ms.openlocfilehash: 44dece4d46a6d702d48fa49983818986fcd59f7b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "98050957"
---
# <a name="onboard-as-an-azure-event-grid-partner-using-the-azure-portal"></a>A bordo como parceiro da Azure Event Grid utilizando o portal Azure
Este artigo descreve a forma como os fornecedores de SaaS de terceiros, também conhecidos como editores de eventos ou parceiros, estão a bordo da Event Grid para poderem publicar eventos a partir dos seus serviços e como esses eventos são consumidos pelos utilizadores finais.

> [!IMPORTANT]
> Se não está familiarizado com os Eventos de Parceiros, consulte a visão geral dos [Eventos parceiros](partner-events-overview.md) para uma introdução detalhada de conceitos-chave que são fundamentais para compreender e seguir os passos deste artigo.

## <a name="onboarding-process-for-event-publishers-partners"></a>Processo de embarque para editores de eventos (parceiros)
Em resumo, permitir que os eventos do seu serviço sejam consumidos pelos utilizadores normalmente envolve o seguinte processo:

1. **Comunique o seu interesse** em se tornar um parceiro para a equipa de serviço de Grade de Eventos antes de prosseguir com os próximos passos.
1. Crie um tipo de tópico parceiro criando um **registo.** 
1. Criar um **espaço de nomes.**
1. Crie um **canal de eventos** e **um tópico de parceiro** (passo único).
1. Teste a funcionalidade Eventos parceiros de ponta a ponta.

Para #4 passo, deve decidir que tipo de experiência do utilizador pretende proporcionar. Tem as seguintes opções:
- Forneça a sua própria solução, tipicamente uma experiência web graphical user interface (GUI), hospedada sob o seu domínio usando o nosso SDK e/ou REST API para criar um canal de eventos e o seu tópico parceiro correspondente. Com esta opção, pode solicitar ao utilizador a subscrição e o grupo de recursos sob o qual irá criar um tópico de parceiro.
- Utilize o portal Azure ou o CLI para criar o canal de eventos e o tópico do parceiro associado. Com esta opção, você deve ter entrado na subscrição Azure do utilizador de alguma forma e grupo de recursos sob o qual você vai criar um tópico parceiro. 

Este artigo mostra-lhe como embarcar como um parceiro da Azure Event Grid usando o portal Azure. 

> [!NOTE]
> Registar um tipo de tópico parceiro é um passo opcional. Para ajudá-lo a decidir se deve criar um tipo de tópico de parceiro, consulte [Recursos geridos pela editora de eventos.](partner-events-overview.md#resources-managed-by-event-publishers)

## <a name="communicate-your-interest-in-becoming-a-partner"></a>Comunique o seu interesse em se tornar um parceiro
Preencha [este formulário](https://aka.ms/gridpartnerform) e contacte a equipa da Grelha de Eventos em [GridPartner@microsoft.com](mailto:GridPartner@microsoft.com) . Teremos uma conversa consigo fornecendo informações detalhadas sobre casos de utilização de Eventos Parceiros, personas, processo de embarque, funcionalidade, preços e muito mais.

## <a name="prerequisites"></a>Pré-requisitos
Para completar os passos restantes, certifique-se de que tem:

- Uma subscrição do Azure. Se não tiver uma, [crie uma conta gratuita](https://azure.microsoft.com/free/) antes de começar.
- Um [inquilino](../active-directory/develop/quickstart-create-new-tenant.md)de Azure.

## <a name="register-a-partner-topic-type-optional"></a>Registar um tipo de tópico parceiro (opcional)
1. Inicie sessão no [portal do Azure](https://portal.azure.com/).
2. Selecione **todos os serviços** do painel de navegação à esquerda e, em seguida, digite **registos de parceiros de grelha de evento** na barra de pesquisa e selecione-o. 
1. Na página **'Registos de Parceiros de Grelha de Eventos',** selecione **+ Adicione** na barra de ferramentas. 

    :::image type="content" source="./media/onboard-partner/add-partner-registration-link.png" alt-text="Adicionar link de registo de parceiros":::
1. Na página **'Criar Registos de Tipo de Tópico' - Bases, insira** as seguintes informações: 
    1. Na secção detalhes do **Projeto,** siga estes passos:
        1. Selecione a sua **subscrição Azure**. 
        1. Selecione um grupo de **recursos** Azure existente ou crie um novo grupo de recursos. 
    1. Na secção de Detalhes de **Registo,** siga estes passos:
        1. Para **nome de registo,** insira o nome para a inscrição. 
        1. Para **o nome da Organização,** insira o nome da sua organização. 
    1. Na secção **de tipo de recurso Partner,** introduza detalhes sobre o seu tipo de recurso que serão exibidos na página de criação de tópicos do **parceiro:** 
        1. Para **o nome do tipo de recurso parceiro,** insira o nome para o tipo de recurso. Este será o tipo de tópico de parceiro que será criado na sua subscrição do Azure. 
        2. Para **o nome Display,** introduza um nome de exibição fácil de utilização para o tipo de tópico do parceiro (recurso). 
        3. Introduza uma **descrição para o tipo de recurso**. 
        4. Introduza uma **descrição para o cenário.** Deve explicar as formas ou cenários em que os tópicos parceiros para os seus recursos podem ser usados.  

            :::image type="content" source="./media/onboard-partner/create-partner-registration-page.png" alt-text="Criar registo de parceiros":::            
1. Selecione **Seguinte: Serviço personalizado** na parte inferior da página. No separador Serviço de Apoio ao **Cliente** da página **'Criar Registo de** Parceiros', introduza informações que os utilizadores assinantes utilizarão para o contactar em caso de problema com a fonte do evento:
    1. Introduza o **número de telefone.**
    1. Introduza **a extensão** para o número de telefone.
    1. Introduza um **URL** do site de suporte. 
    
        :::image type="content" source="./media/onboard-partner/create-partner-registration-customer-service.png" alt-text="Criar registo de parceiros - atendimento ao cliente":::        
1. Selecione **Seguinte: Etiquetas** na parte inferior da página. 
1. Na página **Tags,** configuure os seguintes valores. 
    1. Insira um **nome** e um **valor** para a etiqueta que pretende adicionar. Este passo é **opcional.** 
    1. Selecione **Review + crie** na parte inferior da página para criar o registo (tipo de tópico parceiro).

## <a name="create-a-partner-namespace"></a>Criar um espaço de nome de parceiro

1. No portal Azure, selecione **Todos os serviços** do menu de navegação à esquerda e, em seguida, digite **Os espaços de nome do Parceiro de Grelha de Evento na** barra de pesquisa e, em seguida, selecione-o da lista. 
1. Na página **De Espaços com Nomes parceiros de grelha de evento,** selecione **+ Adicione** na barra de ferramentas. 
    
    :::image type="content" source="./media/onboard-partner/add-partner-namespace-link.png" alt-text="Espaços com nomes de parceiros - Adicionar link":::
1. Na página **Create Partner Namespace - Basics, especifique** as seguintes informações.
    1. Na secção detalhes do **Projeto,** faça os seguintes passos: 
        1. Selecione uma **subscrição Azure**.
        1. Selecione um grupo de **recursos** existente ou crie um grupo de recursos. 
    1. Na secção detalhes do **Namespace,** faça os seguintes passos:
        1. Insira um **nome** para o espaço de nomes. 
        1. Selecione um **local** para o espaço de nomes. 
    1. Na secção **de Detalhes de Inscrição,** faça as seguintes etapas para associar o espaço de nome a um registo de parceiros. 
        1. Selecione a **subscrição** na qual existe o registo do parceiro. 
        1. Selecione o **grupo de recursos** que contém o registo do parceiro. 
        1. Selecione o registo do **parceiro** na lista de drop-down.
    1. Selecione **Seguinte: Etiquetas** na parte inferior da página.

        :::image type="content" source="./media/onboard-partner/create-partner-namespace-basics-page.png" alt-text="Criar espaço de nome de parceiro - página básica":::
1. Na página **Tags,** adicione tags (opcional).
    1. Insira um **nome** e um **valor** para a etiqueta que pretende adicionar. Este passo é **opcional.**
    1. Selecione **'Rever + criar'** na parte inferior da página.         
1. Na página **'Rever + criar',** rever os detalhes e selecionar **Criar.** 

## <a name="create-an-event-channel"></a>Criar um canal de eventos
> [!IMPORTANT]
> Terá de solicitar ao seu utilizador uma subscrição Azure, grupo de recursos, localização e nome de tópico de parceiro para criar um tópico parceiro que o seu utilizador irá possuir e gerir.

1. Aceda à página **geral** do espaço de nomes que criou. 

    :::image type="content" source="./media/onboard-partner/partner-namespace-overview.png" alt-text="Espaço de nome do parceiro - página geral":::
    partner-namespace-overview.png
1. Selecione **+ Canal de Eventos** na barra de ferramentas. 
1. Na página **'Criar Canal de Eventos' - Basics, especifique** as seguintes informações. 
    1. Na secção **de detalhes do Canal,** faça estes passos:
        1. Para **o nome do canal event,** insira um nome para o canal de eventos. 
        1. Insira a **fonte**. Consulte [as especificações cloud Events 1.0](https://github.com/cloudevents/spec/blob/v1.0/spec.md#source-1) para obter uma ideia de um valor adequado para a fonte. Além disso, consulte [este exemplo de esquema de Cloud Events](cloud-event-schema.md#sample-event-using-cloudevents-schema).
    1. Na secção de detalhes do **Destino,** insira detalhes para o tópico do parceiro de destino que será criado para este canal de eventos. 
        1. Introduza o **ID da subscrição** na qual o tópico do parceiro será criado. 
        1. Insira o **nome do grupo de recursos** no qual o recurso tópico parceiro será criado. 
        1. Insira um **nome para o tópico do parceiro**. 
    1. Selecione **Seguinte: Filtros** na parte inferior da página. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-basics-page.png" alt-text="Criar canal de eventos - página básica":::
1. Na página **Filtros,** adicione filtros. execute os seguintes passos:
    1. Filtrar os atributos de cada evento. Só os eventos que combinam com todos os filtros são entregues. Podem ser especificados até 25 filtros. Comparações são insensíveis a casos. As teclas válidas utilizadas para filtros variam em função do esquema do evento. No exemplo `eventid` seguinte, `source` , e pode ser usado para `eventtype` `eventtypeversioin` chaves. Também pode utilizar propriedades personalizadas dentro da carga útil de dados, utilizando o `.` como operador de nidificação. Por exemplo: `data` `data.key` . . . `data.key1.key2` .
    1. Selecione **Seguinte: Funcionalidades adicionais** na parte inferior da página. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-filters-page.png" alt-text="Criar canal de eventos - página de filtros":::
        create-event-channel-filters-page.png
1. Na página **de funcionalidades adicionais,** pode definir um tempo de **validade** e **descrição para o tópico do parceiro.** 
    1. O **tempo de validade** é o momento em que o tópico e o seu canal de eventos associados serão automaticamente eliminados se não forem ativados pelo cliente. Um incumprimento de sete dias é usado no caso de não ser providenciado um tempo. Selecione a caixa de verificação para especificar o seu próprio tempo de validade. 
    1. Como este tópico é um recurso que não é criado pelo utilizador, uma **descrição** pode ajudar o utilizador a compreender a natureza deste tópico. Será fornecida uma descrição genérica se nenhuma for definida. Selecione a caixa de verificação para definir a descrição do tópico do seu próprio parceiro. 
    1. Selecione **Seguinte: Rever + criar**. 
    
        :::image type="content" source="./media/onboard-partner/create-event-channel-additional-features-page.png" alt-text="Criar canal de eventos - página de funcionalidades adicionais":::
1. No **Review + criar,** rever as definições e selecione **Criar** para criar o canal de eventos. 

## <a name="next-steps"></a>Passos seguintes
- [Visão geral dos tópicos do parceiro](./partner-events-overview.md)
- [Formulário de tópicos de parceiros no formato de embarque](https://aka.ms/gridpartnerform)
- [Tópico parceiro Auth0](auth0-overview.md)
- [Como usar o tópico parceiro Auth0](auth0-how-to.md)