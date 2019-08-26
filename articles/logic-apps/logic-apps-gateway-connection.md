---
title: Acessar fontes de dados locais de aplicativos lógicos do Azure | Microsoft Docs
description: Conectar-se a fontes de dados locais de aplicativos lógicos criando um gateway de dados local
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: arthii, LADocs
ms.topic: article
ms.date: 10/01/2018
ms.openlocfilehash: 029dc8daaf456c155d46eefa699772882bdabee5
ms.sourcegitcommit: 6d2a147a7e729f05d65ea4735b880c005f62530f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/22/2019
ms.locfileid: "69982875"
---
# <a name="connect-to-on-premises-data-sources-from-azure-logic-apps"></a>Conectar-se a fontes de dados locais de aplicativos lógicos do Azure

Para acessar fontes de dados locais de seus aplicativos lógicos, crie um recurso de gateway de dados local no portal do Azure. Seus aplicativos lógicos podem usar os [conectores locais](../logic-apps/logic-apps-gateway-install.md#supported-connections). Este artigo mostra como criar o recurso de gateway do Azure *depois* de [baixar e instalar o gateway no computador local](../logic-apps/logic-apps-gateway-install.md). 

> [!TIP]
> Para se conectar às redes virtuais do Azure, considere criar um [*ambiente de serviço de integração*](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md) em vez disso. 

Para obter informações sobre como usar o gateway com outros serviços, consulte estes artigos:

* [Gateway de dados local do Microsoft Power BI](https://powerbi.microsoft.com/documentation/powerbi-gateway-onprem/)
* [Microsoft Flow gateway de dados local](https://flow.microsoft.com/documentation/gateway-manage/)
* [Microsoft PowerApps gateway de dados local](https://powerapps.microsoft.com/tutorials/gateway-management/)
* [Azure Analysis Services Gateway de dados local](../analysis-services/analysis-services-gateway.md)

## <a name="prerequisites"></a>Pré-requisitos

* Você já [baixou e instalou o gateway de dados em um computador local](../logic-apps/logic-apps-gateway-install.md).

* A instalação do gateway ainda não está associada a um recurso de gateway no Azure. Você pode vincular a instalação do gateway somente a um recurso de gateway, o que acontece quando você cria o recurso de gateway e seleciona a instalação do gateway. Essa vinculação torna a instalação do gateway indisponível para outros recursos.

* Quando você entrar no portal do Azure e criar o recurso de gateway, use a mesma conta de entrada que foi usada anteriormente para [instalar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md#requirements) junto com a mesma [assinatura do Azure](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access) que foi usada para instalar o Gateway. Se você ainda não tiver uma assinatura do Azure, <a href="https://azure.microsoft.com/free/" target="_blank">Inscreva-se para obter uma conta gratuita do Azure</a>.

* Para criar e manter o recurso de gateway no portal do Azure, sua [conta de serviço do Windows](../logic-apps/logic-apps-gateway-install.md#windows-service-account) precisa de pelo menos permissões de **colaborador** . O gateway de dados local é executado como um serviço do Windows e é configurado para usar `NT SERVICE\PBIEgwService` para as credenciais de logon do serviço do Windows. 

  > [!NOTE]
  > A conta de serviço do Windows difere da conta usada para se conectar a fontes de dados locais e da conta corporativa ou de estudante do Azure usada para entrar nos serviços de nuvem.

## <a name="download-and-install-gateway"></a>Baixar e instalar o gateway

Antes de continuar com as etapas neste artigo, verifique se o gateway já está instalado em um computador local.
Se você ainda não fez isso, siga as etapas para [baixar e instalar o gateway de dados local](../logic-apps/logic-apps-gateway-install.md). 

<a name="create-gateway-resource"></a>

## <a name="create-azure-resource-for-gateway"></a>Criar recurso do Azure para gateway

Depois de instalar o gateway em um computador local, você pode criar um recurso do Azure para seu gateway. Essa etapa também associa o recurso de gateway à sua assinatura do Azure.

1. Inicie sessão no <a href="https://portal.azure.com" target="_blank">portal do Azure</a>. Certifique-se de usar o mesmo endereço de email corporativo ou de estudante do Azure usado para instalar o gateway.

2. No menu principal do Azure, selecione **criar um recurso** > 
**integração** > de recursos**Gateway de dados local**.

   ![Localizar "gateway de dados local"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway.png)

3. Na página **criar gateway de conexão** , forneça estas informações para o recurso de gateway:

   | Propriedade | Descrição | 
   |----------|-------------|
   | **Nome do Recurso** | O nome do recurso de gateway, que pode conter apenas letras`-`, números, hifens (), sublinhados (`_`), parênteses `)`(`(`,) e pontos`.`(). | 
   | **Subscrição** | O nome da sua assinatura do Azure, que deve ser a mesma assinatura do seu aplicativo lógico. A assinatura padrão é baseada na conta do Azure que você usou para entrar. | 
   | **Grupo de recursos** | O nome do [grupo de recursos do Azure](../azure-resource-manager/resource-group-overview.md) para organizar recursos relacionados | 
   | **Location** | O Azure restringe esse local à mesma região que foi selecionada para o serviço de nuvem do gateway durante a [instalação do gateway](../logic-apps/logic-apps-gateway-install.md). <p>**Nota**: Verifique se esse local de recurso do gateway corresponde ao local do serviço de nuvem do gateway. Caso contrário, a instalação do gateway pode não aparecer na lista de gateways instalados para que você selecione na próxima etapa. Você pode usar regiões diferentes para o recurso de gateway e para seu aplicativo lógico. | 
   | **Nome da instalação** | Se a instalação do gateway ainda não estiver selecionada, selecione o gateway que você instalou anteriormente. | 
   | | | 

   Segue-se um exemplo:

   ![Forneça detalhes para criar seu gateway de dados local](./media/logic-apps-gateway-connection/createblade.png)

4. Para adicionar o recurso de gateway ao seu painel do Azure, selecione **fixar no painel**. Quando tiver terminado, escolha **Create** (Criar).

   Para localizar ou exibir seu gateway a qualquer momento, no menu principal do Azure, selecione **todos os serviços**. 
   Na caixa de pesquisa, insira "gateways de dados locais" e, em seguida, selecione **gateways de dados locais**.

   ![Localizar "gateways de dados locais"](./media/logic-apps-gateway-connection/find-on-premises-data-gateway-enterprise-integration.png)

<a name="connect-logic-app-gateway"></a>

## <a name="connect-to-on-premises-data"></a>Ligar aos dados no local

Depois de criar o recurso de gateway e associar sua assinatura do Azure a esse recurso, agora você pode criar uma conexão entre seu aplicativo lógico e sua fonte de dados local usando o gateway.

1. No portal do Azure, crie ou abra seu aplicativo lógico no designer de aplicativo lógico.

2. Adicione um conector que ofereça suporte a conexões locais, por exemplo, **SQL Server**.

3. Agora configure sua conexão:

   1. Selecione **conectar por meio do gateway de dados local**. 

   2. Para **gateways**, selecione o recurso de gateway que você criou anteriormente. 

      Embora o local de conexão do gateway deva existir na mesma região que o aplicativo lógico, você pode selecionar um gateway em uma região diferente.

   3. Forneça um nome de conexão exclusivo e as outras informações necessárias. 

      O nome de conexão exclusivo ajuda a identificar facilmente essa conexão mais tarde, especialmente quando você cria várias conexões. Se aplicável, inclua também o domínio qualificado para seu nome de usuário.
   
      Segue-se um exemplo:

      ![Criar conexão entre o aplicativo lógico e o gateway de dados](./media/logic-apps-gateway-connection/blankconnection.png)

   4. Quando tiver terminado, escolha **Create** (Criar). 

Sua conexão de gateway agora está pronta para uso do seu aplicativo lógico.

## <a name="edit-connection"></a>Editar conexão

Depois de criar uma conexão de gateway para seu aplicativo lógico, talvez você queira atualizar posteriormente as configurações para essa conexão específica.

1. Localize sua conexão de gateway:

   * Para localizar todas as conexões de API apenas para seu aplicativo lógico, no menu do aplicativo lógico, em **ferramentas de desenvolvimento**, selecione conexões de **API**. 
   
     ![Vá para seu aplicativo lógico, selecione "conexões de API"](./media/logic-apps-gateway-connection/logic-app-find-api-connections.png)

   * Para localizar todas as conexões de API associadas à sua assinatura do Azure: 

     * No menu principal do Azure, vá para **todos os serviços** > **conexões de API** **da Web** > . 
     * Ou, no menu principal do Azure, vá para **todos os recursos**.

2. Selecione a conexão de gateway desejada e escolha **Editar conexão de API**.

   > [!TIP]
   > Se as atualizações não entrarem em vigor, tente [interromper e reiniciar o serviço Windows do gateway](./logic-apps-gateway-install.md#restart-gateway).

<a name="change-delete-gateway-resource"></a>

## <a name="delete-gateway-resource"></a>Excluir recurso de gateway

Para criar um recurso de gateway diferente, associar o gateway a um recurso diferente ou remover o recurso de gateway, você pode excluir o recurso de gateway sem afetar a instalação do gateway. 

1. No menu principal do Azure, vá para **todos os recursos**. 

2. Localize e selecione o recurso de gateway.

3. Se ainda não estiver selecionado, no menu de recursos do gateway, selecione **Gateway de dados local**. 

4. Na barra de ferramentas do recurso, escolha **excluir**.

<a name="faq"></a>

## <a name="frequently-asked-questions"></a>Perguntas mais frequentes

[!INCLUDE [existing-gateway-location-changed](../../includes/logic-apps-existing-gateway-location-changed.md)]

## <a name="get-support"></a>Obter suporte

* Relativamente a dúvidas, visite o [fórum do Azure Logic Apps](https://social.msdn.microsoft.com/Forums/en-US/home?forum=azurelogicapps).
* Para submeter ou votar em ideias para funcionalidades, visite o [site de comentários dos utilizadores do Logic Apps](https://aka.ms/logicapps-wish).

## <a name="next-steps"></a>Passos seguintes

* [Proteger as suas aplicações lógicas](./logic-apps-securing-a-logic-app.md)
* [Exemplos e cenários comuns para aplicativos lógicos](./logic-apps-examples-and-scenarios.md)
