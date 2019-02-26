---
title: Com o portal de administração no Azure Stack | Documentos da Microsoft
description: Como um operador do Azure Stack, saiba como utilizar o portal de administração.
services: azure-stack
documentationcenter: ''
author: jeffgilb
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.custom: mvc
ms.date: 02/25/2019
ms.author: jeffgilb
ms.reviewer: efemmano
ms.lastreviewed: 02/25/2019
ms.openlocfilehash: 8662cfde881a90ce8f7fc6cceaa576d3b971d9d6
ms.sourcegitcommit: 1516779f1baffaedcd24c674ccddd3e95de844de
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/26/2019
ms.locfileid: "56817937"
---
# <a name="quickstart-use-the-azure-stack-administration-portal"></a>Início rápido: utilizar o portal de administração do Azure Stack

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

Existem dois portais no Azure Stack; o portal de administração e o portal de utilizador (por vezes referido como o *inquilino* portal.) Como um operador de pilha do Azure, vai utilizar o portal de administração para operações do Azure Stack e o gerenciamento diários.

## <a name="access-the-administrator-portal"></a>Aceder ao portal de administrador

Para aceder ao portal de administrador, navegue para o URL do portal e a de início de sessão com as credenciais de um operador do Azure Stack. Para um sistema integrado, o portal que URL varia com base no nome da região e externo nome de domínio completamente qualificado (FQDN) da sua implementação do Azure Stack. Portal de administração do URL é sempre o mesmo para implementações do Azure Stack Development Kit (ASDK). 

| Ambiente | URL do Portal de administrador |   
| -- | -- | 
| ASDK| https://adminportal.local.azurestack.external  |
| Sistemas integrados | https://adminportal.&lt;*region*&gt;.&lt;*FQDN*&gt; | 
| | |

> [!TIP]
> Para um ambiente de ASDK, precisa primeiro certifique-se de que o utilizador pode [ligar para o anfitrião do kit de desenvolvimento](azure-stack-connect-azure-stack.md) por meio de conexão de área de trabalho remoto ou uma rede privada virtual (VPN).

 ![O portal de administração](media/azure-stack-manage-portals/admin-portal.png)

O fuso horário predefinido para todas as implementações do Azure Stack está definido para Hora Universal Coordenada (UTC). 

No portal do administrador, pode fazer coisas como:

* [Registar o Azure Stack com o Azure](azure-stack-registration.md)
* [Povoar o marketplace](azure-stack-download-azure-marketplace-item.md)
* [Criar planos, ofertas e subscrições para os utilizadores](azure-stack-plan-offer-quota-overview.md)
* [Monitorizar alertas e estado de funcionamento](azure-stack-monitor-health.md)
* [Gerir atualizações do Azure Stack](azure-stack-updates.md)

O **tutorial de início rápido** mosaico fornece ligações para documentação online para as tarefas mais comuns.

Embora um operador pode criar recursos, tais como máquinas virtuais, redes virtuais e contas de armazenamento no portal de administração, deve [inicie sessão no portal de utilizador](user/azure-stack-use-portal.md) para criar e testar recursos.

>[!NOTE]
>O **criar uma máquina virtual** tem de ligação no mosaico do tutorial de início rápido-o a criar uma máquina virtual no portal de administração, mas apenas destina-se para validar que o Azure Stack foi implementado com êxito.

## <a name="understand-subscription-behavior"></a>Compreender o comportamento de subscrição

Existem três subscrições criadas por predefinição no portal de administração do; consumo de provedor padrão e de medição. Como um operador, será mais utiliza a *subscrição do fornecedor predefinido*. Não é possível adicionar quaisquer outras subscrições e utilizá-los no portal de administração. 

Outras subscrições são criadas por utilizadores no portal de utilizador com base nos planos e ofertas que cria para eles. No entanto, o portal de utilizador não fornece acesso a qualquer um dos recursos administrativos e operacionais do portal de administração do.

Os portais de administração e de utilizador são apoiados por instâncias separadas do Gestor de recursos do Azure. Devido a essa separação do Azure Resource Manager, as subscrições não ultrapassam portais. Por exemplo, se, enquanto um operador de pilha do Azure, iniciar sessão no portal de utilizador, não é possível aceder a *subscrição do fornecedor predefinido*. Embora não tiver acesso a quaisquer funções administrativas, pode criar subscrições para mesmo de ofertas de públicas disponíveis. Enquanto tem sessão iniciada portal de utilizador são considerados um utilizador do inquilino.

  >[!NOTE]
  >Num ambiente de ASDK, se um utilizador pertencer ao mesmo diretório do inquilino o operador de pilha do Azure, eles não são impedidos de iniciar sessão no portal de administração. No entanto, não é possível aceder a qualquer uma das funções administrativas ou adicionar subscrições para aceder a oferece que estejam disponíveis no portal de utilizador.

## <a name="administration-portal-tips"></a>Dicas de portais de administração

### <a name="customize-the-dashboard"></a>Personalizar o dashboard

O dashboard contém um conjunto de mosaicos predefinidos. Pode selecionar **editar dashboard** para modificar o dashboard predefinido ou selecione **novo dashboard** para adicionar um dashboard personalizado. Pode adicionar facilmente mosaicos num dashboard. Por exemplo, pode selecionar **+ criar um recurso**, clique com botão direito **ofertas + planos**e, em seguida, selecione **afixar ao dashboard**.

Às vezes, poderá ver um dashboard em branco no portal. Para recuperar o dashboard, clique em **editar Dashboard**e, em seguida, clique com botão direito e selecione **repor para o estado predefinido**.

### <a name="quick-access-to-online-documentation"></a>Acesso rápido a documentação online

Para acessar a documentação de operador do Azure Stack, utilize a ajuda e suporte de ícone (ponto de interrogação) no canto superior direito do portal do administrador. Mover o cursor para o ícone e, em seguida, selecione **ajuda + suporte**.

### <a name="quick-access-to-help-and-support"></a>Acesso rápido a ajuda e suporte

Se selecionar o ícone de ajuda e suporte (ponto de interrogação) no canto superior direito do portal do administrador e, em seguida, selecione **novo pedido de suporte**, acontece por um dos seguintes resultados:

- Se estiver a utilizar um sistema integrado, esta ação abre um site onde é possível diretamente abrir um pedido de suporte com o Microsoft ao cliente suportar (CSS). Consulte a [onde pode obter suporte](azure-stack-manage-basics.md#where-to-get-support) compreender quando deve passar por meio de suporte da Microsoft ou o suporte de fornecedor de hardware do fabricante de equipamento original (OEM).
- Se estiver a utilizar o ASDK, esta ação abre o [site de fóruns do Azure Stack](https://social.msdn.microsoft.com/Forums/home?forum=AzureStack) diretamente. Esses fóruns são monitorados regularmente. Como o ASDK é um ambiente de avaliação, não há oficial suporte oferecido por meio da CSS de Microsoft.

### <a name="quick-access-to-the-azure-roadmap"></a>Acesso rápido para o mapa do Azure

Se selecionou **ajuda e suporte** (o ponto de interrogação) no canto superior direito da administração portal e, em seguida, selecione **mapa do Azure**, um novo separador do browser abre-se e leva-o para o mapa do Azure. Ao escrever **do Azure Stack** no **produtos** caixa de pesquisa, pode ver todas as atualizações de mapa do Azure Stack.

## <a name="next-steps"></a>Passos Seguintes

[Registar o Azure Stack com o Azure](azure-stack-registration.md) e preencher a [marketplace do Azure Stack](azure-stack-marketplace.md) com itens para proporcionar aos usuários. 
