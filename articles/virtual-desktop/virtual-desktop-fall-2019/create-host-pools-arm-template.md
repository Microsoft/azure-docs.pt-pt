---
title: Windows Virtual Desktop (clássico) anfitrião piscina Azure Resource Manager - Azure
description: Como criar uma piscina de anfitriões no Windows Virtual Desktop (clássico) com um modelo de Gestor de Recursos Azure.
author: Heidilohr
ms.topic: how-to
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b9999ae92840d79bb19464216c0f28504011b3f3
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88008326"
---
# <a name="create-a-host-pool-in-windows-virtual-desktop-classic-with-an-azure-resource-manager-template"></a>Crie uma piscina de anfitriões no Windows Virtual Desktop (clássico) com um modelo de Gestor de Recursos Azure

>[!IMPORTANT]
>Este conteúdo aplica-se ao Windows Virtual Desktop (clássico), que não suporta objetos de desktop virtual do Gestor de Recursos Azure Windows.

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

Siga as instruções desta secção para criar uma piscina de anfitrião para um inquilino virtual do Windows desktop com um modelo de Gestor de Recursos Azure fornecido pela Microsoft. Este artigo irá dizer-lhe como criar um pool de anfitriões no Windows Virtual Desktop, criar um grupo de recursos com VMs numa subscrição Azure, juntar esses VMs ao domínio AD e registar os VMs com o Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que você precisa para executar o modelo de Gestor de Recursos Azure

Certifique-se de que sabe as seguintes coisas antes de executar o modelo Azure Resource Manager:

- Onde está a origem da imagem que pretende usar. É da Galeria Azure ou é personalizado?
- O seu domínio junta-se a credenciais.
- As suas credenciais de ambiente de trabalho virtual do Windows.

Quando criar uma piscina de anfitriões virtual do Windows com o modelo Azure Resource Manager, pode criar uma máquina virtual a partir da galeria Azure, uma imagem gerida ou uma imagem não gerida. Para saber mais sobre como criar imagens VM, consulte [Prepare um VHD ou VHDX do Windows para fazer o upload para Azure](../../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Crie uma imagem gerida de um VM generalizado em Azure](../../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Executar o modelo de Gestor de Recursos Azure para o provisionamento de uma nova piscina de anfitriões

Para começar, vá a [este URL GitHub.](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool)

### <a name="deploy-the-template-to-azure"></a>Implementar o modelo no Azure

Se estiver a implementar uma subscrição da Enterprise, desloque-se para baixo e selecione **Implementar para Azure,** então preencha os parâmetros com base na sua fonte de imagem.

Se estiver a implementar uma subscrição do Cloud Solution Provider, siga estes passos para implementar para o Azure:

1. Desloque-se para baixo e clique no botão direito **Implementar para Azure** e, em seguida, selecione **Copy Link Location**.
2. Abra um editor de texto como o Bloco de Notas e cole o link lá.
3. Logo a seguir https://portal.azure.com/ " e antes da hashtag (#) insira um sinal (@) seguido pelo nome de domínio do inquilino. Aqui está um exemplo do formato que deve utilizar: `https://portal.azure.com/@Contoso.onmicrosoft.com#create/` .
4. Inscreva-se no portal Azure como utilizador com permissões Admin/Contribuinte para a subscrição do Cloud Solution Provider.
5. Cole o link que copiou para o editor de texto na barra de endereços.

Para obter orientações sobre quais os parâmetros que deve introduzir para o seu cenário, consulte o ficheiro De [Leitura](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)virtual do Windows . O Readme é sempre atualizado com as últimas alterações.

## <a name="assign-users-to-the-desktop-application-group"></a>Atribuir utilizadores ao grupo de aplicação desktop

Depois de o modelo GitHub Azure Resource Manager estar concluído, atribua o acesso ao utilizador antes de começar a testar os ambientes de trabalho completos nas suas máquinas virtuais.

Em primeiro lugar, [descarregue e importe o módulo PowerShell virtual do Windows Desktop](/powershell/windows-virtual-desktop/overview/) para utilizar na sessão PowerShell se ainda não o fez.

Para atribuir os utilizadores ao grupo de aplicações para desktop, abra uma janela PowerShell e execute este cmdlet para iniciar sessão no ambiente de ambiente de trabalho virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, adicione os utilizadores ao grupo de aplicação desktop com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A UPN do utilizador deve corresponder à identidade do utilizador no Diretório Ativo Azure (por exemplo, user1@contoso.com ). Se pretender adicionar vários utilizadores, tem de executar este cmdlet para cada utilizador.

Depois de ter concluído estes passos, os utilizadores adicionados ao grupo de aplicações para desktop podem iniciar sessão no Windows Virtual Desktop com clientes de Ambiente de Trabalho Remoto suportados e ver um recurso para um ambiente de trabalho de sessão.

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs da piscina anfitriã. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [acesso vm just-in-time](../../security-center/security-center-just-in-time.md).