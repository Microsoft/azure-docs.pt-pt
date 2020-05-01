---
title: Windows Virtual Desktop host pool Azure Marketplace - Azure
description: Como criar um pool de anfitriões do Windows Virtual Desktop utilizando o Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 741bd94b290560bdc850cbf7bc24ec57104d8a66
ms.sourcegitcommit: 50ef5c2798da04cf746181fbfa3253fca366feaa
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/30/2020
ms.locfileid: "82614355"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Tutorial: Criar uma piscina de anfitriões usando o Azure Marketplace

>[!IMPORTANT]
>Este conteúdo aplica-se à versão outono 2019 que não suporta objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure. Se está a tentar gerir os objetos de ambiente de trabalho virtual do Gestor de Recursos Do Azure Windows introduzidos na atualização da primavera de 2020, consulte [este artigo](../create-host-pools-azure-marketplace.md).

Neste tutorial, você vai aprender a criar uma piscina de anfitriões dentro de um inquilino do Windows Virtual Desktop usando uma oferta do Microsoft Azure Marketplace.

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

As tarefas neste tutorial incluem:

> [!div class="checklist"]
>
> * Crie uma piscina de anfitriões no Windows Virtual Desktop.
> * Crie um grupo de recursos com VMs numa subscrição Azure.
> * Junte-se aos VMs no domínio do Diretório Ativo.
> * Registe os VMs com o Windows Virtual Desktop.

## <a name="prerequisites"></a>Pré-requisitos

* Um inquilino no Ambiente de Trabalho Virtual. Um [tutorial](tenant-setup-azure-active-directory.md) anterior cria um inquilino.
* [Módulo PowerShell de ambiente](/powershell/windows-virtual-desktop/overview/)de trabalho virtual windows .

Assim que tiver este módulo, faça o seguinte cmdlet para iniciar sessão na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Executar o Azure Marketplace oferecendo para fornecer um novo pool de anfitriões

Para gerir o Azure Marketplace oferecendo-se para fornecer um novo pool de anfitriões:

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
1. Introduza o **Windows Virtual Desktop** na janela de pesquisa do Marketplace.
1. Selecione **Windows Virtual Desktop - Forme uma piscina de anfitriões**e, em seguida, selecione **Criar**.

Depois disso, siga as instruções na secção seguinte para introduzir as informações para as abas apropriadas.

### <a name="basics"></a>Noções básicas

Eis o que faz para o separador **Basics:**

1. Selecione uma **Subscrição**.
1. Para **o grupo Recursos,** selecione **Criar novo** e fornecer um nome para o novo grupo de recursos.
1. Selecione uma **Região**.
1. Insira um nome para a piscina anfitriã que é única dentro do inquilino do Windows Virtual Desktop.
1. Selecione **o tipo de ambiente**de trabalho . Se selecionar **Personal**, cada utilizador que se conecta a esta piscina de hospedante é permanentemente atribuído a uma máquina virtual.
1. Insira os utilizadores que possam iniciar sessão nos clientes do Windows Virtual Desktop e aceda a um ambiente de trabalho. Use uma lista separada de vírina. Por exemplo, se quiser `user1@contoso.com` atribuir `user2@contoso.com` e aceder, insira*`user1@contoso.com,user2@contoso.com`*
1. Para a localização dos **metadados do Serviço,** selecione a mesma localização que a rede virtual que tem conectividade com o servidor Ative Directory.

   >[!IMPORTANT]
   >Se estiver a utilizar uma solução pura de domínio de diretório ativo Azure (Azure AD DS) e azure Ative Directory (Azure AD), certifique-se de implementar o seu pool de acolhimento na mesma região que o seu Azure AD DS para evitar erros de adesão ao domínio e credenciais.

1. Selecione **Seguinte: Configure as máquinas virtuais**.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Para o separador **de máquinas virtuais Configure:**

1. Ou aceita os predefinidos ou personaliza o número e o tamanho das máquinas virtuais.

    >[!NOTE]
    >Se o tamanho específico da máquina virtual que procura não aparecer no seletor de tamanho, isso é porque ainda não o embarcamos na ferramenta Azure Marketplace. Para solicitar um tamanho, crie um pedido ou revote um pedido existente no [fórum Windows Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Introduza um prefixo para os nomes das máquinas virtuais. Por exemplo, se introduzir *prefixo,* as máquinas virtuais serão chamadas **prefixo-0**, **prefixo-1**, e assim por diante.
1. Selecione **Seguinte: Definições de máquina virtual**.

### <a name="virtual-machine-settings"></a>Definições da máquina virtual

Para o separador de definições da **máquina Virtual:**

1. Para obter fonte de **imagem,** selecione a fonte e introduza as informações apropriadas para como encontrá-la e como armazená-la. As suas opções diferem para armazenamento blob, imagem gerida e Galeria.

   Se optar por não utilizar discos geridos, selecione a conta de armazenamento que contém o ficheiro *.vhd.*
1. Introduza o nome principal do utilizador e a palavra-passe. Esta conta deve ser a conta de domínio que irá juntar as máquinas virtuais ao domínio do Diretório Ativo. Este mesmo nome de utilizador e palavra-passe serão criados nas máquinas virtuais como uma conta local. Pode repor estas contas locais mais tarde.

   >[!NOTE]
   > Se estiver a juntar as suas máquinas virtuais a um ambiente Azure AD DS, certifique-se de que o utilizador de adesão ao seu domínio é membro do grupo de administradores da [AAD DC](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > A conta também deve fazer parte do domínio gerido pela Azure AD DS ou pelo inquilino Azure AD. As contas de diretórios externos associados ao seu inquilino Azure AD não podem autenticar corretamente durante o processo de união de domínios.

1. Selecione a **rede Virtual** que tem conectividade com o servidor Ative Directory e, em seguida, escolha uma subnet para hospedar as máquinas virtuais.
1. Selecione **Seguinte: Informações do Windows Virtual Desktop**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informações sobre o inquilino virtual do Windows Desktop

Para o separador de informações do **inquilino do Windows Virtual Desktop:**

1. Para o nome do grupo de **inquilinos Windows Virtual Desktop,** insira o nome do grupo de inquilinos que contém o seu inquilino. Deixe-o como o padrão, a menos que lhe tenha sido dado um nome específico de grupo de inquilinos.
1. Para o nome do **inquilino Do Windows Virtual Desktop,** insira o nome do inquilino onde estará a criar esta piscina de hospedada.
1. Especifique o tipo de credenciais que pretende utilizar para autenticar como o proprietário do IDS Dono do Ambiente de Trabalho Virtual do Windows. Insira o diretor de serviço da UPN ou do Serviço e uma palavra-passe.

   Se tiver concluído os [principais de serviço Create e atribuições de funções com tutorial PowerShell,](create-service-principal-role-powershell.md)selecione **Service principal**.

1. Para **o principal de serviço,** para id de inquilino da **AD Azure,** insira a conta de administrador a pedido do arrendatário para a instância Azure AD que contém o diretor de serviço. Apenas são suportados os diretores de serviço com uma credencial de senha.
1. Selecione **Seguinte: Rever + criar**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Complete a configuração e crie a máquina virtual

Em **Rever e Criar,** reveja as informações de configuração. Se precisar mudar alguma coisa, volte e faça mudanças. Quando estiver pronto, selecione **Create** para implementar a sua piscina anfitriã.

Dependendo de quantas máquinas virtuais está a criar, este processo pode demorar 30 minutos ou mais para ser concluído.

>[!IMPORTANT]
> Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nas suas máquinas virtuais. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para que os utilizadores acedam às máquinas virtuais do grupo anfitrião.
>
> Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize acesso just-in-time. Para mais informações, consulte Proteja as suas portas de [gestão com acesso just-in-time](../../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Atribuir utilizadores adicionais ao grupo de aplicações de ambiente de trabalho

Depois do Azure Marketplace terminar de criar a piscina, pode atribuir mais utilizadores ao grupo de aplicações de desktop. Se não quiser adicionar mais, ignore esta secção.

Para atribuir os utilizadores ao grupo de aplicações de ambiente de trabalho:

1. Abra uma janela do PowerShell.

1. Executar o seguinte comando para iniciar sessão no ambiente de ambiente de trabalho virtual do Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Adicione os utilizadores ao grupo de aplicações de ambiente de trabalho utilizando este comando:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   A UPN do utilizador deve corresponder à identidade do utilizador em *user1@contoso.com*Azure AD, por exemplo, . Se pretender adicionar vários utilizadores, execute o comando para cada utilizador.

Os utilizadores que adicionar ao grupo de aplicações de desktop podem iniciar sessão no Windows Virtual Desktop com clientes de ambiente de trabalho remoto suportados e ver um recurso para um ambiente de trabalho de sessão.

Aqui estão os clientes suportados atuais:

* [Cliente de Ambiente de Trabalho Remoto para Windows 7 e Windows 10](../connect-windows-7-and-10.md)
* [Windows Virtual Desktop web client](connect-web-2019.md)

## <a name="next-steps"></a>Passos seguintes

Fez uma piscina de anfitriões e atribuiu aos utilizadores acesso ao seu ambiente de trabalho. Você pode povoar sua piscina anfitriã com programas RemoteApp. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, consulte este tutorial:

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](../manage-app-groups.md)
