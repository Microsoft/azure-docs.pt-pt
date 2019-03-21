---
title: Compreender as principais diferenças entre o Azure e o Azure Stack, ao utilizar a criação de aplicações e serviços | Documentos da Microsoft
description: O que precisa de saber para utilizar os serviços ou a criar aplicações para o Azure Stack.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: c81f551d-c13e-47d9-a5c2-eb1ea4806228
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.date: 12/27/2018
ms.author: sethm
ms.lastreviewed: 12/27/2018
ms.openlocfilehash: 158f22ec2ab07bfc2f893d4b0c55d862e0b19de0
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2019
ms.locfileid: "58106666"
---
# <a name="key-considerations-using-services-or-building-apps-for-azure-stack"></a>Considerações-chave: Utilizar os serviços ou criar aplicativos para o Azure Stack

Antes de utilizar os serviços ou criar aplicações para o Azure Stack, tem de compreender as diferenças entre o Azure Stack e o Azure. Este artigo identifica considerações-chave quando utiliza o Azure Stack como o ambiente de desenvolvimento de cloud híbrida.

## <a name="overview"></a>Descrição geral

O Azure Stack é uma plataforma de cloud híbrida que lhe permite utilizar serviços do Azure a partir do datacenter da sua empresa ou do seu fornecedor de serviços. Pode criar uma aplicação no Azure Stack e, em seguida, implementá-la para o Azure Stack, para o Azure ou para a cloud híbrida do Azure.

O operador do Azure Stack permitirá que sabe quais serviços estão disponíveis para utilização e como obter suporte. Eles oferecem estes serviços através da respetiva personalizados planos e ofertas.

O conteúdo técnico do Azure parte do princípio de que as aplicações estão a ser desenvolvidas para um serviço do Azure em vez do Azure Stack. Quando criar e implementar aplicações no Azure Stack, tem de compreender algumas diferenças fundamentais, como:

* O Azure Stack oferece uma **subconjunto** dos serviços e funcionalidades que estão disponíveis no Azure.
* O fornecedor de serviço ou da empresa pode escolher quais pretende oferecer os serviços. As opções disponíveis podem incluir aplicações ou serviços personalizados. Eles podem oferecer a sua própria documentação personalizada.
* Tem de utilizar o correto do Azure Stack os pontos finais (por exemplo, os URLs para o endereço de portal e o ponto de final do Azure Resource Manager).
* Tem de utilizar versões do PowerShell e a API que são suportadas pelo Azure Stack. A utilização de versões suportadas assegura que as suas aplicações trabalham no Azure Stack e o Azure.

## <a name="cheat-sheet-high-level-differences"></a>Referência rápida: Diferenças de alto nível

A tabela seguinte descreve as diferenças de alto nível entre o Azure Stack e o Azure. Ter essas diferenças em mente ao desenvolver para o Azure Stack ou utilizar serviços do Azure Stack.

*Aplica-se a: Integrados do Azure Stack, sistemas e o Kit de desenvolvimento do Azure Stack*

| Área | (Global) do Azure | Azure Stack |
| -------- | ------------- | ----------|
| Quem opera-lo? | Microsoft | O fornecedor de serviço ou de organização.|
| Quem contactar para obter suporte? | Microsoft | Para um sistema integrado, entre em contato com o operador do Azure Stack (no seu fornecedor de serviço ou de organização) para o suporte.<br><br>Para obter suporte do Azure Stack Development Kit, visite o [fóruns da Microsoft](https://social.msdn.microsoft.com/Forums/home?forum=azurestack). Como o kit de desenvolvimento é um ambiente de avaliação, não há nenhum oficial de suporte oferecido por meio da Microsoft dos clientes suportar (CSS).
| Serviços disponíveis | Ver a lista de [produtos do Azure](https://azure.microsoft.com/services/?b=17.04b). Serviços disponíveis variam consoante a região do Azure. | O Azure Stack suporta um subconjunto de serviços do Azure. Serviços reais irão variar consoante o que o fornecedor de serviço ou de organização opta por oferecer.
| O Azure Resource Manager endpoint * | https://management.azure.com | Para um sistema integrado do Azure Stack, utilize o ponto final que seu operador do Azure Stack fornecido.<br><br>Para obter o development kit, utilize: https://management.local.azurestack.external
| Portal URL * | [https://portal.azure.com](https://portal.azure.com) | Para um sistema integrado do Azure Stack, vá para o URL que o operador do Azure Stack fornecido.<br><br>Para obter o development kit, utilize: https://portal.local.azurestack.external
| Região | Pode selecionar qual a região que pretende implementar. | Para um sistema integrado do Azure Stack, utilize a região que está disponível no seu sistema.<br><br>Para o kit de desenvolvimento, região será sempre **local**.
| Grupos de recursos | Um grupo de recursos pode abranger regiões. | Para sistemas integrados e o kit de desenvolvimento, existe apenas uma região.
|Suportados espaços de nomes, tipos de recursos e as versões de API | A versão mais recente (ou versões anteriores que ainda não foram preteridas). | O Azure Stack oferece suporte a versões específicas. Consulte a [requisitos de versão](#version-requirements) seção deste artigo.
| | |

* Se for um operador do Azure Stack, veja [com o portal de administrador](../azure-stack-manage-portals.md) e [Noções básicas de administração](../azure-stack-manage-basics.md) para obter mais informações.

## <a name="helpful-tools-and-best-practices"></a>Ferramentas úteis e práticas recomendadas
 
 A Microsoft fornece ferramentas e orientações que o ajudam a desenvolver para o Azure Stack.

| Recomendação | Referências |
| -------- | ------------- |
| Instale as ferramentas corretas na sua estação de trabalho do desenvolvedor. | - [Instalar o PowerShell](azure-stack-powershell-install.md)<br>- [Transferir ferramentas](azure-stack-powershell-download.md)<br>- [Configurar o PowerShell](azure-stack-powershell-configure-user.md)<br>- [Instalar o Visual Studio](azure-stack-install-visual-studio.md) 
| Rever as informações sobre os seguintes itens:<br>-Considerações sobre os modelos do Resource Manager as do azure<br>-Como localizar modelos de início rápido<br>-Utilizar um módulo de política para o ajudar a utilizar o Azure para desenvolver para o Azure Stack | [Desenvolver para o Azure Stack](azure-stack-developer.md) | 
| Veja e siga as melhores práticas de modelos. | [Modelos de início rápido do Resource Manager](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#best-practices)
| | |

## <a name="version-requirements"></a>Requisitos da versão

O Azure Stack oferece suporte a versões específicas do Azure PowerShell e APIs de serviço do Azure. Utilize as versões suportadas para se certificar de que a sua aplicação pode implementar para ambas do Azure Stack e para o Azure.

Para certificar-se de que utiliza uma versão correta do Azure PowerShell, utilize [perfis de versão de API](azure-stack-version-profiles.md). Para determinar o perfil da versão de API mais recente que pode utilizar, descubra de que a compilação do Azure Stack que está a utilizar. Pode obter estas informações do seu administrador do Azure Stack.

> [!NOTE]
>  Se estiver usando o Kit de desenvolvimento do Azure Stack, e têm acesso administrativo, consulte a [determinar a versão atual](../azure-stack-updates.md#determine-the-current-version) secção para determinar a compilação do Azure Stack.

Para outras APIs, execute o seguinte comando do PowerShell para gerar os espaços de nomes, tipos de recursos e as versões de API que são suportadas na sua subscrição do Azure Stack. Tenha em atenção que pode ainda ser diferenças num nível de propriedade. Para este comando funcione, tem de ter já [instalados](azure-stack-powershell-install.md) e [configurado](azure-stack-powershell-configure-user.md) PowerShell para um ambiente do Azure Stack. Também tem de ter uma subscrição para uma oferta do Azure Stack.

```powershell
Get-AzureRmResourceProvider | Select ProviderNamespace -Expand ResourceTypes | Select * -Expand ApiVersions | `
Select ProviderNamespace, ResourceTypeName, @{Name="ApiVersion"; Expression={$_}} 
```

Exemplo de saída (truncado): ![Exemplo de saída do comando Get-AzureRmResourceProvider](media/azure-stack-considerations/image1.png)
 
## <a name="next-steps"></a>Passos Seguintes

Para obter informações mais detalhadas sobre as diferenças num nível de serviço, consulte:

* [Considerações para máquinas virtuais no Azure Stack](azure-stack-vm-considerations.md)
* [Considerações para armazenamento no Azure Stack](azure-stack-acs-differences.md)
* [Considerations for Azure Stack networking](azure-stack-network-differences.md) (Considerações sobre a rede do Azure Stack)
