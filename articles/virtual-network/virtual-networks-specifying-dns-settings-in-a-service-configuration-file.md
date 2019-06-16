---
title: Especificar as definições de DNS num ficheiro de configuração do serviço | Documentos da Microsoft
description: especificar as definições de DNS personalizadas usando o arquivo de configuração de serviço para a rede virtual
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
ms.assetid: 467a4b99-8691-40b3-b640-e25e49675c71
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/24/2016
ms.author: genli
ms.openlocfilehash: 0ac488a67d8b9debf6539d199395997cf44cf1e4
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60232748"
---
# <a name="specifying-dns-settings-in-a-service-configuration-file"></a>Especificar as definições de DNS num ficheiro de configuração de serviço
## <a name="dns-elements"></a>Elementos DNS
Um ficheiro de configuração de serviço pode conter um elemento de DnsServers com uma lista de endereços de IPv4 para os servidores de sistema de nomes de domínio (DNS) que o serviço irá utilizar. As definições no ficheiro de configuração do serviço têm precedência sobre as definições no ficheiro de configuração de rede. Para obter mais informações, consulte [esquema de configuração de serviço do Azure (. cscfg ficheiro)](https://msdn.microsoft.com/library/azure/ee758710.aspx).

**Elemento NetworkConfiguration**

      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>

> [!WARNING]
> O **name** atributo a **DnsServer** elemento é utilizado apenas como um nome de referência. Não representa o nome de anfitrião para o servidor DNS. Cada **DnsServer** valor do atributo tem de ser exclusivo em toda a subscrição completa do Microsoft Azure.
> 
> 

## <a name="see-also"></a>Consultar Também
[Esquema de configuração de serviço do Azure (. cscfg)](https://msdn.microsoft.com/library/windowsazure/ee758710)

[Esquema de configuração de rede Virtual do Azure](https://go.microsoft.com/fwlink/?LinkId=248093)

[Configurar uma rede Virtual com arquivos de configuração de rede](https://go.microsoft.com/fwlink/?LinkId=248094)

[Acerca das definições de rede Virtual no Portal de gestão](https://go.microsoft.com/fwlink/?LinkId=248092)

