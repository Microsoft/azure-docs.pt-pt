---
title: Habilitar Enterprise State Roaming no Azure Active Directory | Microsoft Docs
description: Perguntas frequentes sobre Enterprise State Roaming configurações em dispositivos Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 06/28/2019
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: f0f4a260bb52fb10147f6d6b9e74aa5cd4fd0e1a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2019
ms.locfileid: "68562153"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Ativar o Roaming de Estado Empresarial no Azure Active Directory
Enterprise State Roaming está disponível para qualquer organização com uma licença de Azure AD Premium ou Enterprise Mobility + Security (EMS). Para obter mais informações sobre como obter uma assinatura do Azure AD, consulte a [página de produto do Azure ad](https://azure.microsoft.com/services/active-directory).

Quando você habilita Enterprise State Roaming, sua organização recebe automaticamente uma licença gratuita de uso limitado para o Azure Rights Management Protection da proteção de informações do Azure. Essa assinatura gratuita é limitada a criptografar e descriptografar as configurações da empresa e os dados de aplicativo sincronizados pelo Enterprise State Roaming. Você deve ter [uma assinatura paga](https://azure.microsoft.com/pricing/details/information-protection/) para usar os recursos completos do serviço de Rights Management do Azure.

## <a name="to-enable-enterprise-state-roaming"></a>Para habilitar Enterprise State Roaming

1. Entre no [centro de administração do Azure ad](https://aad.portal.azure.com/).
1. Selecione **Azure Active Directory** &gt; dispositivos EnterpriseStateroaming&gt; .
1. Selecionar **os usuários podem sincronizar configurações e dados de aplicativo entre dispositivos**. Para obter mais informações, consulte [como definir as configurações do dispositivo](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
   ![imagem da configuração do dispositivo rotulada os usuários podem sincronizar configurações e dados de aplicativo entre dispositivos](./media/enterprise-state-roaming-enable/device-settings.png)
  
Para que um dispositivo Windows 10 Use o serviço de Enterprise State Roaming, o dispositivo deve se autenticar usando uma identidade do Azure AD. Para dispositivos que ingressaram no Azure AD, a identidade de entrada principal do usuário é a identidade do Azure AD, portanto, nenhuma configuração adicional é necessária. Para dispositivos que usam Active Directory local, o administrador de ti deve [configurar dispositivos ingressados no Azure Active Directory híbrido](hybrid-azuread-join-manual-steps.md). 

## <a name="data-storage"></a>Armazenamento de dados
Enterprise State Roaming dados são hospedados em uma ou mais [regiões do Azure](https://azure.microsoft.com/regions/) que melhor se alinham com o valor de país/região definido na instância de Azure Active Directory. Enterprise State Roaming dados são particionados com base em três regiões geográficas principais: América do Norte, EMEA e a-Pacífico. Enterprise State Roaming dados para o locatário estão localizados localmente com a região geográfica e não são replicados entre regiões.  Por exemplo:

| Valor de país/região | tem seus dados hospedados em |
| -------------------- | ------------------------ |
| Um país/região da EMEA, como França ou Zâmbia | Uma ou mais das regiões do Azure na Europa |
| Um país/região da América do Norte, como Estados Unidos ou Canadá | Uma ou mais das regiões do Azure dentro dos EUA |
| Um país/região do oeste, como Austrália ou Nova Zelândia | Uma ou mais das regiões do Azure na Ásia |
| Regiões da América do Sul e da Antártica | Uma ou mais regiões do Azure dentro dos EUA |

O valor de país/região é definido como parte do processo de criação de diretório do Azure AD e não pode ser modificado posteriormente. Se você precisar de mais detalhes em seu local de armazenamento de dados, arquivo um tíquete com o [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Exibir status de sincronização de dispositivo por usuário
Siga estas etapas para exibir um relatório de status de sincronização de dispositivo por usuário.

1. Entre no [centro de administração do Azure ad](https://aad.portal.azure.com/).
1. Selecione **Azure Active Directory** &gt; usuários todos&gt; os **usuários**.
1. Selecione o usuário e, em seguida, selecione **dispositivos**.
1. Em **Mostrar**, selecione **dispositivos sincronizando configurações e dados de aplicativo** para mostrar o status de sincronização.
  
   ![imagem da configuração de dados de sincronização do dispositivo](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Se houver dispositivos sincronizando para esse usuário, você verá os dispositivos como mostrado aqui.
  
   ![imagem de dados de coluna de sincronização do dispositivo](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Retenção de dados
Os dados sincronizados com o Microsoft Cloud usando Enterprise State Roaming são mantidos até que sejam excluídos manualmente ou até que os dados em questão sejam determinados como obsoletos. 

### <a name="explicit-deletion"></a>Exclusão explícita
A exclusão explícita é quando um administrador do Azure exclui um usuário ou um diretório ou, de outra forma, solicita explicitamente que os dados sejam excluídos.

* **Exclusão de usuário**: Quando um usuário é excluído no Azure AD, os dados de roaming da conta de usuário são excluídos após 90 a 180 dias. 
* **Exclusão de diretório**: A exclusão de um diretório inteiro no Azure AD é uma operação imediata. Todos os dados de configurações associados a esse diretório são excluídos após 90 a 180 dias. 
* **Na exclusão da solicitação**: Se o administrador do Azure AD quiser excluir manualmente os dados de um usuário específico ou de configurações, o administrador poderá arquivar um tíquete com o [suporte do Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Exclusão de dados obsoleta
Os dados que não foram acessados por um ano ("o período de retenção") serão tratados como obsoletos e poderão ser excluídos do Microsoft Cloud. O período de retenção está sujeito a alterações, mas não será inferior a 90 dias. Os dados obsoletos podem ser um conjunto específico de configurações do Windows/aplicativo ou todas as configurações de um usuário. Por exemplo:

* Se nenhum dispositivo acessar uma coleção de configurações específica (por exemplo, um aplicativo for removido do dispositivo ou um grupo de configurações, como "tema" estiver desabilitado para todos os dispositivos de um usuário), essa coleção se tornará obsoleta após o período de retenção e poderá ser excluída . 
* Se um usuário desativou a sincronização de configurações em todos os seus dispositivos, nenhum dos dados de configurações será acessado e todos os dados de configurações desse usuário ficarão obsoletos e poderão ser excluídos após o período de retenção. 
* Se o administrador do diretório do Azure AD desligar Enterprise State Roaming para o diretório inteiro, todos os usuários nesse diretório irão parar de sincronizar as configurações e todos os dados de configurações de todos os usuários ficarão obsoletos e poderão ser excluídos após o período de retenção. 

### <a name="deleted-data-recovery"></a>Recuperação de dados excluída
A política de retenção de dados não é configurável. Depois que os dados são excluídos permanentemente, eles não são recuperáveis. No entanto, os dados de configurações são excluídos somente da nuvem da Microsoft, não do dispositivo do usuário final. Se qualquer dispositivo se reconectar mais tarde ao serviço de Enterprise State Roaming, as configurações serão sincronizadas novamente e armazenadas no Microsoft Cloud.

## <a name="next-steps"></a>Passos seguintes

* [Visão geral de Enterprise State Roaming](enterprise-state-roaming-overview.md)
* [Perguntas frequentes sobre configurações e roaming de dados](enterprise-state-roaming-faqs.md)
* [Configurações de Política de Grupo e MDM para sincronização de configurações](enterprise-state-roaming-group-policy-settings.md)
* [Referência de configurações de roaming do Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Resolução de problemas](enterprise-state-roaming-troubleshooting.md)
