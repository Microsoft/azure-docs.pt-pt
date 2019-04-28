---
title: O que é dedicado HSM? -Azure dedicado por HSM | Documentos da Microsoft
description: Descrição geral de HSM dedicados do Azure fornece recursos de armazenamento de chaves no Azure que cumpra FIPS 140-2 nível 3 certificação
services: dedicated-hsm
author: barclayn
manager: barbkess
tags: azure-resource-manager
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: overview
ms.custom: mvc, seodec18
ms.date: 12/07/2018
ms.author: barclayn
ms.openlocfilehash: 88a49c9c124c5399749d2b60595d7f5c7ec77b20
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 04/23/2019
ms.locfileid: "62117997"
---
# <a name="what-is-azure-dedicated-hsm"></a>O que é o Azure Dedicated HSM?

HSM dedicada do Azure é um serviço do Azure que fornece armazenamento de chaves criptográfico no Azure. HSM dedicados cumpre os requisitos de segurança mais exigentes. É a solução ideal para clientes que requerem FIPS 140-2 nível dispositivos validados 3 e controle completo e exclusivo do dispositivo de HSM. 

 Dispositivos HSM são implementados globalmente em várias regiões do Azure. Podem ser facilmente aprovisionados como um par de dispositivos e configuradas para elevada disponibilidade. Também podem ser aprovisionados dispositivos HSM em várias regiões, para garantir em relação de ativação pós-falha do nível regional. Microsoft oferece o serviço de HSM dedicados através da utilização a [SafeNet Luna rede HSM 7 (modelo A790)](https://safenet.gemalto.com/data-encryption/hardware-security-modules-hsms/safenet-network-hsm/) appliance da Gemalto. Este dispositivo oferece os mais altos níveis de desempenho e opções de integração de criptografia. 

Depois que eles são provisionados, HSM dispositivos ligados diretamente à rede virtual de um cliente. Também podem ser acedidos por ferramentas de gerenciamento e de aplicativos no local ao configurar a conectividade VPN ponto a site ou site a site. Os clientes obter o software e documentação para configurar e gerir dispositivos HSM a partir do portal de suporte da Gemalto.

## <a name="why-use-azure-dedicated-hsm"></a>Porquê utilizar o HSM do Azure dedicado?

### <a name="fips-140-2-level-3-compliance"></a>Conformidade do FIPS 140-2 nível 3

Muitas organizações têm regulamentos da indústria rigorosas que exigem que cumpre o armazenamento de chaves criptográfico [FIPS 140-2 nível-3](https://csrc.nist.gov/publications/detail/fips/140/2/final) requisitos. Um serviço multi-inquilino do Azure Key Vault da Microsoft apenas fornece atualmente FIPS 140-2 nível 2 certificação. Azure HSM dedicados atende uma necessidade real de setor de serviços financeiros, agências governamentais e outras pessoas que têm de cumprir FIPS 140-2 nível 3 requisitos.

### <a name="single-tenant-devices"></a>Dispositivos de inquilino único

Muitos dos nossos clientes têm um requisito para inquilinos de único dispositivo de armazenamento de criptografia. O serviço de HSM dedicados do Azure permite-lhes para aprovisionar um dispositivo físico a partir de um dos centros de dados distribuídos globalmente da Microsoft. Depois de estar aprovisionada para um cliente, apenas que o cliente pode aceder ao dispositivo.

### <a name="full-administrative-control"></a>Controle administrativo completo

Muitos clientes exigem controle administrativo completo e único acesso ao respetivo dispositivo para fins administrativos. Depois de um dispositivo é aprovisionado, apenas o cliente tem acesso administrativo ou nível de aplicativo no dispositivo.

 A Microsoft não tem nenhum controle administrativo depois do cliente acede ao dispositivo pela primeira vez, altura em que o cliente altera a palavra-passe. A partir desse ponto, o cliente tem um inquilino único verdadeiro com o controle administrativo completo e a capacidade de gestão de aplicações. A Microsoft manter o acesso de nível de monitor (não uma função de administrador) para a telemetria através de ligação da porta serial. Este acesso aborda os monitores de hardware, tais como temperatura, o estado de funcionamento de fonte de alimentação de energia e o estado de funcionamento do ventoinha. 
 
 O cliente está livre para desativar esta monitorização necessário. No entanto, se eles desativá-la, eles não irão receber alertas de estado de funcionamento proativa da Microsoft.

### <a name="high-performance"></a>Elevado desempenho

O dispositivo da Gemalto foi selecionado para este serviço para uma variedade de motivos. Ele oferece uma ampla gama de suporte de algoritmos criptográficos, uma variedade de sistemas operativos suportados e um amplo suporte de API. O modelo específico é implementado oferece um desempenho excelente com 10.000 operações por segundo para RSA 2048. Ele oferece suporte a 10 partições que podem ser utilizadas para instâncias de aplicação único. Este dispositivo é uma latência baixa, alta capacidade e dispositivos de alto débito.

### <a name="unique-cloud-based-offering"></a>Oferta com base na cloud exclusiva

Microsoft reconhecido uma necessidade específica de um conjunto exclusivo de clientes. É o único fornecedor de cloud que oferece um serviço HSM dedicado, que é o nível FIPS 140-2 validados 3 e oferece desse tipo de certa forma de baseado na nuvem e integração de aplicações no local de novos clientes.

## <a name="is-azure-dedicated-hsm-right-for-you"></a>É o HSM do Azure dedicado adequado para si?

HSM dedicada do Azure é um serviço especializado que reflete os requisitos exclusivos para um tipo específico de organização de grande escala. Como resultado, é esperado que a maior parte dos clientes do Azure não se ajustará o perfil de utilização para este serviço. Muitos irão encontrar o serviço Azure Key Vault para ser mais adequado e económico. Para ajudar a decidir se é uma opção para os seus requisitos, identificámos os seguintes critérios.

### <a name="best-fit"></a>Ajuste automático

HSM dedicada do Azure é mais adequado a cenários de "lift-and-shift", que requerem acesso direto e único aos dispositivos HSM. Os exemplos incluem:

- Migração de aplicações no local às máquinas de virtuais do Azure
- Migrando aplicativos do Amazon AWS EC2 para máquinas virtuais que utilizam o serviço clássico de HSM da Cloud de AWS (Amazon não oferece este serviço para novos clientes)
- Execução de software compactado, como Apache/Ngnix descarga de SSL, Oracle TDE e ADCS em máquinas de virtuais do Azure 

### <a name="not-a-fit"></a>Não é uma opção

HSM dedicada do Azure não é uma boa opção para o seguinte tipo de cenário: Serviços cloud da Microsoft que suporte a encriptação com chaves geridas pelo cliente (por exemplo, o Azure Information Protection, Azure Disk Encryption, Azure Data Lake Store, armazenamento do Azure, base de dados do Azure SQL e chave do cliente para o Office 365) que não estão integradas HSM dedicados do Azure.

### <a name="it-depends"></a>Depende

Se o HSM do Azure dedicado funcionará para depende de uma combinação potencialmente complexa de requisitos e comprometimentos que pode ou não pode fazer. Um exemplo é o requisito de nível 3 FIPS 140-2. Este requisito é comum e dedicado HSM está atualmente a única opção para atender ele. Se estes requisitos estabelecidos não são relevantes, em seguida, muitas vezes é uma escolha entre o Azure Key Vault e o HSM dedicados. Avalie os requisitos antes de tomar uma decisão.

Situações em que terá de avaliar as opções incluem: 

- Novo código em execução na máquina de virtual de um cliente do Azure
- SQL Server TDE numa máquina virtual do Azure
- Encriptação do lado do cliente de armazenamento do Azure
- SQL Server e BD SQL do Azure Always encriptada

## <a name="next-steps"></a>Passos Seguintes

Este é um serviço altamente especializado. Por conseguinte, recomendamos que compreende perfeitamente os conceitos-chave neste conjunto de documentação, incluindo preços, suporte e contratos de nível de serviço. 

O [guias de integração da Gemalto](https://safenet.gemalto.com/partners/microsoft/) a facilitar o aprovisionamento de HSMs num ambiente de rede virtual existente. Existem também são guias de procedimentos para ajudá-lo a determinar como configurar a sua arquitetura de implantação.

* [Elevada disponibilidade](high-availability.md)
* [segurança física](physical-security.md)
* [Redes](networking.md)
* [Capacidade de suporte](supportability.md)
* [Monitorização](monitoring.md)
