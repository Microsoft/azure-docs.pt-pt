---
title: Windows Virtual Desktop host pool Azure Marketplace - Azure
description: Como criar um pool de anfitriões do Windows Virtual Desktop utilizando o Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: 40659414848adb9be86b6163cf456809e9cb750f
ms.sourcegitcommit: f718b98dfe37fc6599d3a2de3d70c168e29d5156
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/11/2020
ms.locfileid: "77134602"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Tutorial: Criar uma piscina de anfitriões usando o Azure Marketplace

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

Este tutorial descreve como criar uma piscina de anfitriões dentro de um inquilino do Windows Virtual Desktop utilizando uma oferta do Microsoft Azure Marketplace. As tarefas incluem:

> [!div class="checklist"]
> * Crie uma piscina de anfitriões no Windows Virtual Desktop.
> * Crie um grupo de recursos com VMs numa subscrição Azure.
> * Junte-se aos VMs no domínio do Diretório Ativo.
> * Registe os VMs com o Windows Virtual Desktop.

Antes de começar, [faça o download e importe o módulo Windows Virtual Desktop PowerShell](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para utilizar na sua sessão PowerShell se ainda não o fez. Depois disso, execute o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [Portal do Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Executar o Azure Marketplace oferecendo para fornecer um novo pool de anfitriões

Para gerir o Azure Marketplace oferecendo-se para fornecer um novo pool de anfitriões:

1. No menu do portal Azure ou na página **Inicial,** selecione **Criar um recurso**.
2. Introduza o **Windows Virtual Desktop** na janela de pesquisa do Marketplace.
3. Selecione **Windows Virtual Desktop - Forme uma piscina de anfitriões**e, em seguida, selecione **Criar**.

Depois disso, siga as instruções na secção seguinte para introduzir as informações para as lâminas apropriadas.

### <a name="basics"></a>Noções básicas

Eis o que faz esquelético para a lâmina **básica:**

1. Insira um nome para a piscina anfitriã que é única dentro do inquilino do Windows Virtual Desktop.
2. Selecione a opção adequada para um ambiente de trabalho pessoal. Se selecionar **Sim,** cada utilizador que se conecte a esta piscina será permanentemente atribuído a uma máquina virtual.
3. Insira uma lista separada de utilizadores que possam iniciar sessão nos clientes do Windows Virtual Desktop e aceder a um ambiente de trabalho após o Acabamento da oferta do Azure Marketplace. Por exemplo, se quiser atribuir user1@contoso.com e user2@contoso.com acesso, introduza "user1@contoso.com,user2@contoso.com."
4. Selecione **Criar novo** e fornecer um nome para o novo grupo de recursos.
5. Para **localização**, selecione a mesma localização que a rede virtual que tem conectividade com o servidor De Diretório Ativo.
6. Selecione **Seguinte: Configure as máquinas virtuais >** .

>[!IMPORTANT]
>Se estiver a utilizar uma solução pura de domínio de diretório ativo Azure e uma solução de Diretório Ativo Azure, certifique-se de implementar o seu pool de hospedagem na mesma região que os seus Serviços de Domínio de Diretório Ativo Azure para evitar erros de adesão ao domínio e credenciais.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Para a lâmina de **máquinas virtuais Configure:**

1. Ou aceita os predefinidos ou personaliza o número e o tamanho dos VMs.
    
    >[!NOTE]
    >Se o tamanho de VM específico que procura não aparecer no seletor de tamanho VM, isso é porque ainda não o embarcamos na ferramenta Azure Marketplace. Para solicitar um tamanho VM, crie um pedido ou revote um pedido existente no [fórum Windows Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).
    
2. Introduza um prefixo para os nomes das máquinas virtuais. Por exemplo, se introduzir o nome "prefixo", as máquinas virtuais serão chamadas de "prefixo-0", "prefixo-1", e assim por diante.
3. Selecione **Seguinte : Definições de máquinavirtual**.

### <a name="virtual-machine-settings"></a>Definições da máquina virtual

Para a lâmina de definição da **máquina virtual:**

>[!NOTE]
> Se estiver a juntar os seus VMs a um ambiente azure Ative Directory Domain Services (Azure AD DS), certifique-se de que o utilizador de adesão ao seu domínio é membro do grupo de administradores da [AAD DC](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
>
> A conta também deve fazer parte do domínio gerido pela Azure AD DS ou pelo inquilino Azure AD - contas de diretórios externos associados ao seu inquilino Azure AD não podem autenticar corretamente durante o processo de adesão ao domínio. 

1. Para obter fonte de **imagem,** selecione a fonte e introduza as informações apropriadas para como encontrá-la e como armazená-la. Se optar por não utilizar discos geridos, selecione a conta de armazenamento que contém o ficheiro .vhd.
2. Introduza o nome principal do utilizador e a palavra-passe para a conta de domínio que irá juntar os VMs ao domínio do Diretório Ativo. Este mesmo nome de utilizador e palavra-passe serão criados nas máquinas virtuais como uma conta local. Pode repor estas contas locais mais tarde.
3. Selecione a rede virtual que tem conectividade com o servidor Ative Directory e, em seguida, escolha uma subnet para hospedar as máquinas virtuais.
4. Selecione **Seguinte: Informações do Windows Virtual Desktop**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informações sobre o inquilino virtual do Windows Desktop

Para a lâmina de informação do **inquilino do Windows Virtual Desktop:**

1. Para o nome do grupo de **inquilinos Windows Virtual Desktop,** insira o nome do grupo de inquilinos que contém o seu inquilino. Deixe-o como o padrão, a menos que lhe tenha sido dado um nome específico de grupo de inquilinos.
2. Para o nome do **inquilino Do Windows Virtual Desktop,** insira o nome do inquilino onde estará a criar esta piscina de hospedada.
3. Especifique o tipo de credenciais que pretende utilizar para autenticar como o proprietário do IDS Dono do Ambiente de Trabalho Virtual do Windows. Se tiver concluído os [principais de serviço Create e atribuições de funções com tutorial PowerShell,](./create-service-principal-role-powershell.md)selecione **Service principal**. Quando aparecer o ID do **inquilino da Azure AD,** introduza o ID para a instância de Diretório Ativo Azure que contenha o diretor de serviço.
4. Insira as credenciais para a conta de administrador a inquilino. Apenas são suportados os diretores de serviço com uma credencial de senha.
5. Selecione **Seguinte : Rever + criar**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Complete a configuração e crie a máquina virtual

Para as duas últimas lâminas:

1. Na lâmina **'Rever e Criar',** reveja as informações de configuração. Se precisar de mudar alguma coisa, volte para a lâmina apropriada e faça a sua mudança antes de continuar. Se a informação parecer correta, selecione **OK**.
2. Selecione **Criar** para implementar a sua piscina anfitriã.

Dependendo de quantos VMs está a criar, este processo pode demorar 30 minutos ou mais para ser concluído.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Atribuir utilizadores adicionais ao grupo de aplicações de ambiente de trabalho

Depois do Azure Marketplace oferecer acabamentos, pode atribuir mais utilizadores ao grupo de aplicações de desktop antes de começar a testar os desktops da sessão completa nas suas máquinas virtuais. Se já adicionou utilizadores predefinidos na oferta do Azure Marketplace e não quer adicionar mais, pode saltar esta secção.

Para atribuir os utilizadores ao grupo de aplicações de ambiente de trabalho, tem primeiro de abrir uma janela PowerShell. Depois disso, terá de introduzir os seguintes dois cmdlets.

Execute o seguinte cmdlet para iniciar sessão no ambiente de ambiente de trabalho virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Adicione os utilizadores ao grupo de aplicações de ambiente de trabalho utilizando este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A UPN do utilizador deve corresponder à identidade do utilizador no Diretório Ativo Azure (por exemplo, user1@contoso.com). Se pretender adicionar vários utilizadores, tem de executar este cmdlet para cada utilizador.

Depois de ter concluído estes passos, os utilizadores adicionados ao grupo de aplicações para desktop podem iniciar sessão no Windows Virtual Desktop com clientes de ambiente de trabalho remoto suportados e ver um recurso para um ambiente de trabalho de sessão.

Aqui estão os clientes suportados atuais:

- [Cliente de Ambiente de Trabalho Remoto para Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Windows Virtual Desktop web client](connect-web.md)

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs do grupo anfitrião. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [o acesso VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Passos seguintes

Agora que fez uma piscina de anfitriões e atribuiu aos utilizadores para acederem ao seu ambiente de trabalho, pode povoar a sua piscina de anfitriões com programas RemoteApp. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, consulte este tutorial:

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](./manage-app-groups.md)
