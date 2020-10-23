---
title: Windows Virtual Desktop FAQ - Azure
description: Perguntas frequentes e boas práticas para o Windows Virtual Desktop.
author: Heidilohr
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: b915445b74e202f010c5505cc240b6f36e9da77c
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/16/2020
ms.locfileid: "92108512"
---
# <a name="windows-virtual-desktop-faq"></a>FAQ do Windows Virtual Desktop

Este artigo responde a perguntas frequentes e explica as melhores práticas para o Windows Virtual Desktop.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Quais são as permissões mínimas de administração que preciso para gerir objetos?

Se pretender criar piscinas de anfitriões e outros objetos, deve ser-lhe atribuída a função Contribuinte na subscrição ou grupo de recursos com quem está a trabalhar.

Deve ser-lhe atribuída a função de Administrador de Acesso ao Utilizador num grupo de aplicações para publicar grupos de aplicações a utilizadores ou grupos de utilizadores.

Para restringir um administrador apenas para gerir as sessões de utilizador, como o envio de mensagens para os utilizadores, a assinatura de utilizadores, e assim por diante, pode criar funções personalizadas. Por exemplo:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>O suporte virtual do Windows desktop divide os modelos Azure Ative Directory?

Quando um utilizador é atribuído a um grupo de aplicações, o serviço faz uma simples atribuição de funções Azure. Como resultado, o Azure Ative Directory (AD) do utilizador e o AD Azure do grupo de aplicações devem estar no mesmo local. Todos os objetos de serviço, tais como piscinas de anfitriões, grupos de aplicações e espaços de trabalho, também devem estar no mesmo AD Azure que o utilizador.

Pode criar máquinas virtuais (VMs) num AD Azure diferente, desde que sincronize o Ative Directory com o AD Azure do utilizador na mesma rede virtual (VNET).

## <a name="what-are-location-restrictions"></a>O que são restrições de localização?

Todos os recursos de serviço têm uma localização associada a eles. A localização de uma piscina anfitriã determina em que geografia os metadados de serviço do conjunto anfitrião estão armazenados. Um grupo de aplicações não pode existir sem uma piscina de anfitriões. Se adicionar aplicações a um grupo de aplicações RemoteApp, também precisará de um anfitrião de sessão para determinar as aplicações do menu inicial. Para qualquer ação de grupo de aplicações, você também precisará de um acesso de dados relacionado no pool anfitrião. Para garantir que os dados não estão a ser transferidos entre vários locais, a localização do grupo de aplicações deve ser a mesma que a do pool anfitrião.

Os espaços de trabalho também devem estar no mesmo local que os seus grupos de aplicações. Sempre que o espaço de trabalho atualiza, o grupo de aplicações relacionados atualiza juntamente com o mesmo. Tal como acontece com os grupos de aplicações, o serviço requer que todos os espaços de trabalho estejam associados a grupos de aplicações criados no mesmo local.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Como expandir as propriedades de um objeto em PowerShell?

Quando executar um cmdlet PowerShell, só se vê o nome e a localização do recurso.

Por exemplo:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Para ver todas as propriedades de um recurso, adicione `format-list` ou ao fim do `fl` cmdlet.

Por exemplo:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Para ver propriedades específicas, adicione os nomes de propriedade específicos depois `format-list` ou `fl` .

Por exemplo:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>O Windows Virtual Desktop suporta utilizadores convidados?

O Windows Virtual Desktop não suporta contas de utilizadores de Azure AD. Por exemplo, digamos que um grupo de utilizadores convidados tem licenças Microsoft 365 E3 Per-user, Windows E3 Per-user ou WIN VDA na sua própria empresa, mas são utilizadores convidados em AD de outra empresa. A outra empresa iria gerir os objetos de utilizador dos utilizadores convidados tanto em AD AZure como em Ative Directory, como contas locais.

Não podes usar as tuas licenças para benefício de terceiros. Além disso, o Windows Virtual Desktop não suporta atualmente a Conta Microsoft (MSA).

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Por que não vejo o endereço IP do cliente na tabela WVDConnections?

Atualmente não temos uma forma confiável de recolher os endereços IP do cliente web, por isso não incluímos esse valor na tabela.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Como é que o Windows Virtual Desktop lida com cópias de segurança?

Existem várias opções no Azure para o manuseamento de backup. Pode utilizar backup Azure, Recuperação de Sítios e instantâneos.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>O Windows Virtual Desktop suporta aplicações de colaboração de terceiros?

O Windows Virtual Desktop está atualmente otimizado para equipas. A Microsoft não suporta atualmente aplicações de colaboração de terceiros como o Zoom. As organizações de terceiros são responsáveis por dar orientações de compatibilidade aos seus clientes. O Windows Virtual Desktop também não suporta o Skype para o Negócios.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Posso mudar de piscina para piscina pessoal de anfitrião?

Uma vez que você cria uma piscina de anfitrião, você não pode mudar o seu tipo. No entanto, você pode mover quaisquer VMs que você registre para uma piscina de anfitrião para um tipo diferente de piscina de anfitrião.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Qual é o maior perfil que o FSLogix consegue suportar?

As limitações ou quotas em FSLogix dependem do tecido de armazenamento utilizado para armazenar ficheiros VHD(X) de perfil do utilizador.

A tabela seguinte dá um exemplo de como qualquer recurso que um perfil FSLogix necessita para suportar cada utilizador. Os requisitos podem variar muito dependendo do utilizador, aplicações e atividade em cada perfil.

| Recurso | Requisito |
|---|---|
| Estado estável IOPS | 10 |
| Iniciar sedução/assinatura IOPS | 50 |

O exemplo nesta tabela é de um único utilizador, mas pode ser usado para estimar os requisitos para o número total de utilizadores no seu ambiente. Por exemplo, precisaria de cerca de 1.000 IOPS para 100 utilizadores e cerca de 5.000 IOPS durante a sindução e sedução.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Existe um limite de escala para piscinas hospedeiras criadas no portal Azure?

Estes fatores podem afetar o limite de escala para as piscinas hospedeiras:

- O modelo Azure está limitado a 800 objetos. Para saber mais, consulte [os limites de subscrição e serviço da Azure, quotas e constrangimentos.](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits) Cada VM também cria cerca de seis objetos, o que significa que pode criar cerca de 132 VMs cada vez que executar o modelo.

- Existem restrições sobre quantos núcleos pode criar por região e por subscrição. Por exemplo, se tiver uma subscrição do Enterprise Agreement, pode criar 350 núcleos. Terá de dividir 350 pelo número padrão de núcleos por VM ou pelo seu próprio limite de núcleo para determinar quantos VMs pode criar cada vez que executar o modelo. Saiba mais nos [limites das Máquinas Virtuais - Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- O nome do prefixo VM e o número de VMs são inferiores a 15 caracteres. Para saber mais, consulte [as regras de nomeação e as restrições para os recursos da Azure.](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute)

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Posso gerir ambientes de desktop virtual do Windows com o Azure Lighthouse?

O Azure Lighthouse não suporta totalmente a gestão de ambientes de ambientes de ambiente de trabalho virtual do Windows. Uma vez que o Farol não suporta atualmente a gestão de utilizadores de inquilinos cross-Azure AD, os clientes do Farol ainda precisam de assinar no AD Azure que os clientes usam para gerir os utilizadores.

Também não pode utilizar subscrições de caixa de areia CSP com o serviço De Ambiente de Trabalho Virtual do Windows. Para saber mais, consulte [a conta de caixa de areia Integração.](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account)

Finalmente, se for habilitado o fornecedor de recursos a partir da conta do proprietário da CSP, as contas de clientes da CSP não poderão modificar o fornecedor de recursos.
