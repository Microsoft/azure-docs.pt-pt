---
title: 'Tutorial: provisionamento de usuário para a margem de atraso-Azure AD'
description: Saiba como configurar Azure Active Directory para provisionar e desprovisionar automaticamente as contas de usuário para a margem de atraso.
services: active-directory
documentationcenter: ''
author: ArvindHarinder1
manager: CelesteDG
ms.assetid: d4ca2365-6729-48f7-bb7f-c0f5ffe740a3
ms.service: active-directory
ms.subservice: saas-app-tutorial
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/27/2019
ms.author: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4cd8e483d6c189e311fdb1925ad0f2effc2affe1
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 12/05/2019
ms.locfileid: "74849156"
---
# <a name="tutorial-configure-slack-for-automatic-user-provisioning"></a>Tutorial: configurar a margem de atraso para o provisionamento automático de usuário

O objetivo deste tutorial é mostrar as etapas que precisam ser executadas na margem de atraso e no Azure AD para provisionar e desprovisionar automaticamente as contas de usuário do Azure AD até a margem de atraso.

## <a name="prerequisites"></a>Pré-requisitos

O cenário descrito neste tutorial pressupõe que você já tem os seguintes itens:

* Um inquilino do Azure Active Directory
* Um locatário de margem de atraso com o [plano de adição](https://aadsyncfabric.slack.com/pricing) ou melhor habilitado
* Uma conta de usuário na margem de atraso com permissões de administrador de equipe

Observação: a integração de provisionamento do Azure AD depende da [API scim de margem de atraso](https://api.slack.com/scim), que está disponível para as equipes de margem de atraso no plano mais ou melhor.

## <a name="assigning-users-to-slack"></a>Atribuindo usuários à margem de atraso

Azure Active Directory usa um conceito chamado "atribuições" para determinar quais usuários devem receber acesso aos aplicativos selecionados. No contexto do provisionamento automático de conta de usuário, somente os usuários e grupos que foram "atribuídos" a um aplicativo no Azure AD serão sincronizados.

Antes de configurar e habilitar o serviço de provisionamento, você precisará decidir quais usuários e/ou grupos no Azure AD representam os usuários que precisam de acesso ao seu aplicativo de margem de atraso. Depois de decidir, você pode atribuir esses usuários ao seu aplicativo de margem de atraso seguindo estas instruções:

[Atribuir um usuário ou grupo a um aplicativo empresarial](../manage-apps/assign-user-or-group-access-portal.md)

### <a name="important-tips-for-assigning-users-to-slack"></a>Dicas importantes para atribuir usuários à margem de atraso

* É recomendável que um único usuário do Azure AD seja atribuído à margem de atraso para testar a configuração de provisionamento. Usuários e/ou grupos adicionais podem ser atribuídos posteriormente.

* Ao atribuir um usuário à margem de atraso, você deve selecionar a função **usuário** ou "grupo" na caixa de diálogo de atribuição. A função de "acesso padrão" não funciona para provisionamento.

## <a name="configuring-user-provisioning-to-slack"></a>Configurando o provisionamento de usuário para a margem de atraso 

Esta seção orienta você pela conexão do Azure AD com a API de provisionamento de conta de usuário da margem de atraso e pela configuração do serviço de provisionamento para criar, atualizar e desabilitar contas de usuário atribuídas na margem de atraso com base na atribuição de usuário e de grupo no Azure AD.

**Dica:** Você também pode optar por habilitar o logon único baseado em SAML para a margem de atraso, seguindo as instruções fornecidas em [portal do Azure](https://portal.azure.com). O logon único pode ser configurado independentemente do provisionamento automático, embora esses dois recursos se complementem.

### <a name="to-configure-automatic-user-account-provisioning-to-slack-in-azure-ad"></a>Para configurar o provisionamento automático de conta de usuário para a margem de atraso no Azure AD:

1. Na [portal do Azure](https://portal.azure.com), navegue até a seção **Azure Active Directory > aplicativos empresariais > todos os aplicativos** .

2. Se você já tiver configurado a margem de atraso para logon único, pesquise por sua instância de margem de atraso usando o campo de pesquisa. Caso contrário, selecione **Adicionar** e pesquise a **margem de atraso** na Galeria de aplicativos. Selecione a margem de atraso nos resultados da pesquisa e adicione-a à lista de aplicativos.

3. Selecione sua instância de margem de atraso e, em seguida, selecione a guia **provisionamento** .

4. Defina o **modo de provisionamento** como **automático**.

   ![Provisionamento de margem de atraso](./media/slack-provisioning-tutorial/slack1.png)

5. Na seção **credenciais de administrador** , clique em **autorizar**. Isso abre uma caixa de diálogo de autorização de margem de atraso em uma nova janela do navegador.

6. Na nova janela, entre na margem de atraso usando sua conta de administrador da equipe. na caixa de diálogo autorização resultante, selecione a equipe de margem de atraso para a qual você deseja habilitar o provisionamento e, em seguida, selecione **autorizar**. Depois de concluído, retorne ao portal do Azure para concluir a configuração de provisionamento.

    ![Caixa de diálogo de autorização](./media/slack-provisioning-tutorial/slackauthorize.png)

7. No portal do Azure, clique em **testar conexão** para garantir que o Azure ad possa se conectar ao seu aplicativo de margem de atraso. Se a conexão falhar, verifique se a sua conta de margem de atraso tem permissões de administrador de equipe e tente a etapa "autorizar" novamente.

8. Insira o endereço de email de uma pessoa ou grupo que deve receber notificações de erro de provisionamento no campo **email de notificação** e marque a caixa de seleção abaixo.

9. Clique em **Guardar**.

10. Na seção mapeamentos, selecione **sincronizar Azure Active Directory usuários para a margem de atraso**.

11. Na seção **mapeamentos de atributo** , examine os atributos de usuário que serão sincronizados do Azure ad para a margem de atraso. Observe que os atributos selecionados como propriedades **correspondentes** serão usados para corresponder as contas de usuário na margem de atraso das operações de atualização. Selecione o botão Guardar para consolidar as alterações.

12. Para habilitar o serviço de provisionamento do Azure AD para a margem de atraso, altere o **status de provisionamento** para **ativado** na seção **configurações**

13. Clique em **Guardar**.

Isso iniciará a sincronização inicial de todos os usuários e/ou grupos atribuídos à margem de atraso na seção usuários e grupos. Observe que a sincronização inicial levará mais tempo do que as sincronizações subsequentes, que ocorrem aproximadamente a cada 10 minutos, desde que o serviço esteja em execução. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os relatórios de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo de margem de atraso.

## <a name="optional-configuring-group-object-provisioning-to-slack"></a>Adicional Configurando o provisionamento de objeto de grupo para a margem de atraso

Opcionalmente, você pode habilitar o provisionamento de objetos de grupo do Azure AD para a margem de atraso. Isso é diferente de "atribuir grupos de usuários", pois o objeto de grupo real, além de seus membros, será replicado do Azure AD para a margem de atraso. Por exemplo, se você tiver um grupo chamado "meu grupo" no Azure AD, um grupo idêntico chamado "meu grupo" será criado dentro da margem de atraso.

### <a name="to-enable-provisioning-of-group-objects"></a>Para habilitar o provisionamento de objetos de Grupo:

1. Na seção mapeamentos, selecione **sincronizar Azure Active Directory grupos à margem de atraso**.

2. Na folha mapeamento de atributo, defina habilitado como Sim.

3. Na seção **mapeamentos de atributo** , examine os atributos de grupo que serão sincronizados do Azure ad para a margem de atraso. Observe que os atributos selecionados como propriedades **correspondentes** serão usados para corresponder os grupos na margem de atraso das operações de atualização. 

4. Clique em **Guardar**.

Isso resulta em qualquer objeto de grupo atribuído à margem de atraso na seção **usuários e grupos** sendo totalmente sincronizada do Azure ad até a margem de atraso. Você pode usar a seção **detalhes de sincronização** para monitorar o progresso e seguir os links para os logs de atividade de provisionamento, que descrevem todas as ações executadas pelo serviço de provisionamento em seu aplicativo de margem de atraso.

Para obter mais informações sobre como ler o registos de aprovisionamento do AD do Azure, consulte [relatórios sobre o aprovisionamento de contas de utilizadores automático](../manage-apps/check-status-user-account-provisioning.md).

## <a name="connector-limitations"></a>Limitações do conector

* Ao configurar o atributo **DisplayName** de margem de atraso, esteja ciente dos seguintes comportamentos:

  * Os valores não são totalmente exclusivos (por exemplo, 2 usuários podem ter o mesmo nome de exibição)

  * Dá suporte a caracteres que não estão em inglês, espaços, maiúsculas e minúsculas. 
  
  * A pontuação permitida inclui pontos, sublinhados, hifens, apóstrofos, colchetes (por exemplo, **([{}])** ) e separadores (por exemplo **,/;** ).
  
  * Somente as atualizações se essas duas configurações forem definidas no local de trabalho/a **sincronização de perfil da organização será habilitada** e **os usuários não poderão alterar seu nome de exibição**.
  
* O atributo de **nome de usuário** da margem de atraso deve ter menos de 21 caracteres e ter um valor exclusivo.

* A margem de atraso só permite correspondência com os atributos **username** e **email**.  

## <a name="additional-resources"></a>Recursos Adicionais

* [Gerenciando o provisionamento de conta de usuário para aplicativos empresariais](../manage-apps/configure-automatic-user-provisioning-portal.md)
* [What is application access and single sign-on with Azure Active Directory?](../manage-apps/what-is-single-sign-on.md) (O que é o acesso a aplicações e o início de sessão único com o Azure Active Directory?)
