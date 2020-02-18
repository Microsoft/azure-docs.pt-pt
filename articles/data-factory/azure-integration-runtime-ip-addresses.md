---
title: Endereços IP do Azure Integration Runtime
description: Saiba quais os endereços IP a partir de onde deve permitir o tráfego de entrada, de forma a configurar corretamente as firewalls para garantir o acesso da rede aos armazéns de dados.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: 8b1fd7b1962a4d46d38287e5faf8f1ba4cbb40bd
ms.sourcegitcommit: b8f2fee3b93436c44f021dff7abe28921da72a6d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 02/18/2020
ms.locfileid: "77423637"
---
# <a name="azure-integration-runtime-ip-addresses"></a>Endereços IP do Azure Integration Runtime

Os endereços IP que o Azure Integration Runtime utiliza depende da região onde está localizado o seu tempo de execução de integração Azure. *Todos* Os tempos de execução de integração azure que se encontram na mesma região utilizam as mesmas gamas de endereços IP.

> [!IMPORTANT]  
> Os fluxos de dados não utilizam atualmente estes IPs. 
>
> Pode utilizar estas gamas IP para execuções de Movimentos de Dados, Pipeline e Atividades Externas. Estas gamas IP podem ser utilizadas para a lista de whitelisting em lojas de dados/ Network Security Group (NSG)/ Firewalls para acesso de entrada a partir do tempo de execução da Integração Azure. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Endereços IP de integração azure: regiões específicas

Permitir o tráfego a partir dos endereços IP listados para o tempo de execução da Integração Azure na região específica de Azure onde os seus recursos estão localizados:

|                | Região              | Endereços IP                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Ásia           | Ásia Leste           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Ásia Sudeste      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Austrália      | Leste da Austrália      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Austrália Sudeste | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brasil         | Sul do Brasil        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canadá         | Canadá Central      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| Europa         | Europa do Norte        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa Ocidental         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| França         | França Central      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| Índia          | Índia Central       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japão          | Leste do Japão          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Coreia          | Coreia do Sul Central       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| África do Sul   | África do Sul Norte  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Reino Unido | Sul do Reino Unido            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Estados Unidos  | E.U.A. Central          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | E.U.A. Leste             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | E.U.A. Leste 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | LESTE DOS EUA 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | E.U.A. Centro-Norte    | 40.80.185.0/25,</br>40.80.185.128/26,</br>52.162.111.48/28   |
|                | E.U.A. Centro-Sul    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | E.U.A. Centro-Oeste     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | E.U.A. Oeste             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | E.U.A. Oeste 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | US Gov - Virginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Problema conhecido com armazenamento azure

* Ao ligar-se à conta de Armazenamento Azure, as regras da rede IP não têm qualquer efeito sobre os pedidos originários do tempo de execução da integração Azure na mesma região que a conta de armazenamento. Para mais informações, por favor [leia as informações](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)sobre este artigo. 

  Em vez disso, sugerimos a utilização de [serviços fidedignos durante a ligação ao Armazenamento Azure.](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993) 

## <a name="next-steps"></a>Passos seguintes

* [Considerações de segurança para o movimento de dados na Fábrica de Dados Azure](data-movement-security-considerations.md)
