---
title: Migrar manualmente do Windows Virtual Desktop (clássico) - Azure
description: Como migrar manualmente do Windows Virtual Desktop (clássico) para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: fd292fa6b4f613e0a5f5a80e0cd87675f529baf5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "92516176"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Migrar manualmente do Windows Virtual Desktop (clássico)

O Windows Virtual Desktop (clássico) cria o seu ambiente de serviço com cmdlets PowerShell, APIs REST e objetos de serviço. Um "objeto" num ambiente de serviço virtual do Windows Desktop é uma coisa que o Windows Virtual Desktop cria. Os objetos de serviço incluem inquilinos, piscinas de anfitrião, grupos de aplicações e anfitriões de sessão.

No entanto, o Windows Virtual Desktop (clássico) não está integrado com o Azure. Sem integração do Azure, quaisquer objetos que crie não são geridos automaticamente pelo portal Azure porque não estão ligados à sua subscrição do Azure.

A recente grande atualização do Windows Virtual Desktop marca uma mudança no serviço para uma integração completa do Azure. Os objetos que cria no Windows Virtual Desktop são geridos automaticamente pelo portal Azure.

Neste artigo, vamos explicar por que deve considerar migrar para a versão mais recente do Windows Virtual Desktop. Depois disso, diremos como migrar manualmente do Windows Virtual Desktop (clássico) para a mais recente atualização do Windows Virtual Desktop.

## <a name="why-migrate"></a>Por que migrar?

As principais atualizações podem ser inconvenientes, especialmente as que tem de fazer manualmente. No entanto, existem algumas razões pelas quais não pode migrar automaticamente:

- Os objetos de serviço existentes feitos com a versão clássica não têm qualquer representação em Azure. O seu âmbito de aplicação não se estende para além do serviço de desktop virtual do Windows.
- Com a mais recente atualização, o ID da aplicação do serviço foi alterado para remover o consentimento das aplicações da forma como o fez para o Windows Virtual Desktop (clássico). Não será possível criar novos objetos Azure com o Windows Virtual Desktop a não ser que sejam autenticados com o novo ID da aplicação.

Apesar do incómodo, migrar para longe da versão clássica ainda é importante. Eis o que pode fazer depois de migrar:

- Gerir o Windows Virtual Desktop através do portal Azure.
- Atribua grupos de utilizadores Azure Ative (AD) a grupos de aplicação.
- Utilize a funcionalidade de Análise de Registo melhorada para resolver problemas na sua implementação.
- Utilize o controlo de acesso baseado em funções (Azure RBAC) para gerir o acesso administrativo.

## <a name="when-should-i-migrate"></a>Quando devo migrar?

Ao perguntar-se se deve migrar, deve também ter em conta a situação atual e futura da sua implantação.

Há alguns cenários em particular onde recomendamos que migrar manualmente:

- Você tem uma configuração de piscina de anfitrião de teste com um pequeno número de utilizadores.
- Você tem uma configuração de piscina de anfitrião de produção com um pequeno número de utilizadores, mas planeia eventualmente aumentar até centenas de utilizadores.
- Tem uma configuração simples que pode ser facilmente replicada. Por exemplo, se os seus VMs usarem uma imagem de galeria.

> [!IMPORTANT]
> Se estiver a usar uma configuração avançada que demorou muito tempo a estabilizar ou tem muitos utilizadores, não recomendamos migração manual.

## <a name="prepare-for-migration"></a>Prepare para a migração

Antes de começar, terá de se certificar de que o seu ambiente está pronto para migrar.

Eis o que precisa para iniciar o processo de migração:

- Uma subscrição Azure onde irá criar novos objetos de serviço Azure.
- Certifique-se de que está designado para as seguintes funções:
    
    - Contribuinte
    - Administrador de Acesso dos Utilizadores
    
    A função Contribuinte permite criar objetos Azure na sua subscrição, e a função de Administrador de Acesso ao Utilizador permite-lhe atribuir utilizadores a grupos de aplicações.

## <a name="how-to-migrate-manually"></a>Como migrar manualmente

Agora que se preparou para o processo de migração, está na hora de migrar.

Para migrar manualmente do Windows Virtual Desktop (clássico) para o Windows Virtual Desktop:

1. Siga as instruções em [Criar uma piscina de anfitrião com o portal Azure](create-host-pools-azure-marketplace.md) para criar todos os objetos de alto nível com o portal Azure.
2. Se pretender trazer as máquinas virtuais que já está a utilizar, siga as instruções no [Registo das máquinas virtuais para o conjunto de anfitriões virtual do Windows Para](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) registá-las manualmente na nova piscina de anfitriões que criou no passo 1.
3. Crie novos grupos de aplicações RemoteApp.
4. Publique utilizadores ou grupos de utilizadores para os novos grupos de aplicações do Desktop e RemoteApp.
5. Atualize a sua política de Acesso Condicional para permitir que os novos objetos seguindo as instruções na [configuração da autenticação de vários fatores](set-up-mfa.md).

Para evitar tempo de inatividade, deve primeiro registar os anfitriões da sessão existentes nas piscinas hospedeiras integradas do Azure Resource Manager em pequenos grupos de cada vez. Depois disso, leve lentamente os seus utilizadores para os novos grupos de aplicações integrados do Azure Resource Manager.

## <a name="next-steps"></a>Passos seguintes

Assim que migrar, conheça como funciona o Windows Virtual Desktop, verificando os [nossos tutoriais.](create-host-pools-azure-marketplace.md) Saiba mais sobre as capacidades avançadas de gestão na [Expandir uma piscina de anfitriões existente](expand-existing-host-pool.md) e [personalize propriedades RDP.](customize-rdp-properties.md)

Para saber mais sobre objetos de serviço, consulte o [ambiente de ambiente de trabalho virtual do Windows.](environment-setup.md)
