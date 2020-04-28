---
title: Ativar o Roaming de Estado Empresarial no Azure Active Directory
description: Perguntas frequentes sobre as definições de Roaming do Estado da Empresa em dispositivos Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.collection: M365-identity-device-management
ms.openlocfilehash: 8c44d6266f5ea8cdd4f75d0449cb49852e71c905
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/28/2020
ms.locfileid: "78672401"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Ativar o Roaming de Estado Empresarial no Azure Active Directory

Enterprise State Roaming está disponível para qualquer organização com licença Azure AD Premium ou Enterprise Mobility + Security (EMS). Para obter mais informações sobre como obter uma subscrição de AD Azure, consulte a página do [produto Azure AD](https://azure.microsoft.com/services/active-directory).

Ao ativar o Enterprise State Roaming, a sua organização recebe automaticamente uma licença de utilização limitada gratuita para a proteção da Segurança dos Direitos do Azure da Proteção de Informação azure. Esta subscrição gratuita limita-se a encriptar e desencriptar as configurações da empresa e os dados de aplicação sincronizados pelo Enterprise State Roaming. Deve ter [uma subscrição paga](https://azure.microsoft.com/pricing/details/information-protection/) para utilizar todas as capacidades do serviço de Gestão de Direitos do Azure.

> [!NOTE]
> Este artigo aplica-se ao navegador baseado no Microsoft Edge Legacy HTML lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Crómio microsoft Edge, lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Para permitir o roaming do Estado Da Empresa

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com/).
1. Selecione **Azure Ative Directory** &gt; **Devices** &gt; **Enterprise State Roaming**.
1. Selecione **Os Utilizadores podem sincronizar as definições e os dados das aplicações através dos dispositivos.** Para mais informações, consulte [como configurar as definições](/azure/active-directory/device-management-azure-portal)do dispositivo .
  
   ![imagem da definição de dispositivo Utilizadores rotulados podem sincronizar definições e dados de aplicações em todos os dispositivos](./media/enterprise-state-roaming-enable/device-settings.png)
  
Para que um dispositivo Windows 10 utilize o serviço De roaming do Estado Da Empresa, o dispositivo deve autenticar utilizando uma identidade Azure AD. Para dispositivos que se juntam ao Azure AD, a identidade principal de entrada do utilizador é a sua identidade Azure AD, pelo que não é necessária nenhuma configuração adicional. Para dispositivos que utilizem no local O Diretório Ativo, o administrador de TI deve [configurar dispositivos híbridos Azure Ative Directory](hybrid-azuread-join-manual-steps.md). 

## <a name="data-storage"></a>Armazenamento de dados

Os dados do Roaming do Estado Empresarial estão alojados em uma ou mais [regiões azure](https://azure.microsoft.com/regions/) que melhor se alinham com o valor país/região estabelecido na instância de Diretório Ativo Azure. Os dados do Roaming do Estado Da Empresa são divididos com base em três grandes regiões geográficas: América do Norte, EMEA e APAC. Os dados de roaming do Estado Empresarial para o inquilino estão localizados localmente com a região geográfica, e não são replicados em todas as regiões.  Por exemplo:

| Valor país/região | tem os seus dados hospedados em |
| -------------------- | ------------------------ |
| Um país/região da EMEA como a França ou a Zâmbia | Uma ou mais das regiões de Azure na Europa |
| Um país/região norte-americano como os Estados Unidos ou o Canadá | Uma ou mais das regiões de Azure dentro dos EUA |
| Um país/região da APAC, como a Austrália ou a Nova Zelândia | Uma ou mais das regiões de Azure dentro da Ásia |
| Regiões sul-americanas e antárticas | Uma ou mais regiões azure dentro dos EUA |

O valor país/região é definido como parte do processo de criação de diretório sinuoso Azure E não pode ser posteriormente modificado. Se precisar de mais detalhes sobre a sua localização de armazenamento de dados, preencha um bilhete com [suporte Azure](https://azure.microsoft.com/support/options/).

## <a name="view-per-user-device-sync-status"></a>Ver estado de sincronização do dispositivo por utilizador

Siga estes passos para visualizar um relatório de estado de sincronização do dispositivo por utilizador.

1. Inscreva-se no [centro de administração da Azure AD](https://aad.portal.azure.com/).
1. Selecione **Utilizadores de Diretório** &gt; **Users** &gt; Ativo Azure **Todos os utilizadores**.
1. Selecione o utilizador e, em seguida, selecione **Dispositivos**.
1. Em **'Show',** selecione **Dispositivos sincronizando definições e dados** de aplicações para mostrar o estado de sincronização.
  
   ![imagem da definição de dados de sincronização do dispositivo](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Se houver dispositivos sincronizados para este utilizador, vê os dispositivos como mostrados aqui.
  
   ![imagem de dados de sincronia do dispositivo](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Retenção de dados

Os dados sincronizados com a nuvem da Microsoft utilizando o Enterprise State Roaming são retidos até que seja manualmente eliminado ou até que os dados em questão sejam determinados como estando apenas. 

### <a name="explicit-deletion"></a>Supressão explícita

A eliminação explícita é quando um administrador do Azure elimina um utilizador ou um diretório ou solicita explicitamente que os dados serão eliminados.

* **Eliminação do utilizador**: Quando um utilizador é eliminado em AD Azure, os dados de roaming da conta de utilizador são eliminados após 90 a 180 dias. 
* **Eliminação do diretório**: Apagar todo um diretório em Azure AD é uma operação imediata. Todos os dados de definições associados a esse diretório são eliminados após 90 a 180 dias. 
* **Após a eliminação**do pedido : Se o administrador da AD Azure quiser eliminar manualmente os dados ou dados de configurações de um utilizador específico, o administrador pode arquivar um bilhete com [suporte Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Eliminação de dados estadreto

Os dados que não tenham sido acedidos há um ano ("o período de retenção") serão tratados como estando estando fortalecidos e poderão ser eliminados da nuvem da Microsoft. O período de retenção está sujeito a alterações, mas não será inferior a 90 dias. Os dados relativos ao mau tempo podem ser um conjunto específico de definições windows/aplicações ou todas as definições para um utilizador. Por exemplo:

* Se nenhum dispositivo aceder a uma determinada recolha de definições (por exemplo, uma aplicação é removida do dispositivo, ou um grupo de definições como "Tema" é desativado para todos os dispositivos do utilizador), então essa recolha torna-se estadósta após o período de retenção e pode ser eliminada. 
* Se um utilizador tiver desligado as definições sincronizar todos os seus dispositivos, então nenhum dos dados de definição será acedido, e todos os dados de definições para esse utilizador ficarão velhos e poderão ser eliminados após o período de retenção. 
* Se o administrador de diretório da AD Azure desativar o Enterprise State Roaming para todo o diretório, então todos os utilizadores desse diretório deixarão de sincronizar definições, e todos os dados de definições para todos os utilizadores ficarão obsoletos e poderão ser eliminados após o período de retenção. 

### <a name="deleted-data-recovery"></a>Recuperação de dados eliminados

A política de retenção de dados não é configurável. Uma vez eliminados permanentemente os dados, não é recuperável. No entanto, os dados das definições são eliminados apenas a partir da nuvem da Microsoft, e não do dispositivo de utilizador final. Se algum dispositivo voltar a ligar-se ao serviço de Roaming do Estado da Empresa, as definições são novamente sincronizadas e armazenadas na nuvem da Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Roaming de Estado Empresarial](enterprise-state-roaming-overview.md)
* [FAQ de definições e roaming de dados](enterprise-state-roaming-faqs.md)
* [Política de Grupo e definições de MDM para sincronização de configurações](enterprise-state-roaming-group-policy-settings.md)
* [Referência das definições de roaming do Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Resolução de problemas](enterprise-state-roaming-troubleshooting.md)
