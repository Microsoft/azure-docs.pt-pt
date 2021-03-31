---
title: Filtragem do Azure Firewall Manager nas regras da rede (pré-visualização)
description: Como utilizar a filtragem FQDN nas regras de rede
services: firewall-manager
author: vhorne
ms.service: firewall-manager
ms.topic: article
ms.date: 07/30/2020
ms.author: victorh
ms.openlocfilehash: 28cd26532ca5bdf83902854b7910f7d6c18a4eab
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "87460155"
---
# <a name="fqdn-filtering-in-network-rules-preview"></a>Filtragem FQDN nas regras da rede (pré-visualização)

> [!IMPORTANT]
> A filtragem FQDN nas regras de rede está atualmente em pré-visualização pública.
> Esta versão de pré-visualização é disponibiliza sem um contrato de nível de serviço e não é recomendada para cargas de trabalho de produção. Algumas funcionalidades poderão não ser suportadas ou poderão ter capacidades limitadas. Para obter mais informações, veja [Termos Suplementares de Utilização para Pré-visualizações do Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Um nome de domínio totalmente qualificado (FQDN) representa um nome de domínio de um anfitrião ou endereço IP(es). Pode utilizar FQDNs em regras de rede com base na resolução de DNS na política de Azure Firewall e Firewall. Esta capacidade permite filtrar o tráfego de saída com qualquer protocolo TCP/UDP (incluindo NTP, SSH, RDP, entre outros). Tem de permitir que o DNS Proxy utilize FQDNs nas suas regras de rede. Para obter mais informações consulte [as definições de DNS da política do Azure Firewall (pré-visualização)](dns-settings.md).

## <a name="how-it-works"></a>Como funciona

Assim que definir qual o servidor DNS de que a sua organização necessita (Azure DNS ou o seu próprio DNS personalizado), o Azure Firewall traduz o FQDN para um endereço IP(es) com base no servidor DNS selecionado. Esta tradução acontece tanto para o processamento de regras de aplicação como para a rede.

Qual é a diferença entre usar nomes de domínio nas regras de aplicação em comparação com as regras de rede? 

- A filtragem FQDN nas regras de aplicação para HTTP/S e MSSQL baseia-se num proxy transparente de nível de aplicação e no cabeçalho SNI. Como tal, pode discernir entre dois FQDNs que são resolvidos para o mesmo endereço IP. Não é o caso da filtragem FQDN nas regras da rede. Utilize sempre as regras de aplicação sempre que possível.
- Nas regras de aplicação, pode utilizar HTTP/S e MSSQL como protocolos selecionados. Nas regras de rede, pode utilizar qualquer protocolo TCP/UDP com o seu destino FQDNs.

## <a name="next-steps"></a>Passos seguintes

[Definições de DNS de Firewall Azure Firewall](dns-settings.md)
