---
title: Obtenha apoio para o programa de marketplace comercial no Partner Center
description: Conheça as suas opções de suporte para o programa de marketplace comercial no Partner Center, incluindo como apresentar um pedido de suporte.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: navits09
ms.author: navits
ms.date: 01/19/2020
ms.openlocfilehash: 6075027124352746a3adbb8f6937d3787eb22b9d
ms.sourcegitcommit: fc401c220eaa40f6b3c8344db84b801aa9ff7185
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/20/2021
ms.locfileid: "98602559"
---
# <a name="support-for-the-commercial-marketplace-program-in-partner-center"></a>Apoio ao programa de marketplace comercial no Partner Center

A Microsoft fornece suporte para uma grande variedade de produtos e serviços. Encontrar a equipa de apoio certa é importante para garantir uma resposta adequada e oportuna. Considere os seguintes cenários, que devem ajudá-lo a encaminhar a sua consulta para a equipa apropriada:

- Se é editor e tem uma pergunta de um cliente, peça ao seu cliente para solicitar apoio utilizando os links de suporte no [portal Azure](https://portal.azure.com/).
- Se você é um editor e detetou um problema de segurança com uma aplicação em execução no Azure, consulte [como registar um bilhete de suporte para eventos de segurança](/azure/security/fundamentals/event-support-ticket). Os editores devem reportar casos de segurança suspeitos, incluindo incidentes de segurança e vulnerabilidades do seu software e ofertas de serviços Azure Marketplace, o mais rapidamente possível.
- Se é editor e tem uma pergunta relacionada com a sua app ou serviço, reveja as seguintes opções de suporte.

## <a name="get-help-or-open-a-support-ticket"></a>Obtenha ajuda ou abra um bilhete de apoio

1. Inscreva-se na sua conta de trabalho. Se ainda não o fez, terá de [criar uma conta Partner Center](partner-center-portal/create-account.md).

1. No menu no canto superior direito da página, selecione o ícone **De suporte.** O **painel de ajuda e apoio** aparece no lado direito da página.

1. Para obter ajuda no mercado comercial, **selecione Commercial Marketplace**.

   ![Suporte menu suspenso](./media/support/commercial-marketplace-support-pane.png)

1. Na caixa **de resumo de problema,** introduza uma breve descrição da questão.

1. Na caixa **de tipo Problema,** faça uma das seguintes:

    - **Opção 1**: Introduza palavras-chave como: Marketplace, app Azure, oferta SaaS, gestão de conta, gestão de chumbo, emissão de implementação, pagamento ou co-venda de ofertas migratórias. Em seguida, selecione um tipo de problema da lista recomendada que aparece.

    - **Opção 2**: **Selecione Procurar tópicos** da lista de **categorias** e, em seguida, selecione **Mercado Comercial**. Em seguida, selecione o **tópico** e **o subtópico** apropriados.

1. Depois de ter encontrado o tópico da sua escolha, selecione **Soluções de Revisão.**

    ![Passo seguinte](./media/support/next-step.png)

São apresentadas as seguintes opções:

- Para selecionar um tópico diferente, clique **em Selecionar um problema diferente.**
- Para ajudar a resolver o problema, reveja os passos e documentos recomendados, se disponível.

    ![Soluções recomendadas](./media/support/recommended-solutions.png)

Se não conseguir encontrar a sua resposta na autoajuda, **selecione Forneça detalhes de emissão**. Preencha todos os campos necessários para acelerar o processo de resolução e, em seguida, **selecione Enviar por isso**.

>[!Note]
>Se não se inscreveu no Partner Center, poderá ser obrigado a iniciar sessão antes de poder criar um bilhete.

## <a name="track-your-existing-support-requests"></a>Acompanhe os seus pedidos de apoio existentes

Para rever os seus bilhetes abertos e fechados, no menu de navegação à esquerda, selecione **Commercial Marketplace**  >  **Support**.

## <a name="record-issue-details-with-a-har-file"></a>Registar detalhes de emissão com um ficheiro HAR

Para ajudar os agentes de suporte a resolver o seu problema, considere anexar um ficheiro http Archive (HAR) ao seu bilhete de suporte. Os ficheiros HAR são registos de pedidos de rede num navegador web.

> [!WARNING]
> Os ficheiros HAR podem registar dados sensíveis sobre a sua conta Partner Center.

### <a name="microsoft-edge-and-google-chrome"></a>Microsoft Edge e Google Chrome

Para gerar um ficheiro HAR utilizando **o Microsoft Edge** ou o Google **Chrome:**

1. Vá à página web onde está a passar pelo problema.
2. No canto superior direito da janela, selecione o ícone elipse e, em seguida, **Mais ferramentas**  >  **Ferramentas Developer**. Pode premir F12 como atalho.
3. No painel de ferramentas do Desenvolvedor, selecione o separador **'Rede'.**
4. Selecione **Parar de gravar o registo de rede** e **limpar** para remover os registos existentes. O ícone do disco ficará cinzento.

    ![Como remover os registos existentes no Microsoft Edge ou no Google Chrome](media/support/chromium-stop-clear-session.png)

5. Selecione **registo de rede record** para começar a gravar. Quando começar a gravar, o ícone do disco ficará vermelho.

    ![Como começar a gravar no Microsoft Edge ou no Google Chrome](media/support/chromium-start-session.png)

6. Reproduza o problema que pretende resolver os problemas.
7. Depois de reproduzir o problema, selecione Parar de **gravar o registo de rede**.
8. Selecione **Export HAR**, marcado com um ícone de seta para baixo, e guarde o ficheiro.

    ![Como exportar um ficheiro HAR no Microsoft Edge ou no Google Chrome](media/support/chromium-network-export-har.png)

### <a name="mozilla-firefox"></a>Mozilla Firefox

Para gerar um ficheiro HAR utilizando **o Mozilla Firefox:**

1. Vá à página web onde está a passar pelo problema.
1. No canto superior direito da janela, selecione o ícone de elipse e, em seguida, **Web Developer**  >  **Toggle Tools**. Pode premir F12 como atalho.
1. Selecione o separador **'Rede'** e, em seguida, selecione **'Limpar'** para remover os registos existentes.

    ![Como remover os registos existentes no Mozilla Firefox](media/support/firefox-clear-session.png)

1. Reproduza o problema que pretende resolver os problemas.
1. Depois de reproduzir o problema, selecione **HAR Export/Import**  >  **Save All As HAR**.

    ![Como exportar um ficheiro HAR no Mozilla Firefox](media/support/firefox-network-export-har.png)

### <a name="apple-safari"></a>Apple Safari

Para gerar um ficheiro HAR utilizando **o Safari:**

1. Ative as ferramentas de desenvolvedor no Safari: selecione  >  **Safari Preferences**. Vá ao separador **Advanced** e, em seguida, selecione **o menu Show Develop na barra de menus.**
1. Vá à página web onde está a passar pelo problema.
1. Selecione **Desenvolver,** em seguida, **selecione Show Web Inspetor**.
1. Selecione o separador **'Rede'** e, em seguida, selecione **Itens de Rede Limpos** para remover os registos existentes.

    ![Como remover os registos existentes no Safari](media/support/safari-clear-session.png)

1. Reproduza o problema que pretende resolver os problemas.
1. Depois de reproduzir o problema, selecione **Export** e guarde o ficheiro.

    ![Como exportar um ficheiro HAR no Safari](media/support/safari-network-export-har.png)

## <a name="next-steps"></a>Passos seguintes

- [Atualizar uma oferta existente no Marketplace Comercial](partner-center-portal/update-existing-offer.md)
