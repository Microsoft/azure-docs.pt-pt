---
title: Implementar extensão do painel de acesso Azure para IE utilizando um GPO Microsoft Docs
description: Como utilizar a política de grupo para implementar o addon do Internet Explorer para o portal My Apps.
services: active-directory
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/08/2018
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 94c434a2892060acfdd56c496a31e41597c21357
ms.sourcegitcommit: bc943dc048d9ab98caf4706b022eb5c6421ec459
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/14/2020
ms.locfileid: "84763436"
---
# <a name="how-to-deploy-the-access-panel-extension-for-internet-explorer-using-group-policy"></a>Como: Implementar a extensão do Painel de Acesso para o Internet Explorer utilizando a política de grupo

Este tutorial mostra como utilizar a política de grupo para instalar remotamente a extensão do Painel de Acesso para o Internet Explorer nas máquinas dos seus utilizadores. Esta extensão é necessária para os utilizadores do Internet Explorer que precisam de assinar em aplicações configuradas usando [um único sinal baseado em palavra-passe.](what-is-single-sign-on.md#password-based-sso)

Recomenda-se que os administradores automatizam a implantação desta extensão. Caso contrário, os utilizadores têm de descarregar e instalar a extensão em si, o que é propenso a erro do utilizador e requer permissões de administrador. Este tutorial abrange um método de automatização de implementações de software utilizando a política de grupo. [Saiba mais sobre a política de grupo.](https://technet.microsoft.com/windowsserver/bb310732.aspx)

A extensão do Painel de Acesso também se encontra disponível para [o Chrome](https://go.microsoft.com/fwLink/?LinkID=311859) e [Firefox](https://go.microsoft.com/fwLink/?LinkID=626998), nenhum dos quais requer permissões de administrador para instalar.

## <a name="prerequisites"></a>Pré-requisitos

* Criou os [Serviços de Domínio do Diretório Ativo](https://msdn.microsoft.com/library/aa362244%28v=vs.85%29.aspx)e juntou as máquinas dos seus utilizadores ao seu domínio.
* Tem de ter a permissão de "Editar definições" para editar o Objeto de Política de Grupo (GPO). Por padrão, os membros dos seguintes grupos de segurança têm esta permissão: Administradores de Domínio, Administradores empresariais e Proprietários de Criadores de Políticas de Grupo. [Saiba mais.](https://technet.microsoft.com/library/cc781991%28v=ws.10%29.aspx)

## <a name="step-1-create-the-distribution-point"></a>Passo 1: Criar o ponto de distribuição

Em primeiro lugar, deve colocar o pacote instalador num local de rede que possa ser acedido pelas máquinas onde pretende instalar remotamente a extensão. Para tal, siga estes passos:

1. Inscreva-se no servidor como administrador.
1. Na janela Do Gestor do **Servidor,** aceda aos **Serviços de Ficheiros e Armazenamento**.

    ![Abrir ficheiros e serviços de armazenamento](./media/deploy-access-panel-browser-extension/files-services.png)

1. Vá ao **separador Ações.** Em seguida, clique em **Tarefas**  >  **Nova Partilha...**

    ![Screenshot mostra onde encontrar Nova Partilha a partir do ecrã Tasks](./media/deploy-access-panel-browser-extension/shares.png)

1. Preencha o **Novo Assistente de Partilha** e desemende permissões para garantir que pode ser acedido a partir das máquinas dos seus utilizadores. [Saiba mais sobre ações.](https://technet.microsoft.com/library/cc753175.aspx)
1. Descarregue o seguinte pacote do Microsoft Windows Installer (ficheiro.msi): [Painel de acesso Extension.msi](https://account.activedirectory.windowsazure.com/Applications/Installers/x64/Access%20Panel%20Extension.msi)
1. Copie o pacote do instalador para um local desejado na partilha.

    ![Copie o ficheiro .msi para a partilha](./media/deploy-access-panel-browser-extension/copy-package.png)

1. Verifique se as máquinas do seu cliente podem aceder ao pacote instalador a partir da partilha.

## <a name="step-2-create-the-group-policy-object"></a>Passo 2: Criar o objeto de política de grupo

1. Inscreva-se no servidor que acolhe a instalação Ative Directory Domain Services (AD DS).
1. No Gestor de Servidores, aceda à **Tools**  >  **Gestão de Políticas do Grupo**tools.

    ![Ir para ferramentas > Gestão de Políticas de Grupo](./media/deploy-access-panel-browser-extension/tools-gpm.png)

1. No painel esquerdo da janela de Gestão de Políticas de **Grupo,** consulte a sua hierarquia da Unidade Organizacional (OU) e determine em que âmbito pretende aplicar a política de grupo. Por exemplo, pode decidir escolher um pequeno OU para implementar em alguns utilizadores para testes, ou pode escolher um OU de alto nível para implementar em toda a sua organização.

   > [!NOTE]
   > Se quiser criar ou editar as suas Unidades de Organização (OUs), volte para o Gestor do Servidor e vá para **Tools**  >  **Ative Directory Users and Computers**.

1. Uma vez selecionado um OU, clique com o botão direito e **selecione Criar um GPO neste domínio, e ligá-lo aqui...**

    ![Screenshot mostra a criação de uma nova opção GPO](./media/deploy-access-panel-browser-extension/create-gpo.png)

1. No novo pedido **de GPO,** escreva um nome para o novo Objeto de Política de Grupo.
1. Clique com o botão direito no Objeto de Política de Grupo que criou e selecione **Editar**.

## <a name="step-3-assign-the-installation-package"></a>Passo 3: Atribuir o pacote de instalação

1. Determine se pretende implementar a extensão com base na **configuração do computador** ou **na configuração do utilizador**. Ao utilizar a configuração do [computador,](https://technet.microsoft.com/library/cc736413%28v=ws.10%29.aspx)a extensão é instalada no computador independentemente do facto de os utilizadores iniciarem o seu login. Com [a configuração do utilizador,](https://technet.microsoft.com/library/cc781953%28v=ws.10%29.aspx)os utilizadores têm a extensão instalada para os mesmos, independentemente dos computadores a que iniciam sessão.
1. No painel esquerdo da janela Do Editor de **Gestão de Políticas** de Grupo, vá a qualquer um dos seguintes caminhos de pasta, dependendo do tipo de configuração que escolheu:

   * `Computer Configuration/Policies/Software Settings/`
   * `User Configuration/Policies/Software Settings/`

1. Clique à direita **Na instalação do Software**e, em seguida, selecione **Novo**  >  **Pacote...**
1. Aceda à pasta partilhada que contém o pacote do instalador a partir do [passo 1: Criar o Ponto de Distribuição,](#step-1-create-the-distribution-point)selecione o ficheiro .msi e clique em **Abrir**.

   > [!IMPORTANT]
   > Se a partilha estiver localizada neste mesmo servidor, verifique se está a aceder ao .msi através do caminho do ficheiro de rede, em vez do caminho do ficheiro local.

    ![Selecione o pacote de instalação da pasta partilhada](./media/deploy-access-panel-browser-extension/select-package.png)

1. Na operação **'Implementar software',** selecione **Atribuído** para o seu método de implementação. Em seguida, clique em **OK**.

A extensão é agora implantada para a UO que selecionou. [Saiba mais sobre a Instalação de Software de Política de Grupo.](https://technet.microsoft.com/library/cc738858%28v=ws.10%29.aspx)

## <a name="step-4-auto-enable-the-extension-for-internet-explorer"></a>Passo 4: Ativar automaticamente a extensão para o Internet Explorer

Além de executar o instalador, todas as extensões do Internet Explorer devem ser ativadas explicitamente antes de poderem ser utilizadas. Siga os passos abaixo para permitir a extensão do painel de acesso utilizando a política do grupo:

1. Na janela do Editor de **Gestão de Políticas** de Grupo, vá a qualquer um dos seguintes caminhos, dependendo do tipo de configuração que escolheu no [Passo 3: Atribuir o Pacote de Instalação](#step-3-assign-the-installation-package):

   * `Computer Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`
   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/Security Features/Add-on Management`

1. Clique na **lista de adicionar**à direita e selecione **Editar**.

    ![Clique no direito em "Lista de Adicionar" e selecione "Editar"](./media/deploy-access-panel-browser-extension/edit-add-on-list.png)

1. Na janela **Lista de Adição,** selecione **Ativado**. Em seguida, na secção **Opções,** clique em **Mostrar...**.

    ![Clique em Enable e, em seguida, clique em Mostrar...](./media/deploy-access-panel-browser-extension/edit-add-on-list-window.png)

1. Na janela **'Mostrar Conteúdo',** execute os seguintes passos:

   1. Para a primeira coluna (o campo Nome de **Valor),** copie e cole o seguinte ID da classe:`{030E9A3F-7B18-4122-9A60-B87235E4F59E}`
   1. Para a segunda coluna (o campo **Valor),** digite o seguinte valor:`1`
   1. Clique **em OK** para fechar a janela **'Mostrar Conteúdo'.**

      ![Preencha os valores especificados no passo anterior](./media/deploy-access-panel-browser-extension/show-contents.png)

1. Clique **em OK** para aplicar as suas alterações e fechar a janela **'Lista de Entradas'.**

A extensão deve agora ser ativada para as máquinas da U. selecionada. [Saiba mais sobre a utilização da política de grupo para ativar ou desativar os add-ons do Internet Explorer.](https://technet.microsoft.com/library/dn454941.aspx)

## <a name="step-5-optional-disable-remember-password-prompt"></a>Passo 5 (Opcional): Desative o pedido de "Lembre-se da palavra-passe"

Quando os utilizadores iniciarem seduções em websites que utilizam a Extensão do Painel de Acesso, o Internet Explorer pode mostrar a seguinte pergunta perguntando "Gostaria de armazenar a sua palavra-passe?"

![Mostra o "Gostaria de armazenar a sua palavra-passe..." rápido](./media/deploy-access-panel-browser-extension/remember-password-prompt.png)

Se desejar evitar que os seus utilizadores vejam este pedido, siga os passos abaixo para evitar que se lembre automaticamente de palavras-passe:

1. Na janela do **Editor de Gestão de Políticas** de Grupo, vá para o caminho listado abaixo. Esta definição de configuração só está disponível na **Configuração do Utilizador.**

   * `User Configuration/Policies/Administrative Templates/Windows Components/Internet Explorer/`
1. Encontre a definição denominada **Ligue a função auto-completa para nomes de utilizador e palavras-passe nos formulários**.

   > [!NOTE]
   > Versões anteriores do Ative Directory podem listar esta definição com o nome Não permita que **se complete automaticamente para guardar palavras-passe**. A configuração para esta definição difere da definição descrita neste tutorial.

    ![Lembre-se de procurar isto em Definições do Utilizador](./media/deploy-access-panel-browser-extension/disable-auto-complete.png)

1. Clique à direita na definição acima e **selecione Editar.**
1. Na janela intitulada **Ligue a função de preencha automaticamente os nomes de utilizador e as palavras-passe nos formulários**, selecione **Disabled**.

    ![Selecione a opção "Desativado" para a função de turn-in auto-completa](./media/deploy-access-panel-browser-extension/disable-passwords.png)

1. Clique **em OK** para aplicar estas alterações e fechar a janela.

Os utilizadores deixarão de poder armazenar as suas credenciais ou utilizar automaticamente as credenciais previamente armazenadas. No entanto, esta política permite que os utilizadores continuem a utilizar o auto-completo para outros tipos de campos de formulários, como campos de pesquisa.

> [!WARNING]
> Se esta política for ativada depois de os utilizadores terem optado por armazenar algumas credenciais, esta política *não* irá limpar as credenciais que já foram armazenadas.

## <a name="step-6-testing-the-deployment"></a>Passo 6: Testar a implantação

Siga os passos abaixo para verificar se a extensão foi bem sucedida:

1. Se tiver implementado a **configuração do computador,** inscreva-se numa máquina cliente que pertence à U que selecionou no [Passo 2: Criar o Objeto de Política de Grupo](#step-2-create-the-group-policy-object). Se tiver implementado a **configuração do utilizador,** certifique-se de que se inscreve como um utilizador que pertence a esse OU.
1. Pode ser necessário um par de inscrições para que as alterações de política de grupo possam ser atualizadas completamente com esta máquina. Para forçar a atualização, abra uma janela **de pedido de comando** de comando e execute o seguinte comando:`gpupdate /force`
1. Tem de reiniciar a máquina para que a instalação se realize. O arranque pode demorar significativamente mais tempo do que o habitual enquanto a extensão se instala.
1. Depois de reiniciar, abra o **Internet Explorer**. No canto superior direito da janela, clique em **Ferramentas** (o ícone da engrenagem) e, em seguida, **selecione Gerir os add-ons**.
1. Na janela **'Gerir add-ons',** verifique se a **extensão** do painel de acesso foi instalada e se o seu **Estado** foi definido como **Ativado**.

   ![Verifique se a extensão do painel de acesso está instalada e ativada](./media/deploy-access-panel-browser-extension/verify-install.png)

## <a name="learn-more"></a>Saiba mais

* [Acesso à aplicação e inscrição única com diretório ativo Azure](what-is-single-sign-on.md)
* [Resolução de problemas da extensão do painel de acesso para o Internet Explorer](manage-access-panel-browser-extension.md)
