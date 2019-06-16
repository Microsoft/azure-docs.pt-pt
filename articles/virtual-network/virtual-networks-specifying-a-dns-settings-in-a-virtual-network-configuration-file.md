---
title: Especificar as definições de DNS num ficheiro de configuração de rede virtual | Documentos da Microsoft
description: Como alterar as definições do servidor DNS numa rede virtual com um ficheiro de configuração de rede virtual no modelo de implementação clássica
services: virtual-network
documentationcenter: na
author: genlin
manager: cshepard
editor: tysonn
tags: azure-service-management
ms.assetid: a8905927-92ac-42b5-8c33-8e42c000692c
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/23/2016
ms.author: genli
ms.openlocfilehash: 36f7ed9b02b66718327c1a05a6cf29eedf39e7a5
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 06/13/2019
ms.locfileid: "60232840"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificar as definições de DNS num ficheiro de configuração de rede virtual
Um ficheiro de configuração de rede tem dois elementos que pode utilizar para especificar as definições do sistema de nomes de domínio (DNS): **DnsServers** e **DnsServerRef**. Pode adicionar uma lista de servidores DNS ao especificar os endereços IP e os nomes de referência a **DnsServers** elemento. Em seguida, pode utilizar um **DnsServerRef** elemento para especificar quais as entradas de servidor DNS do elemento DnsServers são utilizadas para sites de rede diferente na sua rede virtual.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica.

O ficheiro de configuração de rede pode conter os seguintes elementos. O título de cada elemento está ligado a uma página que fornece informações adicionais sobre as definições de valor do elemento.

> [!IMPORTANT]
> Para obter informações sobre como configurar o ficheiro de configuração de rede, consulte [configurar uma rede Virtual com um ficheiro de configuração de rede](virtual-networks-using-network-configuration-file.md). Para obter informações sobre cada elemento contido no ficheiro de configuração de rede, consulte [esquema de configuração de rede Virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Elemento de DNS](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> O **name** atributo o **DnsServer** elemento é usado apenas como referência para o **DnsServerRef** elemento. Não representa o nome de anfitrião para o servidor DNS. Cada **DnsServer** valor do atributo tem de ser exclusivo em toda a subscrição completa do Microsoft Azure
> 
> 

[Elemento de Sites de rede virtual](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Para poder especificar esta definição para o elemento de Sites de rede Virtual, ele deve ser definido anteriormente no elemento DNS. O DnsServerRef *name* nos Sites de rede Virtual elemento tem de referenciar um valor de nome especificado no elemento DNS para DnsServer *nome*.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* Compreender os [esquema de configuração de rede Virtual do Azure](https://go.microsoft.com/fwlink/?LinkId=248093).
* Compreender os [esquema de configuração de serviço do Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configurar uma rede virtual com arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md).

