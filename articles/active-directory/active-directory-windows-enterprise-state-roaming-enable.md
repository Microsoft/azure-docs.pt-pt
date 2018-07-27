---
title: Ativar estado da empresa em Roaming no Azure Active Directory | Documentos da Microsoft
description: Perguntas mais frequentes sobre as definições de Roaming de estado empresarial em dispositivos Windows. Roaming de estado empresarial fornece aos usuários uma experiência unificada em todos os seus dispositivos Windows e reduz o tempo necessário para configurar um novo dispositivo.
services: active-directory
keywords: roaming, cloud do windows, como ativar o roaming de estado empresarial de estado empresarial
documentationcenter: ''
author: tanning
manager: mtillman
editor: curtand
ms.component: devices
ms.assetid: f71d66fd-7f9e-45eb-9cfe-5d989870f8a4
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/23/2018
ms.author: markvi
ms.openlocfilehash: bb2210619e481189fc88ca3bb6b8044a8f5d7e14
ms.sourcegitcommit: a5eb246d79a462519775a9705ebf562f0444e4ec
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/26/2018
ms.locfileid: "39262953"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Ativar o Roaming de Estado Empresarial no Azure Active Directory
Roaming de estado empresarial está disponível para qualquer organização com um Azure AD Premium ou Enterprise Mobility + licença Security (EMS). Para obter mais informações sobre como obter uma subscrição do Azure AD, consulte a [página de produto do Azure AD](https://azure.microsoft.com/services/active-directory).

Quando ativar o Roaming de estado empresarial, sua organização é automaticamente concedida uma licença gratuita, de utilização limitada para a proteção Azure Rights Management do Azure Information Protection. Esta subscrição gratuita está limitada a encriptação e desencriptação de definições da empresa e dados de aplicação sincronizados por Roaming de estado empresarial. Tem de ter [uma subscrição paga](https://azure.microsoft.com/pricing/details/information-protection/) para utilizar as funcionalidades completas do serviço Azure Rights Management.

## <a name="to-enable-enterprise-state-roaming"></a>Para ativar o Roaming de estado empresarial

1. Inicie sessão no [Centro de administração do Azure AD](https://aad.portal.azure.com/).

2. Selecione **do Azure Active Directory** &gt; **dispositivos** &gt; **definições do dispositivo**.

3. Selecione **os utilizadores podem sincronizar definições e dados de aplicação em todos os dispositivos**. Para obter mais informações, consulte [como configurar as definições de dispositivo](https://docs.microsoft.com/azure/active-directory/device-management-azure-portal).
  
  ![imagem da definição de dispositivo denominada os utilizadores pode sincronizar definições e dados de aplicação em todos os dispositivos](./media/active-directory-windows-enterprise-state-roaming-enable/device-settings.png)
  
Para um dispositivo Windows 10 utilizar o serviço de Roaming de estado empresarial, o dispositivo tem de ser autenticado com uma identidade do Azure AD. Para dispositivos que estão associados ao Azure AD, a identidade do utilizador primário início de sessão é a identidade do Azure AD, portanto, é necessária nenhuma configuração adicional. Para dispositivos que utilizam o Active Directory no local, o administrador de TI deve [ligar os dispositivos associados a um domínio ao Azure AD para experiências do Windows 10](active-directory-azureadjoin-devices-group-policy.md).

## <a name="data-storage"></a>Armazenamento de dados
Dados de Roaming de estado empresarial são hospedados num ou mais [regiões do Azure](https://azure.microsoft.com/regions/) que melhor se alinham com o valor de país/região definido na instância do Azure Active Directory. Roaming de estado empresarial os dados são particionados com base em três regiões geográficas grandes: América do Norte, EMEA e APAC. Dados de Roaming de estado empresarial para o inquilino está localmente localizados com a região geográfica e não são replicados em várias regiões.  Por exemplo:
Valor de país/região | tem dos dados hospedados em
---------------------|-------------------------
Um país EMEA, como "França" ou "Zâmbia" | uma ou das regiões do Azure dentro da Europa 
Um país América do Norte, como "EUA" ou "Canadá" | uma ou mais das regiões do Azure nos E.U.A.
Um país APAC, como "Australia" ou "Nova Zelândia" | uma ou mais das regiões do Azure na Ásia
Regiões da América do Sul e Antárctica | um ou mais regiões do Azure nos E.U.A.

O valor de país/região está definido como parte do processo de criação de diretório do Azure AD e não pode ser modificado posteriormente. Se precisar de obter mais detalhes na sua localização de armazenamento de dados, um ticket [suporte do Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Ver o estado de sincronização do dispositivo por utilizador
Siga estes passos para ver um relatório de estado de sincronização de dispositivos por utilizador.

1. Inicie sessão no [Centro de administração do Azure AD](https://aad.portal.azure.com/).

2. Selecione **do Azure Active Directory** &gt; **utilizadores** &gt; **todos os utilizadores**.

3. Selecione o utilizador e, em seguida, selecione **dispositivos**.

4. Sob **mostrar**, selecione **dispositivos estão a sincronizar definições e dados da aplicação** para mostrar o estado da sincronização.
  
  ![imagem da definição de dados de sincronização do dispositivo](./media/active-directory-windows-enterprise-state-roaming-enable/sync-status.png)
  
5. Se existirem dispositivos a sincronização para este utilizador, pode ver os dispositivos conforme mostrado aqui.
  
  ![imagem dos dados em colunas de sincronização de dispositivo](./media/active-directory-windows-enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Retenção de dados
Dados sincronizados no Azure com o Roaming de estado empresarial são mantidos até ser eliminado manualmente ou até que os dados em questão são identificados como sendo obsoletos. 

### <a name="explicit-deletion"></a>Eliminação explícita
Eliminação explícita é quando um administrador do Azure elimina um utilizador ou um diretório ou caso contrário, solicita explicitamente que os dados estão a ser eliminado.

* **Eliminação do utilizador**: quando um utilizador é eliminado no Azure AD, a conta de utilizador roaming de dados é eliminada após 90 para 180 dias. 
* **Eliminação de diretório**: a eliminar um diretório inteiro no Azure AD é uma operação de imediato. Todos os dados de definições associados com que o diretório é eliminado após 90 para 180 dias. 
* **Na eliminação do pedido**: se o administrador do Azure AD quiser eliminar manualmente os dados ou dados de definições de um utilizador específico, o administrador pode enviar um pedido com [suporte do Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Eliminação de dados obsoletos
Dados que não tiverem sido acedidos durante um ano ("o período de retenção") serão tratados como obsoleto e podem ser eliminados do Azure. O período de retenção está sujeitas a alterações, mas não será inferior a 90 dias. Os dados obsoletos podem ser um conjunto específico de definições do Windows/aplicação ou todas as definições para um utilizador. Por exemplo:

* Se não existem dispositivos aceder a uma coleção de definições (por exemplo, um aplicativo é removido do dispositivo ou um grupo de definições, tais como "Tema" está desativado para todos os dispositivos de um utilizador), em seguida, essa coleção se tornam obsoleta após o período de retenção e podem ser eliminada . 
* Se um utilizador tiver desativado a sincronização de definições em todos os seu dispositivos, em seguida, nenhum dos dados de definições serão acessados e todos os dados de definições para que o utilizador irão tornar-se obsoletos e podem ser eliminados após o período de retenção. 
* Se o administrador do diretório do Azure AD Se desligar Roaming de estado empresarial para o diretório, em seguida, todos os utilizadores que diretório irá parar a sincronizar definições e todos os dados de definições para todos os utilizadores irão tornar-se obsoletos e podem ser eliminados após o período de retenção. 

### <a name="deleted-data-recovery"></a>Recuperação de dados eliminados
A política de retenção de dados não é configurável. Depois dos dados são eliminados permanentemente, não é recuperável. No entanto, os dados de definições são eliminados apenas a partir do Azure, não a partir do dispositivo do utilizador final. Se qualquer dispositivo volta mais tarde para o serviço de Roaming de estado empresarial, as definições são sincronizadas novamente e armazenadas no Azure.

## <a name="related-topics"></a>Tópicos relacionados
* [Descrição geral do Roaming de estado empresarial](active-directory-windows-enterprise-state-roaming-overview.md)
* [FAQ de roaming de dados e definições](active-directory-windows-enterprise-state-roaming-faqs.md)
* [Definições de política e o MDM para sincronização de definições de grupo](active-directory-windows-enterprise-state-roaming-group-policy-settings.md)
* [Referência de configurações de roaming com o Windows 10](active-directory-windows-enterprise-state-roaming-windows-settings-reference.md)
* [Resolução de problemas](active-directory-windows-enterprise-state-roaming-troubleshooting.md)
