---
title: Windows Virtual Desktop (clássico) anfitrião piscina Azure Marketplace - Azure
description: Como criar uma piscina de anfitriões virtual do Windows Desktop (clássico) utilizando o Azure Marketplace.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: lizross
ms.openlocfilehash: d7e0862bd7519f59602f1e769301614895bd6e09
ms.sourcegitcommit: 9f4510cb67e566d8dad9a7908fd8b58ade9da3b7
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/01/2021
ms.locfileid: "106121885"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Tutorial: Criar uma piscina de anfitriões no Windows Virtual Desktop (clássico)

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows. Se estiver a tentar gerir os objetos de ambiente de trabalho virtuais do Azure Resource Manager Windows, consulte [este artigo](../create-host-pools-azure-marketplace.md).

Neste tutorial, você aprenderá a criar uma piscina de anfitriões dentro de um inquilino virtual do Windows desktop usando uma oferta do Microsoft Azure Marketplace.

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

As tarefas neste tutorial incluem:

> [!div class="checklist"]
>
> * Crie uma piscina de anfitriões no Windows Virtual Desktop.
> * Crie um grupo de recursos com VMs numa subscrição Azure.
> * Junte os VMs ao domínio do Diretório Ativo.
> * Registar os VMs com o Windows Virtual Desktop.

## <a name="prerequisites"></a>Pré-requisitos

* Um inquilino no Ambiente de Trabalho Virtual. Um [tutorial](tenant-setup-azure-active-directory.md) anterior cria um inquilino.
* [Módulo Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/)do Windows .

Assim que tiver este módulo, faça o seguinte cmdlet para iniciar scontabilidade na sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no [portal do Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Executar o Azure Marketplace oferecendo-se para oferecer uma nova piscina de anfitriões

Para executar o Azure Marketplace oferecendo uma nova piscina de anfitriões:

1. No menu do portal do Azure ou a partir da **Home Page**, selecione **Criar um recurso**.
1. Introduza **o Ambiente de Trabalho Virtual do Windows** na janela de pesquisa do Marketplace.
1. Selecione **Windows Virtual Desktop - Fore um pool de anfitriões** e, em seguida, selecione **Criar**.

Depois disso, siga as instruções na secção seguinte para introduzir as informações para os separadores apropriados.

### <a name="basics"></a>Noções básicas

Aqui está o que se faz para o **separador Básico:**

1. Selecione uma **subscrição**.
1. Para **o grupo de recursos,** selecione Criar **novo** e fornecer um nome para o novo grupo de recursos.
1. Selecione uma **Região**.
1. Insira um nome para a piscina de anfitriões que é única dentro do inquilino virtual do Windows Desktop.
1. Selecione **o tipo de ambiente de trabalho**. Se selecionar **Personal**, cada utilizador que se conecta a esta piscina de anfitrião está permanentemente atribuído a uma máquina virtual.
1. Insira os utilizadores que podem iniciar seducação nos clientes do Windows Virtual Desktop e aceder a um ambiente de trabalho. Use uma lista separada por vírgulas. Por exemplo, se quiser atribuir `user1@contoso.com` e `user2@contoso.com` aceder, insira *`user1@contoso.com,user2@contoso.com`*
1. Para **a localização dos metadados de serviço,** selecione a mesma localização que a rede virtual que tem conectividade com o servidor Ative Directory.

   >[!IMPORTANT]
   >Se estiver a utilizar uma solução pure Azure Ative Directory Domain Services (Azure AD DS) e Azure Ative Directory (Azure AD), certifique-se de que implanta o seu pool de anfitriões na mesma região que o Azure AD DS para evitar erros de união de domínios e credenciais.

1. Selecione **Seguinte: Configurar máquinas virtuais**.

### <a name="configure-virtual-machines"></a>Configure máquinas virtuais

Para o **separador Configure máquinas virtuais:**

1. Aceite os predefinidos ou personalize o número e o tamanho das máquinas virtuais.

    >[!NOTE]
    >Se o tamanho específico da máquina virtual que procura não aparece no seletor de tamanhos, isso é porque ainda não o acedemos à ferramenta Azure Marketplace.

2. Introduza um prefixo para os nomes das máquinas virtuais. Por exemplo, se introduzir *prefixo,* as máquinas virtuais serão chamadas **prefixo-0**, **prefixo-1**, e assim por diante.
3. Selecione **Seguinte: Configurações de máquinas virtuais**.

### <a name="virtual-machine-settings"></a>Definições da máquina virtual

Para o separador **configurações de configurações da máquina Virtual:**

1. Para **a fonte de imagem,** selecione a fonte e introduza as informações apropriadas sobre como encontrá-la e como armazená-la. As suas opções diferem para o armazenamento blob, imagem gerida e Galeria.

   Se optar por não utilizar discos geridos, selecione a conta de armazenamento que contém o ficheiro *.vhd.*
1. Insira o nome principal do utilizador e a palavra-passe. Esta conta deve ser a conta de domínio que irá juntar as máquinas virtuais ao domínio do Ative Directory. Este mesmo nome de utilizador e senha serão criados nas máquinas virtuais como conta local. Pode redefinir estas contas locais mais tarde.

   >[!NOTE]
   > Se estiver a juntar as suas máquinas virtuais a um ambiente Azure AD DS, certifique-se de que o seu domínio se junta ao utilizador é membro do grupo de [Administradores AAD DC](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > A conta também deve fazer parte do domínio gerido da Azure AD DS ou do inquilino AD Azure. As contas de diretórios externos associados ao seu inquilino AZure AD não podem autenticar corretamente durante o processo de junção de domínios.

1. Selecione a **rede Virtual** que tem conectividade com o servidor Ative Directory e, em seguida, escolha uma sub-rede para hospedar as máquinas virtuais.
1. Selecione **Seguinte: Informações do Windows Virtual Desktop**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informações sobre inquilinos virtuais do Windows Desktop

Para o separador **de informações do windows Virtual Desktop:**

1. Para **o nome do grupo de inquilinos virtual do Windows Desktop,** insira o nome para o grupo de inquilinos que contém o seu inquilino. Deixe-o como padrão, a menos que lhe tenha sido fornecido um nome específico do grupo de inquilinos.
1. Para **o nome do inquilino virtual do Windows Desktop,** insira o nome do inquilino onde estará a criar esta piscina de anfitriões.
1. Especifique o tipo de credenciais que pretende utilizar para autenticar como o proprietário RDS do Windows Virtual Desktop. Insira o upn ou o principal de serviço e uma senha.

   Se tiver concluído os [principais de serviço create e atribuições de funções com o tutorial PowerShell](create-service-principal-role-powershell.md), selecione **Service principal**.

1. Para **o diretor de serviço**, para a identificação do inquilino da **Azure AD**, insira a conta de administração do arrendatário para a instância AD Azure que contém o principal do serviço. Apenas os princípios de serviço com uma credencial de senha são suportados.
1. Selecione **Seguinte: Rever + criar**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Configuração completa e criar a máquina virtual

Em **Revisão e Criar,** reveja as informações de configuração. Se precisas de mudar alguma coisa, volta e faz mudanças. Quando estiver pronto, selecione **Criar** para implantar a sua piscina de anfitriões.

Dependendo de quantas máquinas virtuais está a criar, este processo pode demorar 30 minutos ou mais a ser concluído.

>[!IMPORTANT]
> Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nas suas máquinas virtuais. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem às máquinas virtuais da piscina anfitriã.
>
> Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize acesso just-in-time. Para mais informações, consulte [Secure your management ports com acesso just-in-time](../../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Atribuir utilizadores adicionais ao grupo de aplicações para desktop

Depois de o Azure Marketplace terminar de criar o pool, pode atribuir mais utilizadores ao grupo de aplicações para desktop. Se não quiser adicionar mais, ignore esta secção.

Para atribuir os utilizadores ao grupo de aplicações para desktop:

1. Abra uma janela do PowerShell.

1. Executar o seguinte comando para iniciar sins no ambiente de ambiente de trabalho virtual do Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Adicione os utilizadores ao grupo de aplicações para desktop utilizando este comando:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   A UPN do utilizador deve corresponder à identidade do utilizador no AZure AD, por exemplo, *user1@contoso.com* . Se pretender adicionar vários utilizadores, execute o comando para cada utilizador.

Os utilizadores que adicionar ao grupo de aplicações de ambiente de trabalho podem iniciar sessão no Windows Virtual Desktop com clientes de Ambiente de Trabalho Remoto suportados e ver um recurso para um ambiente de trabalho de sessão.

Aqui estão os atuais clientes apoiados:

* [Cliente de desktop remoto para Windows 7 e Windows 10](connect-windows-7-10-2019.md)
* [Cliente web virtual do Windows Desktop](connect-web-2019.md)

## <a name="next-steps"></a>Passos seguintes

Você fez uma piscina de anfitriões e atribuiu aos utilizadores para aceder ao seu ambiente de trabalho. Pode preencher a sua piscina de anfitriões com programas RemoteApp. Para saber mais sobre como gerir aplicações no Windows Virtual Desktop, consulte este tutorial:

> [!div class="nextstepaction"]
> [Gerir grupos de aplicações tutorial](manage-app-groups-2019.md)
