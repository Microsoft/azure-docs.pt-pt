---
title: O que é dedicado HSM? -Azure dedicado por HSM | Documentos da Microsoft
description: Descrição geral de HSM dedicados do Azure fornece recursos de armazenamento de chaves no Azure que cumpra FIPS 140-2 nível 3 certificação
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
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881073"
---
# <a name="what-is-azure-dedicated-hsm"></a>O que é o Azure Dedicated HSM?

HSM dedicada do Azure é um serviço do Azure que fornece armazenamento de chaves criptográfico no Azure. HSM dedicados cumpre os requisitos de segurança mais exigentes. É a solução ideal para clientes que exigem dispositivos com validação FIPS 140-2 nível 3 e controle completo e exclusivo do dispositivo HSM. 

 Os dispositivos HSM são implantados globalmente em várias regiões do Azure. Eles podem ser facilmente provisionados como um par de dispositivos e configurados para alta disponibilidade. Os dispositivos HSM também podem ser provisionados entre regiões para garantir o failover de nível regional. A Microsoft fornece o serviço HSM dedicado usando o dispositivo [SafeNet Luna Network HSM 7 (Model A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) da Gemalto. Este dispositivo oferece os mais altos níveis de desempenho e opções de integração de criptografia. 

Depois que eles são provisionados, os dispositivos HSM são conectados diretamente à rede virtual de um cliente. Eles também podem ser acessados por ferramentas de gerenciamento e aplicativos locais quando você configura a conectividade de VPN ponto a site ou site a site. Os clientes obtêm o software e a documentação para configurar e gerenciar dispositivos HSM no portal de suporte do Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Por que usar o HSM dedicado do Azure?

### <a name="fips-140-2-level-3-compliance"></a>Conformidade do FIPS 140-2 nível-3

Muitas organizações têm regulamentações rigorosas do setor que ditam que o armazenamento de chaves de criptografia atende aos requisitos [FIPS 140-2 nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) . Atualmente, o serviço de Azure Key Vault multilocatário da Microsoft fornece apenas certificação FIPS 140-2 nível-2. O HSM dedicado do Azure atende a uma necessidade real para o setor de serviços financeiros, agências governamentais e outros que devem atender aos requisitos FIPS 140-2 nível-3.

### <a name="single-tenant-devices"></a>Dispositivos de locatário único

Muitos dos nossos clientes têm um requisito para inquilinos de único dispositivo de armazenamento de criptografia. O serviço HSM dedicado do Azure permite que eles provisionem um dispositivo físico de um dos data centers distribuídos globalmente da Microsoft. Depois que ele é provisionado para um cliente, somente esse cliente pode acessar o dispositivo.

### <a name="full-administrative-control"></a>Controle administrativo completo

Muitos clientes exigem controle administrativo total e acesso exclusivo ao seu dispositivo para fins administrativos. Depois que um dispositivo é provisionado, somente o cliente tem acesso administrativo ou em nível de aplicativo ao dispositivo.

 A Microsoft não tem controle administrativo depois que o cliente acessa o dispositivo pela primeira vez e, nesse ponto, o cliente altera a senha. A partir desse ponto, o cliente é um verdadeiro locatário único com controle administrativo completo e capacidade de gerenciamento de aplicativos. A Microsoft mantém o acesso no nível do monitor (não uma função de administrador) para telemetria por meio da conexão de porta serial. Esse acesso abrange monitores de hardware, como temperatura, integridade da fonte de energia e integridade do ventilador. 
 
 O cliente é livre para desabilitar esse monitoramento necessário. No entanto, se eles o desabilitarem, eles não receberão alertas proativos de integridade da Microsoft.

### <a name="high-performance"></a>Elevado desempenho

O dispositivo Gemalto foi selecionado para esse serviço por vários motivos. Ele oferece uma ampla gama de suporte a algoritmos criptográficos, uma variedade de sistemas operacionais com suporte e amplo suporte a API. O modelo específico que é implantado oferece excelente desempenho com 10.000 operações por segundo para RSA-2048. Ele oferece suporte a 10 partições que podem ser utilizadas para instâncias de aplicação único. Este dispositivo é um dispositivo de baixa latência, alta capacidade e alta taxa de transferência.

### <a name="unique-cloud-based-offering"></a>Oferta exclusiva baseada em nuvem

A Microsoft reconheceu uma necessidade específica para um conjunto exclusivo de clientes. É o único provedor de nuvem que oferece novos clientes um serviço HSM dedicado que é o FIPS 140-2 nível 3 validado e oferece tal extensão de integração de aplicativos locais e baseados em nuvem.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>É o HSM do Azure dedicado adequado para si?

O HSM dedicado do Azure é um serviço especializado que resolve requisitos exclusivos para um tipo específico de organização em grande escala. Como resultado, espera-se que a maior parte dos clientes do Azure não se ajuste ao perfil de uso para esse serviço. Muitos acharão que o serviço Azure Key Vault ser mais apropriado e econômico. Para ajudá-lo a decidir se ele é adequado para seus requisitos, identificamos os critérios a seguir.

### <a name="best-fit"></a>Melhor ajuste

O HSM dedicado do Azure é mais adequado para cenários "de" deslocamento "que exigem acesso direto e único a dispositivos HSM. Os exemplos incluem:

- Migração de aplicações no local às máquinas de virtuais do Azure
- Migrar aplicativos do Amazon AWS EC2 para máquinas virtuais que usam o serviço clássico de Cloud HSM do AWS (a Amazon não está oferecendo esse serviço a novos clientes)
- Executando softwares encapsulados de redução, como o descarregamento de SSL do Apache/Ngnix, Oracle TDE e ADCS em máquinas virtuais do Azure 

### <a name="not-a-fit"></a>Não é um ajuste

O HSM dedicado do Azure não é uma boa opção para o seguinte tipo de cenário: Serviços em nuvem da Microsoft que dão suporte à criptografia com chaves gerenciadas pelo cliente (como a proteção de informações do Azure, Azure Disk Encryption, Azure Data Lake Store, armazenamento do Azure, banco de dados SQL do Azure e chave do cliente para o Office 365) que não estão integradas ao HSM dedicado do Azure.

### <a name="it-depends"></a>Depende

Se o HSM dedicado do Azure funcionará para você depende de uma combinação potencialmente complexa de requisitos e comprometimentos que você pode ou não fazer. Um exemplo é o requisito de nível 3 de FIPS 140-2. Esse requisito é comum e o HSM dedicado atualmente é a única opção para atender a ele. Se esses requisitos obrigatórios não forem relevantes, em geral, é uma opção entre Azure Key Vault e HSM dedicado. Avalie seus requisitos antes de tomar uma decisão.

As situações em que você precisará avaliar suas opções incluem: 

- Novo código em execução na máquina virtual do Azure de um cliente
- SQL Server TDE em uma máquina virtual do Azure
- Encriptação do lado do cliente de armazenamento do Azure
- SQL Server e BD SQL do Azure Always encriptada

## <a name="next-steps"></a>Passos Seguintes

Esse é um serviço altamente especializado. Portanto, recomendamos que você entenda totalmente os principais conceitos deste conjunto de documentação, incluindo preços, suporte e contratos de nível de serviço. 

Os [guias de integração do Gemalto](https://safenet.gemalto.com/partners/microsoft/) ajudam a facilitar o provisionamento de HSMs em um ambiente de rede virtual existente. Também há guias de instruções para ajudá-lo a determinar como configurar sua arquitetura de implantação.

* [Elevada disponibilidade](high-availability.md)
* [Segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitorização](monitoring.md)
