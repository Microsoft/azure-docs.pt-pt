---
title: 'Azure Active Directory Domain Services: Junte-se a uma VM do Windows Server a um domínio gerido | Documentos da Microsoft'
description: Junte-se a uma máquina virtual do Windows Server para o Azure AD DS
services: active-directory-ds
documentationcenter: ''
author: eringreenlee
manager: daveba
editor: curtand
ms.assetid: 29316313-c76c-4fb9-8954-5fa5ec82609e
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/21/2018
ms.author: ergreenl
ms.openlocfilehash: f18d3409d53123733d7f423c77864cd0739765eb
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/18/2019
ms.locfileid: "57900595"
---
# <a name="join-a-windows-server-virtual-machine-to-a-managed-domain"></a>Associar uma máquina virtual do Windows Server a um domínio gerido
Este artigo mostra como implementar uma máquina virtual do Windows Server com o portal do Azure. Em seguida, mostra como associar a máquina virtual a um domínio gerido do Azure Active Directory Domain Services (Azure AD DS).

[!INCLUDE [active-directory-ds-prerequisites.md](../../includes/active-directory-ds-prerequisites.md)]

## <a name="step-1-create-a-windows-server-virtual-machine"></a>Passo 1: Criar uma máquina virtual do Windows Server
Para criar uma máquina virtual do Windows que está associada à rede virtual em que ativou o Azure AD DS, efetue os seguintes passos:

1. Inicie sessão no [portal do Azure](https://portal.azure.com).
2. Na parte superior do painel esquerdo, selecione **New**.
3. Selecione **Computação** e, em seguida, selecione **Windows Server 2016 Datacenter**.

    ![A ligação do Windows Server 2016 Datacenter](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-image.png)

4. Na **Noções básicas** painel do assistente, configure as definições básicas para a máquina virtual.

    ![O painel de noções básicas](./media/active-directory-domain-services-admin-guide/create-windows-vm-basics.png)

    > [!TIP]
    > O nome de utilizador e palavra-passe que introduzir aqui são para uma conta de administrador local que é utilizada para iniciar sessão na máquina virtual. Escolha uma palavra-passe segura para proteger a máquina virtual contra ataques de força bruta de palavra-passe. Não introduza as credenciais de uma conta de utilizador de domínio aqui.
    >

5. Selecione um **tamanho** para a máquina virtual. Para ver mais tamanhos, selecione **ver tudo** ou altere a **tipo de disco suportado** filtro.

    ![O painel "Selecionar um tamanho de"](./media/active-directory-domain-services-admin-guide/create-windows-vm-size.png)

6. Na **definições** painel, selecione a rede virtual no qual o seu domínio do Azure AD DS gerido é implementado. Escolha uma sub-rede diferente daquela que seu domínio gerido é implementado numa. Para as outras definições, mantenha as predefinições e, em seguida, selecione **OK**.

    ![Definições de rede virtual para a máquina virtual](./media/active-directory-domain-services-admin-guide/create-windows-vm-select-vnet.png)

    > [!TIP]
    > **Escolha a rede virtual correta e a sub-rede.**
    >
    > Selecione a rede virtual em como no qual o seu domínio gerido é implementado ou uma rede virtual que está ligada à mesma, utilizando virtual peering de rede. Se selecionar uma rede virtual sem ligação, não é possível associar a máquina virtual para o domínio gerido.
    >
    > Recomendamos que implemente o seu domínio gerido numa sub-rede dedicada. Por conseguinte, não escolha a sub-rede em que ativou o seu domínio gerido.

7. Para as outras definições, mantenha as predefinições e, em seguida, selecione **OK**.
8. Sobre o **Compra** página, reveja as definições e, em seguida, selecione **OK** para implementar a máquina virtual.
9. A implementação da VM está afixada ao dashboard do portal do Azure.

    ![Concluído](./media/active-directory-domain-services-admin-guide/create-windows-vm-done.png)
10. Depois de concluída a implementação, pode ver informações sobre a VM na **descrição geral** página.


## <a name="step-2-connect-to-the-windows-server-virtual-machine-by-using-the-local-administrator-account"></a>Passo 2: Ligar à máquina virtual do Windows Server com a conta de administrador local
Em seguida, ligar à máquina virtual recentemente criada do Windows Server para a associar ao domínio. Utilize as credenciais de administrador local que especificou quando criou a máquina virtual.

Para ligar à máquina virtual, execute os seguintes passos:

1. Na **descrição geral** painel, selecione **Connect**.  
    Um ficheiro do protocolo de ambiente de trabalho remoto (. rdp) é criado e transferido.

    ![Ligar à máquina de virtual do Windows](./media/active-directory-domain-services-admin-guide/connect-windows-vm.png)

2. Para ligar à sua VM, abra o ficheiro RDP transferido. Se lhe for pedido, selecione **Ligar**.
3. Introduza o seu **credenciais de administrador local**, que especificou quando criou a máquina virtual (por exemplo, *localhost\mahesh*).
4. Se vir um aviso de certificado durante o processo de início de sessão, selecione **Sim** ou **continuar** para se ligar.

Neste ponto, deve estar conectado à máquina virtual do Windows criada recentemente com as suas credenciais de administrador local. A próxima etapa é associar a máquina virtual ao domínio.


## <a name="step-3-join-the-windows-server-virtual-machine-to-the-azure-ad-ds-managed-domain"></a>Passo 3: Junte-se a máquina virtual do Windows Server com o Azure AD do domínio gerido do DS
Para associar a máquina virtual do Windows Server com o Azure AD DS gerido domínio, conclua os seguintes passos:

1. Ligue à VM do Windows Server, conforme mostrado no "Passo 2." Na **começar** ecrã aberto **Gestor de servidor**.
2. No painel esquerdo dos **Gestor de servidores** janela, selecione **servidor Local**.

    ![A janela do Gestor de servidor na máquina virtual](./media/active-directory-domain-services-admin-guide/join-domain-server-manager.png)

3. Sob **propriedades**, selecione **grupo de trabalho**.
4. Na **propriedades do sistema** janela, selecione **alteração** para aderir ao domínio.

    ![A janela de propriedades do sistema](./media/active-directory-domain-services-admin-guide/join-domain-system-properties.png)

5. Na **domínio** caixa, especifique o nome do seu domínio do Azure AD DS gerido e, em seguida, selecione **OK**.

    ![Especifique o domínio a ser associada](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-domain.png)

6. São-lhe pedido que introduza as credenciais para aderir ao domínio. Utilizar as credenciais para uma *utilizador que pertence ao grupo de administradores do Azure AD DC*. Apenas os membros deste grupo têm privilégios para associar máquinas ao domínio gerido.

    ![A janela de segurança do Windows para especificar credenciais](./media/active-directory-domain-services-admin-guide/join-domain-system-properties-specify-credentials.png)

7. Pode especificar as credenciais em qualquer uma das seguintes formas:

   * **Formato UPN**: (Recomendado) Especifique o sufixo de nome principal (UPN) do utilizador da conta de utilizador, conforme configurado no Azure AD. Neste exemplo, o sufixo UPN do utilizador *bob* é *bob\@domainservicespreview.onmicrosoft.com*.

   * **Formato de SAMAccountName**: Pode especificar o nome da conta no formato SAMAccountName. Neste exemplo, o usuário *bob* tem de introduzir *CONTOSO100\bob*.

     > [!TIP]
     > **Recomendamos que utilize o formato do UPN para especificar as credenciais.**
     >
     > Se o prefixo UPN de um utilizador for excessivamente longo (por exemplo, *joehasareallylongname*), o SAMAccountName pode ser gerado automaticamente. Se vários usuários têm o mesmo prefixo UPN (por exemplo, *bob*) no seu inquilino do Azure AD, o seu formato de SAMAccountName pode ser gerado automaticamente pelo serviço. Nestes casos, o formato o UPN pode ser utilizado de forma fiável para iniciar sessão no domínio.
     >

8. Depois de se associar com êxito a um domínio, a seguinte mensagem de boas-vindas a ao domínio.

    ![Bem-vindo ao domínio](./media/active-directory-domain-services-admin-guide/join-domain-done.png)

9. Para concluir a aderir ao domínio, reinicie a máquina virtual.

## <a name="troubleshoot-joining-a-domain"></a>Resolver problemas de associação a um domínio
### <a name="connectivity-issues"></a>Problemas de conectividade
Se a máquina virtual não conseguiu encontrar o domínio, tente os passos de resolução de problemas seguintes:

* Certifique-se de que a máquina virtual está ligada à mesma rede virtual do Azure AD DS está ativado no. Caso contrário, a máquina virtual não consegue ligar ou associar ao domínio.

* Certifique-se de que a máquina virtual está numa rede virtual que por sua vez está ligada à rede virtual do Azure AD DS está ativado.

* Tente fazer ping o nome de domínio DNS do domínio gerido (por exemplo, *ping contoso100.com*). Se não for possível fazer isso, tente fazer ping os endereços IP para o domínio que é apresentado na página onde ativou o Azure AD DS (por exemplo, *ping 10.0.0.4*). Se pode enviar pings para o endereço IP, mas não o domínio, DNS pode estar incorretamente configurado. Verifique se os endereços IP do domínio estão configurados como servidores DNS para a rede virtual.

* Tente liberar o cache do resolvedor DNS na máquina virtual (*ipconfig /flushdns*).

Se uma janela for apresentada solicitando as credenciais aderir ao domínio, não tem problemas de conectividade.

### <a name="credentials-related-issues"></a>Problemas relacionados com as credenciais
Se estiver a ter problemas com as credenciais e não é possível aderir ao domínio, tente os passos de resolução de problemas seguintes:

* Tente utilizar o formato do UPN para especificar as credenciais. Se existirem muitos utilizadores com o mesmo prefixo UPN no seu inquilino, ou se o seu prefixo UPN for excessivamente longo, SAMAccountName para a sua conta pode ser gerado automaticamente. Nestes casos, o formato de SAMAccountName para a sua conta pode ser diferente do que espera nem utiliza no seu domínio no local.

* Tente utilizar as credenciais de uma conta de utilizador que pertence a *administradores do AAD DC* grupo.

* Verifique se tiver [ativada a sincronização de palavra-passe](active-directory-ds-getting-started-password-sync.md) ao seu domínio gerido.

* Verifique que já utilizou o UPN do utilizador, conforme configurado no Azure AD (por exemplo, *bob\@domainservicespreview.onmicrosoft.com*) para iniciar sessão.

* Aguarde o tempo suficiente para a sincronização de palavra-passe para serem concluídas, conforme especificado no guia de introdução.

## <a name="related-content"></a>Conteúdo relacionado
* [O Azure AD DS guia de introdução](active-directory-ds-getting-started.md)
* [Administrar um domínio do Azure AD DS gerido](active-directory-ds-admin-guide-administer-domain.md)
