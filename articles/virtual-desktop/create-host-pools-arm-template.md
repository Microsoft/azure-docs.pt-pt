---
title: Criar um pool de hosts de área de trabalho virtual do Windows com um modelo de Azure Resource Manager – Azure
description: Como criar um pool de hosts na área de trabalho virtual do Windows com um modelo de Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 08/29/2019
ms.author: helohr
ms.openlocfilehash: b96ecad93c481a762b81a7888940850ed3a73e5f
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/30/2019
ms.locfileid: "71679616"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template"></a>Criar um conjunto de anfitriões com um modelo do Azure Resource Manager

Pools de hosts são uma coleção de uma ou mais máquinas virtuais idênticas em ambientes de locatário da área de trabalho virtual do Windows. Cada pool de hosts pode conter um grupo de aplicativos com os quais os usuários podem interagir como fariam em uma área de trabalho física.

Siga as instruções desta seção para criar um pool de hosts para um locatário de área de trabalho virtual do Windows com um modelo de Azure Resource Manager fornecido pela Microsoft. Este artigo informará como criar um pool de hosts na área de trabalho virtual do Windows, criar um grupo de recursos com VMs em uma assinatura do Azure, ingressar essas VMs no domínio do AD e registrar as VMs com a área de trabalho virtual do Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que você precisa para executar o modelo de Azure Resource Manager

Verifique se você conhece as ações a seguir antes de executar o modelo de Azure Resource Manager:

- Onde a origem da imagem que você deseja usar é. Ele é da galeria do Azure ou é personalizado?
- Suas credenciais de ingresso no domínio.
- Suas credenciais de área de trabalho virtual do Windows.

Ao criar um pool de hosts de área de trabalho virtual do Windows com o modelo de Azure Resource Manager, você pode criar uma máquina virtual na galeria do Azure, em uma imagem gerenciada ou em uma imagem não gerenciada. Para saber mais sobre como criar imagens de VM, consulte [preparar um VHD do Windows ou VHDX para carregar no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) e [criar uma imagem gerenciada de uma VM generalizada no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Executar o modelo de Azure Resource Manager para provisionar um novo pool de hosts

Para começar, vá para [esta URL do GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Implantar o modelo no Azure

Se você estiver implantando em uma assinatura do Enterprise, role para baixo e selecione **implantar no Azure**e, em seguida, ignorar antecipadamente os parâmetros com base na origem da imagem.

Se você estiver implantando em uma assinatura do provedor de soluções na nuvem, siga estas etapas para implantar no Azure:

1. Role para baixo e clique com o botão direito do mouse em **implantar no Azure**e selecione **Copiar local do link**.
2. Abra um editor de texto como o bloco de notas e cole o link lá.
3. Logo após "https://portal.azure.com/" e antes da hashtag (#), insira um sinal de arroba (@) seguido pelo nome de domínio do locatário. Veja um exemplo do formato que você deve usar: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Entre no portal do Azure como um usuário com permissões de administrador/colaborador para a assinatura do provedor de soluções na nuvem.
5. Cole o link que você copiou para o editor de texto na barra de endereços.

Para obter diretrizes sobre quais parâmetros devem ser inseridos para seu cenário, consulte o [arquivo Leiame](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md)da área de trabalho virtual do Windows. O Leiame sempre é atualizado com as alterações mais recentes.

## <a name="assign-users-to-the-desktop-application-group"></a>Atribuir usuários ao grupo de aplicativos da área de trabalho

Após a conclusão do modelo de Azure Resource Manager do GitHub, atribua o acesso do usuário antes de começar a testar as áreas de trabalho de sessão completas em suas máquinas virtuais.

Primeiro, [Baixe e importe o módulo do PowerShell de área de trabalho virtual do Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) para usar em sua sessão do PowerShell, se ainda não tiver feito isso.

Para atribuir usuários ao grupo de aplicativos da área de trabalho, abra uma janela do PowerShell e execute este cmdlet para entrar no ambiente de área de trabalho virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Depois disso, adicione usuários ao grupo de aplicativos da área de trabalho com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

O UPN do usuário deve corresponder à identidade do usuário em Azure Active Directory (por exemplo, user1@contoso.com). Se você quiser adicionar vários usuários, deverá executar esse cmdlet para cada usuário.

Depois de concluir essas etapas, os usuários adicionados ao grupo de aplicativos da área de trabalho podem entrar na área de trabalho virtual do Windows com clientes Área de Trabalho Remota com suporte e ver um recurso para uma área de trabalho de sessão.

>[!IMPORTANT]
>Para ajudar a proteger seu ambiente de área de trabalho virtual do Windows no Azure, recomendamos que você não abra a porta de entrada 3389 em suas VMs. A área de trabalho virtual do Windows não requer uma porta de entrada aberta 3389 para que os usuários acessem as VMs do pool de hosts. Se você precisar abrir a porta 3389 para fins de solução de problemas, recomendamos o uso [do acesso à VM just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).