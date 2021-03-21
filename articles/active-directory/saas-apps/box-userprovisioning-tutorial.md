---
title: 'Tutorial: Caixa de Configuração para fornecimento automático de utilizadores com diretório ativo Azure | Microsoft Docs'
description: Saiba como configurar um único sinal entre o Azure Ative Directory e o Box .
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.topic: tutorial
ms.date: 07/20/2020
ms.author: jeedes
ms.openlocfilehash: df4031a590eb4547d4327cebe96ccbe63d21785a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/20/2021
ms.locfileid: "102437813"
---
# <a name="tutorial-configure-box-for-automatic-user-provisioning"></a>Tutorial: Caixa de configuração para o provisionamento automático do utilizador

O objetivo deste tutorial é mostrar os passos que você precisa para executar em Box e Azure AD para automaticamente fornecendo e desavisenciar contas de utilizadores de Azure AD para Box.

> [!NOTE]
> Este tutorial descreve um conector construído em cima do Serviço de Provisionamento de Utilizadores Azure AD. Para obter detalhes importantes sobre o que este serviço faz, como funciona e perguntas frequentes, veja [Automatizar o aprovisionamento e desaprovisionamento de utilizadores em aplicações SaaS no Azure Active Directory](../app-provisioning/user-provisioning.md).

## <a name="prerequisites"></a>Pré-requisitos

Para configurar a integração AD da Azure com a Box, precisa dos seguintes itens:

- Um inquilino da AD AZure
- Um plano de negócios de caixa ou melhor

> [!NOTE]
> Ao testar os passos neste tutorial, recomendamos que *não* utilize um ambiente de produção.

> [!NOTE]
> As aplicações precisam de ser ativadas na aplicação Box primeiro.

> [!NOTE]
> Esta integração também está disponível para usar a partir do ambiente cloud do governo dos EUA Azure AD. Você pode encontrar esta aplicação na Azure AD US Government Cloud Application Gallery e configurá-la da mesma forma que você faz a partir de nuvem pública.

Para testar os passos neste tutorial, siga estas recomendações:

- Não utilize o seu ambiente de produção, a não ser que seja necessário.
- Se não tiver um ambiente experimental da AD Azure, pode [ter um julgamento de um mês.](https://azure.microsoft.com/pricing/free-trial/)

## <a name="assigning-users-to-box"></a>Atribuir utilizadores à Box 

O Azure Ative Directory utiliza um conceito chamado "atribuições" para determinar quais os utilizadores que devem ter acesso a aplicações selecionadas. No contexto do provisionamento automático da conta de utilizador, apenas os utilizadores e grupos que foram "atribuídos" a uma aplicação em AZure AD são sincronizados.

Antes de configurar e ativar o serviço de fornecimento, tem de decidir quais os utilizadores e/ou grupos em AZure AD que representam os utilizadores que precisam de acesso à sua aplicação Box. Uma vez decididos, pode atribuir estes utilizadores à sua aplicação Box seguindo as instruções aqui:

[Atribuir um utilizador ou grupo a uma aplicação empresarial](../manage-apps/assign-user-or-group-access-portal.md)

## <a name="assign-users-and-groups"></a>Atribuir utilizadores e grupos
O **separador Box > Utilizadores e Grupos** no portal Azure permite-lhe especificar quais os utilizadores e grupos que devem ter acesso à Box. A atribuição de um utilizador ou grupo faz com que ocorram as seguintes coisas:

* O Azure AD permite que o utilizador designado (seja por atribuição direta ou membro do grupo) autente para a Caixa. Se um utilizador não for designado, então o Azure AD não permite que entrem na Box e retorne um erro na página de entrada AZure AD.
* Um azulejo de aplicação para Box é adicionado ao lançador de [aplicações](../manage-apps/end-user-experiences.md)do utilizador.
* Se o provisionamento automático estiver ativado, os utilizadores e/ou grupos designados são adicionados à fila de provisionamento a ser automaticamente a provisionada.
  
  * Se apenas os objetos do utilizador foram configurados para serem provisionados, então todos os utilizadores diretamente designados são colocados na fila de provisionamento, e todos os utilizadores que são membros de quaisquer grupos designados são colocados na fila de provisionamento. 
  * Se os objetos de grupo foram configurados para serem provisionados, então todos os objetos de grupo atribuídos são a provisionados para a Box, e todos os utilizadores que são membros desses grupos. O grupo e os membros do utilizador são preservados ao serem escritos para a Box.

Pode utilizar o **separador Atributos > Único Sign-On** para configurar quais os atributos do utilizador (ou reclamações) apresentados à Caixa durante a autenticação baseada em SAML, e o **separador Atributos > Provisioning** para configurar como os atributos do utilizador e do grupo fluem de Azure AD para Box durante as operações de provisionamento.

### <a name="important-tips-for-assigning-users-to-box"></a>Dicas importantes para atribuir utilizadores à Box 

*   Recomenda-se que um único utilizador AD Azure atribuído à Box para testar a configuração de provisionamento. Utilizadores e/ou grupos adicionais podem ser atribuídos mais tarde.

*   Ao atribuir um utilizador à caixa, deve selecionar uma função de utilizador válida. A função "Acesso Predefinido" não funciona para o provisionamento.

## <a name="enable-automated-user-provisioning"></a>Ativar o fornecimento automatizado de utilizadores

Esta secção guia através da ligação do seu AD Azure à conta de utilizador da Box que fornece API, e configurar o serviço de fornecimento para criar, atualizar e desativar as contas de utilizador atribuídas em Box com base na atribuição de utilizadores e grupos em Azure AD.

Se o provisionamento automático estiver ativado, os utilizadores e/ou grupos designados são adicionados à fila de provisionamento a ser automaticamente a provisionada.
    
 * Se apenas os objetos do utilizador forem configurados para serem a provisionados, os utilizadores diretamente designados são colocados na fila de provisionamento, e todos os utilizadores que sejam membros de quaisquer grupos designados são colocados na fila de provisionamento. 
    
 * Se os objetos de grupo foram configurados para serem provisionados, então todos os objetos de grupo atribuídos são a provisionados para a Box, e todos os utilizadores que são membros desses grupos. O grupo e os membros do utilizador são preservados ao serem escritos para a Box.

> [!TIP] 
> Pode também optar por ativar Sign-On única baseada em SAML para caixa, seguindo as instruções fornecidas no [portal Azure](https://portal.azure.com). O único sinal pode ser configurado independentemente do fornecimento automático, embora estas duas características se elogiem mutuamente.

### <a name="to-configure-automatic-user-account-provisioning"></a>Para configurar o provisionamento automático da conta de utilizador:

O objetivo desta secção é delinear como permitir o provisionamento das contas de utilizador do Ative Directory à Box.

1. No [portal Azure,](https://portal.azure.com)consulte a secção **Azure Ative Directory > Enterprise Apps > Todas as aplicações.**

2. Se já tiver configurado a Caixa para um único sinal de acesso, procure a sua instância de Box utilizando o campo de pesquisa. Caso contrário, **selecione Add** e procure **Box** na galeria de aplicações. Selecione Caixa a partir dos resultados da pesquisa e adicione-a à sua lista de aplicações.

3. Selecione a sua instância de Caixa e, em seguida, selecione o **separador Provisioning.**

4. Defina o **Modo de Aprovisionamento** como **Automático**. 

    ![Screenshot do separador Provisioning para Caixa no portal Azure. O modo de provisionamento é definido para Automático e O Autorização é destacado em Credenciais de Administração.](./media/box-userprovisioning-tutorial/provisioning.png)

5. Na secção **Credenciais de Administração,** clique **em Autorizar** a abrir um diálogo de login box numa nova janela do navegador.

6. No **Login para conceder acesso à** página Box, forneça as credenciais necessárias e, em seguida, clique em **'Autorizo'.** 
   
    ![Screenshot do Login para conceder acesso ao ecrã da caixa, mostrando entrada para Email e Password, e o botão Authorize.](./media/box-userprovisioning-tutorial/IC769546.png "Permitir o fornecimento automático do utilizador")

7. Clique **em Grant acesso à Box** para autorizar esta operação e para voltar ao portal Azure. 
   
    ![Screenshot do ecrã de acesso autorizado em Box, mostrando uma mensagem explicativa e o acesso do Grant ao botão Box.](./media/box-userprovisioning-tutorial/IC769549.png "Permitir o fornecimento automático do utilizador")

8. No portal Azure, clique em **Test Connection** para garantir que o Azure AD pode ligar-se à sua aplicação Box. Se a ligação falhar, certifique-se de que a sua conta Box tem permissões de Administração da Equipa e tente novamente o passo **"Autorizar".**

9. Insira o endereço de e-mail de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **de e-mail de notificação** e verifique a caixa de verificação.

10. Clique **em Guardar.**

11. Na secção Mappings, selecione **Synchronize Azure Ative Directory Users to Box.**

12. Na secção **De Mapeamentos de Atributos,** reveja os atributos do utilizador que são sincronizados de AD AD a Box. Os atributos selecionados como propriedades **de correspondência** são utilizados para combinar as contas do utilizador na Caixa para operações de atualização. Selecione o botão Guardar para confirmar as alterações.

13. Para ativar o serviço de prestação de AD Azure para a Caixa, altere o **Estado de Provisionamento** para **On** na secção Definições

14. Clique **em Guardar.**

Isto inicia a sincronização inicial de quaisquer utilizadores e/ou grupos atribuídos à Caixa na secção Utilizadores e Grupos. A sincronização inicial demora mais tempo a ser efetuada do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 40 minutos, desde que o serviço esteja em funcionamento. Pode utilizar a secção Detalhes da **Sincronização** para monitorizar o progresso e seguir links para o provisionamento de registos de atividades, que descrevem todas as ações realizadas pelo serviço de fornecimento na sua aplicação Box.

Para obter mais informações sobre como ler os registos de provisionamento da AZure AD, consulte [Reportar sobre o provisionamento automático da conta de utilizador](../app-provisioning/check-status-user-account-provisioning.md).

No seu inquilino Box, os utilizadores sincronizados estão listados em **Utilizadores Geridos** na **Consola de Administração**.

![Estado de integração](./media/box-userprovisioning-tutorial/IC769556.png "Estado de integração")


## <a name="additional-resources"></a>Recursos adicionais

* [Gerir o aprovisionamento de contas de utilizador para Aplicações Empresariais](tutorial-list.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
* [Configurar o único sign-on](box-tutorial.md)