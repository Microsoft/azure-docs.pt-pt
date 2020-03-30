---
title: Componentes e limites do sistema de informação Azure
description: Este artigo fornece uma descrição geral da arquitetura e gestão do Microsoft Azure.
services: security
documentationcenter: na
author: TerryLanfear
manager: barbkess
editor: TomSh
ms.assetid: 61e95a87-39c5-48f5-aee6-6f90ddcd336e
ms.service: security
ms.subservice: security-fundamentals
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/28/2018
ms.author: terrylan
ms.openlocfilehash: 68535f70507e7a81d217f4148314a3d76ec832ea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 03/27/2020
ms.locfileid: "68727200"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componentes e limites do sistema de informação Azure
Este artigo fornece uma descrição geral da arquitetura e gestão azure. O ambiente do sistema Azure é constituído pelas seguintes redes:

- Rede de produção do Microsoft Azure (rede Azure)
- Rede corporativa da Microsoft (corpnet)

As equipas de TI separadas são responsáveis pelas operações e manutenção destas redes.

## <a name="azure-architecture"></a>Arquitetura azure
O Azure é uma plataforma de computação em nuvem e infraestrutura para construção, implantação e gestão de aplicações e serviços através de uma rede de datacenters. A Microsoft gere estes centros de dados. Com base no número de recursos que especifica, o Azure cria máquinas virtuais (VMs) com base na necessidade de recursos. Estes VMs funcionam num hipervisor Azure, projetado para ser usado na nuvem e não é acessível ao público.

Em cada nó de servidor físico Azure, há um hipervisor que passa diretamente sobre o hardware. O hipervisor divide um nó em um número variável de VMs convidados. Cada nó também tem uma Raiz VM, que executa o sistema operativo do hospedeiro. O Windows Firewall está ativado em cada VM. Define quais portas são endereçadas configurando o ficheiro de definição de serviço. Estes portos são os únicos abertos e endereçados, internamente ou externos. Todo o tráfego e acesso ao disco e rede é mediado pelo hipervisor e pelo sistema operativo raiz.

Na camada de hospedeiro, os VMs Azure executam uma versão personalizada e endurecida do mais recente Servidor Windows. O Azure utiliza uma versão do Windows Server que inclui apenas os componentes necessários para hospedar VMs. Isto melhora o desempenho e reduz a superfície de ataque. Os limites da máquina são aplicados pelo hipervisor, o que não depende da segurança do sistema operativo.

### <a name="azure-management-by-fabric-controllers"></a>Gestão azure por controladores de tecido

Em Azure, os VMs em funcionamento em servidores físicos (lâminas/nós) são agrupados em aglomerados de cerca de 1000. Os VMs são geridos de forma independente por um componente de software de plataforma escalado e redundante chamado controlador de tecido (FC).

Cada FC gere o ciclo de vida das aplicações em execução no seu cluster, e provisões e monitoriza a saúde do hardware sob o seu controlo. Executa operações autonómicas, tais como reencarnar casos de VM em servidores saudáveis quando determina que um servidor falhou. O FC realiza ainda operações de gestão de aplicações, tais como implantação, atualização e dimensionamento de aplicações.

O datacenter está dividido em clusters. Os clusters isolam falhas ao nível do FC e impedem que certas classes de erros afetem servidores para além do cluster em que ocorrem. Os FCs que servem um determinado cluster Azure são agrupados num cluster FC.

### <a name="hardware-inventory"></a>Inventário de Hardware

O FC prepara um inventário de dispositivos de hardware e rede Azure durante o processo de configuração da armadilha de botas. Quaisquer novos componentes de hardware e rede que entrem no ambiente de produção do Azure devem seguir o processo de configuração da bootstrap. O FC é responsável pela gestão de todo o inventário listado no ficheiro de configuração datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Imagens do sistema operativo geridos pelo FC

A equipa do sistema operativo fornece imagens, sob a forma de Discos Rígidos Virtuais, implantados em todos os VMs hospedeiros e convidados no ambiente de produção do Azure. A equipa constrói estas imagens base através de um processo automatizado de construção offline. A imagem base é uma versão do sistema operativo em que o núcleo e outros componentes do núcleo foram modificados e otimizados para suportar o ambiente Azure.

Existem três tipos de imagens do sistema operativo geridas por tecidos:

- Anfitrião: Um sistema operativo personalizado que funciona em VMs hospedeiros.
- Nativo: Um sistema operativo nativo que funciona com inquilinos (por exemplo, Armazenamento Azure). Este sistema operativo não tem nenhum hipervisor.
- Convidado: Um sistema operativo convidado que funciona em VMs convidados.

Os sistemas operativos geridos pelo hospedeiro e nativos geridos pelo FC são projetados para serem utilizados na nuvem, e não são acessíveis ao público.

#### <a name="host-and-native-operating-systems"></a>Sistemas operativos hospedeiros e nativos

Hospedeiro e nativo são imagens endurecidas do sistema operativo que acolhem os agentes de tecido, e funcionam num nó de computação (funciona como primeiro VM no nó) e nós de armazenamento. O benefício de utilizar imagens base otimizadas de hospedeiro e nativo é que reduz a área de superfície exposta por APIs ou componentes não utilizados. Estes podem apresentar riscos de segurança elevados e aumentar a pegada do sistema operativo. Os sistemas operativos de pegada reduzida incluem apenas os componentes necessários ao Azure.

#### <a name="guest-operating-system"></a>Sistema operativo convidado

Os componentes internos azure em execução em VMs do sistema operativo de hóspedes não têm oportunidade de executar o Protocolo de Ambiente de Trabalho Remoto. Quaisquer alterações às definições de configuração da linha de base devem passar pelo processo de gestão de alterações e de libertação.

## <a name="azure-datacenters"></a>Centros de dados azure
A equipa da Microsoft Cloud Infrastructure and Operations (MCIO) gere as infraestruturas físicas e instalações do datacenter para todos os serviços online da Microsoft. O MCIO é o principal responsável pela gestão dos controlos físicos e ambientais dentro dos datacenters, bem como pela gestão e apoio de dispositivos de rede de perímetro externo (como routers de borda e routers de datacenter). O MCIO também é responsável pela instalação do hardware mínimo do servidor em prateleiras no datacenter. Os clientes não têm interação direta com o Azure.

## <a name="service-management-and-service-teams"></a>Equipas de gestão e serviços de serviço
Vários grupos de engenharia, conhecidos como equipas de serviço, gerem o apoio do serviço Azure. Cada equipa de serviço é responsável por uma área de apoio ao Azure. Cada equipa de serviço deve disponibilizar um engenheiro 24x7 para investigar e resolver falhas no serviço. As equipas de serviço não têm, por defeito, acesso físico ao hardware que opera no Azure.

As equipas de serviço são:

- Plataforma de Aplicação
- Azure Active Directory
- Computação do Azure
- Rede Azure
- Serviços de Engenharia de Nuvem
- ISSD: Segurança
- Autenticação Multifator
- SQL Database
- Storage

## <a name="types-of-users"></a>Tipos de utilizadores
Os colaboradores (ou empreiteiros) da Microsoft são considerados utilizadores internos. Todos os outros utilizadores são considerados utilizadores externos. Todos os utilizadores internos do Azure têm o seu estatuto de colaborador categorizado com um nível de sensibilidade que define o seu acesso aos dados dos clientes (acesso ou sem acesso). Os privilégios do utilizador para o Azure (autorização após a autenticação) são descritos na tabela seguinte:

| Função | Interna ou externa | Nível de sensibilidade | Privilégios e funções autorizados realizados | Tipo de acesso
| --- | --- | --- | --- | --- |
| Engenheiro de datacenter azure | Interno | Sem acesso aos dados dos clientes | Gerir a segurança física das instalações. Conduza patrulhas dentro e fora do datacenter, e monitorize todos os pontos de entrada. Acompanhe para dentro e para fora do datacenter determinado pessoal não autorizado que forneça serviços gerais (como refeições ou limpeza) ou trabalhos de TI dentro do datacenter. Realizar monitorização de rotina e manutenção de hardware de rede. Realizar trabalhos de gestão de incidentes e correção de ruturas utilizando uma variedade de ferramentas. Realizar monitorização de rotina e manutenção do hardware físico nos datacenters. Acesso a ambiente a pedido dos proprietários. Capaz escedânea de investigações forenses, registo de relatórios de incidentes, e exigir formação obrigatória de segurança e requisitos de política. Propriedade operacional e manutenção de ferramentas de segurança críticas, tais como scanners e recolha de registos. | Acesso persistente ao ambiente. |
| Triagem de incidentes azure (engenheiros de resposta rápida) | Interno | Acesso aos dados dos clientes | Gerir as comunicações entre o MCIO, equipas de apoio e engenharia. Incidentes na plataforma de triagem, problemas de implantação e pedidos de serviço. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas não clientes. |
| Engenheiros de implantação azure | Interno | Acesso aos dados dos clientes | Implementar e atualizar componentes da plataforma, software e alterações de configuração programadas no suporte do Azure. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas não clientes. |
| Apoio à paralisação do cliente azure (inquilino) | Interno | Acesso aos dados dos clientes | Depuração e diagnóstico de falhas na plataforma para inquilinos de computação individuais e contas Azure. Analise as falhas. Conduza correções críticas à plataforma ou ao cliente e conduza melhorias técnicas através do suporte. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas não clientes. |
| Engenheiros do site azure ao vivo (engenheiros de monitorização) e incidente | Interno | Acesso aos dados dos clientes | Diagnosticar e mitigar a saúde da plataforma utilizando ferramentas de diagnóstico. Correções de acionamento para condutores de volume, itens de reparação resultantes de interrupções e ações de restauro de paragem. | Acesso just-in-time ao ambiente, com acesso persistente limitado a sistemas não clientes. |
|Clientes Azure | Externo | N/D | N/D | N/D |

O Azure utiliza identificadores únicos para autenticar utilizadores e clientes organizacionais (ou processos que atuam em nome dos utilizadores da organização). Isto aplica-se a todos os ativos e dispositivos que fazem parte do ambiente Azure.

### <a name="azure-internal-authentication"></a>Autenticação interna azure

As comunicações entre componentes internos do Azure estão protegidas com encriptação TLS. Na maioria dos casos, os certificados X.509 são auto-assinados. Os certificados com ligações que podem ser acedidas de fora da rede Azure são uma exceção, assim como os certificados para os FCs. Os FCs têm certificados emitidos por um Certificado de Autoridade da Microsoft (CA) que é apoiado por uma raiz de confiança CA. Isto permite que as chaves públicas do FC sejam reboladas facilmente. Além disso, as ferramentas de desenvolvimento da Microsoft utilizam chaves públicas FC. Quando os desenvolvedores submetem novas imagens de aplicação, as imagens são encriptadas com uma chave pública FC de forma a proteger quaisquer segredos incorporados.

### <a name="azure-hardware-device-authentication"></a>Autenticação de dispositivo de hardware Azure

O FC mantém um conjunto de credenciais (chaves e/ou senhas) utilizadas para se autenticar em vários dispositivos de hardware sob o seu controlo. A Microsoft utiliza um sistema para impedir o acesso a estas credenciais. Especificamente, o transporte, persistência e utilização destas credenciais destinam-se a impedir os desenvolvedores, administradores e serviços de backup do Azure e o acesso pessoal a informações sensíveis, confidenciais ou privadas.

A Microsoft usa encriptação com base na chave pública de identidade principal do FC. Isto ocorre na configuração do FC e nos tempos de reconfiguração do FC, para transferir as credenciais utilizadas para aceder a dispositivos de hardware de rede. Quando o FC precisa das credenciais, o FC recupera e desencripta-as.

### <a name="network-devices"></a>Dispositivos de rede

A equipa de networking Azure configura as contas de serviço de rede para permitir que um cliente Azure autentique dispositivos de rede (routers, switches e equilibradores de carga).

## <a name="secure-service-administration"></a>Administração de serviços seguros
O pessoal de operações do Azure é obrigado a utilizar postos de trabalho administrativos seguros (SAWs). Os clientes podem implementar controlos semelhantes utilizando estações de trabalho de acesso privilegiados. Com os SAWs, o pessoal administrativo utiliza uma conta administrativa individualmente atribuída que é separada da conta de utilizador padrão do utilizador. O SAW baseia-se nessa prática de separação de contas, fornecendo uma estação de trabalho de confiança para essas contas sensíveis.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física do Azure](physical-security.md)
- [Disponibilidade de infraestruturas azure](infrastructure-availability.md)
- [Arquitetura de rede Azure](infrastructure-network.md)
- [Rede de produção azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização de infraestruturas Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azure](infrastructure-integrity.md)
- [Proteção de dados dos clientes azure](protection-customer-data.md)
