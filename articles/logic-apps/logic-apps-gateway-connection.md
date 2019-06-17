---
title: Aceder a origens de dados no local a partir do Azure Logic Apps | Documentos da Microsoft
description: Ligar a dados no local origens de aplicações lógicas através da criação de um gateway de dados no local
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 0580fe09c2cb6569724a9b4365233a3142645a47
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65546263"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Ligar a origens de dados no local a partir do Azure Logic Apps

Para aceder a origens de dados no local a partir as logic apps, crie um recurso de gateway de dados no local no portal do Azure. As logic apps, em seguida, podem utilizar o [conectores no local](../logic-apps/logic-apps-gateway-install.md#supported-connections). Este artigo mostra como criar o seu recurso de gateway do Azure *após* [transferir e instalar o gateway no seu computador local](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Para ligar a redes virtuais do Azure, considere criar um [ *ambiente de serviço de integração* ](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em vez disso. 

Para obter informações sobre como utilizar o gateway com outros serviços, veja estes artigos:

* [Gateway de dados do Microsoft Power BI no local](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Gateway de dados do Microsoft Flow no local](https://flow.microsoft.com/documentation/gateway-manage/)
* [Gateway de dados do Microsoft PowerApps no local](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Gateway de dados no local do Analysis Services do Azure](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Pré-requisitos

* Tiver já [baixado e instalado o gateway de dados num computador local](../logic-apps/logic-apps-gateway-install.md).

* A instalação do gateway já não está associada a um recurso de gateway no Azure. Pode ligar a sua instalação do gateway apenas para um recurso de gateway, o que acontece quando criar o recurso de gateway e selecione a sua instalação do gateway. Essa vinculação torna a instalação do gateway indisponível para outros recursos.

* Quando iniciar sessão no portal do Azure e criar o recurso de gateway, certifique-se de utilizar a mesma início de sessão na conta que foi utilizada anteriormente para [instalar o gateway de dados no local](../logic-apps/logic-apps-gateway-install.md#requirements) juntamente com o mesmo [subscrição do Azure ](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) que foi utilizada para instalar o gateway. Se não tiver uma subscrição do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se uma conta gratuita do Azure</a>.

* Para criar e manter o recurso de gateway no portal do Azure, sua [conta de serviço do Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) necessita de pelo menos **contribuinte** permissões. O gateway de dados no local é executado como um serviço do Windows e está configurado para utilizar `NT SERVICE\PBIEgwService` credenciais de início de sessão do serviço para o Windows. 

  > [!NOTE]
  > A conta de serviço é diferente da conta utilizada para ligar a dados no local de Windows origens e do Azure conta escolar ou profissional utilizada para iniciar sessão nos serviços cloud.

## <a name="download-and-install-gateway"></a>Transferir e instalar gateway

Antes de continuar com os passos neste artigo, certificar-se de que o gateway já está instalado num computador local.
Se ainda não o fez, siga os passos para [transferir e instalar o gateway de dados no local](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Criar recurso do Azure para o gateway

Depois de instalar o gateway num computador local, em seguida, pode criar um recurso do Azure para o seu gateway. Este passo também associa o seu recurso de gateway com a sua subscrição do Azure.

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>. Certifique-se de utilizar o mesmo trabalho do Azure ou endereço de e-mail utilizado para instalar o gateway de instituição de ensino.

2. No menu principal do Azure, selecione **criar um recurso** > 
**integração** > **gateway de dados no local**.

   ![Localizar "gateway de dados no local"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Sobre o **criar gateway de ligação** , indique estas informações para o seu recurso de gateway:

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Nome** | O nome do seu recurso de gateway | 
   | **Subscrição** | Nome da sua subscrição do Azure, que deve ser a mesma subscrição que a sua aplicação lógica. A assinatura padrão baseia-se a conta do Azure que utilizou para iniciar sessão. | 
   | **Grupo de recursos** | O nome para o [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) para organizar os recursos relacionados | 
   | **Location** | Azure restringe esta localização para a mesma região que foi selecionada para o serviço de nuvem de gateway durante [instalação do gateway](../logic-apps/logic-apps-gateway-install.md). <p>**Nota**: Certifique-se de que a localização de serviço de nuvem de gateway corresponde a esta localização de recursos de gateway. Caso contrário, a instalação de gateway pode não aparecer na lista de gateways instalados para que possa selecioná-se no próximo passo. Pode utilizar diferentes regiões para o seu recurso de gateway e para a aplicação lógica. | 
   | **Nome de instalação** | Se a instalação do gateway não estiver selecionada, selecione o gateway que instalou anteriormente. | 
   | | | 

   Segue-se um exemplo:

   ![Forneça detalhes para criar o gateway de dados no local](./media/logic-apps-gateway-connection/createblade.png)

4. Para adicionar o recurso de gateway ao dashboard do Azure, selecione **afixar ao dashboard**. Quando tiver terminado, escolha **Create** (Criar).

   Para localizar ou ver o gateway em qualquer altura, no menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, introduza "no local gateways de dados" e, em seguida, selecione **Gateways de dados no local**.

   ![Localizar "Gateways de dados no local"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ligar aos dados no local

Depois de criar o seu recurso de gateway e associar a sua subscrição do Azure com este recurso, agora pode criar uma ligação entre a aplicação lógica e sua origem de dados no local ao utilizar o gateway.

1. No portal do Azure, crie ou abra a aplicação lógica no Estruturador da aplicação lógica.

2. Adicionar um conector que suporta ligações no local, por exemplo, **SQL Server**.

3. Agora configure a sua ligação:

   1. Selecione **ligar através do gateway de dados no local**. 

   2. Para **Gateways**, selecione o recurso de gateway que criou anteriormente. 

      Embora a localização de ligação de gateway tem de existir na mesma região que a sua aplicação lógica, pode selecionar um gateway numa região diferente.

   3. Forneça um nome de ligação exclusivo e outras informações necessárias. 

      O nome da ligação exclusivo ajuda-o a identificar facilmente mais tarde, essa conexão, especialmente quando cria várias ligações. Se aplicável, incluem também o domínio qualificado para o seu nome de utilizador.
   
      Segue-se um exemplo:

      ![Criar ligação entre o gateway de dados e de aplicação lógica](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Quando tiver terminado, escolha **Create** (Criar). 

A ligação de gateway está agora pronta para a sua aplicação lógica utilizar.

## <a name="edit-connection"></a>Editar ligação

Depois de criar uma ligação de gateway para a aplicação lógica, pode querer atualizar mais tarde as definições para essa ligação específica.

1. Localize a ligação de gateway:

   * Para localizar todas as ligações de API para apenas a aplicação lógica, no menu da sua aplicação lógica, em **ferramentas de desenvolvimento**, selecione **ligações de API**. 
   
     ![Aceda à sua aplicação lógica, selecione "Ligações de API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Para localizar todas as ligações de API associadas à subscrição do Azure: 

     * A partir do menu principal do Azure, aceda a **todos os serviços** > **Web** > **ligações de API**. 
     * Em alternativa, no menu principal do Azure, aceda a **todos os recursos**.

2. Selecione a ligação de gateway que pretende e, em seguida, escolha **ligação editar API**.

   > [!TIP]
   > Se as atualizações não entrem em vigor, tente [interromper e reiniciar o serviço Windows do gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Eliminar recurso do gateway

Para criar um recurso de gateway diferentes, associar o seu gateway com um recurso diferente ou remova o recurso de gateway, pode eliminar o recurso do gateway sem afetar a instalação do gateway. 

1. A partir do menu principal do Azure, aceda a **todos os recursos**. 

2. Localize e selecione o recurso de gateway.

3. Se ainda não estiver selecionada, no menu de recursos do gateway, selecione **Gateway de dados no local**. 

4. Na barra de ferramentas, recursos, escolha **eliminar**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos Seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários para aplicações lógicas comuns](./logic-apps-examples-and-scenarios.md)
