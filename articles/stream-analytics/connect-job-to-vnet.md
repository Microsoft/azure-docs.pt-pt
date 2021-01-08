---
title: Conecte os trabalhos do Stream Analytics aos recursos numa Rede Virtual Azure (VNET)
description: Este artigo descreve como ligar um trabalho do Azure Stream Analytics com recursos que estão num VNET.
author: sidramadoss
ms.author: sidram
ms.service: stream-analytics
ms.topic: conceptual
ms.date: 01/04/2021
ms.custom: devx-track-js
ms.openlocfilehash: 99563760bf37c4046e7dd81e779fedbe415380bc
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 01/08/2021
ms.locfileid: "98019487"
---
# <a name="connect-stream-analytics-jobs-to-resources-in-an-azure-virtual-network-vnet"></a>Conecte os trabalhos do Stream Analytics a recursos numa Rede Virtual Azure (VNet)

Os seus trabalhos stream Analytics fazem ligações de saída à sua entrada e produção Recursos Azure para processar dados em tempo real e produzir resultados. Estes recursos de entrada e saída (por exemplo, Azure Event Hubs e Azure SQL Database) podem estar por trás de uma firewall Azure ou numa Rede Virtual Azure (VNet). O serviço Stream Analytics opera a partir de redes que não podem ser incluídas diretamente nas suas regras de rede.

No entanto, existem duas formas de ligar de forma segura os seus trabalhos stream Analytics aos seus recursos de entrada e produção nesses cenários.
* Utilizando pontos finais privados em clusters Stream Analytics.
* Utilização do modo de autenticação de identidade gerida juntamente com a definição de rede "Permitir serviços fidedignos".

O seu trabalho stream Analytics não aceita qualquer ligação de entrada.

## <a name="private-endpoints-in-stream-analytics-clusters"></a>Pontos finais privados em clusters Stream Analytics.
[Stream Analytics clusters](https://docs.microsoft.com/azure/stream-analytics/cluster-overview) é um único cluster de computação dedicado a inquilinos onde você pode executar seus trabalhos Stream Analytics. Pode criar pontos finais privados geridos no seu cluster Stream Analytics, o que permite que quaisquer trabalhos em execução no seu cluster façam uma ligação de saída segura aos seus recursos de entrada e saída.

A criação de pontos finais privados no seu cluster Stream Analytics é uma [operação de dois passos.](https://docs.microsoft.com/azure/stream-analytics/private-endpoints) Esta opção é mais adequada para cargas de trabalho de streaming médias a grandes, uma vez que o tamanho mínimo de um cluster Stream Analytics é de 36 SUs (embora as 36 SUs possam ser partilhadas por diferentes empregos em várias subscrições ou ambientes como desenvolvimento, teste e produção).

## <a name="managed-identity-authentication-with-allow-trusted-services-configuration"></a>Autenticação de identidade gerida com configuração de "Permitir serviços fidedignos"
Alguns serviços Azure fornecem Permitir a definição de rede **de serviços fidedignos** da Microsoft, que, quando ativada, permite que os seus trabalhos stream Analytics se conectem de forma segura ao seu recurso utilizando uma autenticação forte. Esta opção permite-lhe ligar os seus trabalhos aos seus recursos de entrada e saída sem necessitar de um cluster Stream Analytics e pontos finais privados. Configurar o seu trabalho para usar esta técnica é uma operação de 2 etapas:
* Utilize o modo de autenticação de identidade gerida ao configurar a entrada ou saída no seu trabalho stream Analytics.
* Conceda aos seus trabalhos específicos stream Analytics acesso explícito aos seus recursos-alvo, atribuindo um papel Azure à identidade gerida pelo sistema do trabalho. 

Permitir **que serviços confiáveis** da Microsoft não conceda acesso coberto a qualquer trabalho. Isto dá-lhe o controlo total dos quais os trabalhos específicos do Stream Analytics podem aceder aos seus recursos de forma segura. 

Os seus trabalhos podem ligar-se aos seguintes serviços Azure utilizando esta técnica:
1. [Blob Storage ou Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity) - pode ser a conta de armazenamento do seu trabalho, entrada de streaming ou saída.
2. [Azure Event Hubs](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity) - pode ser a entrada ou saída de streaming do seu trabalho.

Se os seus trabalhos precisarem de se ligar a outros tipos de entrada ou saída, pode escrever de Stream Analytics para Event Hubs primeiro e depois para qualquer destino à sua escolha usando Funções Azure. Se pretender escrever diretamente do Stream Analytics para outros tipos de saída protegidos num VNet ou firewall, então a única opção é utilizar pontos finais privados em clusters Stream Analytics.

## <a name="next-steps"></a>Próximos passos

* [Criar e remover pontos finais privados em clusters Stream Analytics](https://docs.microsoft.com/azure/stream-analytics/private-endpoints)
* [Ligue-se aos Centros de Eventos num VNet utilizando a autenticação de identidade gerida](https://docs.microsoft.com/azure/stream-analytics/event-hubs-managed-identity)
* [Ligue ao armazenamento blob e à Gen2 ADLS num VNet utilizando a autenticação de identidade gerida](https://docs.microsoft.com/azure/stream-analytics/blob-output-managed-identity)
