---
title: Filtragem FQDN de Azure Firewall nas regras da rede (pré-visualização)
description: Como utilizar a filtragem FQDN do Azure Firewall nas regras da rede
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 6e90a8bc0998b43a84658958215e4b7977f8fdd0
ms.sourcegitcommit: f988fc0f13266cea6e86ce618f2b511ce69bbb96
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 07/31/2020
ms.locfileid: "87461311"
---
# <a name="use-fqdn-filtering-in-network-rules-preview"></a>Utilize a filtragem FQDN nas regras da rede (pré-visualização)

> [!IMPORTANT]
> A filtragem FQDN nas regras de rede está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, consulte [termos de utilização suplementares para pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um nome de domínio totalmente qualificado (FQDN) representa um nome de domínio de um anfitrião ou endereço IP(es). Pode utilizar FQDNs em regras de rede com base na resolução de DNS na política de Azure Firewall e Firewall. Esta capacidade permite filtrar o tráfego de saída com qualquer protocolo TCP/UDP (incluindo NTP, SSH, RDP, entre outros). Tem de permitir que o DNS Proxy utilize FQDNs nas suas regras de rede. Para obter mais informações consulte [as definições de DNS da Firewall (pré-visualização)](dns-settings.md).

## <a name="how-it-works"></a>Como funciona

Assim que definir qual o servidor DNS de que a sua organização necessita (Azure DNS ou o seu próprio DNS personalizado), o Azure Firewall traduz o FQDN para um endereço IP(es) com base no servidor DNS selecionado. Esta tradução acontece tanto para o processamento de regras de aplicação como para a rede.

Qual é a diferença entre usar nomes de domínio nas regras de aplicação em comparação com as regras de rede? 

- A filtragem FQDN nas regras de aplicação para HTTP/S e MSSQL baseia-se num proxy transparente de nível de aplicação e no cabeçalho SNI. Como tal, pode discernir entre dois FQDNs que são resolvidos para o mesmo endereço IP. Não é o caso da filtragem FQDN nas regras da rede. Utilize sempre as regras de aplicação sempre que possível.
- Nas regras de aplicação, pode utilizar HTTP/S e MSSQL como protocolos selecionados. Nas regras de rede, pode utilizar qualquer protocolo TCP/UDP com o seu destino FQDNs.

## <a name="next-steps"></a>Passos seguintes

[Definições de DNS de Firewall Azure Firewall](dns-settings.md)
