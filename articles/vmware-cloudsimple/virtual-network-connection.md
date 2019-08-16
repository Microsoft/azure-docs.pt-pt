---
title: Conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute
description: Descreve como obter informações de emparelhamento para uma conexão entre a rede virtual do Azure e seu ambiente CloudSimple
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/14/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ba2d2fd9c943fe55e82956d022f6ba9840a550f
ms.sourcegitcommit: 040abc24f031ac9d4d44dbdd832e5d99b34a8c61
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 08/16/2019
ms.locfileid: "69536602"
---
# <a name="connect-azure-virtual-network-to-cloudsimple-using-expressroute"></a>Conectar a rede virtual do Azure ao CloudSimple usando o ExpressRoute

Você pode estender sua rede de nuvem privada para sua rede virtual do Azure e recursos do Azure. Uma conexão do ExpressRoute permite que você acesse recursos em execução na sua assinatura do Azure de sua nuvem privada.

## <a name="request-authorization-key"></a>Solicitar chave de autorização

Uma chave de autorização é necessária para a conexão de ExpressRoute entre sua nuvem privada e a rede virtual do Azure. Para obter uma chave, arquivo de um tíquete com <a href="https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest" target="_blank">suporte</a>.  Use as seguintes informações na solicitação:

* Tipo de problema: **Technical**
* Subscrição: Selecione a assinatura na qual o serviço CloudSimple está implantado * *
* Serviço: **Solução VMware por CloudSimple**
* Tipo de problema: **Solicitação de serviço**
* Subtipo de problema: **Chave de autorização para conexão de VNET do Azure**
* Requerente: **Solicitação de chave de autorização para conexão de VNET do Azure**

## <a name="obtain-peering-information-for-azure-virtual-network-to-cloudsimple-connection"></a>Obter informações de emparelhamento para a rede virtual do Azure para conexão CloudSimple

Para configurar a conexão, você deve estabelecer um vínculo entre a rede virtual do Azure e o ambiente do CloudSimple.  Como parte do procedimento, você deve fornecer o URI do circuito par e a chave de autorização. Obtenha o URI e a chave de autorização do [portal do CloudSimple](access-cloudsimple-portal.md).  Selecione **rede** no menu lateral e, em seguida, selecione **conexão de rede do Azure**. Ou selecione **conta** no menu lateral e, em seguida, selecione **conexão de rede do Azure**.

Observe os ícones de cópia para o URI do circuito par e para a chave de autorização para cada uma das regiões. Para cada nuvem privada que você deseja conectar:

* Clique em **copiar** para copiar o URI. Cole-o em um arquivo no qual ele pode estar disponível para ser adicionado ao portal do Azure.  
* Clique em **copiar** para copiar a chave de autorização e cole-a no arquivo também.

![Página conexão de rede virtual](media/network-virt-conn-page.png)

Para obter detalhes sobre como configurar a rede virtual do Azure para o link do CloudSimple, consulte [conectar seu ambiente de nuvem privada do CloudSimple à rede virtual do Azure usando o ExpressRoute](azure-expressroute-connection.md).
