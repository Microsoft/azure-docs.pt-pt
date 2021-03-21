---
title: Filtragem FQDN de Azure Firewall nas regras da rede
description: Como utilizar a filtragem FQDN do Azure Firewall nas regras da rede
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/06/2020
ms.author: victorh
ms.openlocfilehash: f7196c7715ad5d2c02759040b780b96218e1655e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/19/2021
ms.locfileid: "94695950"
---
# <a name="use-fqdn-filtering-in-network-rules"></a>Utilize filtragem FQDN nas regras de rede

Um nome de domínio totalmente qualificado (FQDN) representa um nome de domínio de um anfitrião ou endereço IP(es). Pode utilizar FQDNs em regras de rede com base na resolução de DNS na política de Azure Firewall e Firewall. Esta capacidade permite filtrar o tráfego de saída com qualquer protocolo TCP/UDP (incluindo NTP, SSH, RDP, entre outros). Tem de permitir que o DNS Proxy utilize FQDNs nas suas regras de rede. Para obter mais informações consulte [as definições de DNS da Firewall Azure](dns-settings.md).

> [!NOTE]
> Por design, a filtragem FQDN não suporta wildcards.

## <a name="how-it-works"></a>Como funciona

Assim que definir qual o servidor DNS de que a sua organização necessita (Azure DNS ou o seu próprio DNS personalizado), o Azure Firewall traduz o FQDN para um endereço IP(es) com base no servidor DNS selecionado. Esta tradução acontece tanto para o processamento de regras de aplicação como para a rede.

Quando uma nova resolução de DNS ocorre, novos endereços IP são adicionados às regras de firewall. Os antigos endereços IP que já não são devolvidos pelo servidor DNS expiram em 15 minutos. As regras do Azure Firewall são atualizadas a cada 15 segundos da resolução de DNS das FQDNs nas regras de rede.

### <a name="differences-in-application-rules-vs-network-rules"></a>Diferenças nas regras de aplicação vs. regras de rede

- A filtragem FQDN nas regras de aplicação para HTTP/S e MSSQL baseia-se num proxy transparente de nível de aplicação e no cabeçalho SNI. Como tal, pode discernir entre dois FQDNs que são resolvidos para o mesmo endereço IP. Não é o caso da filtragem FQDN nas regras da rede. 

   Utilize sempre as regras de aplicação quando possível:
     - Se o protocolo for HTTP/S ou MSSQL, utilize as regras de aplicação para filtragem FQDN.
   - Para outros protocolos para além de HTTP/S ou MSSQL, pode utilizar regras de aplicação ou rede para filtragem FQDN.

## <a name="next-steps"></a>Passos seguintes

[Definições de DNS de Firewall Azure Firewall](dns-settings.md)
