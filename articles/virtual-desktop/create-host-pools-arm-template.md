---
title: Criar um conjunto de anfitrião com um modelo de Gestor de recursos do Azure (pré-visualização) - Azure
description: Como criar um conjunto de anfitrião na área de Trabalho Virtual do Windows com um modelo Azure Resource Manager.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 7f4849c19d56bb385e7ad3ce0aa95e16d5c53c23
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/21/2019
ms.locfileid: "58318468"
---
# <a name="create-a-host-pool-with-an-azure-resource-manager-template-preview"></a>Criar um conjunto de anfitrião com um modelo do Azure Resource Manager (pré-visualização)

Conjuntos de anfitrião são uma coleção de um ou mais máquinas virtuais idênticas em ambientes de inquilino (pré-visualização) de área de Trabalho Virtual do Windows. Cada conjunto de anfitrião pode conter um grupo de aplicações que os usuários podem interagir com como numa área de trabalho física.

Siga as instruções nesta secção para criar um conjunto de anfitrião para um inquilino de área de Trabalho Virtual do Windows com um modelo Azure Resource Manager fornecido pela Microsoft. Este artigo lhe dirá como criar um conjunto de anfitrião na área de Trabalho Virtual do Windows, crie um grupo de recursos com VMs numa subscrição do Azure, Junte-se a essas VMs ao domínio do AD e registe-se as VMs com a área de Trabalho Virtual do Windows.

## <a name="what-you-need-to-run-the-azure-resource-manager-template"></a>O que precisa para executar o modelo Azure Resource Manager

Certifique-se de que conhece os seguintes procedimentos antes de executar o modelo Azure Resource Manager:

- Em que é a origem da imagem que pretende utilizar. É proveniente de galeria do Azure ou é personalizado?
- As credenciais de associação de domínio.
- As credenciais de área de Trabalho Virtual do Windows.

Quando cria um conjunto de anfitrião de área de Trabalho Virtual do Windows com o modelo Azure Resource Manager, pode criar uma máquina virtual a partir da galeria do Azure, uma imagem gerida ou uma imagem não gerida. Para saber mais sobre como criar imagens de VM, veja [preparar um VHD do Windows ou o VHDX para carregar para o Azure](https://docs.microsoft.com/azure/virtual-machines/windows/prepare-for-upload-vhd-image) e [criar uma imagem gerida de uma VM generalizada no Azure](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource).

## <a name="run-the-azure-resource-manager-template-for-provisioning-a-new-host-pool"></a>Executar o modelo Azure Resource Manager para o aprovisionamento de um novo conjunto de anfitrião

Para começar, aceda a [este URL do GitHub](https://github.com/Azure/RDS-Templates/tree/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool).

### <a name="deploy-the-template-to-azure"></a>Implementar o modelo para o Azure

Se estiver a implementar numa subscrição Enterprise, desloque para baixo e selecione **implementar no Azure**, em seguida, ignorar ahead, preencha os parâmetros com base na sua origem de imagem.

Se estiver a implementar uma subscrição do fornecedor de soluções Cloud, siga estes passos para implementar no Azure:

1. Desloque para baixo e clique com botão direito **implementar no Azure**, em seguida, selecione **localização da ligação de cópia**.
2. Abra um editor de texto como o bloco de notas e cole a ligação existe.
3. Logo após "https://portal.azure.com/" e antes da hashtag (#) introduza um arroba (@) seguido do nome de domínio do inquilino. Eis um exemplo de formato, deve utilizar: https://portal.azure.com/@Contoso.onmicrosoft.com#create/.
4. Inicie sessão no portal do Azure como um utilizador com permissões de administrador/contribuinte à subscrição do fornecedor de soluções Cloud.
5. Cole a ligação que copiou para o editor de texto na barra de endereço.

Para obter orientações sobre quais parâmetros que deve introduzir para o seu cenário, consulte a área de Trabalho Virtual do Windows [ficheiro Leia-me](https://github.com/Azure/RDS-Templates/blob/master/wvd-templates/Create%20and%20provision%20WVD%20host%20pool/README.md). O ficheiro Leia-me sempre é atualizada com as alterações mais recentes.

## <a name="assign-users-to-the-desktop-application-group"></a>Atribuir utilizadores para o grupo de aplicações de ambiente de trabalho

Após a conclusão do modelo do GitHub do Azure Resource Manager, atribua o acesso de utilizador antes de iniciar as áreas de trabalho de sessão completa nas suas máquinas virtuais de teste.

Primeiro, [transferir e importar o módulo do Windows PowerShell de ambiente de Trabalho Virtual](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) a utilizar na sua sessão do PowerShell, se ainda não o fez.

Para atribuir aos utilizadores para o grupo de aplicações de ambiente de trabalho, abra uma janela do PowerShell e execute este cmdlet para iniciar sessão no ambiente de área de Trabalho Virtual do Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Em seguida, defina o contexto para o grupo de inquilino especificado no modelo do Azure Resource Manager com este cmdlet:

```powershell
Set-RdsContext -TenantGroupName <Tenant Group name>
```

Depois disso, adicione utilizadores ao grupo de área de trabalho de aplicação com este cmdlet:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

UPN do utilizador deve corresponder a identidade do utilizador no Azure Active Directory (por exemplo, user1@contoso.com). Se pretender adicionar múltiplos utilizadores, tem de executar este cmdlet para cada utilizador.

Depois de concluir estes passos, utilizadores adicionados ao grupo de aplicação de ambiente de trabalho podem iniciar sessão no ambiente de Trabalho Virtual do Windows com clientes de ambiente de trabalho remoto suportados e ver um recurso para uma área de trabalho de sessão.
