---
title: Dupla encriptação no Microsoft Azure
description: Este artigo descreve como o Microsoft Azure fornece encriptação dupla para dados em repouso e dados em trânsito.
services: security
documentationcenter: na
author: TerryLanfear
manager: rkarlin
ms.assetid: 9dcb190e-e534-4787-bf82-8ce73bf47dba
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 08/13/2020
ms.author: terrylan
ms.openlocfilehash: 020e8249f57ccb1a14da798a717a00dcc3962389
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/29/2021
ms.locfileid: "88227321"
---
# <a name="double-encryption"></a>Encriptação dupla
A dupla encriptação é onde duas ou mais camadas independentes de encriptação são ativadas para proteger contra compromissos de qualquer camada de encriptação. Usar duas camadas de encriptação atenua as ameaças que vêm com dados encriptadores. Por exemplo:

- Erros de configuração na encriptação de dados
- Erros de implementação no algoritmo de encriptação
- Compromisso de uma única chave de encriptação

O Azure fornece encriptação dupla para dados em repouso e dados em trânsito.

## <a name="data-at-rest"></a>Dados inativos
A abordagem da Microsoft para permitir duas camadas de encriptação para dados em repouso é:

- **Encriptação do disco utilizando chaves geridas pelo cliente**. Fornece a sua própria chave para encriptação de discos. Pode trazer as suas próprias chaves para o seu Key Vault (BYOK – Bring Your Own Key), ou gerar novas chaves no Cofre da Chave Azure para encriptar os recursos desejados.
- **Encriptação de infraestrutura utilizando teclas geridas pela plataforma**.  Por predefinição, os discos são automaticamente encriptados em repouso utilizando chaves de encriptação geridas pela plataforma.

## <a name="data-in-transit"></a>Dados em trânsito
A abordagem da Microsoft para permitir duas camadas de encriptação para dados em trânsito é:

- **Encriptação de trânsito utilizando a Segurança da Camada de Transporte (TLS) 1.2 para proteger os dados quando viaja entre os serviços de cloud e você**. Todo o tráfego que sai de um datacenter é encriptado em trânsito, mesmo que o destino de tráfego seja outro controlador de domínio na mesma região. TLS 1.2 é o protocolo de segurança predefinido utilizado. O TLS proporciona uma autenticação forte, privacidade e integridade da mensagem (permitindo a deteção de adulteração de mensagens, interceção e falsificação), interoperabilidade, flexibilidade de algoritmos e facilidade de implantação e utilização.
- **Camada adicional de encriptação fornecida na camada de infraestrutura.** Um método de encriptação da camada de ligação de dados utilizando as Normas de Segurança MAC IEEE 802.1AE (também conhecidas como MACsec) é aplicado de ponto a ponto através do hardware de rede subjacente. Sempre que o tráfego do Cliente Azure se move entre datacenters-- fora dos limites físicos não controlados pela Microsoft (ou em nome da Microsoft)-- Os pacotes são encriptados e desencriptados nos dispositivos antes de serem enviados, evitando ataques físicos "homem-no-meio" ou ataques de snooping/escutas telefónicas. Como esta tecnologia está integrada no próprio hardware de rede, fornece encriptação de taxa de linha no hardware da rede sem aumento de latência de ligação mensurável. Esta encriptação do MACsec está em padrão para todo o tráfego Azure que viaja dentro de uma região ou entre regiões, e não é necessária nenhuma ação por parte dos clientes para permitir.

## <a name="next-steps"></a>Passos seguintes
Saiba como [a encriptação é usada no Azure.](encryption-overview.md)
