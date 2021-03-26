---
title: O que é o Dedicated HSM? - Azure Dedicado HSM | Microsoft Docs
description: Saiba como o Azure Dedicated HSM é um serviço Azure que fornece armazenamento de chaves criptográficas em Azure.
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
ms.date: 03/25/2021
ms.author: keithp
ms.openlocfilehash: 0e07839c3c5ce542335eeadc92e6a3c98fe87856
ms.sourcegitcommit: 73d80a95e28618f5dfd719647ff37a8ab157a668
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/26/2021
ms.locfileid: "105606985"
---
# <a name="what-is-azure-dedicated-hsm"></a>O que é o Azure Dedicated HSM?

Azure Dedicated HSM é um serviço Azure que fornece armazenamento de chaves criptográficas em Azure. O HSM dedicado cumpre os mais rigorosos requisitos de segurança. É a solução ideal para clientes que necessitam de dispositivos validados FIPS 140-2 Nível 3 e controlo completo e exclusivo do aparelho HSM. 

 Os dispositivos HSM são implantados globalmente em várias regiões de Azure. Podem ser facilmente a provisionados como um par de dispositivos e configurados para uma elevada disponibilidade. Os dispositivos HSM também podem ser a provisionados em todas as regiões para garantir contra o fracasso a nível regional. A Microsoft fornece o serviço dedicado HSM utilizando os [eletrodomésticos A790 do modelo Thales Luna 7 HSM.](https://cpl.thalesgroup.com/encryption/hardware-security-modules/network-hsms) Este dispositivo oferece os mais altos níveis de desempenho e opções de integração criptográfica. 

Depois de serem a provisionados, os dispositivos HSM estão ligados diretamente à rede virtual de um cliente. Também podem ser acedidos através de aplicações e ferramentas de gestão no local quando configurar conectividade ponto-a-local ou VPN local.a-local. Os clientes obtêm o software e documentação para configurar e gerir dispositivos HSM do portal de [suporte ao cliente Thales.](https://supportportal.thalesgroup.com/csm)

## <a name="why-use-azure-dedicated-hsm"></a>Porquê usar o HSM dedicado a Azure?

### <a name="fips-140-2-level-3-compliance"></a>Conformidade FIPS 140-2 Nível-3

Muitas organizações têm regulamentos rigorosos da indústria que ditam que as chaves criptográficas devem ser armazenadas em HSMs validados [fips 140-2 Nível-3.](https://csrc.nist.gov/publications/detail/fips/140/2/final) A Azure Dedicado HSM e uma nova oferta de inquilino único, [Azure Key Vault Managed HSM (pré-visualização),](https://docs.microsoft.com/azure/key-vault/managed-hsm)ajudam clientes de vários segmentos da indústria, tais como indústria de serviços financeiros, agências governamentais, e outros cumprem os requisitos fips 140-2 Nível-3. Enquanto o serviço [de abóbada](https://docs.microsoft.com/azure/key-vault) chave de multi-inquilino da Microsoft utiliza atualmente HSMs validados FIPS 140-2 Nível-2. 

### <a name="single-tenant-devices"></a>Dispositivos de inquilino único

Muitos dos nossos clientes têm um requisito para um único arrendamento do dispositivo de armazenamento criptográfico. O serviço HSM dedicado a Azure permite-lhes a provisionar um dispositivo físico a partir de um dos centros de dados distribuídos globalmente pela Microsoft. Depois de ser a provisionado a um cliente, só o cliente pode aceder ao dispositivo.

### <a name="full-administrative-control"></a>Controlo administrativo total

Muitos clientes necessitam de controlo administrativo total e acesso exclusivo ao seu dispositivo para fins administrativos. Depois de um dispositivo ser a provisionado, apenas o cliente tem acesso administrativo ou ao nível da aplicação ao dispositivo.

 A Microsoft não tem controlo administrativo depois de o cliente aceder ao dispositivo pela primeira vez, altura em que o cliente altera a palavra-passe. A partir daí, o cliente é um verdadeiro inquilino único com total capacidade de controlo administrativo e de gestão de aplicações. A Microsoft mantém o acesso ao nível do monitor (não um papel administrativo) para a telemetria através da ligação por porta em série. Este acesso abrange monitores de hardware tais como temperatura, saúde de alimentação e saúde dos ventiladores. 
 
 O cliente é livre de desativar esta monitorização necessária. No entanto, se o desativarem, não receberão alertas de saúde proactivos da Microsoft.

### <a name="high-performance"></a>Elevado desempenho

O dispositivo Thales foi selecionado para este serviço por uma variedade de razões. Oferece uma ampla gama de suporte a algoritmos criptográficos, uma variedade de sistemas operativos suportados e um amplo suporte api. O modelo específico que é implementado oferece um excelente desempenho com 10.000 operações por segundo para o RSA-2048. Suporta 10 divisórias que podem ser usadas para instâncias de aplicação únicas. Este dispositivo é um dispositivo de baixa latência, alta capacidade e alta potência.

### <a name="unique-cloud-based-offering"></a>Oferta única baseada em nuvem

A Microsoft reconheceu uma necessidade específica de um conjunto único de clientes. É o único fornecedor de nuvem que oferece aos novos clientes um serviço dedicado ao HSM que é validado pelo FIPS 140-2 Nível 3 e oferece uma extensão tão grande de integração de aplicações baseadas na nuvem e no local.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>O Azure Dedicado HSM está certo para si?

O Azure Dedicated HSM é um serviço especializado que aborda requisitos únicos para um tipo específico de organização em larga escala. Como resultado, espera-se que a maior parte dos clientes da Azure não se encaixem no perfil de utilização deste serviço. Muitos acharão o serviço Azure Key Vault mais apropriado e rentável. Para ajudá-lo a decidir se é adequado para os seus requisitos, identificamos os seguintes critérios.

### <a name="best-fit"></a>Melhor ajuste

O HSM dedicado a Azure é o mais adequado para cenários de "elevação e mudança" que requerem acesso direto e exclusivo aos dispositivos HSM. Os exemplos incluem:

- Aplicações migratórias de máquinas virtuais Azure para Azure
- Aplicações migratórias da Amazon AWS EC2 para máquinas virtuais que utilizam o serviço AWS Cloud HSM Classic (a Amazon não está a oferecer este serviço a novos clientes)
- Executando software embrulhado em redução como Apache/Ngnix SSL Offload, Oracle TDE e ADCS em Azure Virtual Machines 

### <a name="not-a-fit"></a>Não é um ajuste

O Azure Dedicated HSM não é um bom ajuste para o seguinte tipo de cenário: os serviços em nuvem da Microsoft que suportam a encriptação com chaves geridas pelo cliente (tais como Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, Azure Storage, Azure SQL Database e Customer Key for Office 365) que não estão integrados com Azure Dedicated HSM.

### <a name="it-depends"></a>Depende

Se o Azure Dedicado HSM funcionará para si depende de uma mistura potencialmente complexa de requisitos e compromissos que pode ou não fazer. Um exemplo é o requisito FIPS 140-2 Nível 3. Este requisito é comum, e Azure Dedicado HSM e uma nova oferta de inquilino único, [Azure Key Vault Managed HSM (pré-visualização)](https://docs.microsoft.com/azure/key-vault/managed-hsm) são atualmente as únicas opções para o cumprir. Se estes requisitos obrigatórios não são relevantes, então muitas vezes é uma escolha entre Azure Key Vault e Azure Dedicado HSM. Avalie os seus requisitos antes de tomar uma decisão.

As situações em que terá de ponderar as suas opções incluem: 

- Novo código em execução na máquina virtual Azure de um cliente
- SQL Server TDE em uma máquina virtual Azure
- Encriptação do lado do cliente do Azure Storage
- SQL Server e Azure SQL DB Sempre Encriptado

## <a name="next-steps"></a>Passos seguintes

Este é um serviço altamente especializado. Por isso, recomendamos que compreenda plenamente os conceitos-chave deste conjunto de documentação, incluindo preços, suporte e acordos de nível de serviço. 

Os [guias de integração do Thales](https://cpl.thalesgroup.com/partners/overview) ajudam-no a facilitar o fornecimento de HSMs num ambiente de rede virtual existente. Existem também guias para ajudá-lo a determinar como configurar a sua arquitetura de implantação.

* [Elevada disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Rede](networking.md)
* [Suportabilidade](supportability.md)
* [Monitorização](monitoring.md)
