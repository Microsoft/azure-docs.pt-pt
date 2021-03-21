---
title: Provisão a pedido em sincronização de nuvem Azure AD Connect
description: Este artigo descreve como utilizar a funcionalidade de sincronização em nuvem do Azure AD Connect para testar alterações na configuração.
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 09/14/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 5048b78c7d59b3358dbffe2e3e6eedf41decabb8
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102554280"
---
# <a name="on-demand-provisioning-in-azure-ad-connect-cloud-sync"></a>Provisão a pedido em sincronização de nuvem Azure AD Connect

Pode utilizar a funcionalidade de sincronização em nuvem do Azure Ative Directory (Azure AD) Connect para testar alterações de configuração aplicando estas alterações a um único utilizador. Esta provisão a pedido ajuda-o a validar e verificar se as alterações efetuadas à configuração foram corretamente aplicadas e estão a ser corretamente sincronizadas com a Azure AD.  

> [!IMPORTANT] 
> Quando utiliza o provisionamento a pedido, os filtros de escoamento não são aplicados ao utilizador que selecionou. Pode utilizar o provisionamento a pedido em utilizadores que estejam fora das unidades de organização que especificou.

## <a name="validate-a-user"></a>Validar um utilizador
Para utilizar o provisionamento a pedido, siga estes passos:

1.  No portal do Azure, selecione **Azure Active Directory**.
2.  Selecione **Azure Ad Connect**.
3.  **Selecione Gerir a sincronização de nuvens**.

    ![Screenshot que mostra a ligação para gerir a sincronização da nuvem.](media/how-to-install/install-6.png)
4. Em **Configuração,** selecione a sua configuração.
5. Em **Validação**, selecione o botão **Devisão de utilizador.** 

   ![Screenshot que mostra o botão para o provisionamento de um utilizador.](media/how-to-on-demand-provision/on-demand-2.png)

6. No ecrã **De provisão a pedido,** introduza o nome distinto de um utilizador e selecione o botão **Provision.**  
 
   ![Screenshot que mostra um nome de utilizador e um botão Provision.](media/how-to-on-demand-provision/on-demand-3.png)
7. Após o provisionamento final, um ecrã de sucesso aparece com quatro marcas de verificação verdes. Quaisquer erros aparecem para a esquerda.

   ![Screenshot que mostra provisão bem sucedida.](media/how-to-on-demand-provision/on-demand-4.png)

## <a name="get-details-about-provisioning"></a>Obter detalhes sobre o provisionamento
Agora pode olhar para as informações do utilizador e determinar se as alterações que escoou na configuração foram aplicadas. O resto deste artigo descreve as secções individuais que aparecem nos detalhes de um utilizador sincronizado com sucesso.

### <a name="import-user"></a>Utilizador de importação
A secção **de utilizador importância** fornece informações sobre o utilizador que foi importado do Ative Directory. Isto é o que o utilizador parece antes de se abastecer no Azure AD. Selecione o link **de detalhes ver** para exibir esta informação.

![Screenshot do botão para visualização de detalhes sobre um utilizador importado.](media/how-to-on-demand-provision/on-demand-5.png)

Ao utilizar esta informação, pode ver os vários atributos (e seus valores) que foram importados. Se criou um mapeamento de atributos personalizados, pode ver o valor aqui.

![Screenshot que mostra detalhes do utilizador.](media/how-to-on-demand-provision/on-demand-6.png)

### <a name="determine-if-user-is-in-scope"></a>Determinar se o utilizador está no âmbito
A **Secção Determinou se o utilizador está na** secção de âmbito fornece informações sobre se o utilizador que foi importado para a Azure AD está no âmbito. Selecione o link **de detalhes ver** para exibir esta informação.

![Screenshot do botão para visualização de detalhes sobre o âmbito do utilizador.](media/how-to-on-demand-provision/on-demand-7.png)

Ao utilizar estas informações, pode ver se o utilizador está no âmbito.

![Screenshot que mostra detalhes do alcance do utilizador.](media/how-to-on-demand-provision/on-demand-10a.png)

### <a name="match-user-between-source-and-target-system"></a>Match user entre o sistema de origem e alvo
A secção match user entre a secção **de sistema de origem e alvo** fornece informações sobre se o utilizador já existe em Azure AD e se deve ocorrer uma junção em vez de fornecer um novo utilizador. Selecione o link **de detalhes ver** para exibir esta informação.

![Screenshot do botão para visualizar detalhes sobre um utilizador compatível.](media/how-to-on-demand-provision/on-demand-8.png)

Ao utilizar estas informações, pode ver se foi encontrado um fósforo ou se um novo utilizador será criado.

![Screenshot que mostra informações do utilizador.](media/how-to-on-demand-provision/on-demand-11.png)

Os detalhes correspondentes mostram uma mensagem com uma das três operações seguintes:
- **Criar**: Um utilizador é criado em Azure AD.
- **Atualização**: Um utilizador é atualizado com base numa alteração efetuada na configuração.
- **Excluir**: Um utilizador é removido do Azure AD.

Dependendo do tipo de operação que executou, a mensagem variará.

### <a name="perform-action"></a>Realizar ação
A secção **de ação Perform** fornece informações sobre o utilizador que foi a provisionado ou exportado para Azure AD após a configuração ter sido aplicada. Isto é o que o utilizador parece depois de se abastecer no Azure AD. Selecione o link **de detalhes ver** para exibir esta informação.

![Screenshot do botão para visualizar detalhes sobre uma ação executada.](media/how-to-on-demand-provision/on-demand-9.png)

Ao utilizar esta informação, pode ver os valores dos atributos após a aplicação da configuração. Parecem-se com o que foi importado, ou são diferentes? A configuração foi aplicada com sucesso?  

Este processo permite-lhe rastrear a transformação do atributo à medida que se move através da nuvem e para o seu inquilino AD AZure.

![Screenshot que mostra detalhes de atributos rastreados.](media/how-to-on-demand-provision/on-demand-12.png)

## <a name="next-steps"></a>Passos seguintes 

- [O que é a sincronização de nuvem AZure AD Connect?](what-is-cloud-sync.md)
- [Instalar sincronização de nuvem Azure AD Connect](how-to-install.md)
 