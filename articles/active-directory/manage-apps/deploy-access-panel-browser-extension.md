---
title: Implementar extensão do Painel de Acesso Azure para IE utilizando um GPO [ Microsoft Docs
description: Como utilizar a política de grupo para implementar o addon do Internet Explorer para o portal My Apps.
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
ms.collection: M365-identity-device-management
ms.openlocfilehash: 71c342ede77349b3f6c22093e5877ad5f5ce6549
ms.sourcegitcommit: b1e25a8a442656e98343463aca706f4fde629867
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/27/2020
ms.locfileid: "67807691"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como: Implementar a extensão do Painel de Acesso para o Internet Explorer utilizando a política do grupo

Este tutorial mostra como usar a política de grupo para instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nas máquinas dos seus utilizadores. Esta extensão é necessária para os utilizadores do Internet Explorer que precisam de iniciar sessão em aplicações configuradas usando um [único sinal baseado em palavra-passe](what-is-single-sign-on.md#password-based-sso).

Recomenda-se que os administradores automatizam a implantação desta extensão. Caso contrário, os utilizadores têm de descarregar e instalar a extensão por si próprias, o que é propenso a erros do utilizador e requer permissões de administrador. Este tutorial abrange um método de automatização de implementações de software utilizando a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A extensão do Painel de Acesso também está disponível para [Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), nenhum dos quais requer permissões de administrador para instalar.

## <a name="prerequisites"></a>Pré-requisitos

* Criou serviços de domínio de [diretório ativo](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e juntou-se às máquinas dos seus utilizadores para o seu domínio.
* Deve ter a permissão "Editar configurações" para editar o Objeto de Política de Grupo (GPO). Por padrão, os membros dos seguintes grupos de segurança têm esta permissão: Administradores de Domínio, Administradores de Empresas e Proprietários de Criadores de Políticas de Grupo. [Saiba mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Passo 1: Criar o ponto de distribuição

Em primeiro lugar, deve colocar a embalagem do instalador num local de rede que possa ser acedido pelas máquinas que pretende instalar remotamente a extensão. Para tal, siga estes passos:

1. Inscreva-se no servidor como administrador.
1. Na janela do Gestor de **Servidores,** vá a **Ficheiros e Serviços**de Armazenamento.

    ![Arquivos abertos e serviços de armazenamento](./media/deploy-access-panel-browser-extension/files-services.png)

1. Vá ao separador **Shares.** Em seguida, clique em **Tarefas** > **Novas Partilhas...**

    ![Screenshot mostra onde encontrar New Share a partir do ecrã tasks](./media/deploy-access-panel-browser-extension/shares.png)

1. Complete o **Novo Assistente** de Partilha e detete teperte permissões para garantir que pode ser acedido a partir das máquinas dos seus utilizadores. [Saiba mais sobre ações.](https://technet.microsoft.com/library/cc753175.aspx)
1. Descarregue o seguinte pacote do Microsoft Windows Installer (ficheiro.msi): [Access Panel Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Copie o pacote do instalador para um local desejado na parte.

    ![Copiar o ficheiro .msi para a parte](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Verifique se as máquinas do cliente são capazes de aceder ao pacote de instalação a partir da parte.

## <a name="step-2-create-the-group-policy-object"></a>Passo 2: Criar o objeto político do grupo

1. Inscreva-se no servidor que acolhe a instalação ative Directory Domain Services (AD DS).
1. No Gestor de Servidores, vá à**Gestão de Políticas**do Grupo **Tools.** > 

    ![Ir a Ferramentas > Gestão de Políticas de Grupo](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. No painel esquerdo da janela de Gestão de Políticas do **Grupo,** veja a sua hierarquia da Unidade Organizacional (OU) e determine em que âmbito pretende aplicar a política do grupo. Por exemplo, você pode decidir escolher um pequeno OU para implementar para alguns utilizadores para testes, ou você pode escolher um OU de alto nível para implementar para toda a sua organização.

   > [!NOTE]
   > Se quiser criar ou editar as suas Unidades de Organização (OUs), volte para o Gestor de Servidores e vá para **Tools** > **Ative Directory Users and Computers**.

1. Depois de ter selecionado um OU, clique-o à direita e selecione **Criar um GPO neste domínio, e link-o aqui...**

    ![Screenshot mostra a Criação de uma nova opção GPO](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. No pedido de **GPO Novo,** digite um nome para o novo Objeto político do grupo.
1. Clique no Objeto de Política de Grupo que criou e **selecione Editar**.

## <a name="step-3-assign-the-installation-package"></a>Passo 3: Atribuir o pacote de instalação

1. Determine se deseja implementar a extensão com base na **configuração** do computador ou **na configuração**do utilizador . Ao utilizar a configuração do [computador,](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)a extensão é instalada no computador, independentemente da qual os utilizadores acedam ao mesmo. Com a configuração do [utilizador,](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)os utilizadores têm a extensão instalada para eles, independentemente dos computadores em que acedam.
1. No painel esquerdo da janela editor de gestão de políticas do **grupo,** vá a qualquer um dos seguintes caminhos de pastas, dependendo do tipo de configuração que escolheu:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Clique direito **na instalação**do Software, em seguida, selecione **New** > **Package...**
1. Vá para a pasta partilhada que contém o pacote de instalação do [Passo 1: Criar o Ponto](#step-1-create-the-distribution-point)de Distribuição, selecione o ficheiro .msi e clique em **Abrir**.

   > [!IMPORTANT]
   > Se a parte estiver localizada neste mesmo servidor, verifique se está a aceder ao .msi através do caminho do ficheiro de rede, em vez do caminho de ficheiro local.

    ![Selecione o pacote de instalação a partir da pasta partilhada](./media/deploy-access-panel-browser-extension/select-package.png)

1. No pedido de **software de implementação,** selecione Designado para o seu método **de** implementação. Em seguida, clique em **OK**.

A extensão está agora implantada para a U.E. que selecionou. [Saiba mais sobre a Instalação de Software de Política de Grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Passo 4: Ativar automaticamente a extensão para o Internet Explorer

Além de executar o instalador, todas as extensões para o Internet Explorer devem ser ativadas explicitamente antes de poderem ser utilizadas. Siga os passos abaixo para permitir a extensão do painel de acesso utilizando a política do grupo:

1. Na janela Editor de Gestão de Políticas do **Grupo,** vá a qualquer um dos seguintes caminhos, dependendo do tipo de configuração que escolheu no Passo 3: Atribuir o Pacote de [Instalação:](#step-3-assign-the-installation-package)

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Clique direito na **lista de add-on,** e selecione **Editar**.

    ![Clique na direita "Lista de adicionar" e selecione "Editar"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. Na janela **Lista add-on,** selecione **Ativado**. Em seguida, sob a secção **Opções,** clique em **Mostrar...**.

    ![Clique em Ativar e, em seguida, clique em Mostrar...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. Na janela **Show Contents,** execute os seguintes passos:

   1. Para a primeira coluna (o campo **Nome de Valor),** copie e cole o seguinte ID de classe:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Para a segunda coluna (o campo **Valor),** escreva no seguinte valor:`1`
   1. Clique **em OK** para fechar a janela Show **Conteúdos.**

      ![Preencha os valores especificados no passo anterior](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Clique **em OK** para aplicar as suas alterações e fechar a janela Lista de **Adição.**

A extensão deve agora ser ativada para as máquinas no Ou selecionado. [Saiba mais sobre a utilização da política de grupo para ativar ou desativar add-ons do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Passo 5 (Opcional): Desativar a solicitação "Lembre-se da palavra-passe"

Quando os utilizadores acedem a websites que utilizam a extensão do Painel de Acesso, o Internet Explorer pode mostrar o seguinte pedido perguntando "Gostaria de armazenar a sua palavra-passe?"

![Mostra o "Gostaria de armazenar a sua senha..." pronta](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Se pretender evitar que os seus utilizadores vejam este pedido, siga os passos abaixo para evitar que o complete automaticamente as palavras-passe:

1. Na janela editor de gestão de políticas do **grupo,** vá para o caminho listado abaixo. Esta definição de configuração só está disponível na configuração do **utilizador.**

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Encontre a definição denominada **Ligar a função de conclusão automática para nomes de utilizadores e palavras-passe em formulários**.

   > [!NOTE]
   > Versões anteriores do Ative Directory podem listar esta definição com o nome **Não permita que o auto-completo guarde senhas**. A configuração para essa definição difere da definição descrita neste tutorial.

    ![Lembre-se de procurar isto em definições do utilizador](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Clique na definição acima e selecione **Editar**.
1. Na janela intitulada **Ligue a função de conclusão automática para nomes de utilizador e palavras-passe em formulários**, selecione **Disabled**.

    ![Selecione a opção "Desativado" para a função de ligar automaticamente](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Clique **em OK** para aplicar estas alterações e fechar a janela.

Os utilizadores deixarão de poder armazenar as suas credenciais ou utilizar automaticamente para aceder a credenciais previamente armazenadas. No entanto, esta política permite que os utilizadores continuem a utilizar automaticamente para outros tipos de campos de formulários, como campos de pesquisa.

> [!WARNING]
> Se esta política for ativada depois de os utilizadores terem optado por armazenar algumas credenciais, esta política *não* limpará as credenciais que já foram armazenadas.

## <a name="step-6-testing-the-deployment"></a>Passo 6: Testar a implantação

Siga os passos abaixo para verificar se a extensão foi bem sucedida:

1. Se foi implementado utilizando a **Configuração**do Computador, inscreva-se numa máquina cliente que pertence à Ou selecionada no [Passo 2: Criar o Objeto de Política de Grupo](#step-2-create-the-group-policy-object). Se tiver implementado utilizando a Configuração do **Utilizador,** certifique-se de iniciar sessão como utilizador que pertença a esse Ou.
1. Pode ser preciso um par de inscrições para as alterações da política do grupo para atualizar totalmente com esta máquina. Para forçar a atualização, abra uma janela **Command Prompt** e execute o seguinte comando:`gpupdate /force`
1. Tem de reiniciar a máquina para que a instalação tenha lugar. O arranque pode demorar significativamente mais tempo do que o habitual enquanto a extensão se instala.
1. Depois de reiniciar, abra o **Internet Explorer.** No canto superior direito da janela, clique em **Ferramentas** (o ícone de engrenagem) e, em seguida, **selecione 'Gerir add-ons**' .
1. Na janela **'Gerir add-ons',** verifique se a **extensão** do painel de acesso foi instalada e se o seu **Estado** foi definido para **ativado**.

   ![Verifique se a extensão do painel de acesso está instalada e ativada](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Saiba mais

* [Acesso à aplicação e inscrição única com diretório ativo azure](what-is-single-sign-on.md)
* [Resolução de problemas da extensão do painel de acesso para o Internet Explorer](manage-access-panel-browser-extension.md)
