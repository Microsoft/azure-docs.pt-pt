---
title: Ativar o Roaming de Estado Empresarial no Azure Active Directory
description: Perguntas frequentes sobre as definições de Roaming do Estado da Empresa em dispositivos Windows.
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: how-to
ms.date: 02/12/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: na
ms.custom: references_regions
ms.collection: M365-identity-device-management
ms.openlocfilehash: 34b554fbef63f23b3540fe49e5c45976122add25
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "89268609"
---
# <a name="enable-enterprise-state-roaming-in-azure-active-directory"></a>Ativar o Roaming de Estado Empresarial no Azure Active Directory

Enterprise State Roaming está disponível para qualquer organização com licença Azure AD Premium ou Enterprise Mobility + Security (EMS). Para obter mais informações sobre como obter uma subscrição AD AZure, consulte a página do [produto AD Azure](https://azure.microsoft.com/services/active-directory).

Quando ativa o Roaming do Estado da Empresa, a sua organização recebe automaticamente uma licença de utilização limitada gratuita para a proteção de Gestão de Direitos Azure da Azure Information Protection. Esta subscrição gratuita está limitada a encriptar e desencriptar as definições da empresa e os dados de aplicação sincronizados pelo Enterprise State Roaming. Deve ter [uma subscrição paga](https://azure.microsoft.com/pricing/details/information-protection/) para utilizar todas as capacidades do serviço de Gestão de Direitos Azure.

> [!NOTE]
> Este artigo aplica-se ao navegador baseado em HTML do Microsoft Edge Legacy lançado com o Windows 10 em julho de 2015. O artigo não se aplica ao novo navegador baseado em Chromium microsoft Edge lançado a 15 de janeiro de 2020. Para obter mais informações sobre o comportamento do Sync para o novo Microsoft Edge, consulte o artigo [Microsoft Edge Sync](/deployedge/microsoft-edge-enterprise-sync).

## <a name="to-enable-enterprise-state-roaming"></a>Para ativar o roaming do Estado da Empresa

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com/).
1. Selecione **Azure Ative Directory**  >  **Devices**  >  **Enterprise State Roaming**.
1. Selecione **Os Utilizadores podem sincronizar as definições e os dados da aplicação em todos os dispositivos.** Para obter mais informações, [consulte como configurar as definições do dispositivo](./device-management-azure-portal.md).
  
   ![imagem da definição do dispositivo Os utilizadores podem sincronizar as definições e os dados da aplicação em todos os dispositivos](./media/enterprise-state-roaming-enable/device-settings.png)
  
Para que um dispositivo Windows 10 utilize o serviço Enterprise State Roaming, o dispositivo deve autenticar utilizando uma identidade AD AZure. Para os dispositivos que se unem ao Azure AD, a principal identidade de inscrição do utilizador é a sua identidade AD Azure, pelo que não é necessária nenhuma configuração adicional. Para os dispositivos que utilizam no local Ative Directory, o administrador de TI deve [configurar dispositivos híbridos Azure Ative Directory](./hybrid-azuread-join-plan.md). 

## <a name="data-storage"></a>Armazenamento de dados

Os dados do Roaming do Estado da Empresa são alojados numa ou mais [regiões do Azure](https://azure.microsoft.com/regions/) que melhor se alinham com o valor país/região definido no exemplo do Azure Ative Directory. Os dados do Roaming do Estado da Empresa são divididos com base em três grandes regiões geográficas: América do Norte, EMEA e APAC. Os dados do Roaming do Estado Empresarial para o inquilino estão localizados localmente com a região geográfica, e não são replicados em todas as regiões.  Por exemplo:

| Valor país/região | tem os seus dados hospedados em |
| -------------------- | ------------------------ |
| Um país/região EMEA como a França ou a Zâmbia | Uma ou mais das regiões de Azure na Europa |
| Um país/região norte-americano como estados unidos ou Canadá | Uma ou mais das regiões de Azure nos EUA |
| Um país/região APAC como a Austrália ou a Nova Zelândia | Uma ou mais das regiões de Azure na Ásia |
| Regiões sul-americanas e antárticas | Uma ou mais regiões de Azure nos EUA |

O valor país/região é definido como parte do processo de criação de diretórios Azure AD e não pode ser posteriormente modificado. Se precisar de mais detalhes sobre a sua localização de armazenamento de dados, preencha um bilhete com [suporte Azure.](https://azure.microsoft.com/support/options/)

## <a name="view-per-user-device-sync-status"></a>Ver estado de sincronização do dispositivo por utilizador

Siga estes passos para ver um relatório de estado de sincronização do dispositivo por utilizador.

1. Inscreva-se no [centro de administração Azure AD](https://aad.portal.azure.com/).
1. Selecione **Utilizadores de Diretório Ativo Azure**  >    >  **Todos os utilizadores**.
1. Selecione o utilizador e, em seguida, selecione **Dispositivos**.
1. Em **'Mostrar'** **(200 euros), selecione dispositivos sincronizando as definições e os dados da aplicação** para mostrar o estado de sincronização.
  
   ![imagem da definição de dados de sincronização do dispositivo](./media/enterprise-state-roaming-enable/sync-status.png)
  
1. Se existirem dispositivos sincronizados para este utilizador, consulte os dispositivos como mostrado aqui.
  
   ![imagem de dados colunaar de sincronização de dispositivos](./media/enterprise-state-roaming-enable/device-status-row.png)

## <a name="data-retention"></a>Retenção de dados

Os dados sincronizados na nuvem da Microsoft utilizando o Roaming do Estado da Empresa são retidos até que seja eliminado manualmente ou até que os dados em questão sejam determinados como estando em situação de estagnação. 

### <a name="explicit-deletion"></a>Eliminação explícita

A eliminação explícita é quando um administrador do Azure elimina um utilizador ou um diretório ou solicita explicitamente que os dados devem ser eliminados.

* **Eliminação do utilizador**: Quando um utilizador é eliminado em Azure AD, os dados de roaming da conta de utilizador são eliminados após 90 a 180 dias. 
* **Eliminação do diretório**: Eliminar todo um diretório em Azure AD é uma operação imediata. Todos os dados de definições associados a esse diretório são eliminados após 90 a 180 dias. 
* **A pedido de eliminação**: Se o administrador AD AD AZure quiser eliminar manualmente os dados ou definições de um utilizador específico, o administrador pode arquivar um bilhete com [suporte Azure](https://azure.microsoft.com/support/). 

### <a name="stale-data-deletion"></a>Eliminação de dados obsoletos

Os dados que não foram acedidos há um ano ("o período de retenção") serão tratados como obsoletos e poderão ser eliminados da nuvem da Microsoft. O período de retenção está sujeito a alterações, mas não será inferior a 90 dias. Os dados antigos podem ser um conjunto específico de definições do Windows/aplicação ou de todas as definições para um utilizador. Por exemplo:

* Se nenhum dispositivo aceder a uma determinada recolha de configurações (por exemplo, uma aplicação é removida do dispositivo, ou um grupo de definições como "Tema" é desativado para todos os dispositivos de um utilizador), então essa recolha fica em mau estado após o período de retenção e pode ser eliminada. 
* Se um utilizador tiver desligado as definições sincronizadas em todos os seus dispositivos, nenhum dos dados de definições será acedido e todos os dados de definições para esse utilizador ficarão em mau estado e poderão ser eliminados após o período de retenção. 
* Se o diretório Azure AD desligar o Roaming do Estado da Empresa para todo o diretório, então todos os utilizadores nesse diretório deixarão de sincronizar as definições, e todos os dados de definições para todos os utilizadores ficarão em situação de falência e poderão ser eliminados após o período de retenção. 

### <a name="deleted-data-recovery"></a>Recuperação de dados eliminados

A política de retenção de dados não é configurável. Uma vez eliminados permanentemente os dados, não é recuperável. No entanto, os dados das definições são eliminados apenas a partir da nuvem da Microsoft, e não do dispositivo do utilizador final. Se algum dispositivo voltar a ligar-se ao serviço Enterprise State Roaming, as definições são novamente sincronizadas e armazenadas na nuvem da Microsoft.

## <a name="next-steps"></a>Passos seguintes

* [Descrição geral do Roaming de Estado Empresarial](enterprise-state-roaming-overview.md)
* [FAQ de definições e roaming de dados](enterprise-state-roaming-faqs.md)
* [Políticas de grupo e configurações de MDM para sincronização de definições](enterprise-state-roaming-group-policy-settings.md)
* [Referência das definições de roaming do Windows 10](enterprise-state-roaming-windows-settings-reference.md)
* [Resolução de problemas](enterprise-state-roaming-troubleshooting.md)