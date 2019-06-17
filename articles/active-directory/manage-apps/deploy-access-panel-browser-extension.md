---
title: Implementar a extensão de painel de acesso do Azure para o IE com um GPO | Documentos da Microsoft
description: Como utilizar a política de grupo para implementar o suplemento do Internet Explorer para o portal as minhas aplicações.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: mimart
ms.reviewer: asteen
ms.custom: H1Hack27Feb2017
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b6f069489738e9dceeee350a36aa2b45715a314
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "65825038"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como implementar a extensão do painel de acesso para o Internet Explorer usando a diretiva de grupo
Este tutorial mostra como utilizar a política de grupo para instalar remotamente a extensão do painel de acesso para o Internet Explorer nos computadores dos seus utilizadores. É necessária para os usuários do Internet Explorer que tem de iniciar sessão em aplicações que estão configuradas ao utilizar esta extensão [baseado em palavra-passe de início de sessão único](what-is-single-sign-on.md#password-based-sso).

Recomenda-se que os administradores de automatizar a implementação desta extensão. Caso contrário, os utilizadores têm de transferir e instalar a extensão, que é suscetível a erros de utilizador e requer permissões de administrador. Este tutorial abrange um método de automatização de implementações de software utilizando a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

Também está disponível para a extensão do painel de acesso [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), nenhum dos quais requerem permissões de administrador para instalar.

## <a name="prerequisites"></a>Pré-requisitos
* Configurou [serviços de domínio do Active Directory](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx), e associar máquinas dos seus utilizadores ao seu domínio.
* Tem de ter a permissão de "Edit settings" para editar o objeto de política de grupo (GPO). Por predefinição, os membros dos grupos de segurança seguintes têm esta permissão: Os administradores do domínio, administradores da empresa e proprietários de criador de políticas de grupo. [Saiba mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Passo 1: Criar o ponto de distribuição
Em primeiro lugar, tem de colocar o pacote do instalador numa localização de rede que pode ser acessada pelas máquinas que deseja instalar remotamente a extensão no. Para tal, siga estes passos:

1. Inicie sessão servidor como administrador
2. Na **Gestor de servidores** janela, aceda à **ficheiros e serviços de armazenamento**.
   
    ![Ficheiros abertos e serviços de armazenamento](./media/deploy-access-panel-browser-extension/files-services.png)
3. Vá para o **partilhas** separador. Em seguida, clique em **tarefas** > **nova partilha...**
   
    ![Ficheiros abertos e serviços de armazenamento](./media/deploy-access-panel-browser-extension/shares.png)
4. Concluir o **Assistente de nova partilha** e definir permissões para se certificar de que pode ser acedido a partir de máquinas dos seus utilizadores. [Saiba mais sobre as partilhas.](https://technet.microsoft.com/library/cc753175.aspx)
5. Baixe o seguinte pacote do Microsoft Windows Installer (arquivo. msi): [Extension do painel de acesso](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
6. Copie o pacote de instalador para uma localização pretendida na partilha.
   
    ![Copie o arquivo. msi para a partilha.](./media/deploy-access-panel-browser-extension/copy-package.png)
7. Certifique-se de que as máquinas de cliente são capazes de aceder ao pacote de instalador do compartilhamento de. 

## <a name="step-2-create-the-group-policy-object"></a>Passo 2: Criar o objeto de política de grupo
1. Inicie sessão no servidor que aloja a instalação de serviços de domínio do Active Directory (AD DS).
2. O Gestor de servidor, aceda a **ferramentas** > **gestão de políticas de grupo**.
   
    ![Vá para ferramentas > Gestão de política de grupo](./media/deploy-access-panel-browser-extension/tools-gpm.png)
3. No painel esquerdo dos **gerenciamento de diretiva de grupo** janela, visualizar a hierarquia de unidade organizacional (UO) e determine em qual âmbito que gostaria de aplicar a política de grupo. Por exemplo, poderá optar por selecionar uma UO pequena para implementar a alguns utilizadores para fins de teste, ou pode escolher uma UO de nível superior para implementar em toda a organização.
   
   > [!NOTE]
   > Se gostaria de criar ou editar as suas unidades organizacionais (UOs), mude novamente para o Gestor de servidor e aceda a **ferramentas** > **Active Directory Users and Computers**.
   > 
   > 
4. Assim que tiver selecionado um UO, faça duplo clique nele e selecione **criar um GPO neste domínio e ligá-lo aqui...**
   
    ![Criar um novo GPO](./media/deploy-access-panel-browser-extension/create-gpo.png)
5. Na **novo GPO** linha de comandos, escreva um nome para o novo objeto de política de grupo.
   
    ![Nome do novo GPO](./media/deploy-access-panel-browser-extension/name-gpo.png)
6. Com o objeto de diretiva de grupo que criou e selecione o botão direito **editar**.
   
    ![Editar o novo GPO](./media/deploy-access-panel-browser-extension/edit-gpo.png)

## <a name="step-3-assign-the-installation-package"></a>Passo 3: Atribuir o pacote de instalação
1. Determinar se pretende implementar a extensão com base na **configuração do computador** ou **configuração de utilizador**. Ao usar [configuração do computador](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx), a extensão é instalada no computador, independentemente de qual os utilizadores iniciem sessão-lo. Com o [configuração do usuário](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx), os utilizadores têm a extensão instalada para eles, independentemente de quais os computadores que iniciam sessão no.
2. No painel esquerdo dos **Editor de gerenciamento de diretiva de grupo** janela, aceda a qualquer um dos seguintes caminhos de pasta, dependendo do tipo de configuração que escolheu:
   
   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`
3. Com o botão direito **instalação de Software**, em seguida, selecione **New** > **pacote...**
   
    ![Criar um novo pacote de instalação de software](./media/deploy-access-panel-browser-extension/new-package.png)
4. Vá para a pasta partilhada que contém o pacote de instalador do [passo 1: Criar o ponto de distribuição](#step-1-create-the-distribution-point), selecione o arquivo. msi e clique em **aberto**.
   
   > [!IMPORTANT]
   > Se a partilha estiver localizada no, esse mesmo servidor, certifique-se de que está a aceder o. msi através do caminho de ficheiro de rede, em vez do caminho do ficheiro local.
   > 
   > 
   
    ![Selecione o pacote de instalação a partir da pasta compartilhada.](./media/deploy-access-panel-browser-extension/select-package.png)
5. Na **implementação de Software** linha de comandos, selecione **atribuído** para o seu método de implementação. Em seguida, clique em **OK**.
   
    ![Selecione atribuído e, em seguida, clique em OK.](./media/deploy-access-panel-browser-extension/deployment-method.png)

A extensão é agora implementada para a UO que selecionou. [Saiba mais sobre a instalação de Software da diretiva de grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Passo 4: Auto-ativar a extensão para o Internet Explorer
Além de executar o instalador, cada extensão para o Internet Explorer tem de estar explicitamente ativada antes de poder ser utilizada. Siga os passos abaixo para ativar a extensão do painel de acesso usando a diretiva de grupo:

1. Na **Editor de gerenciamento de diretiva de grupo** janela, aceda a qualquer um dos seguintes caminhos, dependendo do tipo de configuração que selecionou no [passo 3: Atribuir o pacote de instalação](#step-3-assign-the-installation-package):
   
   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
2. Com o botão direito **lista de suplemento**e selecione **editar**.
    ![Edite lista de suplemento.](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)
3. Na **lista de suplemento** janela, selecione **ativado**. Em seguida, no **opções** secção, clique em **mostrar...** .
   
    ![Clique em ativar, em seguida, clique em Mostrar...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)
4. Na **Mostrar conteúdo** janela, execute os seguintes passos:
   
   1. Na primeira coluna (os **nome do valor** campo), copie e cole o seguinte ID de classe: `{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   2. Na segunda coluna (os **valor** campo), escreva o seguinte valor: `1`
   3. Clique em **OK** para fechar a **Mostrar conteúdo** janela.
      
      ![Preencha os valores conforme especificado acima.](./media/deploy-access-panel-browser-extension/show-contents.png)
5. Clique em **OK** para aplicar as alterações e fechar o **suplemento lista** janela.

A extensão agora deve ser ativada para as máquinas na UO selecionada. [Saiba mais sobre como utilizar a política de grupo para ativar ou desativar complementos do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Passo 5 (opcional): Desativar linha de comandos "Memorizar palavra-passe"
Quando os utilizadores iniciam sessão em Web sites com a extensão do painel de acesso, o Internet Explorer pode mostrar o seguinte pedido perguntando "Gostaria de armazenar a palavra-passe?"

![Prompt de senha](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Se desejar impedir que os utilizadores vejam esta linha de comandos, em seguida, siga os passos abaixo para impedir a conclusão automática de memorizar palavras-passe:

1. Na **Editor de gerenciamento de diretiva de grupo** janela, vá para o caminho listado abaixo. Esta definição de configuração só está disponível em **configuração do usuário**.
   
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
2. Encontrar a definição denominada **ativar a funcionalidade de conclusão automática de mensagens em fila para nomes de utilizador e palavras-passe em formulários**.
   
   > [!NOTE]
   > As versões anteriores do Active Directory podem listar esta definição com o nome **não permitir a conclusão automática para guardar palavras-passe**. A configuração para essa definição difere a definição descrita neste tutorial.
   > 
   > 
   
    ![Não se esqueça de isto Procurar em definições do utilizador.](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)
3. Clique com o botão direito do rato em definição acima e selecione **editar**.
4. Na janela de intitulada **ativar a funcionalidade de conclusão automática de mensagens em fila para nomes de utilizador e palavras-passe em formulários**, selecione **desativado**.
   
    ![Selecione desativar](./media/deploy-access-panel-browser-extension/disable-passwords.png)
5. Clique em **OK** para aplicar estas alterações e fechar a janela.

Os utilizadores já não será capazes de armazenar as suas credenciais ou utilização de conclusão automática para aceder a credenciais armazenadas anteriormente. No entanto, esta política permitir que os utilizadores continuam a usar a conclusão automática de mensagens em fila para outros tipos de campos de formulário, como campos de pesquisa.

> [!WARNING]
> Se esta política será ativada após os utilizadores optaram por armazenar algumas credenciais, esta política vai *não* limpar as credenciais que já tiverem sido armazenadas.
> 
> 

## <a name="step-6-testing-the-deployment"></a>Passo 6: Teste da implementação
Siga os passos abaixo para verificar se a implementação de extensão foi concluída com êxito:

1. Se tiver implementado usando **configuração do computador**, inicie sessão numa máquina de cliente que pertence à UO que selecionou no [passo 2: Criar o objeto de política de grupo](#step-2-create-the-group-policy-object). Se tiver implementado usando **configuração do usuário**, certifique-se de iniciar sessão como um utilizador que pertença ao UO.
2. Poderá demorar alguns início de sessão ins para a política de grupo totalmente é alterado para atualizar com esta máquina. Para forçar a atualização, abra um **linha de comandos** janela e execute o seguinte comando: `gpupdate /force`
3. Tem de reiniciar a máquina para a instalação ocorra. Inicialização poderá demorar consideravelmente mais tempo do que instala normal enquanto a extensão.
4. Depois de reiniciar, abra **Internet Explorer**. No canto canto superior direito da janela, clique em **ferramentas** (o ícone de engrenagem) e, em seguida, selecione **gerir suplementos**.
   
    ![Vá para ferramentas > Gerir suplementos](./media/deploy-access-panel-browser-extension/manage-add-ons.png)
5. Na **gerir suplementos** janela, certifique-se de que o **extensão do painel de acesso** foi instalado e que seu **estado** foi definido como **ativado**.
   
    ![Certifique-se de que a extensão do painel de acesso é instalado e ativado.](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="related-articles"></a>Artigos relacionados
* [Acesso a aplicações e início de sessão único com o Azure Active Directory](what-is-single-sign-on.md)
* [A extensão do painel de acesso de resolução de problemas do Internet Explorer](manage-access-panel-browser-extension.md)

