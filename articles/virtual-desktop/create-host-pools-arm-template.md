---
title: Windows Virtual Desktop hospeda piscina Azure Resource Manager - Azure
description: Como criar uma piscina de anfitriões no Windows Virtual Desktop com um modelo de Gestor de Recursos Azure.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9ce6440989cbf962c474de2a6c90db4c485bf4a5
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "80292336"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Criar um conjunto de anfitriões com um modelo do Azure Resource Manager

As piscinas hospedeiras são uma coleção de uma ou mais máquinas virtuais idênticas dentro dos ambientes de inquilinos do Windows Virtual Desktop. Cada piscina de anfitriões pode conter um grupo de aplicações com o quais os utilizadores podem interagir como fariam num ambiente de trabalho físico.

Siga as instruções desta secção para criar um pool de anfitriões para um inquilino do Windows Virtual Desktop com um modelo de Gestor de Recursos Azure fornecido pela Microsoft. Este artigo irá dizer-lhe como criar um pool de anfitriões no Windows Virtual Desktop, criar um grupo de recursos com VMs numa subscrição Azure, juntar-se a esses VMs no domínio AD e registar os VMs com o Windows Virtual Desktop.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que precisa para executar o modelo de Gestor de Recursos Azure

Certifique-se de que sabe as seguintes coisas antes de executar o modelo do Gestor de Recursos Azure:

- Onde está a fonte da imagem que pretende utilizar. É da Galeria Azure ou é costume?
- O seu domínio junta-se a credenciais.
- As suas credenciais de Ambiente de Trabalho Virtual windows.

Ao criar um conjunto de anfitriões do Windows Virtual Desktop com o modelo Do Gestor de Recursos Azure, pode criar uma máquina virtual a partir da galeria Azure, uma imagem gerida ou uma imagem não gerida. Para saber mais sobre como criar imagens VM, consulte [Prepare um Windows VHD ou VHDX para fazer o upload para Azure](../virtual-machines/windows/prepare-for-upload-vhd-image.md) e [Criar uma imagem gerida de um VM generalizado em Azure](../virtual-machines/windows/capture-image-resource.md).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Executar o modelo do Gestor de Recursos Azure para fornecer uma nova piscina de anfitriões

Para começar, vá a [este URL GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Desdobrar o modelo para Azure

Se estiver a implantar uma subscrição da Enterprise, desloque-se e selecione **Deploy para Azure,** então salte para a frente preencha os parâmetros com base na sua fonte de imagem.

Se estiver a implementar uma subscrição do Cloud Solution Provider, siga estes passos para implementar para o Azure:

1. Desloque-se para baixo e clique à **direita, desloque-se para O Azure**e, em seguida, selecione **Copy Link Location**.
2. Abra um editor de texto como o Notepad e colhe o link lá.
3. Logo ahttps://portal.azure.com/seguir " e antes da hashtag (#) introduzir um sinal de at sign (@) seguido pelo nome de domínio do inquilino. Aqui está um exemplo do formato `https://portal.azure.com/@Contoso.onmicrosoft.com#create/`que deve usar: .
4. Inscreva-se no portal Azure como utilizador com permissões Admin/Contributor para a subscrição do Cloud Solution Provider.
5. Colhe o link que copiou para o editor de texto na barra de endereços.

Para obter orientações sobre quais os parâmetros que deve introduzir para o seu cenário, consulte o ficheiro Windows Virtual Desktop [Readme](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). O Readme é sempre atualizado com as últimas alterações.

## <a name="assign-users-to-the-desktop-application-group"></a>Atribuir utilizadores ao grupo de aplicações de ambiente de trabalho

Depois de o modelo gitHub Azure Resource Manager terminar, atribua o acesso ao utilizador antes de começar a testar os desktops da sessão completa nas suas máquinas virtuais.

Primeiro, [descarregue e importe o módulo Windows Virtual Desktop PowerShell](/powershell/windows-virtual-desktop/overview/) para utilizar na sua sessão PowerShell se ainda não o fez.

Para atribuir os utilizadores ao grupo de aplicações de ambiente de trabalho, abra uma janela PowerShell e execute este cmdlet para iniciar sessão no ambiente de ambiente de trabalho virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, adicione os utilizadores ao grupo de aplicações de ambiente de trabalho com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

A UPN do utilizador deve corresponder à identidade do utilizador no user1@contoso.comDiretório Ativo Azure (por exemplo, ). Se pretender adicionar vários utilizadores, tem de executar este cmdlet para cada utilizador.

Depois de ter concluído estes passos, os utilizadores adicionados ao grupo de aplicações para desktop podem iniciar sessão no Windows Virtual Desktop com clientes de ambiente de trabalho remoto suportados e ver um recurso para um ambiente de trabalho de sessão.

>[!IMPORTANT]
>Para ajudar a proteger o ambiente de ambiente de trabalho virtual do Windows em Azure, recomendamos que não abra a porta de entrada 3389 nos seus VMs. O Windows Virtual Desktop não necessita de uma porta de entrada aberta 3389 para os utilizadores acederem aos VMs do grupo anfitrião. Se tiver de abrir a porta 3389 para efeitos de resolução de problemas, recomendamos que utilize [o acesso VM just-in-time](../security-center/security-center-just-in-time.md).