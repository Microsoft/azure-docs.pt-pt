---
title: Modelo de operações da SAP HANA on Azure (Grandes Instâncias) [ Microsoft Docs
description: Modelo de operações da SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e147e4a5f104ca4cd1a10a776c907e3f9f1d6128
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/28/2020
ms.locfileid: "77616979"
---
# <a name="operations-model-and-responsibilities"></a>Modelo e responsabilidades de operações

O serviço prestado com o SAP HANA on Azure (Grandes Instâncias) está alinhado com os serviços Azure IaaS. Obtém-se uma instância de uma Grande Instância HANA com um sistema operativo instalado que está otimizado para o SAP HANA. Tal como acontece com os VMs Azure IaaS, a maioria das tarefas de endurecimento do SISTEMA, instalação de software adicional, instalação de HANA, operação do OS e HANA, e atualização do OS e HANA é da sua responsabilidade. A Microsoft não obriga as atualizações do OS ou as atualizações da HANA sobre si.

![Responsabilidades da SAP HANA em Azure (Grandes Instâncias)](./media/hana-overview-architecture/image2-responsibilities.png)

Como mostrado no diagrama, SAP HANA on Azure (Grandes Instâncias) é uma oferta iaaS multi-inquilino. Na maior parte das vezes, a divisão de responsabilidade está no limite da infraestrutura do OS. A Microsoft é responsável por todos os aspetos do serviço abaixo da linha do sistema operativo. Você é responsável por todos os aspetos do serviço acima da linha. O SO é da sua responsabilidade. Pode continuar a utilizar a maioria dos métodos atuais no local que pode utilizar para o cumprimento, segurança, gestão de aplicações, base e gestão de OS. Os sistemas aparecem como se estivessem na sua rede em todos os aspetos.

Este serviço está otimizado para o SAP HANA, por isso existem áreas onde precisa trabalhar com a Microsoft para utilizar as capacidades de infraestrutura subjacentes para obter melhores resultados.

A lista seguinte fornece mais detalhes sobre cada uma das camadas e as suas responsabilidades:

**Rede**: Todas as redes internas para o carimbo de grande instância executando SAP HANA. A sua responsabilidade inclui o acesso ao armazenamento, a conectividade entre as instâncias (para escala-out e outras funções), conectividade com a paisagem e conectividade com O Azure onde a camada de aplicação SAP está alojada em VMs. Também inclui conectividade WAN entre Os Centros de Dados Azure para a replicação de fins de recuperação de desastres. Todas as redes são divididas pelo arrendatário e têm qualidade de serviço aplicada.

**Armazenamento**: O armazenamento virtualizado dividido para todos os volumes necessários pelos servidores SAP HANA, bem como para instantâneos. 

**Servidores**: Os servidores físicos dedicados para executar os DBs SAP HANA atribuídos aos inquilinos. Os servidores da classe I de SKUs são abstratos de hardware. Com este tipo de servidores, a configuração do servidor é recolhida e mantida em perfis, que podem ser movidos de um hardware físico para outro hardware físico. Tal movimento (manual) de um perfil por operações pode ser comparado um pouco com a cura do serviço Azure. Os servidores da classe II SKUs não oferecem tal capacidade.

**SDDC**: O software de gestão que é usado para gerir centros de dados como entidades definidas por software. Permite à Microsoft reunir recursos para razões de escala, disponibilidade e desempenho.

**O/S:** O SISTEMA que escolher (SUSE Linux ou Red Hat Linux) que está a funcionar nos servidores. As imagens de OS com as suas informações foram fornecidas pelo fornecedor linux individual à Microsoft para executar o SAP HANA. Deve ter uma subscrição com o fornecedor Linux para a imagem específica otimizada pelo SAP HANA. É responsável pelo registo das imagens com o fornecedor de SO. 

A partir do ponto de entrega da Microsoft, é responsável por qualquer nova correção do sistema operativo Linux. Este patching inclui pacotes adicionais que podem ser necessários para uma instalação bem sucedida do SAP HANA e que não foram incluídos pelo fornecedor linux específico nas suas imagens sap HANA otimizadas. (Para mais informações, consulte a documentação de instalação HANA da SAP e as notas SAP.) 

É responsável pela correção de OS devido a mau funcionamento ou otimização do SISTEMA e dos seus controladores em relação ao hardware específico do servidor. Também é responsável pela segurança ou correção funcional do Sistema Operativo. 

A sua responsabilidade também inclui monitorização e planeamento de capacidades de:

- Consumo de recursos da CPU.
- Consumo de memória.
- Volumes de disco relacionados com espaço livre, IOPS e latência.
- Tráfego de volume de rede entre hana grande instância e a camada de aplicação SAP.

A infraestrutura subjacente da HANA Large Instance fornece funcionalidade para cópia de segurança e restauro do volume de SO. Usar esta funcionalidade também é da sua responsabilidade.

**Middleware**: O Caso SAP HANA, principalmente. Administração, operações e monitorização são da sua responsabilidade. Pode utilizar a funcionalidade fornecida para utilizar instantâneos de armazenamento para fins de backup e restauro e recuperação de desastres. Estas capacidades são fornecidas pela infraestrutura. As suas responsabilidades incluem também a conceção de alta disponibilidade ou recuperação de desastres com estas capacidades, alavancando-as e monitorizando para determinar se os instantâneos de armazenamento executados com sucesso.

**Dados**: Os seus dados geridos pelo SAP HANA e outros dados, tais como ficheiros de backup localizados em volumes ou partilhas de ficheiros. As suas responsabilidades incluem monitorizar o espaço livre do disco e gerir o conteúdo nos volumes. Também é responsável pela monitorização da execução bem sucedida de cópias de segurança de volumes de discos e instantâneos de armazenamento. A execução bem sucedida da replicação de dados para sites de recuperação de desastres é da responsabilidade da Microsoft.

**Candidaturas:** As instâncias de aplicação SAP ou, no caso de aplicações não SAP, a camada de aplicação dessas aplicações. As suas responsabilidades incluem implantação, administração, operações e monitorização dessas aplicações. É responsável pelo planeamento da capacidade do consumo de recursos cpu, consumo de memória, consumo de armazenamento de Azure e consumo de largura de banda da rede dentro das redes virtuais. É também responsável pelo planeamento de capacidade supor o consumo de recursos de redes virtuais para o SAP HANA em Azure (Grandes Instâncias).

**WANs**: As ligações que estabelece desde as instalações até às implantações do Azure para cargas de trabalho. Todos os clientes com HANA Large Instance utilizam o Azure ExpressRoute para conectividade. Esta ligação não faz parte da solução SAP HANA sobre azure (Grandes Instâncias). É responsável pela instalação desta ligação.

**Arquivo**: Pode preferir arquivar cópias de dados utilizando os seus próprios métodos em contas de armazenamento. O arquivamento requer gestão, conformidade, custos e operações. É responsável por gerar cópias de arquivo e cópias de segurança no Azure e armazená-las de forma compatível.

Consulte o [SLA para SAP HANA em Azure (Grandes Instâncias)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Passos seguintes**
- Consulte [a arquitetura SAP HANA (Grandes Instâncias) em Azure](hana-architecture.md)
