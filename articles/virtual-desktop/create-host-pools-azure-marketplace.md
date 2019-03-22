---
title: Criar um conjunto de anfitriões com o Azure Marketplace (pré-visualização) - Azure
description: Como criar um conjunto de anfitrião de área de Trabalho Virtual do Windows com o Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: a1d172f219580b390ba4855928d6f6a68c8269b9
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318114"
---
# <a name="tutorial-create-a-host-pool-with-azure-marketplace-preview"></a>Tutorial: Criar um conjunto de anfitriões com o Azure Marketplace (pré-visualização)

Conjuntos de anfitrião são uma coleção de um ou mais máquinas virtuais idênticas em ambientes de inquilino (pré-visualização) de área de Trabalho Virtual do Windows. Cada conjunto de anfitrião pode conter um grupo de aplicações que os usuários podem interagir com como numa área de trabalho física.

Este artigo descreve como criar um conjunto de anfitriões dentro de um inquilino de área de Trabalho Virtual do Windows através de uma oferta do Microsoft Azure Marketplace. Isto inclui a criação de um conjunto de anfitrião no Windows área de Trabalho Virtual, criar um grupo de recursos com as VMs numa subscrição do Azure, adicionar essas VMs ao domínio do Active Directory e registar as VMs com a área de Trabalho Virtual do Windows.

Antes de começar, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Inicie sessão no portal do Azure em <https://portal.azure.com>.

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Executar a oferta para aprovisionar um novo conjunto de anfitrião do Azure Marketplace

Para executar a oferta para aprovisionar um novo conjunto de anfitrião do Azure Marketplace:

1. Selecione **+** ou **+ criar um recurso**.
2. Introduza **área de Trabalho Virtual do Windows** na janela de pesquisa de mercado.
3. Selecione **Desktop Virtual do Windows - aprovisionar um conjunto de anfitrião**, em seguida, selecione **criar**.

Siga as indicações para introduzir as informações para os painéis apropriados.

### <a name="basics"></a>Noções básicas

Eis o que fazer para o painel Noções básicas:

1. Introduza um nome para o conjunto de anfitrião que seja exclusivo no inquilino de área de Trabalho Virtual do Windows.
2. Selecione a opção adequada para a área de trabalho pessoal. Se selecionou **Sim**, cada utilizador que se liga a este conjunto de anfitrião irá ser permanentemente atribuído a uma máquina virtual.
3. Introduza uma lista separada por vírgulas de utilizadores que podem iniciar sessão para os clientes de ambiente de Trabalho Virtual do Windows e aceder a uma área de trabalho após a conclusão da oferta do Azure Marketplace. Por exemplo, se quiser atribuir user1@contoso.com e user2@contoso.com aceder, introduza "user1@contoso.com,user2@contoso.com."
4. Selecione **criar novo** e forneça um nome para o novo grupo de recursos.
5. Para **localização**, selecione a mesma localização que a rede virtual que tem conetividade ao servidor do Active Directory.
6. Selecione **OK**.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Para o painel de máquinas virtuais de configurar:

1. Aceite as predefinições ou personalizar o número e tamanho das VMs.
2. Introduza um prefixo para os nomes das máquinas virtuais. Por exemplo, se introduzir prefixo de nome de"", as máquinas virtuais serão chamadas "prefixo-0," "prefixo-1" e assim por diante.
3. Selecione **OK**.

### <a name="virtual-machine-settings"></a>Definições da máquina virtual

Para o painel de definição de Máquina Virtual:

1. Selecione o **origem da imagem** e introduza as informações apropriadas para a encontrá-la e armazená-los. Se optar por não utilizar discos geridos, selecione a conta de armazenamento que contém o ficheiro. vhd.
2. Introduza o nome principal de utilizador e palavra-passe da conta de domínio que irão aderir as VMs para o domínio do Active Directory. Esse mesmo nome de utilizador e palavra-passe serão criadas nas máquinas virtuais como uma conta local. Pode redefinir essas contas locais mais tarde.
3. Selecione a rede virtual que tem conetividade ao servidor do Active Directory, em seguida, escolha uma sub-rede para alojar as máquinas virtuais.
4. Selecione **OK**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informações de inquilino de área de Trabalho Virtual do Windows

Para o painel de informações do inquilino de área de Trabalho Virtual do Windows:

1. Introduza o **nome do grupo de inquilino de área de Trabalho Virtual do Windows** para o grupo de inquilino que contém o seu inquilino. Se não tiver um nome de grupo de inquilino específico planejado, deixá-lo como predefinição.
2. Introduza o **nome do inquilino de área de Trabalho Virtual do Windows** para o inquilino, criará este conjunto de anfitrião no.
3. Especifique o tipo de credenciais que pretende utilizar para efetuar a autenticação como o proprietário de RDS de inquilino de área de Trabalho Virtual do Windows. Se selecionou **principal de serviço**, também tem de fornecer a **ID de inquilino do Azure AD** associados com o principal de serviço.
4. Introduza as credenciais de qualquer um para a conta de administrador inquilino. Apenas os principais de serviço com uma credencial de palavra-passe são suportados.
5. Selecione **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Concluir a configuração e criar a máquina virtual

Para os últimos dois painéis:

1. Na **resumo** painel, reveja as informações de configuração. Se tiver de mudar algo, volte para o painel adequado e mudar antes de continuar. Se as informações tem o aspeto pretendidas, selecione **OK**.
2. Na **comprar** painel, reveja as informações adicionais sobre a compra do Azure Marketplace.
3. Selecione **criar** para implementar o conjunto de anfitrião.

Consoante o número de VMs que está a criar, este processo pode demorar 30 minutos ou mais para concluir.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Opcional) Atribuir utilizadores adicionais para o grupo de aplicações de ambiente de trabalho

Depois de concluída a oferta do Azure Marketplace, pode atribuir utilizadores adicionais para o grupo de aplicações de ambiente de trabalho antes de iniciar as áreas de trabalho de sessão completa nas suas máquinas virtuais de teste. Se já adicionou utilizadores padrão na oferta do Azure Marketplace e não quiser adicionar mais, pode ignorar esta secção.

Para atribuir utilizadores ao grupo de área de trabalho de aplicação, primeiro tem de abrir uma janela do PowerShell. Depois disso, terá de introduzir os seguintes dois cmdlets.

Execute o seguinte cmdlet para iniciar sessão no ambiente de área de Trabalho Virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Defina o contexto para o grupo de inquilino de área de Trabalho Virtual do Windows que especificou no Azure Marketplace oferece com o seguinte cmdlet. Se marcou o inquilino de área de Trabalho Virtual do Windows valor de grupo como o valor predefinido no Azure Marketplace da oferta, pode ignorar este passo.

```powershell
Set-RdsContext -TenantGroupName <tenantgroupname>
```

Depois de fazer essas duas coisas, pode adicionar utilizadores ao grupo de área de trabalho de aplicação com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

UPN do utilizador deve corresponder a identidade do utilizador no Azure Active Directory (por exemplo, user1@contoso.com). Se pretender adicionar múltiplos utilizadores, tem de executar este cmdlet para cada utilizador.

Depois de concluir estes passos, utilizadores adicionados ao grupo de aplicação de ambiente de trabalho podem iniciar sessão no ambiente de Trabalho Virtual do Windows com clientes de ambiente de trabalho remoto suportados e ver um recurso para uma área de trabalho de sessão.

Seguem-se os clientes suportados atuais:

- [Cliente de ambiente de trabalho remoto para Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Cliente de web de área de Trabalho Virtual do Windows](connect-web.md)

## <a name="next-steps"></a>Passos Seguintes

Agora que fez um host de agrupamento e os utilizadores atribuídos para acessar sua área de trabalho, também pode preencher o conjunto de anfitrião com RemoteApps. Para saber mais sobre como gerir aplicações na área de Trabalho Virtual do Windows, veja o tutorial de grupos de aplicações de gerir.

> [!div class="nextstepaction"]
> [Gerir o tutorial de grupos de aplicações](./manage-app-groups.md)
