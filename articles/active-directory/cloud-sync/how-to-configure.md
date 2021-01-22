---
title: Azure AD Connect cloud sync nova configuração de agente
description: Este artigo descreve como instalar a sincronização de nuvens.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/21/2021
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ffdd27b67a122f82cc5fdb5568f11c85387955e8
ms.sourcegitcommit: 52e3d220565c4059176742fcacc17e857c9cdd02
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/21/2021
ms.locfileid: "98660801"
---
# <a name="create-a-new-configuration-for-azure-ad-connect-cloud-sync"></a>Crie uma nova configuração para sincronização de nuvem AZure AD Connect

Depois de ter instalado o agente de provisionamento AZure AD Connect, tem de iniciar seduca no portal Azure e configurá-lo. Siga estes passos para ativar o agente.

## <a name="configure-provisioning"></a>Disposição de configuração
Para configurar o provisionamento, siga estes passos.

 1. No portal Azure, selecione **Azure Ative Directory**
 2. Selecione **Azure Ad Connect**.
 3. **Selecione Gerir a sincronização de nuvens**.

 ![Gerir o provisionamento](media/how-to-install/install-6.png)
 
 4. Selecione **nova configuração**.
 5. No ecrã de configuração, selecione o seu domínio e se deve ativar a sincronização de haxixe de palavra-passe.  Clique **em Criar**.  
 
 ![Criar nova configuração](media/how-to-configure/configure-1.png)


 6.  O ecrã de configuração de provisionamento editar será aberto.

   ![Configuração de edição](media/how-to-configure/con-1.png)

 7. Introduza um **e-mail de Notificação**. Este e-mail será notificado quando o provisionamento não for saudável.  Recomenda-se que mantenha **Prevent acidental mente** ativada e definir o **limiar de eliminação acidental** para um número sobre o qual deseja ser notificado.  Para obter mais informações, consulte [as eliminações acidentais](#accidental-deletions) abaixo.
 8. Mova o seletor para Ativar e selecione Guardar.

## <a name="scope-provisioning-to-specific-users-and-groups"></a>Âmbito de fornecimento a utilizadores e grupos específicos
Pode procurar o agente para sincronizar utilizadores e grupos específicos utilizando grupos de Diretório Ativo no local ou unidades organizacionais. Não é possível configurar grupos e unidades organizacionais dentro de uma configuração. 

 1.  No portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure Ad Connect**.
 3. **Selecione Gerir a sincronização de nuvens**.
 4. Em **Configuração,** selecione a sua configuração.

 ![Secção de configuração](media/how-to-configure/scope-1.png)
 
 5. Em **Configuração,** **selecione Editar filtros** de deteção para alterar o âmbito da regra de configuração.
 ![Editar âmbito](media/how-to-configure/scope-3.png)
 7. À direita, pode mudar o âmbito.  Clique **em "Fazer"**  e **guarde** quando terminar.
 8. Uma vez alterado o âmbito, deve [reiniciar o provisionamento](#restart-provisioning) para iniciar uma sincronização imediata das alterações.

## <a name="attribute-mapping"></a>Mapeamento do atributo
O azure AD Connect cloud sync permite-lhe mapear facilmente atributos entre os seus objetos de utilizador/grupo no local e os objetos em Azure AD.  Pode personalizar os mapeamentos de atributos padrão de acordo com as necessidades do seu negócio. Assim, pode alterar ou eliminar os mapeamentos de atributos existentes ou criar novos mapeamentos de atributos.  Para obter mais informações consulte [o mapeamento do atributo](how-to-attribute-mapping.md).

## <a name="on-demand-provisioning"></a>Aprovisionamento a pedido
O azure AD Connect cloud sync permite testar alterações de configuração, aplicando estas alterações a um único utilizador ou grupo.  Pode utilizar isto para validar e verificar se as alterações efetuadas à configuração foram corretamente aplicadas e estão a ser corretamente sincronizadas para a Azure AD.  Para obter mais informações, consulte [o provisionamento a pedido.](how-to-on-demand-provision.md)

## <a name="accidental-deletions"></a>Supressões acidentais
A função de eliminação acidental foi concebida para o proteger de alterações de configuração acidentais e alterações no diretório no local que afetariam muitos utilizadores e grupos.  Esta funcionalidade permite-lhe:

- Configure a capacidade de evitar eliminações acidentais automaticamente. 
- Desa ajuste o # de objetos (limiar) para além do qual a configuração irá produzir efeito 
- configurar um endereço de e-mail de notificação para que possam receber uma notificação por e-mail uma vez que o trabalho de sincronização em questão é colocado em quarentena para este cenário 

Para mais informações ver [eliminações acidentais](how-to-accidental-deletes.md)

## <a name="quarantines"></a>Quarentenas
A sincronização da nuvem monitoriza a saúde da sua configuração e coloca objetos insalubres num estado de quarentena. Se a maioria ou todas as chamadas feitas contra o sistema alvo falharem consistentemente devido a um erro, por exemplo, credenciais de administração inválidas, o trabalho de sincronização está marcado como em quarentena.  Para obter mais informações, consulte a secção de resolução de problemas em [quarentenas.](how-to-troubleshoot.md#provisioning-quarantined-problems)

## <a name="restart-provisioning"></a>Reiniciar o provisionamento 
Se não quiser esperar pela próxima execução programada, desencadeie o provisionamento executado utilizando o botão **de provisionamento Restart.** 
 1.  No portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure Ad Connect**.
 3.  **Selecione Gerir a sincronização de nuvens**.
 4. Em **Configuração,** selecione a sua configuração.

   ![Seleção de configuração para reiniciar o provisionamento](media/how-to-configure/scope-1.png)

 5. No topo, **selecione Reatamento**.

## <a name="remove-a-configuration"></a>Remover uma configuração
Para eliminar uma configuração, siga estes passos.

 1.  No portal do Azure, selecione **Azure Active Directory**.
 2. Selecione **Azure Ad Connect**.
 3. **Selecione Gerir a sincronização de nuvens**.
 4. Em **Configuração,** selecione a sua configuração.
   
   ![Seleção de configuração para remover configuração](media/how-to-configure/scope-1.png)

 5. Na parte superior do ecrã de configuração, **selecione Delete**.

>[!IMPORTANT]
>Não há confirmação antes de apagar uma configuração. Certifique-se de que esta é a ação que pretende tomar antes de selecionar **Delete**.


## <a name="next-steps"></a>Passos seguintes 

- [O que é o aprovisionamento?](what-is-provisioning.md)
- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
