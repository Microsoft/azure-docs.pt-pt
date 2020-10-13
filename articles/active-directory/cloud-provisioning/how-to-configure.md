---
title: Azure AD Connect nuvem que provisiona nova configuração de agente
description: Este artigo descreve como instalar o fornecimento de nuvens.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/26/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: c8b18629a776dd98950f49b1f607cbc876abcd9c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "91628906"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-based-provisioning"></a>Criar uma nova configuração para o Azure AD Connect Provisioning baseado em nuvem

Depois de ter instalado o agente, tem de iniciar seducação no portal Azure e configurar o Azure Ative Directory (Azure AD) Ligar o provisionamento em nuvem. Siga estes passos para ativar o agente.

## <a name="configure-provisioning"></a>Disposição de configuração
Para configurar o provisionamento, siga estes passos.

 1. No portal Azure, selecione **Azure Ative Directory**
 2. Selecione **Azure Ad Connect**.
 3. **Selecione Gerir o provisionamento**.

 ![Gerir o provisionamento](media/how-to-configure/manage1.png)
 
 4. Selecione **nova configuração**.
 5. No ecrã de configuração, selecione o seu domínio e se deve ativar a sincronização de haxixe de palavra-passe.  Clique **em Criar**.  
 
 ![Criar nova configuração](media/how-to-configure/configure1.png)


 6.  O ecrã de configuração de provisionamento editar será aberto.

   ![Configuração de edição](media/how-to-configure/configure2.png)

 7. Introduza um **e-mail de Notificação**. Este e-mail será notificado quando o provisionamento não for saudável.
 8. Mova o seletor para Ativar e selecione Guardar.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Âmbito de fornecimento a utilizadores e grupos específicos
Pode procurar o agente para sincronizar utilizadores e grupos específicos utilizando grupos de Diretório Ativo no local ou unidades organizacionais. Não é possível configurar grupos e unidades organizacionais dentro de uma configuração. 

 1.  No portal Azure, selecione **Azure Ative Directory**.
 2. Selecione **Azure Ad Connect**.
 3. **Selecione Gerir o provisionamento (Pré-visualização)**.
 4. Em **Configuração,** selecione a sua configuração.

 ![Secção de configuração](media/how-to-configure/scope1.png)
 
 5. Em **Configuração,** **selecione Editar filtros** de deteção para alterar o âmbito da regra de configuração.
 ![Editar âmbito](media/how-to-configure/scope3.png)
 7. À direita, pode mudar o âmbito.  Clique **em "Fazer"**  e **guarde** quando terminar.
 8. Uma vez alterado o âmbito, deve [reiniciar o provisionamento](#restart-provisioning) para iniciar uma sincronização imediata das alterações.

## <a name="attribute-mapping"></a>Mapeamento do atributo
O fornecimento de nuvem AD Connect Azure permite-lhe mapear facilmente atributos entre os objetos de utilizador/grupo no local e os objetos em Azure AD.  Pode personalizar os mapeamentos de atributos padrão de acordo com as necessidades do seu negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes ou criar novos mapeamentos de atributos.  Para obter mais informações consulte [o mapeamento do atributo](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Aprovisionamento a pedido
O fornecimento de nuvem AD Connect Azure permite testar alterações de configuração, aplicando estas alterações a um único utilizador ou grupo.  Pode utilizar isto para validar e verificar se as alterações efetuadas à configuração foram corretamente aplicadas e estão a ser corretamente sincronizadas para a Azure AD.  Para obter mais informações, consulte [o provisionamento a pedido.](how-to-on-demand-provision.md)

## <a name="restart-provisioning"></a>Reiniciar o provisionamento 
Se não quiser esperar pela próxima execução programada, desencadeie o provisionamento executado utilizando o botão **de provisionamento Restart.** 
 1.  No portal Azure, selecione **Azure Ative Directory**.
 2. Selecione **Azure Ad Connect**.
 3.  **Selecione Gerir o provisionamento (Pré-visualização)**.
 4. Em **Configuração,** selecione a sua configuração.

   ![Seleção de configuração para reiniciar o provisionamento](media/how-to-configure/scope1.png)

 5. No topo, **selecione Reatamento**.

## <a name="remove-a-configuration"></a>Remover uma configuração
Para eliminar uma configuração, siga estes passos.

 1.  No portal Azure, selecione **Azure Ative Directory**.
 2. Selecione **Azure Ad Connect**.
 3. **Selecione Gerir o provisionamento (Pré-visualização)**.
 4. Em **Configuração,** selecione a sua configuração.
   
   ![Seleção de configuração para remover configuração](media/how-to-configure/scope1.png)

 5. Na parte superior do ecrã de configuração, **selecione Delete**.

>[!IMPORTANT]
>Não há confirmação antes de apagar uma configuração. Certifique-se de que esta é a ação que pretende tomar antes de selecionar **Delete**.


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é o aprovisionamento na cloud do Azure AD Connect?](what-is-cloud-provisioning.md)
