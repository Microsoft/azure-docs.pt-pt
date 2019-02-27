---
title: 'Azure Active Directory Domain Services: Introdução | Documentos da Microsoft'
description: Ativar o Azure Active Directory Domain Services no portal do Azure
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: ace1ed4a-bf7f-43c1-a64a-6b51a2202473
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ergreenl
ms.openlocfilehash: 734fb5ce641d48800cef68ea79cdb258e44ac267
ms.sourcegitcommit: 50ea09d19e4ae95049e27209bd74c1393ed8327e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56867104"
---
# <a name="enable-azure-active-directory-domain-services-using-the-azure-portal"></a>Ativar o Azure Active Directory Domain Services no portal do Azure

## <a name="task-3-configure-administrative-group"></a>Tarefa 3: configurar o grupo administrativo

Esta tarefa de configuração, vai criar um grupo administrativo no diretório do Azure AD. Denomina-se este grupo administrativo especial *administradores do AAD DC*. Os membros deste grupo são concedidos permissões administrativas nos computadores que estão associados a um domínio para o domínio gerido. Em computadores associados a um domínio, este grupo é adicionado ao grupo de administradores. Além disso, os membros deste grupo podem utilizar o ambiente de trabalho remoto para ligar remotamente a computadores associados a um domínio.

> [!NOTE]
> Não tem permissões de administrador Corporativo ou de administrador de domínio no domínio gerido que criou utilizando o Azure Active Directory Domain Services. Nos domínios geridos, estas permissões são reservadas pelo serviço e não estar disponíveis para os utilizadores no inquilino. No entanto, pode utilizar o grupo administrativo especial criado nesta tarefa de configuração para realizar algumas operações privilegiadas. Estas operações incluem associar computadores ao domínio, a que pertencem ao grupo de administração de computadores associados a um domínio e a configuração de diretiva de grupo.
>

O assistente cria automaticamente o grupo administrativo no diretório do Azure AD. Este grupo é chamado de "Administradores do AAD DC". Se tiver um grupo existente com este nome no diretório do Azure AD, o assistente seleciona este grupo. Pode configurar a associação de grupo utilizando o **grupo Administrador** página do assistente.

1. Para configurar a associação de grupo, clique em **administradores do AAD DC**.

    ![Configurar associação a um grupo](./media/getting-started/domain-services-blade-admingroup.png)

2. Clique nas **adicionar membros** botão para adicionar utilizadores de diretório do Azure AD para o grupo de administrador.

3. Quando tiver terminado, clique em **OK** mover para o **resumo** página do assistente.

## <a name="configure-synchronization"></a>Configurar a sincronização

O Azure AD Domain Services permite a sincronização completa de todos os utilizadores e grupos disponíveis no Azure AD, ou pode selecionar o âmbito de sincronização para sincronizar apenas os grupos específicos. Se escolher a sincronização completa, irá **não** pode escolher o âmbito de sincronização num momento posterior. Para saber mais sobre a sincronização de âmbito, visite o [artigo de sincronização de âmbito do Azure AD Domain Services](active-directory-ds-scoped-synchronization.md).

### <a name="full-synchronization"></a>Sincronização completa

1. Para a sincronização completa, basta clicar em "OK" na parte inferior do ecrã, como completa é já escolhido.
    ![Sincronização completa](./media/active-directory-domain-services-admin-guide/create-sync-all.PNG)

### <a name="scoped-synchronization"></a>Âmbito de sincronização

1. Ativar/desativar o botão de sincronização para "Com âmbito" e uma página de grupos selecionados será apresentado. A partir disso, pode ver que grupos já estão selecionados a ser sincronizadas para o seu domínio gerido.
    ![Âmbito de sincronização](media/active-directory-domain-services-admin-guide/create-sync-scoped.PNG)
2. Clique em **selecionar grupos** na barra de navegação superior. A partir daqui, um Seletor de grupo será apresentada no lado. Utilize esta opção para selecionar todos os grupos adicionais para sincronizar com os serviços de domínio do Azure AD. Quando terminar, clique em **selecione** para fechar o Seletor de grupo e adicionar esses grupos à lista selecionada.
    ![Selecione grupos de âmbito de sincronização](media/active-directory-domain-services-admin-guide/create-sync-scoped-groupselect.PNG)
3. Clique em **OK** para mover para a página de resumo.

## <a name="deploy-your-managed-domain"></a>Implementar o seu domínio gerido

1. Sobre o **resumo** página do assistente, reveja as definições de configuração para o domínio gerido. Pode voltar atrás para qualquer passo do Assistente para fazer alterações, se necessário. Quando tiver terminado, clique em **OK** para criar o novo domínio gerido.

    ![Resumo](./media/getting-started/domain-services-blade-summary.png)

2. Verá uma notificação que mostra o progresso da implementação do Azure AD Domain Services. Clique na notificação para ver o progresso detalhado para a implementação.

    ![Notificação - implementação em curso](./media/getting-started/domain-services-blade-deployment-in-progress.png)

## <a name="check-the-deployment-status-of-your-managed-domain"></a>Verificar o estado de implementação do seu domínio gerido

O processo de aprovisionamento do seu domínio gerido pode demorar até uma hora.

1. Enquanto a implementação está em curso, pode pesquisar por 'Serviços de domínio' no **pesquisar recursos** caixa de pesquisa. Selecione **Azure AD Domain Services** do resultado da pesquisa. O **Azure AD Domain Services** painel lista o domínio gerido, que está a ser aprovisionado.

    ![Encontrar o domínio gerido, que está a ser aprovisionado](./media/getting-started/domain-services-provisioning-state-find-resource.png)

2. Clique no nome do domínio gerido (por exemplo, ' contoso100.com') para ver mais detalhes sobre o domínio gerido.

    ![Serviços de domínio - estado de aprovisionamento](./media/getting-started/domain-services-provisioning-state.png)

3. O **descrição geral** separador mostra que o domínio gerido está atualmente a ser aprovisionado. Não é possível configurar o domínio gerido até que esteja totalmente aprovisionado. Poderá demorar até uma hora para o seu domínio gerido ser totalmente aprovisionado.

    ![Serviços de domínio - separador de descrição geral durante o estado de aprovisionamento](./media/getting-started/domain-services-provisioning-state-details.png)

4. Quando o domínio gerido está totalmente aprovisionado, o **descrição geral** separador mostra o estado de domínio como **em execução**.

    ![Serviços de domínio - separador Descrição geral depois de totalmente aprovisionado](./media/getting-started/domain-services-provisioned.png)
    >[!NOTE]
    >Durante o processo de aprovisionamento, o Azure AD Domain Services cria aplicações empresariais com o nome "Serviços de controlador de domínio" e "AzureActiveDirectoryDomainControllerServices" no seu diretório. Estas aplicações empresariais são necessárias para atender a seu domínio gerido. É imperativo que estes não são eliminados em qualquer altura.
    >

5. Sobre o **propriedades** separador, verá dois endereços IP no qual o domínio controladores estão disponíveis para a rede virtual.

    ![Serviços de domínio - separador de propriedades, depois de totalmente aprovisionado](./media/getting-started/domain-services-provisioned-properties.png)

## <a name="need-help"></a>Precisa de ajuda?

Pode demorar uma hora ou dois para ambos os controladores de domínio para o seu domínio gerido ser aprovisionado. Se a implementação falhou ou está bloqueada no estado "Pendente" há mais de duas horas, fique à vontade para [contacte a equipa de produto para obter ajuda](active-directory-ds-contact-us.md).

## <a name="next-step"></a>Passo seguinte

[Tarefa 4: atualizar as definições de DNS para a rede virtual do Azure](active-directory-ds-getting-started-dns.md)
