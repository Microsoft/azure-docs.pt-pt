---
title: Modelo de operações da SAP HANA em Azure (Grandes Instâncias) | Microsoft Docs
description: Modelo de operações da SAP HANA em Azure (Grandes Instâncias).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9660540c60c6b3a734dc507a6056af9f1f8d6dc9
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/02/2021
ms.locfileid: "101675554"
---
# <a name="operations-model-and-responsibilities"></a>Modelo e responsabilidades de operações

O serviço prestado com SAP HANA em Azure (Grandes Instâncias) está alinhado com os serviços Azure IaaS. Obtém-se um exemplo de uma HANA Large Instance com um sistema operativo instalado que é otimizado para SAP HANA. Tal como acontece com os VMs Azure IaaS, a maioria das tarefas de endurecimento do SISTEMA, instalação de software adicional, instalação de HANA, funcionamento do OS e HANA, e a atualização do OS e HANA é da sua responsabilidade. A Microsoft não força atualizações de SO ou atualizações HANA sobre si.

![Responsabilidades da SAP HANA em Azure (Grandes Instâncias)](./media/hana-overview-architecture/image2-responsibilities.png)

Como mostrado no diagrama, SAP HANA on Azure (Grandes Instâncias) é uma oferta de IaaS multi-inquilino. Na sua maioria, a divisão de responsabilidades está no limite da infraestrutura oss. A Microsoft é responsável por todos os aspetos do serviço abaixo da linha do sistema operativo. Você é responsável por todos os aspetos do serviço acima da linha. O SO é da sua responsabilidade. Pode continuar a utilizar os métodos mais atuais no local que poderá utilizar para a conformidade, segurança, gestão de aplicações, base e gestão de SO. Os sistemas parecem estar na sua rede em todos os aspetos.

Este serviço está otimizado para o SAP HANA, pelo que existem áreas onde é necessário trabalhar com a Microsoft para utilizar as capacidades de infraestrutura subjacentes para obter os melhores resultados.

A seguinte lista fornece mais detalhes sobre cada uma das camadas e as suas responsabilidades:

**Ligação em rede**: Todas as redes internas para o carimbo de grande instância que funciona SAP HANA. A sua responsabilidade inclui o acesso ao armazenamento, a conectividade entre as instâncias (para o scale-out e outras funções), conectividade com a paisagem e conectividade com a Azure onde a camada de aplicação SAP está hospedada em VMs. Também inclui conectividade WAN entre Centros de Dados Azure para a replicação de fins de recuperação de desastres. Todas as redes são divididas pelo arrendatário e têm qualidade de serviço aplicada.

**Armazenamento**: O armazenamento dividido virtualizado para todos os volumes necessários pelos servidores SAP HANA, bem como para instantâneos. 

**Servidores**: Os servidores físicos dedicados para executar os DBs SAP HANA atribuídos aos inquilinos. Os servidores da classe tipo I de SKUs são abstraídos de hardware. Com este tipo de servidores, a configuração do servidor é recolhida e mantida em perfis, que podem ser movidos de um hardware físico para outro hardware físico. Tal movimento (manual) de um perfil por operações pode ser comparado um pouco com a cura do serviço Azure. Os servidores da classe TIPO II SKUs não oferecem tal capacidade.

**SDDC**: O software de gestão que é utilizado para gerir centros de dados como entidades definidas por software. Permite à Microsoft reunir recursos por razões de escala, disponibilidade e desempenho.

**O/S**: O SISTEMA que escolher (SUSE Linux ou Red Hat Linux) que está a funcionar nos servidores. As imagens de SO que lhe são fornecidas foram fornecidas pelo fornecedor linux individual à Microsoft para executar o SAP HANA. Deve ter uma subscrição com o fornecedor Linux para a imagem específica otimizada da SAP HANA. É responsável pelo registo das imagens junto do fornecedor de OS. 

A partir do ponto de entrega pela Microsoft, é responsável por qualquer remendação adicional do sistema operativo Linux. Este patching inclui pacotes adicionais que podem ser necessários para uma instalação SAP HANA bem sucedida e que não foram incluídos pelo fornecedor linux específico nas suas imagens de SO otimizadas SAP HANA. (Para mais informações, consulte a documentação de instalação da SAP e notas SAP.) 

É responsável pela correção do SISTEMA devido a mau funcionamento ou otimização do SISTEMA e dos seus controladores em relação ao hardware específico do servidor. Também é responsável pela segurança ou remendação funcional do SISTEMA. 

A sua responsabilidade inclui também a monitorização e planeamento de capacidades de:

- Consumo de recursos cpu.
- Consumo de memória.
- Volumes de disco relacionados com espaço livre, IOPS e latência.
- Tráfego de volume de rede entre HANA Large Instance e a camada de aplicação SAP.

A infraestrutura subjacente da HANA Large Instance fornece funcionalidade para cópia de segurança e restauro do volume de SO. Utilizar esta funcionalidade também é da sua responsabilidade.

**Middleware**: O caso SAP HANA, principalmente. Administração, operações e monitorização são da sua responsabilidade. Pode utilizar a funcionalidade fornecida para utilizar instantâneos de armazenamento para fins de backup e restauro e recuperação de desastres. Estas capacidades são fornecidas pela infraestrutura. As suas responsabilidades também incluem conceber alta disponibilidade ou recuperação de desastres com estas capacidades, alavancando-as e monitorizando para determinar se os instantâneos de armazenamento executados com sucesso.

**Dados**: Os seus dados geridos pela SAP HANA, e outros dados, tais como ficheiros de backups localizados em volumes ou partilhas de ficheiros. As suas responsabilidades incluem monitorizar o espaço livre do disco e gerir o conteúdo nos volumes. Também é responsável por monitorizar a execução bem sucedida de cópias de segurança de volumes de discos e instantâneos de armazenamento. A execução bem sucedida da replicação de dados em sites de recuperação de desastres é da responsabilidade da Microsoft.

**Aplicações:** Os casos de aplicação SAP ou, no caso de aplicações não-SAP, a camada de aplicação desses pedidos. As suas responsabilidades incluem a implantação, administração, operações e monitorização dessas aplicações. É responsável pelo planeamento da capacidade de consumo de recursos da CPU, consumo de memória, consumo de armazenamento de Azure e consumo de largura de banda de rede em redes virtuais. Você também é responsável pelo planeamento de capacidade para o consumo de recursos de redes virtuais para SAP HANA em Azure (Grandes Instâncias).

**WANs**: As ligações que estabelece desde as instalações do Azure para as cargas de trabalho. Todos os clientes com HANA Large Instance usam a Azure ExpressRoute para conectividade. Esta ligação não faz parte da solução SAP HANA on Azure (Grandes Instâncias). É responsável pela configuração desta ligação.

**Arquivo**: Pode preferir arquivar cópias de dados utilizando os seus próprios métodos em contas de armazenamento. O arquivamento requer gestão, conformidade, custos e operações. É responsável por gerar cópias de arquivo e cópias de segurança no Azure e guardá-las de forma compatível.

Consulte o [SLA para SAP HANA em Azure (Grandes Instâncias)](https://azure.microsoft.com/support/legal/sla/sap-hana-large/).

**Próximos passos**
- Consulte [a arquitetura SAP HANA (Grandes Instâncias) em Azure](hana-architecture.md)
