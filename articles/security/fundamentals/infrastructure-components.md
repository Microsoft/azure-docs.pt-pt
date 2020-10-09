---
title: Componentes e limites do sistema de informação azul
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
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pt-PT
ms.lasthandoff: 10/09/2020
ms.locfileid: "68727200"
---
# <a name="azure-information-system-components-and-boundaries"></a>Componentes e limites do sistema de informação azul
Este artigo apresenta uma descrição geral da arquitetura e gestão Azure. O ambiente do sistema Azure é constituído pelas seguintes redes:

- Rede de produção microsoft Azure (rede Azure)
- Rede corporativa da Microsoft (corpnet)

As equipas de TI separadas são responsáveis pelas operações e manutenção destas redes.

## <a name="azure-architecture"></a>Arquitetura azul
O Azure é uma plataforma de computação em nuvem e infraestrutura para construir, implantar e gerir aplicações e serviços através de uma rede de datacenters. A Microsoft gere estes datacenters. Com base no número de recursos especificados, o Azure cria máquinas virtuais (VMs) com base na necessidade de recursos. Estes VMs funcionam num hipervisor Azure, projetado para ser utilizado na nuvem e não é acessível ao público.

Em cada nó do servidor físico Azure, existe um hipervisor que passa diretamente por cima do hardware. O hipervisor divide um nó num número variável de VMs convidados. Cada nó também tem um VM de raiz, que executa o sistema operativo hospedeiro. O Windows Firewall está ativado em cada VM. Define quais as portas que são endereçadas configurando o ficheiro de definição de serviço. Estas portas são as únicas abertas e endereçais, interna ou externamente. Todo o tráfego e acesso ao disco e rede é mediado pelo hipervisor e sistema operativo raiz.

Na camada de anfitrião, os VMs Azure executam uma versão personalizada e endurecida do mais recente Windows Server. O Azure utiliza uma versão do Windows Server que inclui apenas os componentes necessários para hospedar VMs. Isto melhora o desempenho e reduz a superfície de ataque. Os limites da máquina são aplicados pelo hipervisor, que não depende da segurança do sistema operativo.

### <a name="azure-management-by-fabric-controllers"></a>Gestão Azure por controladores de tecidos

Em Azure, os VMs que executam servidores físicos (lâminas/nós) são agrupados em aglomerados de cerca de 1000. Os VMs são geridos independentemente por um componente de software de plataforma escalonado e redundante chamado controlador de tecido (FC).

Cada FC gere o ciclo de vida das aplicações em execução no seu cluster, e prevê e monitoriza a saúde do hardware sob o seu controlo. Executa operações autonómicas, como reencarnar as instâncias de VM em servidores saudáveis quando determina que um servidor falhou. O FC realiza ainda operações de gestão de aplicações, tais como implementação, atualização e escalonamento de aplicações.

O datacenter é dividido em aglomerados. Os clusters isolam falhas ao nível fc, e impedem que certas classes de erros afetem os servidores para além do cluster em que ocorrem. Os FCs que servem um cluster Azure particular são agrupados num cluster FC.

### <a name="hardware-inventory"></a>Inventário de Hardware

O FC prepara um inventário de hardware e dispositivos de rede Azure durante o processo de configuração da bootstrap. Quaisquer novos componentes de hardware e rede que entrem no ambiente de produção do Azure devem seguir o processo de configuração da bootstrap. O FC é responsável pela gestão de todo o inventário listado no ficheiro de configuração datacenter.xml.

### <a name="fc-managed-operating-system-images"></a>Imagens do sistema operativo gerido pelo FC

A equipa do sistema operativo fornece imagens, sob a forma de Discos Rígidos Virtuais, implantadas em todos os VMs hospedeiros e convidados no ambiente de produção do Azure. A equipa constrói estas imagens base através de um processo automatizado de construção offline. A imagem base é uma versão do sistema operativo em que o núcleo e outros componentes do núcleo foram modificados e otimizados para suportar o ambiente Azure.

Existem três tipos de imagens do sistema operativo gerido por tecidos:

- Anfitrião: Um sistema operativo personalizado que funciona em VMs hospedeiros.
- Nativo: Um sistema operativo nativo que funciona com inquilinos (por exemplo, Azure Storage). Este sistema operativo não tem hipervisor.
- Hóspede: Um sistema operativo de hóspedes que funciona em VMs de hóspedes.

Os sistemas operativos geridos pelo host e nativo FC são projetados para serem utilizados na nuvem e não são acessíveis ao público.

#### <a name="host-and-native-operating-systems"></a>Sistemas operativos hospedeiros e nativos

Hospedeiro e nativo são imagens do sistema operativo endurecido que acolhem os agentes de tecido, e funcionam num nó computacional (funciona como primeiro VM no nó) e nos nós de armazenamento. O benefício da utilização de imagens base otimizadas do hospedeiro e nativo é que reduz a área de superfície exposta por APIs ou componentes não utilizados. Estes podem apresentar elevados riscos de segurança e aumentar a pegada do sistema operativo. Os sistemas operativos de pegada reduzida incluem apenas os componentes necessários ao Azure.

#### <a name="guest-operating-system"></a>Sistema operativo convidado

Os componentes internos Azure em execução nos VMs do sistema operativo convidado não têm oportunidade de executar o Protocolo de Ambiente de Trabalho Remoto. Quaisquer alterações nas definições de configuração de base devem passar pelo processo de gestão de alterações e libertação.

## <a name="azure-datacenters"></a>Centros de dados Azure
A equipa da Microsoft Cloud Infrastructure and Operations (MCIO) gere as instalações de infraestrutura física e datacenter para todos os serviços online da Microsoft. A MCIO é a principal responsável pela gestão dos controlos físicos e ambientais dentro dos datacenters, bem como pela gestão e suporte de dispositivos de rede de perímetros exteriores (tais como routers de arestas e routers de datacenters). O MCIO é também responsável pela configuração do hardware mínimo do servidor nos racks do datacenter. Os clientes não têm interação direta com a Azure.

## <a name="service-management-and-service-teams"></a>Equipas de gestão de serviços e serviços
Vários grupos de engenharia, conhecidos como equipas de serviço, gerem o apoio do serviço Azure. Cada equipa de serviço é responsável por uma área de apoio ao Azure. Cada equipa de serviço deve disponibilizar um engenheiro 24x7 para investigar e resolver falhas no serviço. As equipas de serviço não têm, por padrão, acesso físico ao hardware que opera no Azure.

As equipas de serviço são:

- Plataforma de Aplicação
- Azure Active Directory
- Computação do Azure
- Rede Azure
- Serviços de Engenharia de Nuvem
- ISSD: Segurança
- Autenticação Multifactor
- SQL Database
- Armazenamento

## <a name="types-of-users"></a>Tipos de utilizadores
Os colaboradores (ou empreiteiros) da Microsoft são considerados utilizadores internos. Todos os outros utilizadores são considerados utilizadores externos. Todos os utilizadores internos da Azure têm o seu estatuto de colaborador categorizado com um nível de sensibilidade que define o seu acesso aos dados dos clientes (acesso ou sem acesso). Os privilégios do utilizador para a Azure (autorização após a autenticação) são descritos no quadro seguinte:

| Função | Interno ou externo | Nível de sensibilidade | Privilégios e funções autorizadas realizadas | Tipo de acesso
| --- | --- | --- | --- | --- |
| Engenheiro de datacenter Azure | Interno | Sem acesso aos dados dos clientes | Gerir a segurança física das instalações. Faça patrulhas dentro e fora do centro de dados, e monitorize todos os pontos de entrada. Acompanhe dentro e fora do datacenter certos funcionários não autorizados que prestam serviços gerais (como refeições ou limpeza) ou trabalhos de TI dentro do datacenter. Realizar monitorização de rotina e manutenção de hardware de rede. Execute o trabalho de gestão de incidentes e break-fix utilizando uma variedade de ferramentas. Realizar monitorização e manutenção de rotina do hardware físico nos datacenters. Acesso ao ambiente a pedido dos proprietários. Capaz de realizar investigações forenses, registar relatórios de incidentes, e exigir treino obrigatório de segurança e requisitos políticos. Propriedade operacional e manutenção de ferramentas de segurança críticas, tais como scanners e recolha de registos. | Acesso persistente ao meio ambiente. |
| Triagem de incidentes Azure (engenheiros de resposta rápida) | Interno | Acesso aos dados dos clientes | Gerir comunicações entre as equipas de apoio e engenharia da MCIO. Incidentes na plataforma de triagem, problemas de implantação e pedidos de serviço. | Acesso just-in-time ao ambiente, com acesso limitado e persistente a sistemas não-clientes. |
| Engenheiros de implantação da Azure | Interno | Acesso aos dados dos clientes | Implementar e atualizar componentes da plataforma, software e alterações de configuração programadas no suporte do Azure. | Acesso just-in-time ao ambiente, com acesso limitado e persistente a sistemas não-clientes. |
| Suporte de paragem do cliente Azure (inquilino) | Interno | Acesso aos dados dos clientes | Depurar e diagnosticar falhas na plataforma para inquilinos computação individual e contas Azure. Analisar falhas. Conduza correções críticas para a plataforma ou cliente e impulsione melhorias técnicas em todo o suporte. | Acesso just-in-time ao ambiente, com acesso limitado e persistente a sistemas não-clientes. |
| Engenheiros do local ao vivo do Azure (engenheiros de monitorização) e incidente | Interno | Acesso aos dados dos clientes | Diagnosticar e mitigar a saúde da plataforma utilizando ferramentas de diagnóstico. Correções de acionamento para condutores de volume, itens de reparação resultantes de interrupções e ações de restauro de assistência. | Acesso just-in-time ao ambiente, com acesso limitado e persistente a sistemas não-clientes. |
|Clientes Azure | Externo | N/D | N/D | N/D |

O Azure utiliza identificadores exclusivos para autenticar utilizadores e clientes organizacionais (ou processos que atuam em nome de utilizadores organizacionais). Isto aplica-se a todos os ativos e dispositivos que fazem parte do ambiente Azure.

### <a name="azure-internal-authentication"></a>Autenticação interna do Azure

As comunicações entre os componentes internos do Azure estão protegidas com encriptação TLS. Na maioria dos casos, os certificados X.509 são auto-assinados. Os certificados com ligações que podem ser acedidos a partir de fora da rede Azure são uma exceção, assim como certificados para os FCs. Os FCs têm certificados emitidos por um Certificado de Autoridade da Microsoft (CA) que é apoiado por uma CA de raiz fidedigna. Isto permite que as chaves públicas do FC sejam facilmente enroladas. Além disso, as ferramentas de desenvolvimento da Microsoft usam chaves públicas FC. Quando os desenvolvedores submetem novas imagens de aplicação, as imagens são encriptadas com uma chave pública FC para proteger quaisquer segredos incorporados.

### <a name="azure-hardware-device-authentication"></a>Autenticação de dispositivos de hardware Azure

O FC mantém um conjunto de credenciais (chaves e/ou palavras-passe) usadas para autenticar-se em vários dispositivos de hardware sob o seu controlo. A Microsoft utiliza um sistema para impedir o acesso a estas credenciais. Especificamente, o transporte, persistência e utilização destas credenciais destinam-se a impedir que os desenvolvedores, administradores e serviços de backup e o pessoal tenham acesso a informações confidenciais, confidenciais ou privadas.

A Microsoft usa encriptação com base na chave pública de identidade principal do FC. Isto ocorre nos tempos de configuração do FC e fc, para transferir as credenciais utilizadas para aceder a dispositivos de hardware de rede. Quando o FC precisa das credenciais, o FC recupera-as e desencripta-as.

### <a name="network-devices"></a>Dispositivos de rede

A equipa de networking da Azure configura contas de serviço de rede para permitir que um cliente Azure autença para dispositivos de rede (routers, comutadores e equilibradores de carga).

## <a name="secure-service-administration"></a>Administração de serviços seguros
O pessoal das operações da Azure é obrigado a utilizar postos de trabalho administrativos seguros (SAWs). Os clientes podem implementar controlos semelhantes utilizando estações de trabalho privilegiadas de acesso. Com os SAWs, o pessoal administrativo utiliza uma conta administrativa individualmente atribuída que é separada da conta padrão do utilizador. A SAW baseia-se nessa prática de separação de contas, fornecendo uma estação de trabalho de confiança para essas contas sensíveis.

## <a name="next-steps"></a>Passos seguintes
Para saber mais sobre o que a Microsoft faz para ajudar a proteger a infraestrutura Azure, consulte:

- [Instalações, instalações e segurança física](physical-security.md)
- [Disponibilidade de infraestrutura Azure](infrastructure-availability.md)
- [Arquitetura da rede Azure](infrastructure-network.md)
- [Rede de produção Azure](production-network.md)
- [Funcionalidades de segurança da Base de Dados Azure SQL](infrastructure-sql.md)
- [Operações e gestão de produção da Azure](infrastructure-operations.md)
- [Monitorização das infraestruturas do Azure](infrastructure-monitoring.md)
- [Integridade da infraestrutura azul](infrastructure-integrity.md)
- [Proteção de dados do cliente Azure](protection-customer-data.md)
