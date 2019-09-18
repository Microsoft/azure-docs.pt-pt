---
title: Especificando as configurações de DNS em um arquivo de configuração de rede virtual | Microsoft Docs
description: Como alterar as configurações do servidor DNS em uma rede virtual usando um arquivo de configuração de rede virtual no modelo de implantação clássico
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
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
ms.openlocfilehash: c15d73673c19383deabe15ef30026990dfd138b9
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/17/2019
ms.locfileid: "71059095"
---
# <a name="specifying-dns-settings-in-a-virtual-network-configuration-file"></a>Especificando as configurações de DNS em um arquivo de configuração de rede virtual
Um arquivo de configuração de rede tem dois elementos que você pode usar para especificar configurações de DNS (sistema de nomes de domínio): **Dnsservers** e **DnsServerRef**. Você pode adicionar uma lista de servidores DNS especificando seus endereços IP e nomes de referência para o elemento **dnsservers** . Em seguida, você pode usar um elemento **DnsServerRef** para especificar quais entradas do servidor DNS do elemento dnsservers são usadas para diferentes sites de rede em sua rede virtual.

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Este artigo abrange o modelo de implementação clássica.

O arquivo de configuração de rede pode conter os seguintes elementos. O título de cada elemento é vinculado a uma página que fornece informações adicionais sobre as configurações de valor do elemento.

> [!IMPORTANT]
> Para obter informações sobre como configurar o arquivo de configuração de rede, consulte [Configurar uma rede virtual usando um arquivo de configuração de rede](virtual-networks-using-network-configuration-file.md). Para obter informações sobre cada elemento contido no arquivo de configuração de rede, consulte [esquema de configuração de rede virtual do Azure](https://msdn.microsoft.com/library/azure/jj157100.aspx).
> 
> 

[Elemento DNS](https://go.microsoft.com/fwlink/?LinkId=248093)

    <Dns>
      <DnsServers>
        <DnsServer name="ID1" IPAddress="IPAddress1" />
        <DnsServer name="ID2" IPAddress="IPAddress2" />
        <DnsServer name="ID3" IPAddress="IPAddress3" />
      </DnsServers>
    </Dns>

> [!WARNING]
> O atributo **Name** no elemento **DnsServer** é usado apenas como uma referência para o elemento **DnsServerRef** . Ele não representa o nome do host para o servidor DNS. Cada valor de atributo **DnsServer** deve ser exclusivo em toda a assinatura de Microsoft Azure
> 
> 

[Elemento de sites de rede virtual](https://go.microsoft.com/fwlink/?LinkId=248093)

    <DnsServersRef>
      <DnsServerRef name="ID1" />
      <DnsServerRef name="ID2" />
      <DnsServerRef name="ID3" />
    </DnsServersRef>

> [!NOTE]
> Para especificar essa configuração para o elemento de sites de rede virtual, ele deve ser definido anteriormente no elemento DNS. O *nome* do DnsServerRef no elemento sites de rede virtual deve se referir a um valor de nome especificado no elemento DNS para o *nome*DnsServer.
> 
> 

## <a name="next-steps"></a>Passos Seguintes
* Entenda o [esquema de configuração de rede virtual do Azure](https://go.microsoft.com/fwlink/?LinkId=248093).
* Entenda o [esquema de configuração do serviço do Azure](https://msdn.microsoft.com/library/windowsazure/ee758710).
* [Configure uma rede virtual usando arquivos de configuração de rede](virtual-networks-using-network-configuration-file.md).

