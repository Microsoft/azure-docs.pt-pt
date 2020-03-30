---
title: O que é o Dedicated HSM? - Azure Dedicado HSM [ HSM ] Microsoft Docs
description: Visão geral do Azure Dedicado HSM fornece principais capacidades de armazenamento dentro do Azure que cumpre a certificação FIPS 140-2 Nível 3
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: mbaldwin
ms.openlocfilehash: dd5ce117645ef2b368bbf8f0e441770d6e746b5b
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2020
ms.locfileid: "70881073"
---
# <a name="what-is-azure-dedicated-hsm"></a>O que é o Azure Dedicated HSM?

O Azure Dedicated HSM é um serviço Azure que fornece armazenamento de chaves criptográficas em Azure. O HSM dedicado cumpre os requisitos de segurança mais rigorosos. É a solução ideal para clientes que necessitam de dispositivos validados por 140-2 nível 3 e controlo completo e exclusivo do aparelho HSM. 

 Os dispositivos HSM são implantados globalmente em várias regiões do Azure. Podem ser facilmente provisionados como um par de dispositivos e configurados para alta disponibilidade. Os dispositivos HSM também podem ser provisionados em todas as regiões para garantir contra a falha a nível regional. A Microsoft fornece o serviço HSM dedicado utilizando o aparelho [SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) da Gemalto. Este dispositivo oferece os mais altos níveis de desempenho e opções de integração criptográfica. 

Depois de aprovisionados, os dispositivos HSM estão ligados diretamente à rede virtual de um cliente. Também podem ser acedidos através de ferramentas de aplicação e gestão no local quando configurar a conectividade VPN ponto-a-site ou site-to-site. Os clientes obtêm o software e documentação para configurar e gerir dispositivos HSM a partir do portal de suporte da Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Porquê usar o Azure Dedicado HSM?

### <a name="fips-140-2-level-3-compliance"></a>FIPS 140-2 Conformidade nível-3

Muitas organizações têm regulamentos rigorosos do setor que ditam que o armazenamento de chaves criptográficas cumpre os requisitos [de Nível 3 do FIPS 140-2.](https://csrc.nist.gov/publications/detail/fips/140/2/final) O serviço azure key vault multi-inquilino da Microsoft atualmente apenas fornece certificação FIPS 140-2 Level-2. O Azure Dedicado HSM satisfaz uma necessidade real para a indústria de serviços financeiros, agências governamentais e outros que devem satisfazer os requisitos do NÍvel 140-3 do FIPS.

### <a name="single-tenant-devices"></a>Dispositivos de inquilino único

Muitos dos nossos clientes têm um requisito para um único arrendamento do dispositivo de armazenamento criptográfico. O serviço Azure Dedicated HSM permite-lhes fornecer um dispositivo físico a partir de um dos centros de dados distribuídos globalmente pela Microsoft. Depois de ser provisionado a um cliente, só que o cliente pode aceder ao dispositivo.

### <a name="full-administrative-control"></a>Controlo administrativo total

Muitos clientes requerem controlo administrativo total e acesso exclusivo ao seu dispositivo para fins administrativos. Depois de aprovisionado um dispositivo, apenas o cliente tem acesso administrativo ou de nível de aplicação ao dispositivo.

 A Microsoft não tem controlo administrativo depois de o cliente aceder ao dispositivo pela primeira vez, altura em que o cliente altera a palavra-passe. A partir daí, o cliente é um verdadeiro inquilino único com total controlo administrativo e capacidade de gestão de aplicações. A Microsoft mantém o acesso ao nível do monitor (não uma função de administração) para telemetria através da ligação por porta em série. Este acesso abrange monitores de hardware tais como temperatura, saúde de alimentação e saúde dos ventiladores. 
 
 O cliente é livre de desativar esta monitorização necessária. No entanto, se o desativarem, não receberão alertas de saúde pró-ativos da Microsoft.

### <a name="high-performance"></a>Elevado desempenho

O dispositivo Gemalto foi selecionado para este serviço por uma variedade de razões. Oferece uma ampla gama de suporte criptográfico de algoritmos, uma variedade de sistemas operativos suportados e amplo suporte a API. O modelo específico que foi implementado oferece um excelente desempenho com 10.000 operações por segundo para rSA-2048. Suporta 10 divisórias que podem ser usadas para instâncias de aplicação únicas. Este dispositivo é um dispositivo de baixa latência, alta capacidade e alta carga.

### <a name="unique-cloud-based-offering"></a>Oferta única baseada em nuvem

A Microsoft reconheceu uma necessidade específica de um conjunto único de clientes. É o único fornecedor de nuvem que oferece aos novos clientes um serviço Dedicado HSM que é validado por 140-2 Nível 3 e oferece uma extensão de integração de aplicações baseada na nuvem e no local.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>O Azure Dedicated HSM é o certo para si?

O Azure Dedicated HSM é um serviço especializado que aborda requisitos únicos para um tipo específico de organização em larga escala. Como resultado, espera-se que a maior parte dos clientes do Azure não se encaixe no perfil de utilização deste serviço. Muitos acharão o serviço Azure Key Vault mais apropriado e rentável. Para ajudá-lo a decidir se é adequado para os seus requisitos, identificamos os seguintes critérios.

### <a name="best-fit"></a>Melhor ajuste

O Azure Dedicado HSM é mais adequado para cenários de "elevação e mudança" que requerem acesso direto e exclusivo aos dispositivos HSM. Os exemplos incluem:

- Aplicações migratórias de instalações para máquinas virtuais azure
- Aplicações migratórias da Amazon AWS EC2 para máquinas virtuais que usam o serviço AWS Cloud HSM Classic (a Amazon não está a oferecer este serviço a novos clientes)
- Funcionamento de software embrulhado em encolhe, tais como Apache/Ngnix SSL Offload, Oracle TDE e ADCS em Máquinas Virtuais Azure 

### <a name="not-a-fit"></a>Não é um ajuste

O Azure Dedicado HSM não é um bom ajuste para o seguinte tipo de cenário: serviços na nuvem da Microsoft que suportam encriptação com chaves geridas pelo cliente (como Proteção de Informação Azure, Encriptação de Discos Azure, Azure Data Lake Store, Armazenamento Azure, Azure SQL Base de Dados e Chave de Cliente para o Office 365) que não estão integrados com o Azure Dedicado HSM.

### <a name="it-depends"></a>Depende

Se o Azure Dedicated HSM trabalhará para si depende de uma mistura potencialmente complexa de requisitos e compromissos que pode ou não fazer. Um exemplo é o requisito de Nível 3 do FIPS 140-2. Este requisito é comum, e o HSM dedicado é atualmente a única opção para o cumprir. Se estes requisitos mandatados não são relevantes, então muitas vezes é uma escolha entre azure key vault e HSM dedicado. Avalie os seus requisitos antes de tomar uma decisão.

As situações em que terá de ponderar as suas opções incluem: 

- Novo código em execução na máquina virtual Azure de um cliente
- SQL Server TDE em uma máquina virtual Azure
- Encriptação do lado do cliente do Azure Storage
- SQL Server e Azure SQL DB sempre encriptados

## <a name="next-steps"></a>Passos seguintes

Este é um serviço altamente especializado. Por isso, recomendamos que compreenda plenamente os conceitos-chave deste conjunto de documentação, incluindo preços, apoio e acordos de nível de serviço. 

Os guias de [integração Gemalto](https://safenet.gemalto.com/partners/microsoft/) ajudam-no a facilitar o fornecimento de HSMs para um ambiente de rede virtual existente. Há também guias de como ajudá-lo a determinar como configurar a sua arquitetura de implantação.

* [Elevada disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
