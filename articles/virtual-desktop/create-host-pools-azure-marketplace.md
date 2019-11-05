---
title: Criar um pool de hosts de área de trabalho virtual do Windows usando o Azure Marketplace – Azure
description: Como criar um pool de hosts de área de trabalho virtual do Windows usando o Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: 76cb2e0378cc7a005232304174c1de887adfe387
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: HT
ms.contentlocale: pt-PT
ms.lasthandoff: 11/04/2019
ms.locfileid: "73482230"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Tutorial: criar um pool de hosts usando o Azure Marketplace

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas em ambientes de locatário da área de trabalho virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativos com os quais os usuários podem interagir como fariam em uma área de trabalho física.

Este tutorial descreve como criar um pool de hosts em um locatário de área de trabalho virtual do Windows usando uma oferta Microsoft Azure Marketplace. As tarefas incluem:

> [!div class="checklist"]
> * Crie um pool de hosts na área de trabalho virtual do Windows.
> * Crie um grupo de recursos com VMs em uma assinatura do Azure.
> * Ingresse as VMs no domínio Active Directory.
> * Registre as VMs com a área de trabalho virtual do Windows.

Antes de começar, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso. Depois disso, execute o seguinte cmdlet para entrar em sua conta:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Iniciar sessão no Azure

Iniciar sessão no [portal do Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Executar a oferta do Azure Marketplace para provisionar um novo pool de hosts

Para executar a oferta do Azure Marketplace para provisionar um novo pool de hosts:

1. No menu portal do Azure ou na **Home** Page do, selecione **criar um recurso**.
2. Insira **área de trabalho virtual do Windows** na janela pesquisa do Marketplace.
3. Selecione **área de trabalho virtual do Windows-provisionar um pool de hosts**e, em seguida, selecione **criar**.

Depois disso, siga as instruções na próxima seção para inserir as informações para as folhas apropriadas.

### <a name="basics"></a>Noções básicas

Veja o que você faz para a folha **noções básicas** :

1. Insira um nome para o pool de hosts que é exclusivo no locatário da área de trabalho virtual do Windows.
2. Selecione a opção apropriada para uma área de trabalho pessoal. Se você selecionar **Sim**, cada usuário que se conecta a esse pool de hosts será permanentemente atribuído a uma máquina virtual.
3. Insira uma lista separada por vírgulas de usuários que podem entrar nos clientes da área de trabalho virtual do Windows e acessar uma área de trabalho após a conclusão da oferta do Azure Marketplace. Por exemplo, se você quiser atribuir user1@contoso.com e user2@contoso.com acesso, insira "user1@contoso.comuser2@contoso.com".
4. Selecione **criar novo** e forneça um nome para o novo grupo de recursos.
5. Para **local**, selecione o mesmo local que a rede virtual que tem conectividade com o servidor de Active Directory.
6. Selecione **OK**.

>[!IMPORTANT]
>Se você estiver usando uma solução Azure Active Directory Domain Services e Azure Active Directory pura, certifique-se de implantar o pool de hosts na mesma região que o Azure Active Directory Domain Services para evitar erros de associação de domínio e de credenciais.

### <a name="configure-virtual-machines"></a>Configurar máquinas virtuais

Para a folha **configurar máquinas virtuais** :

1. Aceite os padrões ou personalize o número e o tamanho das VMs.
2. Insira um prefixo para os nomes das máquinas virtuais. Por exemplo, se você inserir o nome "prefixo", as máquinas virtuais serão chamadas de "prefix-0", "prefixo-1" e assim por diante.
3. Selecione **OK**.

### <a name="virtual-machine-settings"></a>Definições da máquina virtual

Para a folha **configurações de máquina virtual** :

>[!NOTE]
> Se você estiver ingressando suas VMs em um ambiente Azure Active Directory Domain Services (AD DS do Azure), verifique se o usuário ingressar no domínio também é membro do [grupo de administradores de DC do AAD](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).

1. Para **origem da imagem**, selecione a origem e insira as informações apropriadas sobre como encontrá-la e como armazená-la. Se você optar por não usar discos gerenciados, selecione a conta de armazenamento que contém o arquivo. vhd.
2. Insira o nome principal do usuário e a senha para a conta de domínio que ingressará as VMs no domínio Active Directory. Esse mesmo nome de usuário e senha serão criados nas máquinas virtuais como uma conta local. Você pode redefinir essas contas locais mais tarde.
3. Selecione a rede virtual que tem conectividade com o servidor de Active Directory e, em seguida, escolha uma sub-rede para hospedar as máquinas virtuais.
4. Selecione **OK**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informações de locatário da área de trabalho virtual do Windows

Para a folha **informações de locatário da área de trabalho virtual do Windows** :

1. Para **nome do grupo de locatários da área de trabalho virtual do Windows**, insira o nome do grupo de locatários que contém seu locatário. Deixe como o padrão, a menos que você tenha fornecido um nome de grupo de locatários específico.
2. Para **nome do locatário da área de trabalho virtual do Windows**, insira o nome do locatário no qual você criará esse pool de hosts.
3. Especifique o tipo de credenciais que você deseja usar para autenticar como o proprietário RDS do locatário da área de trabalho virtual do Windows. Se você concluiu o [tutorial criar entidades de serviço e atribuições de função com o PowerShell](./create-service-principal-role-powershell.md), selecione **entidade de serviço**. Quando a **ID de locatário do Azure ad** for exibida, insira a ID para a instância de Azure Active Directory que contém a entidade de serviço.
4. Insira as credenciais para a conta de administrador de locatário. Há suporte apenas para entidades de serviço com uma credencial de senha.
5. Selecione **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Concluir a instalação e criar a máquina virtual

Para as duas últimas folhas:

1. Na folha **Resumo** , examine as informações de instalação. Se você precisar alterar algo, volte para a folha apropriada e faça a alteração antes de continuar. Se as informações estiverem corretas, selecione **OK**.
2. Na folha **comprar** , examine as informações adicionais sobre sua compra no Azure Marketplace.
3. Selecione **criar** para implantar o pool de hosts.

Dependendo de quantas VMs você está criando, esse processo pode levar 30 minutos ou mais para ser concluído.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Adicional Atribuir usuários adicionais ao grupo de aplicativos da área de trabalho

Depois que a oferta do Azure Marketplace for concluída, você poderá atribuir mais usuários ao grupo de aplicativos da área de trabalho antes de começar a testar as áreas de trabalho de sessão completas em suas máquinas virtuais. Se você já tiver adicionado usuários padrão na oferta do Azure Marketplace e não quiser adicionar mais, poderá ignorar esta seção.

Para atribuir usuários ao grupo de aplicativos da área de trabalho, você deve primeiro abrir uma janela do PowerShell. Depois disso, você precisará inserir os dois cmdlets a seguir.

Execute o seguinte cmdlet para entrar no ambiente de área de trabalho virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Adicione usuários ao grupo de aplicativos da área de trabalho usando este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

O UPN do usuário deve corresponder à identidade do usuário em Azure Active Directory (por exemplo, user1@contoso.com). Se você quiser adicionar vários usuários, deverá executar esse cmdlet para cada usuário.

Depois de concluir essas etapas, os usuários adicionados ao grupo de aplicativos da área de trabalho podem entrar na área de trabalho virtual do Windows com clientes Área de Trabalho Remota com suporte e ver um recurso para uma área de trabalho de sessão.

Estes são os clientes atuais com suporte:

- [Área de Trabalho Remota Client para Windows 7 e Windows 10](connect-windows-7-and-10.md)
- [Cliente Web da área de trabalho virtual do Windows](connect-web.md)

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente de área de trabalho virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 em suas VMs. A área de trabalho virtual do Windows não requer uma porta de entrada aberta 3389 para que os usuários acessem as VMs do pool de hosts. Se você precisar abrir a porta 3389 para fins de solução de problemas, recomendamos o uso [do acesso à VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Passos seguintes

Agora que você criou um pool de hosts e atribuiu usuários para acessar sua área de trabalho, você pode preencher o pool de hosts com programas RemoteApp. Para saber mais sobre como gerenciar aplicativos na área de trabalho virtual do Windows, consulte este tutorial:

> [!div class="nextstepaction"]
> [Tutorial para gerenciar grupos de aplicativos](./manage-app-groups.md)
